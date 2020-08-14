---
title: Proteggere un' Blazor WebAssembly app ospitata ASP.NET Core con Azure Active Directory
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: devx-track-csharp, mvc
ms.date: 07/08/2020
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
uid: blazor/security/webassembly/hosted-with-azure-active-directory
ms.openlocfilehash: 407dab96216149178abd5bdd21ef318154f414da
ms.sourcegitcommit: ec41ab354952b75557240923756a8c2ac79b49f8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/13/2020
ms.locfileid: "88202719"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-hosted-app-with-azure-active-directory"></a><span data-ttu-id="45d6a-102">Proteggere un' Blazor WebAssembly app ospitata ASP.NET Core con Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="45d6a-102">Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory</span></span>

<span data-ttu-id="45d6a-103">Di [Javier Calvarro Nelson](https://github.com/javiercn) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="45d6a-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="45d6a-104">Questo articolo descrive come creare un' [ Blazor WebAssembly app ospitata](xref:blazor/hosting-models#blazor-webassembly) che usa [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) per l'autenticazione.</span><span class="sxs-lookup"><span data-stu-id="45d6a-104">This article describes how to create a [hosted Blazor WebAssembly app](xref:blazor/hosting-models#blazor-webassembly) that uses [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) for authentication.</span></span>

## <a name="register-apps-in-aad-and-create-solution"></a><span data-ttu-id="45d6a-105">Registrare le app in AAD e creare la soluzione</span><span class="sxs-lookup"><span data-stu-id="45d6a-105">Register apps in AAD and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="45d6a-106">Creare un tenant</span><span class="sxs-lookup"><span data-stu-id="45d6a-106">Create a tenant</span></span>

<span data-ttu-id="45d6a-107">Seguire le istruzioni riportate nella [Guida introduttiva: configurare un tenant](/azure/active-directory/develop/quickstart-create-new-tenant) per creare un tenant in AAD.</span><span class="sxs-lookup"><span data-stu-id="45d6a-107">Follow the guidance in [Quickstart: Set up a tenant](/azure/active-directory/develop/quickstart-create-new-tenant) to create a tenant in AAD.</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="45d6a-108">Registrare un'app per le API server</span><span class="sxs-lookup"><span data-stu-id="45d6a-108">Register a server API app</span></span>

<span data-ttu-id="45d6a-109">Seguire le istruzioni disponibili in [Guida introduttiva: registrare un'applicazione con la piattaforma di identità Microsoft](/azure/active-directory/develop/quickstart-register-app) e gli argomenti di Azure AAD successivi per registrare un'app AAD per l'app per le *API del server* e quindi eseguire le operazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="45d6a-109">Follow the guidance in [Quickstart: Register an application with the Microsoft identity platform](/azure/active-directory/develop/quickstart-register-app) and subsequent Azure AAD topics to register an AAD app for the *Server API app* and then do the following:</span></span>

1. <span data-ttu-id="45d6a-110">In **Azure Active Directory**  >  **registrazioni app**selezionare **nuova registrazione**.</span><span class="sxs-lookup"><span data-stu-id="45d6a-110">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="45d6a-111">Specificare un **nome** per l'app (ad esempio, \*\* Blazor Server AAD\*\*).</span><span class="sxs-lookup"><span data-stu-id="45d6a-111">Provide a **Name** for the app (for example, **Blazor Server AAD**).</span></span>
1. <span data-ttu-id="45d6a-112">Scegliere i **tipi di conto supportati**.</span><span class="sxs-lookup"><span data-stu-id="45d6a-112">Choose a **Supported account types**.</span></span> <span data-ttu-id="45d6a-113">Per questa esperienza è possibile selezionare **solo gli account in questa directory aziendale** (tenant singolo).</span><span class="sxs-lookup"><span data-stu-id="45d6a-113">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="45d6a-114">L' *app* per le API del server non richiede un **URI di reindirizzamento** in questo scenario, quindi lasciare l'elenco a discesa impostato su **Web** e non immettere un URI di reindirizzamento.</span><span class="sxs-lookup"><span data-stu-id="45d6a-114">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="45d6a-115">Disabilitare la **Permissions**  >  casella di controllo autorizzazioni**Concedi amministratore per OpenID e autorizzazioni offline_access** .</span><span class="sxs-lookup"><span data-stu-id="45d6a-115">Disable the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="45d6a-116">Selezionare **Registra**.</span><span class="sxs-lookup"><span data-stu-id="45d6a-116">Select **Register**.</span></span>

<span data-ttu-id="45d6a-117">Registrare le seguenti informazioni:</span><span class="sxs-lookup"><span data-stu-id="45d6a-117">Record the following information:</span></span>

* <span data-ttu-id="45d6a-118">*App per le API server* ID applicazione (client) (ad esempio, `41451fa7-82d9-4673-8fa5-69eff5a761fd` )</span><span class="sxs-lookup"><span data-stu-id="45d6a-118">*Server API app* Application (client) ID (for example, `41451fa7-82d9-4673-8fa5-69eff5a761fd`)</span></span>
* <span data-ttu-id="45d6a-119">ID directory (tenant) (ad esempio, `e86c78e2-8bb4-4c41-aefd-918e0565a45e` )</span><span class="sxs-lookup"><span data-stu-id="45d6a-119">Directory (tenant) ID (for example, `e86c78e2-8bb4-4c41-aefd-918e0565a45e`)</span></span>
* <span data-ttu-id="45d6a-120">Dominio primario/editore/tenant di AAD (ad esempio, `contoso.onmicrosoft.com` ): il dominio è disponibile come **dominio del server di pubblicazione** nel pannello **personalizzazione** del portale di Azure per l'app registrata.</span><span class="sxs-lookup"><span data-stu-id="45d6a-120">AAD Primary/Publisher/Tenant domain (for example, `contoso.onmicrosoft.com`): The domain is available as the **Publisher domain** in the **Branding** blade of the Azure portal for the registered app.</span></span>

<span data-ttu-id="45d6a-121">In **autorizzazioni API**rimuovere l'autorizzazione **Microsoft Graph**  >  **utente. Read** perché l'app non richiede l'accesso o il profilo utente.</span><span class="sxs-lookup"><span data-stu-id="45d6a-121">In **API permissions**, remove the **Microsoft Graph** > **User.Read** permission, as the app doesn't require sign in or user profile access.</span></span>

<span data-ttu-id="45d6a-122">In **esporre un'API**:</span><span class="sxs-lookup"><span data-stu-id="45d6a-122">In **Expose an API**:</span></span>

1. <span data-ttu-id="45d6a-123">Selezionare **Aggiungi un ambito**.</span><span class="sxs-lookup"><span data-stu-id="45d6a-123">Select **Add a scope**.</span></span>
1. <span data-ttu-id="45d6a-124">Fai clic su **Salva e continua**.</span><span class="sxs-lookup"><span data-stu-id="45d6a-124">Select **Save and continue**.</span></span>
1. <span data-ttu-id="45d6a-125">Specificare un **nome** per l'ambito (ad esempio, `API.Access` ).</span><span class="sxs-lookup"><span data-stu-id="45d6a-125">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="45d6a-126">Fornire un **nome visualizzato** per il consenso dell'amministratore, ad esempio `Access API` .</span><span class="sxs-lookup"><span data-stu-id="45d6a-126">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="45d6a-127">Fornire una **Descrizione del consenso dell'amministratore** , ad esempio `Allows the app to access server app API endpoints.` .</span><span class="sxs-lookup"><span data-stu-id="45d6a-127">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="45d6a-128">Verificare che lo **stato** sia impostato su **abilitato**.</span><span class="sxs-lookup"><span data-stu-id="45d6a-128">Confirm that the **State** is set to **Enabled**.</span></span>
1. <span data-ttu-id="45d6a-129">Selezionare **Aggiungi ambito**.</span><span class="sxs-lookup"><span data-stu-id="45d6a-129">Select **Add scope**.</span></span>

<span data-ttu-id="45d6a-130">Registrare le seguenti informazioni:</span><span class="sxs-lookup"><span data-stu-id="45d6a-130">Record the following information:</span></span>

* <span data-ttu-id="45d6a-131">URI ID app (ad esempio, `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd` , `api://41451fa7-82d9-4673-8fa5-69eff5a761fd` o il valore personalizzato specificato)</span><span class="sxs-lookup"><span data-stu-id="45d6a-131">App ID URI (for example, `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd`, `api://41451fa7-82d9-4673-8fa5-69eff5a761fd`, or the custom value that you provided)</span></span>
* <span data-ttu-id="45d6a-132">Ambito predefinito (ad esempio, `API.Access` )</span><span class="sxs-lookup"><span data-stu-id="45d6a-132">Default scope (for example, `API.Access`)</span></span>

<span data-ttu-id="45d6a-133">L'URI ID app potrebbe richiedere una configurazione speciale nell'app client, descritta nella sezione [ambiti dei token di accesso](#access-token-scopes) più avanti in questo argomento.</span><span class="sxs-lookup"><span data-stu-id="45d6a-133">The App ID URI might require a special configuration in the client app, which is described in the [Access token scopes](#access-token-scopes) section later in this topic.</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="45d6a-134">Registrare un'app client</span><span class="sxs-lookup"><span data-stu-id="45d6a-134">Register a client app</span></span>

<span data-ttu-id="45d6a-135">Seguire le istruzioni disponibili in [Guida introduttiva: registrare un'applicazione con la piattaforma di identità Microsoft](/azure/active-directory/develop/quickstart-register-app) e gli argomenti di Azure AAD successivi per registrare un'app AAD per l' *app client* e quindi eseguire le operazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="45d6a-135">Follow the guidance in [Quickstart: Register an application with the Microsoft identity platform](/azure/active-directory/develop/quickstart-register-app) and subsequent Azure AAD topics to register a AAD app for the *Client app* and then do the following:</span></span>

1. <span data-ttu-id="45d6a-136">In **Azure Active Directory**  >  **registrazioni app**selezionare **nuova registrazione**.</span><span class="sxs-lookup"><span data-stu-id="45d6a-136">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="45d6a-137">Specificare un **nome** per l'app (ad esempio, \*\* Blazor client AAD\*\*).</span><span class="sxs-lookup"><span data-stu-id="45d6a-137">Provide a **Name** for the app (for example, **Blazor Client AAD**).</span></span>
1. <span data-ttu-id="45d6a-138">Scegliere i **tipi di conto supportati**.</span><span class="sxs-lookup"><span data-stu-id="45d6a-138">Choose a **Supported account types**.</span></span> <span data-ttu-id="45d6a-139">Per questa esperienza è possibile selezionare **solo gli account in questa directory aziendale** (tenant singolo).</span><span class="sxs-lookup"><span data-stu-id="45d6a-139">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="45d6a-140">Lasciare l'elenco a discesa **URI di reindirizzamento** impostato su **Web** e specificare l'URI di reindirizzamento seguente: `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="45d6a-140">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="45d6a-141">La porta predefinita per un'app in esecuzione su gheppio è 5001.</span><span class="sxs-lookup"><span data-stu-id="45d6a-141">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="45d6a-142">Se l'app viene eseguita su una porta di Gheppio diversa, usare la porta dell'app.</span><span class="sxs-lookup"><span data-stu-id="45d6a-142">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="45d6a-143">Per IIS Express, la porta generata in modo casuale per l'app si trova nelle proprietà dell'app Server nel pannello **debug** .</span><span class="sxs-lookup"><span data-stu-id="45d6a-143">For IIS Express, the randomly generated port for the app can be found in the Server app's properties in the **Debug** panel.</span></span> <span data-ttu-id="45d6a-144">Poiché l'app non esiste in questo momento e la porta IIS Express non è nota, tornare a questo passaggio dopo la creazione dell'app e aggiornare l'URI di reindirizzamento.</span><span class="sxs-lookup"><span data-stu-id="45d6a-144">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="45d6a-145">Un contrassegno viene visualizzato nella sezione [creare l'app](#create-the-app) per ricordare IIS Express agli utenti di aggiornare l'URI di reindirizzamento.</span><span class="sxs-lookup"><span data-stu-id="45d6a-145">A remark appears in the [Create the app](#create-the-app) section to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="45d6a-146">Disabilitare la **Permissions**  >  casella di controllo autorizzazioni**Concedi amministratore per OpenID e autorizzazioni offline_access** .</span><span class="sxs-lookup"><span data-stu-id="45d6a-146">Disable the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="45d6a-147">Selezionare **Registra**.</span><span class="sxs-lookup"><span data-stu-id="45d6a-147">Select **Register**.</span></span>

<span data-ttu-id="45d6a-148">Registrare l'ID dell'applicazione *client* (client) (ad esempio, `4369008b-21fa-427c-abaa-9b53bf58e538` ).</span><span class="sxs-lookup"><span data-stu-id="45d6a-148">Record the *Client app* Application (client) ID (for example, `4369008b-21fa-427c-abaa-9b53bf58e538`).</span></span>

<span data-ttu-id="45d6a-149">In **Authentication**  >  **configurazioni piattaforma**di autenticazione  >  **Web**:</span><span class="sxs-lookup"><span data-stu-id="45d6a-149">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="45d6a-150">Verificare che l' **URI di reindirizzamento** di `https://localhost:{PORT}/authentication/login-callback` sia presente.</span><span class="sxs-lookup"><span data-stu-id="45d6a-150">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="45d6a-151">Per **concessione implicita**, selezionare le caselle di controllo per i token di **accesso** e i **token ID**.</span><span class="sxs-lookup"><span data-stu-id="45d6a-151">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="45d6a-152">Per questa esperienza sono accettabili le impostazioni predefinite rimanenti per l'app.</span><span class="sxs-lookup"><span data-stu-id="45d6a-152">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="45d6a-153">Fare clic sul pulsante **Salva**.</span><span class="sxs-lookup"><span data-stu-id="45d6a-153">Select the **Save** button.</span></span>

<span data-ttu-id="45d6a-154">In **autorizzazioni API**:</span><span class="sxs-lookup"><span data-stu-id="45d6a-154">In **API permissions**:</span></span>

1. <span data-ttu-id="45d6a-155">Verificare che l'app disponga **Microsoft Graph**  >  autorizzazione**User. Read** .</span><span class="sxs-lookup"><span data-stu-id="45d6a-155">Confirm that the app has **Microsoft Graph** > **User.Read** permission.</span></span>
1. <span data-ttu-id="45d6a-156">Selezionare **Aggiungi un'autorizzazione** seguita da **API personali**.</span><span class="sxs-lookup"><span data-stu-id="45d6a-156">Select **Add a permission** followed by **My APIs**.</span></span>
1. <span data-ttu-id="45d6a-157">Selezionare l' *app per le API server* dalla colonna **nome** (ad esempio, \*\* Blazor Server AAD\*\*).</span><span class="sxs-lookup"><span data-stu-id="45d6a-157">Select the *Server API app* from the **Name** column (for example, **Blazor Server AAD**).</span></span>
1. <span data-ttu-id="45d6a-158">Aprire l'elenco di **API** .</span><span class="sxs-lookup"><span data-stu-id="45d6a-158">Open the **API** list.</span></span>
1. <span data-ttu-id="45d6a-159">Abilitare l'accesso all'API (ad esempio, `API.Access` ).</span><span class="sxs-lookup"><span data-stu-id="45d6a-159">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="45d6a-160">Selezionare **Aggiungi autorizzazioni**.</span><span class="sxs-lookup"><span data-stu-id="45d6a-160">Select **Add permissions**.</span></span>
1. <span data-ttu-id="45d6a-161">Selezionare il pulsante **concedere il consenso dell'amministratore per {tenant Name}** .</span><span class="sxs-lookup"><span data-stu-id="45d6a-161">Select the **Grant admin consent for {TENANT NAME}** button.</span></span> <span data-ttu-id="45d6a-162">Selezionare **Sì** per confermare.</span><span class="sxs-lookup"><span data-stu-id="45d6a-162">Select **Yes** to confirm.</span></span>

### <a name="create-the-app"></a><span data-ttu-id="45d6a-163">Creare l'app</span><span class="sxs-lookup"><span data-stu-id="45d6a-163">Create the app</span></span>

<span data-ttu-id="45d6a-164">In una cartella vuota, sostituire i segnaposto nel comando seguente con le informazioni registrate in precedenza ed eseguire il comando in una shell dei comandi:</span><span class="sxs-lookup"><span data-stu-id="45d6a-164">In an empty folder, replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{TENANT DOMAIN}" -ho -o {APP NAME} --tenant-id "{TENANT ID}"
```

| <span data-ttu-id="45d6a-165">Segnaposto</span><span class="sxs-lookup"><span data-stu-id="45d6a-165">Placeholder</span></span>                  | <span data-ttu-id="45d6a-166">Nome portale di Azure</span><span class="sxs-lookup"><span data-stu-id="45d6a-166">Azure portal name</span></span>                                     | <span data-ttu-id="45d6a-167">Esempio</span><span class="sxs-lookup"><span data-stu-id="45d6a-167">Example</span></span>                                |
| ---------------------------- | ----------------------------------------------------- | -------------------------------------- |
| `{APP NAME}`                 | &mdash;                                               | `BlazorSample`                         |
| `{CLIENT APP CLIENT ID}`     | <span data-ttu-id="45d6a-168">ID applicazione (client) per l' *app client*</span><span class="sxs-lookup"><span data-stu-id="45d6a-168">Application (client) ID for the *Client app*</span></span>          | `4369008b-21fa-427c-abaa-9b53bf58e538` |
| `{DEFAULT SCOPE}`            | <span data-ttu-id="45d6a-169">Nome ambito</span><span class="sxs-lookup"><span data-stu-id="45d6a-169">Scope name</span></span>                                            | `API.Access`                           |
| `{SERVER API APP CLIENT ID}` | <span data-ttu-id="45d6a-170">ID applicazione (client) per l' *app per le API server*</span><span class="sxs-lookup"><span data-stu-id="45d6a-170">Application (client) ID for the *Server API app*</span></span>      | `41451fa7-82d9-4673-8fa5-69eff5a761fd` |
| `{SERVER API APP ID URI}`    | <span data-ttu-id="45d6a-171">URI ID applicazione ([vedere la nota](#access-token-scopes))</span><span class="sxs-lookup"><span data-stu-id="45d6a-171">Application ID URI ([see note](#access-token-scopes))</span></span> | `41451fa7-82d9-4673-8fa5-69eff5a761fd` |
| `{TENANT DOMAIN}`            | <span data-ttu-id="45d6a-172">Dominio primario/server di pubblicazione/tenant</span><span class="sxs-lookup"><span data-stu-id="45d6a-172">Primary/Publisher/Tenant domain</span></span>                       | `contoso.onmicrosoft.com`              |
| `{TENANT ID}`                | <span data-ttu-id="45d6a-173">ID directory (tenant)</span><span class="sxs-lookup"><span data-stu-id="45d6a-173">Directory (tenant) ID</span></span>                                 | `e86c78e2-8bb4-4c41-aefd-918e0565a45e` |

<span data-ttu-id="45d6a-174">Il percorso di output specificato con l' `-o|--output` opzione Crea una cartella di progetto se non esiste e diventa parte del nome dell'app.</span><span class="sxs-lookup"><span data-stu-id="45d6a-174">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

> [!NOTE]
> <span data-ttu-id="45d6a-175">Passare l'URI ID app all' `app-id-uri` opzione, ma si noti che potrebbe essere necessaria una modifica alla configurazione nell'app client, descritta nella sezione [ambiti dei token di accesso](#access-token-scopes) .</span><span class="sxs-lookup"><span data-stu-id="45d6a-175">Pass the App ID URI to the `app-id-uri` option, but note a configuration change might be required in the client app, which is described in the [Access token scopes](#access-token-scopes) section.</span></span>

> [!NOTE]
> <span data-ttu-id="45d6a-176">Nel portale di Azure, l'URI di reindirizzamento Web delle configurazioni della piattaforma di autenticazione dell' *app client* **Authentication**  >  **Platform configurations**  >  **Web**  >  **Redirect URI** è configurato per la porta 5001 per le app eseguite nel server gheppio con le impostazioni predefinite.</span><span class="sxs-lookup"><span data-stu-id="45d6a-176">In the Azure portal, the *Client app's* **Authentication** > **Platform configurations** > **Web** > **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="45d6a-177">Se l' *app client* viene eseguita su una porta IIS Express casuale, la porta per l'app si trova nelle proprietà dell' *app* per le API del server nel pannello **debug** .</span><span class="sxs-lookup"><span data-stu-id="45d6a-177">If the *Client app* is run on a random IIS Express port, the port for the app can be found in the *Server API app's* properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="45d6a-178">Se la porta non è stata configurata in precedenza con la porta nota dell' *app client* , tornare alla registrazione dell' *app client* nell'portale di Azure e aggiornare l'URI di reindirizzamento con la porta corretta.</span><span class="sxs-lookup"><span data-stu-id="45d6a-178">If the port wasn't configured earlier with the *Client app's* known port, return to the *Client app's* registration in the Azure portal and update the redirect URI with the correct port.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="45d6a-179">Configurazione dell'app Server</span><span class="sxs-lookup"><span data-stu-id="45d6a-179">Server app configuration</span></span>

<span data-ttu-id="45d6a-180">*Questa sezione è relativa all'app della soluzione **`Server`** .*</span><span class="sxs-lookup"><span data-stu-id="45d6a-180">*This section pertains to the solution's **`Server`** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="45d6a-181">Pacchetto di autenticazione</span><span class="sxs-lookup"><span data-stu-id="45d6a-181">Authentication package</span></span>

<span data-ttu-id="45d6a-182">Il pacchetto fornisce il supporto per l'autenticazione e l'autorizzazione delle chiamate a ASP.NET Core API Web [`Microsoft.AspNetCore.Authentication.AzureAD.UI`](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI/) :</span><span class="sxs-lookup"><span data-stu-id="45d6a-182">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the [`Microsoft.AspNetCore.Authentication.AzureAD.UI`](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI/) package:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureAD.UI" 
  Version="3.1.4" />
```

### <a name="authentication-service-support"></a><span data-ttu-id="45d6a-183">Supporto del servizio di autenticazione</span><span class="sxs-lookup"><span data-stu-id="45d6a-183">Authentication service support</span></span>

<span data-ttu-id="45d6a-184">Il `AddAuthentication` metodo imposta i servizi di autenticazione all'interno dell'app e configura il gestore di JWT Bearer come metodo di autenticazione predefinito.</span><span class="sxs-lookup"><span data-stu-id="45d6a-184">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="45d6a-185">Il <xref:Microsoft.AspNetCore.Authentication.AzureADAuthenticationBuilderExtensions.AddAzureADBearer%2A> metodo imposta i parametri specifici nel gestore di JWT Bearer necessario per convalidare i token emessi dal Azure Active Directory:</span><span class="sxs-lookup"><span data-stu-id="45d6a-185">The <xref:Microsoft.AspNetCore.Authentication.AzureADAuthenticationBuilderExtensions.AddAzureADBearer%2A> method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory:</span></span>

```csharp
services.AddAuthentication(AzureADDefaults.BearerAuthenticationScheme)
    .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
```

<span data-ttu-id="45d6a-186"><xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> e <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> verificare che:</span><span class="sxs-lookup"><span data-stu-id="45d6a-186"><xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> and <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> ensure that:</span></span>

* <span data-ttu-id="45d6a-187">L'app tenta di analizzare e convalidare i token nelle richieste in ingresso.</span><span class="sxs-lookup"><span data-stu-id="45d6a-187">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="45d6a-188">Eventuali richieste che tentano di accedere a una risorsa protetta senza credenziali appropriate hanno esito negativo.</span><span class="sxs-lookup"><span data-stu-id="45d6a-188">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="userno-locidentityname"></a><span data-ttu-id="45d6a-189">Utente. Identity . Nome</span><span class="sxs-lookup"><span data-stu-id="45d6a-189">User.Identity.Name</span></span>

<span data-ttu-id="45d6a-190">Per impostazione predefinita, l'API dell'app Server viene popolata `User.Identity.Name` con il valore del `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` tipo di attestazione (ad esempio, `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com` ).</span><span class="sxs-lookup"><span data-stu-id="45d6a-190">By default, the Server app API populates `User.Identity.Name` with the value from the `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` claim type (for example, `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com`).</span></span>

<span data-ttu-id="45d6a-191">Per configurare l'app per la ricezione del valore dal `name` tipo di attestazione, configurare il <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType> di <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="45d6a-191">To configure the app to receive the value from the `name` claim type, configure the <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType> of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;

...

services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a><span data-ttu-id="45d6a-192">Impostazioni dell'app</span><span class="sxs-lookup"><span data-stu-id="45d6a-192">App settings</span></span>

<span data-ttu-id="45d6a-193">Il `appsettings.json` file contiene le opzioni per configurare il gestore di connessione JWT usato per convalidare i token di accesso:</span><span class="sxs-lookup"><span data-stu-id="45d6a-193">The `appsettings.json` file contains the options to configure the JWT bearer handler used to validate access tokens:</span></span>

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

<span data-ttu-id="45d6a-194">Esempio:</span><span class="sxs-lookup"><span data-stu-id="45d6a-194">Example:</span></span>

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

### <a name="weatherforecast-controller"></a><span data-ttu-id="45d6a-195">Controller WeatherForecast</span><span class="sxs-lookup"><span data-stu-id="45d6a-195">WeatherForecast controller</span></span>

<span data-ttu-id="45d6a-196">Il controller WeatherForecast (*Controllers/WeatherForecastController. cs*) espone un'API protetta con l' [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attributo applicato al controller.</span><span class="sxs-lookup"><span data-stu-id="45d6a-196">The WeatherForecast controller (*Controllers/WeatherForecastController.cs*) exposes a protected API with the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute applied to the controller.</span></span> <span data-ttu-id="45d6a-197">È **importante** comprendere che:</span><span class="sxs-lookup"><span data-stu-id="45d6a-197">It's **important** to understand that:</span></span>

* <span data-ttu-id="45d6a-198">L' [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attributo in questo controller API è l'unico elemento che protegge questa API da accessi non autorizzati.</span><span class="sxs-lookup"><span data-stu-id="45d6a-198">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="45d6a-199">L' [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attributo usato nell' Blazor WebAssembly app funge solo da hint per l'app che l'utente deve essere autorizzato affinché l'app funzioni correttamente.</span><span class="sxs-lookup"><span data-stu-id="45d6a-199">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute used in the Blazor WebAssembly app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

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

## <a name="client-app-configuration"></a><span data-ttu-id="45d6a-200">Configurazione dell'app client</span><span class="sxs-lookup"><span data-stu-id="45d6a-200">Client app configuration</span></span>

<span data-ttu-id="45d6a-201">*Questa sezione è relativa all'app della soluzione **`Client`** .*</span><span class="sxs-lookup"><span data-stu-id="45d6a-201">*This section pertains to the solution's **`Client`** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="45d6a-202">Pacchetto di autenticazione</span><span class="sxs-lookup"><span data-stu-id="45d6a-202">Authentication package</span></span>

<span data-ttu-id="45d6a-203">Quando viene creata un'app per usare gli account aziendali o dell'Istituto di istruzione ( `SingleOrg` ), l'app riceve automaticamente un riferimento al pacchetto per [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ( [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) ).</span><span class="sxs-lookup"><span data-stu-id="45d6a-203">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)).</span></span> <span data-ttu-id="45d6a-204">Il pacchetto fornisce un set di primitive che consentono all'app di autenticare gli utenti e ottenere i token per chiamare le API protette.</span><span class="sxs-lookup"><span data-stu-id="45d6a-204">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="45d6a-205">Se si aggiunge l'autenticazione a un'app, aggiungere manualmente il pacchetto al file di progetto dell'app:</span><span class="sxs-lookup"><span data-stu-id="45d6a-205">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="3.2.0" />
```

<span data-ttu-id="45d6a-206">Il [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) pacchetto aggiunge il [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) pacchetto all'app in modo transitivo.</span><span class="sxs-lookup"><span data-stu-id="45d6a-206">The [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) package transitively adds the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="45d6a-207">Supporto del servizio di autenticazione</span><span class="sxs-lookup"><span data-stu-id="45d6a-207">Authentication service support</span></span>

<span data-ttu-id="45d6a-208"><xref:System.Net.Http.HttpClient>Viene aggiunto il supporto per le istanze di che includono token di accesso quando si effettuano richieste al progetto server.</span><span class="sxs-lookup"><span data-stu-id="45d6a-208">Support for <xref:System.Net.Http.HttpClient> instances is added that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="45d6a-209">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="45d6a-209">`Program.cs`:</span></span>

```csharp
builder.Services.AddHttpClient("{APP ASSEMBLY}.ServerAPI", client => 
        client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddScoped(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("{APP ASSEMBLY}.ServerAPI"));
```

<span data-ttu-id="45d6a-210">Il segnaposto `{APP ASSEMBLY}` è il nome dell'assembly dell'app (ad esempio, `BlazorSample.ServerAPI` ).</span><span class="sxs-lookup"><span data-stu-id="45d6a-210">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `BlazorSample.ServerAPI`).</span></span>

<span data-ttu-id="45d6a-211">Il supporto per l'autenticazione degli utenti viene registrato nel contenitore del servizio con il <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> metodo di estensione fornito dal [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) pacchetto.</span><span class="sxs-lookup"><span data-stu-id="45d6a-211">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> extension method provided by the [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) package.</span></span> <span data-ttu-id="45d6a-212">Questo metodo configura i servizi necessari per l'interazione dell'app con il Identity provider (IP).</span><span class="sxs-lookup"><span data-stu-id="45d6a-212">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="45d6a-213">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="45d6a-213">`Program.cs`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="45d6a-214">Il <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> metodo accetta un callback per configurare i parametri necessari per autenticare un'app.</span><span class="sxs-lookup"><span data-stu-id="45d6a-214">The <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="45d6a-215">Quando si registra l'app, è possibile ottenere i valori necessari per la configurazione dell'app dalla configurazione di AAD del portale di Azure.</span><span class="sxs-lookup"><span data-stu-id="45d6a-215">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

<span data-ttu-id="45d6a-216">La configurazione viene fornita dal `wwwroot/appsettings.json` file:</span><span class="sxs-lookup"><span data-stu-id="45d6a-216">Configuration is supplied by the `wwwroot/appsettings.json` file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/{TENANT ID}",
    "ClientId": "{CLIENT APP CLIENT ID}",
    "ValidateAuthority": true
  }
}
```

<span data-ttu-id="45d6a-217">Esempio:</span><span class="sxs-lookup"><span data-stu-id="45d6a-217">Example:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/e86c78e2-...-918e0565a45e",
    "ClientId": "4369008b-21fa-427c-abaa-9b53bf58e538",
    "ValidateAuthority": true
  }
}
```

### <a name="access-token-scopes"></a><span data-ttu-id="45d6a-218">Ambiti del token di accesso</span><span class="sxs-lookup"><span data-stu-id="45d6a-218">Access token scopes</span></span>

<span data-ttu-id="45d6a-219">Gli ambiti dei token di accesso predefiniti rappresentano l'elenco degli ambiti dei token di accesso:</span><span class="sxs-lookup"><span data-stu-id="45d6a-219">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="45d6a-220">Incluso per impostazione predefinita nella richiesta di accesso.</span><span class="sxs-lookup"><span data-stu-id="45d6a-220">Included by default in the sign in request.</span></span>
* <span data-ttu-id="45d6a-221">Utilizzato per eseguire il provisioning di un token di accesso immediatamente dopo l'autenticazione.</span><span class="sxs-lookup"><span data-stu-id="45d6a-221">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="45d6a-222">Tutti gli ambiti devono appartenere alla stessa app per ogni regola di Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="45d6a-222">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="45d6a-223">Per altre app per le API è possibile aggiungere altri ambiti, se necessario:</span><span class="sxs-lookup"><span data-stu-id="45d6a-223">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

<span data-ttu-id="45d6a-224">Per ulteriori informazioni, vedere le sezioni seguenti dell'articolo *scenari aggiuntivi* :</span><span class="sxs-lookup"><span data-stu-id="45d6a-224">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="45d6a-225">Richiedere token di accesso aggiuntivi</span><span class="sxs-lookup"><span data-stu-id="45d6a-225">Request additional access tokens</span></span>](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="45d6a-226">Connetti token alle richieste in uscita</span><span class="sxs-lookup"><span data-stu-id="45d6a-226">Attach tokens to outgoing requests</span></span>](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

::: moniker range=">= aspnetcore-5.0"

### <a name="login-mode"></a><span data-ttu-id="45d6a-227">Modalità di accesso</span><span class="sxs-lookup"><span data-stu-id="45d6a-227">Login mode</span></span>

[!INCLUDE[](~/includes/blazor-security/msal-login-mode.md)]

::: moniker-end

### <a name="imports-file"></a><span data-ttu-id="45d6a-228">Importa file</span><span class="sxs-lookup"><span data-stu-id="45d6a-228">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="45d6a-229">Pagina di indice</span><span class="sxs-lookup"><span data-stu-id="45d6a-229">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a><span data-ttu-id="45d6a-230">Componente app</span><span class="sxs-lookup"><span data-stu-id="45d6a-230">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="45d6a-231">Componente RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="45d6a-231">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="45d6a-232">Componente LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="45d6a-232">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="45d6a-233">Componente di autenticazione</span><span class="sxs-lookup"><span data-stu-id="45d6a-233">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="45d6a-234">Componente FetchData</span><span class="sxs-lookup"><span data-stu-id="45d6a-234">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="45d6a-235">Eseguire l'app</span><span class="sxs-lookup"><span data-stu-id="45d6a-235">Run the app</span></span>

<span data-ttu-id="45d6a-236">Eseguire l'app dal progetto server.</span><span class="sxs-lookup"><span data-stu-id="45d6a-236">Run the app from the Server project.</span></span> <span data-ttu-id="45d6a-237">Quando si usa Visual Studio, eseguire una delle operazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="45d6a-237">When using Visual Studio, either:</span></span>

* <span data-ttu-id="45d6a-238">Impostare l'elenco a discesa **progetti di avvio** nella barra degli strumenti sull'app per le *API server* e selezionare il pulsante **Esegui** .</span><span class="sxs-lookup"><span data-stu-id="45d6a-238">Set the **Startup Projects** drop down list in the toolbar to the *Server API app* and select the **Run** button.</span></span>
* <span data-ttu-id="45d6a-239">Selezionare il progetto server in **Esplora soluzioni** , quindi fare clic sul pulsante **Esegui** nella barra degli strumenti o avviare l'app dal menu **debug** .</span><span class="sxs-lookup"><span data-stu-id="45d6a-239">Select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="45d6a-240">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="45d6a-240">Additional resources</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="45d6a-241">Richieste API Web non autenticate o non autorizzate in un'app con un client predefinito sicuro</span><span class="sxs-lookup"><span data-stu-id="45d6a-241">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:blazor/security/webassembly/aad-groups-roles>
* <xref:security/authentication/azure-active-directory/index>
* [<span data-ttu-id="45d6a-242">Documentazione di Microsoft Identity Platform</span><span class="sxs-lookup"><span data-stu-id="45d6a-242">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
