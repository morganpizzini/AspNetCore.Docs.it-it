---
<span data-ttu-id="1fdc7-101">title: "proteggere un' Blazor app ASP.NET Core webassembly autonoma con Azure Active Directory B2C" autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="1fdc7-101">title: 'Secure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory B2C' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1fdc7-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1fdc7-102">'Blazor'</span></span>
- <span data-ttu-id="1fdc7-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1fdc7-103">'Identity'</span></span>
- <span data-ttu-id="1fdc7-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1fdc7-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="1fdc7-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1fdc7-105">'Razor'</span></span>
- <span data-ttu-id="1fdc7-106">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="1fdc7-106">'SignalR' uid:</span></span> 

---
# <a name="secure-an-aspnet-core-blazor-webassembly-standalone-app-with-azure-active-directory-b2c"></a><span data-ttu-id="1fdc7-107">Proteggere un' Blazor app ASP.NET Core webassembly autonoma con Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="1fdc7-107">Secure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory B2C</span></span>

<span data-ttu-id="1fdc7-108">Di [Javier Calvarro Nelson](https://github.com/javiercn) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="1fdc7-108">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="1fdc7-109">Per creare un' Blazor app webassembly autonoma che usa [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) per l'autenticazione:</span><span class="sxs-lookup"><span data-stu-id="1fdc7-109">To create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) for authentication:</span></span>

<span data-ttu-id="1fdc7-110">Per creare un tenant e registrare un'app Web nel portale di Azure, seguire le istruzioni riportate negli argomenti seguenti:</span><span class="sxs-lookup"><span data-stu-id="1fdc7-110">Follow the guidance in the following topics to create a tenant and register a web app in the Azure Portal:</span></span>

[<span data-ttu-id="1fdc7-111">Creare un tenant di AAD B2C</span><span class="sxs-lookup"><span data-stu-id="1fdc7-111">Create an AAD B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)

<span data-ttu-id="1fdc7-112">Registrare le seguenti informazioni:</span><span class="sxs-lookup"><span data-stu-id="1fdc7-112">Record the following information:</span></span>

* <span data-ttu-id="1fdc7-113">AAD B2C istanza, ad esempio, `https://contoso.b2clogin.com/` che include la barra finale.</span><span class="sxs-lookup"><span data-stu-id="1fdc7-113">AAD B2C instance (for example, `https://contoso.b2clogin.com/`, which includes the trailing slash).</span></span>
* <span data-ttu-id="1fdc7-114">AAD B2C dominio del tenant (ad esempio, `contoso.onmicrosoft.com` ).</span><span class="sxs-lookup"><span data-stu-id="1fdc7-114">AAD B2C Tenant domain (for example, `contoso.onmicrosoft.com`).</span></span>

<span data-ttu-id="1fdc7-115">Seguire le istruzioni riportate in [esercitazione: registrare un'applicazione in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) di nuovo per registrare un'app AAD per l' *app client*:</span><span class="sxs-lookup"><span data-stu-id="1fdc7-115">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) again to register an AAD app for the *Client app*:</span></span>

1. <span data-ttu-id="1fdc7-116">In **Azure Active Directory**  >  **registrazioni app**selezionare **nuova registrazione**.</span><span class="sxs-lookup"><span data-stu-id="1fdc7-116">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="1fdc7-117">Specificare un **nome** per l'app, ad esempio \*\* Blazor AAD B2C autonomo\*\*.</span><span class="sxs-lookup"><span data-stu-id="1fdc7-117">Provide a **Name** for the app (for example, **Blazor Standalone AAD B2C**).</span></span>
1. <span data-ttu-id="1fdc7-118">Per i **tipi di account supportati**, selezionare l'opzione multi-tenant: **account in qualsiasi directory organizzativa o provider di identità. Per l'autenticazione degli utenti con Azure AD B2C.**</span><span class="sxs-lookup"><span data-stu-id="1fdc7-118">For **Supported account types**, select the multi-tenant option: **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span>
1. <span data-ttu-id="1fdc7-119">Lasciare l'elenco a discesa **URI di reindirizzamento** impostato su **Web**e specificare l'URI di reindirizzamento seguente: `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="1fdc7-119">Leave the **Redirect URI** drop down set to **Web**, and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="1fdc7-120">La porta predefinita per un'app in esecuzione su gheppio è 5001.</span><span class="sxs-lookup"><span data-stu-id="1fdc7-120">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="1fdc7-121">Per IIS Express, la porta generata in modo casuale si trova nelle proprietà dell'app nel pannello **debug** .</span><span class="sxs-lookup"><span data-stu-id="1fdc7-121">For IIS Express, the randomly generated port can be found in the app's properties in the **Debug** panel.</span></span>
1. <span data-ttu-id="1fdc7-122">Verificare che **Permissions**  >  **le autorizzazioni concessino l'amministratore concent a OpenID e offline_access autorizzazioni** siano abilitate.</span><span class="sxs-lookup"><span data-stu-id="1fdc7-122">Confirm that **Permissions** > **Grant admin concent to openid and offline_access permissions** is enabled.</span></span>
1. <span data-ttu-id="1fdc7-123">Selezionare **Registra**.</span><span class="sxs-lookup"><span data-stu-id="1fdc7-123">Select **Register**.</span></span>

<span data-ttu-id="1fdc7-124">Registrare l'ID applicazione (ID client) (ad esempio, `11111111-1111-1111-1111-111111111111` ).</span><span class="sxs-lookup"><span data-stu-id="1fdc7-124">Record the Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`).</span></span>

<span data-ttu-id="1fdc7-125">In **Authentication**  >  **configurazioni piattaforma**di autenticazione  >  **Web**:</span><span class="sxs-lookup"><span data-stu-id="1fdc7-125">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="1fdc7-126">Verificare che l' **URI di reindirizzamento** di `https://localhost:{PORT}/authentication/login-callback` sia presente.</span><span class="sxs-lookup"><span data-stu-id="1fdc7-126">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="1fdc7-127">Per **concessione implicita**, selezionare le caselle di controllo per i token di **accesso** e i **token ID**.</span><span class="sxs-lookup"><span data-stu-id="1fdc7-127">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="1fdc7-128">Per questa esperienza sono accettabili le impostazioni predefinite rimanenti per l'app.</span><span class="sxs-lookup"><span data-stu-id="1fdc7-128">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="1fdc7-129">Fare clic sul pulsante **Salva**.</span><span class="sxs-lookup"><span data-stu-id="1fdc7-129">Select the **Save** button.</span></span>

<span data-ttu-id="1fdc7-130">In **Home**  >  **Azure ad B2C**  >  **flussi utente**:</span><span class="sxs-lookup"><span data-stu-id="1fdc7-130">In **Home** > **Azure AD B2C** > **User flows**:</span></span>

[<span data-ttu-id="1fdc7-131">Creare un flusso utente di iscrizione e accesso</span><span class="sxs-lookup"><span data-stu-id="1fdc7-131">Create a sign-up and sign-in user flow</span></span>](/azure/active-directory-b2c/tutorial-create-user-flows)

<span data-ttu-id="1fdc7-132">Selezionare almeno l'attributo utente nome visualizzato dell' **applicazione Claims**  >  **Display Name** per popolare `context.User.Identity.Name` nel `LoginDisplay` componente (*Shared/LoginDisplay. Razor*).</span><span class="sxs-lookup"><span data-stu-id="1fdc7-132">At a minimum, select the **Application claims** > **Display Name** user attribute to populate the `context.User.Identity.Name` in the `LoginDisplay` component (*Shared/LoginDisplay.razor*).</span></span>

<span data-ttu-id="1fdc7-133">Registrare il nome del flusso utente di iscrizione e accesso creato per l'app (ad esempio, `B2C_1_signupsignin` ).</span><span class="sxs-lookup"><span data-stu-id="1fdc7-133">Record the sign-up and sign-in user flow name created for the app (for example, `B2C_1_signupsignin`).</span></span>

<span data-ttu-id="1fdc7-134">Sostituire i segnaposto nel comando seguente con le informazioni registrate in precedenza ed eseguire il comando in una shell dei comandi:</span><span class="sxs-lookup"><span data-stu-id="1fdc7-134">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --client-id "{CLIENT ID}" --domain "{TENANT DOMAIN}" -ssp "{SIGN UP OR SIGN IN POLICY}"
```

<span data-ttu-id="1fdc7-135">Per specificare il percorso di output, che crea una cartella di progetto, se non esiste, includere l'opzione di output nel comando con un percorso (ad esempio, `-o BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="1fdc7-135">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="1fdc7-136">Il nome della cartella diventa anche parte del nome del progetto.</span><span class="sxs-lookup"><span data-stu-id="1fdc7-136">The folder name also becomes part of the project's name.</span></span>

<span data-ttu-id="1fdc7-137">Dopo aver creato l'app, dovrebbe essere possibile:</span><span class="sxs-lookup"><span data-stu-id="1fdc7-137">After creating the app, you should be able to:</span></span>

* <span data-ttu-id="1fdc7-138">Accedere all'app usando un account utente di AAD.</span><span class="sxs-lookup"><span data-stu-id="1fdc7-138">Log into the app using an AAD user account.</span></span>
* <span data-ttu-id="1fdc7-139">Richiedere i token di accesso per le API Microsoft.</span><span class="sxs-lookup"><span data-stu-id="1fdc7-139">Request access tokens for Microsoft APIs.</span></span> <span data-ttu-id="1fdc7-140">Per altre informazioni, vedere:</span><span class="sxs-lookup"><span data-stu-id="1fdc7-140">For more information, see:</span></span>
  * [<span data-ttu-id="1fdc7-141">Ambiti del token di accesso</span><span class="sxs-lookup"><span data-stu-id="1fdc7-141">Access token scopes</span></span>](#access-token-scopes)
  * <span data-ttu-id="1fdc7-142">[Guida introduttiva: configurare un'applicazione per esporre le API Web](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span><span class="sxs-lookup"><span data-stu-id="1fdc7-142">[Quickstart: Configure an application to expose web APIs](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span></span>

## <a name="authentication-package"></a><span data-ttu-id="1fdc7-143">Pacchetto di autenticazione</span><span class="sxs-lookup"><span data-stu-id="1fdc7-143">Authentication package</span></span>

<span data-ttu-id="1fdc7-144">Quando viene creata un'app per usare un singolo account B2C ( `IndividualB2C` ), l'app riceve automaticamente un riferimento al pacchetto per [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ( `Microsoft.Authentication.WebAssembly.Msal` ).</span><span class="sxs-lookup"><span data-stu-id="1fdc7-144">When an app is created to use an Individual B2C Account (`IndividualB2C`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="1fdc7-145">Il pacchetto fornisce un set di primitive che consentono all'app di autenticare gli utenti e ottenere i token per chiamare le API protette.</span><span class="sxs-lookup"><span data-stu-id="1fdc7-145">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="1fdc7-146">Se si aggiunge l'autenticazione a un'app, aggiungere manualmente il pacchetto al file di progetto dell'app:</span><span class="sxs-lookup"><span data-stu-id="1fdc7-146">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="3.2.0" />
```

<span data-ttu-id="1fdc7-147">Il `Microsoft.Authentication.WebAssembly.Msal` pacchetto aggiunge il `Microsoft.AspNetCore.Components.WebAssembly.Authentication` pacchetto all'app in modo transitivo.</span><span class="sxs-lookup"><span data-stu-id="1fdc7-147">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="1fdc7-148">Supporto del servizio di autenticazione</span><span class="sxs-lookup"><span data-stu-id="1fdc7-148">Authentication service support</span></span>

<span data-ttu-id="1fdc7-149">Il supporto per l'autenticazione degli utenti viene registrato nel contenitore del servizio con il `AddMsalAuthentication` metodo di estensione fornito dal `Microsoft.Authentication.WebAssembly.Msal` pacchetto.</span><span class="sxs-lookup"><span data-stu-id="1fdc7-149">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="1fdc7-150">Questo metodo configura tutti i servizi necessari per l'interazione dell'app con il Identity provider (IP).</span><span class="sxs-lookup"><span data-stu-id="1fdc7-150">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="1fdc7-151">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="1fdc7-151">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAdB2C", options.ProviderOptions.Authentication);
});
```

<span data-ttu-id="1fdc7-152">Il `AddMsalAuthentication` metodo accetta un callback per configurare i parametri necessari per autenticare un'app.</span><span class="sxs-lookup"><span data-stu-id="1fdc7-152">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="1fdc7-153">Quando si registra l'app, è possibile ottenere i valori necessari per la configurazione dell'app dalla configurazione di AAD.</span><span class="sxs-lookup"><span data-stu-id="1fdc7-153">The values required for configuring the app can be obtained from the AAD configuration when you register the app.</span></span>

<span data-ttu-id="1fdc7-154">La configurazione viene fornita dal file *wwwroot/appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="1fdc7-154">Configuration is supplied by the *wwwroot/appsettings.json* file:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "{AAD B2C INSTANCE}{DOMAIN}/{SIGN UP OR SIGN IN POLICY}",
    "ClientId": "{CLIENT ID}",
    "ValidateAuthority": false
  }
}
```

<span data-ttu-id="1fdc7-155">Esempio:</span><span class="sxs-lookup"><span data-stu-id="1fdc7-155">Example:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "https://contoso.b2clogin.com/contoso.onmicrosoft.com/B2C_1_signupsignin1",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "ValidateAuthority": false
  }
}
```

## <a name="access-token-scopes"></a><span data-ttu-id="1fdc7-156">Ambiti del token di accesso</span><span class="sxs-lookup"><span data-stu-id="1fdc7-156">Access token scopes</span></span>

<span data-ttu-id="1fdc7-157">Il Blazor modello webassembly non configura automaticamente l'app per richiedere un token di accesso per un'API protetta.</span><span class="sxs-lookup"><span data-stu-id="1fdc7-157">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="1fdc7-158">Per eseguire il provisioning di un token di accesso come parte del flusso di accesso, aggiungere l'ambito agli ambiti dei token di accesso predefiniti di `MsalProviderOptions` :</span><span class="sxs-lookup"><span data-stu-id="1fdc7-158">To provision an access token as part of the sign-in flow, add the scope to the default access token scopes of the `MsalProviderOptions`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

<span data-ttu-id="1fdc7-159">Per ulteriori informazioni, vedere le sezioni seguenti dell'articolo *scenari aggiuntivi* :</span><span class="sxs-lookup"><span data-stu-id="1fdc7-159">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="1fdc7-160">Richiedere token di accesso aggiuntivi</span><span class="sxs-lookup"><span data-stu-id="1fdc7-160">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="1fdc7-161">Connetti token alle richieste in uscita</span><span class="sxs-lookup"><span data-stu-id="1fdc7-161">Attach tokens to outgoing requests</span></span>](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a><span data-ttu-id="1fdc7-162">Importa file</span><span class="sxs-lookup"><span data-stu-id="1fdc7-162">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="1fdc7-163">Pagina di indice</span><span class="sxs-lookup"><span data-stu-id="1fdc7-163">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="1fdc7-164">Componente app</span><span class="sxs-lookup"><span data-stu-id="1fdc7-164">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="1fdc7-165">Componente RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="1fdc7-165">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="1fdc7-166">Componente LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="1fdc7-166">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="1fdc7-167">Componente di autenticazione</span><span class="sxs-lookup"><span data-stu-id="1fdc7-167">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/wasm-aad-b2c-userflows.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="1fdc7-168">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="1fdc7-168">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* [<span data-ttu-id="1fdc7-169">Richieste API Web non autenticate o non autorizzate in un'app con un client predefinito sicuro</span><span class="sxs-lookup"><span data-stu-id="1fdc7-169">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:security/blazor/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/authentication/azure-ad-b2c>
* [<span data-ttu-id="1fdc7-170">Esercitazione: Creare un tenant di Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="1fdc7-170">Tutorial: Create an Azure Active Directory B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)
* [<span data-ttu-id="1fdc7-171">Documentazione di Microsoft Identity Platform</span><span class="sxs-lookup"><span data-stu-id="1fdc7-171">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
