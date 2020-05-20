---
<span data-ttu-id="873f8-101">title: "proteggere un' Blazor app ASP.NET Core webassembly autonoma con Azure Active Directory" autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="873f8-101">title: 'Secure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="873f8-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="873f8-102">'Blazor'</span></span>
- <span data-ttu-id="873f8-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="873f8-103">'Identity'</span></span>
- <span data-ttu-id="873f8-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="873f8-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="873f8-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="873f8-105">'Razor'</span></span>
- <span data-ttu-id="873f8-106">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="873f8-106">'SignalR' uid:</span></span> 

---
# <a name="secure-an-aspnet-core-blazor-webassembly-standalone-app-with-azure-active-directory"></a><span data-ttu-id="873f8-107">Proteggere un' Blazor app ASP.NET Core webassembly autonoma con Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="873f8-107">Secure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory</span></span>

<span data-ttu-id="873f8-108">Di [Javier Calvarro Nelson](https://github.com/javiercn) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="873f8-108">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="873f8-109">Per creare un' Blazor app webassembly autonoma che usa [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) per l'autenticazione:</span><span class="sxs-lookup"><span data-stu-id="873f8-109">To create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) for authentication:</span></span>

<span data-ttu-id="873f8-110">[Creare un'applicazione Web e un tenant di AAD](/azure/active-directory/develop/v2-overview):</span><span class="sxs-lookup"><span data-stu-id="873f8-110">[Create an AAD tenant and web application](/azure/active-directory/develop/v2-overview):</span></span>

<span data-ttu-id="873f8-111">Registrare un'app AAD nell'area **Azure Active Directory**  >  **registrazioni app** del portale di Azure:</span><span class="sxs-lookup"><span data-stu-id="873f8-111">Register a AAD app in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="873f8-112">Specificare un **nome** per l'app, ad esempio \*\* Blazor AAD autonomo\*\*.</span><span class="sxs-lookup"><span data-stu-id="873f8-112">Provide a **Name** for the app (for example, **Blazor Standalone AAD**).</span></span>
1. <span data-ttu-id="873f8-113">Scegliere i **tipi di conto supportati**.</span><span class="sxs-lookup"><span data-stu-id="873f8-113">Choose a **Supported account types**.</span></span> <span data-ttu-id="873f8-114">È possibile selezionare gli **account in questa directory organizzativa solo** per questa esperienza.</span><span class="sxs-lookup"><span data-stu-id="873f8-114">You may select **Accounts in this organizational directory only** for this experience.</span></span>
1. <span data-ttu-id="873f8-115">Lasciare l'elenco a discesa **URI di reindirizzamento** impostato su **Web**e specificare l'URI di reindirizzamento seguente: `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="873f8-115">Leave the **Redirect URI** drop down set to **Web**, and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="873f8-116">La porta predefinita per un'app in esecuzione su gheppio è 5001.</span><span class="sxs-lookup"><span data-stu-id="873f8-116">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="873f8-117">Per IIS Express, la porta generata in modo casuale si trova nelle proprietà dell'app nel pannello **debug** .</span><span class="sxs-lookup"><span data-stu-id="873f8-117">For IIS Express, the randomly generated port can be found in the app's properties in the **Debug** panel.</span></span>
1. <span data-ttu-id="873f8-118">Disabilitare la **Permissions**  >  casella di controllo autorizzazioni**concessi da amministratore a OpenID e autorizzazioni offline_access** .</span><span class="sxs-lookup"><span data-stu-id="873f8-118">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="873f8-119">Selezionare **Registra**.</span><span class="sxs-lookup"><span data-stu-id="873f8-119">Select **Register**.</span></span>

<span data-ttu-id="873f8-120">Registrare le seguenti informazioni:</span><span class="sxs-lookup"><span data-stu-id="873f8-120">Record the following information:</span></span>

* <span data-ttu-id="873f8-121">ID applicazione (ID client) (ad esempio, `11111111-1111-1111-1111-111111111111` )</span><span class="sxs-lookup"><span data-stu-id="873f8-121">Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`)</span></span>
* <span data-ttu-id="873f8-122">ID directory (ID tenant) (ad esempio, `22222222-2222-2222-2222-222222222222` )</span><span class="sxs-lookup"><span data-stu-id="873f8-122">Directory ID (Tenant ID) (for example, `22222222-2222-2222-2222-222222222222`)</span></span>

<span data-ttu-id="873f8-123">In **Authentication**  >  **configurazioni piattaforma**di autenticazione  >  **Web**:</span><span class="sxs-lookup"><span data-stu-id="873f8-123">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="873f8-124">Verificare che l' **URI di reindirizzamento** di `https://localhost:{PORT}/authentication/login-callback` sia presente.</span><span class="sxs-lookup"><span data-stu-id="873f8-124">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="873f8-125">Per **concessione implicita**, selezionare le caselle di controllo per i token di **accesso** e i **token ID**.</span><span class="sxs-lookup"><span data-stu-id="873f8-125">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="873f8-126">Per questa esperienza sono accettabili le impostazioni predefinite rimanenti per l'app.</span><span class="sxs-lookup"><span data-stu-id="873f8-126">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="873f8-127">Fare clic sul pulsante **Salva**.</span><span class="sxs-lookup"><span data-stu-id="873f8-127">Select the **Save** button.</span></span>

<span data-ttu-id="873f8-128">Creare l'app.</span><span class="sxs-lookup"><span data-stu-id="873f8-128">Create the app.</span></span> <span data-ttu-id="873f8-129">Sostituire i segnaposto nel comando seguente con le informazioni registrate in precedenza ed eseguire il comando in una shell dei comandi:</span><span class="sxs-lookup"><span data-stu-id="873f8-129">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" --tenant-id "{TENANT ID}"
```

<span data-ttu-id="873f8-130">Per specificare il percorso di output, che crea una cartella di progetto, se non esiste, includere l'opzione di output nel comando con un percorso (ad esempio, `-o BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="873f8-130">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="873f8-131">Il nome della cartella diventa anche parte del nome del progetto.</span><span class="sxs-lookup"><span data-stu-id="873f8-131">The folder name also becomes part of the project's name.</span></span>

<span data-ttu-id="873f8-132">Dopo aver creato l'app, dovrebbe essere possibile:</span><span class="sxs-lookup"><span data-stu-id="873f8-132">After creating the app, you should be able to:</span></span>

* <span data-ttu-id="873f8-133">Accedere all'app usando un account utente di AAD.</span><span class="sxs-lookup"><span data-stu-id="873f8-133">Log into the app using an AAD user account.</span></span>
* <span data-ttu-id="873f8-134">Richiedere i token di accesso per le API Microsoft.</span><span class="sxs-lookup"><span data-stu-id="873f8-134">Request access tokens for Microsoft APIs.</span></span> <span data-ttu-id="873f8-135">Per altre informazioni, vedere:</span><span class="sxs-lookup"><span data-stu-id="873f8-135">For more information, see:</span></span>
  * [<span data-ttu-id="873f8-136">Ambiti del token di accesso</span><span class="sxs-lookup"><span data-stu-id="873f8-136">Access token scopes</span></span>](#access-token-scopes)
  * <span data-ttu-id="873f8-137">[Guida introduttiva: configurare un'applicazione per esporre le API Web](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span><span class="sxs-lookup"><span data-stu-id="873f8-137">[Quickstart: Configure an application to expose web APIs](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span></span>

## <a name="authentication-package"></a><span data-ttu-id="873f8-138">Pacchetto di autenticazione</span><span class="sxs-lookup"><span data-stu-id="873f8-138">Authentication package</span></span>

<span data-ttu-id="873f8-139">Quando viene creata un'app per usare gli account aziendali o dell'Istituto di istruzione ( `SingleOrg` ), l'app riceve automaticamente un riferimento al pacchetto per [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ( `Microsoft.Authentication.WebAssembly.Msal` ).</span><span class="sxs-lookup"><span data-stu-id="873f8-139">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="873f8-140">Il pacchetto fornisce un set di primitive che consentono all'app di autenticare gli utenti e ottenere i token per chiamare le API protette.</span><span class="sxs-lookup"><span data-stu-id="873f8-140">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="873f8-141">Se si aggiunge l'autenticazione a un'app, aggiungere manualmente il pacchetto al file di progetto dell'app:</span><span class="sxs-lookup"><span data-stu-id="873f8-141">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="3.2.0" />
```

<span data-ttu-id="873f8-142">Il `Microsoft.Authentication.WebAssembly.Msal` pacchetto aggiunge il `Microsoft.AspNetCore.Components.WebAssembly.Authentication` pacchetto all'app in modo transitivo.</span><span class="sxs-lookup"><span data-stu-id="873f8-142">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="873f8-143">Supporto del servizio di autenticazione</span><span class="sxs-lookup"><span data-stu-id="873f8-143">Authentication service support</span></span>

<span data-ttu-id="873f8-144">Il supporto per l'autenticazione degli utenti viene registrato nel contenitore del servizio con il `AddMsalAuthentication` metodo di estensione fornito dal `Microsoft.Authentication.WebAssembly.Msal` pacchetto.</span><span class="sxs-lookup"><span data-stu-id="873f8-144">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="873f8-145">Questo metodo configura i servizi necessari per l'interazione dell'app con il Identity provider (IP).</span><span class="sxs-lookup"><span data-stu-id="873f8-145">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="873f8-146">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="873f8-146">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
});
```

<span data-ttu-id="873f8-147">Il `AddMsalAuthentication` metodo accetta un callback per configurare i parametri necessari per autenticare un'app.</span><span class="sxs-lookup"><span data-stu-id="873f8-147">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="873f8-148">Quando si registra l'app, è possibile ottenere i valori necessari per la configurazione dell'app dalla configurazione di AAD.</span><span class="sxs-lookup"><span data-stu-id="873f8-148">The values required for configuring the app can be obtained from the AAD configuration when you register the app.</span></span>

<span data-ttu-id="873f8-149">La configurazione viene fornita dal file *wwwroot/appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="873f8-149">Configuration is supplied by the *wwwroot/appsettings.json* file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/{TENANT ID}",
    "ClientId": "{CLIENT ID}",
    "ValidateAuthority": true
  }
}
```

<span data-ttu-id="873f8-150">Esempio:</span><span class="sxs-lookup"><span data-stu-id="873f8-150">Example:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/e86c78e2-...-918e0565a45e",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "ValidateAuthority": true
  }
}
```

## <a name="access-token-scopes"></a><span data-ttu-id="873f8-151">Ambiti del token di accesso</span><span class="sxs-lookup"><span data-stu-id="873f8-151">Access token scopes</span></span>

<span data-ttu-id="873f8-152">Il Blazor modello webassembly non configura automaticamente l'app per richiedere un token di accesso per un'API protetta.</span><span class="sxs-lookup"><span data-stu-id="873f8-152">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="873f8-153">Per eseguire il provisioning di un token di accesso come parte del flusso di accesso, aggiungere l'ambito agli ambiti dei token di accesso predefiniti di `MsalProviderOptions` :</span><span class="sxs-lookup"><span data-stu-id="873f8-153">To provision an access token as part of the sign-in flow, add the scope to the default access token scopes of the `MsalProviderOptions`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

<span data-ttu-id="873f8-154">Per ulteriori informazioni, vedere le sezioni seguenti dell'articolo *scenari aggiuntivi* :</span><span class="sxs-lookup"><span data-stu-id="873f8-154">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="873f8-155">Richiedere token di accesso aggiuntivi</span><span class="sxs-lookup"><span data-stu-id="873f8-155">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="873f8-156">Connetti token alle richieste in uscita</span><span class="sxs-lookup"><span data-stu-id="873f8-156">Attach tokens to outgoing requests</span></span>](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a><span data-ttu-id="873f8-157">Importa file</span><span class="sxs-lookup"><span data-stu-id="873f8-157">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="873f8-158">Pagina di indice</span><span class="sxs-lookup"><span data-stu-id="873f8-158">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="873f8-159">Componente app</span><span class="sxs-lookup"><span data-stu-id="873f8-159">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="873f8-160">Componente RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="873f8-160">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="873f8-161">Componente LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="873f8-161">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="873f8-162">Componente di autenticazione</span><span class="sxs-lookup"><span data-stu-id="873f8-162">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="873f8-163">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="873f8-163">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* [<span data-ttu-id="873f8-164">Richieste API Web non autenticate o non autorizzate in un'app con un client predefinito sicuro</span><span class="sxs-lookup"><span data-stu-id="873f8-164">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:security/blazor/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/blazor/webassembly/aad-groups-roles>
* <xref:security/authentication/azure-active-directory/index>
* [<span data-ttu-id="873f8-165">Documentazione di Microsoft Identity Platform</span><span class="sxs-lookup"><span data-stu-id="873f8-165">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
