---
title: Proteggere un' Blazor WebAssembly app ospitata ASP.NET Core con Azure Active Directory B2C
author: guardrex
description: Informazioni su come proteggere un' Blazor WebAssembly app ospitata ASP.NET Core con Azure Active Directory B2C.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/26/2020
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
uid: blazor/security/webassembly/hosted-with-azure-active-directory-b2c
ms.openlocfilehash: 1c87330dec069e05f274206d2d35f50f489f9623
ms.sourcegitcommit: da5a5bed5718a9f8db59356ef8890b4b60ced6e9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/22/2021
ms.locfileid: "98710620"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-hosted-app-with-azure-active-directory-b2c"></a><span data-ttu-id="6b9fb-103">Proteggere un' Blazor WebAssembly app ospitata ASP.NET Core con Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="6b9fb-103">Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory B2C</span></span>

<span data-ttu-id="6b9fb-104">Di [Javier Calvarro Nelson](https://github.com/javiercn) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="6b9fb-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="6b9fb-105">Questo articolo descrive come creare un' [ Blazor WebAssembly app ospitata](xref:blazor/hosting-models#blazor-webassembly) che usa [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) per l'autenticazione.</span><span class="sxs-lookup"><span data-stu-id="6b9fb-105">This article describes how to create a [hosted Blazor WebAssembly app](xref:blazor/hosting-models#blazor-webassembly) that uses [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) for authentication.</span></span>

## <a name="register-apps-in-aad-b2c-and-create-solution"></a><span data-ttu-id="6b9fb-106">Registrare le app in AAD B2C e creare la soluzione</span><span class="sxs-lookup"><span data-stu-id="6b9fb-106">Register apps in AAD B2C and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="6b9fb-107">Creare un tenant</span><span class="sxs-lookup"><span data-stu-id="6b9fb-107">Create a tenant</span></span>

<span data-ttu-id="6b9fb-108">Seguire le istruzioni riportate in [esercitazione: creare un tenant di Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-create-tenant) per creare un tenant di AAD B2C.</span><span class="sxs-lookup"><span data-stu-id="6b9fb-108">Follow the guidance in [Tutorial: Create an Azure Active Directory B2C tenant](/azure/active-directory-b2c/tutorial-create-tenant) to create an AAD B2C tenant.</span></span> <span data-ttu-id="6b9fb-109">Tornare a questo articolo immediatamente dopo la creazione o l'identificazione di un tenant da usare.</span><span class="sxs-lookup"><span data-stu-id="6b9fb-109">Return to this article immediately after creating or identifying a tenant to use.</span></span>

<span data-ttu-id="6b9fb-110">Registrare l'istanza di AAD B2C, ad esempio, `https://contoso.b2clogin.com/` che include la barra finale.</span><span class="sxs-lookup"><span data-stu-id="6b9fb-110">Record the AAD B2C instance (for example, `https://contoso.b2clogin.com/`, which includes the trailing slash).</span></span> <span data-ttu-id="6b9fb-111">L'istanza è lo schema e l'host di una registrazione di app di Azure B2C, che è possibile trovare aprendo la finestra **endpoint** dalla pagina **registrazioni app** nella portale di Azure.</span><span class="sxs-lookup"><span data-stu-id="6b9fb-111">The instance is the scheme and host of an Azure B2C app registration, which can be found by opening the **Endpoints** window from the **App registrations** page in the Azure portal.</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="6b9fb-112">Registrare un'app per le API server</span><span class="sxs-lookup"><span data-stu-id="6b9fb-112">Register a server API app</span></span>

<span data-ttu-id="6b9fb-113">Registrare un'app AAD B2C per l' *app* per le API del server:</span><span class="sxs-lookup"><span data-stu-id="6b9fb-113">Register an AAD B2C app for the *Server API app*:</span></span>

1. <span data-ttu-id="6b9fb-114">In **Azure Active Directory**  >  **registrazioni app** selezionare **nuova registrazione**.</span><span class="sxs-lookup"><span data-stu-id="6b9fb-114">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="6b9fb-115">Specificare un **nome** per l'app, ad esempio **Blazor Server AAD B2C**.</span><span class="sxs-lookup"><span data-stu-id="6b9fb-115">Provide a **Name** for the app (for example, **Blazor Server AAD B2C**).</span></span>
1. <span data-ttu-id="6b9fb-116">Per i **tipi di account supportati**, selezionare l'opzione multi-tenant: **account in qualsiasi provider di identità o directory organizzativa (per l'autenticazione degli utenti con flussi utente)**</span><span class="sxs-lookup"><span data-stu-id="6b9fb-116">For **Supported account types**, select the multi-tenant option: **Accounts in any identity provider or organizational directory (for authenticating users with user flows)**</span></span>
1. <span data-ttu-id="6b9fb-117">L' *app* per le API del server non richiede un **URI di reindirizzamento** in questo scenario, quindi lasciare l'elenco a discesa impostato su **Web** e non immettere un URI di reindirizzamento.</span><span class="sxs-lookup"><span data-stu-id="6b9fb-117">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="6b9fb-118">Verificare che   >  **le autorizzazioni concedano il consenso dell'amministratore per OpenID e offline_access autorizzazioni** siano selezionate.</span><span class="sxs-lookup"><span data-stu-id="6b9fb-118">Confirm that **Permissions** > **Grant admin consent to openid and offline_access permissions** is selected.</span></span>
1. <span data-ttu-id="6b9fb-119">Selezionare **Registra**.</span><span class="sxs-lookup"><span data-stu-id="6b9fb-119">Select **Register**.</span></span>

<span data-ttu-id="6b9fb-120">Registrare le seguenti informazioni:</span><span class="sxs-lookup"><span data-stu-id="6b9fb-120">Record the following information:</span></span>

* <span data-ttu-id="6b9fb-121">*App per le API server* ID applicazione (client) (ad esempio, `41451fa7-82d9-4673-8fa5-69eff5a761fd` )</span><span class="sxs-lookup"><span data-stu-id="6b9fb-121">*Server API app* Application (client) ID (for example, `41451fa7-82d9-4673-8fa5-69eff5a761fd`)</span></span>
* <span data-ttu-id="6b9fb-122">Dominio primario/editore/tenant di AAD (ad esempio, `contoso.onmicrosoft.com` ): il dominio è disponibile come **dominio del server di pubblicazione** nel pannello **personalizzazione** del portale di Azure per l'app registrata.</span><span class="sxs-lookup"><span data-stu-id="6b9fb-122">AAD Primary/Publisher/Tenant domain (for example, `contoso.onmicrosoft.com`): The domain is available as the **Publisher domain** in the **Branding** blade of the Azure portal for the registered app.</span></span>

<span data-ttu-id="6b9fb-123">In **esporre un'API**:</span><span class="sxs-lookup"><span data-stu-id="6b9fb-123">In **Expose an API**:</span></span>

1. <span data-ttu-id="6b9fb-124">Selezionare **Aggiungi un ambito**.</span><span class="sxs-lookup"><span data-stu-id="6b9fb-124">Select **Add a scope**.</span></span>
1. <span data-ttu-id="6b9fb-125">Fai clic su **Salva e continua**.</span><span class="sxs-lookup"><span data-stu-id="6b9fb-125">Select **Save and continue**.</span></span>
1. <span data-ttu-id="6b9fb-126">Specificare un **nome** per l'ambito (ad esempio, `API.Access` ).</span><span class="sxs-lookup"><span data-stu-id="6b9fb-126">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="6b9fb-127">Fornire un **nome visualizzato** per il consenso dell'amministratore, ad esempio `Access API` .</span><span class="sxs-lookup"><span data-stu-id="6b9fb-127">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="6b9fb-128">Fornire una **Descrizione del consenso dell'amministratore** , ad esempio `Allows the app to access server app API endpoints.` .</span><span class="sxs-lookup"><span data-stu-id="6b9fb-128">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="6b9fb-129">Verificare che lo **stato** sia impostato su **abilitato**.</span><span class="sxs-lookup"><span data-stu-id="6b9fb-129">Confirm that the **State** is set to **Enabled**.</span></span>
1. <span data-ttu-id="6b9fb-130">Selezionare **Aggiungi ambito**.</span><span class="sxs-lookup"><span data-stu-id="6b9fb-130">Select **Add scope**.</span></span>

<span data-ttu-id="6b9fb-131">Registrare le seguenti informazioni:</span><span class="sxs-lookup"><span data-stu-id="6b9fb-131">Record the following information:</span></span>

* <span data-ttu-id="6b9fb-132">URI ID app (ad esempio, `api://41451fa7-82d9-4673-8fa5-69eff5a761fd` , `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd` o il valore personalizzato specificato)</span><span class="sxs-lookup"><span data-stu-id="6b9fb-132">App ID URI (for example, `api://41451fa7-82d9-4673-8fa5-69eff5a761fd`, `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd`, or the custom value that you provided)</span></span>
* <span data-ttu-id="6b9fb-133">Nome ambito (ad esempio, `API.Access` )</span><span class="sxs-lookup"><span data-stu-id="6b9fb-133">Scope name (for example, `API.Access`)</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="6b9fb-134">Registrare un'app client</span><span class="sxs-lookup"><span data-stu-id="6b9fb-134">Register a client app</span></span>

<span data-ttu-id="6b9fb-135">Registrare un'app AAD B2C per l' *app client*:</span><span class="sxs-lookup"><span data-stu-id="6b9fb-135">Register an AAD B2C app for the *Client app*:</span></span>

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="6b9fb-136">In **Azure Active Directory** > **registrazioni app** selezionare **nuova registrazione**.</span><span class="sxs-lookup"><span data-stu-id="6b9fb-136">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="6b9fb-137">Specificare un **nome** per l'app, ad esempio **Blazor AAD B2C client**.</span><span class="sxs-lookup"><span data-stu-id="6b9fb-137">Provide a **Name** for the app (for example, **Blazor Client AAD B2C**).</span></span>
1. <span data-ttu-id="6b9fb-138">Per i **tipi di account supportati**, selezionare l'opzione multi-tenant: **account in qualsiasi provider di identità o directory organizzativa (per l'autenticazione degli utenti con flussi utente)**</span><span class="sxs-lookup"><span data-stu-id="6b9fb-138">For **Supported account types**, select the multi-tenant option: **Accounts in any identity provider or organizational directory (for authenticating users with user flows)**</span></span>
1. <span data-ttu-id="6b9fb-139">Impostare l'elenco a discesa **URI di reindirizzamento** sull' **applicazione a pagina singola (Spa)** e specificare l'URI di reindirizzamento seguente: `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="6b9fb-139">Set the **Redirect URI** drop down to **Single-page application (SPA)** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="6b9fb-140">La porta predefinita per un'app in esecuzione in Kestrel è la 5001.</span><span class="sxs-lookup"><span data-stu-id="6b9fb-140">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="6b9fb-141">Se l'app viene eseguita su una porta di Gheppio diversa, usare la porta dell'app.</span><span class="sxs-lookup"><span data-stu-id="6b9fb-141">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="6b9fb-142">Per IIS Express, la porta generata in modo casuale per l'app si trova nelle *`Server`* proprietà dell'app nel pannello **debug** .</span><span class="sxs-lookup"><span data-stu-id="6b9fb-142">For IIS Express, the randomly generated port for the app can be found in the *`Server`* app's properties in the **Debug** panel.</span></span> <span data-ttu-id="6b9fb-143">Poiché l'app non esiste in questo momento e la porta IIS Express non è nota, tornare a questo passaggio dopo la creazione dell'app e aggiornare l'URI di reindirizzamento.</span><span class="sxs-lookup"><span data-stu-id="6b9fb-143">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="6b9fb-144">Un contrassegno viene visualizzato nella sezione [creare l'app](#create-the-app) per ricordare IIS Express agli utenti di aggiornare l'URI di reindirizzamento.</span><span class="sxs-lookup"><span data-stu-id="6b9fb-144">A remark appears in the [Create the app](#create-the-app) section to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="6b9fb-145">Verificare che  > **le autorizzazioni concedano il consenso dell'amministratore per OpenID e offline_access autorizzazioni** siano selezionate.</span><span class="sxs-lookup"><span data-stu-id="6b9fb-145">Confirm that **Permissions** > **Grant admin consent to openid and offline_access permissions** is selected.</span></span>
1. <span data-ttu-id="6b9fb-146">Selezionare **Registra**.</span><span class="sxs-lookup"><span data-stu-id="6b9fb-146">Select **Register**.</span></span>

1. <span data-ttu-id="6b9fb-147">Registrare l'ID dell'applicazione (client) (ad esempio, `4369008b-21fa-427c-abaa-9b53bf58e538` ).</span><span class="sxs-lookup"><span data-stu-id="6b9fb-147">Record the Application (client) ID (for example, `4369008b-21fa-427c-abaa-9b53bf58e538`).</span></span>

<span data-ttu-id="6b9fb-148">Nelle configurazioni della piattaforma di **autenticazione** >  > **applicazione a pagina singola (Spa)**:</span><span class="sxs-lookup"><span data-stu-id="6b9fb-148">In **Authentication** > **Platform configurations** > **Single-page application (SPA)**:</span></span>

1. <span data-ttu-id="6b9fb-149">Verificare che l' **URI di reindirizzamento** di `https://localhost:{PORT}/authentication/login-callback` sia presente.</span><span class="sxs-lookup"><span data-stu-id="6b9fb-149">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="6b9fb-150">Per **concessione implicita**, assicurarsi che le caselle di controllo per i **token di accesso** e i **token ID** **non** siano selezionate.</span><span class="sxs-lookup"><span data-stu-id="6b9fb-150">For **Implicit grant**, ensure that the check boxes for **Access tokens** and **ID tokens** are **not** selected.</span></span>
1. <span data-ttu-id="6b9fb-151">Per questa esperienza sono accettabili le impostazioni predefinite rimanenti per l'app.</span><span class="sxs-lookup"><span data-stu-id="6b9fb-151">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="6b9fb-152">Fare clic sul pulsante **Salva**.</span><span class="sxs-lookup"><span data-stu-id="6b9fb-152">Select the **Save** button.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="6b9fb-153">In **Azure Active Directory** > **registrazioni app** selezionare **nuova registrazione**.</span><span class="sxs-lookup"><span data-stu-id="6b9fb-153">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="6b9fb-154">Specificare un **nome** per l'app, ad esempio **Blazor AAD B2C client**.</span><span class="sxs-lookup"><span data-stu-id="6b9fb-154">Provide a **Name** for the app (for example, **Blazor Client AAD B2C**).</span></span>
1. <span data-ttu-id="6b9fb-155">Per i **tipi di account supportati**, selezionare l'opzione multi-tenant: **account in qualsiasi provider di identità o directory organizzativa (per l'autenticazione degli utenti con flussi utente)**</span><span class="sxs-lookup"><span data-stu-id="6b9fb-155">For **Supported account types**, select the multi-tenant option: **Accounts in any identity provider or organizational directory (for authenticating users with user flows)**</span></span>
1. <span data-ttu-id="6b9fb-156">Lasciare l'elenco a discesa **URI di reindirizzamento** impostato su **Web** e specificare l'URI di reindirizzamento seguente: `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="6b9fb-156">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="6b9fb-157">La porta predefinita per un'app in esecuzione in Kestrel è la 5001.</span><span class="sxs-lookup"><span data-stu-id="6b9fb-157">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="6b9fb-158">Se l'app viene eseguita su una porta di Gheppio diversa, usare la porta dell'app.</span><span class="sxs-lookup"><span data-stu-id="6b9fb-158">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="6b9fb-159">Per IIS Express, la porta generata in modo casuale per l'app si trova nelle *`Server`* proprietà dell'app nel pannello **debug** .</span><span class="sxs-lookup"><span data-stu-id="6b9fb-159">For IIS Express, the randomly generated port for the app can be found in the *`Server`* app's properties in the **Debug** panel.</span></span> <span data-ttu-id="6b9fb-160">Poiché l'app non esiste in questo momento e la porta IIS Express non è nota, tornare a questo passaggio dopo la creazione dell'app e aggiornare l'URI di reindirizzamento.</span><span class="sxs-lookup"><span data-stu-id="6b9fb-160">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="6b9fb-161">Un contrassegno viene visualizzato nella sezione [creare l'app](#create-the-app) per ricordare IIS Express agli utenti di aggiornare l'URI di reindirizzamento.</span><span class="sxs-lookup"><span data-stu-id="6b9fb-161">A remark appears in the [Create the app](#create-the-app) section to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="6b9fb-162">Verificare che  > **le autorizzazioni concedano il consenso dell'amministratore per OpenID e offline_access autorizzazioni** siano selezionate.</span><span class="sxs-lookup"><span data-stu-id="6b9fb-162">Confirm that **Permissions** > **Grant admin consent to openid and offline_access permissions** is selected.</span></span>
1. <span data-ttu-id="6b9fb-163">Selezionare **Registra**.</span><span class="sxs-lookup"><span data-stu-id="6b9fb-163">Select **Register**.</span></span>

<span data-ttu-id="6b9fb-164">Registrare l'ID dell'applicazione (client) (ad esempio, `4369008b-21fa-427c-abaa-9b53bf58e538` ).</span><span class="sxs-lookup"><span data-stu-id="6b9fb-164">Record the Application (client) ID (for example, `4369008b-21fa-427c-abaa-9b53bf58e538`).</span></span>

<span data-ttu-id="6b9fb-165">In  > **configurazioni piattaforma** di autenticazione > **Web**:</span><span class="sxs-lookup"><span data-stu-id="6b9fb-165">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="6b9fb-166">Verificare che l' **URI di reindirizzamento** di `https://localhost:{PORT}/authentication/login-callback` sia presente.</span><span class="sxs-lookup"><span data-stu-id="6b9fb-166">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="6b9fb-167">Per **concessione implicita**, selezionare le caselle di controllo per i token di **accesso** e i **token ID**.</span><span class="sxs-lookup"><span data-stu-id="6b9fb-167">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="6b9fb-168">Per questa esperienza sono accettabili le impostazioni predefinite rimanenti per l'app.</span><span class="sxs-lookup"><span data-stu-id="6b9fb-168">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="6b9fb-169">Fare clic sul pulsante **Salva**.</span><span class="sxs-lookup"><span data-stu-id="6b9fb-169">Select the **Save** button.</span></span>

::: moniker-end

<span data-ttu-id="6b9fb-170">In **autorizzazioni API**:</span><span class="sxs-lookup"><span data-stu-id="6b9fb-170">In **API permissions**:</span></span>

1. <span data-ttu-id="6b9fb-171">Selezionare **Aggiungi un'autorizzazione** seguita da **API personali**.</span><span class="sxs-lookup"><span data-stu-id="6b9fb-171">Select **Add a permission** followed by **My APIs**.</span></span>
1. <span data-ttu-id="6b9fb-172">Selezionare l' *app per le API server* dalla colonna **nome** , ad esempio **Blazor Server AAD B2C**.</span><span class="sxs-lookup"><span data-stu-id="6b9fb-172">Select the *Server API app* from the **Name** column (for example, **Blazor Server AAD B2C**).</span></span>
1. <span data-ttu-id="6b9fb-173">Aprire l'elenco di **API** .</span><span class="sxs-lookup"><span data-stu-id="6b9fb-173">Open the **API** list.</span></span>
1. <span data-ttu-id="6b9fb-174">Abilitare l'accesso all'API (ad esempio, `API.Access` ).</span><span class="sxs-lookup"><span data-stu-id="6b9fb-174">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="6b9fb-175">Selezionare **Aggiungi autorizzazioni**.</span><span class="sxs-lookup"><span data-stu-id="6b9fb-175">Select **Add permissions**.</span></span>
1. <span data-ttu-id="6b9fb-176">Selezionare il pulsante **concedere il consenso dell'amministratore per {tenant Name}** .</span><span class="sxs-lookup"><span data-stu-id="6b9fb-176">Select the **Grant admin consent for {TENANT NAME}** button.</span></span> <span data-ttu-id="6b9fb-177">Selezionare **Sì** per confermare.</span><span class="sxs-lookup"><span data-stu-id="6b9fb-177">Select **Yes** to confirm.</span></span>

<span data-ttu-id="6b9fb-178">In **Home**  >  **Azure ad B2C**  >  **flussi utente**:</span><span class="sxs-lookup"><span data-stu-id="6b9fb-178">In **Home** > **Azure AD B2C** > **User flows**:</span></span>

[<span data-ttu-id="6b9fb-179">Creare un flusso utente di iscrizione e accesso</span><span class="sxs-lookup"><span data-stu-id="6b9fb-179">Create a sign-up and sign-in user flow</span></span>](/azure/active-directory-b2c/tutorial-create-user-flows)

<span data-ttu-id="6b9fb-180">Selezionare almeno l'attributo utente nome visualizzato dell' **applicazione Claims**  >   per popolare `context.User.Identity.Name` nel `LoginDisplay` componente ( `Shared/LoginDisplay.razor` ).</span><span class="sxs-lookup"><span data-stu-id="6b9fb-180">At a minimum, select the **Application claims** > **Display Name** user attribute to populate the `context.User.Identity.Name` in the `LoginDisplay` component (`Shared/LoginDisplay.razor`).</span></span>

<span data-ttu-id="6b9fb-181">Registrare il nome del flusso utente di iscrizione e accesso creato per l'app (ad esempio, `B2C_1_signupsignin` ).</span><span class="sxs-lookup"><span data-stu-id="6b9fb-181">Record the sign-up and sign-in user flow name created for the app (for example, `B2C_1_signupsignin`).</span></span>

### <a name="create-the-app"></a><span data-ttu-id="6b9fb-182">Creare l'app</span><span class="sxs-lookup"><span data-stu-id="6b9fb-182">Create the app</span></span>

<span data-ttu-id="6b9fb-183">Sostituire i segnaposto nel comando seguente con le informazioni registrate in precedenza ed eseguire il comando in una shell dei comandi:</span><span class="sxs-lookup"><span data-stu-id="6b9fb-183">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{TENANT DOMAIN}" -ho -o {APP NAME} -ssp "{SIGN UP OR SIGN IN POLICY}"
```

| <span data-ttu-id="6b9fb-184">Segnaposto</span><span class="sxs-lookup"><span data-stu-id="6b9fb-184">Placeholder</span></span>                   | <span data-ttu-id="6b9fb-185">Nome portale di Azure</span><span class="sxs-lookup"><span data-stu-id="6b9fb-185">Azure portal name</span></span>                                     | <span data-ttu-id="6b9fb-186">Esempio</span><span class="sxs-lookup"><span data-stu-id="6b9fb-186">Example</span></span>                                        |
| ----------------------------- | ----------------------------------------------------- | ---------------------------------------------- |
| `{AAD B2C INSTANCE}`          | <span data-ttu-id="6b9fb-187">Istanza</span><span class="sxs-lookup"><span data-stu-id="6b9fb-187">Instance</span></span>                                              | `https://contoso.b2clogin.com/`                |
| `{APP NAME}`                  | &mdash;                                               | `BlazorSample`                                 |
| `{CLIENT APP CLIENT ID}`      | <span data-ttu-id="6b9fb-188">ID applicazione (client) per l' *`Client`* app</span><span class="sxs-lookup"><span data-stu-id="6b9fb-188">Application (client) ID for the *`Client`* app</span></span>        | `4369008b-21fa-427c-abaa-9b53bf58e538`         |
| `{DEFAULT SCOPE}`             | <span data-ttu-id="6b9fb-189">Nome ambito</span><span class="sxs-lookup"><span data-stu-id="6b9fb-189">Scope name</span></span>                                            | `API.Access`                                   |
| `{SERVER API APP CLIENT ID}`  | <span data-ttu-id="6b9fb-190">ID applicazione (client) per l' *app per le API server*</span><span class="sxs-lookup"><span data-stu-id="6b9fb-190">Application (client) ID for the *Server API app*</span></span>      | `41451fa7-82d9-4673-8fa5-69eff5a761fd`         |
| `{SERVER API APP ID URI}`     | <span data-ttu-id="6b9fb-191">URI dell'ID applicazione&dagger;</span><span class="sxs-lookup"><span data-stu-id="6b9fb-191">Application ID URI&dagger;</span></span>                            | `41451fa7-82d9-4673-8fa5-69eff5a761fd`&dagger; |
| `{SIGN UP OR SIGN IN POLICY}` | <span data-ttu-id="6b9fb-192">Flusso utente di iscrizione/accesso</span><span class="sxs-lookup"><span data-stu-id="6b9fb-192">Sign-up/sign-in user flow</span></span>                             | `B2C_1_signupsignin1`                          |
| `{TENANT DOMAIN}`             | <span data-ttu-id="6b9fb-193">Dominio primario/server di pubblicazione/tenant</span><span class="sxs-lookup"><span data-stu-id="6b9fb-193">Primary/Publisher/Tenant domain</span></span>                       | `contoso.onmicrosoft.com`                      |

<span data-ttu-id="6b9fb-194">&dagger;Il Blazor WebAssembly modello aggiunge automaticamente uno schema `api://` all'argomento Uri ID app passato nel `dotnet new` comando.</span><span class="sxs-lookup"><span data-stu-id="6b9fb-194">&dagger;The Blazor WebAssembly template automatically adds a scheme of `api://` to the App ID URI argument passed in the `dotnet new` command.</span></span> <span data-ttu-id="6b9fb-195">Quando si specifica l'URI ID app per il `{SERVER API APP ID URI}` segnaposto e se lo schema è `api://` , rimuovere lo schema ( `api://` ) dall'argomento, come illustrato nel valore di esempio della tabella precedente.</span><span class="sxs-lookup"><span data-stu-id="6b9fb-195">When providing the App ID URI for the `{SERVER API APP ID URI}` placeholder and if the scheme is `api://`, remove the scheme (`api://`) from the argument, as the example value in the preceding table shows.</span></span> <span data-ttu-id="6b9fb-196">Se l'URI ID app è un valore personalizzato o ha un altro schema (ad esempio, `https://` per un dominio di pubblicazione non trusted simile a `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd` ), è necessario aggiornare manualmente l'URI dell'ambito predefinito e rimuovere lo `api://` schema dopo che l' *`Client`* app è stata creata dal modello.</span><span class="sxs-lookup"><span data-stu-id="6b9fb-196">If the App ID URI is a custom value or has some other scheme (for example, `https://` for an untrusted publisher domain similar to `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd`), you must manually update the default scope URI and remove the `api://` scheme after the *`Client`* app is created by the template.</span></span> <span data-ttu-id="6b9fb-197">Per ulteriori informazioni, vedere la nota nella sezione [ambiti dei token di accesso](#access-token-scopes) .</span><span class="sxs-lookup"><span data-stu-id="6b9fb-197">For more information, see the note in the [Access token scopes](#access-token-scopes) section.</span></span> <span data-ttu-id="6b9fb-198">Blazor WebAssemblyÈ possibile che il modello venga modificato in una versione futura di ASP.NET Core per risolvere questi scenari.</span><span class="sxs-lookup"><span data-stu-id="6b9fb-198">The Blazor WebAssembly template might be changed in a future release of ASP.NET Core to address these scenarios.</span></span> <span data-ttu-id="6b9fb-199">Per altre informazioni, vedere [schema doppio per URI ID app con il Blazor modello WASM (hosted, Single org) (DotNet/aspnetcore #27417)](https://github.com/dotnet/aspnetcore/issues/27417).</span><span class="sxs-lookup"><span data-stu-id="6b9fb-199">For more information, see [Double scheme for App ID URI with Blazor WASM template (hosted, single org) (dotnet/aspnetcore #27417)](https://github.com/dotnet/aspnetcore/issues/27417).</span></span>

<span data-ttu-id="6b9fb-200">Il percorso di output specificato con l'opzione `-o|--output` consente di creare una cartella di progetto se non esiste e viene incluso nel nome dell'app.</span><span class="sxs-lookup"><span data-stu-id="6b9fb-200">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

> [!NOTE]
> <span data-ttu-id="6b9fb-201">L'ambito impostato dal Blazor modello ospitato potrebbe avere ripetutamente l'host Uri ID app.</span><span class="sxs-lookup"><span data-stu-id="6b9fb-201">The scope set up by the Hosted Blazor template might have the App ID URI host repeated.</span></span> <span data-ttu-id="6b9fb-202">Verificare che l'ambito configurato per la `DefaultAccessTokenScopes` raccolta sia corretto in `Program.Main` ( `Program.cs` ) dell' *`Client`* app.</span><span class="sxs-lookup"><span data-stu-id="6b9fb-202">Confirm that the scope configured for the `DefaultAccessTokenScopes` collection is correct in `Program.Main` (`Program.cs`) of the *`Client`* app.</span></span>

> [!NOTE]
> <span data-ttu-id="6b9fb-203">Nell'portale di Azure l' *`Client`* **URI di reindirizzamento** della configurazione della piattaforma dell'app è configurato per la porta 5001 per le app eseguite nel server gheppio con le impostazioni predefinite.</span><span class="sxs-lookup"><span data-stu-id="6b9fb-203">In the Azure portal, the *`Client`* app's platform configuration **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="6b9fb-204">Se l' *`Client`* app viene eseguita su una porta IIS Express casuale, la porta per l'app si trova nelle proprietà dell' *app* per le API del server nel pannello **debug** .</span><span class="sxs-lookup"><span data-stu-id="6b9fb-204">If the *`Client`* app is run on a random IIS Express port, the port for the app can be found in the *Server API app's* properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="6b9fb-205">Se la porta non è stata configurata in precedenza con la *`Client`* porta nota dell'app, tornare alla *`Client`* registrazione dell'app nel portale di Azure e aggiornare l'URI di reindirizzamento con la porta corretta.</span><span class="sxs-lookup"><span data-stu-id="6b9fb-205">If the port wasn't configured earlier with the *`Client`* app's known port, return to the *`Client`* app's registration in the Azure portal and update the redirect URI with the correct port.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="6b9fb-206">*`Server`* configurazione dell'app</span><span class="sxs-lookup"><span data-stu-id="6b9fb-206">*`Server`* app configuration</span></span>

<span data-ttu-id="6b9fb-207">*Questa sezione è relativa all'app della soluzione **`Server`** .*</span><span class="sxs-lookup"><span data-stu-id="6b9fb-207">*This section pertains to the solution's **`Server`** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="6b9fb-208">Pacchetto di autenticazione</span><span class="sxs-lookup"><span data-stu-id="6b9fb-208">Authentication package</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="6b9fb-209">Il supporto per l'autenticazione e l'autorizzazione delle chiamate a ASP.NET Core API Web con la Identity piattaforma Microsoft è fornito dai pacchetti seguenti:</span><span class="sxs-lookup"><span data-stu-id="6b9fb-209">The support for authenticating and authorizing calls to ASP.NET Core Web APIs with the Microsoft Identity Platform is provided by the following packages:</span></span>

* [`Microsoft.Identity.Web`](https://www.nuget.org/packages/Microsoft.Identity.Web)
* [`Microsoft.Identity.Web.UI`](https://www.nuget.org/packages/Microsoft.Identity.Web.UI)

```xml
<PackageReference Include="Microsoft.Identity.Web" Version="{VERSION}" />
<PackageReference Include="Microsoft.Identity.Web.UI" Version="{VERSION}" />
```

<span data-ttu-id="6b9fb-210">Per il segnaposto `{VERSION}` , la versione stabile più recente del pacchetto che corrisponde alla versione del Framework condiviso dell'app è disponibile nella cronologia delle **versioni** del pacchetto in NuGet.org.</span><span class="sxs-lookup"><span data-stu-id="6b9fb-210">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at NuGet.org.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="6b9fb-211">Il pacchetto fornisce il supporto per l'autenticazione e l'autorizzazione delle chiamate a ASP.NET Core API Web [`Microsoft.AspNetCore.Authentication.AzureADB2C.UI`](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureADB2C.UI) :</span><span class="sxs-lookup"><span data-stu-id="6b9fb-211">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the [`Microsoft.AspNetCore.Authentication.AzureADB2C.UI`](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureADB2C.UI) package:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureADB2C.UI" 
  Version="{VERSION}" />
```

<span data-ttu-id="6b9fb-212">Per il segnaposto `{VERSION}` , la versione stabile più recente del pacchetto che corrisponde alla versione del Framework condiviso dell'app è disponibile nella cronologia delle **versioni** del pacchetto in [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI).</span><span class="sxs-lookup"><span data-stu-id="6b9fb-212">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI).</span></span>

::: moniker-end

### <a name="authentication-service-support"></a><span data-ttu-id="6b9fb-213">Supporto del servizio di autenticazione</span><span class="sxs-lookup"><span data-stu-id="6b9fb-213">Authentication service support</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="6b9fb-214">Il `AddAuthentication` metodo imposta i servizi di autenticazione all'interno dell'app e configura il gestore di JWT Bearer come metodo di autenticazione predefinito.</span><span class="sxs-lookup"><span data-stu-id="6b9fb-214">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="6b9fb-215">Il <xref:Microsoft.Identity.Web.MicrosoftIdentityWebApiAuthenticationBuilderExtensions.AddMicrosoftIdentityWebApi%2A> metodo configura i servizi per proteggere l'API Web con Microsoft Identity Platform v 2.0.</span><span class="sxs-lookup"><span data-stu-id="6b9fb-215">The <xref:Microsoft.Identity.Web.MicrosoftIdentityWebApiAuthenticationBuilderExtensions.AddMicrosoftIdentityWebApi%2A> method configures services to protect the web API with Microsoft Identity Platform v2.0.</span></span> <span data-ttu-id="6b9fb-216">Questo metodo prevede una `AzureAdB2C` sezione nella configurazione dell'app con le impostazioni necessarie per inizializzare le opzioni di autenticazione.</span><span class="sxs-lookup"><span data-stu-id="6b9fb-216">This method expects an `AzureAdB2C` section in the app's configuration with the necessary settings to initialize authentication options.</span></span>

```csharp
services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
    .AddMicrosoftIdentityWebApi(Configuration.GetSection("AzureAdB2C"));
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="6b9fb-217">Il `AddAuthentication` metodo imposta i servizi di autenticazione all'interno dell'app e configura il gestore di JWT Bearer come metodo di autenticazione predefinito.</span><span class="sxs-lookup"><span data-stu-id="6b9fb-217">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="6b9fb-218">Il <xref:Microsoft.AspNetCore.Authentication.AzureADB2CAuthenticationBuilderExtensions.AddAzureADB2CBearer%2A> metodo imposta i parametri specifici nel gestore di JWT Bearer necessario per convalidare i token emessi dal Azure Active Directory B2C:</span><span class="sxs-lookup"><span data-stu-id="6b9fb-218">The <xref:Microsoft.AspNetCore.Authentication.AzureADB2CAuthenticationBuilderExtensions.AddAzureADB2CBearer%2A> method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory B2C:</span></span>

```csharp
services.AddAuthentication(AzureADB2CDefaults.BearerAuthenticationScheme)
    .AddAzureADB2CBearer(options => Configuration.Bind("AzureAdB2C", options));
```

::: moniker-end

<span data-ttu-id="6b9fb-219"><xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> e <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> verificare che:</span><span class="sxs-lookup"><span data-stu-id="6b9fb-219"><xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> and <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> ensure that:</span></span>

* <span data-ttu-id="6b9fb-220">L'app tenta di analizzare e convalidare i token nelle richieste in ingresso.</span><span class="sxs-lookup"><span data-stu-id="6b9fb-220">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="6b9fb-221">Eventuali richieste che tentano di accedere a una risorsa protetta senza credenziali appropriate hanno esito negativo.</span><span class="sxs-lookup"><span data-stu-id="6b9fb-221">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="userno-locidentityname"></a><span data-ttu-id="6b9fb-222">Utente. Identity . Nome</span><span class="sxs-lookup"><span data-stu-id="6b9fb-222">User.Identity.Name</span></span>

<span data-ttu-id="6b9fb-223">Per impostazione predefinita, `User.Identity.Name` non viene popolato.</span><span class="sxs-lookup"><span data-stu-id="6b9fb-223">By default, the `User.Identity.Name` isn't populated.</span></span>

<span data-ttu-id="6b9fb-224">Per configurare l'app per la ricezione del valore dal `name` tipo di attestazione:</span><span class="sxs-lookup"><span data-stu-id="6b9fb-224">To configure the app to receive the value from the `name` claim type:</span></span>

* <span data-ttu-id="6b9fb-225">Aggiungere uno spazio dei nomi per <xref:Microsoft.AspNetCore.Authentication.JwtBearer?displayProperty=fullName> a `Startup.cs` :</span><span class="sxs-lookup"><span data-stu-id="6b9fb-225">Add a namespace for <xref:Microsoft.AspNetCore.Authentication.JwtBearer?displayProperty=fullName> to `Startup.cs`:</span></span>

  ```csharp
  using Microsoft.AspNetCore.Authentication.JwtBearer;
  ```

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="6b9fb-226">Configurare <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType> di <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="6b9fb-226">Configure the <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType> of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

  ```csharp
  services.Configure<JwtBearerOptions>(
      JwtBearerDefaults.AuthenticationScheme, options =>
      {
          options.TokenValidationParameters.NameClaimType = "name";
      });
  ```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <span data-ttu-id="6b9fb-227">Configurare <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType> di <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="6b9fb-227">Configure the <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType> of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

  ```csharp
  services.Configure<JwtBearerOptions>(
      AzureADB2CDefaults.JwtBearerAuthenticationScheme, options =>
      {
          options.TokenValidationParameters.NameClaimType = "name";
      });
  ```

::: moniker-end

### <a name="app-settings"></a><span data-ttu-id="6b9fb-228">Impostazioni app</span><span class="sxs-lookup"><span data-stu-id="6b9fb-228">App settings</span></span>

<span data-ttu-id="6b9fb-229">Il `appsettings.json` file contiene le opzioni per configurare il gestore di connessione JWT usato per convalidare i token di accesso.</span><span class="sxs-lookup"><span data-stu-id="6b9fb-229">The `appsettings.json` file contains the options to configure the JWT bearer handler used to validate access tokens.</span></span>

```json
{
  "AzureAdB2C": {
    "Instance": "https://{TENANT}.b2clogin.com/",
    "ClientId": "{SERVER API APP CLIENT ID}",
    "Domain": "{TENANT DOMAIN}",
    "SignUpSignInPolicyId": "{SIGN UP OR SIGN IN POLICY}"
  }
}
```

<span data-ttu-id="6b9fb-230">Esempio:</span><span class="sxs-lookup"><span data-stu-id="6b9fb-230">Example:</span></span>

```json
{
  "AzureAdB2C": {
    "Instance": "https://contoso.b2clogin.com/",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "Domain": "contoso.onmicrosoft.com",
    "SignUpSignInPolicyId": "B2C_1_signupsignin1",
  }
}
```

### <a name="weatherforecast-controller"></a><span data-ttu-id="6b9fb-231">Controller WeatherForecast</span><span class="sxs-lookup"><span data-stu-id="6b9fb-231">WeatherForecast controller</span></span>

<span data-ttu-id="6b9fb-232">Il controller WeatherForecast (*Controllers/WeatherForecastController. cs*) espone un'API protetta con l' [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attributo applicato al controller.</span><span class="sxs-lookup"><span data-stu-id="6b9fb-232">The WeatherForecast controller (*Controllers/WeatherForecastController.cs*) exposes a protected API with the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute applied to the controller.</span></span> <span data-ttu-id="6b9fb-233">È **importante** comprendere che:</span><span class="sxs-lookup"><span data-stu-id="6b9fb-233">It's **important** to understand that:</span></span>

* <span data-ttu-id="6b9fb-234">L' [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attributo in questo controller API è l'unico elemento che protegge questa API da accessi non autorizzati.</span><span class="sxs-lookup"><span data-stu-id="6b9fb-234">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="6b9fb-235">L' [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attributo usato nell' Blazor WebAssembly app funge solo da hint per l'app che l'utente deve essere autorizzato affinché l'app funzioni correttamente.</span><span class="sxs-lookup"><span data-stu-id="6b9fb-235">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute used in the Blazor WebAssembly app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

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

## <a name="client-app-configuration"></a><span data-ttu-id="6b9fb-236">*`Client`* configurazione dell'app</span><span class="sxs-lookup"><span data-stu-id="6b9fb-236">*`Client`* app configuration</span></span>

<span data-ttu-id="6b9fb-237">*Questa sezione è relativa all'app della soluzione **`Client`** .*</span><span class="sxs-lookup"><span data-stu-id="6b9fb-237">*This section pertains to the solution's **`Client`** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="6b9fb-238">Pacchetto di autenticazione</span><span class="sxs-lookup"><span data-stu-id="6b9fb-238">Authentication package</span></span>

<span data-ttu-id="6b9fb-239">Quando viene creata un'app per usare un singolo account B2C ( `IndividualB2C` ), l'app riceve automaticamente un riferimento al pacchetto per [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ( [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) ).</span><span class="sxs-lookup"><span data-stu-id="6b9fb-239">When an app is created to use an Individual B2C Account (`IndividualB2C`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)).</span></span> <span data-ttu-id="6b9fb-240">Il pacchetto fornisce un set di primitive che consentono all'app di autenticare gli utenti e ottenere i token per chiamare le API protette.</span><span class="sxs-lookup"><span data-stu-id="6b9fb-240">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="6b9fb-241">Se si aggiunge l'autenticazione a un'app, aggiungere manualmente il pacchetto al file di progetto dell'app:</span><span class="sxs-lookup"><span data-stu-id="6b9fb-241">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="{VERSION}" />
```

<span data-ttu-id="6b9fb-242">Per il segnaposto `{VERSION}` , la versione stabile più recente del pacchetto che corrisponde alla versione del Framework condiviso dell'app è disponibile nella cronologia delle **versioni** del pacchetto in [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal).</span><span class="sxs-lookup"><span data-stu-id="6b9fb-242">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal).</span></span>

<span data-ttu-id="6b9fb-243">Il [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) pacchetto aggiunge il [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) pacchetto all'app in modo transitivo.</span><span class="sxs-lookup"><span data-stu-id="6b9fb-243">The [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package transitively adds the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="6b9fb-244">Supporto del servizio di autenticazione</span><span class="sxs-lookup"><span data-stu-id="6b9fb-244">Authentication service support</span></span>

<span data-ttu-id="6b9fb-245"><xref:System.Net.Http.HttpClient>Viene aggiunto il supporto per le istanze di che includono token di accesso quando si effettuano richieste al progetto server.</span><span class="sxs-lookup"><span data-stu-id="6b9fb-245">Support for <xref:System.Net.Http.HttpClient> instances is added that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="6b9fb-246">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="6b9fb-246">`Program.cs`:</span></span>

```csharp
builder.Services.AddHttpClient("{APP ASSEMBLY}.ServerAPI", client => 
    client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddScoped(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("{APP ASSEMBLY}.ServerAPI"));
```

<span data-ttu-id="6b9fb-247">Il segnaposto `{APP ASSEMBLY}` è il nome dell'assembly dell'app (ad esempio, `BlazorSample.ServerAPI` ).</span><span class="sxs-lookup"><span data-stu-id="6b9fb-247">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `BlazorSample.ServerAPI`).</span></span>

<span data-ttu-id="6b9fb-248">Il supporto per l'autenticazione degli utenti viene registrato nel contenitore del servizio con il <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> metodo di estensione fornito dal [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) pacchetto.</span><span class="sxs-lookup"><span data-stu-id="6b9fb-248">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> extension method provided by the [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package.</span></span> <span data-ttu-id="6b9fb-249">Questo metodo configura i servizi necessari per l'interazione dell'app con il Identity provider (IP).</span><span class="sxs-lookup"><span data-stu-id="6b9fb-249">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="6b9fb-250">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="6b9fb-250">`Program.cs`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAdB2C", options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="6b9fb-251">Il <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> metodo accetta un callback per configurare i parametri necessari per autenticare un'app.</span><span class="sxs-lookup"><span data-stu-id="6b9fb-251">The <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="6b9fb-252">Quando si registra l'app, è possibile ottenere i valori necessari per la configurazione dell'app dalla configurazione di AAD del portale di Azure.</span><span class="sxs-lookup"><span data-stu-id="6b9fb-252">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

<span data-ttu-id="6b9fb-253">La configurazione viene fornita dal `wwwroot/appsettings.json` file:</span><span class="sxs-lookup"><span data-stu-id="6b9fb-253">Configuration is supplied by the `wwwroot/appsettings.json` file:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "{AAD B2C INSTANCE}{TENANT DOMAIN}/{SIGN UP OR SIGN IN POLICY}",
    "ClientId": "{CLIENT APP CLIENT ID}",
    "ValidateAuthority": false
  }
}
```

<span data-ttu-id="6b9fb-254">Esempio:</span><span class="sxs-lookup"><span data-stu-id="6b9fb-254">Example:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "https://contoso.b2clogin.com/contoso.onmicrosoft.com/B2C_1_signupsignin1",
    "ClientId": "4369008b-21fa-427c-abaa-9b53bf58e538",
    "ValidateAuthority": false
  }
}
```

### <a name="access-token-scopes"></a><span data-ttu-id="6b9fb-255">Ambiti del token di accesso</span><span class="sxs-lookup"><span data-stu-id="6b9fb-255">Access token scopes</span></span>

<span data-ttu-id="6b9fb-256">Gli ambiti dei token di accesso predefiniti rappresentano l'elenco degli ambiti dei token di accesso:</span><span class="sxs-lookup"><span data-stu-id="6b9fb-256">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="6b9fb-257">Incluso per impostazione predefinita nella richiesta di accesso.</span><span class="sxs-lookup"><span data-stu-id="6b9fb-257">Included by default in the sign in request.</span></span>
* <span data-ttu-id="6b9fb-258">Utilizzato per eseguire il provisioning di un token di accesso immediatamente dopo l'autenticazione.</span><span class="sxs-lookup"><span data-stu-id="6b9fb-258">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="6b9fb-259">Tutti gli ambiti devono appartenere alla stessa app per ogni regola di Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="6b9fb-259">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="6b9fb-260">Per altre app per le API è possibile aggiungere altri ambiti, se necessario:</span><span class="sxs-lookup"><span data-stu-id="6b9fb-260">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="6b9fb-261">Il Blazor WebAssembly modello aggiunge automaticamente uno schema `api://` all'argomento Uri ID app passato nel `dotnet new` comando.</span><span class="sxs-lookup"><span data-stu-id="6b9fb-261">The Blazor WebAssembly template automatically adds a scheme of `api://` to the App ID URI argument passed in the `dotnet new` command.</span></span> <span data-ttu-id="6b9fb-262">Quando si genera un'app dal Blazor modello di progetto, verificare che il valore dell'ambito del token di accesso predefinito usi il valore URI dell'ID app personalizzato corretto specificato nell'portale di Azure o un valore con **uno** dei formati seguenti:</span><span class="sxs-lookup"><span data-stu-id="6b9fb-262">When generating an app from the Blazor project template, confirm that the value of the default access token scope uses either the correct custom App ID URI value that you provided in the Azure portal or a value with **one** of the following formats:</span></span>
>
> * <span data-ttu-id="6b9fb-263">Quando il dominio del server di pubblicazione della directory è **attendibile**, l'ambito del token di accesso predefinito è in genere un valore simile all'esempio seguente, dove `API.Access` è il nome dell'ambito predefinito:</span><span class="sxs-lookup"><span data-stu-id="6b9fb-263">When the publisher domain of the directory is **trusted**, the default access token scope is typically a value similar to the following example, where `API.Access` is the default scope name:</span></span>
>
>   ```csharp
>   options.ProviderOptions.DefaultAccessTokenScopes.Add(
>       "api://41451fa7-82d9-4673-8fa5-69eff5a761fd/API.Access");
>   ```
>
>   <span data-ttu-id="6b9fb-264">Esaminare il valore per uno schema doppio ( `api://api://...` ).</span><span class="sxs-lookup"><span data-stu-id="6b9fb-264">Inspect the value for a double scheme (`api://api://...`).</span></span> <span data-ttu-id="6b9fb-265">Se è presente uno schema doppio, rimuovere il primo `api://` schema dal valore.</span><span class="sxs-lookup"><span data-stu-id="6b9fb-265">If a double scheme is present, remove the first `api://` scheme from the value.</span></span>
>
> * <span data-ttu-id="6b9fb-266">Quando il dominio del server di pubblicazione della directory non è **attendibile**, l'ambito del token di accesso predefinito è in genere un valore simile all'esempio seguente, dove `API.Access` è il nome dell'ambito predefinito:</span><span class="sxs-lookup"><span data-stu-id="6b9fb-266">When the publisher domain of the directory is **untrusted**, the default access token scope is typically a value similar to the following example, where `API.Access` is the default scope name:</span></span>
>
>   ```csharp
>   options.ProviderOptions.DefaultAccessTokenScopes.Add(
>       "https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd/API.Access");
>   ```
>
>   <span data-ttu-id="6b9fb-267">Esaminare il valore per uno `api://` schema aggiuntivo ( `api://https://contoso.onmicrosoft.com/...` ).</span><span class="sxs-lookup"><span data-stu-id="6b9fb-267">Inspect the value for an extra `api://` scheme (`api://https://contoso.onmicrosoft.com/...`).</span></span> <span data-ttu-id="6b9fb-268">Se `api://` è presente uno schema aggiuntivo, rimuovere lo `api://` schema dal valore.</span><span class="sxs-lookup"><span data-stu-id="6b9fb-268">If an extra `api://` scheme is present, remove the `api://` scheme from the value.</span></span>
>
> <span data-ttu-id="6b9fb-269">Blazor WebAssemblyÈ possibile che il modello venga modificato in una versione futura di ASP.NET Core per risolvere questi scenari.</span><span class="sxs-lookup"><span data-stu-id="6b9fb-269">The Blazor WebAssembly template might be changed in a future release of ASP.NET Core to address these scenarios.</span></span> <span data-ttu-id="6b9fb-270">Per altre informazioni, vedere [schema doppio per URI ID app con il Blazor modello WASM (hosted, Single org) (DotNet/aspnetcore #27417)](https://github.com/dotnet/aspnetcore/issues/27417).</span><span class="sxs-lookup"><span data-stu-id="6b9fb-270">For more information, see [Double scheme for App ID URI with Blazor WASM template (hosted, single org) (dotnet/aspnetcore #27417)](https://github.com/dotnet/aspnetcore/issues/27417).</span></span>

<span data-ttu-id="6b9fb-271">Specificare gli ambiti aggiuntivi con `AdditionalScopesToConsent` :</span><span class="sxs-lookup"><span data-stu-id="6b9fb-271">Specify additional scopes with `AdditionalScopesToConsent`:</span></span>

```csharp
options.ProviderOptions.AdditionalScopesToConsent.Add("{ADDITIONAL SCOPE URI}");
```

<span data-ttu-id="6b9fb-272">Per ulteriori informazioni, vedere le sezioni seguenti dell'articolo *scenari aggiuntivi* :</span><span class="sxs-lookup"><span data-stu-id="6b9fb-272">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="6b9fb-273">Richiedere token di accesso aggiuntivi</span><span class="sxs-lookup"><span data-stu-id="6b9fb-273">Request additional access tokens</span></span>](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="6b9fb-274">Connetti token alle richieste in uscita</span><span class="sxs-lookup"><span data-stu-id="6b9fb-274">Attach tokens to outgoing requests</span></span>](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

::: moniker range=">= aspnetcore-5.0"

### <a name="login-mode"></a><span data-ttu-id="6b9fb-275">Modalità di accesso</span><span class="sxs-lookup"><span data-stu-id="6b9fb-275">Login mode</span></span>

[!INCLUDE[](~/blazor/includes/security/msal-login-mode.md)]

::: moniker-end

### <a name="imports-file"></a><span data-ttu-id="6b9fb-276">Importa file</span><span class="sxs-lookup"><span data-stu-id="6b9fb-276">Imports file</span></span>

[!INCLUDE[](~/blazor/includes/security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="6b9fb-277">Pagina di indice</span><span class="sxs-lookup"><span data-stu-id="6b9fb-277">Index page</span></span>

[!INCLUDE[](~/blazor/includes/security/index-page-msal.md)]

### <a name="app-component"></a><span data-ttu-id="6b9fb-278">Componente app</span><span class="sxs-lookup"><span data-stu-id="6b9fb-278">App component</span></span>

[!INCLUDE[](~/blazor/includes/security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="6b9fb-279">Componente RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="6b9fb-279">RedirectToLogin component</span></span>

[!INCLUDE[](~/blazor/includes/security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="6b9fb-280">Componente LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="6b9fb-280">LoginDisplay component</span></span>

[!INCLUDE[](~/blazor/includes/security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="6b9fb-281">Componente di autenticazione</span><span class="sxs-lookup"><span data-stu-id="6b9fb-281">Authentication component</span></span>

[!INCLUDE[](~/blazor/includes/security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="6b9fb-282">Componente FetchData</span><span class="sxs-lookup"><span data-stu-id="6b9fb-282">FetchData component</span></span>

[!INCLUDE[](~/blazor/includes/security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="6b9fb-283">Eseguire l'app</span><span class="sxs-lookup"><span data-stu-id="6b9fb-283">Run the app</span></span>

<span data-ttu-id="6b9fb-284">Eseguire l'app dal progetto server.</span><span class="sxs-lookup"><span data-stu-id="6b9fb-284">Run the app from the Server project.</span></span> <span data-ttu-id="6b9fb-285">Quando si usa Visual Studio, eseguire una delle operazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="6b9fb-285">When using Visual Studio, either:</span></span>

* <span data-ttu-id="6b9fb-286">Impostare l'elenco a discesa **progetti di avvio** nella barra degli strumenti sull'app per le *API server* e selezionare il pulsante **Esegui** .</span><span class="sxs-lookup"><span data-stu-id="6b9fb-286">Set the **Startup Projects** drop down list in the toolbar to the *Server API app* and select the **Run** button.</span></span>
* <span data-ttu-id="6b9fb-287">Selezionare il progetto server in **Esplora soluzioni** , quindi fare clic sul pulsante **Esegui** nella barra degli strumenti o avviare l'app dal menu **debug** .</span><span class="sxs-lookup"><span data-stu-id="6b9fb-287">Select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

<!-- HOLD
[!INCLUDE[](~/blazor/includes/security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/blazor/includes/security/wasm-aad-b2c-userflows.md)]

[!INCLUDE[](~/blazor/includes/security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="6b9fb-288">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="6b9fb-288">Additional resources</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="6b9fb-289">Compilare una versione personalizzata della libreria JavaScript Authentication. MSAL</span><span class="sxs-lookup"><span data-stu-id="6b9fb-289">Build a custom version of the Authentication.MSAL JavaScript library</span></span>](xref:blazor/security/webassembly/additional-scenarios#build-a-custom-version-of-the-authenticationmsal-javascript-library)
* [<span data-ttu-id="6b9fb-290">Richieste API Web non autenticate o non autorizzate in un'app con un client predefinito sicuro</span><span class="sxs-lookup"><span data-stu-id="6b9fb-290">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/authentication/azure-ad-b2c>
* [<span data-ttu-id="6b9fb-291">Esercitazione: Creare un tenant di Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="6b9fb-291">Tutorial: Create an Azure Active Directory B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)
* [<span data-ttu-id="6b9fb-292">Esercitazione: Registrare un'applicazione in Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="6b9fb-292">Tutorial: Register an application in Azure Active Directory B2C</span></span>](/azure/active-directory-b2c/tutorial-register-applications)
* [<span data-ttu-id="6b9fb-293">Documentazione di Microsoft Identity Platform</span><span class="sxs-lookup"><span data-stu-id="6b9fb-293">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
