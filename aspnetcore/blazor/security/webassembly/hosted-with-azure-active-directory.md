---
title: Proteggere un' Blazor WebAssembly app ospitata ASP.NET Core con Azure Active Directory
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/08/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/security/webassembly/hosted-with-azure-active-directory
ms.openlocfilehash: 82916c06413300bbefa85c619239c23a8e40468a
ms.sourcegitcommit: f7873c02c1505c99106cbc708f37e18fc0a496d1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86147760"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-hosted-app-with-azure-active-directory"></a><span data-ttu-id="80dc1-102">Proteggere un' Blazor WebAssembly app ospitata ASP.NET Core con Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="80dc1-102">Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory</span></span>

<span data-ttu-id="80dc1-103">Di [Javier Calvarro Nelson](https://github.com/javiercn) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="80dc1-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="80dc1-104">Questo articolo descrive come creare un' [ Blazor WebAssembly app ospitata](xref:blazor/hosting-models#blazor-webassembly) che usa [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) per l'autenticazione.</span><span class="sxs-lookup"><span data-stu-id="80dc1-104">This article describes how to create a [Blazor WebAssembly hosted app](xref:blazor/hosting-models#blazor-webassembly) that uses [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) for authentication.</span></span>

## <a name="register-apps-in-aad-and-create-solution"></a><span data-ttu-id="80dc1-105">Registrare le app in AAD e creare la soluzione</span><span class="sxs-lookup"><span data-stu-id="80dc1-105">Register apps in AAD and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="80dc1-106">Creare un tenant</span><span class="sxs-lookup"><span data-stu-id="80dc1-106">Create a tenant</span></span>

<span data-ttu-id="80dc1-107">Seguire le istruzioni riportate nella [Guida introduttiva: configurare un tenant](/azure/active-directory/develop/quickstart-create-new-tenant) per creare un tenant in AAD.</span><span class="sxs-lookup"><span data-stu-id="80dc1-107">Follow the guidance in [Quickstart: Set up a tenant](/azure/active-directory/develop/quickstart-create-new-tenant) to create a tenant in AAD.</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="80dc1-108">Registrare un'app per le API server</span><span class="sxs-lookup"><span data-stu-id="80dc1-108">Register a server API app</span></span>

<span data-ttu-id="80dc1-109">Seguire le istruzioni disponibili in [Guida introduttiva: registrare un'applicazione con la piattaforma di identità Microsoft](/azure/active-directory/develop/quickstart-register-app) e gli argomenti di Azure AAD successivi per registrare un'app AAD per l'app per le *API del server* e quindi eseguire le operazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="80dc1-109">Follow the guidance in [Quickstart: Register an application with the Microsoft identity platform](/azure/active-directory/develop/quickstart-register-app) and subsequent Azure AAD topics to register an AAD app for the *Server API app* and then do the following:</span></span>

1. <span data-ttu-id="80dc1-110">In **Azure Active Directory**  >  **registrazioni app**selezionare **nuova registrazione**.</span><span class="sxs-lookup"><span data-stu-id="80dc1-110">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="80dc1-111">Specificare un **nome** per l'app (ad esempio, \*\* Blazor Server AAD\*\*).</span><span class="sxs-lookup"><span data-stu-id="80dc1-111">Provide a **Name** for the app (for example, **Blazor Server AAD**).</span></span>
1. <span data-ttu-id="80dc1-112">Scegliere i **tipi di conto supportati**.</span><span class="sxs-lookup"><span data-stu-id="80dc1-112">Choose a **Supported account types**.</span></span> <span data-ttu-id="80dc1-113">Per questa esperienza è possibile selezionare **solo gli account in questa directory aziendale** (tenant singolo).</span><span class="sxs-lookup"><span data-stu-id="80dc1-113">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="80dc1-114">L' *app* per le API del server non richiede un **URI di reindirizzamento** in questo scenario, quindi lasciare l'elenco a discesa impostato su **Web** e non immettere un URI di reindirizzamento.</span><span class="sxs-lookup"><span data-stu-id="80dc1-114">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="80dc1-115">Disabilitare la **Permissions**  >  casella di controllo autorizzazioni**Concedi amministratore per OpenID e autorizzazioni offline_access** .</span><span class="sxs-lookup"><span data-stu-id="80dc1-115">Disable the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="80dc1-116">Selezionare **Registra**.</span><span class="sxs-lookup"><span data-stu-id="80dc1-116">Select **Register**.</span></span>

<span data-ttu-id="80dc1-117">Registrare le seguenti informazioni:</span><span class="sxs-lookup"><span data-stu-id="80dc1-117">Record the following information:</span></span>

* <span data-ttu-id="80dc1-118">*App per le API server* ID applicazione (client) (ad esempio, `41451fa7-82d9-4673-8fa5-69eff5a761fd` )</span><span class="sxs-lookup"><span data-stu-id="80dc1-118">*Server API app* Application (client) ID (for example, `41451fa7-82d9-4673-8fa5-69eff5a761fd`)</span></span>
* <span data-ttu-id="80dc1-119">ID directory (tenant) (ad esempio, `e86c78e2-8bb4-4c41-aefd-918e0565a45e` )</span><span class="sxs-lookup"><span data-stu-id="80dc1-119">Directory (tenant) ID (for example, `e86c78e2-8bb4-4c41-aefd-918e0565a45e`)</span></span>
* <span data-ttu-id="80dc1-120">Dominio primario/editore/tenant di AAD (ad esempio, `contoso.onmicrosoft.com` ): il dominio è disponibile come **dominio del server di pubblicazione** nel pannello **personalizzazione** del portale di Azure per l'app registrata.</span><span class="sxs-lookup"><span data-stu-id="80dc1-120">AAD Primary/Publisher/Tenant domain (for example, `contoso.onmicrosoft.com`): The domain is available as the **Publisher domain** in the **Branding** blade of the Azure portal for the registered app.</span></span>

<span data-ttu-id="80dc1-121">In **autorizzazioni API**rimuovere l'autorizzazione **Microsoft Graph**  >  **utente. Read** perché l'app non richiede l'accesso o il profilo utente.</span><span class="sxs-lookup"><span data-stu-id="80dc1-121">In **API permissions**, remove the **Microsoft Graph** > **User.Read** permission, as the app doesn't require sign in or user profile access.</span></span>

<span data-ttu-id="80dc1-122">In **esporre un'API**:</span><span class="sxs-lookup"><span data-stu-id="80dc1-122">In **Expose an API**:</span></span>

1. <span data-ttu-id="80dc1-123">Selezionare **Aggiungi un ambito**.</span><span class="sxs-lookup"><span data-stu-id="80dc1-123">Select **Add a scope**.</span></span>
1. <span data-ttu-id="80dc1-124">Fai clic su **Salva e continua**.</span><span class="sxs-lookup"><span data-stu-id="80dc1-124">Select **Save and continue**.</span></span>
1. <span data-ttu-id="80dc1-125">Specificare un **nome** per l'ambito (ad esempio, `API.Access` ).</span><span class="sxs-lookup"><span data-stu-id="80dc1-125">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="80dc1-126">Fornire un **nome visualizzato** per il consenso dell'amministratore, ad esempio `Access API` .</span><span class="sxs-lookup"><span data-stu-id="80dc1-126">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="80dc1-127">Fornire una **Descrizione del consenso dell'amministratore** , ad esempio `Allows the app to access server app API endpoints.` .</span><span class="sxs-lookup"><span data-stu-id="80dc1-127">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="80dc1-128">Verificare che lo **stato** sia impostato su **abilitato**.</span><span class="sxs-lookup"><span data-stu-id="80dc1-128">Confirm that the **State** is set to **Enabled**.</span></span>
1. <span data-ttu-id="80dc1-129">Selezionare **Aggiungi ambito**.</span><span class="sxs-lookup"><span data-stu-id="80dc1-129">Select **Add scope**.</span></span>

<span data-ttu-id="80dc1-130">Registrare le seguenti informazioni:</span><span class="sxs-lookup"><span data-stu-id="80dc1-130">Record the following information:</span></span>

* <span data-ttu-id="80dc1-131">URI ID app (ad esempio, `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd` , `api://41451fa7-82d9-4673-8fa5-69eff5a761fd` o il valore personalizzato specificato)</span><span class="sxs-lookup"><span data-stu-id="80dc1-131">App ID URI (for example, `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd`, `api://41451fa7-82d9-4673-8fa5-69eff5a761fd`, or the custom value that you provided)</span></span>
* <span data-ttu-id="80dc1-132">Ambito predefinito (ad esempio, `API.Access` )</span><span class="sxs-lookup"><span data-stu-id="80dc1-132">Default scope (for example, `API.Access`)</span></span>

<span data-ttu-id="80dc1-133">L'URI ID app potrebbe richiedere una configurazione speciale nell'app client, descritta nella sezione [ambiti dei token di accesso](#access-token-scopes) più avanti in questo argomento.</span><span class="sxs-lookup"><span data-stu-id="80dc1-133">The App ID URI might require a special configuration in the client app, which is described in the [Access token scopes](#access-token-scopes) section later in this topic.</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="80dc1-134">Registrare un'app client</span><span class="sxs-lookup"><span data-stu-id="80dc1-134">Register a client app</span></span>

<span data-ttu-id="80dc1-135">Seguire le istruzioni disponibili in [Guida introduttiva: registrare un'applicazione con la piattaforma di identità Microsoft](/azure/active-directory/develop/quickstart-register-app) e gli argomenti di Azure AAD successivi per registrare un'app AAD per l' *app client* e quindi eseguire le operazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="80dc1-135">Follow the guidance in [Quickstart: Register an application with the Microsoft identity platform](/azure/active-directory/develop/quickstart-register-app) and subsequent Azure AAD topics to register a AAD app for the *Client app* and then do the following:</span></span>

1. <span data-ttu-id="80dc1-136">In **Azure Active Directory**  >  **registrazioni app**selezionare **nuova registrazione**.</span><span class="sxs-lookup"><span data-stu-id="80dc1-136">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="80dc1-137">Specificare un **nome** per l'app (ad esempio, \*\* Blazor client AAD\*\*).</span><span class="sxs-lookup"><span data-stu-id="80dc1-137">Provide a **Name** for the app (for example, **Blazor Client AAD**).</span></span>
1. <span data-ttu-id="80dc1-138">Scegliere i **tipi di conto supportati**.</span><span class="sxs-lookup"><span data-stu-id="80dc1-138">Choose a **Supported account types**.</span></span> <span data-ttu-id="80dc1-139">Per questa esperienza è possibile selezionare **solo gli account in questa directory aziendale** (tenant singolo).</span><span class="sxs-lookup"><span data-stu-id="80dc1-139">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="80dc1-140">Lasciare l'elenco a discesa **URI di reindirizzamento** impostato su **Web** e specificare l'URI di reindirizzamento seguente: `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="80dc1-140">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="80dc1-141">La porta predefinita per un'app in esecuzione su gheppio è 5001.</span><span class="sxs-lookup"><span data-stu-id="80dc1-141">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="80dc1-142">Se l'app viene eseguita su una porta di Gheppio diversa, usare la porta dell'app.</span><span class="sxs-lookup"><span data-stu-id="80dc1-142">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="80dc1-143">Per IIS Express, la porta generata in modo casuale per l'app si trova nelle proprietà dell'app Server nel pannello **debug** .</span><span class="sxs-lookup"><span data-stu-id="80dc1-143">For IIS Express, the randomly generated port for the app can be found in the Server app's properties in the **Debug** panel.</span></span> <span data-ttu-id="80dc1-144">Poiché l'app non esiste in questo momento e la porta IIS Express non è nota, tornare a questo passaggio dopo la creazione dell'app e aggiornare l'URI di reindirizzamento.</span><span class="sxs-lookup"><span data-stu-id="80dc1-144">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="80dc1-145">Un contrassegno viene visualizzato nella sezione [creare l'app](#create-the-app) per ricordare IIS Express agli utenti di aggiornare l'URI di reindirizzamento.</span><span class="sxs-lookup"><span data-stu-id="80dc1-145">A remark appears in the [Create the app](#create-the-app) section to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="80dc1-146">Disabilitare la **Permissions**  >  casella di controllo autorizzazioni**Concedi amministratore per OpenID e autorizzazioni offline_access** .</span><span class="sxs-lookup"><span data-stu-id="80dc1-146">Disable the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="80dc1-147">Selezionare **Registra**.</span><span class="sxs-lookup"><span data-stu-id="80dc1-147">Select **Register**.</span></span>

<span data-ttu-id="80dc1-148">Registrare l'ID dell'applicazione *client* (client) (ad esempio, `4369008b-21fa-427c-abaa-9b53bf58e538` ).</span><span class="sxs-lookup"><span data-stu-id="80dc1-148">Record the *Client app* Application (client) ID (for example, `4369008b-21fa-427c-abaa-9b53bf58e538`).</span></span>

<span data-ttu-id="80dc1-149">In **Authentication**  >  **configurazioni piattaforma**di autenticazione  >  **Web**:</span><span class="sxs-lookup"><span data-stu-id="80dc1-149">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="80dc1-150">Verificare che l' **URI di reindirizzamento** di `https://localhost:{PORT}/authentication/login-callback` sia presente.</span><span class="sxs-lookup"><span data-stu-id="80dc1-150">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="80dc1-151">Per **concessione implicita**, selezionare le caselle di controllo per i token di **accesso** e i **token ID**.</span><span class="sxs-lookup"><span data-stu-id="80dc1-151">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="80dc1-152">Per questa esperienza sono accettabili le impostazioni predefinite rimanenti per l'app.</span><span class="sxs-lookup"><span data-stu-id="80dc1-152">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="80dc1-153">Fare clic sul pulsante **Salva**.</span><span class="sxs-lookup"><span data-stu-id="80dc1-153">Select the **Save** button.</span></span>

<span data-ttu-id="80dc1-154">In **autorizzazioni API**:</span><span class="sxs-lookup"><span data-stu-id="80dc1-154">In **API permissions**:</span></span>

1. <span data-ttu-id="80dc1-155">Verificare che l'app disponga **Microsoft Graph**  >  autorizzazione**User. Read** .</span><span class="sxs-lookup"><span data-stu-id="80dc1-155">Confirm that the app has **Microsoft Graph** > **User.Read** permission.</span></span>
1. <span data-ttu-id="80dc1-156">Selezionare **Aggiungi un'autorizzazione** seguita da **API personali**.</span><span class="sxs-lookup"><span data-stu-id="80dc1-156">Select **Add a permission** followed by **My APIs**.</span></span>
1. <span data-ttu-id="80dc1-157">Selezionare l' *app per le API server* dalla colonna **nome** (ad esempio, \*\* Blazor Server AAD\*\*).</span><span class="sxs-lookup"><span data-stu-id="80dc1-157">Select the *Server API app* from the **Name** column (for example, **Blazor Server AAD**).</span></span>
1. <span data-ttu-id="80dc1-158">Aprire l'elenco di **API** .</span><span class="sxs-lookup"><span data-stu-id="80dc1-158">Open the **API** list.</span></span>
1. <span data-ttu-id="80dc1-159">Abilitare l'accesso all'API (ad esempio, `API.Access` ).</span><span class="sxs-lookup"><span data-stu-id="80dc1-159">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="80dc1-160">Selezionare **Aggiungi autorizzazioni**.</span><span class="sxs-lookup"><span data-stu-id="80dc1-160">Select **Add permissions**.</span></span>
1. <span data-ttu-id="80dc1-161">Selezionare il pulsante **concedere il consenso dell'amministratore per {tenant Name}** .</span><span class="sxs-lookup"><span data-stu-id="80dc1-161">Select the **Grant admin consent for {TENANT NAME}** button.</span></span> <span data-ttu-id="80dc1-162">Selezionare **Sì** per confermare.</span><span class="sxs-lookup"><span data-stu-id="80dc1-162">Select **Yes** to confirm.</span></span>

### <a name="create-the-app"></a><span data-ttu-id="80dc1-163">Creare l'app</span><span class="sxs-lookup"><span data-stu-id="80dc1-163">Create the app</span></span>

<span data-ttu-id="80dc1-164">In una cartella vuota, sostituire i segnaposto nel comando seguente con le informazioni registrate in precedenza ed eseguire il comando in una shell dei comandi:</span><span class="sxs-lookup"><span data-stu-id="80dc1-164">In an empty folder, replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{TENANT DOMAIN}" -ho -o {APP NAME} --tenant-id "{TENANT ID}"
```

| <span data-ttu-id="80dc1-165">Segnaposto</span><span class="sxs-lookup"><span data-stu-id="80dc1-165">Placeholder</span></span>                  | <span data-ttu-id="80dc1-166">Nome portale di Azure</span><span class="sxs-lookup"><span data-stu-id="80dc1-166">Azure portal name</span></span>                                     | <span data-ttu-id="80dc1-167">Esempio</span><span class="sxs-lookup"><span data-stu-id="80dc1-167">Example</span></span>                                |
| ---------------------------- | ----------------------------------------------------- | -------------------------------------- |
| `{APP NAME}`                 | &mdash;                                               | `BlazorSample`                         |
| `{CLIENT APP CLIENT ID}`     | <span data-ttu-id="80dc1-168">ID applicazione (client) per l' *app client*</span><span class="sxs-lookup"><span data-stu-id="80dc1-168">Application (client) ID for the *Client app*</span></span>          | `4369008b-21fa-427c-abaa-9b53bf58e538` |
| `{DEFAULT SCOPE}`            | <span data-ttu-id="80dc1-169">Nome ambito</span><span class="sxs-lookup"><span data-stu-id="80dc1-169">Scope name</span></span>                                            | `API.Access`                           |
| `{SERVER API APP CLIENT ID}` | <span data-ttu-id="80dc1-170">ID applicazione (client) per l' *app per le API server*</span><span class="sxs-lookup"><span data-stu-id="80dc1-170">Application (client) ID for the *Server API app*</span></span>      | `41451fa7-82d9-4673-8fa5-69eff5a761fd` |
| `{SERVER API APP ID URI}`    | <span data-ttu-id="80dc1-171">URI ID applicazione ([vedere la nota](#access-token-scopes))</span><span class="sxs-lookup"><span data-stu-id="80dc1-171">Application ID URI ([see note](#access-token-scopes))</span></span> | `41451fa7-82d9-4673-8fa5-69eff5a761fd` |
| `{TENANT DOMAIN}`            | <span data-ttu-id="80dc1-172">Dominio primario/server di pubblicazione/tenant</span><span class="sxs-lookup"><span data-stu-id="80dc1-172">Primary/Publisher/Tenant domain</span></span>                       | `contoso.onmicrosoft.com`              |
| `{TENANT ID}`                | <span data-ttu-id="80dc1-173">ID directory (tenant)</span><span class="sxs-lookup"><span data-stu-id="80dc1-173">Directory (tenant) ID</span></span>                                 | `e86c78e2-8bb4-4c41-aefd-918e0565a45e` |

<span data-ttu-id="80dc1-174">Il percorso di output specificato con l' `-o|--output` opzione Crea una cartella di progetto se non esiste e diventa parte del nome dell'app.</span><span class="sxs-lookup"><span data-stu-id="80dc1-174">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

> [!NOTE]
> <span data-ttu-id="80dc1-175">Passare l'URI ID app all' `app-id-uri` opzione, ma si noti che potrebbe essere necessaria una modifica alla configurazione nell'app client, descritta nella sezione [ambiti dei token di accesso](#access-token-scopes) .</span><span class="sxs-lookup"><span data-stu-id="80dc1-175">Pass the App ID URI to the `app-id-uri` option, but note a configuration change might be required in the client app, which is described in the [Access token scopes](#access-token-scopes) section.</span></span>

> [!NOTE]
> <span data-ttu-id="80dc1-176">Nel portale di Azure, l'URI di reindirizzamento Web delle configurazioni della piattaforma di autenticazione dell' *app client* **Authentication**  >  **Platform configurations**  >  **Web**  >  **Redirect URI** è configurato per la porta 5001 per le app eseguite nel server gheppio con le impostazioni predefinite.</span><span class="sxs-lookup"><span data-stu-id="80dc1-176">In the Azure portal, the *Client app's* **Authentication** > **Platform configurations** > **Web** > **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="80dc1-177">Se l' *app client* viene eseguita su una porta IIS Express casuale, la porta per l'app si trova nelle proprietà dell' *app* per le API del server nel pannello **debug** .</span><span class="sxs-lookup"><span data-stu-id="80dc1-177">If the *Client app* is run on a random IIS Express port, the port for the app can be found in the *Server API app's* properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="80dc1-178">Se la porta non è stata configurata in precedenza con la porta nota dell' *app client* , tornare alla registrazione dell' *app client* nell'portale di Azure e aggiornare l'URI di reindirizzamento con la porta corretta.</span><span class="sxs-lookup"><span data-stu-id="80dc1-178">If the port wasn't configured earlier with the *Client app's* known port, return to the *Client app's* registration in the Azure portal and update the redirect URI with the correct port.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="80dc1-179">Configurazione dell'app Server</span><span class="sxs-lookup"><span data-stu-id="80dc1-179">Server app configuration</span></span>

<span data-ttu-id="80dc1-180">*Questa sezione è relativa all'app della soluzione **`Server`** .*</span><span class="sxs-lookup"><span data-stu-id="80dc1-180">*This section pertains to the solution's **`Server`** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="80dc1-181">Pacchetto di autenticazione</span><span class="sxs-lookup"><span data-stu-id="80dc1-181">Authentication package</span></span>

<span data-ttu-id="80dc1-182">Il pacchetto fornisce il supporto per l'autenticazione e l'autorizzazione delle chiamate a ASP.NET Core API Web [`Microsoft.AspNetCore.Authentication.AzureAD.UI`](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI/) :</span><span class="sxs-lookup"><span data-stu-id="80dc1-182">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the [`Microsoft.AspNetCore.Authentication.AzureAD.UI`](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI/) package:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureAD.UI" 
  Version="3.1.4" />
```

### <a name="authentication-service-support"></a><span data-ttu-id="80dc1-183">Supporto del servizio di autenticazione</span><span class="sxs-lookup"><span data-stu-id="80dc1-183">Authentication service support</span></span>

<span data-ttu-id="80dc1-184">Il `AddAuthentication` metodo imposta i servizi di autenticazione all'interno dell'app e configura il gestore di JWT Bearer come metodo di autenticazione predefinito.</span><span class="sxs-lookup"><span data-stu-id="80dc1-184">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="80dc1-185">Il <xref:Microsoft.AspNetCore.Authentication.AzureADAuthenticationBuilderExtensions.AddAzureADBearer%2A> metodo imposta i parametri specifici nel gestore di JWT Bearer necessario per convalidare i token emessi dal Azure Active Directory:</span><span class="sxs-lookup"><span data-stu-id="80dc1-185">The <xref:Microsoft.AspNetCore.Authentication.AzureADAuthenticationBuilderExtensions.AddAzureADBearer%2A> method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory:</span></span>

```csharp
services.AddAuthentication(AzureADDefaults.BearerAuthenticationScheme)
    .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
```

<span data-ttu-id="80dc1-186"><xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A>e <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> verificare che:</span><span class="sxs-lookup"><span data-stu-id="80dc1-186"><xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> and <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> ensure that:</span></span>

* <span data-ttu-id="80dc1-187">L'app tenta di analizzare e convalidare i token nelle richieste in ingresso.</span><span class="sxs-lookup"><span data-stu-id="80dc1-187">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="80dc1-188">Eventuali richieste che tentano di accedere a una risorsa protetta senza credenziali appropriate hanno esito negativo.</span><span class="sxs-lookup"><span data-stu-id="80dc1-188">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="useridentityname"></a><span data-ttu-id="80dc1-189">Utente. Identity . Nome</span><span class="sxs-lookup"><span data-stu-id="80dc1-189">User.Identity.Name</span></span>

<span data-ttu-id="80dc1-190">Per impostazione predefinita, l'API dell'app Server viene popolata `User.Identity.Name` con il valore del `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` tipo di attestazione (ad esempio, `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com` ).</span><span class="sxs-lookup"><span data-stu-id="80dc1-190">By default, the Server app API populates `User.Identity.Name` with the value from the `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` claim type (for example, `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com`).</span></span>

<span data-ttu-id="80dc1-191">Per configurare l'app per la ricezione del valore dal `name` tipo di attestazione, configurare il <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType> di <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="80dc1-191">To configure the app to receive the value from the `name` claim type, configure the <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType> of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;

...

services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a><span data-ttu-id="80dc1-192">Impostazioni app</span><span class="sxs-lookup"><span data-stu-id="80dc1-192">App settings</span></span>

<span data-ttu-id="80dc1-193">Il `appsettings.json` file contiene le opzioni per configurare il gestore di connessione JWT usato per convalidare i token di accesso:</span><span class="sxs-lookup"><span data-stu-id="80dc1-193">The `appsettings.json` file contains the options to configure the JWT bearer handler used to validate access tokens:</span></span>

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

<span data-ttu-id="80dc1-194">Esempio:</span><span class="sxs-lookup"><span data-stu-id="80dc1-194">Example:</span></span>

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

### <a name="weatherforecast-controller"></a><span data-ttu-id="80dc1-195">Controller WeatherForecast</span><span class="sxs-lookup"><span data-stu-id="80dc1-195">WeatherForecast controller</span></span>

<span data-ttu-id="80dc1-196">Il controller WeatherForecast (*Controllers/WeatherForecastController. cs*) espone un'API protetta con l' [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attributo applicato al controller.</span><span class="sxs-lookup"><span data-stu-id="80dc1-196">The WeatherForecast controller (*Controllers/WeatherForecastController.cs*) exposes a protected API with the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute applied to the controller.</span></span> <span data-ttu-id="80dc1-197">È **importante** comprendere che:</span><span class="sxs-lookup"><span data-stu-id="80dc1-197">It's **important** to understand that:</span></span>

* <span data-ttu-id="80dc1-198">L' [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attributo in questo controller API è l'unico elemento che protegge questa API da accessi non autorizzati.</span><span class="sxs-lookup"><span data-stu-id="80dc1-198">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="80dc1-199">L' [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attributo usato nell' Blazor WebAssembly app funge solo da hint per l'app che l'utente deve essere autorizzato affinché l'app funzioni correttamente.</span><span class="sxs-lookup"><span data-stu-id="80dc1-199">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute used in the Blazor WebAssembly app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

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

## <a name="client-app-configuration"></a><span data-ttu-id="80dc1-200">Configurazione dell'app client</span><span class="sxs-lookup"><span data-stu-id="80dc1-200">Client app configuration</span></span>

<span data-ttu-id="80dc1-201">*Questa sezione è relativa all'app della soluzione **`Client`** .*</span><span class="sxs-lookup"><span data-stu-id="80dc1-201">*This section pertains to the solution's **`Client`** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="80dc1-202">Pacchetto di autenticazione</span><span class="sxs-lookup"><span data-stu-id="80dc1-202">Authentication package</span></span>

<span data-ttu-id="80dc1-203">Quando viene creata un'app per usare gli account aziendali o dell'Istituto di istruzione ( `SingleOrg` ), l'app riceve automaticamente un riferimento al pacchetto per [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ( [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) ).</span><span class="sxs-lookup"><span data-stu-id="80dc1-203">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)).</span></span> <span data-ttu-id="80dc1-204">Il pacchetto fornisce un set di primitive che consentono all'app di autenticare gli utenti e ottenere i token per chiamare le API protette.</span><span class="sxs-lookup"><span data-stu-id="80dc1-204">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="80dc1-205">Se si aggiunge l'autenticazione a un'app, aggiungere manualmente il pacchetto al file di progetto dell'app:</span><span class="sxs-lookup"><span data-stu-id="80dc1-205">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="3.2.0" />
```

<span data-ttu-id="80dc1-206">Il [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) pacchetto aggiunge il [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) pacchetto all'app in modo transitivo.</span><span class="sxs-lookup"><span data-stu-id="80dc1-206">The [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) package transitively adds the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="80dc1-207">Supporto del servizio di autenticazione</span><span class="sxs-lookup"><span data-stu-id="80dc1-207">Authentication service support</span></span>

<span data-ttu-id="80dc1-208"><xref:System.Net.Http.HttpClient>Viene aggiunto il supporto per le istanze di che includono token di accesso quando si effettuano richieste al progetto server.</span><span class="sxs-lookup"><span data-stu-id="80dc1-208">Support for <xref:System.Net.Http.HttpClient> instances is added that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="80dc1-209">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="80dc1-209">`Program.cs`:</span></span>

```csharp
builder.Services.AddHttpClient("{APP ASSEMBLY}.ServerAPI", client => 
        client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddTransient(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("{APP ASSEMBLY}.ServerAPI"));
```

<span data-ttu-id="80dc1-210">Il segnaposto `{APP ASSEMBLY}` è il nome dell'assembly dell'app (ad esempio, `BlazorSample.ServerAPI` ).</span><span class="sxs-lookup"><span data-stu-id="80dc1-210">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `BlazorSample.ServerAPI`).</span></span>

<span data-ttu-id="80dc1-211">Il supporto per l'autenticazione degli utenti viene registrato nel contenitore del servizio con il <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> metodo di estensione fornito dal [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) pacchetto.</span><span class="sxs-lookup"><span data-stu-id="80dc1-211">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> extension method provided by the [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) package.</span></span> <span data-ttu-id="80dc1-212">Questo metodo configura i servizi necessari per l'interazione dell'app con il Identity provider (IP).</span><span class="sxs-lookup"><span data-stu-id="80dc1-212">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="80dc1-213">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="80dc1-213">`Program.cs`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="80dc1-214">Il <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> metodo accetta un callback per configurare i parametri necessari per autenticare un'app.</span><span class="sxs-lookup"><span data-stu-id="80dc1-214">The <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="80dc1-215">Quando si registra l'app, è possibile ottenere i valori necessari per la configurazione dell'app dalla configurazione di AAD del portale di Azure.</span><span class="sxs-lookup"><span data-stu-id="80dc1-215">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

<span data-ttu-id="80dc1-216">La configurazione viene fornita dal `wwwroot/appsettings.json` file:</span><span class="sxs-lookup"><span data-stu-id="80dc1-216">Configuration is supplied by the `wwwroot/appsettings.json` file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/{TENANT ID}",
    "ClientId": "{CLIENT APP CLIENT ID}",
    "ValidateAuthority": true
  }
}
```

<span data-ttu-id="80dc1-217">Esempio:</span><span class="sxs-lookup"><span data-stu-id="80dc1-217">Example:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/e86c78e2-...-918e0565a45e",
    "ClientId": "4369008b-21fa-427c-abaa-9b53bf58e538",
    "ValidateAuthority": true
  }
}
```

### <a name="access-token-scopes"></a><span data-ttu-id="80dc1-218">Ambiti del token di accesso</span><span class="sxs-lookup"><span data-stu-id="80dc1-218">Access token scopes</span></span>

<span data-ttu-id="80dc1-219">Gli ambiti dei token di accesso predefiniti rappresentano l'elenco degli ambiti dei token di accesso:</span><span class="sxs-lookup"><span data-stu-id="80dc1-219">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="80dc1-220">Incluso per impostazione predefinita nella richiesta di accesso.</span><span class="sxs-lookup"><span data-stu-id="80dc1-220">Included by default in the sign in request.</span></span>
* <span data-ttu-id="80dc1-221">Utilizzato per eseguire il provisioning di un token di accesso immediatamente dopo l'autenticazione.</span><span class="sxs-lookup"><span data-stu-id="80dc1-221">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="80dc1-222">Tutti gli ambiti devono appartenere alla stessa app per ogni regola di Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="80dc1-222">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="80dc1-223">Per altre app per le API è possibile aggiungere altri ambiti, se necessario:</span><span class="sxs-lookup"><span data-stu-id="80dc1-223">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

<span data-ttu-id="80dc1-224">Per ulteriori informazioni, vedere le sezioni seguenti dell'articolo *scenari aggiuntivi* :</span><span class="sxs-lookup"><span data-stu-id="80dc1-224">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="80dc1-225">Richiedere token di accesso aggiuntivi</span><span class="sxs-lookup"><span data-stu-id="80dc1-225">Request additional access tokens</span></span>](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="80dc1-226">Connetti token alle richieste in uscita</span><span class="sxs-lookup"><span data-stu-id="80dc1-226">Attach tokens to outgoing requests</span></span>](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)


### <a name="imports-file"></a><span data-ttu-id="80dc1-227">Importa file</span><span class="sxs-lookup"><span data-stu-id="80dc1-227">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="80dc1-228">Pagina di indice</span><span class="sxs-lookup"><span data-stu-id="80dc1-228">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a><span data-ttu-id="80dc1-229">Componente app</span><span class="sxs-lookup"><span data-stu-id="80dc1-229">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="80dc1-230">Componente RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="80dc1-230">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="80dc1-231">Componente LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="80dc1-231">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="80dc1-232">Componente di autenticazione</span><span class="sxs-lookup"><span data-stu-id="80dc1-232">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="80dc1-233">Componente FetchData</span><span class="sxs-lookup"><span data-stu-id="80dc1-233">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="80dc1-234">Eseguire l'app</span><span class="sxs-lookup"><span data-stu-id="80dc1-234">Run the app</span></span>

<span data-ttu-id="80dc1-235">Eseguire l'app dal progetto server.</span><span class="sxs-lookup"><span data-stu-id="80dc1-235">Run the app from the Server project.</span></span> <span data-ttu-id="80dc1-236">Quando si usa Visual Studio, eseguire una delle operazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="80dc1-236">When using Visual Studio, either:</span></span>

* <span data-ttu-id="80dc1-237">Impostare l'elenco a discesa **progetti di avvio** nella barra degli strumenti sull'app per le *API server* e selezionare il pulsante **Esegui** .</span><span class="sxs-lookup"><span data-stu-id="80dc1-237">Set the **Startup Projects** drop down list in the toolbar to the *Server API app* and select the **Run** button.</span></span>
* <span data-ttu-id="80dc1-238">Selezionare il progetto server in **Esplora soluzioni** , quindi fare clic sul pulsante **Esegui** nella barra degli strumenti o avviare l'app dal menu **debug** .</span><span class="sxs-lookup"><span data-stu-id="80dc1-238">Select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="80dc1-239">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="80dc1-239">Additional resources</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="80dc1-240">Richieste API Web non autenticate o non autorizzate in un'app con un client predefinito sicuro</span><span class="sxs-lookup"><span data-stu-id="80dc1-240">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:blazor/security/webassembly/aad-groups-roles>
* <xref:security/authentication/azure-active-directory/index>
* [<span data-ttu-id="80dc1-241">Documentazione di Microsoft Identity Platform</span><span class="sxs-lookup"><span data-stu-id="80dc1-241">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
