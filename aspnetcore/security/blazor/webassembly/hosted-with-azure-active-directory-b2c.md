---
<span data-ttu-id="f2a73-101">title:' proteggere un' Blazor app ospitata in un ASP.NET Core webassembly con Azure Active Directory B2C ' Author: Description: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="f2a73-101">title: 'Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory B2C' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f2a73-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f2a73-102">'Blazor'</span></span>
- <span data-ttu-id="f2a73-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f2a73-103">'Identity'</span></span>
- <span data-ttu-id="f2a73-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f2a73-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="f2a73-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f2a73-105">'Razor'</span></span>
- <span data-ttu-id="f2a73-106">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="f2a73-106">'SignalR' uid:</span></span> 

---
# <a name="secure-an-aspnet-core-blazor-webassembly-hosted-app-with-azure-active-directory-b2c"></a><span data-ttu-id="f2a73-107">Proteggere un' Blazor app ospitata ASP.NET Core webassembly con Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="f2a73-107">Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory B2C</span></span>

<span data-ttu-id="f2a73-108">Di [Javier Calvarro Nelson](https://github.com/javiercn) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="f2a73-108">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="f2a73-109">Questo articolo descrive come creare un' Blazor app webassembly autonoma che usa [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) per l'autenticazione.</span><span class="sxs-lookup"><span data-stu-id="f2a73-109">This article describes how to create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) for authentication.</span></span>

## <a name="register-apps-in-aad-b2c-and-create-solution"></a><span data-ttu-id="f2a73-110">Registrare le app in AAD B2C e creare la soluzione</span><span class="sxs-lookup"><span data-stu-id="f2a73-110">Register apps in AAD B2C and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="f2a73-111">Creare un tenant</span><span class="sxs-lookup"><span data-stu-id="f2a73-111">Create a tenant</span></span>

<span data-ttu-id="f2a73-112">Seguire le istruzioni riportate in [esercitazione: creare un tenant di Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-create-tenant) per creare un tenant di AAD B2C.</span><span class="sxs-lookup"><span data-stu-id="f2a73-112">Follow the guidance in [Tutorial: Create an Azure Active Directory B2C tenant](/azure/active-directory-b2c/tutorial-create-tenant) to create an AAD B2C tenant.</span></span>

<span data-ttu-id="f2a73-113">Registrare le seguenti informazioni:</span><span class="sxs-lookup"><span data-stu-id="f2a73-113">Record the following information:</span></span>

* <span data-ttu-id="f2a73-114">AAD B2C istanza (ad esempio, `https://contoso.b2clogin.com/` , che include la barra finale)</span><span class="sxs-lookup"><span data-stu-id="f2a73-114">AAD B2C instance (for example, `https://contoso.b2clogin.com/`, which includes the trailing slash)</span></span>
* <span data-ttu-id="f2a73-115">AAD B2C dominio tenant (ad esempio, `contoso.onmicrosoft.com` )</span><span class="sxs-lookup"><span data-stu-id="f2a73-115">AAD B2C Tenant domain (for example, `contoso.onmicrosoft.com`)</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="f2a73-116">Registrare un'app per le API server</span><span class="sxs-lookup"><span data-stu-id="f2a73-116">Register a server API app</span></span>

<span data-ttu-id="f2a73-117">Seguire le istruzioni riportate in [esercitazione: registrare un'applicazione in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) per registrare un'app AAD per l'app per le *API del server*:</span><span class="sxs-lookup"><span data-stu-id="f2a73-117">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) to register an AAD app for the *Server API app*:</span></span>

1. <span data-ttu-id="f2a73-118">In **Azure Active Directory**  >  **registrazioni app**selezionare **nuova registrazione**.</span><span class="sxs-lookup"><span data-stu-id="f2a73-118">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="f2a73-119">Specificare un **nome** per l'app, ad esempio \*\* Blazor Server AAD B2C\*\*.</span><span class="sxs-lookup"><span data-stu-id="f2a73-119">Provide a **Name** for the app (for example, **Blazor Server AAD B2C**).</span></span>
1. <span data-ttu-id="f2a73-120">Per i **tipi di account supportati**, selezionare l'opzione multi-tenant: **account in qualsiasi directory organizzativa o provider di identità. Per l'autenticazione degli utenti con Azure AD B2C.**</span><span class="sxs-lookup"><span data-stu-id="f2a73-120">For **Supported account types**, select the multi-tenant option: **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span>
1. <span data-ttu-id="f2a73-121">L' *app* per le API del server non richiede un **URI di reindirizzamento** in questo scenario, quindi lasciare l'elenco a discesa impostato su **Web** e non immettere un URI di reindirizzamento.</span><span class="sxs-lookup"><span data-stu-id="f2a73-121">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="f2a73-122">Verificare che **Permissions**  >  **le autorizzazioni concessino l'amministratore concent a OpenID e offline_access autorizzazioni** siano abilitate.</span><span class="sxs-lookup"><span data-stu-id="f2a73-122">Confirm that **Permissions** > **Grant admin concent to openid and offline_access permissions** is enabled.</span></span>
1. <span data-ttu-id="f2a73-123">Selezionare **Registra**.</span><span class="sxs-lookup"><span data-stu-id="f2a73-123">Select **Register**.</span></span>

<span data-ttu-id="f2a73-124">Registrare le seguenti informazioni:</span><span class="sxs-lookup"><span data-stu-id="f2a73-124">Record the following information:</span></span>

* <span data-ttu-id="f2a73-125">*App per le API server* ID applicazione (ID client) (ad esempio, `11111111-1111-1111-1111-111111111111` )</span><span class="sxs-lookup"><span data-stu-id="f2a73-125">*Server API app* Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`)</span></span>
* <span data-ttu-id="f2a73-126">ID directory (ID tenant) (ad esempio, `222222222-2222-2222-2222-222222222222` )</span><span class="sxs-lookup"><span data-stu-id="f2a73-126">Directory ID (Tenant ID) (for example, `222222222-2222-2222-2222-222222222222`)</span></span>
* <span data-ttu-id="f2a73-127">Dominio del tenant AAD (ad esempio, `contoso.onmicrosoft.com` ) &ndash; il dominio è disponibile come **dominio del server di pubblicazione** nel pannello **personalizzazione** del portale di Azure per l'app registrata.</span><span class="sxs-lookup"><span data-stu-id="f2a73-127">AAD Tenant domain (for example, `contoso.onmicrosoft.com`) &ndash; The domain is available as the **Publisher domain** in the **Branding** blade of the Azure portal for the registered app.</span></span>

<span data-ttu-id="f2a73-128">In **esporre un'API**:</span><span class="sxs-lookup"><span data-stu-id="f2a73-128">In **Expose an API**:</span></span>

1. <span data-ttu-id="f2a73-129">Selezionare **Aggiungi un ambito**.</span><span class="sxs-lookup"><span data-stu-id="f2a73-129">Select **Add a scope**.</span></span>
1. <span data-ttu-id="f2a73-130">Fai clic su **Salva e continua**.</span><span class="sxs-lookup"><span data-stu-id="f2a73-130">Select **Save and continue**.</span></span>
1. <span data-ttu-id="f2a73-131">Specificare un **nome** per l'ambito (ad esempio, `API.Access` ).</span><span class="sxs-lookup"><span data-stu-id="f2a73-131">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="f2a73-132">Fornire un **nome visualizzato** per il consenso dell'amministratore, ad esempio `Access API` .</span><span class="sxs-lookup"><span data-stu-id="f2a73-132">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="f2a73-133">Fornire una **Descrizione del consenso dell'amministratore** , ad esempio `Allows the app to access server app API endpoints.` .</span><span class="sxs-lookup"><span data-stu-id="f2a73-133">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="f2a73-134">Verificare che lo **stato** sia impostato su **abilitato**.</span><span class="sxs-lookup"><span data-stu-id="f2a73-134">Confirm that the **State** is set to **Enabled**.</span></span>
1. <span data-ttu-id="f2a73-135">Selezionare **Aggiungi ambito**.</span><span class="sxs-lookup"><span data-stu-id="f2a73-135">Select **Add scope**.</span></span>

<span data-ttu-id="f2a73-136">Registrare le seguenti informazioni:</span><span class="sxs-lookup"><span data-stu-id="f2a73-136">Record the following information:</span></span>

* <span data-ttu-id="f2a73-137">URI ID app (ad esempio, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111` , `api://11111111-1111-1111-1111-111111111111` o il valore personalizzato specificato)</span><span class="sxs-lookup"><span data-stu-id="f2a73-137">App ID URI (for example, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, `api://11111111-1111-1111-1111-111111111111`, or the custom value that you provided)</span></span>
* <span data-ttu-id="f2a73-138">Ambito predefinito (ad esempio, `API.Access` )</span><span class="sxs-lookup"><span data-stu-id="f2a73-138">Default scope (for example, `API.Access`)</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="f2a73-139">Registrare un'app client</span><span class="sxs-lookup"><span data-stu-id="f2a73-139">Register a client app</span></span>

<span data-ttu-id="f2a73-140">Seguire le istruzioni riportate in [esercitazione: registrare un'applicazione in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) di nuovo per registrare un'app AAD per l' *app client*:</span><span class="sxs-lookup"><span data-stu-id="f2a73-140">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) again to register an AAD app for the *Client app*:</span></span>

1. <span data-ttu-id="f2a73-141">In **Azure Active Directory**  >  **registrazioni app**selezionare **nuova registrazione**.</span><span class="sxs-lookup"><span data-stu-id="f2a73-141">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="f2a73-142">Specificare un **nome** per l'app, ad esempio \*\* Blazor AAD B2C client\*\*.</span><span class="sxs-lookup"><span data-stu-id="f2a73-142">Provide a **Name** for the app (for example, **Blazor Client AAD B2C**).</span></span>
1. <span data-ttu-id="f2a73-143">Per i **tipi di account supportati**, selezionare l'opzione multi-tenant: **account in qualsiasi directory organizzativa o provider di identità. Per l'autenticazione degli utenti con Azure AD B2C.**</span><span class="sxs-lookup"><span data-stu-id="f2a73-143">For **Supported account types**, select the multi-tenant option: **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span>
1. <span data-ttu-id="f2a73-144">Lasciare l'elenco a discesa **URI di reindirizzamento** impostato su **Web**e specificare l'URI di reindirizzamento seguente: `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="f2a73-144">Leave the **Redirect URI** drop down set to **Web**, and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="f2a73-145">La porta predefinita per un'app in esecuzione su gheppio è 5001.</span><span class="sxs-lookup"><span data-stu-id="f2a73-145">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="f2a73-146">Per IIS Express, la porta generata in modo casuale è reperibile nelle proprietà dell'app Server nel pannello **debug** .</span><span class="sxs-lookup"><span data-stu-id="f2a73-146">For IIS Express, the randomly generated port can be found in the Server app's properties in the **Debug** panel.</span></span>
1. <span data-ttu-id="f2a73-147">Verificare che **Permissions**  >  **le autorizzazioni concessino l'amministratore concent a OpenID e offline_access autorizzazioni** siano abilitate.</span><span class="sxs-lookup"><span data-stu-id="f2a73-147">Confirm that **Permissions** > **Grant admin concent to openid and offline_access permissions** is enabled.</span></span>
1. <span data-ttu-id="f2a73-148">Selezionare **Registra**.</span><span class="sxs-lookup"><span data-stu-id="f2a73-148">Select **Register**.</span></span>

<span data-ttu-id="f2a73-149">Registrare l'ID applicazione (ID client) (ad esempio, `11111111-1111-1111-1111-111111111111` ).</span><span class="sxs-lookup"><span data-stu-id="f2a73-149">Record the Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`).</span></span>

<span data-ttu-id="f2a73-150">In **Authentication**  >  **configurazioni piattaforma**di autenticazione  >  **Web**:</span><span class="sxs-lookup"><span data-stu-id="f2a73-150">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="f2a73-151">Verificare che l' **URI di reindirizzamento** di `https://localhost:{PORT}/authentication/login-callback` sia presente.</span><span class="sxs-lookup"><span data-stu-id="f2a73-151">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="f2a73-152">Per **concessione implicita**, selezionare le caselle di controllo per i token di **accesso** e i **token ID**.</span><span class="sxs-lookup"><span data-stu-id="f2a73-152">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="f2a73-153">Per questa esperienza sono accettabili le impostazioni predefinite rimanenti per l'app.</span><span class="sxs-lookup"><span data-stu-id="f2a73-153">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="f2a73-154">Fare clic sul pulsante **Salva**.</span><span class="sxs-lookup"><span data-stu-id="f2a73-154">Select the **Save** button.</span></span>

<span data-ttu-id="f2a73-155">In **autorizzazioni API**:</span><span class="sxs-lookup"><span data-stu-id="f2a73-155">In **API permissions**:</span></span>

1. <span data-ttu-id="f2a73-156">Selezionare **Aggiungi un'autorizzazione** seguita da **API personali**.</span><span class="sxs-lookup"><span data-stu-id="f2a73-156">Select **Add a permission** followed by **My APIs**.</span></span>
1. <span data-ttu-id="f2a73-157">Selezionare l' *app per le API server* dalla colonna **nome** , ad esempio \*\* Blazor Server AAD B2C\*\*.</span><span class="sxs-lookup"><span data-stu-id="f2a73-157">Select the *Server API app* from the **Name** column (for example, **Blazor Server AAD B2C**).</span></span>
1. <span data-ttu-id="f2a73-158">Aprire l'elenco di **API** .</span><span class="sxs-lookup"><span data-stu-id="f2a73-158">Open the **API** list.</span></span>
1. <span data-ttu-id="f2a73-159">Abilitare l'accesso all'API (ad esempio, `API.Access` ).</span><span class="sxs-lookup"><span data-stu-id="f2a73-159">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="f2a73-160">Selezionare **Aggiungi autorizzazioni**.</span><span class="sxs-lookup"><span data-stu-id="f2a73-160">Select **Add permissions**.</span></span>
1. <span data-ttu-id="f2a73-161">Selezionare il pulsante **Concedi contenuto amministratore per {tenant Name}** .</span><span class="sxs-lookup"><span data-stu-id="f2a73-161">Select the **Grant admin content for {TENANT NAME}** button.</span></span> <span data-ttu-id="f2a73-162">Selezionare **Sì** per confermare.</span><span class="sxs-lookup"><span data-stu-id="f2a73-162">Select **Yes** to confirm.</span></span>

<span data-ttu-id="f2a73-163">In **Home**  >  **Azure ad B2C**  >  **flussi utente**:</span><span class="sxs-lookup"><span data-stu-id="f2a73-163">In **Home** > **Azure AD B2C** > **User flows**:</span></span>

[<span data-ttu-id="f2a73-164">Creare un flusso utente di iscrizione e accesso</span><span class="sxs-lookup"><span data-stu-id="f2a73-164">Create a sign-up and sign-in user flow</span></span>](/azure/active-directory-b2c/tutorial-create-user-flows)

<span data-ttu-id="f2a73-165">Selezionare almeno l'attributo utente nome visualizzato dell' **applicazione Claims**  >  **Display Name** per popolare `context.User.Identity.Name` nel `LoginDisplay` componente (*Shared/LoginDisplay. Razor*).</span><span class="sxs-lookup"><span data-stu-id="f2a73-165">At a minimum, select the **Application claims** > **Display Name** user attribute to populate the `context.User.Identity.Name` in the `LoginDisplay` component (*Shared/LoginDisplay.razor*).</span></span>

<span data-ttu-id="f2a73-166">Registrare il nome del flusso utente di iscrizione e accesso creato per l'app (ad esempio, `B2C_1_signupsignin` ).</span><span class="sxs-lookup"><span data-stu-id="f2a73-166">Record the sign-up and sign-in user flow name created for the app (for example, `B2C_1_signupsignin`).</span></span>

### <a name="create-the-app"></a><span data-ttu-id="f2a73-167">Creare l'app</span><span class="sxs-lookup"><span data-stu-id="f2a73-167">Create the app</span></span>

<span data-ttu-id="f2a73-168">Sostituire i segnaposto nel comando seguente con le informazioni registrate in precedenza ed eseguire il comando in una shell dei comandi:</span><span class="sxs-lookup"><span data-stu-id="f2a73-168">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{TENANT DOMAIN}" -ho -ssp "{SIGN UP OR SIGN IN POLICY}" --tenant-id "{TENANT ID}"
```

<span data-ttu-id="f2a73-169">Per specificare il percorso di output, che crea una cartella di progetto, se non esiste, includere l'opzione di output nel comando con un percorso (ad esempio, `-o BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="f2a73-169">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="f2a73-170">Il nome della cartella diventa anche parte del nome del progetto.</span><span class="sxs-lookup"><span data-stu-id="f2a73-170">The folder name also becomes part of the project's name.</span></span>

> [!NOTE]
> <span data-ttu-id="f2a73-171">Passare l'URI ID app all' `app-id-uri` opzione, ma si noti che potrebbe essere necessaria una modifica alla configurazione nell'app client, descritta nella sezione [ambiti dei token di accesso](#access-token-scopes) .</span><span class="sxs-lookup"><span data-stu-id="f2a73-171">Pass the App ID URI to the `app-id-uri` option, but note a configuration change might be required in the client app, which is described in the [Access token scopes](#access-token-scopes) section.</span></span>
>
> <span data-ttu-id="f2a73-172">Inoltre, l'ambito impostato dal Blazor modello ospitato potrebbe avere ripetutamente l'host Uri ID app.</span><span class="sxs-lookup"><span data-stu-id="f2a73-172">Additionally, the scope set up by the Hosted Blazor template might have the App ID URI host repeated.</span></span> <span data-ttu-id="f2a73-173">Verificare che l'ambito configurato per la `DefaultAccessTokenScopes` raccolta sia corretto in `Program.Main` (*Program.cs*) dell' *app client*.</span><span class="sxs-lookup"><span data-stu-id="f2a73-173">Confirm that the scope configured for the `DefaultAccessTokenScopes` collection is correct in `Program.Main` (*Program.cs*) of the *Client app*.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="f2a73-174">Configurazione dell'app Server</span><span class="sxs-lookup"><span data-stu-id="f2a73-174">Server app configuration</span></span>

<span data-ttu-id="f2a73-175">*Questa sezione è relativa all'app **Server** della soluzione.*</span><span class="sxs-lookup"><span data-stu-id="f2a73-175">*This section pertains to the solution's **Server** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="f2a73-176">Pacchetto di autenticazione</span><span class="sxs-lookup"><span data-stu-id="f2a73-176">Authentication package</span></span>

<span data-ttu-id="f2a73-177">Il supporto per l'autenticazione e l'autorizzazione delle chiamate a ASP.NET Core API Web viene fornito da `Microsoft.AspNetCore.Authentication.AzureADB2C.UI` :</span><span class="sxs-lookup"><span data-stu-id="f2a73-177">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the `Microsoft.AspNetCore.Authentication.AzureADB2C.UI`:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureADB2C.UI" 
  Version="3.2.0" />
```

### <a name="authentication-service-support"></a><span data-ttu-id="f2a73-178">Supporto del servizio di autenticazione</span><span class="sxs-lookup"><span data-stu-id="f2a73-178">Authentication service support</span></span>

<span data-ttu-id="f2a73-179">Il `AddAuthentication` metodo imposta i servizi di autenticazione all'interno dell'app e configura il gestore di JWT Bearer come metodo di autenticazione predefinito.</span><span class="sxs-lookup"><span data-stu-id="f2a73-179">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="f2a73-180">Il `AddAzureADB2CBearer` metodo imposta i parametri specifici nel gestore di JWT Bearer necessario per convalidare i token emessi dal Azure Active Directory B2C:</span><span class="sxs-lookup"><span data-stu-id="f2a73-180">The `AddAzureADB2CBearer` method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory B2C:</span></span>

```csharp
services.AddAuthentication(AzureADB2CDefaults.BearerAuthenticationScheme)
    .AddAzureADB2CBearer(options => Configuration.Bind("AzureAdB2C", options));
```

<span data-ttu-id="f2a73-181">`UseAuthentication`e `UseAuthorization` verificare che:</span><span class="sxs-lookup"><span data-stu-id="f2a73-181">`UseAuthentication` and `UseAuthorization` ensure that:</span></span>

* <span data-ttu-id="f2a73-182">L'app tenta di analizzare e convalidare i token nelle richieste in ingresso.</span><span class="sxs-lookup"><span data-stu-id="f2a73-182">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="f2a73-183">Eventuali richieste che tentano di accedere a una risorsa protetta senza credenziali appropriate hanno esito negativo.</span><span class="sxs-lookup"><span data-stu-id="f2a73-183">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="useridentityname"></a><span data-ttu-id="f2a73-184">Utente. Identity . Nome</span><span class="sxs-lookup"><span data-stu-id="f2a73-184">User.Identity.Name</span></span>

<span data-ttu-id="f2a73-185">Per impostazione predefinita, `User.Identity.Name` non viene popolato.</span><span class="sxs-lookup"><span data-stu-id="f2a73-185">By default, the `User.Identity.Name` isn't populated.</span></span>

<span data-ttu-id="f2a73-186">Per configurare l'app per la ricezione del valore dal `name` tipo di attestazione, configurare [TokenValidationParameters. NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) di <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="f2a73-186">To configure the app to receive the value from the `name` claim type, configure the [TokenValidationParameters.NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;

...

services.Configure<JwtBearerOptions>(
    AzureADB2CDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a><span data-ttu-id="f2a73-187">Impostazioni dell'app</span><span class="sxs-lookup"><span data-stu-id="f2a73-187">App settings</span></span>

<span data-ttu-id="f2a73-188">Il file *appSettings. JSON* contiene le opzioni per configurare il gestore di connessione JWT usato per convalidare i token di accesso.</span><span class="sxs-lookup"><span data-stu-id="f2a73-188">The *appsettings.json* file contains the options to configure the JWT bearer handler used to validate access tokens.</span></span>

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

<span data-ttu-id="f2a73-189">Esempio:</span><span class="sxs-lookup"><span data-stu-id="f2a73-189">Example:</span></span>

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

### <a name="weatherforecast-controller"></a><span data-ttu-id="f2a73-190">Controller WeatherForecast</span><span class="sxs-lookup"><span data-stu-id="f2a73-190">WeatherForecast controller</span></span>

<span data-ttu-id="f2a73-191">Il controller WeatherForecast (*Controllers/WeatherForecastController. cs*) espone un'API protetta con l' `[Authorize]` attributo applicato al controller.</span><span class="sxs-lookup"><span data-stu-id="f2a73-191">The WeatherForecast controller (*Controllers/WeatherForecastController.cs*) exposes a protected API with the `[Authorize]` attribute applied to the controller.</span></span> <span data-ttu-id="f2a73-192">È **importante** comprendere che:</span><span class="sxs-lookup"><span data-stu-id="f2a73-192">It's **important** to understand that:</span></span>

* <span data-ttu-id="f2a73-193">L' `[Authorize]` attributo in questo controller API è l'unico elemento che protegge questa API da accessi non autorizzati.</span><span class="sxs-lookup"><span data-stu-id="f2a73-193">The `[Authorize]` attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="f2a73-194">L' `[Authorize]` attributo usato nell' Blazor app webassembly funge solo da hint per l'app che l'utente deve essere autorizzato affinché l'app funzioni correttamente.</span><span class="sxs-lookup"><span data-stu-id="f2a73-194">The `[Authorize]` attribute used in the Blazor WebAssembly app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

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

## <a name="client-app-configuration"></a><span data-ttu-id="f2a73-195">Configurazione dell'app client</span><span class="sxs-lookup"><span data-stu-id="f2a73-195">Client app configuration</span></span>

<span data-ttu-id="f2a73-196">*Questa sezione riguarda l'app **client** della soluzione.*</span><span class="sxs-lookup"><span data-stu-id="f2a73-196">*This section pertains to the solution's **Client** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="f2a73-197">Pacchetto di autenticazione</span><span class="sxs-lookup"><span data-stu-id="f2a73-197">Authentication package</span></span>

<span data-ttu-id="f2a73-198">Quando viene creata un'app per usare un singolo account B2C ( `IndividualB2C` ), l'app riceve automaticamente un riferimento al pacchetto per [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ( `Microsoft.Authentication.WebAssembly.Msal` ).</span><span class="sxs-lookup"><span data-stu-id="f2a73-198">When an app is created to use an Individual B2C Account (`IndividualB2C`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="f2a73-199">Il pacchetto fornisce un set di primitive che consentono all'app di autenticare gli utenti e ottenere i token per chiamare le API protette.</span><span class="sxs-lookup"><span data-stu-id="f2a73-199">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="f2a73-200">Se si aggiunge l'autenticazione a un'app, aggiungere manualmente il pacchetto al file di progetto dell'app:</span><span class="sxs-lookup"><span data-stu-id="f2a73-200">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="3.2.0" />
```

<span data-ttu-id="f2a73-201">Il `Microsoft.Authentication.WebAssembly.Msal` pacchetto aggiunge il `Microsoft.AspNetCore.Components.WebAssembly.Authentication` pacchetto all'app in modo transitivo.</span><span class="sxs-lookup"><span data-stu-id="f2a73-201">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="f2a73-202">Supporto del servizio di autenticazione</span><span class="sxs-lookup"><span data-stu-id="f2a73-202">Authentication service support</span></span>

<span data-ttu-id="f2a73-203">`HttpClient`Viene aggiunto il supporto per le istanze di che includono token di accesso quando si effettuano richieste al progetto server.</span><span class="sxs-lookup"><span data-stu-id="f2a73-203">Support for `HttpClient` instances is added that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="f2a73-204">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="f2a73-204">*Program.cs*:</span></span>

```csharp
builder.Services.AddHttpClient("{APP ASSEMBLY}.ServerAPI", client => 
    client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddTransient(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("{APP ASSEMBLY}.ServerAPI"));
```

<span data-ttu-id="f2a73-205">Il supporto per l'autenticazione degli utenti viene registrato nel contenitore del servizio con il `AddMsalAuthentication` metodo di estensione fornito dal `Microsoft.Authentication.WebAssembly.Msal` pacchetto.</span><span class="sxs-lookup"><span data-stu-id="f2a73-205">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="f2a73-206">Questo metodo configura i servizi necessari per l'interazione dell'app con il Identity provider (IP).</span><span class="sxs-lookup"><span data-stu-id="f2a73-206">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="f2a73-207">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="f2a73-207">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAdB2C", options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="f2a73-208">Il `AddMsalAuthentication` metodo accetta un callback per configurare i parametri necessari per autenticare un'app.</span><span class="sxs-lookup"><span data-stu-id="f2a73-208">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="f2a73-209">Quando si registra l'app, è possibile ottenere i valori necessari per la configurazione dell'app dalla configurazione di AAD del portale di Azure.</span><span class="sxs-lookup"><span data-stu-id="f2a73-209">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

<span data-ttu-id="f2a73-210">La configurazione viene fornita dal file *wwwroot/appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="f2a73-210">Configuration is supplied by the *wwwroot/appsettings.json* file:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "{AAD B2C INSTANCE}{TENANT DOMAIN}/{SIGN UP OR SIGN IN POLICY}",
    "ClientId": "{CLIENT APP CLIENT ID}",
    "ValidateAuthority": false
  }
}
```

<span data-ttu-id="f2a73-211">Esempio:</span><span class="sxs-lookup"><span data-stu-id="f2a73-211">Example:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "https://contoso.b2clogin.com/contoso.onmicrosoft.com/B2C_1_signupsignin1",
    "ClientId": "4369008b-21fa-427c-abaa-9b53bf58e538",
    "ValidateAuthority": false
  }
}
```

### <a name="access-token-scopes"></a><span data-ttu-id="f2a73-212">Ambiti del token di accesso</span><span class="sxs-lookup"><span data-stu-id="f2a73-212">Access token scopes</span></span>

<span data-ttu-id="f2a73-213">Gli ambiti dei token di accesso predefiniti rappresentano l'elenco degli ambiti dei token di accesso:</span><span class="sxs-lookup"><span data-stu-id="f2a73-213">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="f2a73-214">Incluso per impostazione predefinita nella richiesta di accesso.</span><span class="sxs-lookup"><span data-stu-id="f2a73-214">Included by default in the sign in request.</span></span>
* <span data-ttu-id="f2a73-215">Utilizzato per eseguire il provisioning di un token di accesso immediatamente dopo l'autenticazione.</span><span class="sxs-lookup"><span data-stu-id="f2a73-215">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="f2a73-216">Tutti gli ambiti devono appartenere alla stessa app per ogni regola di Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="f2a73-216">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="f2a73-217">Per altre app per le API è possibile aggiungere altri ambiti, se necessario:</span><span class="sxs-lookup"><span data-stu-id="f2a73-217">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

<span data-ttu-id="f2a73-218">Per ulteriori informazioni, vedere le sezioni seguenti dell'articolo *scenari aggiuntivi* :</span><span class="sxs-lookup"><span data-stu-id="f2a73-218">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="f2a73-219">Richiedere token di accesso aggiuntivi</span><span class="sxs-lookup"><span data-stu-id="f2a73-219">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="f2a73-220">Connetti token alle richieste in uscita</span><span class="sxs-lookup"><span data-stu-id="f2a73-220">Attach tokens to outgoing requests</span></span>](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)


### <a name="imports-file"></a><span data-ttu-id="f2a73-221">Importa file</span><span class="sxs-lookup"><span data-stu-id="f2a73-221">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="f2a73-222">Pagina di indice</span><span class="sxs-lookup"><span data-stu-id="f2a73-222">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a><span data-ttu-id="f2a73-223">Componente app</span><span class="sxs-lookup"><span data-stu-id="f2a73-223">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="f2a73-224">Componente RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="f2a73-224">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="f2a73-225">Componente LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="f2a73-225">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="f2a73-226">Componente di autenticazione</span><span class="sxs-lookup"><span data-stu-id="f2a73-226">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="f2a73-227">Componente FetchData</span><span class="sxs-lookup"><span data-stu-id="f2a73-227">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="f2a73-228">Eseguire l'app</span><span class="sxs-lookup"><span data-stu-id="f2a73-228">Run the app</span></span>

<span data-ttu-id="f2a73-229">Eseguire l'app dal progetto server.</span><span class="sxs-lookup"><span data-stu-id="f2a73-229">Run the app from the Server project.</span></span> <span data-ttu-id="f2a73-230">Quando si usa Visual Studio, eseguire una delle operazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="f2a73-230">When using Visual Studio, either:</span></span>

* <span data-ttu-id="f2a73-231">Impostare l'elenco a discesa **progetti di avvio** nella barra degli strumenti sull'app per le *API server* e selezionare il pulsante **Esegui** .</span><span class="sxs-lookup"><span data-stu-id="f2a73-231">Set the **Startup Projects** drop down list in the toolbar to the *Server API app* and select the **Run** button.</span></span>
* <span data-ttu-id="f2a73-232">Selezionare il progetto server in **Esplora soluzioni** , quindi fare clic sul pulsante **Esegui** nella barra degli strumenti o avviare l'app dal menu **debug** .</span><span class="sxs-lookup"><span data-stu-id="f2a73-232">Select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/wasm-aad-b2c-userflows.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="f2a73-233">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="f2a73-233">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* [<span data-ttu-id="f2a73-234">Richieste API Web non autenticate o non autorizzate in un'app con un client predefinito sicuro</span><span class="sxs-lookup"><span data-stu-id="f2a73-234">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:security/blazor/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/authentication/azure-ad-b2c>
* [<span data-ttu-id="f2a73-235">Esercitazione: Creare un tenant di Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="f2a73-235">Tutorial: Create an Azure Active Directory B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)
* [<span data-ttu-id="f2a73-236">Documentazione di Microsoft Identity Platform</span><span class="sxs-lookup"><span data-stu-id="f2a73-236">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
