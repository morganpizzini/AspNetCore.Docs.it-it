---
title: Proteggere un'app ospitata ASP.NET Core WebAssembly con Azure Active Directory B2CSecure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory B2C
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/09/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/hosted-with-azure-active-directory-b2c
ms.openlocfilehash: e2bb0c1bd807d590331b714e3b80d8c4ab434e2f
ms.sourcegitcommit: e8dc30453af8bbefcb61857987090d79230a461d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/11/2020
ms.locfileid: "81123464"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-hosted-app-with-azure-active-directory-b2c"></a><span data-ttu-id="d967c-102">Proteggere un'app ospitata ASP.NET Core WebAssembly con Azure Active Directory B2CSecure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="d967c-102">Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory B2C</span></span>

<span data-ttu-id="d967c-103">Di [Javier Calvarro Nelson](https://github.com/javiercn) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="d967c-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="d967c-104">Questo articolo descrive come Blazor creare un'app autonoma WebAssembly che usa [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) per l'autenticazione.</span><span class="sxs-lookup"><span data-stu-id="d967c-104">This article describes how to create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) for authentication.</span></span>

## <a name="register-apps-in-aad-b2c-and-create-solution"></a><span data-ttu-id="d967c-105">Registrare le app in AAD B2C e creare una soluzione</span><span class="sxs-lookup"><span data-stu-id="d967c-105">Register apps in AAD B2C and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="d967c-106">Creare un tenant</span><span class="sxs-lookup"><span data-stu-id="d967c-106">Create a tenant</span></span>

<span data-ttu-id="d967c-107">Seguire le indicazioni in [Esercitazione: Creare un tenant B2C di Azure Active Directory](/azure/active-directory-b2c/tutorial-create-tenant) per creare un tenant AAD B2C e registrare le informazioni seguenti:Follow the guidance in Tutorial: Create an Azure Active Directory B2C tenant to create an AAD B2C tenant and record the following information:</span><span class="sxs-lookup"><span data-stu-id="d967c-107">Follow the guidance in [Tutorial: Create an Azure Active Directory B2C tenant](/azure/active-directory-b2c/tutorial-create-tenant) to create an AAD B2C tenant and record the following information:</span></span>

* <span data-ttu-id="d967c-108">AAD B2C (ad `https://contoso.b2clogin.com/`esempio, , che include la barra finale)</span><span class="sxs-lookup"><span data-stu-id="d967c-108">AAD B2C instance (for example, `https://contoso.b2clogin.com/`, which includes the trailing slash)</span></span>
* <span data-ttu-id="d967c-109">AAD B2C Dominio tenant `contoso.onmicrosoft.com`(ad esempio, )</span><span class="sxs-lookup"><span data-stu-id="d967c-109">AAD B2C Tenant domain (for example, `contoso.onmicrosoft.com`)</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="d967c-110">Registrare un'app per le API del serverRegister a server API app</span><span class="sxs-lookup"><span data-stu-id="d967c-110">Register a server API app</span></span>

<span data-ttu-id="d967c-111">Seguire le istruzioni in [Esercitazione: Registrare un'applicazione in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) per registrare un'app AAD per *l'app API Server* nell'area Registrazioni di Azure Active Directory App del portale di Azure:Follow the guidance in **Tutorial:** > Register an application in Azure Active Directory B2C to register an AAD app for the Server API app in the Azure Active Directory**App registrations** area of the Azure portal:</span><span class="sxs-lookup"><span data-stu-id="d967c-111">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) to register an AAD app for the *Server API app* in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="d967c-112">Selezionare **Nuova registrazione**.</span><span class="sxs-lookup"><span data-stu-id="d967c-112">Select **New registration**.</span></span>
1. <span data-ttu-id="d967c-113">Specificare un **nome** per l'app, \*\* Blazor \*\*ad esempio Server AAD B2C .</span><span class="sxs-lookup"><span data-stu-id="d967c-113">Provide a **Name** for the app (for example, **Blazor Server AAD B2C**).</span></span>
1. <span data-ttu-id="d967c-114">Per **Tipi di account supportati**, selezionare Account in qualsiasi directory organizzativa o provider di **identità. Per l'autenticazione degli utenti con Azure AD B2C.**</span><span class="sxs-lookup"><span data-stu-id="d967c-114">For **Supported account types**, select **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span> <span data-ttu-id="d967c-115">(multi-tenant) per questa esperienza.</span><span class="sxs-lookup"><span data-stu-id="d967c-115">(multi-tenant) for this experience.</span></span>
1. <span data-ttu-id="d967c-116">*L'app api server* non richiede un URI di **reindirizzamento** in questo scenario, quindi lasciare l'elenco a discesa impostato su **Web** e non immettere un URI di reindirizzamento.</span><span class="sxs-lookup"><span data-stu-id="d967c-116">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="d967c-117">Verificare che **l'opzione Autorizzazioni** > **Concedi autorizzazione amministratore a openid e offline_access autorizzazioni** sia abilitata.</span><span class="sxs-lookup"><span data-stu-id="d967c-117">Confirm that **Permissions** > **Grant admin concent to openid and offline_access permissions** is enabled.</span></span>
1. <span data-ttu-id="d967c-118">Selezionare **Registra**.</span><span class="sxs-lookup"><span data-stu-id="d967c-118">Select **Register**.</span></span>

<span data-ttu-id="d967c-119">In **Esporre un'API**:</span><span class="sxs-lookup"><span data-stu-id="d967c-119">In **Expose an API**:</span></span>

1. <span data-ttu-id="d967c-120">Selezionare **Aggiungi un ambito**.</span><span class="sxs-lookup"><span data-stu-id="d967c-120">Select **Add a scope**.</span></span>
1. <span data-ttu-id="d967c-121">Selezionare **Salva e continua**.</span><span class="sxs-lookup"><span data-stu-id="d967c-121">Select **Save and continue**.</span></span>
1. <span data-ttu-id="d967c-122">Specificare un nome di `API.Access` **ambito,** ad esempio ).</span><span class="sxs-lookup"><span data-stu-id="d967c-122">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="d967c-123">Specificare un nome visualizzato di `Access API` **consenso dell'amministratore,** ad esempio ).</span><span class="sxs-lookup"><span data-stu-id="d967c-123">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="d967c-124">Fornire una descrizione del consenso `Allows the app to access server app API endpoints.`dell'amministratore, ad esempio ). **Admin consent description**</span><span class="sxs-lookup"><span data-stu-id="d967c-124">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="d967c-125">Verificare che **lo stato** sia impostato su **Abilitato**.</span><span class="sxs-lookup"><span data-stu-id="d967c-125">Confirm that the **State** is set to **Enabled**.</span></span>
1. <span data-ttu-id="d967c-126">Selezionare **Aggiungi ambito**.</span><span class="sxs-lookup"><span data-stu-id="d967c-126">Select **Add scope**.</span></span>

<span data-ttu-id="d967c-127">Registrare le seguenti informazioni:</span><span class="sxs-lookup"><span data-stu-id="d967c-127">Record the following information:</span></span>

* <span data-ttu-id="d967c-128">*App PER le API del server* ID applicazione (ID client) `11111111-1111-1111-1111-111111111111`(ad esempio, )</span><span class="sxs-lookup"><span data-stu-id="d967c-128">*Server API app* Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`)</span></span>
* <span data-ttu-id="d967c-129">URI ID app (ad esempio, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, `api://11111111-1111-1111-1111-111111111111`o il valore personalizzato fornito)</span><span class="sxs-lookup"><span data-stu-id="d967c-129">App ID URI (for example, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, `api://11111111-1111-1111-1111-111111111111`, or the custom value that you provided)</span></span>
* <span data-ttu-id="d967c-130">ID directory (ID tenant) `222222222-2222-2222-2222-222222222222`(ad esempio, )</span><span class="sxs-lookup"><span data-stu-id="d967c-130">Directory ID (Tenant ID) (for example, `222222222-2222-2222-2222-222222222222`)</span></span>
* <span data-ttu-id="d967c-131">*App PER le API del server* URI ID app (ad esempio, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`il portale di Azure potrebbe visualizzare per impostazione predefinita il valore per l'ID client)</span><span class="sxs-lookup"><span data-stu-id="d967c-131">*Server API app* App ID URI (for example, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, the Azure portal might default the value to the Client ID)</span></span>
* <span data-ttu-id="d967c-132">Ambito predefinito (ad `API.Access`esempio, )</span><span class="sxs-lookup"><span data-stu-id="d967c-132">Default scope (for example, `API.Access`)</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="d967c-133">Registrare un'app client</span><span class="sxs-lookup"><span data-stu-id="d967c-133">Register a client app</span></span>

<span data-ttu-id="d967c-134">Seguire le istruzioni in Esercitazione: Registrare di nuovo [un'applicazione in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) per registrare un'app AAD per *l'app Client* nell'area Registrazioni di Azure Active Directory App del portale di Azure:Follow the guidance in **Tutorial:** > Register an application in Azure Active Directory B2C again to register an AAD app for the Client app in the Azure Active Directory**App registrations** area of the Azure portal:</span><span class="sxs-lookup"><span data-stu-id="d967c-134">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) again to register an AAD app for the *Client app* in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="d967c-135">Selezionare **Nuova registrazione**.</span><span class="sxs-lookup"><span data-stu-id="d967c-135">Select **New registration**.</span></span>
1. <span data-ttu-id="d967c-136">Specificare un **nome** per l'app, \*\* Blazor \*\*ad esempio AAD B2C client .</span><span class="sxs-lookup"><span data-stu-id="d967c-136">Provide a **Name** for the app (for example, **Blazor Client AAD B2C**).</span></span>
1. <span data-ttu-id="d967c-137">Per **Tipi di account supportati**, selezionare Account in qualsiasi directory organizzativa o provider di **identità. Per l'autenticazione degli utenti con Azure AD B2C.**</span><span class="sxs-lookup"><span data-stu-id="d967c-137">For **Supported account types**, select **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span> <span data-ttu-id="d967c-138">(multi-tenant) per questa esperienza.</span><span class="sxs-lookup"><span data-stu-id="d967c-138">(multi-tenant) for this experience.</span></span>
1. <span data-ttu-id="d967c-139">Lasciare l'elenco a discesa URI di **reindirizzamento** impostato su **Web**e specificare un URI di reindirizzamento di `https://localhost:5001/authentication/login-callback`.</span><span class="sxs-lookup"><span data-stu-id="d967c-139">Leave the **Redirect URI** drop down set to **Web**, and provide a redirect URI of `https://localhost:5001/authentication/login-callback`.</span></span>
1. <span data-ttu-id="d967c-140">Verificare che **l'opzione Autorizzazioni** > **Concedi autorizzazione amministratore a openid e offline_access autorizzazioni** sia abilitata.</span><span class="sxs-lookup"><span data-stu-id="d967c-140">Confirm that **Permissions** > **Grant admin concent to openid and offline_access permissions** is enabled.</span></span>
1. <span data-ttu-id="d967c-141">Selezionare **Registra**.</span><span class="sxs-lookup"><span data-stu-id="d967c-141">Select **Register**.</span></span>

<span data-ttu-id="d967c-142">In**Configurazioni** > piattaforma **di autenticazione** > **Web**:</span><span class="sxs-lookup"><span data-stu-id="d967c-142">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="d967c-143">Verificare che `https://localhost:5001/authentication/login-callback` l'URI di **reindirizzamento** di sia presente.</span><span class="sxs-lookup"><span data-stu-id="d967c-143">Confirm the **Redirect URI** of `https://localhost:5001/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="d967c-144">Per **Concessione implicita**, selezionare le caselle di controllo per Token di **accesso** e **token ID**.</span><span class="sxs-lookup"><span data-stu-id="d967c-144">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="d967c-145">Le impostazioni predefinite rimanenti per l'app sono accettabili per questa esperienza.</span><span class="sxs-lookup"><span data-stu-id="d967c-145">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="d967c-146">Fare clic sul pulsante **Salva**.</span><span class="sxs-lookup"><span data-stu-id="d967c-146">Select the **Save** button.</span></span>

<span data-ttu-id="d967c-147">In **Autorizzazioni API**:</span><span class="sxs-lookup"><span data-stu-id="d967c-147">In **API permissions**:</span></span>

1. <span data-ttu-id="d967c-148">Verificare che l'app disponga dell'autorizzazione **Microsoft Graph** > **User.Read.**</span><span class="sxs-lookup"><span data-stu-id="d967c-148">Confirm that the app has **Microsoft Graph** > **User.Read** permission.</span></span>
1. <span data-ttu-id="d967c-149">Selezionare **Aggiungi un'autorizzazione** seguita da **API personali**.</span><span class="sxs-lookup"><span data-stu-id="d967c-149">Select **Add a permission** followed by **My APIs**.</span></span>
1. <span data-ttu-id="d967c-150">Selezionare *l'app API server* nella colonna **Nome** (ad esempio, \*\* Blazor AAD B2C\*\*del server).</span><span class="sxs-lookup"><span data-stu-id="d967c-150">Select the *Server API app* from the **Name** column (for example, **Blazor Server AAD B2C**).</span></span>
1. <span data-ttu-id="d967c-151">Aprire l'elenco **delle API.**</span><span class="sxs-lookup"><span data-stu-id="d967c-151">Open the **API** list.</span></span>
1. <span data-ttu-id="d967c-152">Abilitare l'accesso all'API, `API.Access`ad esempio ).</span><span class="sxs-lookup"><span data-stu-id="d967c-152">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="d967c-153">Selezionare **Aggiungi autorizzazioni**.</span><span class="sxs-lookup"><span data-stu-id="d967c-153">Select **Add permissions**.</span></span>
1. <span data-ttu-id="d967c-154">Selezionare il pulsante **Concedi il contenuto dell'amministratore per il** nome di TENANT.</span><span class="sxs-lookup"><span data-stu-id="d967c-154">Select the **Grant admin content for {TENANT NAME}** button.</span></span> <span data-ttu-id="d967c-155">Selezionare **Sì** per confermare.</span><span class="sxs-lookup"><span data-stu-id="d967c-155">Select **Yes** to confirm.</span></span>

<span data-ttu-id="d967c-156">In **Home** > **Flussi utente\*\*\*\*B2C** > di Azure AD:</span><span class="sxs-lookup"><span data-stu-id="d967c-156">In **Home** > **Azure AD B2C** > **User flows**:</span></span>

[<span data-ttu-id="d967c-157">Creare un flusso utente di iscrizione e accesso</span><span class="sxs-lookup"><span data-stu-id="d967c-157">Create a sign-up and sign-in user flow</span></span>](/azure/active-directory-b2c/tutorial-create-user-flows)

<span data-ttu-id="d967c-158">Come minimo, selezionare **l'attributo** > utente `context.User.Identity.Name` Nome `LoginDisplay` **visualizzato** attestazioni applicazione per popolare l'oggetto nel componente (*Shared/LoginDisplay.razor*).</span><span class="sxs-lookup"><span data-stu-id="d967c-158">At a minimum, select the **Application claims** > **Display Name** user attribute to populate the `context.User.Identity.Name` in the `LoginDisplay` component (*Shared/LoginDisplay.razor*).</span></span>

<span data-ttu-id="d967c-159">Registrare le seguenti informazioni:</span><span class="sxs-lookup"><span data-stu-id="d967c-159">Record the following information:</span></span>

* <span data-ttu-id="d967c-160">Registrare *l'ID applicazione dell'app* client `33333333-3333-3333-3333-333333333333`(ID client) (ad esempio, ).</span><span class="sxs-lookup"><span data-stu-id="d967c-160">Record the *Client app* Application ID (Client ID) (for example, `33333333-3333-3333-3333-333333333333`).</span></span>
* <span data-ttu-id="d967c-161">Registrare il nome del flusso utente di iscrizione e accesso `B2C_1_signupsignin`creato per l'app, ad esempio ).</span><span class="sxs-lookup"><span data-stu-id="d967c-161">Record the sign-up and sign-in user flow name created for the app (for example, `B2C_1_signupsignin`).</span></span>

### <a name="create-the-app"></a><span data-ttu-id="d967c-162">Creare l'app</span><span class="sxs-lookup"><span data-stu-id="d967c-162">Create the app</span></span>

<span data-ttu-id="d967c-163">Sostituire i segnaposto nel comando seguente con le informazioni registrate in precedenza ed eseguire il comando in una shell dei comandi:</span><span class="sxs-lookup"><span data-stu-id="d967c-163">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{DOMAIN}" -ho -ssp "{SIGN UP OR SIGN IN POLICY}" --tenant-id "{TENANT ID}"
```

<span data-ttu-id="d967c-164">Per specificare il percorso di output, che crea una cartella di progetto se non esiste, `-o BlazorSample`includere l'opzione di output nel comando con un percorso, ad esempio ).</span><span class="sxs-lookup"><span data-stu-id="d967c-164">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="d967c-165">Il nome della cartella diventa anche parte del nome del progetto.</span><span class="sxs-lookup"><span data-stu-id="d967c-165">The folder name also becomes part of the project's name.</span></span>

> [!NOTE]
> <span data-ttu-id="d967c-166">Passare l'URI dell'ID app all'opzione, `app-id-uri` ma si noti che potrebbe essere necessaria una modifica alla configurazione nell'app client, descritta nella sezione Ambiti del token di [accesso.](#access-token-scopes)</span><span class="sxs-lookup"><span data-stu-id="d967c-166">Pass the App ID URI to the `app-id-uri` option, but note a configuration change might be required in the client app, which is described in the [Access token scopes](#access-token-scopes) section.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="d967c-167">Configurazione dell'app server</span><span class="sxs-lookup"><span data-stu-id="d967c-167">Server app configuration</span></span>

<span data-ttu-id="d967c-168">*Questa sezione riguarda l'app **Server** della soluzione.*</span><span class="sxs-lookup"><span data-stu-id="d967c-168">*This section pertains to the solution's **Server** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="d967c-169">Pacchetto di autenticazione</span><span class="sxs-lookup"><span data-stu-id="d967c-169">Authentication package</span></span>

<span data-ttu-id="d967c-170">Il supporto per l'autenticazione e l'autorizzazione delle chiamate `Microsoft.AspNetCore.Authentication.AzureADB2C.UI`a ASP.NET API Web principali viene fornito dal:</span><span class="sxs-lookup"><span data-stu-id="d967c-170">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the `Microsoft.AspNetCore.Authentication.AzureADB2C.UI`:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureADB2C.UI" 
    Version="3.1.0" />
```

### <a name="authentication-service-support"></a><span data-ttu-id="d967c-171">Supporto del servizio di autenticazione</span><span class="sxs-lookup"><span data-stu-id="d967c-171">Authentication service support</span></span>

<span data-ttu-id="d967c-172">Il `AddAuthentication` metodo imposta i servizi di autenticazione all'interno dell'app e configura il gestore del bearer JWT come metodo di autenticazione predefinito.</span><span class="sxs-lookup"><span data-stu-id="d967c-172">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="d967c-173">Il `AddAzureADB2CBearer` metodo imposta i parametri specifici nel gestore JWT Bearer necessari per convalidare i token generati da Azure Active Directory B2C:</span><span class="sxs-lookup"><span data-stu-id="d967c-173">The `AddAzureADB2CBearer` method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory B2C:</span></span>

```csharp
services.AddAuthentication(AzureADB2CDefaults.BearerAuthenticationScheme)
    .AddAzureADB2CBearer(options => Configuration.Bind("AzureAdB2C", options));
```

<span data-ttu-id="d967c-174">`UseAuthentication`e `UseAuthorization` assicurarsi che:</span><span class="sxs-lookup"><span data-stu-id="d967c-174">`UseAuthentication` and `UseAuthorization` ensure that:</span></span>

* <span data-ttu-id="d967c-175">L'app tenta di analizzare e convalidare i token nelle richieste in ingresso.</span><span class="sxs-lookup"><span data-stu-id="d967c-175">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="d967c-176">Qualsiasi richiesta che tenti di accedere a una risorsa protetta senza credenziali appropriate ha esito negativo.</span><span class="sxs-lookup"><span data-stu-id="d967c-176">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="useridentityname"></a><span data-ttu-id="d967c-177">User.Identity.Name</span><span class="sxs-lookup"><span data-stu-id="d967c-177">User.Identity.Name</span></span>

<span data-ttu-id="d967c-178">Per impostazione `User.Identity.Name` predefinita, l'oggetto non è popolato.</span><span class="sxs-lookup"><span data-stu-id="d967c-178">By default, the `User.Identity.Name` isn't populated.</span></span>

<span data-ttu-id="d967c-179">Per configurare l'app per `name` la ricezione del valore dal tipo di <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> `Startup.ConfigureServices`attestazione, configurare il [TokenValidationParameters.NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) di in :</span><span class="sxs-lookup"><span data-stu-id="d967c-179">To configure the app to receive the value from the `name` claim type, configure the [TokenValidationParameters.NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

```csharp
services.Configure<JwtBearerOptions>(
    AzureADB2CDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a><span data-ttu-id="d967c-180">Impostazioni app</span><span class="sxs-lookup"><span data-stu-id="d967c-180">App settings</span></span>

<span data-ttu-id="d967c-181">Il file *appsettings.json* contiene le opzioni per configurare il gestore di connessione JWT utilizzato per convalidare i token di accesso.</span><span class="sxs-lookup"><span data-stu-id="d967c-181">The *appsettings.json* file contains the options to configure the JWT bearer handler used to validate access tokens.</span></span>

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

### <a name="weatherforecast-controller"></a><span data-ttu-id="d967c-182">Controller WeatherForecast</span><span class="sxs-lookup"><span data-stu-id="d967c-182">WeatherForecast controller</span></span>

<span data-ttu-id="d967c-183">Il controller WeatherForecast (*Controllers/WeatherForecastController.cs*) espone `[Authorize]` un'API protetta con l'attributo applicato al controller.</span><span class="sxs-lookup"><span data-stu-id="d967c-183">The WeatherForecast controller (*Controllers/WeatherForecastController.cs*) exposes a protected API with the `[Authorize]` attribute applied to the controller.</span></span> <span data-ttu-id="d967c-184">È **importante** comprendere che:</span><span class="sxs-lookup"><span data-stu-id="d967c-184">It's **important** to understand that:</span></span>

* <span data-ttu-id="d967c-185">L'attributo `[Authorize]` in questo controller API è l'unica cosa che protegge questa API da accessi non autorizzati.</span><span class="sxs-lookup"><span data-stu-id="d967c-185">The `[Authorize]` attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="d967c-186">L'attributo `[Authorize]` Blazor utilizzato nell'app WebAssembly funge solo da suggerimento per l'app che l'utente deve essere autorizzato per il corretto funzionamento dell'app.</span><span class="sxs-lookup"><span data-stu-id="d967c-186">The `[Authorize]` attribute used in the Blazor WebAssembly app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

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

## <a name="client-app-configuration"></a><span data-ttu-id="d967c-187">Configurazione dell'app client</span><span class="sxs-lookup"><span data-stu-id="d967c-187">Client app configuration</span></span>

<span data-ttu-id="d967c-188">*Questa sezione riguarda l'app **Client** della soluzione.*</span><span class="sxs-lookup"><span data-stu-id="d967c-188">*This section pertains to the solution's **Client** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="d967c-189">Pacchetto di autenticazione</span><span class="sxs-lookup"><span data-stu-id="d967c-189">Authentication package</span></span>

<span data-ttu-id="d967c-190">Quando viene creata un'app per usare`IndividualB2C`un account B2C individuale ( ), l'app riceve automaticamente un riferimento al pacchetto per la libreria di [autenticazione Microsoft](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span><span class="sxs-lookup"><span data-stu-id="d967c-190">When an app is created to use an Individual B2C Account (`IndividualB2C`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="d967c-191">Il pacchetto fornisce un set di primitive che consentono all'app di autenticare gli utenti e ottenere token per chiamare API protette.</span><span class="sxs-lookup"><span data-stu-id="d967c-191">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="d967c-192">Se si aggiunge l'autenticazione a un'app, aggiungi manualmente il pacchetto al file di progetto dell'app:</span><span class="sxs-lookup"><span data-stu-id="d967c-192">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="d967c-193">Sostituire `{VERSION}` nel riferimento al pacchetto precedente `Microsoft.AspNetCore.Blazor.Templates` con la <xref:blazor/get-started> versione del pacchetto illustrata nell'articolo.</span><span class="sxs-lookup"><span data-stu-id="d967c-193">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="d967c-194">Il `Microsoft.Authentication.WebAssembly.Msal` pacchetto aggiunge in `Microsoft.AspNetCore.Components.WebAssembly.Authentication` modo transitivo il pacchetto all'app.</span><span class="sxs-lookup"><span data-stu-id="d967c-194">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="d967c-195">Supporto del servizio di autenticazione</span><span class="sxs-lookup"><span data-stu-id="d967c-195">Authentication service support</span></span>

<span data-ttu-id="d967c-196">Il supporto per l'autenticazione degli utenti `AddMsalAuthentication` viene registrato `Microsoft.Authentication.WebAssembly.Msal` nel contenitore dei servizi con il metodo di estensione fornito dal pacchetto.</span><span class="sxs-lookup"><span data-stu-id="d967c-196">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="d967c-197">Questo metodo imposta tutti i servizi necessari all'app per interagire con il provider di identità (IP).</span><span class="sxs-lookup"><span data-stu-id="d967c-197">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="d967c-198">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="d967c-198">*Program.cs*:</span></span>

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

<span data-ttu-id="d967c-199">Il `AddMsalAuthentication` metodo accetta un callback per configurare i parametri necessari per autenticare un'app.</span><span class="sxs-lookup"><span data-stu-id="d967c-199">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="d967c-200">I valori necessari per la configurazione dell'app possono essere ottenuti dalla configurazione di Azure Portal AAD quando si registra l'app.</span><span class="sxs-lookup"><span data-stu-id="d967c-200">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

### <a name="access-token-scopes"></a><span data-ttu-id="d967c-201">Ambiti dei token di accessoAccess token scopes</span><span class="sxs-lookup"><span data-stu-id="d967c-201">Access token scopes</span></span>

<span data-ttu-id="d967c-202">Gli ambiti di token di accesso predefiniti rappresentano l'elenco di ambiti di token di accesso:The default access token scopes represent the list of access token scopes that are:</span><span class="sxs-lookup"><span data-stu-id="d967c-202">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="d967c-203">Incluso per impostazione predefinita nella richiesta di accesso.</span><span class="sxs-lookup"><span data-stu-id="d967c-203">Included by default in the sign in request.</span></span>
* <span data-ttu-id="d967c-204">Utilizzato per eseguire il provisioning di un token di accesso immediatamente dopo l'autenticazione.</span><span class="sxs-lookup"><span data-stu-id="d967c-204">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="d967c-205">Tutti gli ambiti devono appartenere alla stessa app per ogni regola di Azure Active Directory.All scopes must belong to the same app per Azure Active Directory rules.</span><span class="sxs-lookup"><span data-stu-id="d967c-205">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="d967c-206">È possibile aggiungere ambiti aggiuntivi per altre app API in base alle esigenze:Additional scopes can be added for additional API apps as needed:</span><span class="sxs-lookup"><span data-stu-id="d967c-206">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="d967c-207">Se il portale di Azure fornisce un URI di ambito e **l'app genera un'eccezione non gestita** quando riceve una risposta *401 Unauthorized* dall'API, provare a usare un URI di ambito che non includa lo schema e l'host.</span><span class="sxs-lookup"><span data-stu-id="d967c-207">If the Azure portal provides a scope URI and **the app throws an unhandled exception** when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="d967c-208">Ad esempio, il portale di Azure può fornire uno dei seguenti formati URI di ambito:</span><span class="sxs-lookup"><span data-stu-id="d967c-208">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="d967c-209">Fornire l'URI dell'ambito senza lo schema e l'host:</span><span class="sxs-lookup"><span data-stu-id="d967c-209">Supply the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

<span data-ttu-id="d967c-210">Per altre informazioni, vedere <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span><span class="sxs-lookup"><span data-stu-id="d967c-210">For more information, see <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span></span>

### <a name="imports-file"></a><span data-ttu-id="d967c-211">File di importazione</span><span class="sxs-lookup"><span data-stu-id="d967c-211">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="d967c-212">Pagina di indice</span><span class="sxs-lookup"><span data-stu-id="d967c-212">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a><span data-ttu-id="d967c-213">Componente dell'app</span><span class="sxs-lookup"><span data-stu-id="d967c-213">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="d967c-214">Componente RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="d967c-214">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="d967c-215">Componente LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="d967c-215">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="d967c-216">Componente di autenticazione</span><span class="sxs-lookup"><span data-stu-id="d967c-216">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="d967c-217">Componente FetchData</span><span class="sxs-lookup"><span data-stu-id="d967c-217">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="d967c-218">Eseguire l'app</span><span class="sxs-lookup"><span data-stu-id="d967c-218">Run the app</span></span>

<span data-ttu-id="d967c-219">Eseguire l'app dal progetto Server.</span><span class="sxs-lookup"><span data-stu-id="d967c-219">Run the app from the Server project.</span></span> <span data-ttu-id="d967c-220">Quando si usa Visual Studio, selezionare il progetto Server in **Esplora soluzioni** e selezionare il pulsante **Esegui** nella barra degli strumenti o avviare l'app dal menu **Debug.**</span><span class="sxs-lookup"><span data-stu-id="d967c-220">When using Visual Studio, select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/wasm-aad-b2c-userflows.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="d967c-221">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="d967c-221">Additional resources</span></span>

* [<span data-ttu-id="d967c-222">Richiedere token di accesso aggiuntiviRequest additional access tokens</span><span class="sxs-lookup"><span data-stu-id="d967c-222">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* <xref:security/authentication/azure-ad-b2c>
* [<span data-ttu-id="d967c-223">Esercitazione: Creare un tenant di Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="d967c-223">Tutorial: Create an Azure Active Directory B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)
* [<span data-ttu-id="d967c-224">Documentazione di Microsoft Identity Platform</span><span class="sxs-lookup"><span data-stu-id="d967c-224">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
