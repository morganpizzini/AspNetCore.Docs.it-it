---
title: Proteggere un'app Blazor ospitata ASP.NET Core webassembly con Azure Active Directory B2C
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/23/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/hosted-with-azure-active-directory-b2c
ms.openlocfilehash: 45ef1e6599777a38da7db753a8868028f3134f4b
ms.sourcegitcommit: 7bb14d005155a5044c7902a08694ee8ccb20c113
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82110980"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-hosted-app-with-azure-active-directory-b2c"></a><span data-ttu-id="6f92b-102">Proteggere un'app Blazor ospitata ASP.NET Core webassembly con Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="6f92b-102">Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory B2C</span></span>

<span data-ttu-id="6f92b-103">Di [Javier Calvarro Nelson](https://github.com/javiercn) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="6f92b-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

> [!NOTE]
> <span data-ttu-id="6f92b-104">Le indicazioni fornite in questo articolo si applicano a ASP.NET Core 3,2 Preview 4.</span><span class="sxs-lookup"><span data-stu-id="6f92b-104">The guidance in this article applies to ASP.NET Core 3.2 Preview 4.</span></span> <span data-ttu-id="6f92b-105">Questo argomento verrà aggiornato per coprire l'anteprima 5 il venerdì 24 aprile.</span><span class="sxs-lookup"><span data-stu-id="6f92b-105">This topic will be updated to cover Preview 5 on Friday, April 24.</span></span>

<span data-ttu-id="6f92b-106">Questo articolo descrive come creare un' Blazor app webassembly autonoma che usa [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) per l'autenticazione.</span><span class="sxs-lookup"><span data-stu-id="6f92b-106">This article describes how to create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) for authentication.</span></span>

## <a name="register-apps-in-aad-b2c-and-create-solution"></a><span data-ttu-id="6f92b-107">Registrare le app in AAD B2C e creare la soluzione</span><span class="sxs-lookup"><span data-stu-id="6f92b-107">Register apps in AAD B2C and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="6f92b-108">Creare un tenant</span><span class="sxs-lookup"><span data-stu-id="6f92b-108">Create a tenant</span></span>

<span data-ttu-id="6f92b-109">Seguire le istruzioni in [esercitazione: creare un tenant di Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-create-tenant) per creare un tenant di AAD B2C e registrare le informazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="6f92b-109">Follow the guidance in [Tutorial: Create an Azure Active Directory B2C tenant](/azure/active-directory-b2c/tutorial-create-tenant) to create an AAD B2C tenant and record the following information:</span></span>

* <span data-ttu-id="6f92b-110">AAD B2C istanza (ad esempio, `https://contoso.b2clogin.com/`, che include la barra finale)</span><span class="sxs-lookup"><span data-stu-id="6f92b-110">AAD B2C instance (for example, `https://contoso.b2clogin.com/`, which includes the trailing slash)</span></span>
* <span data-ttu-id="6f92b-111">AAD B2C dominio tenant (ad esempio, `contoso.onmicrosoft.com`)</span><span class="sxs-lookup"><span data-stu-id="6f92b-111">AAD B2C Tenant domain (for example, `contoso.onmicrosoft.com`)</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="6f92b-112">Registrare un'app per le API server</span><span class="sxs-lookup"><span data-stu-id="6f92b-112">Register a server API app</span></span>

<span data-ttu-id="6f92b-113">Seguire le istruzioni riportate in [esercitazione: registrare un'applicazione in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) per registrare un'app AAD per l'app per le *API server* nell'area **Azure Active Directory** > **registrazioni app** della portale di Azure:</span><span class="sxs-lookup"><span data-stu-id="6f92b-113">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) to register an AAD app for the *Server API app* in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="6f92b-114">Selezionare **Nuova registrazione**.</span><span class="sxs-lookup"><span data-stu-id="6f92b-114">Select **New registration**.</span></span>
1. <span data-ttu-id="6f92b-115">Specificare un **nome** per l'app, \*\* Blazor ad esempio server AAD B2C\*\*.</span><span class="sxs-lookup"><span data-stu-id="6f92b-115">Provide a **Name** for the app (for example, **Blazor Server AAD B2C**).</span></span>
1. <span data-ttu-id="6f92b-116">Per i **tipi di account supportati**, selezionare **account in qualsiasi directory organizzativa o provider di identità. Per l'autenticazione degli utenti con Azure AD B2C.**</span><span class="sxs-lookup"><span data-stu-id="6f92b-116">For **Supported account types**, select **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span> <span data-ttu-id="6f92b-117">(multi-tenant) per questa esperienza.</span><span class="sxs-lookup"><span data-stu-id="6f92b-117">(multi-tenant) for this experience.</span></span>
1. <span data-ttu-id="6f92b-118">L' *app* per le API del server non richiede un **URI di reindirizzamento** in questo scenario, quindi lasciare l'elenco a discesa impostato su **Web** e non immettere un URI di reindirizzamento.</span><span class="sxs-lookup"><span data-stu-id="6f92b-118">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="6f92b-119">Verificare che le **autorizzazioni** > **concessino l'amministratore concent a OpenID e offline_access autorizzazioni** siano abilitate.</span><span class="sxs-lookup"><span data-stu-id="6f92b-119">Confirm that **Permissions** > **Grant admin concent to openid and offline_access permissions** is enabled.</span></span>
1. <span data-ttu-id="6f92b-120">Selezionare **Registra**.</span><span class="sxs-lookup"><span data-stu-id="6f92b-120">Select **Register**.</span></span>

<span data-ttu-id="6f92b-121">In **esporre un'API**:</span><span class="sxs-lookup"><span data-stu-id="6f92b-121">In **Expose an API**:</span></span>

1. <span data-ttu-id="6f92b-122">Selezionare **Aggiungi un ambito**.</span><span class="sxs-lookup"><span data-stu-id="6f92b-122">Select **Add a scope**.</span></span>
1. <span data-ttu-id="6f92b-123">Fai clic su **Salva e continua**.</span><span class="sxs-lookup"><span data-stu-id="6f92b-123">Select **Save and continue**.</span></span>
1. <span data-ttu-id="6f92b-124">Specificare un **nome** per l'ambito (ad `API.Access`esempio,).</span><span class="sxs-lookup"><span data-stu-id="6f92b-124">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="6f92b-125">Fornire un **nome visualizzato** per il consenso dell'amministratore, `Access API`ad esempio.</span><span class="sxs-lookup"><span data-stu-id="6f92b-125">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="6f92b-126">Fornire una **Descrizione del consenso dell'amministratore** , `Allows the app to access server app API endpoints.`ad esempio.</span><span class="sxs-lookup"><span data-stu-id="6f92b-126">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="6f92b-127">Verificare che lo **stato** sia impostato su **abilitato**.</span><span class="sxs-lookup"><span data-stu-id="6f92b-127">Confirm that the **State** is set to **Enabled**.</span></span>
1. <span data-ttu-id="6f92b-128">Selezionare **Aggiungi ambito**.</span><span class="sxs-lookup"><span data-stu-id="6f92b-128">Select **Add scope**.</span></span>

<span data-ttu-id="6f92b-129">Registrare le seguenti informazioni:</span><span class="sxs-lookup"><span data-stu-id="6f92b-129">Record the following information:</span></span>

* <span data-ttu-id="6f92b-130">*App per le API server* ID applicazione (ID client) (ad esempio, `11111111-1111-1111-1111-111111111111`)</span><span class="sxs-lookup"><span data-stu-id="6f92b-130">*Server API app* Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`)</span></span>
* <span data-ttu-id="6f92b-131">URI ID app (ad esempio `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111` `api://11111111-1111-1111-1111-111111111111`,, o il valore personalizzato specificato)</span><span class="sxs-lookup"><span data-stu-id="6f92b-131">App ID URI (for example, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, `api://11111111-1111-1111-1111-111111111111`, or the custom value that you provided)</span></span>
* <span data-ttu-id="6f92b-132">ID directory (ID tenant) (ad esempio, `222222222-2222-2222-2222-222222222222`)</span><span class="sxs-lookup"><span data-stu-id="6f92b-132">Directory ID (Tenant ID) (for example, `222222222-2222-2222-2222-222222222222`)</span></span>
* <span data-ttu-id="6f92b-133">*App per le API server* URI ID app (ad esempio `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, il portale di Azure potrebbe avere come valore predefinito l'ID client)</span><span class="sxs-lookup"><span data-stu-id="6f92b-133">*Server API app* App ID URI (for example, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, the Azure portal might default the value to the Client ID)</span></span>
* <span data-ttu-id="6f92b-134">Ambito predefinito (ad esempio, `API.Access`)</span><span class="sxs-lookup"><span data-stu-id="6f92b-134">Default scope (for example, `API.Access`)</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="6f92b-135">Registrare un'app client</span><span class="sxs-lookup"><span data-stu-id="6f92b-135">Register a client app</span></span>

<span data-ttu-id="6f92b-136">Seguire le istruzioni riportate in [esercitazione: registrare un'applicazione in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) di nuovo per registrare un'app AAD per l' *app client* nell'area **Azure Active Directory** > **registrazioni app** della portale di Azure:</span><span class="sxs-lookup"><span data-stu-id="6f92b-136">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) again to register an AAD app for the *Client app* in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="6f92b-137">Selezionare **Nuova registrazione**.</span><span class="sxs-lookup"><span data-stu-id="6f92b-137">Select **New registration**.</span></span>
1. <span data-ttu-id="6f92b-138">Specificare un **nome** per l'app, \*\* Blazor ad esempio AAD B2C client\*\*.</span><span class="sxs-lookup"><span data-stu-id="6f92b-138">Provide a **Name** for the app (for example, **Blazor Client AAD B2C**).</span></span>
1. <span data-ttu-id="6f92b-139">Per i **tipi di account supportati**, selezionare **account in qualsiasi directory organizzativa o provider di identità. Per l'autenticazione degli utenti con Azure AD B2C.**</span><span class="sxs-lookup"><span data-stu-id="6f92b-139">For **Supported account types**, select **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span> <span data-ttu-id="6f92b-140">(multi-tenant) per questa esperienza.</span><span class="sxs-lookup"><span data-stu-id="6f92b-140">(multi-tenant) for this experience.</span></span>
1. <span data-ttu-id="6f92b-141">Lasciare l'elenco a discesa **URI di reindirizzamento** impostato su **Web**e specificare un URI di `https://localhost:5001/authentication/login-callback`reindirizzamento.</span><span class="sxs-lookup"><span data-stu-id="6f92b-141">Leave the **Redirect URI** drop down set to **Web**, and provide a redirect URI of `https://localhost:5001/authentication/login-callback`.</span></span>
1. <span data-ttu-id="6f92b-142">Verificare che le **autorizzazioni** > **concessino l'amministratore concent a OpenID e offline_access autorizzazioni** siano abilitate.</span><span class="sxs-lookup"><span data-stu-id="6f92b-142">Confirm that **Permissions** > **Grant admin concent to openid and offline_access permissions** is enabled.</span></span>
1. <span data-ttu-id="6f92b-143">Selezionare **Registra**.</span><span class="sxs-lookup"><span data-stu-id="6f92b-143">Select **Register**.</span></span>

<span data-ttu-id="6f92b-144">In **Authentication** >  > **configurazioni piattaforma**di autenticazione**Web**:</span><span class="sxs-lookup"><span data-stu-id="6f92b-144">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="6f92b-145">Verificare che l' **URI** di `https://localhost:5001/authentication/login-callback` Reindirizzamento di sia presente.</span><span class="sxs-lookup"><span data-stu-id="6f92b-145">Confirm the **Redirect URI** of `https://localhost:5001/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="6f92b-146">Per **concessione implicita**, selezionare le caselle di controllo per i token di **accesso** e i **token ID**.</span><span class="sxs-lookup"><span data-stu-id="6f92b-146">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="6f92b-147">Per questa esperienza sono accettabili le impostazioni predefinite rimanenti per l'app.</span><span class="sxs-lookup"><span data-stu-id="6f92b-147">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="6f92b-148">Fare clic sul pulsante **Salva**.</span><span class="sxs-lookup"><span data-stu-id="6f92b-148">Select the **Save** button.</span></span>

<span data-ttu-id="6f92b-149">In **autorizzazioni API**:</span><span class="sxs-lookup"><span data-stu-id="6f92b-149">In **API permissions**:</span></span>

1. <span data-ttu-id="6f92b-150">Verificare che l'app disponga **Microsoft Graph** > autorizzazione**User. Read** .</span><span class="sxs-lookup"><span data-stu-id="6f92b-150">Confirm that the app has **Microsoft Graph** > **User.Read** permission.</span></span>
1. <span data-ttu-id="6f92b-151">Selezionare **Aggiungi un'autorizzazione** seguita da **API personali**.</span><span class="sxs-lookup"><span data-stu-id="6f92b-151">Select **Add a permission** followed by **My APIs**.</span></span>
1. <span data-ttu-id="6f92b-152">Selezionare l' *app per le API server* dalla colonna **nome** , \*\* Blazor ad esempio server AAD B2C\*\*.</span><span class="sxs-lookup"><span data-stu-id="6f92b-152">Select the *Server API app* from the **Name** column (for example, **Blazor Server AAD B2C**).</span></span>
1. <span data-ttu-id="6f92b-153">Aprire l'elenco di **API** .</span><span class="sxs-lookup"><span data-stu-id="6f92b-153">Open the **API** list.</span></span>
1. <span data-ttu-id="6f92b-154">Abilitare l'accesso all'API (ad esempio, `API.Access`).</span><span class="sxs-lookup"><span data-stu-id="6f92b-154">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="6f92b-155">Selezionare **Aggiungi autorizzazioni**.</span><span class="sxs-lookup"><span data-stu-id="6f92b-155">Select **Add permissions**.</span></span>
1. <span data-ttu-id="6f92b-156">Selezionare il pulsante **Concedi contenuto amministratore per {tenant Name}** .</span><span class="sxs-lookup"><span data-stu-id="6f92b-156">Select the **Grant admin content for {TENANT NAME}** button.</span></span> <span data-ttu-id="6f92b-157">Selezionare **Sì** per confermare.</span><span class="sxs-lookup"><span data-stu-id="6f92b-157">Select **Yes** to confirm.</span></span>

<span data-ttu-id="6f92b-158">In **Home** > **Azure ad B2C** > **flussi utente**:</span><span class="sxs-lookup"><span data-stu-id="6f92b-158">In **Home** > **Azure AD B2C** > **User flows**:</span></span>

[<span data-ttu-id="6f92b-159">Creare un flusso utente di iscrizione e accesso</span><span class="sxs-lookup"><span data-stu-id="6f92b-159">Create a sign-up and sign-in user flow</span></span>](/azure/active-directory-b2c/tutorial-create-user-flows)

<span data-ttu-id="6f92b-160">Selezionare almeno l'attributo utente**nome visualizzato** dell' `context.User.Identity.Name` **applicazione Claims** > per popolare nel `LoginDisplay` componente (*Shared/LoginDisplay. Razor*).</span><span class="sxs-lookup"><span data-stu-id="6f92b-160">At a minimum, select the **Application claims** > **Display Name** user attribute to populate the `context.User.Identity.Name` in the `LoginDisplay` component (*Shared/LoginDisplay.razor*).</span></span>

<span data-ttu-id="6f92b-161">Registrare le seguenti informazioni:</span><span class="sxs-lookup"><span data-stu-id="6f92b-161">Record the following information:</span></span>

* <span data-ttu-id="6f92b-162">Registrare l'ID applicazione dell' *app client* (ID client) (ad esempio `33333333-3333-3333-3333-333333333333`,).</span><span class="sxs-lookup"><span data-stu-id="6f92b-162">Record the *Client app* Application ID (Client ID) (for example, `33333333-3333-3333-3333-333333333333`).</span></span>
* <span data-ttu-id="6f92b-163">Registrare il nome del flusso utente di iscrizione e accesso creato per l'app (ad esempio, `B2C_1_signupsignin`).</span><span class="sxs-lookup"><span data-stu-id="6f92b-163">Record the sign-up and sign-in user flow name created for the app (for example, `B2C_1_signupsignin`).</span></span>

### <a name="create-the-app"></a><span data-ttu-id="6f92b-164">Creare l'app</span><span class="sxs-lookup"><span data-stu-id="6f92b-164">Create the app</span></span>

<span data-ttu-id="6f92b-165">Sostituire i segnaposto nel comando seguente con le informazioni registrate in precedenza ed eseguire il comando in una shell dei comandi:</span><span class="sxs-lookup"><span data-stu-id="6f92b-165">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{DOMAIN}" -ho -ssp "{SIGN UP OR SIGN IN POLICY}" --tenant-id "{TENANT ID}"
```

<span data-ttu-id="6f92b-166">Per specificare il percorso di output, che crea una cartella di progetto, se non esiste, includere l'opzione di output nel comando con un percorso (ad `-o BlazorSample`esempio,).</span><span class="sxs-lookup"><span data-stu-id="6f92b-166">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="6f92b-167">Il nome della cartella diventa anche parte del nome del progetto.</span><span class="sxs-lookup"><span data-stu-id="6f92b-167">The folder name also becomes part of the project's name.</span></span>

> [!NOTE]
> <span data-ttu-id="6f92b-168">Passare l'URI ID app all' `app-id-uri` opzione, ma si noti che potrebbe essere necessaria una modifica alla configurazione nell'app client, descritta nella sezione [ambiti dei token di accesso](#access-token-scopes) .</span><span class="sxs-lookup"><span data-stu-id="6f92b-168">Pass the App ID URI to the `app-id-uri` option, but note a configuration change might be required in the client app, which is described in the [Access token scopes](#access-token-scopes) section.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="6f92b-169">Configurazione dell'app Server</span><span class="sxs-lookup"><span data-stu-id="6f92b-169">Server app configuration</span></span>

<span data-ttu-id="6f92b-170">*Questa sezione è relativa all'app **Server** della soluzione.*</span><span class="sxs-lookup"><span data-stu-id="6f92b-170">*This section pertains to the solution's **Server** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="6f92b-171">Pacchetto di autenticazione</span><span class="sxs-lookup"><span data-stu-id="6f92b-171">Authentication package</span></span>

<span data-ttu-id="6f92b-172">Il supporto per l'autenticazione e l'autorizzazione delle chiamate a ASP.NET Core API Web viene fornito da `Microsoft.AspNetCore.Authentication.AzureADB2C.UI`:</span><span class="sxs-lookup"><span data-stu-id="6f92b-172">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the `Microsoft.AspNetCore.Authentication.AzureADB2C.UI`:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureADB2C.UI" 
    Version="3.1.0" />
```

### <a name="authentication-service-support"></a><span data-ttu-id="6f92b-173">Supporto del servizio di autenticazione</span><span class="sxs-lookup"><span data-stu-id="6f92b-173">Authentication service support</span></span>

<span data-ttu-id="6f92b-174">Il `AddAuthentication` metodo imposta i servizi di autenticazione all'interno dell'app e configura il gestore di JWT Bearer come metodo di autenticazione predefinito.</span><span class="sxs-lookup"><span data-stu-id="6f92b-174">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="6f92b-175">Il `AddAzureADB2CBearer` metodo imposta i parametri specifici nel gestore di JWT Bearer necessario per convalidare i token emessi dal Azure Active Directory B2C:</span><span class="sxs-lookup"><span data-stu-id="6f92b-175">The `AddAzureADB2CBearer` method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory B2C:</span></span>

```csharp
services.AddAuthentication(AzureADB2CDefaults.BearerAuthenticationScheme)
    .AddAzureADB2CBearer(options => Configuration.Bind("AzureAdB2C", options));
```

<span data-ttu-id="6f92b-176">`UseAuthentication`e `UseAuthorization` verificare che:</span><span class="sxs-lookup"><span data-stu-id="6f92b-176">`UseAuthentication` and `UseAuthorization` ensure that:</span></span>

* <span data-ttu-id="6f92b-177">L'app tenta di analizzare e convalidare i token nelle richieste in ingresso.</span><span class="sxs-lookup"><span data-stu-id="6f92b-177">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="6f92b-178">Eventuali richieste che tentano di accedere a una risorsa protetta senza credenziali appropriate hanno esito negativo.</span><span class="sxs-lookup"><span data-stu-id="6f92b-178">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="useridentityname"></a><span data-ttu-id="6f92b-179">User.Identity.Name</span><span class="sxs-lookup"><span data-stu-id="6f92b-179">User.Identity.Name</span></span>

<span data-ttu-id="6f92b-180">Per impostazione predefinita, `User.Identity.Name` non viene popolato.</span><span class="sxs-lookup"><span data-stu-id="6f92b-180">By default, the `User.Identity.Name` isn't populated.</span></span>

<span data-ttu-id="6f92b-181">Per configurare l'app per la ricezione del valore dal `name` tipo di attestazione, configurare [TokenValidationParameters. NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) di <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="6f92b-181">To configure the app to receive the value from the `name` claim type, configure the [TokenValidationParameters.NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

```csharp
services.Configure<JwtBearerOptions>(
    AzureADB2CDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a><span data-ttu-id="6f92b-182">Impostazioni app</span><span class="sxs-lookup"><span data-stu-id="6f92b-182">App settings</span></span>

<span data-ttu-id="6f92b-183">Il file *appSettings. JSON* contiene le opzioni per configurare il gestore di connessione JWT usato per convalidare i token di accesso.</span><span class="sxs-lookup"><span data-stu-id="6f92b-183">The *appsettings.json* file contains the options to configure the JWT bearer handler used to validate access tokens.</span></span>

```json
{
  "AzureAd": {
    "Instance": "https://{ORGANIZATION}.b2clogin.com/",
    "ClientId": "{API CLIENT ID}",
    "Domain": "{DOMAIN}",
    "SignUpSignInPolicyId": "{SIGN UP OR SIGN IN POLICY}"
  }
}
```

### <a name="weatherforecast-controller"></a><span data-ttu-id="6f92b-184">Controller WeatherForecast</span><span class="sxs-lookup"><span data-stu-id="6f92b-184">WeatherForecast controller</span></span>

<span data-ttu-id="6f92b-185">Il controller WeatherForecast (*Controllers/WeatherForecastController. cs*) espone un'API protetta con `[Authorize]` l'attributo applicato al controller.</span><span class="sxs-lookup"><span data-stu-id="6f92b-185">The WeatherForecast controller (*Controllers/WeatherForecastController.cs*) exposes a protected API with the `[Authorize]` attribute applied to the controller.</span></span> <span data-ttu-id="6f92b-186">È **importante** comprendere che:</span><span class="sxs-lookup"><span data-stu-id="6f92b-186">It's **important** to understand that:</span></span>

* <span data-ttu-id="6f92b-187">L' `[Authorize]` attributo in questo controller API è l'unico elemento che protegge questa API da accessi non autorizzati.</span><span class="sxs-lookup"><span data-stu-id="6f92b-187">The `[Authorize]` attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="6f92b-188">L' `[Authorize]` attributo usato nell' Blazor app webassembly funge solo da hint per l'app che l'utente deve essere autorizzato affinché l'app funzioni correttamente.</span><span class="sxs-lookup"><span data-stu-id="6f92b-188">The `[Authorize]` attribute used in the Blazor WebAssembly app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

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

## <a name="client-app-configuration"></a><span data-ttu-id="6f92b-189">Configurazione dell'app client</span><span class="sxs-lookup"><span data-stu-id="6f92b-189">Client app configuration</span></span>

<span data-ttu-id="6f92b-190">*Questa sezione riguarda l'app **client** della soluzione.*</span><span class="sxs-lookup"><span data-stu-id="6f92b-190">*This section pertains to the solution's **Client** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="6f92b-191">Pacchetto di autenticazione</span><span class="sxs-lookup"><span data-stu-id="6f92b-191">Authentication package</span></span>

<span data-ttu-id="6f92b-192">Quando viene creata un'app per usare un singolo account B2C (`IndividualB2C`), l'app riceve automaticamente un riferimento al pacchetto per [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span><span class="sxs-lookup"><span data-stu-id="6f92b-192">When an app is created to use an Individual B2C Account (`IndividualB2C`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="6f92b-193">Il pacchetto fornisce un set di primitive che consentono all'app di autenticare gli utenti e ottenere i token per chiamare le API protette.</span><span class="sxs-lookup"><span data-stu-id="6f92b-193">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="6f92b-194">Se si aggiunge l'autenticazione a un'app, aggiungere manualmente il pacchetto al file di progetto dell'app:</span><span class="sxs-lookup"><span data-stu-id="6f92b-194">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="6f92b-195">Sostituire `{VERSION}` nel riferimento al pacchetto precedente con la versione del `Microsoft.AspNetCore.Blazor.Templates` pacchetto illustrata nell' <xref:blazor/get-started> articolo.</span><span class="sxs-lookup"><span data-stu-id="6f92b-195">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="6f92b-196">Il `Microsoft.Authentication.WebAssembly.Msal` pacchetto aggiunge il `Microsoft.AspNetCore.Components.WebAssembly.Authentication` pacchetto all'app in modo transitivo.</span><span class="sxs-lookup"><span data-stu-id="6f92b-196">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="6f92b-197">Supporto del servizio di autenticazione</span><span class="sxs-lookup"><span data-stu-id="6f92b-197">Authentication service support</span></span>

<span data-ttu-id="6f92b-198">Il supporto per l'autenticazione degli utenti viene registrato nel contenitore del servizio `AddMsalAuthentication` con il metodo di estensione `Microsoft.Authentication.WebAssembly.Msal` fornito dal pacchetto.</span><span class="sxs-lookup"><span data-stu-id="6f92b-198">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="6f92b-199">Questo metodo configura tutti i servizi necessari per l'interazione dell'app con il provider di identità (IP).</span><span class="sxs-lookup"><span data-stu-id="6f92b-199">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="6f92b-200">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="6f92b-200">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    var authentication = options.ProviderOptions.Authentication;
    authentication.Authority = 
        "{AAD B2C INSTANCE}{DOMAIN}/{SIGN UP OR SIGN IN POLICY}";
    authentication.ClientId = "{CLIENT ID}";
    authentication.ValidateAuthority = false;
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="6f92b-201">Il `AddMsalAuthentication` metodo accetta un callback per configurare i parametri necessari per autenticare un'app.</span><span class="sxs-lookup"><span data-stu-id="6f92b-201">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="6f92b-202">Quando si registra l'app, è possibile ottenere i valori necessari per la configurazione dell'app dalla configurazione di AAD del portale di Azure.</span><span class="sxs-lookup"><span data-stu-id="6f92b-202">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

### <a name="access-token-scopes"></a><span data-ttu-id="6f92b-203">Ambiti del token di accesso</span><span class="sxs-lookup"><span data-stu-id="6f92b-203">Access token scopes</span></span>

<span data-ttu-id="6f92b-204">Gli ambiti dei token di accesso predefiniti rappresentano l'elenco degli ambiti dei token di accesso:</span><span class="sxs-lookup"><span data-stu-id="6f92b-204">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="6f92b-205">Incluso per impostazione predefinita nella richiesta di accesso.</span><span class="sxs-lookup"><span data-stu-id="6f92b-205">Included by default in the sign in request.</span></span>
* <span data-ttu-id="6f92b-206">Utilizzato per eseguire il provisioning di un token di accesso immediatamente dopo l'autenticazione.</span><span class="sxs-lookup"><span data-stu-id="6f92b-206">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="6f92b-207">Tutti gli ambiti devono appartenere alla stessa app per ogni regola di Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="6f92b-207">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="6f92b-208">Per altre app per le API è possibile aggiungere altri ambiti, se necessario:</span><span class="sxs-lookup"><span data-stu-id="6f92b-208">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="6f92b-209">Se il portale di Azure fornisce un URI di ambito e **l'app genera un'eccezione non gestita** quando riceve una risposta *401 non autorizzata* dall'API, provare a usare un URI dell'ambito che non includa lo schema e l'host.</span><span class="sxs-lookup"><span data-stu-id="6f92b-209">If the Azure portal provides a scope URI and **the app throws an unhandled exception** when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="6f92b-210">Ad esempio, il portale di Azure può fornire uno dei seguenti formati di URI di ambito:</span><span class="sxs-lookup"><span data-stu-id="6f92b-210">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="6f92b-211">Specificare l'URI dell'ambito senza lo schema e l'host:</span><span class="sxs-lookup"><span data-stu-id="6f92b-211">Supply the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

<span data-ttu-id="6f92b-212">Per altre informazioni, vedere <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span><span class="sxs-lookup"><span data-stu-id="6f92b-212">For more information, see <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span></span>

<!--
    For more information, see <xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests>.
-->

### <a name="imports-file"></a><span data-ttu-id="6f92b-213">Importa file</span><span class="sxs-lookup"><span data-stu-id="6f92b-213">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="6f92b-214">Pagina di indice</span><span class="sxs-lookup"><span data-stu-id="6f92b-214">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a><span data-ttu-id="6f92b-215">Componente app</span><span class="sxs-lookup"><span data-stu-id="6f92b-215">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="6f92b-216">Componente RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="6f92b-216">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="6f92b-217">Componente LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="6f92b-217">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="6f92b-218">Componente di autenticazione</span><span class="sxs-lookup"><span data-stu-id="6f92b-218">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="6f92b-219">Componente FetchData</span><span class="sxs-lookup"><span data-stu-id="6f92b-219">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="6f92b-220">Eseguire l'app</span><span class="sxs-lookup"><span data-stu-id="6f92b-220">Run the app</span></span>

<span data-ttu-id="6f92b-221">Eseguire l'app dal progetto server.</span><span class="sxs-lookup"><span data-stu-id="6f92b-221">Run the app from the Server project.</span></span> <span data-ttu-id="6f92b-222">Quando si usa Visual Studio, selezionare il progetto server in **Esplora soluzioni** e selezionare il pulsante **Esegui** sulla barra degli strumenti o avviare l'app dal menu **debug** .</span><span class="sxs-lookup"><span data-stu-id="6f92b-222">When using Visual Studio, select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/wasm-aad-b2c-userflows.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="6f92b-223">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="6f92b-223">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* <xref:security/authentication/azure-ad-b2c>
* [<span data-ttu-id="6f92b-224">Esercitazione: Creare un tenant di Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="6f92b-224">Tutorial: Create an Azure Active Directory B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)
* [<span data-ttu-id="6f92b-225">Documentazione di Microsoft Identity Platform</span><span class="sxs-lookup"><span data-stu-id="6f92b-225">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
