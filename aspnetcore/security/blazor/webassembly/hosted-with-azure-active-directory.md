---
title: Proteggere un'app ospitata ASP.NET Core WebAssembly con Azure Active DirectorySecure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/08/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/hosted-with-azure-active-directory
ms.openlocfilehash: 8fec9f585f42469665cf29069674a199e1626629
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80977132"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-hosted-app-with-azure-active-directory"></a><span data-ttu-id="4c691-102">Proteggere un'app ospitata ASP.NET Core WebAssembly con Azure Active DirectorySecure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="4c691-102">Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory</span></span>

<span data-ttu-id="4c691-103">Di [Javier Calvarro Nelson](https://github.com/javiercn) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="4c691-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]



<span data-ttu-id="4c691-104">Questo articolo descrive come creare [ Blazor un'app ospitata WebAssembly](xref:blazor/hosting-models#blazor-webassembly) che usa [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) per l'autenticazione.</span><span class="sxs-lookup"><span data-stu-id="4c691-104">This article describes how to create a [Blazor WebAssembly hosted app](xref:blazor/hosting-models#blazor-webassembly) that uses [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) for authentication.</span></span>

## <a name="register-apps-in-aad-b2c-and-create-solution"></a><span data-ttu-id="4c691-105">Registrare le app in AAD B2C e creare una soluzione</span><span class="sxs-lookup"><span data-stu-id="4c691-105">Register apps in AAD B2C and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="4c691-106">Creare un tenant</span><span class="sxs-lookup"><span data-stu-id="4c691-106">Create a tenant</span></span>

<span data-ttu-id="4c691-107">Seguire le indicazioni in [Guida introduttiva: Configurare un tenant](/azure/active-directory/develop/quickstart-create-new-tenant) per creare un tenant in AAD.</span><span class="sxs-lookup"><span data-stu-id="4c691-107">Follow the guidance in [Quickstart: Set up a tenant](/azure/active-directory/develop/quickstart-create-new-tenant) to create a tenant in AAD.</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="4c691-108">Registrare un'app per le API del serverRegister a server API app</span><span class="sxs-lookup"><span data-stu-id="4c691-108">Register a server API app</span></span>

<span data-ttu-id="4c691-109">Seguire le indicazioni in [Guida introduttiva: Registrare un'applicazione con la piattaforma](/azure/active-directory/develop/quickstart-register-app) di identità Microsoft e i successivi argomenti di Azure AAD per registrare un'app AAD per *l'app App server* nell'area Registrazioni di Azure Active Directory App del portale di Azure:Follow the guidance in Quickstart: Register an application with the Microsoft identity platform and subsequent Azure AAD topics to register an AAD app for the Server API app in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span><span class="sxs-lookup"><span data-stu-id="4c691-109">Follow the guidance in [Quickstart: Register an application with the Microsoft identity platform](/azure/active-directory/develop/quickstart-register-app) and subsequent Azure AAD topics to register an AAD app for the *Server API app* in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="4c691-110">Selezionare **Nuova registrazione**.</span><span class="sxs-lookup"><span data-stu-id="4c691-110">Select **New registration**.</span></span>
1. <span data-ttu-id="4c691-111">Specificare un **nome** per l'app, \*\* Blazor \*\*ad esempio Server AAD .</span><span class="sxs-lookup"><span data-stu-id="4c691-111">Provide a **Name** for the app (for example, **Blazor Server AAD**).</span></span>
1. <span data-ttu-id="4c691-112">Scegliere un **Tipo di account supportato**.</span><span class="sxs-lookup"><span data-stu-id="4c691-112">Choose a **Supported account types**.</span></span> <span data-ttu-id="4c691-113">È possibile selezionare **Account solo in questa directory dell'organizzazione** (tenant singolo) per questa esperienza.</span><span class="sxs-lookup"><span data-stu-id="4c691-113">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="4c691-114">*L'app api server* non richiede un URI di **reindirizzamento** in questo scenario, quindi lasciare l'elenco a discesa impostato su **Web** e non immettere un URI di reindirizzamento.</span><span class="sxs-lookup"><span data-stu-id="4c691-114">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="4c691-115">Disattivare la casella di controllo **Autorizzazioni** > **concedere all'amministratore per le autorizzazioni openid e offline_access.**</span><span class="sxs-lookup"><span data-stu-id="4c691-115">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="4c691-116">Selezionare **Registra**.</span><span class="sxs-lookup"><span data-stu-id="4c691-116">Select **Register**.</span></span>

<span data-ttu-id="4c691-117">In **Autorizzazioni API**rimuovere l'autorizzazione Microsoft **Graph** > **User.Read,** poiché l'app non richiede l'accesso o l'accesso al profilo uer.</span><span class="sxs-lookup"><span data-stu-id="4c691-117">In **API permissions**, remove the **Microsoft Graph** > **User.Read** permission, as the app doesn't require sign in or uer profile access.</span></span>

<span data-ttu-id="4c691-118">In **Esporre un'API**:</span><span class="sxs-lookup"><span data-stu-id="4c691-118">In **Expose an API**:</span></span>

1. <span data-ttu-id="4c691-119">Selezionare **Aggiungi un ambito**.</span><span class="sxs-lookup"><span data-stu-id="4c691-119">Select **Add a scope**.</span></span>
1. <span data-ttu-id="4c691-120">Selezionare **Salva e continua**.</span><span class="sxs-lookup"><span data-stu-id="4c691-120">Select **Save and continue**.</span></span>
1. <span data-ttu-id="4c691-121">Specificare un nome di `API.Access` **ambito,** ad esempio ).</span><span class="sxs-lookup"><span data-stu-id="4c691-121">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="4c691-122">Specificare un nome visualizzato di `Access API` **consenso dell'amministratore,** ad esempio ).</span><span class="sxs-lookup"><span data-stu-id="4c691-122">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="4c691-123">Fornire una descrizione del consenso `Allows the app to access server app API endpoints.`dell'amministratore, ad esempio ). **Admin consent description**</span><span class="sxs-lookup"><span data-stu-id="4c691-123">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="4c691-124">Verificare che **lo stato** sia impostato su **Abilitato**.</span><span class="sxs-lookup"><span data-stu-id="4c691-124">Confirm that the **State** is set to **Enabled**.</span></span>
1. <span data-ttu-id="4c691-125">Selezionare **Aggiungi ambito**.</span><span class="sxs-lookup"><span data-stu-id="4c691-125">Select **Add scope**.</span></span>

<span data-ttu-id="4c691-126">Registrare le seguenti informazioni:</span><span class="sxs-lookup"><span data-stu-id="4c691-126">Record the following information:</span></span>

* <span data-ttu-id="4c691-127">*App PER le API del server* ID applicazione (ID client) `11111111-1111-1111-1111-111111111111`(ad esempio, )</span><span class="sxs-lookup"><span data-stu-id="4c691-127">*Server API app* Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`)</span></span>
* <span data-ttu-id="4c691-128">URI ID app (ad esempio, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, `api://11111111-1111-1111-1111-111111111111`o il valore personalizzato fornito)</span><span class="sxs-lookup"><span data-stu-id="4c691-128">App ID URI (for example, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, `api://11111111-1111-1111-1111-111111111111`, or the custom value that you provided)</span></span>
* <span data-ttu-id="4c691-129">ID directory (ID tenant) `222222222-2222-2222-2222-222222222222`(ad esempio, )</span><span class="sxs-lookup"><span data-stu-id="4c691-129">Directory ID (Tenant ID) (for example, `222222222-2222-2222-2222-222222222222`)</span></span>
* <span data-ttu-id="4c691-130">Dominio tenant AAD (ad esempio, `contoso.onmicrosoft.com`)</span><span class="sxs-lookup"><span data-stu-id="4c691-130">AAD Tenant domain (for example, `contoso.onmicrosoft.com`)</span></span>
* <span data-ttu-id="4c691-131">Ambito predefinito (ad `API.Access`esempio, )</span><span class="sxs-lookup"><span data-stu-id="4c691-131">Default scope (for example, `API.Access`)</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="4c691-132">Registrare un'app client</span><span class="sxs-lookup"><span data-stu-id="4c691-132">Register a client app</span></span>

<span data-ttu-id="4c691-133">Seguire le indicazioni in [Guida introduttiva: Registrare un'applicazione con la piattaforma](/azure/active-directory/develop/quickstart-register-app) di identità Microsoft e i successivi argomenti di Azure AAD per registrare un'app AAD per *l'app Client* nell'area Registrazioni di Azure Active Directory App del portale di Azure:Follow the guidance in Quickstart: Register an application with the Microsoft identity platform and subsequent Azure AAD topics to register a AAD app for the Client app in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span><span class="sxs-lookup"><span data-stu-id="4c691-133">Follow the guidance in [Quickstart: Register an application with the Microsoft identity platform](/azure/active-directory/develop/quickstart-register-app) and subsequent Azure AAD topics to register a AAD app for the *Client app* in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="4c691-134">Selezionare **Nuova registrazione**.</span><span class="sxs-lookup"><span data-stu-id="4c691-134">Select **New registration**.</span></span>
1. <span data-ttu-id="4c691-135">Specificare un **nome** per l'app, \*\* Blazor \*\*ad esempio AAD client .</span><span class="sxs-lookup"><span data-stu-id="4c691-135">Provide a **Name** for the app (for example, **Blazor Client AAD**).</span></span>
1. <span data-ttu-id="4c691-136">Scegliere un **Tipo di account supportato**.</span><span class="sxs-lookup"><span data-stu-id="4c691-136">Choose a **Supported account types**.</span></span> <span data-ttu-id="4c691-137">È possibile selezionare **Account solo in questa directory dell'organizzazione** (tenant singolo) per questa esperienza.</span><span class="sxs-lookup"><span data-stu-id="4c691-137">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="4c691-138">Lasciare l'elenco a discesa URI di **reindirizzamento** impostato su **Web**e specificare un URI di reindirizzamento di `https://localhost:5001/authentication/login-callback`.</span><span class="sxs-lookup"><span data-stu-id="4c691-138">Leave the **Redirect URI** drop down set to **Web**, and provide a redirect URI of `https://localhost:5001/authentication/login-callback`.</span></span>
1. <span data-ttu-id="4c691-139">Disattivare la casella di controllo **Autorizzazioni** > **concedere all'amministratore per le autorizzazioni openid e offline_access.**</span><span class="sxs-lookup"><span data-stu-id="4c691-139">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="4c691-140">Selezionare **Registra**.</span><span class="sxs-lookup"><span data-stu-id="4c691-140">Select **Register**.</span></span>

<span data-ttu-id="4c691-141">In**Configurazioni** > piattaforma **di autenticazione** > **Web**:</span><span class="sxs-lookup"><span data-stu-id="4c691-141">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="4c691-142">Verificare che `https://localhost:5001/authentication/login-callback` l'URI di **reindirizzamento** di sia presente.</span><span class="sxs-lookup"><span data-stu-id="4c691-142">Confirm the **Redirect URI** of `https://localhost:5001/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="4c691-143">Per **Concessione implicita**, selezionare le caselle di controllo per Token di **accesso** e **token ID**.</span><span class="sxs-lookup"><span data-stu-id="4c691-143">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="4c691-144">Le impostazioni predefinite rimanenti per l'app sono accettabili per questa esperienza.</span><span class="sxs-lookup"><span data-stu-id="4c691-144">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="4c691-145">Fare clic sul pulsante **Salva**.</span><span class="sxs-lookup"><span data-stu-id="4c691-145">Select the **Save** button.</span></span>

<span data-ttu-id="4c691-146">In **Autorizzazioni API**:</span><span class="sxs-lookup"><span data-stu-id="4c691-146">In **API permissions**:</span></span>

1. <span data-ttu-id="4c691-147">Verificare che l'app disponga dell'autorizzazione **Microsoft Graph** > **User.Read.**</span><span class="sxs-lookup"><span data-stu-id="4c691-147">Confirm that the app has **Microsoft Graph** > **User.Read** permission.</span></span>
1. <span data-ttu-id="4c691-148">Selezionare **Aggiungi un'autorizzazione** seguita da **API personali**.</span><span class="sxs-lookup"><span data-stu-id="4c691-148">Select **Add a permission** followed by **My APIs**.</span></span>
1. <span data-ttu-id="4c691-149">Selezionare *l'app API server* nella colonna **Nome** (ad esempio, \*\* Blazor AAD del server).\*\*</span><span class="sxs-lookup"><span data-stu-id="4c691-149">Select the *Server API app* from the **Name** column (for example, **Blazor Server AAD**).</span></span>
1. <span data-ttu-id="4c691-150">Aprire l'elenco **delle API.**</span><span class="sxs-lookup"><span data-stu-id="4c691-150">Open the **API** list.</span></span>
1. <span data-ttu-id="4c691-151">Abilitare l'accesso all'API, `API.Access`ad esempio ).</span><span class="sxs-lookup"><span data-stu-id="4c691-151">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="4c691-152">Selezionare **Aggiungi autorizzazioni**.</span><span class="sxs-lookup"><span data-stu-id="4c691-152">Select **Add permissions**.</span></span>
1. <span data-ttu-id="4c691-153">Selezionare il pulsante **Concedi il contenuto dell'amministratore per il** nome di TENANT.</span><span class="sxs-lookup"><span data-stu-id="4c691-153">Select the **Grant admin content for {TENANT NAME}** button.</span></span> <span data-ttu-id="4c691-154">Selezionare **Sì** per confermare.</span><span class="sxs-lookup"><span data-stu-id="4c691-154">Select **Yes** to confirm.</span></span>

<span data-ttu-id="4c691-155">Registrare *l'ID applicazione dell'app* client `33333333-3333-3333-3333-333333333333`(ID client) (ad esempio, ).</span><span class="sxs-lookup"><span data-stu-id="4c691-155">Record the *Client app* Application ID (Client ID) (for example, `33333333-3333-3333-3333-333333333333`).</span></span>

### <a name="create-the-app"></a><span data-ttu-id="4c691-156">Creare l'app</span><span class="sxs-lookup"><span data-stu-id="4c691-156">Create the app</span></span>

<span data-ttu-id="4c691-157">Sostituire i segnaposto nel comando seguente con le informazioni registrate in precedenza ed eseguire il comando in una shell dei comandi:</span><span class="sxs-lookup"><span data-stu-id="4c691-157">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{DOMAIN}" -ho --tenant-id "{TENANT ID}"
```

<span data-ttu-id="4c691-158">Per specificare il percorso di output, che crea una cartella di progetto se non esiste, `-o BlazorSample`includere l'opzione di output nel comando con un percorso, ad esempio ).</span><span class="sxs-lookup"><span data-stu-id="4c691-158">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="4c691-159">Il nome della cartella diventa anche parte del nome del progetto.</span><span class="sxs-lookup"><span data-stu-id="4c691-159">The folder name also becomes part of the project's name.</span></span>

> [!NOTE]
> <span data-ttu-id="4c691-160">Passare l'URI dell'ID app all'opzione, `app-id-uri` ma si noti che potrebbe essere necessaria una modifica alla configurazione nell'app client, descritta nella sezione Ambiti del token di [accesso.](#access-token-scopes)</span><span class="sxs-lookup"><span data-stu-id="4c691-160">Pass the App ID URI to the `app-id-uri` option, but note a configuration change might be required in the client app, which is described in the [Access token scopes](#access-token-scopes) section.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="4c691-161">Configurazione dell'app server</span><span class="sxs-lookup"><span data-stu-id="4c691-161">Server app configuration</span></span>

<span data-ttu-id="4c691-162">*Questa sezione riguarda l'app **Server** della soluzione.*</span><span class="sxs-lookup"><span data-stu-id="4c691-162">*This section pertains to the solution's **Server** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="4c691-163">Pacchetto di autenticazione</span><span class="sxs-lookup"><span data-stu-id="4c691-163">Authentication package</span></span>

<span data-ttu-id="4c691-164">Il supporto per l'autenticazione e l'autorizzazione delle chiamate `Microsoft.AspNetCore.Authentication.AzureAD.UI`a ASP.NET API Web principali viene fornito dal:</span><span class="sxs-lookup"><span data-stu-id="4c691-164">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the `Microsoft.AspNetCore.Authentication.AzureAD.UI`:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureAD.UI" 
    Version="3.1.0" />
```

### <a name="authentication-service-support"></a><span data-ttu-id="4c691-165">Supporto del servizio di autenticazione</span><span class="sxs-lookup"><span data-stu-id="4c691-165">Authentication service support</span></span>

<span data-ttu-id="4c691-166">Il `AddAuthentication` metodo imposta i servizi di autenticazione all'interno dell'app e configura il gestore del bearer JWT come metodo di autenticazione predefinito.</span><span class="sxs-lookup"><span data-stu-id="4c691-166">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="4c691-167">Il `AddAzureADBearer` metodo imposta i parametri specifici nel gestore JWT Bearer necessari per convalidare i token generati da Azure Active Directory:</span><span class="sxs-lookup"><span data-stu-id="4c691-167">The `AddAzureADBearer` method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory:</span></span>

```csharp
services.AddAuthentication(AzureADDefaults.BearerAuthenticationScheme)
    .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
```

<span data-ttu-id="4c691-168">`UseAuthentication`e `UseAuthorization` assicurarsi che:</span><span class="sxs-lookup"><span data-stu-id="4c691-168">`UseAuthentication` and `UseAuthorization` ensure that:</span></span>

* <span data-ttu-id="4c691-169">L'app tenta di analizzare e convalidare i token nelle richieste in ingresso.</span><span class="sxs-lookup"><span data-stu-id="4c691-169">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="4c691-170">Qualsiasi richiesta che tenti di accedere a una risorsa protetta senza credenziali appropriate ha esito negativo.</span><span class="sxs-lookup"><span data-stu-id="4c691-170">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="useridentityname"></a><span data-ttu-id="4c691-171">User.Identity.Name</span><span class="sxs-lookup"><span data-stu-id="4c691-171">User.Identity.Name</span></span>

<span data-ttu-id="4c691-172">Per impostazione predefinita, l'API dell'app Server popola `User.Identity.Name` con il valore del `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` tipo di attestazione, `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com`ad esempio ).</span><span class="sxs-lookup"><span data-stu-id="4c691-172">By default, the Server app API populates `User.Identity.Name` with the value from the `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` claim type (for example, `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com`).</span></span>

<span data-ttu-id="4c691-173">Per configurare l'app per `name` la ricezione del valore dal tipo di <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> `Startup.ConfigureServices`attestazione, configurare il [TokenValidationParameters.NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) di in :</span><span class="sxs-lookup"><span data-stu-id="4c691-173">To configure the app to receive the value from the `name` claim type, configure the [TokenValidationParameters.NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

```csharp
services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a><span data-ttu-id="4c691-174">Impostazioni app</span><span class="sxs-lookup"><span data-stu-id="4c691-174">App settings</span></span>

<span data-ttu-id="4c691-175">Il file *appsettings.json* contiene le opzioni per configurare il gestore di connessione JWT utilizzato per convalidare i token di accesso.</span><span class="sxs-lookup"><span data-stu-id="4c691-175">The *appsettings.json* file contains the options to configure the JWT bearer handler used to validate access tokens.</span></span>

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

### <a name="weatherforecast-controller"></a><span data-ttu-id="4c691-176">Controller WeatherForecast</span><span class="sxs-lookup"><span data-stu-id="4c691-176">WeatherForecast controller</span></span>

<span data-ttu-id="4c691-177">Il controller WeatherForecast (*Controllers/WeatherForecastController.cs*) espone `[Authorize]` un'API protetta con l'attributo applicato al controller.</span><span class="sxs-lookup"><span data-stu-id="4c691-177">The WeatherForecast controller (*Controllers/WeatherForecastController.cs*) exposes a protected API with the `[Authorize]` attribute applied to the controller.</span></span> <span data-ttu-id="4c691-178">È **importante** comprendere che:</span><span class="sxs-lookup"><span data-stu-id="4c691-178">It's **important** to understand that:</span></span>

* <span data-ttu-id="4c691-179">L'attributo `[Authorize]` in questo controller API è l'unica cosa che protegge questa API da accessi non autorizzati.</span><span class="sxs-lookup"><span data-stu-id="4c691-179">The `[Authorize]` attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="4c691-180">L'attributo `[Authorize]` Blazor utilizzato nell'app WebAssembly funge solo da suggerimento per l'app che l'utente deve essere autorizzato per il corretto funzionamento dell'app.</span><span class="sxs-lookup"><span data-stu-id="4c691-180">The `[Authorize]` attribute used in the Blazor WebAssembly app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

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

## <a name="client-app-configuration"></a><span data-ttu-id="4c691-181">Configurazione dell'app client</span><span class="sxs-lookup"><span data-stu-id="4c691-181">Client app configuration</span></span>

<span data-ttu-id="4c691-182">*Questa sezione riguarda l'app **Client** della soluzione.*</span><span class="sxs-lookup"><span data-stu-id="4c691-182">*This section pertains to the solution's **Client** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="4c691-183">Pacchetto di autenticazione</span><span class="sxs-lookup"><span data-stu-id="4c691-183">Authentication package</span></span>

<span data-ttu-id="4c691-184">Quando viene creata un'app per`SingleOrg`usare gli account aziendali o dell'istituto`Microsoft.Authentication.WebAssembly.Msal`di istruzione ( ), l'app riceve automaticamente un riferimento al pacchetto per la libreria di [autenticazione Microsoft](/azure/active-directory/develop/msal-overview) ( ).</span><span class="sxs-lookup"><span data-stu-id="4c691-184">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="4c691-185">Il pacchetto fornisce un set di primitive che consentono all'app di autenticare gli utenti e ottenere token per chiamare API protette.</span><span class="sxs-lookup"><span data-stu-id="4c691-185">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="4c691-186">Se si aggiunge l'autenticazione a un'app, aggiungi manualmente il pacchetto al file di progetto dell'app:</span><span class="sxs-lookup"><span data-stu-id="4c691-186">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="4c691-187">Sostituire `{VERSION}` nel riferimento al pacchetto precedente `Microsoft.AspNetCore.Blazor.Templates` con la <xref:blazor/get-started> versione del pacchetto illustrata nell'articolo.</span><span class="sxs-lookup"><span data-stu-id="4c691-187">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="4c691-188">Il `Microsoft.Authentication.WebAssembly.Msal` pacchetto aggiunge in `Microsoft.AspNetCore.Components.WebAssembly.Authentication` modo transitivo il pacchetto all'app.</span><span class="sxs-lookup"><span data-stu-id="4c691-188">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="4c691-189">Supporto del servizio di autenticazione</span><span class="sxs-lookup"><span data-stu-id="4c691-189">Authentication service support</span></span>

<span data-ttu-id="4c691-190">Il supporto per l'autenticazione degli utenti `AddMsalAuthentication` viene registrato `Microsoft.Authentication.WebAssembly.Msal` nel contenitore dei servizi con il metodo di estensione fornito dal pacchetto.</span><span class="sxs-lookup"><span data-stu-id="4c691-190">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="4c691-191">Questo metodo imposta tutti i servizi necessari all'app per interagire con il provider di identità (IP).</span><span class="sxs-lookup"><span data-stu-id="4c691-191">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="4c691-192">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="4c691-192">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    var authentication = options.ProviderOptions.Authentication;
    authentication.Authority = "https://login.microsoftonline.com/{TENANT ID}";
    authentication.ClientId = "{CLIENT ID}";
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="4c691-193">Il `AddMsalAuthentication` metodo accetta un callback per configurare i parametri necessari per autenticare un'app.</span><span class="sxs-lookup"><span data-stu-id="4c691-193">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="4c691-194">I valori necessari per la configurazione dell'app possono essere ottenuti dalla configurazione di Azure Portal AAD quando si registra l'app.</span><span class="sxs-lookup"><span data-stu-id="4c691-194">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

### <a name="access-token-scopes"></a><span data-ttu-id="4c691-195">Ambiti dei token di accessoAccess token scopes</span><span class="sxs-lookup"><span data-stu-id="4c691-195">Access token scopes</span></span>

<span data-ttu-id="4c691-196">Gli ambiti di token di accesso predefiniti rappresentano l'elenco di ambiti di token di accesso:The default access token scopes represent the list of access token scopes that are:</span><span class="sxs-lookup"><span data-stu-id="4c691-196">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="4c691-197">Incluso per impostazione predefinita nella richiesta di accesso.</span><span class="sxs-lookup"><span data-stu-id="4c691-197">Included by default in the sign in request.</span></span>
* <span data-ttu-id="4c691-198">Utilizzato per eseguire il provisioning di un token di accesso immediatamente dopo l'autenticazione.</span><span class="sxs-lookup"><span data-stu-id="4c691-198">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="4c691-199">Tutti gli ambiti devono appartenere alla stessa app per ogni regola di Azure Active Directory.All scopes must belong to the same app per Azure Active Directory rules.</span><span class="sxs-lookup"><span data-stu-id="4c691-199">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="4c691-200">È possibile aggiungere ambiti aggiuntivi per altre app API in base alle esigenze:Additional scopes can be added for additional API apps as needed:</span><span class="sxs-lookup"><span data-stu-id="4c691-200">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="4c691-201">Se il portale di Azure fornisce un URI di ambito e **l'app genera un'eccezione non gestita** quando riceve una risposta *401 Unauthorized* dall'API, provare a usare un URI di ambito che non includa lo schema e l'host.</span><span class="sxs-lookup"><span data-stu-id="4c691-201">If the Azure portal provides a scope URI and **the app throws an unhandled exception** when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="4c691-202">Ad esempio, il portale di Azure può fornire uno dei seguenti formati URI di ambito:</span><span class="sxs-lookup"><span data-stu-id="4c691-202">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="4c691-203">Fornire l'URI dell'ambito senza lo schema e l'host:</span><span class="sxs-lookup"><span data-stu-id="4c691-203">Supply the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

<span data-ttu-id="4c691-204">Per altre informazioni, vedere <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span><span class="sxs-lookup"><span data-stu-id="4c691-204">For more information, see <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span></span>

### <a name="imports-file"></a><span data-ttu-id="4c691-205">File di importazione</span><span class="sxs-lookup"><span data-stu-id="4c691-205">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="4c691-206">Pagina di indice</span><span class="sxs-lookup"><span data-stu-id="4c691-206">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a><span data-ttu-id="4c691-207">Componente dell'app</span><span class="sxs-lookup"><span data-stu-id="4c691-207">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="4c691-208">Componente RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="4c691-208">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="4c691-209">Componente LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="4c691-209">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="4c691-210">Componente di autenticazione</span><span class="sxs-lookup"><span data-stu-id="4c691-210">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="4c691-211">Componente FetchData</span><span class="sxs-lookup"><span data-stu-id="4c691-211">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="4c691-212">Eseguire l'app</span><span class="sxs-lookup"><span data-stu-id="4c691-212">Run the app</span></span>

<span data-ttu-id="4c691-213">Eseguire l'app dal progetto Server.</span><span class="sxs-lookup"><span data-stu-id="4c691-213">Run the app from the Server project.</span></span> <span data-ttu-id="4c691-214">Quando si usa Visual Studio, selezionare il progetto Server in **Esplora soluzioni** e selezionare il pulsante **Esegui** nella barra degli strumenti o avviare l'app dal menu **Debug.**</span><span class="sxs-lookup"><span data-stu-id="4c691-214">When using Visual Studio, select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="4c691-215">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="4c691-215">Additional resources</span></span>

* [<span data-ttu-id="4c691-216">Richiedere token di accesso aggiuntiviRequest additional access tokens</span><span class="sxs-lookup"><span data-stu-id="4c691-216">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* <xref:security/authentication/azure-active-directory/index>
* [<span data-ttu-id="4c691-217">Documentazione di Microsoft Identity Platform</span><span class="sxs-lookup"><span data-stu-id="4c691-217">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
