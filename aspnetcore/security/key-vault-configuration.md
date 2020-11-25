---
title: Provider di configurazione di Azure Key Vault in ASP.NET Core
author: rick-anderson
description: Informazioni su come usare il provider di configurazione Azure Key Vault per configurare un'app usando coppie nome-valore caricate in fase di esecuzione.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, devx-track-azurecli
ms.date: 02/07/2020
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
uid: security/key-vault-configuration
ms.openlocfilehash: 7f5cd3de38f1e45d9b188c513a0e62ca658b2992
ms.sourcegitcommit: 3f0ad1e513296ede1bff39a05be6c278e879afed
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96035905"
---
# <a name="azure-key-vault-configuration-provider-in-aspnet-core"></a><span data-ttu-id="8bfbf-103">Provider di configurazione di Azure Key Vault in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8bfbf-103">Azure Key Vault Configuration Provider in ASP.NET Core</span></span>

<span data-ttu-id="8bfbf-104">Di [Andrew Stanton-Nurse](https://github.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="8bfbf-104">By [Andrew Stanton-Nurse](https://github.com/anurse)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="8bfbf-105">Questo documento illustra come usare il provider di configurazione [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) per caricare i valori di configurazione dell'app da Azure Key Vault segreti.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-105">This document explains how to use the [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) Configuration Provider to load app configuration values from Azure Key Vault secrets.</span></span> <span data-ttu-id="8bfbf-106">Azure Key Vault è un servizio basato sul cloud che aiuta a proteggere le chiavi crittografiche e i segreti usati da app e servizi.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-106">Azure Key Vault is a cloud-based service that assists in safeguarding cryptographic keys and secrets used by apps and services.</span></span> <span data-ttu-id="8bfbf-107">Gli scenari comuni per l'uso di Azure Key Vault con le app ASP.NET Core includono:</span><span class="sxs-lookup"><span data-stu-id="8bfbf-107">Common scenarios for using Azure Key Vault with ASP.NET Core apps include:</span></span>

* <span data-ttu-id="8bfbf-108">Controllo dell'accesso ai dati di configurazione riservati.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-108">Controlling access to sensitive configuration data.</span></span>
* <span data-ttu-id="8bfbf-109">Soddisfare i requisiti per i moduli di protezione hardware convalidati FIPS 140-2 Level 2 (HSM) quando si archiviano i dati di configurazione.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-109">Meeting the requirement for FIPS 140-2 Level 2 validated Hardware Security Modules (HSM's) when storing configuration data.</span></span>

<span data-ttu-id="8bfbf-110">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="8bfbf-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="packages"></a><span data-ttu-id="8bfbf-111">Pacchetti</span><span class="sxs-lookup"><span data-stu-id="8bfbf-111">Packages</span></span>

<span data-ttu-id="8bfbf-112">Aggiungere un riferimento al pacchetto [Azure.Extensions.AspNetCore.Configuration. ](https://www.nuget.org/packages/Azure.Extensions.AspNetCore.Configuration.Secrets/) Pacchetto di segreti.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-112">Add a package reference to the [Azure.Extensions.AspNetCore.Configuration.Secrets](https://www.nuget.org/packages/Azure.Extensions.AspNetCore.Configuration.Secrets/) package.</span></span>

## <a name="sample-app"></a><span data-ttu-id="8bfbf-113">App di esempio</span><span class="sxs-lookup"><span data-stu-id="8bfbf-113">Sample app</span></span>

<span data-ttu-id="8bfbf-114">L'app di esempio viene eseguita in una delle due modalità determinate dall' `#define` istruzione all'inizio del file *Program.cs* :</span><span class="sxs-lookup"><span data-stu-id="8bfbf-114">The sample app runs in either of two modes determined by the `#define` statement at the top of the *Program.cs* file:</span></span>

* <span data-ttu-id="8bfbf-115">`Certificate`: Illustra l'uso di un ID client Azure Key Vault e di un certificato X. 509 per accedere ai segreti archiviati nel Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-115">`Certificate`: Demonstrates the use of an Azure Key Vault Client ID and X.509 certificate to access secrets stored in Azure Key Vault.</span></span> <span data-ttu-id="8bfbf-116">Questa versione dell'esempio può essere eseguita da qualsiasi posizione, distribuita nel servizio app Azure o in qualsiasi host in grado di servire un'app ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-116">This version of the sample can be run from any location, deployed to Azure App Service or any host capable of serving an ASP.NET Core app.</span></span>
* <span data-ttu-id="8bfbf-117">`Managed`: Illustra come usare le [identità gestite per le risorse di Azure](/azure/active-directory/managed-identities-azure-resources/overview) per autenticare l'app Azure Key Vault con Azure ad autenticazione senza credenziali archiviate nel codice o nella configurazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-117">`Managed`: Demonstrates how to use [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview) to authenticate the app to Azure Key Vault with Azure AD authentication without credentials stored in the app's code or configuration.</span></span> <span data-ttu-id="8bfbf-118">Quando si usano identità gestite per l'autenticazione, non è necessario un Azure AD ID applicazione e una password (segreto client).</span><span class="sxs-lookup"><span data-stu-id="8bfbf-118">When using managed identities to authenticate, an Azure AD Application ID and Password (Client Secret) aren't required.</span></span> <span data-ttu-id="8bfbf-119">La `Managed` versione dell'esempio deve essere distribuita in Azure.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-119">The `Managed` version of the sample must be deployed to Azure.</span></span> <span data-ttu-id="8bfbf-120">Seguire le istruzioni riportate nella sezione [usare le identità gestite per le risorse di Azure](#use-managed-identities-for-azure-resources) .</span><span class="sxs-lookup"><span data-stu-id="8bfbf-120">Follow the guidance in the [Use the Managed identities for Azure resources](#use-managed-identities-for-azure-resources) section.</span></span>

<span data-ttu-id="8bfbf-121">Per ulteriori informazioni su come configurare un'app di esempio utilizzando le direttive per il preprocessore ( `#define` ), vedere <xref:index#preprocessor-directives-in-sample-code> .</span><span class="sxs-lookup"><span data-stu-id="8bfbf-121">For more information on how to configure a sample app using preprocessor directives (`#define`), see <xref:index#preprocessor-directives-in-sample-code>.</span></span>

## <a name="secret-storage-in-the-development-environment"></a><span data-ttu-id="8bfbf-122">Archiviazione segreta nell'ambiente di sviluppo</span><span class="sxs-lookup"><span data-stu-id="8bfbf-122">Secret storage in the Development environment</span></span>

<span data-ttu-id="8bfbf-123">Impostare i segreti in locale usando lo [strumento di gestione dei segreti](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="8bfbf-123">Set secrets locally using the [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="8bfbf-124">Quando l'app di esempio viene eseguita nel computer locale nell'ambiente di sviluppo, i segreti vengono caricati dall'archivio dei segreti dell'utente locale.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-124">When the sample app runs on the local machine in the Development environment, secrets are loaded from the local user secrets store.</span></span>

<span data-ttu-id="8bfbf-125">Lo strumento di gestione dei segreti richiede una `<UserSecretsId>` proprietà nel file di progetto dell'app.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-125">The Secret Manager tool requires a `<UserSecretsId>` property in the app's project file.</span></span> <span data-ttu-id="8bfbf-126">Impostare il valore della proprietà ( `{GUID}` ) su un GUID univoco:</span><span class="sxs-lookup"><span data-stu-id="8bfbf-126">Set the property value (`{GUID}`) to any unique GUID:</span></span>

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

<span data-ttu-id="8bfbf-127">I segreti vengono creati come coppie nome/valore.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-127">Secrets are created as name-value pairs.</span></span> <span data-ttu-id="8bfbf-128">I valori gerarchici (sezioni di configurazione) utilizzano un `:` (due punti) come separatore nei nomi delle chiavi di [configurazione ASP.NET Core](xref:fundamentals/configuration/index) .</span><span class="sxs-lookup"><span data-stu-id="8bfbf-128">Hierarchical values (configuration sections) use a `:` (colon) as a separator in [ASP.NET Core configuration](xref:fundamentals/configuration/index) key names.</span></span>

<span data-ttu-id="8bfbf-129">Il gestore del segreto viene usato da una shell dei comandi aperta alla [radice del contenuto](xref:fundamentals/index#content-root)del progetto, dove `{SECRET NAME}` è il nome e `{SECRET VALUE}` è il valore:</span><span class="sxs-lookup"><span data-stu-id="8bfbf-129">The Secret Manager is used from a command shell opened to the project's [content root](xref:fundamentals/index#content-root), where `{SECRET NAME}` is the name and `{SECRET VALUE}` is the value:</span></span>

```dotnetcli
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

<span data-ttu-id="8bfbf-130">Eseguire i comandi seguenti in una shell dei comandi dalla radice del [contenuto](xref:fundamentals/index#content-root) del progetto per impostare i segreti per l'app di esempio:</span><span class="sxs-lookup"><span data-stu-id="8bfbf-130">Execute the following commands in a command shell from the project's [content root](xref:fundamentals/index#content-root) to set the secrets for the sample app:</span></span>

```dotnetcli
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

<span data-ttu-id="8bfbf-131">Quando questi segreti vengono archiviati in Azure Key Vault nell' [archiviazione segreta nell'ambiente di produzione con Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) sezione, il `_dev` suffisso viene modificato in `_prod` .</span><span class="sxs-lookup"><span data-stu-id="8bfbf-131">When these secrets are stored in Azure Key Vault in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section, the `_dev` suffix is changed to `_prod`.</span></span> <span data-ttu-id="8bfbf-132">Il suffisso fornisce un segnale visivo nell'output dell'app che indica l'origine dei valori di configurazione.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-132">The suffix provides a visual cue in the app's output indicating the source of the configuration values.</span></span>

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a><span data-ttu-id="8bfbf-133">Archiviazione segreta nell'ambiente di produzione con Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="8bfbf-133">Secret storage in the Production environment with Azure Key Vault</span></span>

<span data-ttu-id="8bfbf-134">Le istruzioni fornite nell'argomento [avvio rapido: impostare e recuperare un segreto da Azure Key Vault usando l'interfaccia](/azure/key-vault/quick-create-cli) della riga di comando di Azure sono riepilogate qui per la creazione di un Azure Key Vault e l'archiviazione di segreti usati dall'app di esempio.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-134">The instructions provided by the [Quickstart: Set and retrieve a secret from Azure Key Vault using Azure CLI](/azure/key-vault/quick-create-cli) topic are summarized here for creating an Azure Key Vault and storing secrets used by the sample app.</span></span> <span data-ttu-id="8bfbf-135">Per ulteriori informazioni, vedere l'argomento.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-135">Refer to the topic for further details.</span></span>

1. <span data-ttu-id="8bfbf-136">Aprire Azure cloud shell usando uno dei metodi seguenti nel [portale di Azure](https://portal.azure.com/):</span><span class="sxs-lookup"><span data-stu-id="8bfbf-136">Open Azure Cloud shell using any one of the following methods in the [Azure portal](https://portal.azure.com/):</span></span>

   * <span data-ttu-id="8bfbf-137">Selezionare **Prova** nell'angolo superiore destro di un blocco di codice.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-137">Select **Try It** in the upper-right corner of a code block.</span></span> <span data-ttu-id="8bfbf-138">Usare la stringa di ricerca "interfaccia della riga di comando di Azure" nella casella di testo.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-138">Use the search string "Azure CLI" in the text box.</span></span>
   * <span data-ttu-id="8bfbf-139">Aprire Cloud Shell nel browser con il pulsante **avvia cloud Shell** .</span><span class="sxs-lookup"><span data-stu-id="8bfbf-139">Open Cloud Shell in your browser with the **Launch Cloud Shell** button.</span></span>
   * <span data-ttu-id="8bfbf-140">Selezionare il pulsante **Cloud Shell** nel menu nell'angolo in alto a destra del portale di Azure.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-140">Select the **Cloud Shell** button on the menu in the upper-right corner of the Azure portal.</span></span>

   <span data-ttu-id="8bfbf-141">Per altre informazioni, vedere l'interfaccia della riga di comando di [Azure](/cli/azure/) e [Panoramica di Azure cloud Shell](/azure/cloud-shell/overview).</span><span class="sxs-lookup"><span data-stu-id="8bfbf-141">For more information, see [Azure CLI](/cli/azure/) and [Overview of Azure Cloud Shell](/azure/cloud-shell/overview).</span></span>

1. <span data-ttu-id="8bfbf-142">Se non è già stato eseguito l'autenticazione, effettuare l'accesso con il `az login` comando.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-142">If you aren't already authenticated, sign in with the `az login` command.</span></span>

1. <span data-ttu-id="8bfbf-143">Creare un gruppo di risorse con il comando seguente, dove `{RESOURCE GROUP NAME}` è il nome del gruppo di risorse per il nuovo gruppo di risorse e `{LOCATION}` è l'area di Azure (Datacenter):</span><span class="sxs-lookup"><span data-stu-id="8bfbf-143">Create a resource group with the following command, where `{RESOURCE GROUP NAME}` is the resource group name for the new resource group and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="8bfbf-144">Creare un insieme di credenziali delle chiavi nel gruppo di risorse con il comando seguente, dove è il nome del nuovo insieme di credenziali delle `{KEY VAULT NAME}` chiavi e `{LOCATION}` è l'area di Azure (Datacenter):</span><span class="sxs-lookup"><span data-stu-id="8bfbf-144">Create a key vault in the resource group with the following command, where `{KEY VAULT NAME}` is the name for the new key vault and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az keyvault create --name {KEY VAULT NAME} --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="8bfbf-145">Creare segreti nell'insieme di credenziali delle chiavi come coppie nome/valore.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-145">Create secrets in the key vault as name-value pairs.</span></span>

   <span data-ttu-id="8bfbf-146">Azure Key Vault nomi di segreto sono limitati a caratteri alfanumerici e trattini.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-146">Azure Key Vault secret names are limited to alphanumeric characters and dashes.</span></span> <span data-ttu-id="8bfbf-147">I valori gerarchici (sezioni di configurazione) usano `--` (due trattini) come separatore.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-147">Hierarchical values (configuration sections) use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="8bfbf-148">I due punti, che in genere vengono usati per delimitare una sezione da una sottochiave in [ASP.NET Core configurazione](xref:fundamentals/configuration/index), non sono consentiti nei nomi dei segreti di Key Vault.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-148">Colons, which are normally used to delimit a section from a subkey in [ASP.NET Core configuration](xref:fundamentals/configuration/index), aren't allowed in key vault secret names.</span></span> <span data-ttu-id="8bfbf-149">Pertanto, vengono usati due trattini e scambiati per i due punti quando i segreti vengono caricati nella configurazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-149">Therefore, two dashes are used and swapped for a colon when the secrets are loaded into the app's configuration.</span></span>

   <span data-ttu-id="8bfbf-150">I segreti seguenti sono da usare con l'app di esempio.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-150">The following secrets are for use with the sample app.</span></span> <span data-ttu-id="8bfbf-151">I valori includono un `_prod` suffisso per distinguerli dai `_dev` valori dei suffissi caricati nell'ambiente di sviluppo da segreti utente.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-151">The values include a `_prod` suffix to distinguish them from the `_dev` suffix values loaded in the Development environment from User Secrets.</span></span> <span data-ttu-id="8bfbf-152">Sostituire `{KEY VAULT NAME}` con il nome dell'insieme di credenziali delle chiavi creato nel passaggio precedente:</span><span class="sxs-lookup"><span data-stu-id="8bfbf-152">Replace `{KEY VAULT NAME}` with the name of the key vault that you created in the prior step:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a><span data-ttu-id="8bfbf-153">Usare l'ID applicazione e il certificato X. 509 per le app non ospitate in Azure</span><span class="sxs-lookup"><span data-stu-id="8bfbf-153">Use Application ID and X.509 certificate for non-Azure-hosted apps</span></span>

<span data-ttu-id="8bfbf-154">Configurare Azure AD, Azure Key Vault e l'app per usare un ID applicazione Azure Active Directory e un certificato X. 509 per l'autenticazione in un insieme di credenziali **delle chiavi quando l'app è ospitata all'esterno di Azure**.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-154">Configure Azure AD, Azure Key Vault, and the app to use an Azure Active Directory Application ID and X.509 certificate to authenticate to a key vault **when the app is hosted outside of Azure**.</span></span> <span data-ttu-id="8bfbf-155">Per i dettagli, vedere l'articolo relativo alle [informazioni su chiavi, segreti e certificati](/azure/key-vault/about-keys-secrets-and-certificates).</span><span class="sxs-lookup"><span data-stu-id="8bfbf-155">For more information, see [About keys, secrets, and certificates](/azure/key-vault/about-keys-secrets-and-certificates).</span></span>

> [!NOTE]
> <span data-ttu-id="8bfbf-156">Sebbene l'uso di un ID applicazione e di un certificato X. 509 sia supportato per le app ospitate in Azure, è consigliabile usare [identità gestite per le risorse di Azure](#use-managed-identities-for-azure-resources) quando si ospita un'app in Azure.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-156">Although using an Application ID and X.509 certificate is supported for apps hosted in Azure, we recommend using [Managed identities for Azure resources](#use-managed-identities-for-azure-resources) when hosting an app in Azure.</span></span> <span data-ttu-id="8bfbf-157">Le identità gestite non richiedono l'archiviazione di un certificato nell'app o nell'ambiente di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-157">Managed identities don't require storing a certificate in the app or in the development environment.</span></span>

<span data-ttu-id="8bfbf-158">L'app di esempio usa un ID applicazione e un certificato X. 509 quando l' `#define` istruzione all'inizio del file *Program.cs* è impostata su `Certificate` .</span><span class="sxs-lookup"><span data-stu-id="8bfbf-158">The sample app uses an Application ID and X.509 certificate when the `#define` statement at the top of the *Program.cs* file is set to `Certificate`.</span></span>

1. <span data-ttu-id="8bfbf-159">Creare un certificato di archivio PKCS # 12 (*. pfx*).</span><span class="sxs-lookup"><span data-stu-id="8bfbf-159">Create a PKCS#12 archive (*.pfx*) certificate.</span></span> <span data-ttu-id="8bfbf-160">Le opzioni per la creazione di certificati includono [Makecert in Windows](/windows/desktop/seccrypto/makecert) e [openssl](https://www.openssl.org/).</span><span class="sxs-lookup"><span data-stu-id="8bfbf-160">Options for creating certificates include [MakeCert on Windows](/windows/desktop/seccrypto/makecert) and [OpenSSL](https://www.openssl.org/).</span></span>
1. <span data-ttu-id="8bfbf-161">Installare il certificato nell'archivio certificati personale dell'utente corrente.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-161">Install the certificate into the current user's personal certificate store.</span></span> <span data-ttu-id="8bfbf-162">Contrassegnare la chiave come esportabile è facoltativa.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-162">Marking the key as exportable is optional.</span></span> <span data-ttu-id="8bfbf-163">Prendere nota dell'identificazione personale del certificato, che verrà usato più avanti in questo processo.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-163">Note the certificate's thumbprint, which is used later in this process.</span></span>
1. <span data-ttu-id="8bfbf-164">Esportare il certificato di archiviazione PKCS # 12 (*. pfx*) come certificato con codifica der (*. cer*).</span><span class="sxs-lookup"><span data-stu-id="8bfbf-164">Export the PKCS#12 archive (*.pfx*) certificate as a DER-encoded certificate (*.cer*).</span></span>
1. <span data-ttu-id="8bfbf-165">Registrare l'app con Azure AD (**registrazioni app**).</span><span class="sxs-lookup"><span data-stu-id="8bfbf-165">Register the app with Azure AD (**App registrations**).</span></span>
1. <span data-ttu-id="8bfbf-166">Caricare il certificato con codifica DER (*. cer*) in Azure ad:</span><span class="sxs-lookup"><span data-stu-id="8bfbf-166">Upload the DER-encoded certificate (*.cer*) to Azure AD:</span></span>
   1. <span data-ttu-id="8bfbf-167">Selezionare l'app in Azure AD.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-167">Select the app in Azure AD.</span></span>
   1. <span data-ttu-id="8bfbf-168">Passare a **certificati & segreti**.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-168">Navigate to **Certificates & secrets**.</span></span>
   1. <span data-ttu-id="8bfbf-169">Selezionare **Carica certificato** per caricare il certificato, che contiene la chiave pubblica.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-169">Select **Upload certificate** to upload the certificate, which contains the public key.</span></span> <span data-ttu-id="8bfbf-170">Un certificato con *estensione cer*, *PEM* o *CRT* è accettabile.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-170">A *.cer*, *.pem*, or *.crt* certificate is acceptable.</span></span>
1. <span data-ttu-id="8bfbf-171">Archiviare il nome dell'insieme di credenziali delle chiavi, l'ID applicazione e l'identificazione personale del certificato nel file dell'app *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="8bfbf-171">Store the key vault name, Application ID, and certificate thumbprint in the app's *appsettings.json* file.</span></span>
1. <span data-ttu-id="8bfbf-172">Passare a insiemi di credenziali delle **chiavi** nella portale di Azure.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-172">Navigate to **Key vaults** in the Azure portal.</span></span>
1. <span data-ttu-id="8bfbf-173">Selezionare l'insieme di credenziali delle chiavi creato nell' [Archivio Secret nell'ambiente di produzione con Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) sezione.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-173">Select the key vault that you created in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section.</span></span>
1. <span data-ttu-id="8bfbf-174">Selezionare **Criteri di accesso**.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-174">Select **Access policies**.</span></span>
1. <span data-ttu-id="8bfbf-175">Selezionare **Aggiungi criterio di accesso**.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-175">Select **Add Access Policy**.</span></span>
1. <span data-ttu-id="8bfbf-176">Aprire **autorizzazioni segrete** e fornire all'app le autorizzazioni **Get** ed **List** .</span><span class="sxs-lookup"><span data-stu-id="8bfbf-176">Open **Secret permissions** and provide the app with **Get** and **List** permissions.</span></span>
1. <span data-ttu-id="8bfbf-177">Selezionare **Seleziona entità** e selezionare l'app registrata in base al nome.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-177">Select **Select principal** and select the registered app by name.</span></span> <span data-ttu-id="8bfbf-178">Fare clic sul pulsante **Seleziona**.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-178">Select the **Select** button.</span></span>
1. <span data-ttu-id="8bfbf-179">Selezionare **OK**.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-179">Select **OK**.</span></span>
1. <span data-ttu-id="8bfbf-180">Selezionare **Salva**.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-180">Select **Save**.</span></span>
1. <span data-ttu-id="8bfbf-181">Distribuire l'app.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-181">Deploy the app.</span></span>

<span data-ttu-id="8bfbf-182">L' `Certificate` app di esempio ottiene i valori di configurazione da `IConfigurationRoot` con lo stesso nome del nome del segreto:</span><span class="sxs-lookup"><span data-stu-id="8bfbf-182">The `Certificate` sample app obtains its configuration values from `IConfigurationRoot` with the same name as the secret name:</span></span>

* <span data-ttu-id="8bfbf-183">Valori non gerarchici: il valore per `SecretName` viene ottenuto con `config["SecretName"]` .</span><span class="sxs-lookup"><span data-stu-id="8bfbf-183">Non-hierarchical values: The value for `SecretName` is obtained with `config["SecretName"]`.</span></span>
* <span data-ttu-id="8bfbf-184">Valori gerarchici (sezioni): `:` notazione use (due punti) o `GetSection` metodo di estensione.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-184">Hierarchical values (sections): Use `:` (colon) notation or the `GetSection` extension method.</span></span> <span data-ttu-id="8bfbf-185">Per ottenere il valore di configurazione, usare uno di questi approcci:</span><span class="sxs-lookup"><span data-stu-id="8bfbf-185">Use either of these approaches to obtain the configuration value:</span></span>
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

<span data-ttu-id="8bfbf-186">Il certificato X. 509 è gestito dal sistema operativo.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-186">The X.509 certificate is managed by the OS.</span></span> <span data-ttu-id="8bfbf-187">L'app chiama **AddAzureKeyVault** con i valori forniti dal *appsettings.json* file:</span><span class="sxs-lookup"><span data-stu-id="8bfbf-187">The app calls **AddAzureKeyVault** with values supplied by the *appsettings.json* file:</span></span>

[!code-csharp[](key-vault-configuration/samples/3.x/SampleApp/Program.cs?name=snippet1&highlight=46-49)]

<span data-ttu-id="8bfbf-188">Valori di esempio</span><span class="sxs-lookup"><span data-stu-id="8bfbf-188">Example values:</span></span>

* <span data-ttu-id="8bfbf-189">Nome dell'insieme di credenziali delle chiavi: `contosovault`</span><span class="sxs-lookup"><span data-stu-id="8bfbf-189">Key vault name: `contosovault`</span></span>
* <span data-ttu-id="8bfbf-190">ID applicazione: `627e911e-43cc-61d4-992e-12db9c81b413`</span><span class="sxs-lookup"><span data-stu-id="8bfbf-190">Application ID: `627e911e-43cc-61d4-992e-12db9c81b413`</span></span>
* <span data-ttu-id="8bfbf-191">Identificazione personale del certificato: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span><span class="sxs-lookup"><span data-stu-id="8bfbf-191">Certificate thumbprint: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span></span>

<span data-ttu-id="8bfbf-192">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="8bfbf-192">*appsettings.json*:</span></span>

[!code-json[](key-vault-configuration/samples/3.x/SampleApp/appsettings.json?highlight=10-12)]

<span data-ttu-id="8bfbf-193">Quando si esegue l'app, in una pagina Web vengono visualizzati i valori dei segreti caricati.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-193">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="8bfbf-194">Nell'ambiente di sviluppo, i valori Secret vengono caricati con il `_dev` suffisso.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-194">In the Development environment, secret values load with the `_dev` suffix.</span></span> <span data-ttu-id="8bfbf-195">Nell'ambiente di produzione, i valori vengono caricati con il `_prod` suffisso.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-195">In the Production environment, the values load with the `_prod` suffix.</span></span>

## <a name="use-managed-identities-for-azure-resources"></a><span data-ttu-id="8bfbf-196">Usare identità gestite per le risorse di Azure</span><span class="sxs-lookup"><span data-stu-id="8bfbf-196">Use Managed identities for Azure resources</span></span>

<span data-ttu-id="8bfbf-197">**Un'app distribuita in Azure** può trarre vantaggio dalle [identità gestite per le risorse di Azure](/azure/active-directory/managed-identities-azure-resources/overview), che consente all'app di eseguire l'autenticazione con Azure Key Vault usando Azure ad autenticazione senza credenziali (ID applicazione e password/segreto client) archiviate nell'app.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-197">**An app deployed to Azure** can take advantage of [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview), which allows the app to authenticate with Azure Key Vault using Azure AD authentication without credentials (Application ID and Password/Client Secret) stored in the app.</span></span>

<span data-ttu-id="8bfbf-198">L'app di esempio usa le identità gestite per le risorse di Azure quando l' `#define` istruzione all'inizio del file *Program.cs* è impostata su `Managed` .</span><span class="sxs-lookup"><span data-stu-id="8bfbf-198">The sample app uses Managed identities for Azure resources when the `#define` statement at the top of the *Program.cs* file is set to `Managed`.</span></span>

<span data-ttu-id="8bfbf-199">Immettere il nome dell'insieme di credenziali nel *appsettings.json* file dell'app.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-199">Enter the vault name into the app's *appsettings.json* file.</span></span> <span data-ttu-id="8bfbf-200">L'app di esempio non richiede un ID applicazione e una password (segreto client) quando è impostata sulla `Managed` versione, in modo che sia possibile ignorare tali voci di configurazione.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-200">The sample app doesn't require an Application ID and Password (Client Secret) when set to the `Managed` version, so you can ignore those configuration entries.</span></span> <span data-ttu-id="8bfbf-201">L'app viene distribuita in Azure e Azure autentica l'app per accedere Azure Key Vault solo usando il nome dell'insieme di credenziali archiviato nel *appsettings.json* file.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-201">The app is deployed to Azure, and Azure authenticates the app to access Azure Key Vault only using the vault name stored in the *appsettings.json* file.</span></span>

<span data-ttu-id="8bfbf-202">Distribuire l'app di esempio nel servizio app Azure.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-202">Deploy the sample app to Azure App Service.</span></span>

<span data-ttu-id="8bfbf-203">Un'app distribuita nel servizio app Azure viene registrata automaticamente con Azure AD quando viene creato il servizio.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-203">An app deployed to Azure App Service is automatically registered with Azure AD when the service is created.</span></span> <span data-ttu-id="8bfbf-204">Ottenere l'ID oggetto dalla distribuzione per utilizzarlo nel comando seguente.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-204">Obtain the Object ID from the deployment for use in the following command.</span></span> <span data-ttu-id="8bfbf-205">L'ID oggetto viene visualizzato nella portale di Azure nel **Identity** Pannello del servizio app.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-205">The Object ID is shown in the Azure portal on the **Identity** panel of the App Service.</span></span>

<span data-ttu-id="8bfbf-206">Usando l'interfaccia della riga di comando di Azure e l'ID oggetto dell'app, fornire all'app le `list` `get` autorizzazioni e per accedere all'insieme di credenziali delle chiavi:</span><span class="sxs-lookup"><span data-stu-id="8bfbf-206">Using Azure CLI and the app's Object ID, provide the app with `list` and `get` permissions to access the key vault:</span></span>

```azurecli
az keyvault set-policy --name {KEY VAULT NAME} --object-id {OBJECT ID} --secret-permissions get list
```

<span data-ttu-id="8bfbf-207">**Riavviare l'app** usando l'interfaccia della riga di comando di Azure, PowerShell o la portale di Azure.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-207">**Restart the app** using Azure CLI, PowerShell, or the Azure portal.</span></span>

<span data-ttu-id="8bfbf-208">App di esempio:</span><span class="sxs-lookup"><span data-stu-id="8bfbf-208">The sample app:</span></span>

* <span data-ttu-id="8bfbf-209">Crea un'istanza della `DefaultAzureCredential` classe, la credenziale tenta di ottenere un token di accesso dall'ambiente per le risorse di Azure.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-209">Creates an instance of the `DefaultAzureCredential` class, the credential attempts to obtain an access token from environment for Azure resources.</span></span>
* <span data-ttu-id="8bfbf-210">[`Azure.Security.KeyVault.Secrets.Secrets`](/dotnet/api/azure.security.keyvault.secrets)Viene creata una nuova istanza di con l' `DefaultAzureCredential` istanza di.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-210">A new [`Azure.Security.KeyVault.Secrets.Secrets`](/dotnet/api/azure.security.keyvault.secrets) is created with the `DefaultAzureCredential` instance.</span></span>
* <span data-ttu-id="8bfbf-211">L' `Azure.Security.KeyVault.Secrets.Secrets` istanza viene utilizzata con un'implementazione predefinita di `Azure.Extensions.Aspnetcore.Configuration.Secrets` che carica tutti i valori del segreto e sostituisce i doppi trattini ( `--` ) con i due punti ( `:` ) nei nomi delle chiavi.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-211">The `Azure.Security.KeyVault.Secrets.Secrets` instance is used with a default implementation of `Azure.Extensions.Aspnetcore.Configuration.Secrets` that loads all secret values and replaces double-dashes (`--`) with colons (`:`) in key names.</span></span>

[!code-csharp[](key-vault-configuration/samples/3.x/SampleApp/Program.cs?name=snippet2&highlight=12-14)]

<span data-ttu-id="8bfbf-212">Valore di esempio del nome dell'insieme di credenziali delle chiavi: `contosovault`</span><span class="sxs-lookup"><span data-stu-id="8bfbf-212">Key vault name example value: `contosovault`</span></span>

<span data-ttu-id="8bfbf-213">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="8bfbf-213">*appsettings.json*:</span></span>

```json
{
  "KeyVaultName": "Key Vault Name"
}
```

<span data-ttu-id="8bfbf-214">Quando si esegue l'app, in una pagina Web vengono visualizzati i valori dei segreti caricati.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-214">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="8bfbf-215">Nell'ambiente di sviluppo i valori Secret hanno il `_dev` suffisso perché sono forniti da segreti utente.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-215">In the Development environment, secret values have the `_dev` suffix because they're provided by User Secrets.</span></span> <span data-ttu-id="8bfbf-216">Nell'ambiente di produzione, i valori vengono caricati con il `_prod` suffisso perché sono forniti da Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-216">In the Production environment, the values load with the `_prod` suffix because they're provided by Azure Key Vault.</span></span>

<span data-ttu-id="8bfbf-217">Se viene visualizzato un `Access denied` errore, verificare che l'app sia registrata con Azure ad e che l'accesso sia stato fornito all'insieme di credenziali delle chiavi.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-217">If you receive an `Access denied` error, confirm that the app is registered with Azure AD and provided access to the key vault.</span></span> <span data-ttu-id="8bfbf-218">Confermare di aver riavviato il servizio in Azure.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-218">Confirm that you've restarted the service in Azure.</span></span>

<span data-ttu-id="8bfbf-219">Per informazioni sull'uso del provider con un'identità gestita e una pipeline di Azure DevOps, vedere [creare una connessione del servizio Azure Resource Manager a una macchina virtuale con un'identità del servizio gestito](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span><span class="sxs-lookup"><span data-stu-id="8bfbf-219">For information on using the provider with a managed identity and an Azure DevOps pipeline, see [Create an Azure Resource Manager service connection to a VM with a managed service identity](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span></span>

## <a name="configuration-options"></a><span data-ttu-id="8bfbf-220">Opzioni di configurazione</span><span class="sxs-lookup"><span data-stu-id="8bfbf-220">Configuration options</span></span>

<span data-ttu-id="8bfbf-221">AddAzureKeyVault può accettare un AzureKeyVaultConfigurationOptions:</span><span class="sxs-lookup"><span data-stu-id="8bfbf-221">AddAzureKeyVault can accept an AzureKeyVaultConfigurationOptions:</span></span>

```csharp
config.AddAzureKeyVault(new SecretClient(new URI("Your Key Vault Endpoint"), new DefaultAzureCredential()),
                        new AzureKeyVaultConfigurationOptions())
    {
        ...
    });
```

| <span data-ttu-id="8bfbf-222">Proprietà</span><span class="sxs-lookup"><span data-stu-id="8bfbf-222">Property</span></span>         | <span data-ttu-id="8bfbf-223">Descrizione</span><span class="sxs-lookup"><span data-stu-id="8bfbf-223">Description</span></span> |
| ---------------- | ----------- |
| `Manager`        | <span data-ttu-id="8bfbf-224">`Azure.Extensions.Aspnetcore.Configuration.Secrets` istanza di utilizzata per controllare il caricamento del segreto.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-224">`Azure.Extensions.Aspnetcore.Configuration.Secrets` instance used to control secret loading.</span></span> |
| `ReloadInterval` | <span data-ttu-id="8bfbf-225">`Timespan` per attendere tra i tentativi di polling dell'insieme di credenziali delle chiavi per le modifiche.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-225">`Timespan` to wait between attempts at polling the key vault for changes.</span></span> <span data-ttu-id="8bfbf-226">Il valore predefinito è `null` (la configurazione non viene ricaricata).</span><span class="sxs-lookup"><span data-stu-id="8bfbf-226">The default value is `null` (configuration isn't reloaded).</span></span> |

## <a name="use-a-key-name-prefix"></a><span data-ttu-id="8bfbf-227">Usa prefisso nome chiave</span><span class="sxs-lookup"><span data-stu-id="8bfbf-227">Use a key name prefix</span></span>

<span data-ttu-id="8bfbf-228">AddAzureKeyVault fornisce un overload che accetta un'implementazione di `Azure.Extensions.Aspnetcore.Configuration.Secrets` , che consente di controllare il modo in cui i segreti dell'insieme di credenziali delle chiavi vengono convertiti in chiavi di configurazione.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-228">AddAzureKeyVault provides an overload that accepts an implementation of `Azure.Extensions.Aspnetcore.Configuration.Secrets`, which allows you to control how key vault secrets are converted into configuration keys.</span></span> <span data-ttu-id="8bfbf-229">Ad esempio, è possibile implementare l'interfaccia per caricare i valori dei segreti in base a un valore di prefisso fornito all'avvio dell'app.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-229">For example, you can implement the interface to load secret values based on a prefix value you provide at app startup.</span></span> <span data-ttu-id="8bfbf-230">Questo consente, ad esempio, di caricare i segreti in base alla versione dell'app.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-230">This allows you, for example, to load secrets based on the version of the app.</span></span>

> [!WARNING]
> <span data-ttu-id="8bfbf-231">Non usare i prefissi sui segreti dell'insieme di credenziali delle chiavi per collocare i segreti per più app nello stesso insieme di credenziali delle chiavi o per collocare i segreti ambientali (ad esempio, *sviluppo* rispetto ai segreti di *produzione* ) nello stesso insieme di credenziali.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-231">Don't use prefixes on key vault secrets to place secrets for multiple apps into the same key vault or to place environmental secrets (for example, *development* versus *production* secrets) into the same vault.</span></span> <span data-ttu-id="8bfbf-232">È consigliabile che app e ambienti di sviluppo/produzione diversi usino insiemi di credenziali delle chiavi distinti per isolare gli ambienti app per il massimo livello di sicurezza.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-232">We recommend that different apps and development/production environments use separate key vaults to isolate app environments for the highest level of security.</span></span>

<span data-ttu-id="8bfbf-233">Nell'esempio seguente viene stabilito un segreto nell'insieme di credenziali delle chiavi (e usando lo strumento di gestione dei segreti per l'ambiente di sviluppo) per `5000-AppSecret` (i periodi non sono consentiti nei nomi dei segreti dell'insieme di credenziali delle chiavi).</span><span class="sxs-lookup"><span data-stu-id="8bfbf-233">In the following example, a secret is established in the key vault (and using the Secret Manager tool for the Development environment) for `5000-AppSecret` (periods aren't allowed in key vault secret names).</span></span> <span data-ttu-id="8bfbf-234">Questo segreto rappresenta un segreto app per la versione 5.0.0.0 dell'app.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-234">This secret represents an app secret for version 5.0.0.0 of the app.</span></span> <span data-ttu-id="8bfbf-235">Per un'altra versione dell'app, 5.1.0.0, viene aggiunto un segreto all'insieme di credenziali delle chiavi (e usando lo strumento di gestione dei segreti) per `5100-AppSecret` .</span><span class="sxs-lookup"><span data-stu-id="8bfbf-235">For another version of the app, 5.1.0.0, a secret is added to the key vault (and using the Secret Manager tool) for `5100-AppSecret`.</span></span> <span data-ttu-id="8bfbf-236">Ogni versione dell'app carica il valore del segreto con versione nella configurazione come `AppSecret` , rimuovendo la versione durante il caricamento del segreto.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-236">Each app version loads its versioned secret value into its configuration as `AppSecret`, stripping off the version as it loads the secret.</span></span>

<span data-ttu-id="8bfbf-237">AddAzureKeyVault viene chiamato con un oggetto personalizzato `Azure.Extensions.Aspnetcore.Configuration.Secrets` :</span><span class="sxs-lookup"><span data-stu-id="8bfbf-237">AddAzureKeyVault is called with a custom `Azure.Extensions.Aspnetcore.Configuration.Secrets`:</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Program.cs)]

<span data-ttu-id="8bfbf-238">L' `Azure.Extensions.Aspnetcore.Configuration.Secrets` implementazione reagisce ai prefissi di versione dei segreti per caricare il segreto appropriato nella configurazione:</span><span class="sxs-lookup"><span data-stu-id="8bfbf-238">The `Azure.Extensions.Aspnetcore.Configuration.Secrets` implementation reacts to the version prefixes of secrets to load the proper secret into configuration:</span></span>

* <span data-ttu-id="8bfbf-239">`Load` carica un segreto quando il nome inizia con il prefisso.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-239">`Load` loads a secret when its name starts with the prefix.</span></span> <span data-ttu-id="8bfbf-240">Non vengono caricati altri segreti.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-240">Other secrets aren't loaded.</span></span>
* <span data-ttu-id="8bfbf-241">`GetKey`:</span><span class="sxs-lookup"><span data-stu-id="8bfbf-241">`GetKey`:</span></span>
  * <span data-ttu-id="8bfbf-242">Rimuove il prefisso dal nome del segreto.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-242">Removes the prefix from the secret name.</span></span>
  * <span data-ttu-id="8bfbf-243">Sostituisce due trattini in qualsiasi nome con `KeyDelimiter` , che è il delimitatore usato nella configurazione (in genere i due punti).</span><span class="sxs-lookup"><span data-stu-id="8bfbf-243">Replaces two dashes in any name with the `KeyDelimiter`, which is the delimiter used in configuration (usually a colon).</span></span> <span data-ttu-id="8bfbf-244">Azure Key Vault non consente i due punti nei nomi dei segreti.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-244">Azure Key Vault doesn't allow a colon in secret names.</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Startup.cs)]

<span data-ttu-id="8bfbf-245">Il `Load` metodo viene chiamato da un algoritmo del provider che scorre i segreti dell'insieme di credenziali per trovare quelli con il prefisso della versione.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-245">The `Load` method is called by a provider algorithm that iterates through the vault secrets to find the ones that have the version prefix.</span></span> <span data-ttu-id="8bfbf-246">Quando viene trovato un prefisso di versione con `Load` , l'algoritmo usa il `GetKey` metodo per restituire il nome della configurazione del nome del segreto.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-246">When a version prefix is found with `Load`, the algorithm uses the `GetKey` method to return the configuration name of the secret name.</span></span> <span data-ttu-id="8bfbf-247">Rimuove il prefisso della versione dal nome del segreto e restituisce il resto del nome del segreto per il caricamento nelle coppie nome-valore della configurazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-247">It strips off the version prefix from the secret's name and returns the rest of the secret name for loading into the app's configuration name-value pairs.</span></span>

<span data-ttu-id="8bfbf-248">Quando viene implementato questo approccio:</span><span class="sxs-lookup"><span data-stu-id="8bfbf-248">When this approach is implemented:</span></span>

1. <span data-ttu-id="8bfbf-249">La versione dell'app specificata nel file di progetto dell'app.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-249">The app's version specified in the app's project file.</span></span> <span data-ttu-id="8bfbf-250">Nell'esempio seguente la versione dell'app è impostata su `5.0.0.0` :</span><span class="sxs-lookup"><span data-stu-id="8bfbf-250">In the following example, the app's version is set to `5.0.0.0`:</span></span>

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. <span data-ttu-id="8bfbf-251">Verificare che `<UserSecretsId>` sia presente una proprietà nel file di progetto dell'app, dove `{GUID}` è un GUID fornito dall'utente:</span><span class="sxs-lookup"><span data-stu-id="8bfbf-251">Confirm that a `<UserSecretsId>` property is present in the app's project file, where `{GUID}` is a user-supplied GUID:</span></span>

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   <span data-ttu-id="8bfbf-252">Salvare i segreti seguenti localmente con lo [strumento di gestione dei segreti](xref:security/app-secrets):</span><span class="sxs-lookup"><span data-stu-id="8bfbf-252">Save the following secrets locally with the [Secret Manager tool](xref:security/app-secrets):</span></span>

   ```dotnetcli
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. <span data-ttu-id="8bfbf-253">I segreti vengono salvati in Azure Key Vault usando i comandi dell'interfaccia della riga di comando di Azure seguenti:</span><span class="sxs-lookup"><span data-stu-id="8bfbf-253">Secrets are saved in Azure Key Vault using the following Azure CLI commands:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. <span data-ttu-id="8bfbf-254">Quando l'app viene eseguita, vengono caricati i segreti dell'insieme di credenziali delle chiavi.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-254">When the app is run, the key vault secrets are loaded.</span></span> <span data-ttu-id="8bfbf-255">La stringa segreta per `5000-AppSecret` viene confrontata con la versione dell'app specificata nel file di progetto dell'app ( `5.0.0.0` ).</span><span class="sxs-lookup"><span data-stu-id="8bfbf-255">The string secret for `5000-AppSecret` is matched to the app's version specified in the app's project file (`5.0.0.0`).</span></span>

1. <span data-ttu-id="8bfbf-256">La versione `5000` (con il trattino) viene rimossa dal nome della chiave.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-256">The version, `5000` (with the dash), is stripped from the key name.</span></span> <span data-ttu-id="8bfbf-257">In tutta l'app, la lettura della configurazione con la chiave `AppSecret` carica il valore del segreto.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-257">Throughout the app, reading configuration with the key `AppSecret` loads the secret value.</span></span>

1. <span data-ttu-id="8bfbf-258">Se la versione dell'app viene modificata nel file di progetto in `5.1.0.0` e l'app viene nuovamente eseguita, il valore del segreto restituito è `5.1.0.0_secret_value_dev` nell'ambiente di sviluppo e `5.1.0.0_secret_value_prod` in produzione.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-258">If the app's version is changed in the project file to `5.1.0.0` and the app is run again, the secret value returned is `5.1.0.0_secret_value_dev` in the Development environment and `5.1.0.0_secret_value_prod` in Production.</span></span>

> [!NOTE]
> <span data-ttu-id="8bfbf-259">È anche possibile fornire un' <xref:Azure.Security.KeyVault.Secrets.SecretClient> implementazione personalizzata a AddAzureKeyVault.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-259">You can also provide your own <xref:Azure.Security.KeyVault.Secrets.SecretClient> implementation to AddAzureKeyVault.</span></span> <span data-ttu-id="8bfbf-260">Un client personalizzato consente la condivisione di una singola istanza del client nell'app.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-260">A custom client permits sharing a single instance of the client across the app.</span></span>

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="8bfbf-261">Associare una matrice a una classe</span><span class="sxs-lookup"><span data-stu-id="8bfbf-261">Bind an array to a class</span></span>

<span data-ttu-id="8bfbf-262">Il provider è in grado di leggere i valori di configurazione in una matrice per l'associazione a una matrice POCO.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-262">The provider is capable of reading configuration values into an array for binding to a POCO array.</span></span>

<span data-ttu-id="8bfbf-263">Quando si legge da un'origine di configurazione che consente alle chiavi di contenere separatori di due punti ( `:` ), viene usato un segmento di chiave numerico per distinguere le chiavi che costituiscono una matrice ( `:0:` , `:1:` , &hellip; `:{n}:` ).</span><span class="sxs-lookup"><span data-stu-id="8bfbf-263">When reading from a configuration source that allows keys to contain colon (`:`) separators, a numeric key segment is used to distinguish the keys that make up an array (`:0:`, `:1:`, &hellip; `:{n}:`).</span></span> <span data-ttu-id="8bfbf-264">Per altre informazioni, vedere [Configuration: associare una matrice a una classe](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span><span class="sxs-lookup"><span data-stu-id="8bfbf-264">For more information, see [Configuration: Bind an array to a class](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span></span>

<span data-ttu-id="8bfbf-265">Azure Key Vault chiavi non possono utilizzare i due punti come separatore.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-265">Azure Key Vault keys can't use a colon as a separator.</span></span> <span data-ttu-id="8bfbf-266">L'approccio descritto in questo argomento USA trattini doppi ( `--` ) come separatore per i valori gerarchici (sezioni).</span><span class="sxs-lookup"><span data-stu-id="8bfbf-266">The approach described in this topic uses double dashes (`--`) as a separator for hierarchical values (sections).</span></span> <span data-ttu-id="8bfbf-267">Le chiavi di matrice vengono archiviate in Azure Key Vault con trattini doppi e segmenti di chiave numerica ( `--0--` , `--1--` , &hellip; `--{n}--` ).</span><span class="sxs-lookup"><span data-stu-id="8bfbf-267">Array keys are stored in Azure Key Vault with double dashes and numeric key segments (`--0--`, `--1--`, &hellip; `--{n}--`).</span></span>

<span data-ttu-id="8bfbf-268">Esaminare la seguente configurazione del provider di registrazione [Serilog](https://serilog.net/) fornita da un file JSON.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-268">Examine the following [Serilog](https://serilog.net/) logging provider configuration provided by a JSON file.</span></span> <span data-ttu-id="8bfbf-269">Nella matrice sono definiti due valori letterali di oggetto `WriteTo` che riflettono due *sink* Serilog, che descrivono le destinazioni per la registrazione dell'output:</span><span class="sxs-lookup"><span data-stu-id="8bfbf-269">There are two object literals defined in the `WriteTo` array that reflect two Serilog *sinks*, which describe destinations for logging output:</span></span>

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

<span data-ttu-id="8bfbf-270">La configurazione mostrata nel file JSON precedente viene archiviata in Azure Key Vault usando la notazione a doppio trattino ( `--` ) e i segmenti numerici:</span><span class="sxs-lookup"><span data-stu-id="8bfbf-270">The configuration shown in the preceding JSON file is stored in Azure Key Vault using double dash (`--`) notation and numeric segments:</span></span>

| <span data-ttu-id="8bfbf-271">Chiave</span><span class="sxs-lookup"><span data-stu-id="8bfbf-271">Key</span></span> | <span data-ttu-id="8bfbf-272">valore</span><span class="sxs-lookup"><span data-stu-id="8bfbf-272">Value</span></span> |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a><span data-ttu-id="8bfbf-273">Ricarica segreti</span><span class="sxs-lookup"><span data-stu-id="8bfbf-273">Reload secrets</span></span>

<span data-ttu-id="8bfbf-274">I segreti vengono memorizzati nella cache finché non `IConfigurationRoot.Reload()` viene chiamato.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-274">Secrets are cached until `IConfigurationRoot.Reload()` is called.</span></span> <span data-ttu-id="8bfbf-275">I segreti scaduti, disabilitati e aggiornati nell'insieme di credenziali delle chiavi non vengono rispettati dall'app fino a quando non `Reload` viene eseguito.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-275">Expired, disabled, and updated secrets in the key vault are not respected by the app until `Reload` is executed.</span></span>

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a><span data-ttu-id="8bfbf-276">Segreti disabilitati e scaduti</span><span class="sxs-lookup"><span data-stu-id="8bfbf-276">Disabled and expired secrets</span></span>

<span data-ttu-id="8bfbf-277">I segreti disabilitati e scaduti generano un' <xref:Azure.RequestFailedException> .</span><span class="sxs-lookup"><span data-stu-id="8bfbf-277">Disabled and expired secrets throw a <xref:Azure.RequestFailedException>.</span></span> <span data-ttu-id="8bfbf-278">Per impedire che l'app venga generata, fornire la configurazione usando un provider di configurazione diverso o aggiornare il segreto disabilitato o scaduto.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-278">To prevent the app from throwing, provide the configuration using a different configuration provider or update the disabled or expired secret.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="8bfbf-279">Risolvere problemi</span><span class="sxs-lookup"><span data-stu-id="8bfbf-279">Troubleshoot</span></span>

<span data-ttu-id="8bfbf-280">Quando l'app non riesce a caricare la configurazione usando il provider, viene scritto un messaggio di errore nell' [infrastruttura di registrazione ASP.NET Core](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="8bfbf-280">When the app fails to load configuration using the provider, an error message is written to the [ASP.NET Core Logging infrastructure](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="8bfbf-281">Le condizioni seguenti impediranno il caricamento della configurazione:</span><span class="sxs-lookup"><span data-stu-id="8bfbf-281">The following conditions will prevent configuration from loading:</span></span>

* <span data-ttu-id="8bfbf-282">L'app o il certificato non è configurato correttamente in Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-282">The app or certificate isn't configured correctly in Azure Active Directory.</span></span>
* <span data-ttu-id="8bfbf-283">L'insieme di credenziali delle chiavi non esiste in Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-283">The key vault doesn't exist in Azure Key Vault.</span></span>
* <span data-ttu-id="8bfbf-284">L'app non è autorizzata ad accedere all'insieme di credenziali delle chiavi.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-284">The app isn't authorized to access the key vault.</span></span>
* <span data-ttu-id="8bfbf-285">Il criterio di accesso non include le `Get` `List` autorizzazioni e.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-285">The access policy doesn't include `Get` and `List` permissions.</span></span>
* <span data-ttu-id="8bfbf-286">Nell'insieme di credenziali delle chiavi i dati di configurazione (coppia nome-valore) sono denominati, mancanti, disabilitati o scaduti in modo errato.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-286">In the key vault, the configuration data (name-value pair) is incorrectly named, missing, disabled, or expired.</span></span>
* <span data-ttu-id="8bfbf-287">L'app presenta il nome dell'insieme di credenziali delle chiavi ( `KeyVaultName` ), Azure ad ID applicazione () o l'identificazione `AzureADApplicationId` personale del certificato Azure ad ( `AzureADCertThumbprint` ) o Azure ad DirectoryId ( `AzureADDirectoryId` ).</span><span class="sxs-lookup"><span data-stu-id="8bfbf-287">The app has the wrong key vault name (`KeyVaultName`), Azure AD Application Id (`AzureADApplicationId`), or Azure AD certificate thumbprint (`AzureADCertThumbprint`), or Azure AD DirectoryId (`AzureADDirectoryId`).</span></span>
* <span data-ttu-id="8bfbf-288">La chiave di configurazione (nome) non è corretta nell'app per il valore che si sta provando a caricare.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-288">The configuration key (name) is incorrect in the app for the value you're trying to load.</span></span>
* <span data-ttu-id="8bfbf-289">Quando si aggiungono i criteri di accesso per l'app all'insieme di credenziali delle chiavi, il criterio è stato creato, ma il pulsante **Salva** non è stato selezionato nell'interfaccia utente dei **criteri di accesso** .</span><span class="sxs-lookup"><span data-stu-id="8bfbf-289">When adding the access policy for the app to the key vault, the policy was created, but the **Save** button wasn't selected in the **Access policies** UI.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8bfbf-290">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="8bfbf-290">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
* [<span data-ttu-id="8bfbf-291">Microsoft Azure: Key Vault</span><span class="sxs-lookup"><span data-stu-id="8bfbf-291">Microsoft Azure: Key Vault</span></span>](https://azure.microsoft.com/services/key-vault/)
* [<span data-ttu-id="8bfbf-292">Microsoft Azure: Key Vault documentazione</span><span class="sxs-lookup"><span data-stu-id="8bfbf-292">Microsoft Azure: Key Vault Documentation</span></span>](/azure/key-vault/)
* [<span data-ttu-id="8bfbf-293">Come generare e trasferire chiavi HSM protette per Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="8bfbf-293">How to generate and transfer HSM-protected keys for Azure Key Vault</span></span>](/azure/key-vault/key-vault-hsm-protected-keys)
* [<span data-ttu-id="8bfbf-294">Classe KeyVaultClient</span><span class="sxs-lookup"><span data-stu-id="8bfbf-294">KeyVaultClient Class</span></span>](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [<span data-ttu-id="8bfbf-295">Guida introduttiva: Impostare e recuperare un segreto da Azure Key Vault tramite un'app Web .NET</span><span class="sxs-lookup"><span data-stu-id="8bfbf-295">Quickstart: Set and retrieve a secret from Azure Key Vault by using a .NET web app</span></span>](/azure/key-vault/quick-create-net)
* [<span data-ttu-id="8bfbf-296">Esercitazione: Come usare Azure Key Vault con una macchina virtuale Windows di Azure in .NET</span><span class="sxs-lookup"><span data-stu-id="8bfbf-296">Tutorial: How to use Azure Key Vault with Azure Windows Virtual Machine in .NET</span></span>](/azure/key-vault/tutorial-net-windows-virtual-machine)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="8bfbf-297">Questo documento illustra come usare il provider di configurazione [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) per caricare i valori di configurazione dell'app da Azure Key Vault segreti.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-297">This document explains how to use the [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) Configuration Provider to load app configuration values from Azure Key Vault secrets.</span></span> <span data-ttu-id="8bfbf-298">Azure Key Vault è un servizio basato sul cloud che aiuta a proteggere le chiavi crittografiche e i segreti usati da app e servizi.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-298">Azure Key Vault is a cloud-based service that assists in safeguarding cryptographic keys and secrets used by apps and services.</span></span> <span data-ttu-id="8bfbf-299">Gli scenari comuni per l'uso di Azure Key Vault con le app ASP.NET Core includono:</span><span class="sxs-lookup"><span data-stu-id="8bfbf-299">Common scenarios for using Azure Key Vault with ASP.NET Core apps include:</span></span>

* <span data-ttu-id="8bfbf-300">Controllo dell'accesso ai dati di configurazione riservati.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-300">Controlling access to sensitive configuration data.</span></span>
* <span data-ttu-id="8bfbf-301">Soddisfare i requisiti per i moduli di protezione hardware convalidati FIPS 140-2 Level 2 (HSM) quando si archiviano i dati di configurazione.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-301">Meeting the requirement for FIPS 140-2 Level 2 validated Hardware Security Modules (HSM's) when storing configuration data.</span></span>

<span data-ttu-id="8bfbf-302">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="8bfbf-302">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="packages"></a><span data-ttu-id="8bfbf-303">Pacchetti</span><span class="sxs-lookup"><span data-stu-id="8bfbf-303">Packages</span></span>

<span data-ttu-id="8bfbf-304">Aggiungere un riferimento al pacchetto [Microsoft.Extensions.Configuration. ](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) Pacchetto di AzureKeyVault.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-304">Add a package reference to the [Microsoft.Extensions.Configuration.AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) package.</span></span>

## <a name="sample-app"></a><span data-ttu-id="8bfbf-305">App di esempio</span><span class="sxs-lookup"><span data-stu-id="8bfbf-305">Sample app</span></span>

<span data-ttu-id="8bfbf-306">L'app di esempio viene eseguita in una delle due modalità determinate dall' `#define` istruzione all'inizio del file *Program.cs* :</span><span class="sxs-lookup"><span data-stu-id="8bfbf-306">The sample app runs in either of two modes determined by the `#define` statement at the top of the *Program.cs* file:</span></span>

* <span data-ttu-id="8bfbf-307">`Certificate`: Illustra l'uso di un ID client Azure Key Vault e di un certificato X. 509 per accedere ai segreti archiviati nel Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-307">`Certificate`: Demonstrates the use of an Azure Key Vault Client ID and X.509 certificate to access secrets stored in Azure Key Vault.</span></span> <span data-ttu-id="8bfbf-308">Questa versione dell'esempio può essere eseguita da qualsiasi posizione, distribuita nel servizio app Azure o in qualsiasi host in grado di servire un'app ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-308">This version of the sample can be run from any location, deployed to Azure App Service or any host capable of serving an ASP.NET Core app.</span></span>
* <span data-ttu-id="8bfbf-309">`Managed`: Illustra come usare le [identità gestite per le risorse di Azure](/azure/active-directory/managed-identities-azure-resources/overview) per autenticare l'app Azure Key Vault con Azure ad autenticazione senza credenziali archiviate nel codice o nella configurazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-309">`Managed`: Demonstrates how to use [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview) to authenticate the app to Azure Key Vault with Azure AD authentication without credentials stored in the app's code or configuration.</span></span> <span data-ttu-id="8bfbf-310">Quando si usano identità gestite per l'autenticazione, non è necessario un Azure AD ID applicazione e una password (segreto client).</span><span class="sxs-lookup"><span data-stu-id="8bfbf-310">When using managed identities to authenticate, an Azure AD Application ID and Password (Client Secret) aren't required.</span></span> <span data-ttu-id="8bfbf-311">La `Managed` versione dell'esempio deve essere distribuita in Azure.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-311">The `Managed` version of the sample must be deployed to Azure.</span></span> <span data-ttu-id="8bfbf-312">Seguire le istruzioni riportate nella sezione [usare le identità gestite per le risorse di Azure](#use-managed-identities-for-azure-resources) .</span><span class="sxs-lookup"><span data-stu-id="8bfbf-312">Follow the guidance in the [Use the Managed identities for Azure resources](#use-managed-identities-for-azure-resources) section.</span></span>

<span data-ttu-id="8bfbf-313">Per ulteriori informazioni su come configurare un'app di esempio utilizzando le direttive per il preprocessore ( `#define` ), vedere <xref:index#preprocessor-directives-in-sample-code> .</span><span class="sxs-lookup"><span data-stu-id="8bfbf-313">For more information on how to configure a sample app using preprocessor directives (`#define`), see <xref:index#preprocessor-directives-in-sample-code>.</span></span>

## <a name="secret-storage-in-the-development-environment"></a><span data-ttu-id="8bfbf-314">Archiviazione segreta nell'ambiente di sviluppo</span><span class="sxs-lookup"><span data-stu-id="8bfbf-314">Secret storage in the Development environment</span></span>

<span data-ttu-id="8bfbf-315">Impostare i segreti in locale usando lo [strumento di gestione dei segreti](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="8bfbf-315">Set secrets locally using the [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="8bfbf-316">Quando l'app di esempio viene eseguita nel computer locale nell'ambiente di sviluppo, i segreti vengono caricati dall'archivio dei segreti dell'utente locale.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-316">When the sample app runs on the local machine in the Development environment, secrets are loaded from the local user secrets store.</span></span>

<span data-ttu-id="8bfbf-317">Lo strumento di gestione dei segreti richiede una `<UserSecretsId>` proprietà nel file di progetto dell'app.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-317">The Secret Manager tool requires a `<UserSecretsId>` property in the app's project file.</span></span> <span data-ttu-id="8bfbf-318">Impostare il valore della proprietà ( `{GUID}` ) su un GUID univoco:</span><span class="sxs-lookup"><span data-stu-id="8bfbf-318">Set the property value (`{GUID}`) to any unique GUID:</span></span>

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

<span data-ttu-id="8bfbf-319">I segreti vengono creati come coppie nome/valore.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-319">Secrets are created as name-value pairs.</span></span> <span data-ttu-id="8bfbf-320">I valori gerarchici (sezioni di configurazione) utilizzano un `:` (due punti) come separatore nei nomi delle chiavi di [configurazione ASP.NET Core](xref:fundamentals/configuration/index) .</span><span class="sxs-lookup"><span data-stu-id="8bfbf-320">Hierarchical values (configuration sections) use a `:` (colon) as a separator in [ASP.NET Core configuration](xref:fundamentals/configuration/index) key names.</span></span>

<span data-ttu-id="8bfbf-321">Il gestore del segreto viene usato da una shell dei comandi aperta alla [radice del contenuto](xref:fundamentals/index#content-root)del progetto, dove `{SECRET NAME}` è il nome e `{SECRET VALUE}` è il valore:</span><span class="sxs-lookup"><span data-stu-id="8bfbf-321">The Secret Manager is used from a command shell opened to the project's [content root](xref:fundamentals/index#content-root), where `{SECRET NAME}` is the name and `{SECRET VALUE}` is the value:</span></span>

```dotnetcli
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

<span data-ttu-id="8bfbf-322">Eseguire i comandi seguenti in una shell dei comandi dalla radice del [contenuto](xref:fundamentals/index#content-root) del progetto per impostare i segreti per l'app di esempio:</span><span class="sxs-lookup"><span data-stu-id="8bfbf-322">Execute the following commands in a command shell from the project's [content root](xref:fundamentals/index#content-root) to set the secrets for the sample app:</span></span>

```dotnetcli
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

<span data-ttu-id="8bfbf-323">Quando questi segreti vengono archiviati in Azure Key Vault nell' [archiviazione segreta nell'ambiente di produzione con Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) sezione, il `_dev` suffisso viene modificato in `_prod` .</span><span class="sxs-lookup"><span data-stu-id="8bfbf-323">When these secrets are stored in Azure Key Vault in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section, the `_dev` suffix is changed to `_prod`.</span></span> <span data-ttu-id="8bfbf-324">Il suffisso fornisce un segnale visivo nell'output dell'app che indica l'origine dei valori di configurazione.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-324">The suffix provides a visual cue in the app's output indicating the source of the configuration values.</span></span>

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a><span data-ttu-id="8bfbf-325">Archiviazione segreta nell'ambiente di produzione con Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="8bfbf-325">Secret storage in the Production environment with Azure Key Vault</span></span>

<span data-ttu-id="8bfbf-326">Le istruzioni fornite nell'argomento [avvio rapido: impostare e recuperare un segreto da Azure Key Vault usando l'interfaccia](/azure/key-vault/quick-create-cli) della riga di comando di Azure sono riepilogate qui per la creazione di un Azure Key Vault e l'archiviazione di segreti usati dall'app di esempio.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-326">The instructions provided by the [Quickstart: Set and retrieve a secret from Azure Key Vault using Azure CLI](/azure/key-vault/quick-create-cli) topic are summarized here for creating an Azure Key Vault and storing secrets used by the sample app.</span></span> <span data-ttu-id="8bfbf-327">Per ulteriori informazioni, vedere l'argomento.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-327">Refer to the topic for further details.</span></span>

1. <span data-ttu-id="8bfbf-328">Aprire Azure cloud shell usando uno dei metodi seguenti nel [portale di Azure](https://portal.azure.com/):</span><span class="sxs-lookup"><span data-stu-id="8bfbf-328">Open Azure Cloud shell using any one of the following methods in the [Azure portal](https://portal.azure.com/):</span></span>

   * <span data-ttu-id="8bfbf-329">Selezionare **Prova** nell'angolo superiore destro di un blocco di codice.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-329">Select **Try It** in the upper-right corner of a code block.</span></span> <span data-ttu-id="8bfbf-330">Usare la stringa di ricerca "interfaccia della riga di comando di Azure" nella casella di testo.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-330">Use the search string "Azure CLI" in the text box.</span></span>
   * <span data-ttu-id="8bfbf-331">Aprire Cloud Shell nel browser con il pulsante **avvia cloud Shell** .</span><span class="sxs-lookup"><span data-stu-id="8bfbf-331">Open Cloud Shell in your browser with the **Launch Cloud Shell** button.</span></span>
   * <span data-ttu-id="8bfbf-332">Selezionare il pulsante **Cloud Shell** nel menu nell'angolo in alto a destra del portale di Azure.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-332">Select the **Cloud Shell** button on the menu in the upper-right corner of the Azure portal.</span></span>

   <span data-ttu-id="8bfbf-333">Per altre informazioni, vedere l'interfaccia della riga di comando di [Azure](/cli/azure/) e [Panoramica di Azure cloud Shell](/azure/cloud-shell/overview).</span><span class="sxs-lookup"><span data-stu-id="8bfbf-333">For more information, see [Azure CLI](/cli/azure/) and [Overview of Azure Cloud Shell](/azure/cloud-shell/overview).</span></span>

1. <span data-ttu-id="8bfbf-334">Se non è già stato eseguito l'autenticazione, effettuare l'accesso con il `az login` comando.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-334">If you aren't already authenticated, sign in with the `az login` command.</span></span>

1. <span data-ttu-id="8bfbf-335">Creare un gruppo di risorse con il comando seguente, dove `{RESOURCE GROUP NAME}` è il nome del gruppo di risorse per il nuovo gruppo di risorse e `{LOCATION}` è l'area di Azure (Datacenter):</span><span class="sxs-lookup"><span data-stu-id="8bfbf-335">Create a resource group with the following command, where `{RESOURCE GROUP NAME}` is the resource group name for the new resource group and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="8bfbf-336">Creare un insieme di credenziali delle chiavi nel gruppo di risorse con il comando seguente, dove è il nome del nuovo insieme di credenziali delle `{KEY VAULT NAME}` chiavi e `{LOCATION}` è l'area di Azure (Datacenter):</span><span class="sxs-lookup"><span data-stu-id="8bfbf-336">Create a key vault in the resource group with the following command, where `{KEY VAULT NAME}` is the name for the new key vault and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az keyvault create --name {KEY VAULT NAME} --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="8bfbf-337">Creare segreti nell'insieme di credenziali delle chiavi come coppie nome/valore.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-337">Create secrets in the key vault as name-value pairs.</span></span>

   <span data-ttu-id="8bfbf-338">Azure Key Vault nomi di segreto sono limitati a caratteri alfanumerici e trattini.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-338">Azure Key Vault secret names are limited to alphanumeric characters and dashes.</span></span> <span data-ttu-id="8bfbf-339">I valori gerarchici (sezioni di configurazione) usano `--` (due trattini) come separatore.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-339">Hierarchical values (configuration sections) use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="8bfbf-340">I due punti, che in genere vengono usati per delimitare una sezione da una sottochiave in [ASP.NET Core configurazione](xref:fundamentals/configuration/index), non sono consentiti nei nomi dei segreti di Key Vault.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-340">Colons, which are normally used to delimit a section from a subkey in [ASP.NET Core configuration](xref:fundamentals/configuration/index), aren't allowed in key vault secret names.</span></span> <span data-ttu-id="8bfbf-341">Pertanto, vengono usati due trattini e scambiati per i due punti quando i segreti vengono caricati nella configurazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-341">Therefore, two dashes are used and swapped for a colon when the secrets are loaded into the app's configuration.</span></span>

   <span data-ttu-id="8bfbf-342">I segreti seguenti sono da usare con l'app di esempio.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-342">The following secrets are for use with the sample app.</span></span> <span data-ttu-id="8bfbf-343">I valori includono un `_prod` suffisso per distinguerli dai `_dev` valori dei suffissi caricati nell'ambiente di sviluppo da segreti utente.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-343">The values include a `_prod` suffix to distinguish them from the `_dev` suffix values loaded in the Development environment from User Secrets.</span></span> <span data-ttu-id="8bfbf-344">Sostituire `{KEY VAULT NAME}` con il nome dell'insieme di credenziali delle chiavi creato nel passaggio precedente:</span><span class="sxs-lookup"><span data-stu-id="8bfbf-344">Replace `{KEY VAULT NAME}` with the name of the key vault that you created in the prior step:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a><span data-ttu-id="8bfbf-345">Usare l'ID applicazione e il certificato X. 509 per le app non ospitate in Azure</span><span class="sxs-lookup"><span data-stu-id="8bfbf-345">Use Application ID and X.509 certificate for non-Azure-hosted apps</span></span>

<span data-ttu-id="8bfbf-346">Configurare Azure AD, Azure Key Vault e l'app per usare un ID applicazione Azure Active Directory e un certificato X. 509 per l'autenticazione in un insieme di credenziali **delle chiavi quando l'app è ospitata all'esterno di Azure**.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-346">Configure Azure AD, Azure Key Vault, and the app to use an Azure Active Directory Application ID and X.509 certificate to authenticate to a key vault **when the app is hosted outside of Azure**.</span></span> <span data-ttu-id="8bfbf-347">Per i dettagli, vedere l'articolo relativo alle [informazioni su chiavi, segreti e certificati](/azure/key-vault/about-keys-secrets-and-certificates).</span><span class="sxs-lookup"><span data-stu-id="8bfbf-347">For more information, see [About keys, secrets, and certificates](/azure/key-vault/about-keys-secrets-and-certificates).</span></span>

> [!NOTE]
> <span data-ttu-id="8bfbf-348">Sebbene l'uso di un ID applicazione e di un certificato X. 509 sia supportato per le app ospitate in Azure, è consigliabile usare [identità gestite per le risorse di Azure](#use-managed-identities-for-azure-resources) quando si ospita un'app in Azure.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-348">Although using an Application ID and X.509 certificate is supported for apps hosted in Azure, we recommend using [Managed identities for Azure resources](#use-managed-identities-for-azure-resources) when hosting an app in Azure.</span></span> <span data-ttu-id="8bfbf-349">Le identità gestite non richiedono l'archiviazione di un certificato nell'app o nell'ambiente di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-349">Managed identities don't require storing a certificate in the app or in the development environment.</span></span>

<span data-ttu-id="8bfbf-350">L'app di esempio usa un ID applicazione e un certificato X. 509 quando l' `#define` istruzione all'inizio del file *Program.cs* è impostata su `Certificate` .</span><span class="sxs-lookup"><span data-stu-id="8bfbf-350">The sample app uses an Application ID and X.509 certificate when the `#define` statement at the top of the *Program.cs* file is set to `Certificate`.</span></span>

1. <span data-ttu-id="8bfbf-351">Creare un certificato di archivio PKCS # 12 (*. pfx*).</span><span class="sxs-lookup"><span data-stu-id="8bfbf-351">Create a PKCS#12 archive (*.pfx*) certificate.</span></span> <span data-ttu-id="8bfbf-352">Le opzioni per la creazione di certificati includono [Makecert in Windows](/windows/desktop/seccrypto/makecert) e [openssl](https://www.openssl.org/).</span><span class="sxs-lookup"><span data-stu-id="8bfbf-352">Options for creating certificates include [MakeCert on Windows](/windows/desktop/seccrypto/makecert) and [OpenSSL](https://www.openssl.org/).</span></span>
1. <span data-ttu-id="8bfbf-353">Installare il certificato nell'archivio certificati personale dell'utente corrente.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-353">Install the certificate into the current user's personal certificate store.</span></span> <span data-ttu-id="8bfbf-354">Contrassegnare la chiave come esportabile è facoltativa.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-354">Marking the key as exportable is optional.</span></span> <span data-ttu-id="8bfbf-355">Prendere nota dell'identificazione personale del certificato, che verrà usato più avanti in questo processo.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-355">Note the certificate's thumbprint, which is used later in this process.</span></span>
1. <span data-ttu-id="8bfbf-356">Esportare il certificato di archiviazione PKCS # 12 (*. pfx*) come certificato con codifica der (*. cer*).</span><span class="sxs-lookup"><span data-stu-id="8bfbf-356">Export the PKCS#12 archive (*.pfx*) certificate as a DER-encoded certificate (*.cer*).</span></span>
1. <span data-ttu-id="8bfbf-357">Registrare l'app con Azure AD (**registrazioni app**).</span><span class="sxs-lookup"><span data-stu-id="8bfbf-357">Register the app with Azure AD (**App registrations**).</span></span>
1. <span data-ttu-id="8bfbf-358">Caricare il certificato con codifica DER (*. cer*) in Azure ad:</span><span class="sxs-lookup"><span data-stu-id="8bfbf-358">Upload the DER-encoded certificate (*.cer*) to Azure AD:</span></span>
   1. <span data-ttu-id="8bfbf-359">Selezionare l'app in Azure AD.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-359">Select the app in Azure AD.</span></span>
   1. <span data-ttu-id="8bfbf-360">Passare a **certificati & segreti**.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-360">Navigate to **Certificates & secrets**.</span></span>
   1. <span data-ttu-id="8bfbf-361">Selezionare **Carica certificato** per caricare il certificato, che contiene la chiave pubblica.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-361">Select **Upload certificate** to upload the certificate, which contains the public key.</span></span> <span data-ttu-id="8bfbf-362">Un certificato con *estensione cer*, *PEM* o *CRT* è accettabile.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-362">A *.cer*, *.pem*, or *.crt* certificate is acceptable.</span></span>
1. <span data-ttu-id="8bfbf-363">Archiviare il nome dell'insieme di credenziali delle chiavi, l'ID applicazione e l'identificazione personale del certificato nel file dell'app *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="8bfbf-363">Store the key vault name, Application ID, and certificate thumbprint in the app's *appsettings.json* file.</span></span>
1. <span data-ttu-id="8bfbf-364">Passare a insiemi di credenziali delle **chiavi** nella portale di Azure.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-364">Navigate to **Key vaults** in the Azure portal.</span></span>
1. <span data-ttu-id="8bfbf-365">Selezionare l'insieme di credenziali delle chiavi creato nell' [Archivio Secret nell'ambiente di produzione con Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) sezione.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-365">Select the key vault that you created in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section.</span></span>
1. <span data-ttu-id="8bfbf-366">Selezionare **Criteri di accesso**.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-366">Select **Access policies**.</span></span>
1. <span data-ttu-id="8bfbf-367">Selezionare **Aggiungi criterio di accesso**.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-367">Select **Add Access Policy**.</span></span>
1. <span data-ttu-id="8bfbf-368">Aprire **autorizzazioni segrete** e fornire all'app le autorizzazioni **Get** ed **List** .</span><span class="sxs-lookup"><span data-stu-id="8bfbf-368">Open **Secret permissions** and provide the app with **Get** and **List** permissions.</span></span>
1. <span data-ttu-id="8bfbf-369">Selezionare **Seleziona entità** e selezionare l'app registrata in base al nome.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-369">Select **Select principal** and select the registered app by name.</span></span> <span data-ttu-id="8bfbf-370">Fare clic sul pulsante **Seleziona**.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-370">Select the **Select** button.</span></span>
1. <span data-ttu-id="8bfbf-371">Selezionare **OK**.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-371">Select **OK**.</span></span>
1. <span data-ttu-id="8bfbf-372">Selezionare **Salva**.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-372">Select **Save**.</span></span>
1. <span data-ttu-id="8bfbf-373">Distribuire l'app.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-373">Deploy the app.</span></span>

<span data-ttu-id="8bfbf-374">L' `Certificate` app di esempio ottiene i valori di configurazione da `IConfigurationRoot` con lo stesso nome del nome del segreto:</span><span class="sxs-lookup"><span data-stu-id="8bfbf-374">The `Certificate` sample app obtains its configuration values from `IConfigurationRoot` with the same name as the secret name:</span></span>

* <span data-ttu-id="8bfbf-375">Valori non gerarchici: il valore per `SecretName` viene ottenuto con `config["SecretName"]` .</span><span class="sxs-lookup"><span data-stu-id="8bfbf-375">Non-hierarchical values: The value for `SecretName` is obtained with `config["SecretName"]`.</span></span>
* <span data-ttu-id="8bfbf-376">Valori gerarchici (sezioni): `:` notazione use (due punti) o `GetSection` metodo di estensione.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-376">Hierarchical values (sections): Use `:` (colon) notation or the `GetSection` extension method.</span></span> <span data-ttu-id="8bfbf-377">Per ottenere il valore di configurazione, usare uno di questi approcci:</span><span class="sxs-lookup"><span data-stu-id="8bfbf-377">Use either of these approaches to obtain the configuration value:</span></span>
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

<span data-ttu-id="8bfbf-378">Il certificato X. 509 è gestito dal sistema operativo.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-378">The X.509 certificate is managed by the OS.</span></span> <span data-ttu-id="8bfbf-379">L'app chiama <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> con i valori forniti dal *appsettings.json* file:</span><span class="sxs-lookup"><span data-stu-id="8bfbf-379">The app calls <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> with values supplied by the *appsettings.json* file:</span></span>

[!code-csharp[](key-vault-configuration/samples/2.x/SampleApp/Program.cs?name=snippet1&highlight=20-23)]

<span data-ttu-id="8bfbf-380">Valori di esempio</span><span class="sxs-lookup"><span data-stu-id="8bfbf-380">Example values:</span></span>

* <span data-ttu-id="8bfbf-381">Nome dell'insieme di credenziali delle chiavi: `contosovault`</span><span class="sxs-lookup"><span data-stu-id="8bfbf-381">Key vault name: `contosovault`</span></span>
* <span data-ttu-id="8bfbf-382">ID applicazione: `627e911e-43cc-61d4-992e-12db9c81b413`</span><span class="sxs-lookup"><span data-stu-id="8bfbf-382">Application ID: `627e911e-43cc-61d4-992e-12db9c81b413`</span></span>
* <span data-ttu-id="8bfbf-383">Identificazione personale del certificato: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span><span class="sxs-lookup"><span data-stu-id="8bfbf-383">Certificate thumbprint: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span></span>

<span data-ttu-id="8bfbf-384">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="8bfbf-384">*appsettings.json*:</span></span>

[!code-json[](key-vault-configuration/samples/2.x/SampleApp/appsettings.json?highlight=10-12)]

<span data-ttu-id="8bfbf-385">Quando si esegue l'app, in una pagina Web vengono visualizzati i valori dei segreti caricati.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-385">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="8bfbf-386">Nell'ambiente di sviluppo, i valori Secret vengono caricati con il `_dev` suffisso.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-386">In the Development environment, secret values load with the `_dev` suffix.</span></span> <span data-ttu-id="8bfbf-387">Nell'ambiente di produzione, i valori vengono caricati con il `_prod` suffisso.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-387">In the Production environment, the values load with the `_prod` suffix.</span></span>

## <a name="use-managed-identities-for-azure-resources"></a><span data-ttu-id="8bfbf-388">Usare identità gestite per le risorse di Azure</span><span class="sxs-lookup"><span data-stu-id="8bfbf-388">Use Managed identities for Azure resources</span></span>

<span data-ttu-id="8bfbf-389">**Un'app distribuita in Azure** può trarre vantaggio dalle [identità gestite per le risorse di Azure](/azure/active-directory/managed-identities-azure-resources/overview), che consente all'app di eseguire l'autenticazione con Azure Key Vault usando Azure ad autenticazione senza credenziali (ID applicazione e password/segreto client) archiviate nell'app.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-389">**An app deployed to Azure** can take advantage of [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview), which allows the app to authenticate with Azure Key Vault using Azure AD authentication without credentials (Application ID and Password/Client Secret) stored in the app.</span></span>

<span data-ttu-id="8bfbf-390">L'app di esempio usa le identità gestite per le risorse di Azure quando l' `#define` istruzione all'inizio del file *Program.cs* è impostata su `Managed` .</span><span class="sxs-lookup"><span data-stu-id="8bfbf-390">The sample app uses Managed identities for Azure resources when the `#define` statement at the top of the *Program.cs* file is set to `Managed`.</span></span>

<span data-ttu-id="8bfbf-391">Immettere il nome dell'insieme di credenziali nel *appsettings.json* file dell'app.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-391">Enter the vault name into the app's *appsettings.json* file.</span></span> <span data-ttu-id="8bfbf-392">L'app di esempio non richiede un ID applicazione e una password (segreto client) quando è impostata sulla `Managed` versione, in modo che sia possibile ignorare tali voci di configurazione.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-392">The sample app doesn't require an Application ID and Password (Client Secret) when set to the `Managed` version, so you can ignore those configuration entries.</span></span> <span data-ttu-id="8bfbf-393">L'app viene distribuita in Azure e Azure autentica l'app per accedere Azure Key Vault solo usando il nome dell'insieme di credenziali archiviato nel *appsettings.json* file.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-393">The app is deployed to Azure, and Azure authenticates the app to access Azure Key Vault only using the vault name stored in the *appsettings.json* file.</span></span>

<span data-ttu-id="8bfbf-394">Distribuire l'app di esempio nel servizio app Azure.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-394">Deploy the sample app to Azure App Service.</span></span>

<span data-ttu-id="8bfbf-395">Un'app distribuita nel servizio app Azure viene registrata automaticamente con Azure AD quando viene creato il servizio.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-395">An app deployed to Azure App Service is automatically registered with Azure AD when the service is created.</span></span> <span data-ttu-id="8bfbf-396">Ottenere l'ID oggetto dalla distribuzione per utilizzarlo nel comando seguente.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-396">Obtain the Object ID from the deployment for use in the following command.</span></span> <span data-ttu-id="8bfbf-397">L'ID oggetto viene visualizzato nella portale di Azure nel **Identity** Pannello del servizio app.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-397">The Object ID is shown in the Azure portal on the **Identity** panel of the App Service.</span></span>

<span data-ttu-id="8bfbf-398">Usando l'interfaccia della riga di comando di Azure e l'ID oggetto dell'app, fornire all'app le `list` `get` autorizzazioni e per accedere all'insieme di credenziali delle chiavi:</span><span class="sxs-lookup"><span data-stu-id="8bfbf-398">Using Azure CLI and the app's Object ID, provide the app with `list` and `get` permissions to access the key vault:</span></span>

```azurecli
az keyvault set-policy --name {KEY VAULT NAME} --object-id {OBJECT ID} --secret-permissions get list
```

<span data-ttu-id="8bfbf-399">**Riavviare l'app** usando l'interfaccia della riga di comando di Azure, PowerShell o la portale di Azure.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-399">**Restart the app** using Azure CLI, PowerShell, or the Azure portal.</span></span>

<span data-ttu-id="8bfbf-400">App di esempio:</span><span class="sxs-lookup"><span data-stu-id="8bfbf-400">The sample app:</span></span>

* <span data-ttu-id="8bfbf-401">Crea un'istanza della `AzureServiceTokenProvider` classe senza una stringa di connessione.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-401">Creates an instance of the `AzureServiceTokenProvider` class without a connection string.</span></span> <span data-ttu-id="8bfbf-402">Quando non viene specificata una stringa di connessione, il provider tenta di ottenere un token di accesso dalle identità gestite per le risorse di Azure.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-402">When a connection string isn't provided, the provider attempts to obtain an access token from Managed identities for Azure resources.</span></span>
* <span data-ttu-id="8bfbf-403">Viene creato un nuovo oggetto <xref:Microsoft.Azure.KeyVault.KeyVaultClient> con il `AzureServiceTokenProvider` callback del token dell'istanza.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-403">A new <xref:Microsoft.Azure.KeyVault.KeyVaultClient> is created with the `AzureServiceTokenProvider` instance token callback.</span></span>
* <span data-ttu-id="8bfbf-404">L' <xref:Microsoft.Azure.KeyVault.KeyVaultClient> istanza viene utilizzata con un'implementazione predefinita di <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> che carica tutti i valori del segreto e sostituisce i doppi trattini ( `--` ) con i due punti ( `:` ) nei nomi delle chiavi.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-404">The <xref:Microsoft.Azure.KeyVault.KeyVaultClient> instance is used with a default implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> that loads all secret values and replaces double-dashes (`--`) with colons (`:`) in key names.</span></span>

[!code-csharp[](key-vault-configuration/samples/2.x/SampleApp/Program.cs?name=snippet2&highlight=13-21)]

<span data-ttu-id="8bfbf-405">Valore di esempio del nome dell'insieme di credenziali delle chiavi: `contosovault`</span><span class="sxs-lookup"><span data-stu-id="8bfbf-405">Key vault name example value: `contosovault`</span></span>
    
<span data-ttu-id="8bfbf-406">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="8bfbf-406">*appsettings.json*:</span></span>

```json
{
  "KeyVaultName": "Key Vault Name"
}
```

<span data-ttu-id="8bfbf-407">Quando si esegue l'app, in una pagina Web vengono visualizzati i valori dei segreti caricati.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-407">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="8bfbf-408">Nell'ambiente di sviluppo i valori Secret hanno il `_dev` suffisso perché sono forniti da segreti utente.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-408">In the Development environment, secret values have the `_dev` suffix because they're provided by User Secrets.</span></span> <span data-ttu-id="8bfbf-409">Nell'ambiente di produzione, i valori vengono caricati con il `_prod` suffisso perché sono forniti da Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-409">In the Production environment, the values load with the `_prod` suffix because they're provided by Azure Key Vault.</span></span>

<span data-ttu-id="8bfbf-410">Se viene visualizzato un `Access denied` errore, verificare che l'app sia registrata con Azure ad e che l'accesso sia stato fornito all'insieme di credenziali delle chiavi.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-410">If you receive an `Access denied` error, confirm that the app is registered with Azure AD and provided access to the key vault.</span></span> <span data-ttu-id="8bfbf-411">Confermare di aver riavviato il servizio in Azure.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-411">Confirm that you've restarted the service in Azure.</span></span>

<span data-ttu-id="8bfbf-412">Per informazioni sull'uso del provider con un'identità gestita e una pipeline di Azure DevOps, vedere [creare una connessione del servizio Azure Resource Manager a una macchina virtuale con un'identità del servizio gestito](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span><span class="sxs-lookup"><span data-stu-id="8bfbf-412">For information on using the provider with a managed identity and an Azure DevOps pipeline, see [Create an Azure Resource Manager service connection to a VM with a managed service identity](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span></span>

## <a name="use-a-key-name-prefix"></a><span data-ttu-id="8bfbf-413">Usa prefisso nome chiave</span><span class="sxs-lookup"><span data-stu-id="8bfbf-413">Use a key name prefix</span></span>

<span data-ttu-id="8bfbf-414"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> fornisce un overload che accetta un'implementazione di <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> , che consente di controllare la modalità di conversione dei segreti dell'insieme di credenziali delle chiavi in chiavi di configurazione.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-414"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> provides an overload that accepts an implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>, which allows you to control how key vault secrets are converted into configuration keys.</span></span> <span data-ttu-id="8bfbf-415">Ad esempio, è possibile implementare l'interfaccia per caricare i valori dei segreti in base a un valore di prefisso fornito all'avvio dell'app.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-415">For example, you can implement the interface to load secret values based on a prefix value you provide at app startup.</span></span> <span data-ttu-id="8bfbf-416">Questo consente, ad esempio, di caricare i segreti in base alla versione dell'app.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-416">This allows you, for example, to load secrets based on the version of the app.</span></span>

> [!WARNING]
> <span data-ttu-id="8bfbf-417">Non usare i prefissi sui segreti dell'insieme di credenziali delle chiavi per collocare i segreti per più app nello stesso insieme di credenziali delle chiavi o per collocare i segreti ambientali (ad esempio, *sviluppo* rispetto ai segreti di *produzione* ) nello stesso insieme di credenziali.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-417">Don't use prefixes on key vault secrets to place secrets for multiple apps into the same key vault or to place environmental secrets (for example, *development* versus *production* secrets) into the same vault.</span></span> <span data-ttu-id="8bfbf-418">È consigliabile che app e ambienti di sviluppo/produzione diversi usino insiemi di credenziali delle chiavi distinti per isolare gli ambienti app per il massimo livello di sicurezza.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-418">We recommend that different apps and development/production environments use separate key vaults to isolate app environments for the highest level of security.</span></span>

<span data-ttu-id="8bfbf-419">Nell'esempio seguente viene stabilito un segreto nell'insieme di credenziali delle chiavi (e usando lo strumento di gestione dei segreti per l'ambiente di sviluppo) per `5000-AppSecret` (i periodi non sono consentiti nei nomi dei segreti dell'insieme di credenziali delle chiavi).</span><span class="sxs-lookup"><span data-stu-id="8bfbf-419">In the following example, a secret is established in the key vault (and using the Secret Manager tool for the Development environment) for `5000-AppSecret` (periods aren't allowed in key vault secret names).</span></span> <span data-ttu-id="8bfbf-420">Questo segreto rappresenta un segreto app per la versione 5.0.0.0 dell'app.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-420">This secret represents an app secret for version 5.0.0.0 of the app.</span></span> <span data-ttu-id="8bfbf-421">Per un'altra versione dell'app, 5.1.0.0, viene aggiunto un segreto all'insieme di credenziali delle chiavi (e usando lo strumento di gestione dei segreti) per `5100-AppSecret` .</span><span class="sxs-lookup"><span data-stu-id="8bfbf-421">For another version of the app, 5.1.0.0, a secret is added to the key vault (and using the Secret Manager tool) for `5100-AppSecret`.</span></span> <span data-ttu-id="8bfbf-422">Ogni versione dell'app carica il valore del segreto con versione nella configurazione come `AppSecret` , rimuovendo la versione durante il caricamento del segreto.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-422">Each app version loads its versioned secret value into its configuration as `AppSecret`, stripping off the version as it loads the secret.</span></span>

<span data-ttu-id="8bfbf-423"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> viene chiamato con un oggetto personalizzato <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> :</span><span class="sxs-lookup"><span data-stu-id="8bfbf-423"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> is called with a custom <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>:</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Program.cs)]

<span data-ttu-id="8bfbf-424">L' <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> implementazione reagisce ai prefissi di versione dei segreti per caricare il segreto appropriato nella configurazione:</span><span class="sxs-lookup"><span data-stu-id="8bfbf-424">The <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> implementation reacts to the version prefixes of secrets to load the proper secret into configuration:</span></span>

* <span data-ttu-id="8bfbf-425">`Load` carica un segreto quando il nome inizia con il prefisso.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-425">`Load` loads a secret when its name starts with the prefix.</span></span> <span data-ttu-id="8bfbf-426">Non vengono caricati altri segreti.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-426">Other secrets aren't loaded.</span></span>
* <span data-ttu-id="8bfbf-427">`GetKey`:</span><span class="sxs-lookup"><span data-stu-id="8bfbf-427">`GetKey`:</span></span>
  * <span data-ttu-id="8bfbf-428">Rimuove il prefisso dal nome del segreto.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-428">Removes the prefix from the secret name.</span></span>
  * <span data-ttu-id="8bfbf-429">Sostituisce due trattini in qualsiasi nome con `KeyDelimiter` , che è il delimitatore usato nella configurazione (in genere i due punti).</span><span class="sxs-lookup"><span data-stu-id="8bfbf-429">Replaces two dashes in any name with the `KeyDelimiter`, which is the delimiter used in configuration (usually a colon).</span></span> <span data-ttu-id="8bfbf-430">Azure Key Vault non consente i due punti nei nomi dei segreti.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-430">Azure Key Vault doesn't allow a colon in secret names.</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Startup.cs)]

<span data-ttu-id="8bfbf-431">Il `Load` metodo viene chiamato da un algoritmo del provider che scorre i segreti dell'insieme di credenziali per trovare quelli con il prefisso della versione.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-431">The `Load` method is called by a provider algorithm that iterates through the vault secrets to find the ones that have the version prefix.</span></span> <span data-ttu-id="8bfbf-432">Quando viene trovato un prefisso di versione con `Load` , l'algoritmo usa il `GetKey` metodo per restituire il nome della configurazione del nome del segreto.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-432">When a version prefix is found with `Load`, the algorithm uses the `GetKey` method to return the configuration name of the secret name.</span></span> <span data-ttu-id="8bfbf-433">Rimuove il prefisso della versione dal nome del segreto e restituisce il resto del nome del segreto per il caricamento nelle coppie nome-valore della configurazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-433">It strips off the version prefix from the secret's name and returns the rest of the secret name for loading into the app's configuration name-value pairs.</span></span>

<span data-ttu-id="8bfbf-434">Quando viene implementato questo approccio:</span><span class="sxs-lookup"><span data-stu-id="8bfbf-434">When this approach is implemented:</span></span>

1. <span data-ttu-id="8bfbf-435">La versione dell'app specificata nel file di progetto dell'app.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-435">The app's version specified in the app's project file.</span></span> <span data-ttu-id="8bfbf-436">Nell'esempio seguente la versione dell'app è impostata su `5.0.0.0` :</span><span class="sxs-lookup"><span data-stu-id="8bfbf-436">In the following example, the app's version is set to `5.0.0.0`:</span></span>

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. <span data-ttu-id="8bfbf-437">Verificare che `<UserSecretsId>` sia presente una proprietà nel file di progetto dell'app, dove `{GUID}` è un GUID fornito dall'utente:</span><span class="sxs-lookup"><span data-stu-id="8bfbf-437">Confirm that a `<UserSecretsId>` property is present in the app's project file, where `{GUID}` is a user-supplied GUID:</span></span>

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   <span data-ttu-id="8bfbf-438">Salvare i segreti seguenti localmente con lo [strumento di gestione dei segreti](xref:security/app-secrets):</span><span class="sxs-lookup"><span data-stu-id="8bfbf-438">Save the following secrets locally with the [Secret Manager tool](xref:security/app-secrets):</span></span>

   ```dotnetcli
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. <span data-ttu-id="8bfbf-439">I segreti vengono salvati in Azure Key Vault usando i comandi dell'interfaccia della riga di comando di Azure seguenti:</span><span class="sxs-lookup"><span data-stu-id="8bfbf-439">Secrets are saved in Azure Key Vault using the following Azure CLI commands:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. <span data-ttu-id="8bfbf-440">Quando l'app viene eseguita, vengono caricati i segreti dell'insieme di credenziali delle chiavi.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-440">When the app is run, the key vault secrets are loaded.</span></span> <span data-ttu-id="8bfbf-441">La stringa segreta per `5000-AppSecret` viene confrontata con la versione dell'app specificata nel file di progetto dell'app ( `5.0.0.0` ).</span><span class="sxs-lookup"><span data-stu-id="8bfbf-441">The string secret for `5000-AppSecret` is matched to the app's version specified in the app's project file (`5.0.0.0`).</span></span>

1. <span data-ttu-id="8bfbf-442">La versione `5000` (con il trattino) viene rimossa dal nome della chiave.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-442">The version, `5000` (with the dash), is stripped from the key name.</span></span> <span data-ttu-id="8bfbf-443">In tutta l'app, la lettura della configurazione con la chiave `AppSecret` carica il valore del segreto.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-443">Throughout the app, reading configuration with the key `AppSecret` loads the secret value.</span></span>

1. <span data-ttu-id="8bfbf-444">Se la versione dell'app viene modificata nel file di progetto in `5.1.0.0` e l'app viene nuovamente eseguita, il valore del segreto restituito è `5.1.0.0_secret_value_dev` nell'ambiente di sviluppo e `5.1.0.0_secret_value_prod` in produzione.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-444">If the app's version is changed in the project file to `5.1.0.0` and the app is run again, the secret value returned is `5.1.0.0_secret_value_dev` in the Development environment and `5.1.0.0_secret_value_prod` in Production.</span></span>

> [!NOTE]
> <span data-ttu-id="8bfbf-445">È anche possibile fornire un' <xref:Microsoft.Azure.KeyVault.KeyVaultClient> implementazione personalizzata a <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> .</span><span class="sxs-lookup"><span data-stu-id="8bfbf-445">You can also provide your own <xref:Microsoft.Azure.KeyVault.KeyVaultClient> implementation to <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>.</span></span> <span data-ttu-id="8bfbf-446">Un client personalizzato consente la condivisione di una singola istanza del client nell'app.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-446">A custom client permits sharing a single instance of the client across the app.</span></span>

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="8bfbf-447">Associare una matrice a una classe</span><span class="sxs-lookup"><span data-stu-id="8bfbf-447">Bind an array to a class</span></span>

<span data-ttu-id="8bfbf-448">Il provider è in grado di leggere i valori di configurazione in una matrice per l'associazione a una matrice POCO.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-448">The provider is capable of reading configuration values into an array for binding to a POCO array.</span></span>

<span data-ttu-id="8bfbf-449">Quando si legge da un'origine di configurazione che consente alle chiavi di contenere separatori di due punti ( `:` ), viene usato un segmento di chiave numerico per distinguere le chiavi che costituiscono una matrice ( `:0:` , `:1:` , &hellip; `:{n}:` ).</span><span class="sxs-lookup"><span data-stu-id="8bfbf-449">When reading from a configuration source that allows keys to contain colon (`:`) separators, a numeric key segment is used to distinguish the keys that make up an array (`:0:`, `:1:`, &hellip; `:{n}:`).</span></span> <span data-ttu-id="8bfbf-450">Per altre informazioni, vedere [Configuration: associare una matrice a una classe](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span><span class="sxs-lookup"><span data-stu-id="8bfbf-450">For more information, see [Configuration: Bind an array to a class](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span></span>

<span data-ttu-id="8bfbf-451">Azure Key Vault chiavi non possono utilizzare i due punti come separatore.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-451">Azure Key Vault keys can't use a colon as a separator.</span></span> <span data-ttu-id="8bfbf-452">L'approccio descritto in questo argomento USA trattini doppi ( `--` ) come separatore per i valori gerarchici (sezioni).</span><span class="sxs-lookup"><span data-stu-id="8bfbf-452">The approach described in this topic uses double dashes (`--`) as a separator for hierarchical values (sections).</span></span> <span data-ttu-id="8bfbf-453">Le chiavi di matrice vengono archiviate in Azure Key Vault con trattini doppi e segmenti di chiave numerica ( `--0--` , `--1--` , &hellip; `--{n}--` ).</span><span class="sxs-lookup"><span data-stu-id="8bfbf-453">Array keys are stored in Azure Key Vault with double dashes and numeric key segments (`--0--`, `--1--`, &hellip; `--{n}--`).</span></span>

<span data-ttu-id="8bfbf-454">Esaminare la seguente configurazione del provider di registrazione [Serilog](https://serilog.net/) fornita da un file JSON.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-454">Examine the following [Serilog](https://serilog.net/) logging provider configuration provided by a JSON file.</span></span> <span data-ttu-id="8bfbf-455">Nella matrice sono definiti due valori letterali di oggetto `WriteTo` che riflettono due *sink* Serilog, che descrivono le destinazioni per la registrazione dell'output:</span><span class="sxs-lookup"><span data-stu-id="8bfbf-455">There are two object literals defined in the `WriteTo` array that reflect two Serilog *sinks*, which describe destinations for logging output:</span></span>

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

<span data-ttu-id="8bfbf-456">La configurazione mostrata nel file JSON precedente viene archiviata in Azure Key Vault usando la notazione a doppio trattino ( `--` ) e i segmenti numerici:</span><span class="sxs-lookup"><span data-stu-id="8bfbf-456">The configuration shown in the preceding JSON file is stored in Azure Key Vault using double dash (`--`) notation and numeric segments:</span></span>

| <span data-ttu-id="8bfbf-457">Chiave</span><span class="sxs-lookup"><span data-stu-id="8bfbf-457">Key</span></span> | <span data-ttu-id="8bfbf-458">valore</span><span class="sxs-lookup"><span data-stu-id="8bfbf-458">Value</span></span> |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a><span data-ttu-id="8bfbf-459">Ricarica segreti</span><span class="sxs-lookup"><span data-stu-id="8bfbf-459">Reload secrets</span></span>

<span data-ttu-id="8bfbf-460">I segreti vengono memorizzati nella cache finché non `IConfigurationRoot.Reload()` viene chiamato.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-460">Secrets are cached until `IConfigurationRoot.Reload()` is called.</span></span> <span data-ttu-id="8bfbf-461">I segreti scaduti, disabilitati e aggiornati nell'insieme di credenziali delle chiavi non vengono rispettati dall'app fino a quando non `Reload` viene eseguito.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-461">Expired, disabled, and updated secrets in the key vault are not respected by the app until `Reload` is executed.</span></span>

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a><span data-ttu-id="8bfbf-462">Segreti disabilitati e scaduti</span><span class="sxs-lookup"><span data-stu-id="8bfbf-462">Disabled and expired secrets</span></span>

<span data-ttu-id="8bfbf-463">I segreti disabilitati e scaduti generano un' <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException> .</span><span class="sxs-lookup"><span data-stu-id="8bfbf-463">Disabled and expired secrets throw a <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException>.</span></span> <span data-ttu-id="8bfbf-464">Per impedire che l'app venga generata, fornire la configurazione usando un provider di configurazione diverso o aggiornare il segreto disabilitato o scaduto.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-464">To prevent the app from throwing, provide the configuration using a different configuration provider or update the disabled or expired secret.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="8bfbf-465">Risolvere problemi</span><span class="sxs-lookup"><span data-stu-id="8bfbf-465">Troubleshoot</span></span>

<span data-ttu-id="8bfbf-466">Quando l'app non riesce a caricare la configurazione usando il provider, viene scritto un messaggio di errore nell' [infrastruttura di registrazione ASP.NET Core](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="8bfbf-466">When the app fails to load configuration using the provider, an error message is written to the [ASP.NET Core Logging infrastructure](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="8bfbf-467">Le condizioni seguenti impediranno il caricamento della configurazione:</span><span class="sxs-lookup"><span data-stu-id="8bfbf-467">The following conditions will prevent configuration from loading:</span></span>

* <span data-ttu-id="8bfbf-468">L'app o il certificato non è configurato correttamente in Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-468">The app or certificate isn't configured correctly in Azure Active Directory.</span></span>
* <span data-ttu-id="8bfbf-469">L'insieme di credenziali delle chiavi non esiste in Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-469">The key vault doesn't exist in Azure Key Vault.</span></span>
* <span data-ttu-id="8bfbf-470">L'app non è autorizzata ad accedere all'insieme di credenziali delle chiavi.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-470">The app isn't authorized to access the key vault.</span></span>
* <span data-ttu-id="8bfbf-471">Il criterio di accesso non include le `Get` `List` autorizzazioni e.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-471">The access policy doesn't include `Get` and `List` permissions.</span></span>
* <span data-ttu-id="8bfbf-472">Nell'insieme di credenziali delle chiavi i dati di configurazione (coppia nome-valore) sono denominati, mancanti, disabilitati o scaduti in modo errato.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-472">In the key vault, the configuration data (name-value pair) is incorrectly named, missing, disabled, or expired.</span></span>
* <span data-ttu-id="8bfbf-473">L'app presenta il nome dell'insieme di credenziali delle chiavi errato ( `KeyVaultName` ), Azure ad ID applicazione ( `AzureADApplicationId` ) o l'identificazione personale del certificato Azure ad ( `AzureADCertThumbprint` ).</span><span class="sxs-lookup"><span data-stu-id="8bfbf-473">The app has the wrong key vault name (`KeyVaultName`), Azure AD Application Id (`AzureADApplicationId`), or Azure AD certificate thumbprint (`AzureADCertThumbprint`).</span></span>
* <span data-ttu-id="8bfbf-474">La chiave di configurazione (nome) non è corretta nell'app per il valore che si sta provando a caricare.</span><span class="sxs-lookup"><span data-stu-id="8bfbf-474">The configuration key (name) is incorrect in the app for the value you're trying to load.</span></span>
* <span data-ttu-id="8bfbf-475">Quando si aggiungono i criteri di accesso per l'app all'insieme di credenziali delle chiavi, il criterio è stato creato, ma il pulsante **Salva** non è stato selezionato nell'interfaccia utente dei **criteri di accesso** .</span><span class="sxs-lookup"><span data-stu-id="8bfbf-475">When adding the access policy for the app to the key vault, the policy was created, but the **Save** button wasn't selected in the **Access policies** UI.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8bfbf-476">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="8bfbf-476">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
* [<span data-ttu-id="8bfbf-477">Microsoft Azure: Key Vault</span><span class="sxs-lookup"><span data-stu-id="8bfbf-477">Microsoft Azure: Key Vault</span></span>](https://azure.microsoft.com/services/key-vault/)
* [<span data-ttu-id="8bfbf-478">Microsoft Azure: Key Vault documentazione</span><span class="sxs-lookup"><span data-stu-id="8bfbf-478">Microsoft Azure: Key Vault Documentation</span></span>](/azure/key-vault/)
* [<span data-ttu-id="8bfbf-479">Come generare e trasferire chiavi HSM protette per Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="8bfbf-479">How to generate and transfer HSM-protected keys for Azure Key Vault</span></span>](/azure/key-vault/key-vault-hsm-protected-keys)
* [<span data-ttu-id="8bfbf-480">Classe KeyVaultClient</span><span class="sxs-lookup"><span data-stu-id="8bfbf-480">KeyVaultClient Class</span></span>](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [<span data-ttu-id="8bfbf-481">Guida introduttiva: Impostare e recuperare un segreto da Azure Key Vault tramite un'app Web .NET</span><span class="sxs-lookup"><span data-stu-id="8bfbf-481">Quickstart: Set and retrieve a secret from Azure Key Vault by using a .NET web app</span></span>](/azure/key-vault/quick-create-net)
* [<span data-ttu-id="8bfbf-482">Esercitazione: Come usare Azure Key Vault con una macchina virtuale Windows di Azure in .NET</span><span class="sxs-lookup"><span data-stu-id="8bfbf-482">Tutorial: How to use Azure Key Vault with Azure Windows Virtual Machine in .NET</span></span>](/azure/key-vault/tutorial-net-windows-virtual-machine)

::: moniker-end
