---
title: Proteggere un'app Blazor ospitata ASP.NET Core webassembly con Azure Active Directory
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/23/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/hosted-with-azure-active-directory
ms.openlocfilehash: 8c24546da50607d692a9cdc9f9c007d6ac8645ad
ms.sourcegitcommit: 7bb14d005155a5044c7902a08694ee8ccb20c113
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82110928"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-hosted-app-with-azure-active-directory"></a><span data-ttu-id="05bf2-102">Proteggere un'app Blazor ospitata ASP.NET Core webassembly con Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="05bf2-102">Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory</span></span>

<span data-ttu-id="05bf2-103">Di [Javier Calvarro Nelson](https://github.com/javiercn) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="05bf2-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

> [!NOTE]
> <span data-ttu-id="05bf2-104">Le indicazioni fornite in questo articolo si applicano a ASP.NET Core 3,2 Preview 4.</span><span class="sxs-lookup"><span data-stu-id="05bf2-104">The guidance in this article applies to ASP.NET Core 3.2 Preview 4.</span></span> <span data-ttu-id="05bf2-105">Questo argomento verrà aggiornato per coprire l'anteprima 5 il venerdì 24 aprile.</span><span class="sxs-lookup"><span data-stu-id="05bf2-105">This topic will be updated to cover Preview 5 on Friday, April 24.</span></span>

<span data-ttu-id="05bf2-106">Questo articolo descrive come creare un' [ Blazor app ospitata da webassembly](xref:blazor/hosting-models#blazor-webassembly) che usa [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) per l'autenticazione.</span><span class="sxs-lookup"><span data-stu-id="05bf2-106">This article describes how to create a [Blazor WebAssembly hosted app](xref:blazor/hosting-models#blazor-webassembly) that uses [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) for authentication.</span></span>

## <a name="register-apps-in-aad-b2c-and-create-solution"></a><span data-ttu-id="05bf2-107">Registrare le app in AAD B2C e creare la soluzione</span><span class="sxs-lookup"><span data-stu-id="05bf2-107">Register apps in AAD B2C and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="05bf2-108">Creare un tenant</span><span class="sxs-lookup"><span data-stu-id="05bf2-108">Create a tenant</span></span>

<span data-ttu-id="05bf2-109">Seguire le istruzioni riportate nella [Guida introduttiva: configurare un tenant](/azure/active-directory/develop/quickstart-create-new-tenant) per creare un tenant in AAD.</span><span class="sxs-lookup"><span data-stu-id="05bf2-109">Follow the guidance in [Quickstart: Set up a tenant](/azure/active-directory/develop/quickstart-create-new-tenant) to create a tenant in AAD.</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="05bf2-110">Registrare un'app per le API server</span><span class="sxs-lookup"><span data-stu-id="05bf2-110">Register a server API app</span></span>

<span data-ttu-id="05bf2-111">Seguire le istruzioni riportate nella [Guida introduttiva: registrare un'applicazione con la piattaforma di identità Microsoft](/azure/active-directory/develop/quickstart-register-app) e gli argomenti di Azure AAD successivi per registrare un'app AAD per l'app per le *API server* nell'area **Azure Active Directory** > **registrazioni app** del portale di Azure:</span><span class="sxs-lookup"><span data-stu-id="05bf2-111">Follow the guidance in [Quickstart: Register an application with the Microsoft identity platform](/azure/active-directory/develop/quickstart-register-app) and subsequent Azure AAD topics to register an AAD app for the *Server API app* in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="05bf2-112">Selezionare **Nuova registrazione**.</span><span class="sxs-lookup"><span data-stu-id="05bf2-112">Select **New registration**.</span></span>
1. <span data-ttu-id="05bf2-113">Specificare un **nome** per l'app (ad esempio, \*\* Blazor server AAD\*\*).</span><span class="sxs-lookup"><span data-stu-id="05bf2-113">Provide a **Name** for the app (for example, **Blazor Server AAD**).</span></span>
1. <span data-ttu-id="05bf2-114">Scegliere i **tipi di conto supportati**.</span><span class="sxs-lookup"><span data-stu-id="05bf2-114">Choose a **Supported account types**.</span></span> <span data-ttu-id="05bf2-115">Per questa esperienza è possibile selezionare **solo gli account in questa directory aziendale** (tenant singolo).</span><span class="sxs-lookup"><span data-stu-id="05bf2-115">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="05bf2-116">L' *app* per le API del server non richiede un **URI di reindirizzamento** in questo scenario, quindi lasciare l'elenco a discesa impostato su **Web** e non immettere un URI di reindirizzamento.</span><span class="sxs-lookup"><span data-stu-id="05bf2-116">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="05bf2-117">Disabilitare la casella di controllo **autorizzazioni** > **concessi da amministratore a OpenID e autorizzazioni offline_access** .</span><span class="sxs-lookup"><span data-stu-id="05bf2-117">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="05bf2-118">Selezionare **Registra**.</span><span class="sxs-lookup"><span data-stu-id="05bf2-118">Select **Register**.</span></span>

<span data-ttu-id="05bf2-119">In **autorizzazioni API**rimuovere l'autorizzazione **Microsoft Graph** > **utente. Read** , perché l'app non richiede l'accesso al profilo di accesso o UER.</span><span class="sxs-lookup"><span data-stu-id="05bf2-119">In **API permissions**, remove the **Microsoft Graph** > **User.Read** permission, as the app doesn't require sign in or uer profile access.</span></span>

<span data-ttu-id="05bf2-120">In **esporre un'API**:</span><span class="sxs-lookup"><span data-stu-id="05bf2-120">In **Expose an API**:</span></span>

1. <span data-ttu-id="05bf2-121">Selezionare **Aggiungi un ambito**.</span><span class="sxs-lookup"><span data-stu-id="05bf2-121">Select **Add a scope**.</span></span>
1. <span data-ttu-id="05bf2-122">Fai clic su **Salva e continua**.</span><span class="sxs-lookup"><span data-stu-id="05bf2-122">Select **Save and continue**.</span></span>
1. <span data-ttu-id="05bf2-123">Specificare un **nome** per l'ambito (ad `API.Access`esempio,).</span><span class="sxs-lookup"><span data-stu-id="05bf2-123">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="05bf2-124">Fornire un **nome visualizzato** per il consenso dell'amministratore, `Access API`ad esempio.</span><span class="sxs-lookup"><span data-stu-id="05bf2-124">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="05bf2-125">Fornire una **Descrizione del consenso dell'amministratore** , `Allows the app to access server app API endpoints.`ad esempio.</span><span class="sxs-lookup"><span data-stu-id="05bf2-125">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="05bf2-126">Verificare che lo **stato** sia impostato su **abilitato**.</span><span class="sxs-lookup"><span data-stu-id="05bf2-126">Confirm that the **State** is set to **Enabled**.</span></span>
1. <span data-ttu-id="05bf2-127">Selezionare **Aggiungi ambito**.</span><span class="sxs-lookup"><span data-stu-id="05bf2-127">Select **Add scope**.</span></span>

<span data-ttu-id="05bf2-128">Registrare le seguenti informazioni:</span><span class="sxs-lookup"><span data-stu-id="05bf2-128">Record the following information:</span></span>

* <span data-ttu-id="05bf2-129">*App per le API server* ID applicazione (ID client) (ad esempio, `11111111-1111-1111-1111-111111111111`)</span><span class="sxs-lookup"><span data-stu-id="05bf2-129">*Server API app* Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`)</span></span>
* <span data-ttu-id="05bf2-130">URI ID app (ad esempio `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111` `api://11111111-1111-1111-1111-111111111111`,, o il valore personalizzato specificato)</span><span class="sxs-lookup"><span data-stu-id="05bf2-130">App ID URI (for example, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, `api://11111111-1111-1111-1111-111111111111`, or the custom value that you provided)</span></span>
* <span data-ttu-id="05bf2-131">ID directory (ID tenant) (ad esempio, `222222222-2222-2222-2222-222222222222`)</span><span class="sxs-lookup"><span data-stu-id="05bf2-131">Directory ID (Tenant ID) (for example, `222222222-2222-2222-2222-222222222222`)</span></span>
* <span data-ttu-id="05bf2-132">Dominio del tenant AAD (ad esempio `contoso.onmicrosoft.com`,)</span><span class="sxs-lookup"><span data-stu-id="05bf2-132">AAD Tenant domain (for example, `contoso.onmicrosoft.com`)</span></span>
* <span data-ttu-id="05bf2-133">Ambito predefinito (ad esempio, `API.Access`)</span><span class="sxs-lookup"><span data-stu-id="05bf2-133">Default scope (for example, `API.Access`)</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="05bf2-134">Registrare un'app client</span><span class="sxs-lookup"><span data-stu-id="05bf2-134">Register a client app</span></span>

<span data-ttu-id="05bf2-135">Seguire le istruzioni disponibili in [Guida introduttiva: registrare un'applicazione con la piattaforma di identità Microsoft](/azure/active-directory/develop/quickstart-register-app) e gli argomenti di Azure AAD successivi per registrare un'app AAD per l' *app client* nell'area **Azure Active Directory** > **registrazioni app** del portale di Azure:</span><span class="sxs-lookup"><span data-stu-id="05bf2-135">Follow the guidance in [Quickstart: Register an application with the Microsoft identity platform](/azure/active-directory/develop/quickstart-register-app) and subsequent Azure AAD topics to register a AAD app for the *Client app* in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="05bf2-136">Selezionare **Nuova registrazione**.</span><span class="sxs-lookup"><span data-stu-id="05bf2-136">Select **New registration**.</span></span>
1. <span data-ttu-id="05bf2-137">Specificare un **nome** per l'app (ad esempio, \*\* Blazor client AAD\*\*).</span><span class="sxs-lookup"><span data-stu-id="05bf2-137">Provide a **Name** for the app (for example, **Blazor Client AAD**).</span></span>
1. <span data-ttu-id="05bf2-138">Scegliere i **tipi di conto supportati**.</span><span class="sxs-lookup"><span data-stu-id="05bf2-138">Choose a **Supported account types**.</span></span> <span data-ttu-id="05bf2-139">Per questa esperienza è possibile selezionare **solo gli account in questa directory aziendale** (tenant singolo).</span><span class="sxs-lookup"><span data-stu-id="05bf2-139">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="05bf2-140">Lasciare l'elenco a discesa **URI di reindirizzamento** impostato su **Web**e specificare un URI di `https://localhost:5001/authentication/login-callback`reindirizzamento.</span><span class="sxs-lookup"><span data-stu-id="05bf2-140">Leave the **Redirect URI** drop down set to **Web**, and provide a redirect URI of `https://localhost:5001/authentication/login-callback`.</span></span>
1. <span data-ttu-id="05bf2-141">Disabilitare la casella di controllo **autorizzazioni** > **concessi da amministratore a OpenID e autorizzazioni offline_access** .</span><span class="sxs-lookup"><span data-stu-id="05bf2-141">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="05bf2-142">Selezionare **Registra**.</span><span class="sxs-lookup"><span data-stu-id="05bf2-142">Select **Register**.</span></span>

<span data-ttu-id="05bf2-143">In **Authentication** >  > **configurazioni piattaforma**di autenticazione**Web**:</span><span class="sxs-lookup"><span data-stu-id="05bf2-143">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="05bf2-144">Verificare che l' **URI** di `https://localhost:5001/authentication/login-callback` Reindirizzamento di sia presente.</span><span class="sxs-lookup"><span data-stu-id="05bf2-144">Confirm the **Redirect URI** of `https://localhost:5001/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="05bf2-145">Per **concessione implicita**, selezionare le caselle di controllo per i token di **accesso** e i **token ID**.</span><span class="sxs-lookup"><span data-stu-id="05bf2-145">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="05bf2-146">Per questa esperienza sono accettabili le impostazioni predefinite rimanenti per l'app.</span><span class="sxs-lookup"><span data-stu-id="05bf2-146">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="05bf2-147">Fare clic sul pulsante **Salva**.</span><span class="sxs-lookup"><span data-stu-id="05bf2-147">Select the **Save** button.</span></span>

<span data-ttu-id="05bf2-148">In **autorizzazioni API**:</span><span class="sxs-lookup"><span data-stu-id="05bf2-148">In **API permissions**:</span></span>

1. <span data-ttu-id="05bf2-149">Verificare che l'app disponga **Microsoft Graph** > autorizzazione**User. Read** .</span><span class="sxs-lookup"><span data-stu-id="05bf2-149">Confirm that the app has **Microsoft Graph** > **User.Read** permission.</span></span>
1. <span data-ttu-id="05bf2-150">Selezionare **Aggiungi un'autorizzazione** seguita da **API personali**.</span><span class="sxs-lookup"><span data-stu-id="05bf2-150">Select **Add a permission** followed by **My APIs**.</span></span>
1. <span data-ttu-id="05bf2-151">Selezionare l' *app per le API server* dalla colonna **nome** (ad esempio, \*\* Blazor server AAD\*\*).</span><span class="sxs-lookup"><span data-stu-id="05bf2-151">Select the *Server API app* from the **Name** column (for example, **Blazor Server AAD**).</span></span>
1. <span data-ttu-id="05bf2-152">Aprire l'elenco di **API** .</span><span class="sxs-lookup"><span data-stu-id="05bf2-152">Open the **API** list.</span></span>
1. <span data-ttu-id="05bf2-153">Abilitare l'accesso all'API (ad esempio, `API.Access`).</span><span class="sxs-lookup"><span data-stu-id="05bf2-153">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="05bf2-154">Selezionare **Aggiungi autorizzazioni**.</span><span class="sxs-lookup"><span data-stu-id="05bf2-154">Select **Add permissions**.</span></span>
1. <span data-ttu-id="05bf2-155">Selezionare il pulsante **Concedi contenuto amministratore per {tenant Name}** .</span><span class="sxs-lookup"><span data-stu-id="05bf2-155">Select the **Grant admin content for {TENANT NAME}** button.</span></span> <span data-ttu-id="05bf2-156">Selezionare **Sì** per confermare.</span><span class="sxs-lookup"><span data-stu-id="05bf2-156">Select **Yes** to confirm.</span></span>

<span data-ttu-id="05bf2-157">Registrare l'ID applicazione dell' *app client* (ID client) (ad esempio `33333333-3333-3333-3333-333333333333`,).</span><span class="sxs-lookup"><span data-stu-id="05bf2-157">Record the *Client app* Application ID (Client ID) (for example, `33333333-3333-3333-3333-333333333333`).</span></span>

### <a name="create-the-app"></a><span data-ttu-id="05bf2-158">Creare l'app</span><span class="sxs-lookup"><span data-stu-id="05bf2-158">Create the app</span></span>

<span data-ttu-id="05bf2-159">Sostituire i segnaposto nel comando seguente con le informazioni registrate in precedenza ed eseguire il comando in una shell dei comandi:</span><span class="sxs-lookup"><span data-stu-id="05bf2-159">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{DOMAIN}" -ho --tenant-id "{TENANT ID}"
```

<span data-ttu-id="05bf2-160">Per specificare il percorso di output, che crea una cartella di progetto, se non esiste, includere l'opzione di output nel comando con un percorso (ad `-o BlazorSample`esempio,).</span><span class="sxs-lookup"><span data-stu-id="05bf2-160">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="05bf2-161">Il nome della cartella diventa anche parte del nome del progetto.</span><span class="sxs-lookup"><span data-stu-id="05bf2-161">The folder name also becomes part of the project's name.</span></span>

> [!NOTE]
> <span data-ttu-id="05bf2-162">Passare l'URI ID app all' `app-id-uri` opzione, ma si noti che potrebbe essere necessaria una modifica alla configurazione nell'app client, descritta nella sezione [ambiti dei token di accesso](#access-token-scopes) .</span><span class="sxs-lookup"><span data-stu-id="05bf2-162">Pass the App ID URI to the `app-id-uri` option, but note a configuration change might be required in the client app, which is described in the [Access token scopes](#access-token-scopes) section.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="05bf2-163">Configurazione dell'app Server</span><span class="sxs-lookup"><span data-stu-id="05bf2-163">Server app configuration</span></span>

<span data-ttu-id="05bf2-164">*Questa sezione è relativa all'app **Server** della soluzione.*</span><span class="sxs-lookup"><span data-stu-id="05bf2-164">*This section pertains to the solution's **Server** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="05bf2-165">Pacchetto di autenticazione</span><span class="sxs-lookup"><span data-stu-id="05bf2-165">Authentication package</span></span>

<span data-ttu-id="05bf2-166">Il supporto per l'autenticazione e l'autorizzazione delle chiamate a ASP.NET Core API Web viene fornito da `Microsoft.AspNetCore.Authentication.AzureAD.UI`:</span><span class="sxs-lookup"><span data-stu-id="05bf2-166">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the `Microsoft.AspNetCore.Authentication.AzureAD.UI`:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureAD.UI" 
    Version="3.1.0" />
```

### <a name="authentication-service-support"></a><span data-ttu-id="05bf2-167">Supporto del servizio di autenticazione</span><span class="sxs-lookup"><span data-stu-id="05bf2-167">Authentication service support</span></span>

<span data-ttu-id="05bf2-168">Il `AddAuthentication` metodo imposta i servizi di autenticazione all'interno dell'app e configura il gestore di JWT Bearer come metodo di autenticazione predefinito.</span><span class="sxs-lookup"><span data-stu-id="05bf2-168">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="05bf2-169">Il `AddAzureADBearer` metodo imposta i parametri specifici nel gestore di JWT Bearer necessario per convalidare i token emessi dal Azure Active Directory:</span><span class="sxs-lookup"><span data-stu-id="05bf2-169">The `AddAzureADBearer` method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory:</span></span>

```csharp
services.AddAuthentication(AzureADDefaults.BearerAuthenticationScheme)
    .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
```

<span data-ttu-id="05bf2-170">`UseAuthentication`e `UseAuthorization` verificare che:</span><span class="sxs-lookup"><span data-stu-id="05bf2-170">`UseAuthentication` and `UseAuthorization` ensure that:</span></span>

* <span data-ttu-id="05bf2-171">L'app tenta di analizzare e convalidare i token nelle richieste in ingresso.</span><span class="sxs-lookup"><span data-stu-id="05bf2-171">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="05bf2-172">Eventuali richieste che tentano di accedere a una risorsa protetta senza credenziali appropriate hanno esito negativo.</span><span class="sxs-lookup"><span data-stu-id="05bf2-172">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="useridentityname"></a><span data-ttu-id="05bf2-173">User.Identity.Name</span><span class="sxs-lookup"><span data-stu-id="05bf2-173">User.Identity.Name</span></span>

<span data-ttu-id="05bf2-174">Per impostazione predefinita, l'API dell'app Server `User.Identity.Name` viene popolata con il `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` valore del tipo di attestazione `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com`(ad esempio,).</span><span class="sxs-lookup"><span data-stu-id="05bf2-174">By default, the Server app API populates `User.Identity.Name` with the value from the `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` claim type (for example, `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com`).</span></span>

<span data-ttu-id="05bf2-175">Per configurare l'app per la ricezione del valore dal `name` tipo di attestazione, configurare [TokenValidationParameters. NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) di <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="05bf2-175">To configure the app to receive the value from the `name` claim type, configure the [TokenValidationParameters.NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

```csharp
services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a><span data-ttu-id="05bf2-176">Impostazioni app</span><span class="sxs-lookup"><span data-stu-id="05bf2-176">App settings</span></span>

<span data-ttu-id="05bf2-177">Il file *appSettings. JSON* contiene le opzioni per configurare il gestore di connessione JWT usato per convalidare i token di accesso.</span><span class="sxs-lookup"><span data-stu-id="05bf2-177">The *appsettings.json* file contains the options to configure the JWT bearer handler used to validate access tokens.</span></span>

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "{DOMAIN}",
    "TenantId": "{TENANT ID}",
    "ClientId": "{API CLIENT ID}",
  }
}
```

### <a name="weatherforecast-controller"></a><span data-ttu-id="05bf2-178">Controller WeatherForecast</span><span class="sxs-lookup"><span data-stu-id="05bf2-178">WeatherForecast controller</span></span>

<span data-ttu-id="05bf2-179">Il controller WeatherForecast (*Controllers/WeatherForecastController. cs*) espone un'API protetta con `[Authorize]` l'attributo applicato al controller.</span><span class="sxs-lookup"><span data-stu-id="05bf2-179">The WeatherForecast controller (*Controllers/WeatherForecastController.cs*) exposes a protected API with the `[Authorize]` attribute applied to the controller.</span></span> <span data-ttu-id="05bf2-180">È **importante** comprendere che:</span><span class="sxs-lookup"><span data-stu-id="05bf2-180">It's **important** to understand that:</span></span>

* <span data-ttu-id="05bf2-181">L' `[Authorize]` attributo in questo controller API è l'unico elemento che protegge questa API da accessi non autorizzati.</span><span class="sxs-lookup"><span data-stu-id="05bf2-181">The `[Authorize]` attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="05bf2-182">L' `[Authorize]` attributo usato nell' Blazor app webassembly funge solo da hint per l'app che l'utente deve essere autorizzato affinché l'app funzioni correttamente.</span><span class="sxs-lookup"><span data-stu-id="05bf2-182">The `[Authorize]` attribute used in the Blazor WebAssembly app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

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

## <a name="client-app-configuration"></a><span data-ttu-id="05bf2-183">Configurazione dell'app client</span><span class="sxs-lookup"><span data-stu-id="05bf2-183">Client app configuration</span></span>

<span data-ttu-id="05bf2-184">*Questa sezione riguarda l'app **client** della soluzione.*</span><span class="sxs-lookup"><span data-stu-id="05bf2-184">*This section pertains to the solution's **Client** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="05bf2-185">Pacchetto di autenticazione</span><span class="sxs-lookup"><span data-stu-id="05bf2-185">Authentication package</span></span>

<span data-ttu-id="05bf2-186">Quando viene creata un'app per usare gli account aziendali o dell'`SingleOrg`Istituto di istruzione (), l'app riceve automaticamente un riferimento al pacchetto per`Microsoft.Authentication.WebAssembly.Msal` [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ().</span><span class="sxs-lookup"><span data-stu-id="05bf2-186">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="05bf2-187">Il pacchetto fornisce un set di primitive che consentono all'app di autenticare gli utenti e ottenere i token per chiamare le API protette.</span><span class="sxs-lookup"><span data-stu-id="05bf2-187">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="05bf2-188">Se si aggiunge l'autenticazione a un'app, aggiungere manualmente il pacchetto al file di progetto dell'app:</span><span class="sxs-lookup"><span data-stu-id="05bf2-188">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="05bf2-189">Sostituire `{VERSION}` nel riferimento al pacchetto precedente con la versione del `Microsoft.AspNetCore.Blazor.Templates` pacchetto illustrata nell' <xref:blazor/get-started> articolo.</span><span class="sxs-lookup"><span data-stu-id="05bf2-189">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="05bf2-190">Il `Microsoft.Authentication.WebAssembly.Msal` pacchetto aggiunge il `Microsoft.AspNetCore.Components.WebAssembly.Authentication` pacchetto all'app in modo transitivo.</span><span class="sxs-lookup"><span data-stu-id="05bf2-190">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="05bf2-191">Supporto del servizio di autenticazione</span><span class="sxs-lookup"><span data-stu-id="05bf2-191">Authentication service support</span></span>

<span data-ttu-id="05bf2-192">Il supporto per l'autenticazione degli utenti viene registrato nel contenitore del servizio `AddMsalAuthentication` con il metodo di estensione `Microsoft.Authentication.WebAssembly.Msal` fornito dal pacchetto.</span><span class="sxs-lookup"><span data-stu-id="05bf2-192">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="05bf2-193">Questo metodo configura tutti i servizi necessari per l'interazione dell'app con il provider di identità (IP).</span><span class="sxs-lookup"><span data-stu-id="05bf2-193">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="05bf2-194">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="05bf2-194">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    var authentication = options.ProviderOptions.Authentication;
    authentication.Authority = "https://login.microsoftonline.com/{TENANT ID}";
    authentication.ClientId = "{CLIENT ID}";
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="05bf2-195">Il `AddMsalAuthentication` metodo accetta un callback per configurare i parametri necessari per autenticare un'app.</span><span class="sxs-lookup"><span data-stu-id="05bf2-195">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="05bf2-196">Quando si registra l'app, è possibile ottenere i valori necessari per la configurazione dell'app dalla configurazione di AAD del portale di Azure.</span><span class="sxs-lookup"><span data-stu-id="05bf2-196">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

### <a name="access-token-scopes"></a><span data-ttu-id="05bf2-197">Ambiti del token di accesso</span><span class="sxs-lookup"><span data-stu-id="05bf2-197">Access token scopes</span></span>

<span data-ttu-id="05bf2-198">Gli ambiti dei token di accesso predefiniti rappresentano l'elenco degli ambiti dei token di accesso:</span><span class="sxs-lookup"><span data-stu-id="05bf2-198">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="05bf2-199">Incluso per impostazione predefinita nella richiesta di accesso.</span><span class="sxs-lookup"><span data-stu-id="05bf2-199">Included by default in the sign in request.</span></span>
* <span data-ttu-id="05bf2-200">Utilizzato per eseguire il provisioning di un token di accesso immediatamente dopo l'autenticazione.</span><span class="sxs-lookup"><span data-stu-id="05bf2-200">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="05bf2-201">Tutti gli ambiti devono appartenere alla stessa app per ogni regola di Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="05bf2-201">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="05bf2-202">Per altre app per le API è possibile aggiungere altri ambiti, se necessario:</span><span class="sxs-lookup"><span data-stu-id="05bf2-202">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="05bf2-203">Se il portale di Azure fornisce un URI di ambito e **l'app genera un'eccezione non gestita** quando riceve una risposta *401 non autorizzata* dall'API, provare a usare un URI dell'ambito che non includa lo schema e l'host.</span><span class="sxs-lookup"><span data-stu-id="05bf2-203">If the Azure portal provides a scope URI and **the app throws an unhandled exception** when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="05bf2-204">Ad esempio, il portale di Azure può fornire uno dei seguenti formati di URI di ambito:</span><span class="sxs-lookup"><span data-stu-id="05bf2-204">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="05bf2-205">Specificare l'URI dell'ambito senza lo schema e l'host:</span><span class="sxs-lookup"><span data-stu-id="05bf2-205">Supply the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

<span data-ttu-id="05bf2-206">Per altre informazioni, vedere <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span><span class="sxs-lookup"><span data-stu-id="05bf2-206">For more information, see <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span></span>

<!--
    For more information, see <xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests>.
-->

### <a name="imports-file"></a><span data-ttu-id="05bf2-207">Importa file</span><span class="sxs-lookup"><span data-stu-id="05bf2-207">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="05bf2-208">Pagina di indice</span><span class="sxs-lookup"><span data-stu-id="05bf2-208">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a><span data-ttu-id="05bf2-209">Componente app</span><span class="sxs-lookup"><span data-stu-id="05bf2-209">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="05bf2-210">Componente RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="05bf2-210">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="05bf2-211">Componente LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="05bf2-211">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="05bf2-212">Componente di autenticazione</span><span class="sxs-lookup"><span data-stu-id="05bf2-212">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="05bf2-213">Componente FetchData</span><span class="sxs-lookup"><span data-stu-id="05bf2-213">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="05bf2-214">Eseguire l'app</span><span class="sxs-lookup"><span data-stu-id="05bf2-214">Run the app</span></span>

<span data-ttu-id="05bf2-215">Eseguire l'app dal progetto server.</span><span class="sxs-lookup"><span data-stu-id="05bf2-215">Run the app from the Server project.</span></span> <span data-ttu-id="05bf2-216">Quando si usa Visual Studio, selezionare il progetto server in **Esplora soluzioni** e selezionare il pulsante **Esegui** sulla barra degli strumenti o avviare l'app dal menu **debug** .</span><span class="sxs-lookup"><span data-stu-id="05bf2-216">When using Visual Studio, select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="05bf2-217">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="05bf2-217">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* <xref:security/authentication/azure-active-directory/index>
* [<span data-ttu-id="05bf2-218">Documentazione di Microsoft Identity Platform</span><span class="sxs-lookup"><span data-stu-id="05bf2-218">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
