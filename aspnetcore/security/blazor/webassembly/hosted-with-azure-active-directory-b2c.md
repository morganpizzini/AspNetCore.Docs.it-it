---
title: Proteggere un' Blazor app ospitata ASP.NET Core webassembly con Azure Active Directory B2C
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/11/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/blazor/webassembly/hosted-with-azure-active-directory-b2c
ms.openlocfilehash: e8b1a1f86becb1e9f0affe14a667253bd0ec16bf
ms.sourcegitcommit: 1250c90c8d87c2513532be5683640b65bfdf9ddb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83153663"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-hosted-app-with-azure-active-directory-b2c"></a><span data-ttu-id="032bf-102">Proteggere un' Blazor app ospitata ASP.NET Core webassembly con Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="032bf-102">Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory B2C</span></span>

<span data-ttu-id="032bf-103">Di [Javier Calvarro Nelson](https://github.com/javiercn) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="032bf-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="032bf-104">Questo articolo descrive come creare un' Blazor app webassembly autonoma che usa [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) per l'autenticazione.</span><span class="sxs-lookup"><span data-stu-id="032bf-104">This article describes how to create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) for authentication.</span></span>

## <a name="register-apps-in-aad-b2c-and-create-solution"></a><span data-ttu-id="032bf-105">Registrare le app in AAD B2C e creare la soluzione</span><span class="sxs-lookup"><span data-stu-id="032bf-105">Register apps in AAD B2C and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="032bf-106">Creare un tenant</span><span class="sxs-lookup"><span data-stu-id="032bf-106">Create a tenant</span></span>

<span data-ttu-id="032bf-107">Seguire le istruzioni in [esercitazione: creare un tenant di Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-create-tenant) per creare un tenant di AAD B2C e registrare le informazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="032bf-107">Follow the guidance in [Tutorial: Create an Azure Active Directory B2C tenant](/azure/active-directory-b2c/tutorial-create-tenant) to create an AAD B2C tenant and record the following information:</span></span>

* <span data-ttu-id="032bf-108">AAD B2C istanza (ad esempio, `https://contoso.b2clogin.com/` , che include la barra finale)</span><span class="sxs-lookup"><span data-stu-id="032bf-108">AAD B2C instance (for example, `https://contoso.b2clogin.com/`, which includes the trailing slash)</span></span>
* <span data-ttu-id="032bf-109">AAD B2C dominio tenant (ad esempio, `contoso.onmicrosoft.com` )</span><span class="sxs-lookup"><span data-stu-id="032bf-109">AAD B2C Tenant domain (for example, `contoso.onmicrosoft.com`)</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="032bf-110">Registrare un'app per le API server</span><span class="sxs-lookup"><span data-stu-id="032bf-110">Register a server API app</span></span>

<span data-ttu-id="032bf-111">Seguire le istruzioni riportate in [esercitazione: registrare un'applicazione in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) per registrare un'app AAD per l'app per le *API server* nell'area **Azure Active Directory**  >  **registrazioni app** della portale di Azure:</span><span class="sxs-lookup"><span data-stu-id="032bf-111">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) to register an AAD app for the *Server API app* in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="032bf-112">Selezionare **Nuova registrazione**.</span><span class="sxs-lookup"><span data-stu-id="032bf-112">Select **New registration**.</span></span>
1. <span data-ttu-id="032bf-113">Specificare un **nome** per l'app, ad esempio \*\* Blazor Server AAD B2C\*\*.</span><span class="sxs-lookup"><span data-stu-id="032bf-113">Provide a **Name** for the app (for example, **Blazor Server AAD B2C**).</span></span>
1. <span data-ttu-id="032bf-114">Per i **tipi di account supportati**, selezionare **account in qualsiasi directory organizzativa o provider di identità. Per l'autenticazione degli utenti con Azure AD B2C.**</span><span class="sxs-lookup"><span data-stu-id="032bf-114">For **Supported account types**, select **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span> <span data-ttu-id="032bf-115">(multi-tenant) per questa esperienza.</span><span class="sxs-lookup"><span data-stu-id="032bf-115">(multi-tenant) for this experience.</span></span>
1. <span data-ttu-id="032bf-116">L' *app* per le API del server non richiede un **URI di reindirizzamento** in questo scenario, quindi lasciare l'elenco a discesa impostato su **Web** e non immettere un URI di reindirizzamento.</span><span class="sxs-lookup"><span data-stu-id="032bf-116">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="032bf-117">Verificare che **Permissions**  >  **le autorizzazioni concessino l'amministratore concent a OpenID e offline_access autorizzazioni** siano abilitate.</span><span class="sxs-lookup"><span data-stu-id="032bf-117">Confirm that **Permissions** > **Grant admin concent to openid and offline_access permissions** is enabled.</span></span>
1. <span data-ttu-id="032bf-118">Selezionare **Registra**.</span><span class="sxs-lookup"><span data-stu-id="032bf-118">Select **Register**.</span></span>

<span data-ttu-id="032bf-119">In **esporre un'API**:</span><span class="sxs-lookup"><span data-stu-id="032bf-119">In **Expose an API**:</span></span>

1. <span data-ttu-id="032bf-120">Selezionare **Aggiungi un ambito**.</span><span class="sxs-lookup"><span data-stu-id="032bf-120">Select **Add a scope**.</span></span>
1. <span data-ttu-id="032bf-121">Fai clic su **Salva e continua**.</span><span class="sxs-lookup"><span data-stu-id="032bf-121">Select **Save and continue**.</span></span>
1. <span data-ttu-id="032bf-122">Specificare un **nome** per l'ambito (ad esempio, `API.Access` ).</span><span class="sxs-lookup"><span data-stu-id="032bf-122">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="032bf-123">Fornire un **nome visualizzato** per il consenso dell'amministratore, ad esempio `Access API` .</span><span class="sxs-lookup"><span data-stu-id="032bf-123">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="032bf-124">Fornire una **Descrizione del consenso dell'amministratore** , ad esempio `Allows the app to access server app API endpoints.` .</span><span class="sxs-lookup"><span data-stu-id="032bf-124">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="032bf-125">Verificare che lo **stato** sia impostato su **abilitato**.</span><span class="sxs-lookup"><span data-stu-id="032bf-125">Confirm that the **State** is set to **Enabled**.</span></span>
1. <span data-ttu-id="032bf-126">Selezionare **Aggiungi ambito**.</span><span class="sxs-lookup"><span data-stu-id="032bf-126">Select **Add scope**.</span></span>

<span data-ttu-id="032bf-127">Registrare le seguenti informazioni:</span><span class="sxs-lookup"><span data-stu-id="032bf-127">Record the following information:</span></span>

* <span data-ttu-id="032bf-128">*App per le API server* ID applicazione (ID client) (ad esempio, `11111111-1111-1111-1111-111111111111` )</span><span class="sxs-lookup"><span data-stu-id="032bf-128">*Server API app* Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`)</span></span>
* <span data-ttu-id="032bf-129">URI ID app (ad esempio, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111` , `api://11111111-1111-1111-1111-111111111111` o il valore personalizzato specificato)</span><span class="sxs-lookup"><span data-stu-id="032bf-129">App ID URI (for example, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, `api://11111111-1111-1111-1111-111111111111`, or the custom value that you provided)</span></span>
* <span data-ttu-id="032bf-130">ID directory (ID tenant) (ad esempio, `222222222-2222-2222-2222-222222222222` )</span><span class="sxs-lookup"><span data-stu-id="032bf-130">Directory ID (Tenant ID) (for example, `222222222-2222-2222-2222-222222222222`)</span></span>
* <span data-ttu-id="032bf-131">*App per le API server* URI ID app (ad esempio, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111` il portale di Azure potrebbe avere come valore predefinito l'ID client)</span><span class="sxs-lookup"><span data-stu-id="032bf-131">*Server API app* App ID URI (for example, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, the Azure portal might default the value to the Client ID)</span></span>
* <span data-ttu-id="032bf-132">Ambito predefinito (ad esempio, `API.Access` )</span><span class="sxs-lookup"><span data-stu-id="032bf-132">Default scope (for example, `API.Access`)</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="032bf-133">Registrare un'app client</span><span class="sxs-lookup"><span data-stu-id="032bf-133">Register a client app</span></span>

<span data-ttu-id="032bf-134">Seguire le istruzioni riportate in [esercitazione: registrare un'applicazione in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) di nuovo per registrare un'app AAD per l' *app client* nell'area **Azure Active Directory**  >  **registrazioni app** della portale di Azure:</span><span class="sxs-lookup"><span data-stu-id="032bf-134">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) again to register an AAD app for the *Client app* in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="032bf-135">Selezionare **Nuova registrazione**.</span><span class="sxs-lookup"><span data-stu-id="032bf-135">Select **New registration**.</span></span>
1. <span data-ttu-id="032bf-136">Specificare un **nome** per l'app, ad esempio \*\* Blazor AAD B2C client\*\*.</span><span class="sxs-lookup"><span data-stu-id="032bf-136">Provide a **Name** for the app (for example, **Blazor Client AAD B2C**).</span></span>
1. <span data-ttu-id="032bf-137">Per i **tipi di account supportati**, selezionare **account in qualsiasi directory organizzativa o provider di identità. Per l'autenticazione degli utenti con Azure AD B2C.**</span><span class="sxs-lookup"><span data-stu-id="032bf-137">For **Supported account types**, select **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span> <span data-ttu-id="032bf-138">(multi-tenant) per questa esperienza.</span><span class="sxs-lookup"><span data-stu-id="032bf-138">(multi-tenant) for this experience.</span></span>
1. <span data-ttu-id="032bf-139">Lasciare l'elenco a discesa **URI di reindirizzamento** impostato su **Web**e specificare un URI di Reindirizzamento `https://localhost:5001/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="032bf-139">Leave the **Redirect URI** drop down set to **Web**, and provide a redirect URI of `https://localhost:5001/authentication/login-callback`.</span></span>
1. <span data-ttu-id="032bf-140">Verificare che **Permissions**  >  **le autorizzazioni concessino l'amministratore concent a OpenID e offline_access autorizzazioni** siano abilitate.</span><span class="sxs-lookup"><span data-stu-id="032bf-140">Confirm that **Permissions** > **Grant admin concent to openid and offline_access permissions** is enabled.</span></span>
1. <span data-ttu-id="032bf-141">Selezionare **Registra**.</span><span class="sxs-lookup"><span data-stu-id="032bf-141">Select **Register**.</span></span>

<span data-ttu-id="032bf-142">In **Authentication**  >  **configurazioni piattaforma**di autenticazione  >  **Web**:</span><span class="sxs-lookup"><span data-stu-id="032bf-142">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="032bf-143">Verificare che l' **URI di reindirizzamento** di `https://localhost:5001/authentication/login-callback` sia presente.</span><span class="sxs-lookup"><span data-stu-id="032bf-143">Confirm the **Redirect URI** of `https://localhost:5001/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="032bf-144">Per **concessione implicita**, selezionare le caselle di controllo per i token di **accesso** e i **token ID**.</span><span class="sxs-lookup"><span data-stu-id="032bf-144">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="032bf-145">Per questa esperienza sono accettabili le impostazioni predefinite rimanenti per l'app.</span><span class="sxs-lookup"><span data-stu-id="032bf-145">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="032bf-146">Fare clic sul pulsante **Salva**.</span><span class="sxs-lookup"><span data-stu-id="032bf-146">Select the **Save** button.</span></span>

<span data-ttu-id="032bf-147">In **autorizzazioni API**:</span><span class="sxs-lookup"><span data-stu-id="032bf-147">In **API permissions**:</span></span>

1. <span data-ttu-id="032bf-148">Verificare che l'app disponga **Microsoft Graph**  >  autorizzazione**User. Read** .</span><span class="sxs-lookup"><span data-stu-id="032bf-148">Confirm that the app has **Microsoft Graph** > **User.Read** permission.</span></span>
1. <span data-ttu-id="032bf-149">Selezionare **Aggiungi un'autorizzazione** seguita da **API personali**.</span><span class="sxs-lookup"><span data-stu-id="032bf-149">Select **Add a permission** followed by **My APIs**.</span></span>
1. <span data-ttu-id="032bf-150">Selezionare l' *app per le API server* dalla colonna **nome** , ad esempio \*\* Blazor Server AAD B2C\*\*.</span><span class="sxs-lookup"><span data-stu-id="032bf-150">Select the *Server API app* from the **Name** column (for example, **Blazor Server AAD B2C**).</span></span>
1. <span data-ttu-id="032bf-151">Aprire l'elenco di **API** .</span><span class="sxs-lookup"><span data-stu-id="032bf-151">Open the **API** list.</span></span>
1. <span data-ttu-id="032bf-152">Abilitare l'accesso all'API (ad esempio, `API.Access` ).</span><span class="sxs-lookup"><span data-stu-id="032bf-152">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="032bf-153">Selezionare **Aggiungi autorizzazioni**.</span><span class="sxs-lookup"><span data-stu-id="032bf-153">Select **Add permissions**.</span></span>
1. <span data-ttu-id="032bf-154">Selezionare il pulsante **Concedi contenuto amministratore per {tenant Name}** .</span><span class="sxs-lookup"><span data-stu-id="032bf-154">Select the **Grant admin content for {TENANT NAME}** button.</span></span> <span data-ttu-id="032bf-155">Selezionare **Sì** per confermare.</span><span class="sxs-lookup"><span data-stu-id="032bf-155">Select **Yes** to confirm.</span></span>

<span data-ttu-id="032bf-156">In **Home**  >  **Azure ad B2C**  >  **flussi utente**:</span><span class="sxs-lookup"><span data-stu-id="032bf-156">In **Home** > **Azure AD B2C** > **User flows**:</span></span>

[<span data-ttu-id="032bf-157">Creare un flusso utente di iscrizione e accesso</span><span class="sxs-lookup"><span data-stu-id="032bf-157">Create a sign-up and sign-in user flow</span></span>](/azure/active-directory-b2c/tutorial-create-user-flows)

<span data-ttu-id="032bf-158">Selezionare almeno l'attributo utente nome visualizzato dell' **applicazione Claims**  >  **Display Name** per popolare `context.User.Identity.Name` nel `LoginDisplay` componente (*Shared/LoginDisplay. Razor*).</span><span class="sxs-lookup"><span data-stu-id="032bf-158">At a minimum, select the **Application claims** > **Display Name** user attribute to populate the `context.User.Identity.Name` in the `LoginDisplay` component (*Shared/LoginDisplay.razor*).</span></span>

<span data-ttu-id="032bf-159">Registrare le seguenti informazioni:</span><span class="sxs-lookup"><span data-stu-id="032bf-159">Record the following information:</span></span>

* <span data-ttu-id="032bf-160">Registrare l'ID applicazione dell' *app client* (ID client) (ad esempio, `33333333-3333-3333-3333-333333333333` ).</span><span class="sxs-lookup"><span data-stu-id="032bf-160">Record the *Client app* Application ID (Client ID) (for example, `33333333-3333-3333-3333-333333333333`).</span></span>
* <span data-ttu-id="032bf-161">Registrare il nome del flusso utente di iscrizione e accesso creato per l'app (ad esempio, `B2C_1_signupsignin` ).</span><span class="sxs-lookup"><span data-stu-id="032bf-161">Record the sign-up and sign-in user flow name created for the app (for example, `B2C_1_signupsignin`).</span></span>

### <a name="create-the-app"></a><span data-ttu-id="032bf-162">Creare l'app</span><span class="sxs-lookup"><span data-stu-id="032bf-162">Create the app</span></span>

<span data-ttu-id="032bf-163">Sostituire i segnaposto nel comando seguente con le informazioni registrate in precedenza ed eseguire il comando in una shell dei comandi:</span><span class="sxs-lookup"><span data-stu-id="032bf-163">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{DOMAIN}" -ho -ssp "{SIGN UP OR SIGN IN POLICY}" --tenant-id "{TENANT ID}"
```

<span data-ttu-id="032bf-164">Per specificare il percorso di output, che crea una cartella di progetto, se non esiste, includere l'opzione di output nel comando con un percorso (ad esempio, `-o BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="032bf-164">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="032bf-165">Il nome della cartella diventa anche parte del nome del progetto.</span><span class="sxs-lookup"><span data-stu-id="032bf-165">The folder name also becomes part of the project's name.</span></span>

> [!NOTE]
> <span data-ttu-id="032bf-166">Passare l'URI ID app all' `app-id-uri` opzione, ma si noti che potrebbe essere necessaria una modifica alla configurazione nell'app client, descritta nella sezione [ambiti dei token di accesso](#access-token-scopes) .</span><span class="sxs-lookup"><span data-stu-id="032bf-166">Pass the App ID URI to the `app-id-uri` option, but note a configuration change might be required in the client app, which is described in the [Access token scopes](#access-token-scopes) section.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="032bf-167">Configurazione dell'app Server</span><span class="sxs-lookup"><span data-stu-id="032bf-167">Server app configuration</span></span>

<span data-ttu-id="032bf-168">*Questa sezione è relativa all'app **Server** della soluzione.*</span><span class="sxs-lookup"><span data-stu-id="032bf-168">*This section pertains to the solution's **Server** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="032bf-169">Pacchetto di autenticazione</span><span class="sxs-lookup"><span data-stu-id="032bf-169">Authentication package</span></span>

<span data-ttu-id="032bf-170">Il supporto per l'autenticazione e l'autorizzazione delle chiamate a ASP.NET Core API Web viene fornito da `Microsoft.AspNetCore.Authentication.AzureADB2C.UI` :</span><span class="sxs-lookup"><span data-stu-id="032bf-170">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the `Microsoft.AspNetCore.Authentication.AzureADB2C.UI`:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureADB2C.UI" 
    Version="{VERSION}" />
```

### <a name="authentication-service-support"></a><span data-ttu-id="032bf-171">Supporto del servizio di autenticazione</span><span class="sxs-lookup"><span data-stu-id="032bf-171">Authentication service support</span></span>

<span data-ttu-id="032bf-172">Il `AddAuthentication` metodo imposta i servizi di autenticazione all'interno dell'app e configura il gestore di JWT Bearer come metodo di autenticazione predefinito.</span><span class="sxs-lookup"><span data-stu-id="032bf-172">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="032bf-173">Il `AddAzureADB2CBearer` metodo imposta i parametri specifici nel gestore di JWT Bearer necessario per convalidare i token emessi dal Azure Active Directory B2C:</span><span class="sxs-lookup"><span data-stu-id="032bf-173">The `AddAzureADB2CBearer` method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory B2C:</span></span>

```csharp
services.AddAuthentication(AzureADB2CDefaults.BearerAuthenticationScheme)
    .AddAzureADB2CBearer(options => Configuration.Bind("AzureAdB2C", options));
```

<span data-ttu-id="032bf-174">`UseAuthentication`e `UseAuthorization` verificare che:</span><span class="sxs-lookup"><span data-stu-id="032bf-174">`UseAuthentication` and `UseAuthorization` ensure that:</span></span>

* <span data-ttu-id="032bf-175">L'app tenta di analizzare e convalidare i token nelle richieste in ingresso.</span><span class="sxs-lookup"><span data-stu-id="032bf-175">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="032bf-176">Eventuali richieste che tentano di accedere a una risorsa protetta senza credenziali appropriate hanno esito negativo.</span><span class="sxs-lookup"><span data-stu-id="032bf-176">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="useridentityname"></a><span data-ttu-id="032bf-177">Utente. Identity . Nome</span><span class="sxs-lookup"><span data-stu-id="032bf-177">User.Identity.Name</span></span>

<span data-ttu-id="032bf-178">Per impostazione predefinita, `User.Identity.Name` non viene popolato.</span><span class="sxs-lookup"><span data-stu-id="032bf-178">By default, the `User.Identity.Name` isn't populated.</span></span>

<span data-ttu-id="032bf-179">Per configurare l'app per la ricezione del valore dal `name` tipo di attestazione, configurare [TokenValidationParameters. NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) di <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="032bf-179">To configure the app to receive the value from the `name` claim type, configure the [TokenValidationParameters.NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

```csharp
services.Configure<JwtBearerOptions>(
    AzureADB2CDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a><span data-ttu-id="032bf-180">Impostazioni dell'app</span><span class="sxs-lookup"><span data-stu-id="032bf-180">App settings</span></span>

<span data-ttu-id="032bf-181">Il file *appSettings. JSON* contiene le opzioni per configurare il gestore di connessione JWT usato per convalidare i token di accesso.</span><span class="sxs-lookup"><span data-stu-id="032bf-181">The *appsettings.json* file contains the options to configure the JWT bearer handler used to validate access tokens.</span></span>

```json
{
  "AzureAdB2C": {
    "Instance": "https://{ORGANIZATION}.b2clogin.com/",
    "ClientId": "{SERVER API APP CLIENT ID}",
    "Domain": "{DOMAIN}",
    "SignUpSignInPolicyId": "{SIGN UP OR SIGN IN POLICY}"
  }
}
```

<span data-ttu-id="032bf-182">Esempio:</span><span class="sxs-lookup"><span data-stu-id="032bf-182">Example:</span></span>

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

### <a name="weatherforecast-controller"></a><span data-ttu-id="032bf-183">Controller WeatherForecast</span><span class="sxs-lookup"><span data-stu-id="032bf-183">WeatherForecast controller</span></span>

<span data-ttu-id="032bf-184">Il controller WeatherForecast (*Controllers/WeatherForecastController. cs*) espone un'API protetta con l' `[Authorize]` attributo applicato al controller.</span><span class="sxs-lookup"><span data-stu-id="032bf-184">The WeatherForecast controller (*Controllers/WeatherForecastController.cs*) exposes a protected API with the `[Authorize]` attribute applied to the controller.</span></span> <span data-ttu-id="032bf-185">È **importante** comprendere che:</span><span class="sxs-lookup"><span data-stu-id="032bf-185">It's **important** to understand that:</span></span>

* <span data-ttu-id="032bf-186">L' `[Authorize]` attributo in questo controller API è l'unico elemento che protegge questa API da accessi non autorizzati.</span><span class="sxs-lookup"><span data-stu-id="032bf-186">The `[Authorize]` attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="032bf-187">L' `[Authorize]` attributo usato nell' Blazor app webassembly funge solo da hint per l'app che l'utente deve essere autorizzato affinché l'app funzioni correttamente.</span><span class="sxs-lookup"><span data-stu-id="032bf-187">The `[Authorize]` attribute used in the Blazor WebAssembly app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

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

## <a name="client-app-configuration"></a><span data-ttu-id="032bf-188">Configurazione dell'app client</span><span class="sxs-lookup"><span data-stu-id="032bf-188">Client app configuration</span></span>

<span data-ttu-id="032bf-189">*Questa sezione riguarda l'app **client** della soluzione.*</span><span class="sxs-lookup"><span data-stu-id="032bf-189">*This section pertains to the solution's **Client** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="032bf-190">Pacchetto di autenticazione</span><span class="sxs-lookup"><span data-stu-id="032bf-190">Authentication package</span></span>

<span data-ttu-id="032bf-191">Quando viene creata un'app per usare un singolo account B2C ( `IndividualB2C` ), l'app riceve automaticamente un riferimento al pacchetto per [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ( `Microsoft.Authentication.WebAssembly.Msal` ).</span><span class="sxs-lookup"><span data-stu-id="032bf-191">When an app is created to use an Individual B2C Account (`IndividualB2C`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="032bf-192">Il pacchetto fornisce un set di primitive che consentono all'app di autenticare gli utenti e ottenere i token per chiamare le API protette.</span><span class="sxs-lookup"><span data-stu-id="032bf-192">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="032bf-193">Se si aggiunge l'autenticazione a un'app, aggiungere manualmente il pacchetto al file di progetto dell'app:</span><span class="sxs-lookup"><span data-stu-id="032bf-193">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="032bf-194">Sostituire `{VERSION}` nel riferimento al pacchetto precedente con la versione del `Microsoft.AspNetCore.Blazor.Templates` pacchetto illustrata nell' <xref:blazor/get-started> articolo.</span><span class="sxs-lookup"><span data-stu-id="032bf-194">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="032bf-195">Il `Microsoft.Authentication.WebAssembly.Msal` pacchetto aggiunge il `Microsoft.AspNetCore.Components.WebAssembly.Authentication` pacchetto all'app in modo transitivo.</span><span class="sxs-lookup"><span data-stu-id="032bf-195">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="032bf-196">Supporto del servizio di autenticazione</span><span class="sxs-lookup"><span data-stu-id="032bf-196">Authentication service support</span></span>

<span data-ttu-id="032bf-197">`HttpClient`Viene aggiunto il supporto per le istanze di che includono token di accesso quando si effettuano richieste al progetto server.</span><span class="sxs-lookup"><span data-stu-id="032bf-197">Support for `HttpClient` instances is added that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="032bf-198">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="032bf-198">*Program.cs*:</span></span>

```csharp
builder.Services.AddHttpClient("{APP ASSEMBLY}.ServerAPI", client => 
        client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddTransient(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("{APP ASSEMBLY}.ServerAPI"));
```

<span data-ttu-id="032bf-199">Il supporto per l'autenticazione degli utenti viene registrato nel contenitore del servizio con il `AddMsalAuthentication` metodo di estensione fornito dal `Microsoft.Authentication.WebAssembly.Msal` pacchetto.</span><span class="sxs-lookup"><span data-stu-id="032bf-199">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="032bf-200">Questo metodo configura tutti i servizi necessari per l'interazione dell'app con il Identity provider (IP).</span><span class="sxs-lookup"><span data-stu-id="032bf-200">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="032bf-201">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="032bf-201">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAdB2C", options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="032bf-202">Il `AddMsalAuthentication` metodo accetta un callback per configurare i parametri necessari per autenticare un'app.</span><span class="sxs-lookup"><span data-stu-id="032bf-202">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="032bf-203">Quando si registra l'app, è possibile ottenere i valori necessari per la configurazione dell'app dalla configurazione di AAD del portale di Azure.</span><span class="sxs-lookup"><span data-stu-id="032bf-203">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

<span data-ttu-id="032bf-204">La configurazione viene fornita dal file *wwwroot/appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="032bf-204">Configuration is supplied by the *wwwroot/appsettings.json* file:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "{AAD B2C INSTANCE}{DOMAIN}/{SIGN UP OR SIGN IN POLICY}",
    "ClientId": "{CLIENT APP CLIENT ID}",
    "ValidateAuthority": false
  }
}
```

<span data-ttu-id="032bf-205">Esempio:</span><span class="sxs-lookup"><span data-stu-id="032bf-205">Example:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "https://contoso.b2clogin.com/contoso.onmicrosoft.com/B2C_1_signupsignin1",
    "ClientId": "4369008b-21fa-427c-abaa-9b53bf58e538",
    "ValidateAuthority": false
  }
}
```

### <a name="access-token-scopes"></a><span data-ttu-id="032bf-206">Ambiti del token di accesso</span><span class="sxs-lookup"><span data-stu-id="032bf-206">Access token scopes</span></span>

<span data-ttu-id="032bf-207">Gli ambiti dei token di accesso predefiniti rappresentano l'elenco degli ambiti dei token di accesso:</span><span class="sxs-lookup"><span data-stu-id="032bf-207">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="032bf-208">Incluso per impostazione predefinita nella richiesta di accesso.</span><span class="sxs-lookup"><span data-stu-id="032bf-208">Included by default in the sign in request.</span></span>
* <span data-ttu-id="032bf-209">Utilizzato per eseguire il provisioning di un token di accesso immediatamente dopo l'autenticazione.</span><span class="sxs-lookup"><span data-stu-id="032bf-209">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="032bf-210">Tutti gli ambiti devono appartenere alla stessa app per ogni regola di Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="032bf-210">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="032bf-211">Per altre app per le API è possibile aggiungere altri ambiti, se necessario:</span><span class="sxs-lookup"><span data-stu-id="032bf-211">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="032bf-212">Se il portale di Azure fornisce un URI di ambito e **l'app genera un'eccezione non gestita** quando riceve una risposta *401 non autorizzata* dall'API, provare a usare un URI dell'ambito che non includa lo schema e l'host.</span><span class="sxs-lookup"><span data-stu-id="032bf-212">If the Azure portal provides a scope URI and **the app throws an unhandled exception** when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="032bf-213">Ad esempio, il portale di Azure può fornire uno dei seguenti formati di URI di ambito:</span><span class="sxs-lookup"><span data-stu-id="032bf-213">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="032bf-214">Specificare l'URI dell'ambito senza lo schema e l'host:</span><span class="sxs-lookup"><span data-stu-id="032bf-214">Supply the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

<span data-ttu-id="032bf-215">Per ulteriori informazioni, vedere le sezioni seguenti dell'articolo *scenari aggiuntivi* :</span><span class="sxs-lookup"><span data-stu-id="032bf-215">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="032bf-216">Richiedere token di accesso aggiuntivi</span><span class="sxs-lookup"><span data-stu-id="032bf-216">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="032bf-217">Connetti token alle richieste in uscita</span><span class="sxs-lookup"><span data-stu-id="032bf-217">Attach tokens to outgoing requests</span></span>](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)


### <a name="imports-file"></a><span data-ttu-id="032bf-218">Importa file</span><span class="sxs-lookup"><span data-stu-id="032bf-218">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="032bf-219">Pagina di indice</span><span class="sxs-lookup"><span data-stu-id="032bf-219">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a><span data-ttu-id="032bf-220">Componente app</span><span class="sxs-lookup"><span data-stu-id="032bf-220">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="032bf-221">Componente RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="032bf-221">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="032bf-222">Componente LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="032bf-222">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="032bf-223">Componente di autenticazione</span><span class="sxs-lookup"><span data-stu-id="032bf-223">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="032bf-224">Componente FetchData</span><span class="sxs-lookup"><span data-stu-id="032bf-224">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="032bf-225">Eseguire l'app</span><span class="sxs-lookup"><span data-stu-id="032bf-225">Run the app</span></span>

<span data-ttu-id="032bf-226">Eseguire l'app dal progetto server.</span><span class="sxs-lookup"><span data-stu-id="032bf-226">Run the app from the Server project.</span></span> <span data-ttu-id="032bf-227">Quando si usa Visual Studio, selezionare il progetto server in **Esplora soluzioni** e selezionare il pulsante **Esegui** sulla barra degli strumenti o avviare l'app dal menu **debug** .</span><span class="sxs-lookup"><span data-stu-id="032bf-227">When using Visual Studio, select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/wasm-aad-b2c-userflows.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="032bf-228">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="032bf-228">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* [<span data-ttu-id="032bf-229">Richieste API Web non autenticate o non autorizzate in un'app con un client predefinito sicuro</span><span class="sxs-lookup"><span data-stu-id="032bf-229">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:security/blazor/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/authentication/azure-ad-b2c>
* [<span data-ttu-id="032bf-230">Esercitazione: Creare un tenant di Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="032bf-230">Tutorial: Create an Azure Active Directory B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)
* [<span data-ttu-id="032bf-231">Documentazione di Microsoft Identity Platform</span><span class="sxs-lookup"><span data-stu-id="032bf-231">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
