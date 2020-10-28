---
title: "Proteggere un' :::no-loc(Blazor WebAssembly)::: app ospitata ASP.NET Core con Azure Active Directory"
author: guardrex
description: "Informazioni su come proteggere un' :::no-loc(Blazor WebAssembly)::: app ospitata ASP.NET Core con Azure Active Directory."
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: devx-track-csharp, mvc
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
uid: blazor/security/webassembly/hosted-with-azure-active-directory
ms.openlocfilehash: cb1deb71723660964954c2faae4512b7df9b2ed4
ms.sourcegitcommit: 2e3a967331b2c69f585dd61e9ad5c09763615b44
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92690539"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-hosted-app-with-azure-active-directory"></a><span data-ttu-id="29cd5-103">Proteggere un' :::no-loc(Blazor WebAssembly)::: app ospitata ASP.NET Core con Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="29cd5-103">Secure an ASP.NET Core :::no-loc(Blazor WebAssembly)::: hosted app with Azure Active Directory</span></span>

<span data-ttu-id="29cd5-104">Di [Javier Calvarro Nelson](https://github.com/javiercn) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="29cd5-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="29cd5-105">Questo articolo descrive come creare un' [ :::no-loc(Blazor WebAssembly)::: app ospitata](xref:blazor/hosting-models#blazor-webassembly) che usa [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) per l'autenticazione.</span><span class="sxs-lookup"><span data-stu-id="29cd5-105">This article describes how to create a [hosted :::no-loc(Blazor WebAssembly)::: app](xref:blazor/hosting-models#blazor-webassembly) that uses [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) for authentication.</span></span>

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="29cd5-106">Per :::no-loc(Blazor WebAssembly)::: le app create in Visual Studio configurate per supportare gli account in una directory organizzativa di AAD, Visual Studio non configura correttamente l'app nella generazione del progetto.</span><span class="sxs-lookup"><span data-stu-id="29cd5-106">For :::no-loc(Blazor WebAssembly)::: apps created in Visual Studio that are configured to support accounts in an AAD organizational directory, Visual Studio doesn't configure the app correctly on project generation.</span></span> <span data-ttu-id="29cd5-107">Questa operazione verrà risolta in una versione futura di Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="29cd5-107">This will be addressed in a future release of Visual Studio.</span></span> <span data-ttu-id="29cd5-108">Questo articolo illustra come creare l'app con il `dotnet new` comando interfaccia della riga di comando di .NET Core.</span><span class="sxs-lookup"><span data-stu-id="29cd5-108">This article shows how to create the app with the .NET Core CLI's `dotnet new` command.</span></span> <span data-ttu-id="29cd5-109">Se si preferisce creare l'app con Visual Studio prima che l'IDE venga aggiornato per i modelli più recenti :::no-loc(Blazor)::: in ASP.NET Core 5,0, fare riferimento a ogni sezione di questo articolo e confermare o aggiornare la configurazione dell'app dopo che Visual Studio ha creato l'app.</span><span class="sxs-lookup"><span data-stu-id="29cd5-109">If you prefer to create the app with Visual Studio before the IDE is updated for the latest :::no-loc(Blazor)::: templates in ASP.NET Core 5.0, refer to each section of this article and confirm or update the app's configuration after Visual Studio creates the app.</span></span>

::: moniker-end

## <a name="register-apps-in-aad-and-create-solution"></a><span data-ttu-id="29cd5-110">Registrare le app in AAD e creare la soluzione</span><span class="sxs-lookup"><span data-stu-id="29cd5-110">Register apps in AAD and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="29cd5-111">Creare un tenant</span><span class="sxs-lookup"><span data-stu-id="29cd5-111">Create a tenant</span></span>

<span data-ttu-id="29cd5-112">Seguire le istruzioni riportate nella [Guida introduttiva: configurare un tenant](/azure/active-directory/develop/quickstart-create-new-tenant) per creare un tenant in AAD.</span><span class="sxs-lookup"><span data-stu-id="29cd5-112">Follow the guidance in [Quickstart: Set up a tenant](/azure/active-directory/develop/quickstart-create-new-tenant) to create a tenant in AAD.</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="29cd5-113">Registrare un'app per le API server</span><span class="sxs-lookup"><span data-stu-id="29cd5-113">Register a server API app</span></span>

<span data-ttu-id="29cd5-114">Seguire le istruzioni disponibili in [Guida introduttiva: registrare un'applicazione con la piattaforma di identità Microsoft](/azure/active-directory/develop/quickstart-register-app) e gli argomenti di Azure AAD successivi per registrare un'app AAD per l'app per le *API del server* e quindi eseguire le operazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="29cd5-114">Follow the guidance in [Quickstart: Register an application with the Microsoft identity platform](/azure/active-directory/develop/quickstart-register-app) and subsequent Azure AAD topics to register an AAD app for the *Server API app* and then do the following:</span></span>

1. <span data-ttu-id="29cd5-115">In **Azure Active Directory**  >  **registrazioni app** selezionare **nuova registrazione** .</span><span class="sxs-lookup"><span data-stu-id="29cd5-115">In **Azure Active Directory** > **App registrations** , select **New registration** .</span></span>
1. <span data-ttu-id="29cd5-116">Specificare un **nome** per l'app (ad esempio, **:::no-loc(Blazor Server)::: AAD** ).</span><span class="sxs-lookup"><span data-stu-id="29cd5-116">Provide a **Name** for the app (for example, **:::no-loc(Blazor Server)::: AAD** ).</span></span>
1. <span data-ttu-id="29cd5-117">Scegliere i **tipi di conto supportati** .</span><span class="sxs-lookup"><span data-stu-id="29cd5-117">Choose a **Supported account types** .</span></span> <span data-ttu-id="29cd5-118">Per questa esperienza è possibile selezionare **solo gli account in questa directory aziendale** (tenant singolo).</span><span class="sxs-lookup"><span data-stu-id="29cd5-118">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="29cd5-119">L' *app* per le API del server non richiede un **URI di reindirizzamento** in questo scenario, quindi lasciare l'elenco a discesa impostato su **Web** e non immettere un URI di reindirizzamento.</span><span class="sxs-lookup"><span data-stu-id="29cd5-119">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="29cd5-120">Deselezionare la **Permissions**  >  casella di controllo autorizzazioni **Concedi amministratore per OpenID e autorizzazioni offline_access** .</span><span class="sxs-lookup"><span data-stu-id="29cd5-120">Clear the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="29cd5-121">Selezionare **Registra** .</span><span class="sxs-lookup"><span data-stu-id="29cd5-121">Select **Register** .</span></span>

<span data-ttu-id="29cd5-122">Registrare le seguenti informazioni:</span><span class="sxs-lookup"><span data-stu-id="29cd5-122">Record the following information:</span></span>

* <span data-ttu-id="29cd5-123">*App per le API server* ID applicazione (client) (ad esempio, `41451fa7-82d9-4673-8fa5-69eff5a761fd` )</span><span class="sxs-lookup"><span data-stu-id="29cd5-123">*Server API app* Application (client) ID (for example, `41451fa7-82d9-4673-8fa5-69eff5a761fd`)</span></span>
* <span data-ttu-id="29cd5-124">ID directory (tenant) (ad esempio, `e86c78e2-8bb4-4c41-aefd-918e0565a45e` )</span><span class="sxs-lookup"><span data-stu-id="29cd5-124">Directory (tenant) ID (for example, `e86c78e2-8bb4-4c41-aefd-918e0565a45e`)</span></span>
* <span data-ttu-id="29cd5-125">Dominio primario/editore/tenant di AAD (ad esempio, `contoso.onmicrosoft.com` ): il dominio è disponibile come **dominio del server di pubblicazione** nel pannello **personalizzazione** del portale di Azure per l'app registrata.</span><span class="sxs-lookup"><span data-stu-id="29cd5-125">AAD Primary/Publisher/Tenant domain (for example, `contoso.onmicrosoft.com`): The domain is available as the **Publisher domain** in the **Branding** blade of the Azure portal for the registered app.</span></span>

<span data-ttu-id="29cd5-126">In **autorizzazioni API** rimuovere l'autorizzazione **Microsoft Graph**  >  **utente. Read** perché l'app non richiede l'accesso o il profilo utente.</span><span class="sxs-lookup"><span data-stu-id="29cd5-126">In **API permissions** , remove the **Microsoft Graph** > **User.Read** permission, as the app doesn't require sign in or user profile access.</span></span>

<span data-ttu-id="29cd5-127">In **esporre un'API** :</span><span class="sxs-lookup"><span data-stu-id="29cd5-127">In **Expose an API** :</span></span>

1. <span data-ttu-id="29cd5-128">Selezionare **Aggiungi un ambito** .</span><span class="sxs-lookup"><span data-stu-id="29cd5-128">Select **Add a scope** .</span></span>
1. <span data-ttu-id="29cd5-129">Fai clic su **Salva e continua** .</span><span class="sxs-lookup"><span data-stu-id="29cd5-129">Select **Save and continue** .</span></span>
1. <span data-ttu-id="29cd5-130">Specificare un **nome** per l'ambito (ad esempio, `API.Access` ).</span><span class="sxs-lookup"><span data-stu-id="29cd5-130">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="29cd5-131">Fornire un **nome visualizzato** per il consenso dell'amministratore, ad esempio `Access API` .</span><span class="sxs-lookup"><span data-stu-id="29cd5-131">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="29cd5-132">Fornire una **Descrizione del consenso dell'amministratore** , ad esempio `Allows the app to access server app API endpoints.` .</span><span class="sxs-lookup"><span data-stu-id="29cd5-132">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="29cd5-133">Verificare che lo **stato** sia impostato su **abilitato** .</span><span class="sxs-lookup"><span data-stu-id="29cd5-133">Confirm that the **State** is set to **Enabled** .</span></span>
1. <span data-ttu-id="29cd5-134">Selezionare **Aggiungi ambito** .</span><span class="sxs-lookup"><span data-stu-id="29cd5-134">Select **Add scope** .</span></span>

<span data-ttu-id="29cd5-135">Registrare le seguenti informazioni:</span><span class="sxs-lookup"><span data-stu-id="29cd5-135">Record the following information:</span></span>

* <span data-ttu-id="29cd5-136">URI ID app (ad esempio, `api://41451fa7-82d9-4673-8fa5-69eff5a761fd` , `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd` o il valore personalizzato fornito)</span><span class="sxs-lookup"><span data-stu-id="29cd5-136">App ID URI (for example, `api://41451fa7-82d9-4673-8fa5-69eff5a761fd`, `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd`, or the custom value that you provide)</span></span>
* <span data-ttu-id="29cd5-137">Nome ambito (ad esempio, `API.Access` )</span><span class="sxs-lookup"><span data-stu-id="29cd5-137">Scope name (for example, `API.Access`)</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="29cd5-138">Registrare un'app client</span><span class="sxs-lookup"><span data-stu-id="29cd5-138">Register a client app</span></span>

<span data-ttu-id="29cd5-139">Seguire le istruzioni disponibili in [Guida introduttiva: registrare un'applicazione con la piattaforma di identità Microsoft](/azure/active-directory/develop/quickstart-register-app) e gli argomenti di Azure AAD successivi per registrare un'app AAD per l' *`Client`* app e quindi eseguire le operazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="29cd5-139">Follow the guidance in [Quickstart: Register an application with the Microsoft identity platform](/azure/active-directory/develop/quickstart-register-app) and subsequent Azure AAD topics to register a AAD app for the *`Client`* app and then do the following:</span></span>

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="29cd5-140">In **Azure Active Directory** > **registrazioni app** selezionare **nuova registrazione** .</span><span class="sxs-lookup"><span data-stu-id="29cd5-140">In **Azure Active Directory** > **App registrations** , select **New registration** .</span></span>
1. <span data-ttu-id="29cd5-141">Specificare un **nome** per l'app (ad esempio, **:::no-loc(Blazor)::: client AAD** ).</span><span class="sxs-lookup"><span data-stu-id="29cd5-141">Provide a **Name** for the app (for example, **:::no-loc(Blazor)::: Client AAD** ).</span></span>
1. <span data-ttu-id="29cd5-142">Scegliere i **tipi di conto supportati** .</span><span class="sxs-lookup"><span data-stu-id="29cd5-142">Choose a **Supported account types** .</span></span> <span data-ttu-id="29cd5-143">Per questa esperienza è possibile selezionare **solo gli account in questa directory aziendale** (tenant singolo).</span><span class="sxs-lookup"><span data-stu-id="29cd5-143">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="29cd5-144">Impostare l'elenco a discesa **URI di reindirizzamento** sull' **applicazione a pagina singola (Spa)** e specificare l'URI di reindirizzamento seguente: `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="29cd5-144">Set the **Redirect URI** drop down to **Single-page application (SPA)** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="29cd5-145">La porta predefinita per un'app in esecuzione in Kestrel è la 5001.</span><span class="sxs-lookup"><span data-stu-id="29cd5-145">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="29cd5-146">Se l'app viene eseguita su una porta di Gheppio diversa, usare la porta dell'app.</span><span class="sxs-lookup"><span data-stu-id="29cd5-146">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="29cd5-147">Per IIS Express, la porta generata in modo casuale per l'app si trova nelle *`Server`* proprietà dell'app nel pannello **debug** .</span><span class="sxs-lookup"><span data-stu-id="29cd5-147">For IIS Express, the randomly generated port for the app can be found in the *`Server`* app's properties in the **Debug** panel.</span></span> <span data-ttu-id="29cd5-148">Poiché l'app non esiste in questo momento e la porta IIS Express non è nota, tornare a questo passaggio dopo la creazione dell'app e aggiornare l'URI di reindirizzamento.</span><span class="sxs-lookup"><span data-stu-id="29cd5-148">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="29cd5-149">Un contrassegno viene visualizzato nella sezione [creare l'app](#create-the-app) per ricordare IIS Express agli utenti di aggiornare l'URI di reindirizzamento.</span><span class="sxs-lookup"><span data-stu-id="29cd5-149">A remark appears in the [Create the app](#create-the-app) section to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="29cd5-150">Deselezionare la **Permissions** > casella di controllo autorizzazioni **Concedi amministratore per OpenID e autorizzazioni offline_access** .</span><span class="sxs-lookup"><span data-stu-id="29cd5-150">Clear the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="29cd5-151">Selezionare **Registra** .</span><span class="sxs-lookup"><span data-stu-id="29cd5-151">Select **Register** .</span></span>

<span data-ttu-id="29cd5-152">Registrare l' *`Client`* ID dell'applicazione App (client) (ad esempio, `4369008b-21fa-427c-abaa-9b53bf58e538` ).</span><span class="sxs-lookup"><span data-stu-id="29cd5-152">Record the *`Client`* app Application (client) ID (for example, `4369008b-21fa-427c-abaa-9b53bf58e538`).</span></span>

<span data-ttu-id="29cd5-153">Nelle configurazioni della piattaforma di **autenticazione** > **Platform configurations** > **applicazione a pagina singola (Spa)** :</span><span class="sxs-lookup"><span data-stu-id="29cd5-153">In **Authentication** > **Platform configurations** > **Single-page application (SPA)** :</span></span>

1. <span data-ttu-id="29cd5-154">Verificare che l' **URI di reindirizzamento** di `https://localhost:{PORT}/authentication/login-callback` sia presente.</span><span class="sxs-lookup"><span data-stu-id="29cd5-154">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="29cd5-155">Per **concessione implicita** , assicurarsi che le caselle di controllo per i **token di accesso** e i **token ID** **non** siano selezionate.</span><span class="sxs-lookup"><span data-stu-id="29cd5-155">For **Implicit grant** , ensure that the check boxes for **Access tokens** and **ID tokens** are **not** selected.</span></span>
1. <span data-ttu-id="29cd5-156">Per questa esperienza sono accettabili le impostazioni predefinite rimanenti per l'app.</span><span class="sxs-lookup"><span data-stu-id="29cd5-156">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="29cd5-157">Fare clic sul pulsante **Salva** .</span><span class="sxs-lookup"><span data-stu-id="29cd5-157">Select the **Save** button.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="29cd5-158">In **Azure Active Directory** > **registrazioni app** selezionare **nuova registrazione** .</span><span class="sxs-lookup"><span data-stu-id="29cd5-158">In **Azure Active Directory** > **App registrations** , select **New registration** .</span></span>
1. <span data-ttu-id="29cd5-159">Specificare un **nome** per l'app (ad esempio, **:::no-loc(Blazor)::: client AAD** ).</span><span class="sxs-lookup"><span data-stu-id="29cd5-159">Provide a **Name** for the app (for example, **:::no-loc(Blazor)::: Client AAD** ).</span></span>
1. <span data-ttu-id="29cd5-160">Scegliere i **tipi di conto supportati** .</span><span class="sxs-lookup"><span data-stu-id="29cd5-160">Choose a **Supported account types** .</span></span> <span data-ttu-id="29cd5-161">Per questa esperienza è possibile selezionare **solo gli account in questa directory aziendale** (tenant singolo).</span><span class="sxs-lookup"><span data-stu-id="29cd5-161">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="29cd5-162">Lasciare l'elenco a discesa **URI di reindirizzamento** impostato su **Web** e specificare l'URI di reindirizzamento seguente: `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="29cd5-162">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="29cd5-163">La porta predefinita per un'app in esecuzione in Kestrel è la 5001.</span><span class="sxs-lookup"><span data-stu-id="29cd5-163">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="29cd5-164">Se l'app viene eseguita su una porta di Gheppio diversa, usare la porta dell'app.</span><span class="sxs-lookup"><span data-stu-id="29cd5-164">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="29cd5-165">Per IIS Express, la porta generata in modo casuale per l'app si trova nelle *`Server`* proprietà dell'app nel pannello **debug** .</span><span class="sxs-lookup"><span data-stu-id="29cd5-165">For IIS Express, the randomly generated port for the app can be found in the *`Server`* app's properties in the **Debug** panel.</span></span> <span data-ttu-id="29cd5-166">Poiché l'app non esiste in questo momento e la porta IIS Express non è nota, tornare a questo passaggio dopo la creazione dell'app e aggiornare l'URI di reindirizzamento.</span><span class="sxs-lookup"><span data-stu-id="29cd5-166">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="29cd5-167">Un contrassegno viene visualizzato nella sezione [creare l'app](#create-the-app) per ricordare IIS Express agli utenti di aggiornare l'URI di reindirizzamento.</span><span class="sxs-lookup"><span data-stu-id="29cd5-167">A remark appears in the [Create the app](#create-the-app) section to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="29cd5-168">Deselezionare la **Permissions** > casella di controllo autorizzazioni **Concedi amministratore per OpenID e autorizzazioni offline_access** .</span><span class="sxs-lookup"><span data-stu-id="29cd5-168">Clear the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="29cd5-169">Selezionare **Registra** .</span><span class="sxs-lookup"><span data-stu-id="29cd5-169">Select **Register** .</span></span>

<span data-ttu-id="29cd5-170">Registrare l' *`Client`* ID dell'applicazione App (client) (ad esempio, `4369008b-21fa-427c-abaa-9b53bf58e538` ).</span><span class="sxs-lookup"><span data-stu-id="29cd5-170">Record the *`Client`* app Application (client) ID (for example, `4369008b-21fa-427c-abaa-9b53bf58e538`).</span></span>

<span data-ttu-id="29cd5-171">In **Authentication** > **configurazioni piattaforma** di autenticazione > **Web** :</span><span class="sxs-lookup"><span data-stu-id="29cd5-171">In **Authentication** > **Platform configurations** > **Web** :</span></span>

1. <span data-ttu-id="29cd5-172">Verificare che l' **URI di reindirizzamento** di `https://localhost:{PORT}/authentication/login-callback` sia presente.</span><span class="sxs-lookup"><span data-stu-id="29cd5-172">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="29cd5-173">Per **concessione implicita** , selezionare le caselle di controllo per i token di **accesso** e i **token ID** .</span><span class="sxs-lookup"><span data-stu-id="29cd5-173">For **Implicit grant** , select the check boxes for **Access tokens** and **ID tokens** .</span></span>
1. <span data-ttu-id="29cd5-174">Per questa esperienza sono accettabili le impostazioni predefinite rimanenti per l'app.</span><span class="sxs-lookup"><span data-stu-id="29cd5-174">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="29cd5-175">Fare clic sul pulsante **Salva** .</span><span class="sxs-lookup"><span data-stu-id="29cd5-175">Select the **Save** button.</span></span>

::: moniker-end

<span data-ttu-id="29cd5-176">In **autorizzazioni API** :</span><span class="sxs-lookup"><span data-stu-id="29cd5-176">In **API permissions** :</span></span>

1. <span data-ttu-id="29cd5-177">Verificare che l'app disponga **Microsoft Graph**  >  autorizzazione **User. Read** .</span><span class="sxs-lookup"><span data-stu-id="29cd5-177">Confirm that the app has **Microsoft Graph** > **User.Read** permission.</span></span>
1. <span data-ttu-id="29cd5-178">Selezionare **Aggiungi un'autorizzazione** seguita da **API personali** .</span><span class="sxs-lookup"><span data-stu-id="29cd5-178">Select **Add a permission** followed by **My APIs** .</span></span>
1. <span data-ttu-id="29cd5-179">Selezionare l' *app per le API server* dalla colonna **nome** (ad esempio, **:::no-loc(Blazor Server)::: AAD** ).</span><span class="sxs-lookup"><span data-stu-id="29cd5-179">Select the *Server API app* from the **Name** column (for example, **:::no-loc(Blazor Server)::: AAD** ).</span></span>
1. <span data-ttu-id="29cd5-180">Aprire l'elenco di **API** .</span><span class="sxs-lookup"><span data-stu-id="29cd5-180">Open the **API** list.</span></span>
1. <span data-ttu-id="29cd5-181">Abilitare l'accesso all'API (ad esempio, `API.Access` ).</span><span class="sxs-lookup"><span data-stu-id="29cd5-181">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="29cd5-182">Selezionare **Aggiungi autorizzazioni** .</span><span class="sxs-lookup"><span data-stu-id="29cd5-182">Select **Add permissions** .</span></span>
1. <span data-ttu-id="29cd5-183">Selezionare il pulsante **concedere il consenso dell'amministratore per {tenant Name}** .</span><span class="sxs-lookup"><span data-stu-id="29cd5-183">Select the **Grant admin consent for {TENANT NAME}** button.</span></span> <span data-ttu-id="29cd5-184">Selezionare **Sì** per confermare.</span><span class="sxs-lookup"><span data-stu-id="29cd5-184">Select **Yes** to confirm.</span></span>

### <a name="create-the-app"></a><span data-ttu-id="29cd5-185">Creare l'app</span><span class="sxs-lookup"><span data-stu-id="29cd5-185">Create the app</span></span>

<span data-ttu-id="29cd5-186">In una cartella vuota, sostituire i segnaposto nel comando seguente con le informazioni registrate in precedenza ed eseguire il comando in una shell dei comandi:</span><span class="sxs-lookup"><span data-stu-id="29cd5-186">In an empty folder, replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{TENANT DOMAIN}" -ho -o {APP NAME} --tenant-id "{TENANT ID}"
```

| <span data-ttu-id="29cd5-187">Segnaposto</span><span class="sxs-lookup"><span data-stu-id="29cd5-187">Placeholder</span></span>                  | <span data-ttu-id="29cd5-188">Nome portale di Azure</span><span class="sxs-lookup"><span data-stu-id="29cd5-188">Azure portal name</span></span>                                     | <span data-ttu-id="29cd5-189">Esempio</span><span class="sxs-lookup"><span data-stu-id="29cd5-189">Example</span></span>                                      |
| ---------------------------- | ----------------------------------------------------- | -------------------------------------------- |
| `{APP NAME}`                 | &mdash;                                               | `:::no-loc(Blazor):::Sample`                               |
| `{CLIENT APP CLIENT ID}`     | <span data-ttu-id="29cd5-190">ID applicazione (client) per l' *`Client`* app</span><span class="sxs-lookup"><span data-stu-id="29cd5-190">Application (client) ID for the *`Client`* app</span></span>        | `4369008b-21fa-427c-abaa-9b53bf58e538`       |
| `{DEFAULT SCOPE}`            | <span data-ttu-id="29cd5-191">Nome ambito</span><span class="sxs-lookup"><span data-stu-id="29cd5-191">Scope name</span></span>                                            | `API.Access`                                 |
| `{SERVER API APP CLIENT ID}` | <span data-ttu-id="29cd5-192">ID applicazione (client) per l' *app per le API server*</span><span class="sxs-lookup"><span data-stu-id="29cd5-192">Application (client) ID for the *Server API app*</span></span>      | `41451fa7-82d9-4673-8fa5-69eff5a761fd`       |
| `{SERVER API APP ID URI}`    | <span data-ttu-id="29cd5-193">URI dell'ID applicazione</span><span class="sxs-lookup"><span data-stu-id="29cd5-193">Application ID URI</span></span>                                    | `api://41451fa7-82d9-4673-8fa5-69eff5a761fd` |
| `{TENANT DOMAIN}`            | <span data-ttu-id="29cd5-194">Dominio primario/server di pubblicazione/tenant</span><span class="sxs-lookup"><span data-stu-id="29cd5-194">Primary/Publisher/Tenant domain</span></span>                       | `contoso.onmicrosoft.com`                    |
| `{TENANT ID}`                | <span data-ttu-id="29cd5-195">ID directory (tenant)</span><span class="sxs-lookup"><span data-stu-id="29cd5-195">Directory (tenant) ID</span></span>                                 | `e86c78e2-8bb4-4c41-aefd-918e0565a45e`       |

<span data-ttu-id="29cd5-196">Il percorso di output specificato con l'opzione `-o|--output` consente di creare una cartella di progetto se non esiste e viene incluso nel nome dell'app.</span><span class="sxs-lookup"><span data-stu-id="29cd5-196">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="29cd5-197">Potrebbe essere necessaria una modifica alla configurazione quando si usa un tenant di Azure con un dominio Publisher non verificato, descritto nella sezione [impostazioni app](#app-settings) .</span><span class="sxs-lookup"><span data-stu-id="29cd5-197">A configuration change might be required when using an Azure tenant with an unverified publisher domain, which is described in the [App settings](#app-settings) section.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="29cd5-198">Potrebbe essere necessaria una modifica alla configurazione quando si usa un tenant di Azure con un dominio del server di pubblicazione non verificato, descritto nella sezione [ambiti dei token di accesso](#access-token-scopes) .</span><span class="sxs-lookup"><span data-stu-id="29cd5-198">A configuration change might be required when using an Azure tenant with an unverified publisher domain, which is described in the [Access token scopes](#access-token-scopes) section.</span></span>

::: moniker-end

> [!NOTE]
> <span data-ttu-id="29cd5-199">Nell'portale di Azure l' *`Client`* **URI di reindirizzamento** della configurazione della piattaforma dell'app è configurato per la porta 5001 per le app eseguite nel server gheppio con le impostazioni predefinite.</span><span class="sxs-lookup"><span data-stu-id="29cd5-199">In the Azure portal, the *`Client`* app's platform configuration **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="29cd5-200">Se l' *`Client`* app viene eseguita su una porta IIS Express casuale, la porta per l'app si trova nelle proprietà dell' *app* per le API del server nel pannello **debug** .</span><span class="sxs-lookup"><span data-stu-id="29cd5-200">If the *`Client`* app is run on a random IIS Express port, the port for the app can be found in the *Server API app's* properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="29cd5-201">Se la porta non è stata configurata in precedenza con la *`Client`* porta nota dell'app, tornare alla *`Client`* registrazione dell'app nel portale di Azure e aggiornare l'URI di reindirizzamento con la porta corretta.</span><span class="sxs-lookup"><span data-stu-id="29cd5-201">If the port wasn't configured earlier with the *`Client`* app's known port, return to the *`Client`* app's registration in the Azure portal and update the redirect URI with the correct port.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="29cd5-202">*`Server`* configurazione dell'app</span><span class="sxs-lookup"><span data-stu-id="29cd5-202">*`Server`* app configuration</span></span>

<span data-ttu-id="29cd5-203">*Questa sezione è relativa all'app della soluzione **`Server`** .*</span><span class="sxs-lookup"><span data-stu-id="29cd5-203">*This section pertains to the solution's **`Server`** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="29cd5-204">Pacchetto di autenticazione</span><span class="sxs-lookup"><span data-stu-id="29cd5-204">Authentication package</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="29cd5-205">Il supporto per l'autenticazione e l'autorizzazione delle chiamate a ASP.NET Core API Web con la :::no-loc(Identity)::: piattaforma Microsoft è fornito dai pacchetti seguenti:</span><span class="sxs-lookup"><span data-stu-id="29cd5-205">The support for authenticating and authorizing calls to ASP.NET Core Web APIs with the Microsoft :::no-loc(Identity)::: Platform is provided by the following packages:</span></span>

* [`Microsoft.:::no-loc(Identity):::.Web`](https://www.nuget.org/packages/Microsoft.:::no-loc(Identity):::.Web)
* [`Microsoft.:::no-loc(Identity):::.Web.UI`](https://www.nuget.org/packages/Microsoft.:::no-loc(Identity):::.Web.UI)

```xml
<PackageReference Include="Microsoft.:::no-loc(Identity):::.Web" Version="{VERSION}" />
<PackageReference Include="Microsoft.:::no-loc(Identity):::.Web.UI" Version="{VERSION}" />
```

<span data-ttu-id="29cd5-206">Per il segnaposto `{VERSION}` , la versione stabile più recente del pacchetto che corrisponde alla versione del Framework condiviso dell'app è disponibile nella cronologia delle **versioni** del pacchetto in NuGet.org.</span><span class="sxs-lookup"><span data-stu-id="29cd5-206">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at NuGet.org.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="29cd5-207">Il pacchetto fornisce il supporto per l'autenticazione e l'autorizzazione delle chiamate a ASP.NET Core API Web [`Microsoft.AspNetCore.Authentication.AzureAD.UI`](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI) :</span><span class="sxs-lookup"><span data-stu-id="29cd5-207">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the [`Microsoft.AspNetCore.Authentication.AzureAD.UI`](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI) package:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureAD.UI" 
  Version="{VERSION}" />
```

<span data-ttu-id="29cd5-208">Per il segnaposto `{VERSION}` , la versione stabile più recente del pacchetto che corrisponde alla versione del Framework condiviso dell'app è disponibile nella cronologia delle **versioni** del pacchetto in [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI).</span><span class="sxs-lookup"><span data-stu-id="29cd5-208">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI).</span></span>

::: moniker-end

### <a name="authentication-service-support"></a><span data-ttu-id="29cd5-209">Supporto del servizio di autenticazione</span><span class="sxs-lookup"><span data-stu-id="29cd5-209">Authentication service support</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="29cd5-210">Il `AddAuthentication` metodo imposta i servizi di autenticazione all'interno dell'app e configura il gestore di JWT Bearer come metodo di autenticazione predefinito.</span><span class="sxs-lookup"><span data-stu-id="29cd5-210">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="29cd5-211">Il <xref:Microsoft.:::no-loc(Identity):::.Web.Microsoft:::no-loc(Identity):::WebApiAuthenticationBuilderExtensions.AddMicrosoft:::no-loc(Identity):::WebApi%2A> metodo configura i servizi per proteggere l'API Web con Microsoft :::no-loc(Identity)::: Platform v 2.0.</span><span class="sxs-lookup"><span data-stu-id="29cd5-211">The <xref:Microsoft.:::no-loc(Identity):::.Web.Microsoft:::no-loc(Identity):::WebApiAuthenticationBuilderExtensions.AddMicrosoft:::no-loc(Identity):::WebApi%2A> method configures services to protect the web API with Microsoft :::no-loc(Identity)::: Platform v2.0.</span></span> <span data-ttu-id="29cd5-212">Questo metodo prevede una `AzureAd` sezione nella configurazione dell'app con le impostazioni necessarie per inizializzare le opzioni di autenticazione.</span><span class="sxs-lookup"><span data-stu-id="29cd5-212">This method expects an `AzureAd` section in the app's configuration with the necessary settings to initialize authentication options.</span></span>

```csharp
services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
    .AddMicrosoft:::no-loc(Identity):::WebApi(Configuration.GetSection("AzureAd"));
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="29cd5-213">Il `AddAuthentication` metodo imposta i servizi di autenticazione all'interno dell'app e configura il gestore di JWT Bearer come metodo di autenticazione predefinito.</span><span class="sxs-lookup"><span data-stu-id="29cd5-213">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="29cd5-214">Il <xref:Microsoft.AspNetCore.Authentication.AzureADAuthenticationBuilderExtensions.AddAzureADBearer%2A> metodo imposta i parametri specifici nel gestore di JWT Bearer necessario per convalidare i token emessi dal Azure Active Directory:</span><span class="sxs-lookup"><span data-stu-id="29cd5-214">The <xref:Microsoft.AspNetCore.Authentication.AzureADAuthenticationBuilderExtensions.AddAzureADBearer%2A> method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory:</span></span>

```csharp
services.AddAuthentication(AzureADDefaults.BearerAuthenticationScheme)
    .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
```

::: moniker-end

<span data-ttu-id="29cd5-215"><xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> e <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> verificare che:</span><span class="sxs-lookup"><span data-stu-id="29cd5-215"><xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> and <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> ensure that:</span></span>

* <span data-ttu-id="29cd5-216">L'app tenta di analizzare e convalidare i token nelle richieste in ingresso.</span><span class="sxs-lookup"><span data-stu-id="29cd5-216">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="29cd5-217">Eventuali richieste che tentano di accedere a una risorsa protetta senza credenziali appropriate hanno esito negativo.</span><span class="sxs-lookup"><span data-stu-id="29cd5-217">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="userno-locidentityname"></a><span data-ttu-id="29cd5-218">Utente. :::no-loc(Identity)::: . Nome</span><span class="sxs-lookup"><span data-stu-id="29cd5-218">User.:::no-loc(Identity):::.Name</span></span>

<span data-ttu-id="29cd5-219">Per impostazione predefinita, l' *`Server`* API dell'app viene popolata `User.:::no-loc(Identity):::.Name` con il valore del `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` tipo di attestazione (ad esempio, `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com` ).</span><span class="sxs-lookup"><span data-stu-id="29cd5-219">By default, the *`Server`* app API populates `User.:::no-loc(Identity):::.Name` with the value from the `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` claim type (for example, `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com`).</span></span>

<span data-ttu-id="29cd5-220">Per configurare l'app per la ricezione del valore dal `name` tipo di attestazione, configurare il <xref:Microsoft.:::no-loc(Identity):::Model.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType> di <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="29cd5-220">To configure the app to receive the value from the `name` claim type, configure the <xref:Microsoft.:::no-loc(Identity):::Model.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType> of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;

...

services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a><span data-ttu-id="29cd5-221">Impostazioni delle app</span><span class="sxs-lookup"><span data-stu-id="29cd5-221">App settings</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="29cd5-222">Il `appsettings.json` file contiene le opzioni per configurare il gestore di connessione JWT usato per convalidare i token di accesso:</span><span class="sxs-lookup"><span data-stu-id="29cd5-222">The `appsettings.json` file contains the options to configure the JWT bearer handler used to validate access tokens:</span></span>

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "{DOMAIN}",
    "TenantId": "{TENANT ID}",
    "ClientId": "{SERVER API APP CLIENT ID}",
    "CallbackPath": "/signin-oidc"
  }
}
```

<span data-ttu-id="29cd5-223">Esempio:</span><span class="sxs-lookup"><span data-stu-id="29cd5-223">Example:</span></span>

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "contoso.onmicrosoft.com",
    "TenantId": "e86c78e2-8bb4-4c41-aefd-918e0565a45e",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "CallbackPath": "/signin-oidc"
  }
}
```

[!INCLUDE[](~/includes/blazor-security/azure-scope-5x.md)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="29cd5-224">Il `appsettings.json` file contiene le opzioni per configurare il gestore di connessione JWT usato per convalidare i token di accesso:</span><span class="sxs-lookup"><span data-stu-id="29cd5-224">The `appsettings.json` file contains the options to configure the JWT bearer handler used to validate access tokens:</span></span>

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

<span data-ttu-id="29cd5-225">Esempio:</span><span class="sxs-lookup"><span data-stu-id="29cd5-225">Example:</span></span>

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

::: moniker-end

### <a name="weatherforecast-controller"></a><span data-ttu-id="29cd5-226">Controller WeatherForecast</span><span class="sxs-lookup"><span data-stu-id="29cd5-226">WeatherForecast controller</span></span>

<span data-ttu-id="29cd5-227">Il controller WeatherForecast ( *Controllers/WeatherForecastController. cs* ) espone un'API protetta con l' [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attributo applicato al controller.</span><span class="sxs-lookup"><span data-stu-id="29cd5-227">The WeatherForecast controller ( *Controllers/WeatherForecastController.cs* ) exposes a protected API with the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute applied to the controller.</span></span> <span data-ttu-id="29cd5-228">È **importante** comprendere che:</span><span class="sxs-lookup"><span data-stu-id="29cd5-228">It's **important** to understand that:</span></span>

* <span data-ttu-id="29cd5-229">L' [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attributo in questo controller API è l'unico elemento che protegge questa API da accessi non autorizzati.</span><span class="sxs-lookup"><span data-stu-id="29cd5-229">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="29cd5-230">L' [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attributo usato nell' :::no-loc(Blazor WebAssembly)::: app funge solo da hint per l'app che l'utente deve essere autorizzato affinché l'app funzioni correttamente.</span><span class="sxs-lookup"><span data-stu-id="29cd5-230">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute used in the :::no-loc(Blazor WebAssembly)::: app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

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

## <a name="client-app-configuration"></a><span data-ttu-id="29cd5-231">*`Client`* configurazione dell'app</span><span class="sxs-lookup"><span data-stu-id="29cd5-231">*`Client`* app configuration</span></span>

<span data-ttu-id="29cd5-232">*Questa sezione è relativa all'app della soluzione **`Client`** .*</span><span class="sxs-lookup"><span data-stu-id="29cd5-232">*This section pertains to the solution's **`Client`** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="29cd5-233">Pacchetto di autenticazione</span><span class="sxs-lookup"><span data-stu-id="29cd5-233">Authentication package</span></span>

<span data-ttu-id="29cd5-234">Quando viene creata un'app per usare gli account aziendali o dell'Istituto di istruzione ( `SingleOrg` ), l'app riceve automaticamente un riferimento al pacchetto per [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ( [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) ).</span><span class="sxs-lookup"><span data-stu-id="29cd5-234">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)).</span></span> <span data-ttu-id="29cd5-235">Il pacchetto fornisce un set di primitive che consentono all'app di autenticare gli utenti e ottenere i token per chiamare le API protette.</span><span class="sxs-lookup"><span data-stu-id="29cd5-235">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="29cd5-236">Se si aggiunge l'autenticazione a un'app, aggiungere manualmente il pacchetto al file di progetto dell'app:</span><span class="sxs-lookup"><span data-stu-id="29cd5-236">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="{VERSION}" />
```

<span data-ttu-id="29cd5-237">Per il segnaposto `{VERSION}` , la versione stabile più recente del pacchetto che corrisponde alla versione del Framework condiviso dell'app è disponibile nella cronologia delle **versioni** del pacchetto in [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal).</span><span class="sxs-lookup"><span data-stu-id="29cd5-237">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal).</span></span>

<span data-ttu-id="29cd5-238">Il [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) pacchetto aggiunge il [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) pacchetto all'app in modo transitivo.</span><span class="sxs-lookup"><span data-stu-id="29cd5-238">The [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package transitively adds the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="29cd5-239">Supporto del servizio di autenticazione</span><span class="sxs-lookup"><span data-stu-id="29cd5-239">Authentication service support</span></span>

<span data-ttu-id="29cd5-240"><xref:System.Net.Http.HttpClient>Viene aggiunto il supporto per le istanze di che includono token di accesso quando si effettuano richieste al progetto server.</span><span class="sxs-lookup"><span data-stu-id="29cd5-240">Support for <xref:System.Net.Http.HttpClient> instances is added that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="29cd5-241">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="29cd5-241">`Program.cs`:</span></span>

```csharp
builder.Services.AddHttpClient("{APP ASSEMBLY}.ServerAPI", client => 
        client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddScoped(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("{APP ASSEMBLY}.ServerAPI"));
```

<span data-ttu-id="29cd5-242">Il segnaposto `{APP ASSEMBLY}` è il nome dell'assembly dell'app (ad esempio, `:::no-loc(Blazor):::Sample.ServerAPI` ).</span><span class="sxs-lookup"><span data-stu-id="29cd5-242">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `:::no-loc(Blazor):::Sample.ServerAPI`).</span></span>

<span data-ttu-id="29cd5-243">Il supporto per l'autenticazione degli utenti viene registrato nel contenitore del servizio con il <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> metodo di estensione fornito dal [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) pacchetto.</span><span class="sxs-lookup"><span data-stu-id="29cd5-243">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> extension method provided by the [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package.</span></span> <span data-ttu-id="29cd5-244">Questo metodo configura i servizi necessari per l'interazione dell'app con il :::no-loc(Identity)::: provider (IP).</span><span class="sxs-lookup"><span data-stu-id="29cd5-244">This method sets up the services required for the app to interact with the :::no-loc(Identity)::: Provider (IP).</span></span>

<span data-ttu-id="29cd5-245">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="29cd5-245">`Program.cs`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="29cd5-246">Il <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> metodo accetta un callback per configurare i parametri necessari per autenticare un'app.</span><span class="sxs-lookup"><span data-stu-id="29cd5-246">The <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="29cd5-247">Quando si registra l'app, è possibile ottenere i valori necessari per la configurazione dell'app dalla configurazione di AAD del portale di Azure.</span><span class="sxs-lookup"><span data-stu-id="29cd5-247">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

<span data-ttu-id="29cd5-248">La configurazione viene fornita dal `wwwroot/appsettings.json` file:</span><span class="sxs-lookup"><span data-stu-id="29cd5-248">Configuration is supplied by the `wwwroot/appsettings.json` file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/{TENANT ID}",
    "ClientId": "{CLIENT APP CLIENT ID}",
    "ValidateAuthority": true
  }
}
```

<span data-ttu-id="29cd5-249">Esempio:</span><span class="sxs-lookup"><span data-stu-id="29cd5-249">Example:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/e86c78e2-...-918e0565a45e",
    "ClientId": "4369008b-21fa-427c-abaa-9b53bf58e538",
    "ValidateAuthority": true
  }
}
```

### <a name="access-token-scopes"></a><span data-ttu-id="29cd5-250">Ambiti del token di accesso</span><span class="sxs-lookup"><span data-stu-id="29cd5-250">Access token scopes</span></span>

<span data-ttu-id="29cd5-251">Gli ambiti dei token di accesso predefiniti rappresentano l'elenco degli ambiti dei token di accesso:</span><span class="sxs-lookup"><span data-stu-id="29cd5-251">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="29cd5-252">Incluso per impostazione predefinita nella richiesta di accesso.</span><span class="sxs-lookup"><span data-stu-id="29cd5-252">Included by default in the sign in request.</span></span>
* <span data-ttu-id="29cd5-253">Utilizzato per eseguire il provisioning di un token di accesso immediatamente dopo l'autenticazione.</span><span class="sxs-lookup"><span data-stu-id="29cd5-253">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="29cd5-254">Tutti gli ambiti devono appartenere alla stessa app per ogni regola di Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="29cd5-254">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="29cd5-255">Per altre app per le API è possibile aggiungere altri ambiti, se necessario:</span><span class="sxs-lookup"><span data-stu-id="29cd5-255">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="29cd5-256">Specificare gli ambiti aggiuntivi con `AdditionalScopesToConsent` :</span><span class="sxs-lookup"><span data-stu-id="29cd5-256">Specify additional scopes with `AdditionalScopesToConsent`:</span></span>

```csharp
options.ProviderOptions.AdditionalScopesToConsent.Add("{ADDITIONAL SCOPE URI}");
```

::: moniker range="< aspnetcore-5.0"

[!INCLUDE[](~/includes/blazor-security/azure-scope-3x.md)]

::: moniker-end

<span data-ttu-id="29cd5-257">Per ulteriori informazioni, vedere le sezioni seguenti dell'articolo *scenari aggiuntivi* :</span><span class="sxs-lookup"><span data-stu-id="29cd5-257">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="29cd5-258">Richiedere token di accesso aggiuntivi</span><span class="sxs-lookup"><span data-stu-id="29cd5-258">Request additional access tokens</span></span>](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="29cd5-259">Connetti token alle richieste in uscita</span><span class="sxs-lookup"><span data-stu-id="29cd5-259">Attach tokens to outgoing requests</span></span>](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

::: moniker range=">= aspnetcore-5.0"

### <a name="login-mode"></a><span data-ttu-id="29cd5-260">Modalità di accesso</span><span class="sxs-lookup"><span data-stu-id="29cd5-260">Login mode</span></span>

[!INCLUDE[](~/includes/blazor-security/msal-login-mode.md)]

::: moniker-end

### <a name="imports-file"></a><span data-ttu-id="29cd5-261">Importa file</span><span class="sxs-lookup"><span data-stu-id="29cd5-261">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="29cd5-262">Pagina di indice</span><span class="sxs-lookup"><span data-stu-id="29cd5-262">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a><span data-ttu-id="29cd5-263">Componente app</span><span class="sxs-lookup"><span data-stu-id="29cd5-263">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="29cd5-264">Componente RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="29cd5-264">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="29cd5-265">Componente LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="29cd5-265">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="29cd5-266">Componente di autenticazione</span><span class="sxs-lookup"><span data-stu-id="29cd5-266">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="29cd5-267">Componente FetchData</span><span class="sxs-lookup"><span data-stu-id="29cd5-267">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="29cd5-268">Eseguire l'app</span><span class="sxs-lookup"><span data-stu-id="29cd5-268">Run the app</span></span>

<span data-ttu-id="29cd5-269">Eseguire l'app dal progetto server.</span><span class="sxs-lookup"><span data-stu-id="29cd5-269">Run the app from the Server project.</span></span> <span data-ttu-id="29cd5-270">Quando si usa Visual Studio, eseguire una delle operazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="29cd5-270">When using Visual Studio, either:</span></span>

* <span data-ttu-id="29cd5-271">Impostare l'elenco a discesa **progetti di avvio** nella barra degli strumenti sull'app per le *API server* e selezionare il pulsante **Esegui** .</span><span class="sxs-lookup"><span data-stu-id="29cd5-271">Set the **Startup Projects** drop down list in the toolbar to the *Server API app* and select the **Run** button.</span></span>
* <span data-ttu-id="29cd5-272">Selezionare il progetto server in **Esplora soluzioni** , quindi fare clic sul pulsante **Esegui** nella barra degli strumenti o avviare l'app dal menu **debug** .</span><span class="sxs-lookup"><span data-stu-id="29cd5-272">Select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="29cd5-273">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="29cd5-273">Additional resources</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="29cd5-274">Richieste API Web non autenticate o non autorizzate in un'app con un client predefinito sicuro</span><span class="sxs-lookup"><span data-stu-id="29cd5-274">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:blazor/security/webassembly/aad-groups-roles>
* <xref:security/authentication/azure-active-directory/index>
* [<span data-ttu-id="29cd5-275">Documentazione di Microsoft Identity Platform</span><span class="sxs-lookup"><span data-stu-id="29cd5-275">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
