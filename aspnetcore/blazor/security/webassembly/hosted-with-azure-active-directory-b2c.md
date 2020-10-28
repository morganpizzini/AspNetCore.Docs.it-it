---
title: "Proteggere un' :::no-loc(Blazor WebAssembly)::: app ospitata ASP.NET Core con Azure Active Directory B2C"
author: guardrex
description: "Informazioni su come proteggere un' :::no-loc(Blazor WebAssembly)::: app ospitata ASP.NET Core con Azure Active Directory B2C."
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/27/2020
no-loc:
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: blazor/security/webassembly/hosted-with-azure-active-directory-b2c
ms.openlocfilehash: 1a58e19ecaf816ddfb724b9a575d35c801cebd04
ms.sourcegitcommit: 2e3a967331b2c69f585dd61e9ad5c09763615b44
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92690562"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-hosted-app-with-azure-active-directory-b2c"></a><span data-ttu-id="64f5f-103">Proteggere un' :::no-loc(Blazor WebAssembly)::: app ospitata ASP.NET Core con Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="64f5f-103">Secure an ASP.NET Core :::no-loc(Blazor WebAssembly)::: hosted app with Azure Active Directory B2C</span></span>

<span data-ttu-id="64f5f-104">Di [Javier Calvarro Nelson](https://github.com/javiercn) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="64f5f-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="64f5f-105">Questo articolo descrive come creare un' [ :::no-loc(Blazor WebAssembly)::: app ospitata](xref:blazor/hosting-models#blazor-webassembly) che usa [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) per l'autenticazione.</span><span class="sxs-lookup"><span data-stu-id="64f5f-105">This article describes how to create a [hosted :::no-loc(Blazor WebAssembly)::: app](xref:blazor/hosting-models#blazor-webassembly) that uses [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) for authentication.</span></span>

## <a name="register-apps-in-aad-b2c-and-create-solution"></a><span data-ttu-id="64f5f-106">Registrare le app in AAD B2C e creare la soluzione</span><span class="sxs-lookup"><span data-stu-id="64f5f-106">Register apps in AAD B2C and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="64f5f-107">Creare un tenant</span><span class="sxs-lookup"><span data-stu-id="64f5f-107">Create a tenant</span></span>

<span data-ttu-id="64f5f-108">Seguire le istruzioni riportate in [esercitazione: creare un tenant di Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-create-tenant) per creare un tenant di AAD B2C.</span><span class="sxs-lookup"><span data-stu-id="64f5f-108">Follow the guidance in [Tutorial: Create an Azure Active Directory B2C tenant](/azure/active-directory-b2c/tutorial-create-tenant) to create an AAD B2C tenant.</span></span>

<span data-ttu-id="64f5f-109">Registrare l'istanza di AAD B2C, ad esempio, `https://contoso.b2clogin.com/` che include la barra finale.</span><span class="sxs-lookup"><span data-stu-id="64f5f-109">Record the AAD B2C instance (for example, `https://contoso.b2clogin.com/`, which includes the trailing slash).</span></span> <span data-ttu-id="64f5f-110">L'istanza è lo schema e l'host di una registrazione di app di Azure B2C, che è possibile trovare aprendo la finestra **endpoint** dalla pagina **registrazioni app** nella portale di Azure.</span><span class="sxs-lookup"><span data-stu-id="64f5f-110">The instance is the scheme and host of an Azure B2C app registration, which can be found by opening the **Endpoints** window from the **App registrations** page in the Azure portal.</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="64f5f-111">Registrare un'app per le API server</span><span class="sxs-lookup"><span data-stu-id="64f5f-111">Register a server API app</span></span>

<span data-ttu-id="64f5f-112">Seguire le istruzioni riportate in [esercitazione: registrare un'applicazione in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) per registrare un'app AAD per l'app per le *API del server* e quindi eseguire le operazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="64f5f-112">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) to register an AAD app for the *Server API app* and then do the following:</span></span>

1. <span data-ttu-id="64f5f-113">In **Azure Active Directory**  >  **registrazioni app** selezionare **nuova registrazione** .</span><span class="sxs-lookup"><span data-stu-id="64f5f-113">In **Azure Active Directory** > **App registrations** , select **New registration** .</span></span>
1. <span data-ttu-id="64f5f-114">Specificare un **nome** per l'app, ad esempio **:::no-loc(Blazor Server)::: AAD B2C** .</span><span class="sxs-lookup"><span data-stu-id="64f5f-114">Provide a **Name** for the app (for example, **:::no-loc(Blazor Server)::: AAD B2C** ).</span></span>
1. <span data-ttu-id="64f5f-115">Per i **tipi di account supportati** , selezionare l'opzione multi-tenant: **account in qualsiasi provider di identità o directory organizzativa (per l'autenticazione degli utenti con flussi utente)**</span><span class="sxs-lookup"><span data-stu-id="64f5f-115">For **Supported account types** , select the multi-tenant option: **Accounts in any identity provider or organizational directory (for authenticating users with user flows)**</span></span>
1. <span data-ttu-id="64f5f-116">L' *app* per le API del server non richiede un **URI di reindirizzamento** in questo scenario, quindi lasciare l'elenco a discesa impostato su **Web** e non immettere un URI di reindirizzamento.</span><span class="sxs-lookup"><span data-stu-id="64f5f-116">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="64f5f-117">Verificare che **Permissions**  >  **le autorizzazioni concedano il consenso dell'amministratore per OpenID e offline_access autorizzazioni** siano selezionate.</span><span class="sxs-lookup"><span data-stu-id="64f5f-117">Confirm that **Permissions** > **Grant admin consent to openid and offline_access permissions** is selected.</span></span>
1. <span data-ttu-id="64f5f-118">Selezionare **Registra** .</span><span class="sxs-lookup"><span data-stu-id="64f5f-118">Select **Register** .</span></span>

<span data-ttu-id="64f5f-119">Registrare le seguenti informazioni:</span><span class="sxs-lookup"><span data-stu-id="64f5f-119">Record the following information:</span></span>

* <span data-ttu-id="64f5f-120">*App per le API server* ID applicazione (client) (ad esempio, `41451fa7-82d9-4673-8fa5-69eff5a761fd` )</span><span class="sxs-lookup"><span data-stu-id="64f5f-120">*Server API app* Application (client) ID (for example, `41451fa7-82d9-4673-8fa5-69eff5a761fd`)</span></span>
* <span data-ttu-id="64f5f-121">Dominio primario/editore/tenant di AAD (ad esempio, `contoso.onmicrosoft.com` ): il dominio è disponibile come **dominio del server di pubblicazione** nel pannello **personalizzazione** del portale di Azure per l'app registrata.</span><span class="sxs-lookup"><span data-stu-id="64f5f-121">AAD Primary/Publisher/Tenant domain (for example, `contoso.onmicrosoft.com`): The domain is available as the **Publisher domain** in the **Branding** blade of the Azure portal for the registered app.</span></span>

<span data-ttu-id="64f5f-122">In **esporre un'API** :</span><span class="sxs-lookup"><span data-stu-id="64f5f-122">In **Expose an API** :</span></span>

1. <span data-ttu-id="64f5f-123">Selezionare **Aggiungi un ambito** .</span><span class="sxs-lookup"><span data-stu-id="64f5f-123">Select **Add a scope** .</span></span>
1. <span data-ttu-id="64f5f-124">Fai clic su **Salva e continua** .</span><span class="sxs-lookup"><span data-stu-id="64f5f-124">Select **Save and continue** .</span></span>
1. <span data-ttu-id="64f5f-125">Specificare un **nome** per l'ambito (ad esempio, `API.Access` ).</span><span class="sxs-lookup"><span data-stu-id="64f5f-125">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="64f5f-126">Fornire un **nome visualizzato** per il consenso dell'amministratore, ad esempio `Access API` .</span><span class="sxs-lookup"><span data-stu-id="64f5f-126">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="64f5f-127">Fornire una **Descrizione del consenso dell'amministratore** , ad esempio `Allows the app to access server app API endpoints.` .</span><span class="sxs-lookup"><span data-stu-id="64f5f-127">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="64f5f-128">Verificare che lo **stato** sia impostato su **abilitato** .</span><span class="sxs-lookup"><span data-stu-id="64f5f-128">Confirm that the **State** is set to **Enabled** .</span></span>
1. <span data-ttu-id="64f5f-129">Selezionare **Aggiungi ambito** .</span><span class="sxs-lookup"><span data-stu-id="64f5f-129">Select **Add scope** .</span></span>

<span data-ttu-id="64f5f-130">Registrare le seguenti informazioni:</span><span class="sxs-lookup"><span data-stu-id="64f5f-130">Record the following information:</span></span>

* <span data-ttu-id="64f5f-131">URI ID app (ad esempio, `api://41451fa7-82d9-4673-8fa5-69eff5a761fd` , `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd` o il valore personalizzato specificato)</span><span class="sxs-lookup"><span data-stu-id="64f5f-131">App ID URI (for example, `api://41451fa7-82d9-4673-8fa5-69eff5a761fd`, `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd`, or the custom value that you provided)</span></span>
* <span data-ttu-id="64f5f-132">Nome ambito (ad esempio, `API.Access` )</span><span class="sxs-lookup"><span data-stu-id="64f5f-132">Scope name (for example, `API.Access`)</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="64f5f-133">Registrare un'app client</span><span class="sxs-lookup"><span data-stu-id="64f5f-133">Register a client app</span></span>

<span data-ttu-id="64f5f-134">Seguire le istruzioni riportate in [esercitazione: registrare un'applicazione in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) di nuovo per registrare un'app AAD per l' *`Client`* app e quindi eseguire le operazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="64f5f-134">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) again to register an AAD app for the *`Client`* app and then do the following:</span></span>

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="64f5f-135">In **Azure Active Directory** > **registrazioni app** selezionare **nuova registrazione** .</span><span class="sxs-lookup"><span data-stu-id="64f5f-135">In **Azure Active Directory** > **App registrations** , select **New registration** .</span></span>
1. <span data-ttu-id="64f5f-136">Specificare un **nome** per l'app, ad esempio **:::no-loc(Blazor)::: AAD B2C client** .</span><span class="sxs-lookup"><span data-stu-id="64f5f-136">Provide a **Name** for the app (for example, **:::no-loc(Blazor)::: Client AAD B2C** ).</span></span>
1. <span data-ttu-id="64f5f-137">Per i **tipi di account supportati** , selezionare l'opzione multi-tenant: **account in qualsiasi provider di identità o directory organizzativa (per l'autenticazione degli utenti con flussi utente)**</span><span class="sxs-lookup"><span data-stu-id="64f5f-137">For **Supported account types** , select the multi-tenant option: **Accounts in any identity provider or organizational directory (for authenticating users with user flows)**</span></span>
1. <span data-ttu-id="64f5f-138">Impostare l'elenco a discesa **URI di reindirizzamento** sull' **applicazione a pagina singola (Spa)** e specificare l'URI di reindirizzamento seguente: `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="64f5f-138">Set the **Redirect URI** drop down to **Single-page application (SPA)** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="64f5f-139">La porta predefinita per un'app in esecuzione in Kestrel è la 5001.</span><span class="sxs-lookup"><span data-stu-id="64f5f-139">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="64f5f-140">Se l'app viene eseguita su una porta di Gheppio diversa, usare la porta dell'app.</span><span class="sxs-lookup"><span data-stu-id="64f5f-140">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="64f5f-141">Per IIS Express, la porta generata in modo casuale per l'app si trova nelle *`Server`* proprietà dell'app nel pannello **debug** .</span><span class="sxs-lookup"><span data-stu-id="64f5f-141">For IIS Express, the randomly generated port for the app can be found in the *`Server`* app's properties in the **Debug** panel.</span></span> <span data-ttu-id="64f5f-142">Poiché l'app non esiste in questo momento e la porta IIS Express non è nota, tornare a questo passaggio dopo la creazione dell'app e aggiornare l'URI di reindirizzamento.</span><span class="sxs-lookup"><span data-stu-id="64f5f-142">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="64f5f-143">Un contrassegno viene visualizzato nella sezione [creare l'app](#create-the-app) per ricordare IIS Express agli utenti di aggiornare l'URI di reindirizzamento.</span><span class="sxs-lookup"><span data-stu-id="64f5f-143">A remark appears in the [Create the app](#create-the-app) section to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="64f5f-144">Verificare che **Permissions** > **le autorizzazioni concedano il consenso dell'amministratore per OpenID e offline_access autorizzazioni** siano selezionate.</span><span class="sxs-lookup"><span data-stu-id="64f5f-144">Confirm that **Permissions** > **Grant admin consent to openid and offline_access permissions** is selected.</span></span>
1. <span data-ttu-id="64f5f-145">Selezionare **Registra** .</span><span class="sxs-lookup"><span data-stu-id="64f5f-145">Select **Register** .</span></span>

1. <span data-ttu-id="64f5f-146">Registrare l'ID dell'applicazione (client) (ad esempio, `4369008b-21fa-427c-abaa-9b53bf58e538` ).</span><span class="sxs-lookup"><span data-stu-id="64f5f-146">Record the Application (client) ID (for example, `4369008b-21fa-427c-abaa-9b53bf58e538`).</span></span>

<span data-ttu-id="64f5f-147">Nelle configurazioni della piattaforma di **autenticazione** > **Platform configurations** > **applicazione a pagina singola (Spa)** :</span><span class="sxs-lookup"><span data-stu-id="64f5f-147">In **Authentication** > **Platform configurations** > **Single-page application (SPA)** :</span></span>

1. <span data-ttu-id="64f5f-148">Verificare che l' **URI di reindirizzamento** di `https://localhost:{PORT}/authentication/login-callback` sia presente.</span><span class="sxs-lookup"><span data-stu-id="64f5f-148">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="64f5f-149">Per **concessione implicita** , assicurarsi che le caselle di controllo per i **token di accesso** e i **token ID** **non** siano selezionate.</span><span class="sxs-lookup"><span data-stu-id="64f5f-149">For **Implicit grant** , ensure that the check boxes for **Access tokens** and **ID tokens** are **not** selected.</span></span>
1. <span data-ttu-id="64f5f-150">Per questa esperienza sono accettabili le impostazioni predefinite rimanenti per l'app.</span><span class="sxs-lookup"><span data-stu-id="64f5f-150">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="64f5f-151">Fare clic sul pulsante **Salva** .</span><span class="sxs-lookup"><span data-stu-id="64f5f-151">Select the **Save** button.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="64f5f-152">In **Azure Active Directory** > **registrazioni app** selezionare **nuova registrazione** .</span><span class="sxs-lookup"><span data-stu-id="64f5f-152">In **Azure Active Directory** > **App registrations** , select **New registration** .</span></span>
1. <span data-ttu-id="64f5f-153">Specificare un **nome** per l'app, ad esempio **:::no-loc(Blazor)::: AAD B2C client** .</span><span class="sxs-lookup"><span data-stu-id="64f5f-153">Provide a **Name** for the app (for example, **:::no-loc(Blazor)::: Client AAD B2C** ).</span></span>
1. <span data-ttu-id="64f5f-154">Per i **tipi di account supportati** , selezionare l'opzione multi-tenant: **account in qualsiasi provider di identità o directory organizzativa (per l'autenticazione degli utenti con flussi utente)**</span><span class="sxs-lookup"><span data-stu-id="64f5f-154">For **Supported account types** , select the multi-tenant option: **Accounts in any identity provider or organizational directory (for authenticating users with user flows)**</span></span>
1. <span data-ttu-id="64f5f-155">Lasciare l'elenco a discesa **URI di reindirizzamento** impostato su **Web** e specificare l'URI di reindirizzamento seguente: `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="64f5f-155">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="64f5f-156">La porta predefinita per un'app in esecuzione in Kestrel è la 5001.</span><span class="sxs-lookup"><span data-stu-id="64f5f-156">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="64f5f-157">Se l'app viene eseguita su una porta di Gheppio diversa, usare la porta dell'app.</span><span class="sxs-lookup"><span data-stu-id="64f5f-157">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="64f5f-158">Per IIS Express, la porta generata in modo casuale per l'app si trova nelle *`Server`* proprietà dell'app nel pannello **debug** .</span><span class="sxs-lookup"><span data-stu-id="64f5f-158">For IIS Express, the randomly generated port for the app can be found in the *`Server`* app's properties in the **Debug** panel.</span></span> <span data-ttu-id="64f5f-159">Poiché l'app non esiste in questo momento e la porta IIS Express non è nota, tornare a questo passaggio dopo la creazione dell'app e aggiornare l'URI di reindirizzamento.</span><span class="sxs-lookup"><span data-stu-id="64f5f-159">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="64f5f-160">Un contrassegno viene visualizzato nella sezione [creare l'app](#create-the-app) per ricordare IIS Express agli utenti di aggiornare l'URI di reindirizzamento.</span><span class="sxs-lookup"><span data-stu-id="64f5f-160">A remark appears in the [Create the app](#create-the-app) section to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="64f5f-161">Verificare che **Permissions** > **le autorizzazioni concedano il consenso dell'amministratore per OpenID e offline_access autorizzazioni** siano selezionate.</span><span class="sxs-lookup"><span data-stu-id="64f5f-161">Confirm that **Permissions** > **Grant admin consent to openid and offline_access permissions** is selected.</span></span>
1. <span data-ttu-id="64f5f-162">Selezionare **Registra** .</span><span class="sxs-lookup"><span data-stu-id="64f5f-162">Select **Register** .</span></span>

<span data-ttu-id="64f5f-163">Registrare l'ID dell'applicazione (client) (ad esempio, `4369008b-21fa-427c-abaa-9b53bf58e538` ).</span><span class="sxs-lookup"><span data-stu-id="64f5f-163">Record the Application (client) ID (for example, `4369008b-21fa-427c-abaa-9b53bf58e538`).</span></span>

<span data-ttu-id="64f5f-164">In **Authentication** > **configurazioni piattaforma** di autenticazione > **Web** :</span><span class="sxs-lookup"><span data-stu-id="64f5f-164">In **Authentication** > **Platform configurations** > **Web** :</span></span>

1. <span data-ttu-id="64f5f-165">Verificare che l' **URI di reindirizzamento** di `https://localhost:{PORT}/authentication/login-callback` sia presente.</span><span class="sxs-lookup"><span data-stu-id="64f5f-165">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="64f5f-166">Per **concessione implicita** , selezionare le caselle di controllo per i token di **accesso** e i **token ID** .</span><span class="sxs-lookup"><span data-stu-id="64f5f-166">For **Implicit grant** , select the check boxes for **Access tokens** and **ID tokens** .</span></span>
1. <span data-ttu-id="64f5f-167">Per questa esperienza sono accettabili le impostazioni predefinite rimanenti per l'app.</span><span class="sxs-lookup"><span data-stu-id="64f5f-167">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="64f5f-168">Fare clic sul pulsante **Salva** .</span><span class="sxs-lookup"><span data-stu-id="64f5f-168">Select the **Save** button.</span></span>

::: moniker-end

<span data-ttu-id="64f5f-169">In **autorizzazioni API** :</span><span class="sxs-lookup"><span data-stu-id="64f5f-169">In **API permissions** :</span></span>

1. <span data-ttu-id="64f5f-170">Selezionare **Aggiungi un'autorizzazione** seguita da **API personali** .</span><span class="sxs-lookup"><span data-stu-id="64f5f-170">Select **Add a permission** followed by **My APIs** .</span></span>
1. <span data-ttu-id="64f5f-171">Selezionare l' *app per le API server* dalla colonna **nome** , ad esempio **:::no-loc(Blazor Server)::: AAD B2C** .</span><span class="sxs-lookup"><span data-stu-id="64f5f-171">Select the *Server API app* from the **Name** column (for example, **:::no-loc(Blazor Server)::: AAD B2C** ).</span></span>
1. <span data-ttu-id="64f5f-172">Aprire l'elenco di **API** .</span><span class="sxs-lookup"><span data-stu-id="64f5f-172">Open the **API** list.</span></span>
1. <span data-ttu-id="64f5f-173">Abilitare l'accesso all'API (ad esempio, `API.Access` ).</span><span class="sxs-lookup"><span data-stu-id="64f5f-173">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="64f5f-174">Selezionare **Aggiungi autorizzazioni** .</span><span class="sxs-lookup"><span data-stu-id="64f5f-174">Select **Add permissions** .</span></span>
1. <span data-ttu-id="64f5f-175">Selezionare il pulsante **concedere il consenso dell'amministratore per {tenant Name}** .</span><span class="sxs-lookup"><span data-stu-id="64f5f-175">Select the **Grant admin consent for {TENANT NAME}** button.</span></span> <span data-ttu-id="64f5f-176">Selezionare **Sì** per confermare.</span><span class="sxs-lookup"><span data-stu-id="64f5f-176">Select **Yes** to confirm.</span></span>

<span data-ttu-id="64f5f-177">In **Home**  >  **Azure ad B2C**  >  **flussi utente** :</span><span class="sxs-lookup"><span data-stu-id="64f5f-177">In **Home** > **Azure AD B2C** > **User flows** :</span></span>

[<span data-ttu-id="64f5f-178">Creare un flusso utente di iscrizione e accesso</span><span class="sxs-lookup"><span data-stu-id="64f5f-178">Create a sign-up and sign-in user flow</span></span>](/azure/active-directory-b2c/tutorial-create-user-flows)

<span data-ttu-id="64f5f-179">Selezionare almeno l'attributo utente nome visualizzato dell' **applicazione Claims**  >  **Display Name** per popolare `context.User.:::no-loc(Identity):::.Name` nel `LoginDisplay` componente ( `Shared/LoginDisplay.razor` ).</span><span class="sxs-lookup"><span data-stu-id="64f5f-179">At a minimum, select the **Application claims** > **Display Name** user attribute to populate the `context.User.:::no-loc(Identity):::.Name` in the `LoginDisplay` component (`Shared/LoginDisplay.razor`).</span></span>

<span data-ttu-id="64f5f-180">Registrare il nome del flusso utente di iscrizione e accesso creato per l'app (ad esempio, `B2C_1_signupsignin` ).</span><span class="sxs-lookup"><span data-stu-id="64f5f-180">Record the sign-up and sign-in user flow name created for the app (for example, `B2C_1_signupsignin`).</span></span>

### <a name="create-the-app"></a><span data-ttu-id="64f5f-181">Creare l'app</span><span class="sxs-lookup"><span data-stu-id="64f5f-181">Create the app</span></span>

<span data-ttu-id="64f5f-182">Sostituire i segnaposto nel comando seguente con le informazioni registrate in precedenza ed eseguire il comando in una shell dei comandi:</span><span class="sxs-lookup"><span data-stu-id="64f5f-182">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{TENANT DOMAIN}" -ho -o {APP NAME} -ssp "{SIGN UP OR SIGN IN POLICY}"
```

| <span data-ttu-id="64f5f-183">Segnaposto</span><span class="sxs-lookup"><span data-stu-id="64f5f-183">Placeholder</span></span>                   | <span data-ttu-id="64f5f-184">Nome portale di Azure</span><span class="sxs-lookup"><span data-stu-id="64f5f-184">Azure portal name</span></span>                                     | <span data-ttu-id="64f5f-185">Esempio</span><span class="sxs-lookup"><span data-stu-id="64f5f-185">Example</span></span>                                      |
| ----------------------------- | ----------------------------------------------------- | -------------------------------------------- |
| `{AAD B2C INSTANCE}`          | <span data-ttu-id="64f5f-186">Istanza</span><span class="sxs-lookup"><span data-stu-id="64f5f-186">Instance</span></span>                                              | `https://contoso.b2clogin.com/`              |
| `{APP NAME}`                  | &mdash;                                               | `:::no-loc(Blazor):::Sample`                               |
| `{CLIENT APP CLIENT ID}`      | <span data-ttu-id="64f5f-187">ID applicazione (client) per l' *`Client`* app</span><span class="sxs-lookup"><span data-stu-id="64f5f-187">Application (client) ID for the *`Client`* app</span></span>        | `4369008b-21fa-427c-abaa-9b53bf58e538`       |
| `{DEFAULT SCOPE}`             | <span data-ttu-id="64f5f-188">Nome ambito</span><span class="sxs-lookup"><span data-stu-id="64f5f-188">Scope name</span></span>                                            | `API.Access`                                 |
| `{SERVER API APP CLIENT ID}`  | <span data-ttu-id="64f5f-189">ID applicazione (client) per l' *app per le API server*</span><span class="sxs-lookup"><span data-stu-id="64f5f-189">Application (client) ID for the *Server API app*</span></span>      | `41451fa7-82d9-4673-8fa5-69eff5a761fd`       |
| `{SERVER API APP ID URI}`     | <span data-ttu-id="64f5f-190">URI dell'ID applicazione</span><span class="sxs-lookup"><span data-stu-id="64f5f-190">Application ID URI</span></span>                                    | `api://41451fa7-82d9-4673-8fa5-69eff5a761fd` |
| `{SIGN UP OR SIGN IN POLICY}` | <span data-ttu-id="64f5f-191">Flusso utente di iscrizione/accesso</span><span class="sxs-lookup"><span data-stu-id="64f5f-191">Sign-up/sign-in user flow</span></span>                             | `B2C_1_signupsignin1`                        |
| `{TENANT DOMAIN}`             | <span data-ttu-id="64f5f-192">Dominio primario/server di pubblicazione/tenant</span><span class="sxs-lookup"><span data-stu-id="64f5f-192">Primary/Publisher/Tenant domain</span></span>                       | `contoso.onmicrosoft.com`                    |

<span data-ttu-id="64f5f-193">Il percorso di output specificato con l'opzione `-o|--output` consente di creare una cartella di progetto se non esiste e viene incluso nel nome dell'app.</span><span class="sxs-lookup"><span data-stu-id="64f5f-193">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

> [!NOTE]
> <span data-ttu-id="64f5f-194">L'ambito impostato dal :::no-loc(Blazor)::: modello ospitato potrebbe avere ripetutamente l'host Uri ID app.</span><span class="sxs-lookup"><span data-stu-id="64f5f-194">The scope set up by the Hosted :::no-loc(Blazor)::: template might have the App ID URI host repeated.</span></span> <span data-ttu-id="64f5f-195">Verificare che l'ambito configurato per la `DefaultAccessTokenScopes` raccolta sia corretto in `Program.Main` ( `Program.cs` ) dell' *`Client`* app.</span><span class="sxs-lookup"><span data-stu-id="64f5f-195">Confirm that the scope configured for the `DefaultAccessTokenScopes` collection is correct in `Program.Main` (`Program.cs`) of the *`Client`* app.</span></span>

> [!NOTE]
> <span data-ttu-id="64f5f-196">Nell'portale di Azure l' *`Client`* **URI di reindirizzamento** della configurazione della piattaforma dell'app è configurato per la porta 5001 per le app eseguite nel server gheppio con le impostazioni predefinite.</span><span class="sxs-lookup"><span data-stu-id="64f5f-196">In the Azure portal, the *`Client`* app's platform configuration **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="64f5f-197">Se l' *`Client`* app viene eseguita su una porta IIS Express casuale, la porta per l'app si trova nelle proprietà dell' *app* per le API del server nel pannello **debug** .</span><span class="sxs-lookup"><span data-stu-id="64f5f-197">If the *`Client`* app is run on a random IIS Express port, the port for the app can be found in the *Server API app's* properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="64f5f-198">Se la porta non è stata configurata in precedenza con la *`Client`* porta nota dell'app, tornare alla *`Client`* registrazione dell'app nel portale di Azure e aggiornare l'URI di reindirizzamento con la porta corretta.</span><span class="sxs-lookup"><span data-stu-id="64f5f-198">If the port wasn't configured earlier with the *`Client`* app's known port, return to the *`Client`* app's registration in the Azure portal and update the redirect URI with the correct port.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="64f5f-199">*`Server`* configurazione dell'app</span><span class="sxs-lookup"><span data-stu-id="64f5f-199">*`Server`* app configuration</span></span>

<span data-ttu-id="64f5f-200">*Questa sezione è relativa all'app della soluzione **`Server`** .*</span><span class="sxs-lookup"><span data-stu-id="64f5f-200">*This section pertains to the solution's **`Server`** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="64f5f-201">Pacchetto di autenticazione</span><span class="sxs-lookup"><span data-stu-id="64f5f-201">Authentication package</span></span>

<span data-ttu-id="64f5f-202">Il pacchetto fornisce il supporto per l'autenticazione e l'autorizzazione delle chiamate a ASP.NET Core API Web [`Microsoft.AspNetCore.Authentication.AzureADB2C.UI`](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureADB2C.UI) :</span><span class="sxs-lookup"><span data-stu-id="64f5f-202">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the [`Microsoft.AspNetCore.Authentication.AzureADB2C.UI`](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureADB2C.UI) package:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureADB2C.UI" 
  Version="{VERSION}" />
```

<span data-ttu-id="64f5f-203">Per il segnaposto `{VERSION}` , la versione stabile più recente del pacchetto che corrisponde alla versione del Framework condiviso dell'app è disponibile nella cronologia delle **versioni** del pacchetto in [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI).</span><span class="sxs-lookup"><span data-stu-id="64f5f-203">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI).</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="64f5f-204">Supporto del servizio di autenticazione</span><span class="sxs-lookup"><span data-stu-id="64f5f-204">Authentication service support</span></span>

<span data-ttu-id="64f5f-205">Il `AddAuthentication` metodo imposta i servizi di autenticazione all'interno dell'app e configura il gestore di JWT Bearer come metodo di autenticazione predefinito.</span><span class="sxs-lookup"><span data-stu-id="64f5f-205">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="64f5f-206">Il <xref:Microsoft.AspNetCore.Authentication.AzureADB2CAuthenticationBuilderExtensions.AddAzureADB2CBearer%2A> metodo imposta i parametri specifici nel gestore di JWT Bearer necessario per convalidare i token emessi dal Azure Active Directory B2C:</span><span class="sxs-lookup"><span data-stu-id="64f5f-206">The <xref:Microsoft.AspNetCore.Authentication.AzureADB2CAuthenticationBuilderExtensions.AddAzureADB2CBearer%2A> method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory B2C:</span></span>

```csharp
services.AddAuthentication(AzureADB2CDefaults.BearerAuthenticationScheme)
    .AddAzureADB2CBearer(options => Configuration.Bind("AzureAdB2C", options));
```

<span data-ttu-id="64f5f-207"><xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> e <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> verificare che:</span><span class="sxs-lookup"><span data-stu-id="64f5f-207"><xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> and <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> ensure that:</span></span>

* <span data-ttu-id="64f5f-208">L'app tenta di analizzare e convalidare i token nelle richieste in ingresso.</span><span class="sxs-lookup"><span data-stu-id="64f5f-208">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="64f5f-209">Eventuali richieste che tentano di accedere a una risorsa protetta senza credenziali appropriate hanno esito negativo.</span><span class="sxs-lookup"><span data-stu-id="64f5f-209">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="userno-locidentityname"></a><span data-ttu-id="64f5f-210">Utente. :::no-loc(Identity)::: . Nome</span><span class="sxs-lookup"><span data-stu-id="64f5f-210">User.:::no-loc(Identity):::.Name</span></span>

<span data-ttu-id="64f5f-211">Per impostazione predefinita, `User.:::no-loc(Identity):::.Name` non viene popolato.</span><span class="sxs-lookup"><span data-stu-id="64f5f-211">By default, the `User.:::no-loc(Identity):::.Name` isn't populated.</span></span>

<span data-ttu-id="64f5f-212">Per configurare l'app per la ricezione del valore dal `name` tipo di attestazione, configurare il <xref:Microsoft.:::no-loc(Identity):::Model.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType> di <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="64f5f-212">To configure the app to receive the value from the `name` claim type, configure the <xref:Microsoft.:::no-loc(Identity):::Model.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType> of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;

...

services.Configure<JwtBearerOptions>(
    AzureADB2CDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a><span data-ttu-id="64f5f-213">Impostazioni delle app</span><span class="sxs-lookup"><span data-stu-id="64f5f-213">App settings</span></span>

<span data-ttu-id="64f5f-214">Il `appsettings.json` file contiene le opzioni per configurare il gestore di connessione JWT usato per convalidare i token di accesso.</span><span class="sxs-lookup"><span data-stu-id="64f5f-214">The `appsettings.json` file contains the options to configure the JWT bearer handler used to validate access tokens.</span></span>

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

<span data-ttu-id="64f5f-215">Esempio:</span><span class="sxs-lookup"><span data-stu-id="64f5f-215">Example:</span></span>

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

### <a name="weatherforecast-controller"></a><span data-ttu-id="64f5f-216">Controller WeatherForecast</span><span class="sxs-lookup"><span data-stu-id="64f5f-216">WeatherForecast controller</span></span>

<span data-ttu-id="64f5f-217">Il controller WeatherForecast ( *Controllers/WeatherForecastController. cs* ) espone un'API protetta con l' [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attributo applicato al controller.</span><span class="sxs-lookup"><span data-stu-id="64f5f-217">The WeatherForecast controller ( *Controllers/WeatherForecastController.cs* ) exposes a protected API with the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute applied to the controller.</span></span> <span data-ttu-id="64f5f-218">È **importante** comprendere che:</span><span class="sxs-lookup"><span data-stu-id="64f5f-218">It's **important** to understand that:</span></span>

* <span data-ttu-id="64f5f-219">L' [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attributo in questo controller API è l'unico elemento che protegge questa API da accessi non autorizzati.</span><span class="sxs-lookup"><span data-stu-id="64f5f-219">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="64f5f-220">L' [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attributo usato nell' :::no-loc(Blazor WebAssembly)::: app funge solo da hint per l'app che l'utente deve essere autorizzato affinché l'app funzioni correttamente.</span><span class="sxs-lookup"><span data-stu-id="64f5f-220">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute used in the :::no-loc(Blazor WebAssembly)::: app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

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

## <a name="client-app-configuration"></a><span data-ttu-id="64f5f-221">*`Client`* configurazione dell'app</span><span class="sxs-lookup"><span data-stu-id="64f5f-221">*`Client`* app configuration</span></span>

<span data-ttu-id="64f5f-222">*Questa sezione è relativa all'app della soluzione **`Client`** .*</span><span class="sxs-lookup"><span data-stu-id="64f5f-222">*This section pertains to the solution's **`Client`** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="64f5f-223">Pacchetto di autenticazione</span><span class="sxs-lookup"><span data-stu-id="64f5f-223">Authentication package</span></span>

<span data-ttu-id="64f5f-224">Quando viene creata un'app per usare un singolo account B2C ( `IndividualB2C` ), l'app riceve automaticamente un riferimento al pacchetto per [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ( [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) ).</span><span class="sxs-lookup"><span data-stu-id="64f5f-224">When an app is created to use an Individual B2C Account (`IndividualB2C`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)).</span></span> <span data-ttu-id="64f5f-225">Il pacchetto fornisce un set di primitive che consentono all'app di autenticare gli utenti e ottenere i token per chiamare le API protette.</span><span class="sxs-lookup"><span data-stu-id="64f5f-225">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="64f5f-226">Se si aggiunge l'autenticazione a un'app, aggiungere manualmente il pacchetto al file di progetto dell'app:</span><span class="sxs-lookup"><span data-stu-id="64f5f-226">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="{VERSION}" />
```

<span data-ttu-id="64f5f-227">Per il segnaposto `{VERSION}` , la versione stabile più recente del pacchetto che corrisponde alla versione del Framework condiviso dell'app è disponibile nella cronologia delle **versioni** del pacchetto in [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal).</span><span class="sxs-lookup"><span data-stu-id="64f5f-227">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal).</span></span>

<span data-ttu-id="64f5f-228">Il [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) pacchetto aggiunge il [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) pacchetto all'app in modo transitivo.</span><span class="sxs-lookup"><span data-stu-id="64f5f-228">The [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package transitively adds the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="64f5f-229">Supporto del servizio di autenticazione</span><span class="sxs-lookup"><span data-stu-id="64f5f-229">Authentication service support</span></span>

<span data-ttu-id="64f5f-230"><xref:System.Net.Http.HttpClient>Viene aggiunto il supporto per le istanze di che includono token di accesso quando si effettuano richieste al progetto server.</span><span class="sxs-lookup"><span data-stu-id="64f5f-230">Support for <xref:System.Net.Http.HttpClient> instances is added that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="64f5f-231">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="64f5f-231">`Program.cs`:</span></span>

```csharp
builder.Services.AddHttpClient("{APP ASSEMBLY}.ServerAPI", client => 
    client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddScoped(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("{APP ASSEMBLY}.ServerAPI"));
```

<span data-ttu-id="64f5f-232">Il segnaposto `{APP ASSEMBLY}` è il nome dell'assembly dell'app (ad esempio, `:::no-loc(Blazor):::Sample.ServerAPI` ).</span><span class="sxs-lookup"><span data-stu-id="64f5f-232">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `:::no-loc(Blazor):::Sample.ServerAPI`).</span></span>

<span data-ttu-id="64f5f-233">Il supporto per l'autenticazione degli utenti viene registrato nel contenitore del servizio con il <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> metodo di estensione fornito dal [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) pacchetto.</span><span class="sxs-lookup"><span data-stu-id="64f5f-233">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> extension method provided by the [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package.</span></span> <span data-ttu-id="64f5f-234">Questo metodo configura i servizi necessari per l'interazione dell'app con il :::no-loc(Identity)::: provider (IP).</span><span class="sxs-lookup"><span data-stu-id="64f5f-234">This method sets up the services required for the app to interact with the :::no-loc(Identity)::: Provider (IP).</span></span>

<span data-ttu-id="64f5f-235">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="64f5f-235">`Program.cs`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAdB2C", options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="64f5f-236">Il <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> metodo accetta un callback per configurare i parametri necessari per autenticare un'app.</span><span class="sxs-lookup"><span data-stu-id="64f5f-236">The <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="64f5f-237">Quando si registra l'app, è possibile ottenere i valori necessari per la configurazione dell'app dalla configurazione di AAD del portale di Azure.</span><span class="sxs-lookup"><span data-stu-id="64f5f-237">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

<span data-ttu-id="64f5f-238">La configurazione viene fornita dal `wwwroot/appsettings.json` file:</span><span class="sxs-lookup"><span data-stu-id="64f5f-238">Configuration is supplied by the `wwwroot/appsettings.json` file:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "{AAD B2C INSTANCE}{TENANT DOMAIN}/{SIGN UP OR SIGN IN POLICY}",
    "ClientId": "{CLIENT APP CLIENT ID}",
    "ValidateAuthority": false
  }
}
```

<span data-ttu-id="64f5f-239">Esempio:</span><span class="sxs-lookup"><span data-stu-id="64f5f-239">Example:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "https://contoso.b2clogin.com/contoso.onmicrosoft.com/B2C_1_signupsignin1",
    "ClientId": "4369008b-21fa-427c-abaa-9b53bf58e538",
    "ValidateAuthority": false
  }
}
```

### <a name="access-token-scopes"></a><span data-ttu-id="64f5f-240">Ambiti del token di accesso</span><span class="sxs-lookup"><span data-stu-id="64f5f-240">Access token scopes</span></span>

<span data-ttu-id="64f5f-241">Gli ambiti dei token di accesso predefiniti rappresentano l'elenco degli ambiti dei token di accesso:</span><span class="sxs-lookup"><span data-stu-id="64f5f-241">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="64f5f-242">Incluso per impostazione predefinita nella richiesta di accesso.</span><span class="sxs-lookup"><span data-stu-id="64f5f-242">Included by default in the sign in request.</span></span>
* <span data-ttu-id="64f5f-243">Utilizzato per eseguire il provisioning di un token di accesso immediatamente dopo l'autenticazione.</span><span class="sxs-lookup"><span data-stu-id="64f5f-243">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="64f5f-244">Tutti gli ambiti devono appartenere alla stessa app per ogni regola di Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="64f5f-244">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="64f5f-245">Per altre app per le API è possibile aggiungere altri ambiti, se necessario:</span><span class="sxs-lookup"><span data-stu-id="64f5f-245">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="64f5f-246">Specificare gli ambiti aggiuntivi con `AdditionalScopesToConsent` :</span><span class="sxs-lookup"><span data-stu-id="64f5f-246">Specify additional scopes with `AdditionalScopesToConsent`:</span></span>

```csharp
options.ProviderOptions.AdditionalScopesToConsent.Add("{ADDITIONAL SCOPE URI}");
```

<span data-ttu-id="64f5f-247">Per ulteriori informazioni, vedere le sezioni seguenti dell'articolo *scenari aggiuntivi* :</span><span class="sxs-lookup"><span data-stu-id="64f5f-247">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="64f5f-248">Richiedere token di accesso aggiuntivi</span><span class="sxs-lookup"><span data-stu-id="64f5f-248">Request additional access tokens</span></span>](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="64f5f-249">Connetti token alle richieste in uscita</span><span class="sxs-lookup"><span data-stu-id="64f5f-249">Attach tokens to outgoing requests</span></span>](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

::: moniker range=">= aspnetcore-5.0"

### <a name="login-mode"></a><span data-ttu-id="64f5f-250">Modalità di accesso</span><span class="sxs-lookup"><span data-stu-id="64f5f-250">Login mode</span></span>

[!INCLUDE[](~/includes/blazor-security/msal-login-mode.md)]

::: moniker-end

### <a name="imports-file"></a><span data-ttu-id="64f5f-251">Importa file</span><span class="sxs-lookup"><span data-stu-id="64f5f-251">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="64f5f-252">Pagina di indice</span><span class="sxs-lookup"><span data-stu-id="64f5f-252">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a><span data-ttu-id="64f5f-253">Componente app</span><span class="sxs-lookup"><span data-stu-id="64f5f-253">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="64f5f-254">Componente RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="64f5f-254">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="64f5f-255">Componente LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="64f5f-255">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="64f5f-256">Componente di autenticazione</span><span class="sxs-lookup"><span data-stu-id="64f5f-256">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="64f5f-257">Componente FetchData</span><span class="sxs-lookup"><span data-stu-id="64f5f-257">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="64f5f-258">Eseguire l'app</span><span class="sxs-lookup"><span data-stu-id="64f5f-258">Run the app</span></span>

<span data-ttu-id="64f5f-259">Eseguire l'app dal progetto server.</span><span class="sxs-lookup"><span data-stu-id="64f5f-259">Run the app from the Server project.</span></span> <span data-ttu-id="64f5f-260">Quando si usa Visual Studio, eseguire una delle operazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="64f5f-260">When using Visual Studio, either:</span></span>

* <span data-ttu-id="64f5f-261">Impostare l'elenco a discesa **progetti di avvio** nella barra degli strumenti sull'app per le *API server* e selezionare il pulsante **Esegui** .</span><span class="sxs-lookup"><span data-stu-id="64f5f-261">Set the **Startup Projects** drop down list in the toolbar to the *Server API app* and select the **Run** button.</span></span>
* <span data-ttu-id="64f5f-262">Selezionare il progetto server in **Esplora soluzioni** , quindi fare clic sul pulsante **Esegui** nella barra degli strumenti o avviare l'app dal menu **debug** .</span><span class="sxs-lookup"><span data-stu-id="64f5f-262">Select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/wasm-aad-b2c-userflows.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="64f5f-263">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="64f5f-263">Additional resources</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="64f5f-264">Richieste API Web non autenticate o non autorizzate in un'app con un client predefinito sicuro</span><span class="sxs-lookup"><span data-stu-id="64f5f-264">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/authentication/azure-ad-b2c>
* [<span data-ttu-id="64f5f-265">Esercitazione: Creare un tenant di Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="64f5f-265">Tutorial: Create an Azure Active Directory B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)
* [<span data-ttu-id="64f5f-266">Documentazione di Microsoft Identity Platform</span><span class="sxs-lookup"><span data-stu-id="64f5f-266">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
