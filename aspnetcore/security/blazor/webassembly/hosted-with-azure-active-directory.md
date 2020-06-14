---
title: Proteggere un' Blazor app ospitata ASP.NET Core webassembly con Azure Active Directory
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/blazor/webassembly/hosted-with-azure-active-directory
ms.openlocfilehash: 5c73b5e5416956e2f6996e5692100e8c02a25cbf
ms.sourcegitcommit: d243fadeda20ad4f142ea60301ae5f5e0d41ed60
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/12/2020
ms.locfileid: "84724341"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-hosted-app-with-azure-active-directory"></a><span data-ttu-id="b8018-102">Proteggere un' Blazor app ospitata ASP.NET Core webassembly con Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="b8018-102">Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory</span></span>

<span data-ttu-id="b8018-103">Di [Javier Calvarro Nelson](https://github.com/javiercn) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="b8018-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="b8018-104">Questo articolo descrive come creare un' [ Blazor app ospitata da webassembly](xref:blazor/hosting-models#blazor-webassembly) che usa [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) per l'autenticazione.</span><span class="sxs-lookup"><span data-stu-id="b8018-104">This article describes how to create a [Blazor WebAssembly hosted app](xref:blazor/hosting-models#blazor-webassembly) that uses [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) for authentication.</span></span>

## <a name="register-apps-in-aad-and-create-solution"></a><span data-ttu-id="b8018-105">Registrare le app in AAD e creare la soluzione</span><span class="sxs-lookup"><span data-stu-id="b8018-105">Register apps in AAD and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="b8018-106">Creare un tenant</span><span class="sxs-lookup"><span data-stu-id="b8018-106">Create a tenant</span></span>

<span data-ttu-id="b8018-107">Seguire le istruzioni riportate nella [Guida introduttiva: configurare un tenant](/azure/active-directory/develop/quickstart-create-new-tenant) per creare un tenant in AAD.</span><span class="sxs-lookup"><span data-stu-id="b8018-107">Follow the guidance in [Quickstart: Set up a tenant](/azure/active-directory/develop/quickstart-create-new-tenant) to create a tenant in AAD.</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="b8018-108">Registrare un'app per le API server</span><span class="sxs-lookup"><span data-stu-id="b8018-108">Register a server API app</span></span>

<span data-ttu-id="b8018-109">Seguire le istruzioni disponibili in [Guida introduttiva: registrare un'applicazione con la piattaforma di identità Microsoft](/azure/active-directory/develop/quickstart-register-app) e gli argomenti di Azure AAD successivi per registrare un'app AAD per l'app per le *API del server* e quindi eseguire le operazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="b8018-109">Follow the guidance in [Quickstart: Register an application with the Microsoft identity platform](/azure/active-directory/develop/quickstart-register-app) and subsequent Azure AAD topics to register an AAD app for the *Server API app* and then do the following:</span></span>

1. <span data-ttu-id="b8018-110">In **Azure Active Directory**  >  **registrazioni app**selezionare **nuova registrazione**.</span><span class="sxs-lookup"><span data-stu-id="b8018-110">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="b8018-111">Specificare un **nome** per l'app (ad esempio, \*\* Blazor Server AAD\*\*).</span><span class="sxs-lookup"><span data-stu-id="b8018-111">Provide a **Name** for the app (for example, **Blazor Server AAD**).</span></span>
1. <span data-ttu-id="b8018-112">Scegliere i **tipi di conto supportati**.</span><span class="sxs-lookup"><span data-stu-id="b8018-112">Choose a **Supported account types**.</span></span> <span data-ttu-id="b8018-113">Per questa esperienza è possibile selezionare **solo gli account in questa directory aziendale** (tenant singolo).</span><span class="sxs-lookup"><span data-stu-id="b8018-113">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="b8018-114">L' *app* per le API del server non richiede un **URI di reindirizzamento** in questo scenario, quindi lasciare l'elenco a discesa impostato su **Web** e non immettere un URI di reindirizzamento.</span><span class="sxs-lookup"><span data-stu-id="b8018-114">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="b8018-115">Disabilitare la **Permissions**  >  casella di controllo autorizzazioni**Concedi amministratore per OpenID e autorizzazioni offline_access** .</span><span class="sxs-lookup"><span data-stu-id="b8018-115">Disable the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="b8018-116">Selezionare **Registra**.</span><span class="sxs-lookup"><span data-stu-id="b8018-116">Select **Register**.</span></span>

<span data-ttu-id="b8018-117">Registrare le seguenti informazioni:</span><span class="sxs-lookup"><span data-stu-id="b8018-117">Record the following information:</span></span>

* <span data-ttu-id="b8018-118">*App per le API server* ID applicazione (ID client) (ad esempio, `11111111-1111-1111-1111-111111111111` )</span><span class="sxs-lookup"><span data-stu-id="b8018-118">*Server API app* Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`)</span></span>
* <span data-ttu-id="b8018-119">ID directory (ID tenant) (ad esempio, `222222222-2222-2222-2222-222222222222` )</span><span class="sxs-lookup"><span data-stu-id="b8018-119">Directory ID (Tenant ID) (for example, `222222222-2222-2222-2222-222222222222`)</span></span>
* <span data-ttu-id="b8018-120">Dominio del tenant AAD (ad esempio, `contoso.onmicrosoft.com` ): il dominio è disponibile come **dominio del server di pubblicazione** nel pannello **personalizzazione** del portale di Azure per l'app registrata.</span><span class="sxs-lookup"><span data-stu-id="b8018-120">AAD Tenant domain (for example, `contoso.onmicrosoft.com`): The domain is available as the **Publisher domain** in the **Branding** blade of the Azure portal for the registered app.</span></span>

<span data-ttu-id="b8018-121">In **autorizzazioni API**rimuovere l'autorizzazione **Microsoft Graph**  >  **utente. Read** perché l'app non richiede l'accesso o il profilo utente.</span><span class="sxs-lookup"><span data-stu-id="b8018-121">In **API permissions**, remove the **Microsoft Graph** > **User.Read** permission, as the app doesn't require sign in or user profile access.</span></span>

<span data-ttu-id="b8018-122">In **esporre un'API**:</span><span class="sxs-lookup"><span data-stu-id="b8018-122">In **Expose an API**:</span></span>

1. <span data-ttu-id="b8018-123">Selezionare **Aggiungi un ambito**.</span><span class="sxs-lookup"><span data-stu-id="b8018-123">Select **Add a scope**.</span></span>
1. <span data-ttu-id="b8018-124">Fai clic su **Salva e continua**.</span><span class="sxs-lookup"><span data-stu-id="b8018-124">Select **Save and continue**.</span></span>
1. <span data-ttu-id="b8018-125">Specificare un **nome** per l'ambito (ad esempio, `API.Access` ).</span><span class="sxs-lookup"><span data-stu-id="b8018-125">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="b8018-126">Fornire un **nome visualizzato** per il consenso dell'amministratore, ad esempio `Access API` .</span><span class="sxs-lookup"><span data-stu-id="b8018-126">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="b8018-127">Fornire una **Descrizione del consenso dell'amministratore** , ad esempio `Allows the app to access server app API endpoints.` .</span><span class="sxs-lookup"><span data-stu-id="b8018-127">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="b8018-128">Verificare che lo **stato** sia impostato su **abilitato**.</span><span class="sxs-lookup"><span data-stu-id="b8018-128">Confirm that the **State** is set to **Enabled**.</span></span>
1. <span data-ttu-id="b8018-129">Selezionare **Aggiungi ambito**.</span><span class="sxs-lookup"><span data-stu-id="b8018-129">Select **Add scope**.</span></span>

<span data-ttu-id="b8018-130">Registrare le seguenti informazioni:</span><span class="sxs-lookup"><span data-stu-id="b8018-130">Record the following information:</span></span>

* <span data-ttu-id="b8018-131">URI ID app (ad esempio, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111` , `api://11111111-1111-1111-1111-111111111111` o il valore personalizzato specificato)</span><span class="sxs-lookup"><span data-stu-id="b8018-131">App ID URI (for example, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, `api://11111111-1111-1111-1111-111111111111`, or the custom value that you provided)</span></span>
* <span data-ttu-id="b8018-132">Ambito predefinito (ad esempio, `API.Access` )</span><span class="sxs-lookup"><span data-stu-id="b8018-132">Default scope (for example, `API.Access`)</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="b8018-133">Registrare un'app client</span><span class="sxs-lookup"><span data-stu-id="b8018-133">Register a client app</span></span>

<span data-ttu-id="b8018-134">Seguire le istruzioni disponibili in [Guida introduttiva: registrare un'applicazione con la piattaforma di identità Microsoft](/azure/active-directory/develop/quickstart-register-app) e gli argomenti di Azure AAD successivi per registrare un'app AAD per l' *app client* e quindi eseguire le operazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="b8018-134">Follow the guidance in [Quickstart: Register an application with the Microsoft identity platform](/azure/active-directory/develop/quickstart-register-app) and subsequent Azure AAD topics to register a AAD app for the *Client app* and then do the following:</span></span>

1. <span data-ttu-id="b8018-135">In **Azure Active Directory**  >  **registrazioni app**selezionare **nuova registrazione**.</span><span class="sxs-lookup"><span data-stu-id="b8018-135">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="b8018-136">Specificare un **nome** per l'app (ad esempio, \*\* Blazor client AAD\*\*).</span><span class="sxs-lookup"><span data-stu-id="b8018-136">Provide a **Name** for the app (for example, **Blazor Client AAD**).</span></span>
1. <span data-ttu-id="b8018-137">Scegliere i **tipi di conto supportati**.</span><span class="sxs-lookup"><span data-stu-id="b8018-137">Choose a **Supported account types**.</span></span> <span data-ttu-id="b8018-138">Per questa esperienza è possibile selezionare **solo gli account in questa directory aziendale** (tenant singolo).</span><span class="sxs-lookup"><span data-stu-id="b8018-138">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="b8018-139">Lasciare l'elenco a discesa **URI di reindirizzamento** impostato su **Web** e specificare l'URI di reindirizzamento seguente: `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="b8018-139">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="b8018-140">La porta predefinita per un'app in esecuzione su gheppio è 5001.</span><span class="sxs-lookup"><span data-stu-id="b8018-140">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="b8018-141">Se l'app viene eseguita su una porta di Gheppio diversa, usare la porta dell'app.</span><span class="sxs-lookup"><span data-stu-id="b8018-141">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="b8018-142">Per IIS Express, la porta generata in modo casuale per l'app si trova nelle proprietà dell'app Server nel pannello **debug** .</span><span class="sxs-lookup"><span data-stu-id="b8018-142">For IIS Express, the randomly generated port for the app can be found in the Server app's properties in the **Debug** panel.</span></span> <span data-ttu-id="b8018-143">Poiché l'app non esiste in questo momento e la porta IIS Express non è nota, tornare a questo passaggio dopo la creazione dell'app e aggiornare l'URI di reindirizzamento.</span><span class="sxs-lookup"><span data-stu-id="b8018-143">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="b8018-144">Un contrassegno viene visualizzato nella sezione [creare l'app](#create-the-app) per ricordare IIS Express agli utenti di aggiornare l'URI di reindirizzamento.</span><span class="sxs-lookup"><span data-stu-id="b8018-144">A remark appears in the [Create the app](#create-the-app) section to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="b8018-145">Disabilitare la **Permissions**  >  casella di controllo autorizzazioni**Concedi amministratore per OpenID e autorizzazioni offline_access** .</span><span class="sxs-lookup"><span data-stu-id="b8018-145">Disable the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="b8018-146">Selezionare **Registra**.</span><span class="sxs-lookup"><span data-stu-id="b8018-146">Select **Register**.</span></span>

<span data-ttu-id="b8018-147">Registrare l'ID applicazione dell' *app client* (ID client) (ad esempio, `33333333-3333-3333-3333-333333333333` ).</span><span class="sxs-lookup"><span data-stu-id="b8018-147">Record the *Client app* Application ID (Client ID) (for example, `33333333-3333-3333-3333-333333333333`).</span></span>

<span data-ttu-id="b8018-148">In **Authentication**  >  **configurazioni piattaforma**di autenticazione  >  **Web**:</span><span class="sxs-lookup"><span data-stu-id="b8018-148">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="b8018-149">Verificare che l' **URI di reindirizzamento** di `https://localhost:{PORT}/authentication/login-callback` sia presente.</span><span class="sxs-lookup"><span data-stu-id="b8018-149">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="b8018-150">Per **concessione implicita**, selezionare le caselle di controllo per i token di **accesso** e i **token ID**.</span><span class="sxs-lookup"><span data-stu-id="b8018-150">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="b8018-151">Per questa esperienza sono accettabili le impostazioni predefinite rimanenti per l'app.</span><span class="sxs-lookup"><span data-stu-id="b8018-151">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="b8018-152">Fare clic sul pulsante **Salva**.</span><span class="sxs-lookup"><span data-stu-id="b8018-152">Select the **Save** button.</span></span>

<span data-ttu-id="b8018-153">In **autorizzazioni API**:</span><span class="sxs-lookup"><span data-stu-id="b8018-153">In **API permissions**:</span></span>

1. <span data-ttu-id="b8018-154">Verificare che l'app disponga **Microsoft Graph**  >  autorizzazione**User. Read** .</span><span class="sxs-lookup"><span data-stu-id="b8018-154">Confirm that the app has **Microsoft Graph** > **User.Read** permission.</span></span>
1. <span data-ttu-id="b8018-155">Selezionare **Aggiungi un'autorizzazione** seguita da **API personali**.</span><span class="sxs-lookup"><span data-stu-id="b8018-155">Select **Add a permission** followed by **My APIs**.</span></span>
1. <span data-ttu-id="b8018-156">Selezionare l' *app per le API server* dalla colonna **nome** (ad esempio, \*\* Blazor Server AAD\*\*).</span><span class="sxs-lookup"><span data-stu-id="b8018-156">Select the *Server API app* from the **Name** column (for example, **Blazor Server AAD**).</span></span>
1. <span data-ttu-id="b8018-157">Aprire l'elenco di **API** .</span><span class="sxs-lookup"><span data-stu-id="b8018-157">Open the **API** list.</span></span>
1. <span data-ttu-id="b8018-158">Abilitare l'accesso all'API (ad esempio, `API.Access` ).</span><span class="sxs-lookup"><span data-stu-id="b8018-158">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="b8018-159">Selezionare **Aggiungi autorizzazioni**.</span><span class="sxs-lookup"><span data-stu-id="b8018-159">Select **Add permissions**.</span></span>
1. <span data-ttu-id="b8018-160">Selezionare il pulsante **Concedi contenuto amministratore per {tenant Name}** .</span><span class="sxs-lookup"><span data-stu-id="b8018-160">Select the **Grant admin content for {TENANT NAME}** button.</span></span> <span data-ttu-id="b8018-161">Selezionare **Sì** per confermare.</span><span class="sxs-lookup"><span data-stu-id="b8018-161">Select **Yes** to confirm.</span></span>

### <a name="create-the-app"></a><span data-ttu-id="b8018-162">Creare l'app</span><span class="sxs-lookup"><span data-stu-id="b8018-162">Create the app</span></span>

<span data-ttu-id="b8018-163">Sostituire i segnaposto nel comando seguente con le informazioni registrate in precedenza ed eseguire il comando in una shell dei comandi:</span><span class="sxs-lookup"><span data-stu-id="b8018-163">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{TENANT DOMAIN}" -ho --tenant-id "{TENANT ID}"
```

<span data-ttu-id="b8018-164">Per specificare il percorso di output, che crea una cartella di progetto, se non esiste, includere l'opzione di output nel comando con un percorso (ad esempio, `-o BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="b8018-164">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="b8018-165">Il nome della cartella diventa anche parte del nome del progetto.</span><span class="sxs-lookup"><span data-stu-id="b8018-165">The folder name also becomes part of the project's name.</span></span>

> [!NOTE]
> <span data-ttu-id="b8018-166">Passare l'URI ID app all' `app-id-uri` opzione, ma si noti che potrebbe essere necessaria una modifica alla configurazione nell'app client, descritta nella sezione [ambiti dei token di accesso](#access-token-scopes) .</span><span class="sxs-lookup"><span data-stu-id="b8018-166">Pass the App ID URI to the `app-id-uri` option, but note a configuration change might be required in the client app, which is described in the [Access token scopes](#access-token-scopes) section.</span></span>

> [!NOTE]
> <span data-ttu-id="b8018-167">Nel portale di Azure, l'URI di reindirizzamento Web delle configurazioni della piattaforma di autenticazione dell' *app client* **Authentication**  >  **Platform configurations**  >  **Web**  >  **Redirect URI** è configurato per la porta 5001 per le app eseguite nel server gheppio con le impostazioni predefinite.</span><span class="sxs-lookup"><span data-stu-id="b8018-167">In the Azure portal, the *Client app's* **Authentication** > **Platform configurations** > **Web** > **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="b8018-168">Se l' *app client* viene eseguita su una porta IIS Express casuale, la porta per l'app si trova nelle proprietà dell' *app Server* nel pannello **debug** .</span><span class="sxs-lookup"><span data-stu-id="b8018-168">If the *Client app* is run on a random IIS Express port, the port for the app can be found in the *Server app's* properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="b8018-169">Se la porta non è stata configurata in precedenza con la porta nota dell' *app client* , tornare alla registrazione dell' *app client* nell'portale di Azure e aggiornare l'URI di reindirizzamento con la porta corretta.</span><span class="sxs-lookup"><span data-stu-id="b8018-169">If the port wasn't configured earlier with the *Client app's* known port, return to the *Client app's* registration in the Azure portal and update the redirect URI with the correct port.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="b8018-170">Configurazione dell'app Server</span><span class="sxs-lookup"><span data-stu-id="b8018-170">Server app configuration</span></span>

<span data-ttu-id="b8018-171">*Questa sezione è relativa all'app **Server** della soluzione.*</span><span class="sxs-lookup"><span data-stu-id="b8018-171">*This section pertains to the solution's **Server** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="b8018-172">Pacchetto di autenticazione</span><span class="sxs-lookup"><span data-stu-id="b8018-172">Authentication package</span></span>

<span data-ttu-id="b8018-173">Il supporto per l'autenticazione e l'autorizzazione delle chiamate a ASP.NET Core API Web viene fornito dal pacchetto [Microsoft. AspNetCore. Authentication. AzureAD. UI](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI/) :</span><span class="sxs-lookup"><span data-stu-id="b8018-173">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the [Microsoft.AspNetCore.Authentication.AzureAD.UI](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI/) package:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureAD.UI" 
  Version="3.1.4" />
```

### <a name="authentication-service-support"></a><span data-ttu-id="b8018-174">Supporto del servizio di autenticazione</span><span class="sxs-lookup"><span data-stu-id="b8018-174">Authentication service support</span></span>

<span data-ttu-id="b8018-175">Il `AddAuthentication` metodo imposta i servizi di autenticazione all'interno dell'app e configura il gestore di JWT Bearer come metodo di autenticazione predefinito.</span><span class="sxs-lookup"><span data-stu-id="b8018-175">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="b8018-176">Il <xref:Microsoft.AspNetCore.Authentication.AzureADAuthenticationBuilderExtensions.AddAzureADBearer%2A> metodo imposta i parametri specifici nel gestore di JWT Bearer necessario per convalidare i token emessi dal Azure Active Directory:</span><span class="sxs-lookup"><span data-stu-id="b8018-176">The <xref:Microsoft.AspNetCore.Authentication.AzureADAuthenticationBuilderExtensions.AddAzureADBearer%2A> method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory:</span></span>

```csharp
services.AddAuthentication(AzureADDefaults.BearerAuthenticationScheme)
    .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
```

<span data-ttu-id="b8018-177"><xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A>e <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> verificare che:</span><span class="sxs-lookup"><span data-stu-id="b8018-177"><xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> and <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> ensure that:</span></span>

* <span data-ttu-id="b8018-178">L'app tenta di analizzare e convalidare i token nelle richieste in ingresso.</span><span class="sxs-lookup"><span data-stu-id="b8018-178">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="b8018-179">Eventuali richieste che tentano di accedere a una risorsa protetta senza credenziali appropriate hanno esito negativo.</span><span class="sxs-lookup"><span data-stu-id="b8018-179">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="useridentityname"></a><span data-ttu-id="b8018-180">Utente. Identity . Nome</span><span class="sxs-lookup"><span data-stu-id="b8018-180">User.Identity.Name</span></span>

<span data-ttu-id="b8018-181">Per impostazione predefinita, l'API dell'app Server viene popolata `User.Identity.Name` con il valore del `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` tipo di attestazione (ad esempio, `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com` ).</span><span class="sxs-lookup"><span data-stu-id="b8018-181">By default, the Server app API populates `User.Identity.Name` with the value from the `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` claim type (for example, `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com`).</span></span>

<span data-ttu-id="b8018-182">Per configurare l'app per la ricezione del valore dal `name` tipo di attestazione, configurare [TokenValidationParameters. NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) di <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="b8018-182">To configure the app to receive the value from the `name` claim type, configure the [TokenValidationParameters.NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;

...

services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a><span data-ttu-id="b8018-183">Impostazioni app</span><span class="sxs-lookup"><span data-stu-id="b8018-183">App settings</span></span>

<span data-ttu-id="b8018-184">Il *appsettings.jsnel* file contiene le opzioni per configurare il gestore di connessione JWT usato per convalidare i token di accesso:</span><span class="sxs-lookup"><span data-stu-id="b8018-184">The *appsettings.json* file contains the options to configure the JWT bearer handler used to validate access tokens:</span></span>

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "{DOMAIN}",
    "TenantId": "{TENANT ID}",
    "ClientId": "{SERVER API APP CLIENT ID}",
  }
}
```

<span data-ttu-id="b8018-185">Esempio:</span><span class="sxs-lookup"><span data-stu-id="b8018-185">Example:</span></span>

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "contoso.onmicrosoft.com",
    "TenantId": "e86c78e2-8bb4-4c41-aefd-918e0565a45e",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
  }
}
```

### <a name="weatherforecast-controller"></a><span data-ttu-id="b8018-186">Controller WeatherForecast</span><span class="sxs-lookup"><span data-stu-id="b8018-186">WeatherForecast controller</span></span>

<span data-ttu-id="b8018-187">Il controller WeatherForecast (*Controllers/WeatherForecastController. cs*) espone un'API protetta con l' [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attributo applicato al controller.</span><span class="sxs-lookup"><span data-stu-id="b8018-187">The WeatherForecast controller (*Controllers/WeatherForecastController.cs*) exposes a protected API with the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute applied to the controller.</span></span> <span data-ttu-id="b8018-188">È **importante** comprendere che:</span><span class="sxs-lookup"><span data-stu-id="b8018-188">It's **important** to understand that:</span></span>

* <span data-ttu-id="b8018-189">L' [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attributo in questo controller API è l'unico elemento che protegge questa API da accessi non autorizzati.</span><span class="sxs-lookup"><span data-stu-id="b8018-189">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="b8018-190">L' [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attributo usato nell' Blazor app webassembly funge solo da hint per l'app che l'utente deve essere autorizzato affinché l'app funzioni correttamente.</span><span class="sxs-lookup"><span data-stu-id="b8018-190">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute used in the Blazor WebAssembly app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

```csharp
[Authorize]
[ApiController]
[Route("[controller]")]
public class WeatherForecastController : ControllerBase
{
    [HttpGet]
    public IEnumerable<WeatherForecast> Get()
    {
        ...
    }
}
```

## <a name="client-app-configuration"></a><span data-ttu-id="b8018-191">Configurazione dell'app client</span><span class="sxs-lookup"><span data-stu-id="b8018-191">Client app configuration</span></span>

<span data-ttu-id="b8018-192">*Questa sezione riguarda l'app **client** della soluzione.*</span><span class="sxs-lookup"><span data-stu-id="b8018-192">*This section pertains to the solution's **Client** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="b8018-193">Pacchetto di autenticazione</span><span class="sxs-lookup"><span data-stu-id="b8018-193">Authentication package</span></span>

<span data-ttu-id="b8018-194">Quando viene creata un'app per usare gli account aziendali o dell'Istituto di istruzione ( `SingleOrg` ), l'app riceve automaticamente un riferimento al pacchetto per [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([Microsoft. Authentication. webassembly. MSAL](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)).</span><span class="sxs-lookup"><span data-stu-id="b8018-194">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([Microsoft.Authentication.WebAssembly.Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)).</span></span> <span data-ttu-id="b8018-195">Il pacchetto fornisce un set di primitive che consentono all'app di autenticare gli utenti e ottenere i token per chiamare le API protette.</span><span class="sxs-lookup"><span data-stu-id="b8018-195">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="b8018-196">Se si aggiunge l'autenticazione a un'app, aggiungere manualmente il pacchetto al file di progetto dell'app:</span><span class="sxs-lookup"><span data-stu-id="b8018-196">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="3.2.0" />
```

<span data-ttu-id="b8018-197">Il pacchetto [Microsoft. Authentication. webassembly. MSAL](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) aggiunge in modo transitivo il pacchetto [Microsoft. AspNetCore. Components. webassembly. Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) all'app.</span><span class="sxs-lookup"><span data-stu-id="b8018-197">The [Microsoft.Authentication.WebAssembly.Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) package transitively adds the [Microsoft.AspNetCore.Components.WebAssembly.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="b8018-198">Supporto del servizio di autenticazione</span><span class="sxs-lookup"><span data-stu-id="b8018-198">Authentication service support</span></span>

<span data-ttu-id="b8018-199"><xref:System.Net.Http.HttpClient>Viene aggiunto il supporto per le istanze di che includono token di accesso quando si effettuano richieste al progetto server.</span><span class="sxs-lookup"><span data-stu-id="b8018-199">Support for <xref:System.Net.Http.HttpClient> instances is added that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="b8018-200">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="b8018-200">*Program.cs*:</span></span>

```csharp
builder.Services.AddHttpClient("{APP ASSEMBLY}.ServerAPI", client => 
        client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddTransient(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("{APP ASSEMBLY}.ServerAPI"));
```

<span data-ttu-id="b8018-201">Il supporto per l'autenticazione degli utenti viene registrato nel contenitore del servizio con il <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> metodo di estensione fornito dal pacchetto [Microsoft. Authentication. webassembly. MSAL](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) .</span><span class="sxs-lookup"><span data-stu-id="b8018-201">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> extension method provided by the [Microsoft.Authentication.WebAssembly.Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) package.</span></span> <span data-ttu-id="b8018-202">Questo metodo configura i servizi necessari per l'interazione dell'app con il Identity provider (IP).</span><span class="sxs-lookup"><span data-stu-id="b8018-202">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="b8018-203">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="b8018-203">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="b8018-204">Il <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> metodo accetta un callback per configurare i parametri necessari per autenticare un'app.</span><span class="sxs-lookup"><span data-stu-id="b8018-204">The <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="b8018-205">Quando si registra l'app, è possibile ottenere i valori necessari per la configurazione dell'app dalla configurazione di AAD del portale di Azure.</span><span class="sxs-lookup"><span data-stu-id="b8018-205">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

<span data-ttu-id="b8018-206">La configurazione viene fornita dal file *wwwroot/appsettings.js* :</span><span class="sxs-lookup"><span data-stu-id="b8018-206">Configuration is supplied by the *wwwroot/appsettings.json* file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/{TENANT ID}",
    "ClientId": "{CLIENT APP CLIENT ID}",
    "ValidateAuthority": true
  }
}
```

<span data-ttu-id="b8018-207">Esempio:</span><span class="sxs-lookup"><span data-stu-id="b8018-207">Example:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/e86c78e2-...-918e0565a45e",
    "ClientId": "4369008b-21fa-427c-abaa-9b53bf58e538",
    "ValidateAuthority": true
  }
}
```

### <a name="access-token-scopes"></a><span data-ttu-id="b8018-208">Ambiti del token di accesso</span><span class="sxs-lookup"><span data-stu-id="b8018-208">Access token scopes</span></span>

<span data-ttu-id="b8018-209">Gli ambiti dei token di accesso predefiniti rappresentano l'elenco degli ambiti dei token di accesso:</span><span class="sxs-lookup"><span data-stu-id="b8018-209">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="b8018-210">Incluso per impostazione predefinita nella richiesta di accesso.</span><span class="sxs-lookup"><span data-stu-id="b8018-210">Included by default in the sign in request.</span></span>
* <span data-ttu-id="b8018-211">Utilizzato per eseguire il provisioning di un token di accesso immediatamente dopo l'autenticazione.</span><span class="sxs-lookup"><span data-stu-id="b8018-211">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="b8018-212">Tutti gli ambiti devono appartenere alla stessa app per ogni regola di Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="b8018-212">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="b8018-213">Per altre app per le API è possibile aggiungere altri ambiti, se necessario:</span><span class="sxs-lookup"><span data-stu-id="b8018-213">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

<span data-ttu-id="b8018-214">Per ulteriori informazioni, vedere le sezioni seguenti dell'articolo *scenari aggiuntivi* :</span><span class="sxs-lookup"><span data-stu-id="b8018-214">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="b8018-215">Richiedere token di accesso aggiuntivi</span><span class="sxs-lookup"><span data-stu-id="b8018-215">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="b8018-216">Connetti token alle richieste in uscita</span><span class="sxs-lookup"><span data-stu-id="b8018-216">Attach tokens to outgoing requests</span></span>](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)


### <a name="imports-file"></a><span data-ttu-id="b8018-217">Importa file</span><span class="sxs-lookup"><span data-stu-id="b8018-217">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="b8018-218">Pagina di indice</span><span class="sxs-lookup"><span data-stu-id="b8018-218">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a><span data-ttu-id="b8018-219">Componente app</span><span class="sxs-lookup"><span data-stu-id="b8018-219">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="b8018-220">Componente RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="b8018-220">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="b8018-221">Componente LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="b8018-221">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="b8018-222">Componente di autenticazione</span><span class="sxs-lookup"><span data-stu-id="b8018-222">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="b8018-223">Componente FetchData</span><span class="sxs-lookup"><span data-stu-id="b8018-223">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="b8018-224">Eseguire l'app</span><span class="sxs-lookup"><span data-stu-id="b8018-224">Run the app</span></span>

<span data-ttu-id="b8018-225">Eseguire l'app dal progetto server.</span><span class="sxs-lookup"><span data-stu-id="b8018-225">Run the app from the Server project.</span></span> <span data-ttu-id="b8018-226">Quando si usa Visual Studio, eseguire una delle operazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="b8018-226">When using Visual Studio, either:</span></span>

* <span data-ttu-id="b8018-227">Impostare l'elenco a discesa **progetti di avvio** nella barra degli strumenti sull'app per le *API server* e selezionare il pulsante **Esegui** .</span><span class="sxs-lookup"><span data-stu-id="b8018-227">Set the **Startup Projects** drop down list in the toolbar to the *Server API app* and select the **Run** button.</span></span>
* <span data-ttu-id="b8018-228">Selezionare il progetto server in **Esplora soluzioni** , quindi fare clic sul pulsante **Esegui** nella barra degli strumenti o avviare l'app dal menu **debug** .</span><span class="sxs-lookup"><span data-stu-id="b8018-228">Select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="b8018-229">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="b8018-229">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* [<span data-ttu-id="b8018-230">Richieste API Web non autenticate o non autorizzate in un'app con un client predefinito sicuro</span><span class="sxs-lookup"><span data-stu-id="b8018-230">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:security/blazor/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/blazor/webassembly/aad-groups-roles>
* <xref:security/authentication/azure-active-directory/index>
* [<span data-ttu-id="b8018-231">Documentazione di Microsoft Identity Platform</span><span class="sxs-lookup"><span data-stu-id="b8018-231">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
