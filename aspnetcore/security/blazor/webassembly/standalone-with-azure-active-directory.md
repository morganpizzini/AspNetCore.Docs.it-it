---
title: Proteggere un' Blazor app ASP.NET Core webassembly autonoma con Azure Active Directory
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/blazor/webassembly/standalone-with-azure-active-directory
ms.openlocfilehash: b85129d8b56f2106636b47534630f8139e100ae9
ms.sourcegitcommit: 6a71b560d897e13ad5b61d07afe4fcb57f8ef6dc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/09/2020
ms.locfileid: "83851211"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-standalone-app-with-azure-active-directory"></a><span data-ttu-id="9b9e5-102">Proteggere un' Blazor app ASP.NET Core webassembly autonoma con Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="9b9e5-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory</span></span>

<span data-ttu-id="9b9e5-103">Di [Javier Calvarro Nelson](https://github.com/javiercn) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="9b9e5-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="9b9e5-104">Per creare un' Blazor app webassembly autonoma che usa [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) per l'autenticazione:</span><span class="sxs-lookup"><span data-stu-id="9b9e5-104">To create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) for authentication:</span></span>

<span data-ttu-id="9b9e5-105">[Creare un'applicazione Web e un tenant di AAD](/azure/active-directory/develop/v2-overview):</span><span class="sxs-lookup"><span data-stu-id="9b9e5-105">[Create an AAD tenant and web application](/azure/active-directory/develop/v2-overview):</span></span>

<span data-ttu-id="9b9e5-106">Registrare un'app AAD nell'area **Azure Active Directory**  >  **registrazioni app** del portale di Azure:</span><span class="sxs-lookup"><span data-stu-id="9b9e5-106">Register a AAD app in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="9b9e5-107">Specificare un **nome** per l'app, ad esempio \*\* Blazor AAD autonomo\*\*.</span><span class="sxs-lookup"><span data-stu-id="9b9e5-107">Provide a **Name** for the app (for example, **Blazor Standalone AAD**).</span></span>
1. <span data-ttu-id="9b9e5-108">Scegliere i **tipi di conto supportati**.</span><span class="sxs-lookup"><span data-stu-id="9b9e5-108">Choose a **Supported account types**.</span></span> <span data-ttu-id="9b9e5-109">È possibile selezionare gli **account in questa directory organizzativa solo** per questa esperienza.</span><span class="sxs-lookup"><span data-stu-id="9b9e5-109">You may select **Accounts in this organizational directory only** for this experience.</span></span>
1. <span data-ttu-id="9b9e5-110">Lasciare l'elenco a discesa **URI di reindirizzamento** impostato su **Web** e specificare l'URI di reindirizzamento seguente: `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="9b9e5-110">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="9b9e5-111">La porta predefinita per un'app in esecuzione su gheppio è 5001.</span><span class="sxs-lookup"><span data-stu-id="9b9e5-111">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="9b9e5-112">Se l'app viene eseguita su una porta di Gheppio diversa, usare la porta dell'app.</span><span class="sxs-lookup"><span data-stu-id="9b9e5-112">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="9b9e5-113">Per IIS Express, la porta generata in modo casuale per l'app si trova nelle proprietà dell'app nel pannello **debug** .</span><span class="sxs-lookup"><span data-stu-id="9b9e5-113">For IIS Express, the randomly generated port for the app can be found in the app's properties in the **Debug** panel.</span></span> <span data-ttu-id="9b9e5-114">Poiché l'app non esiste in questo momento e la porta IIS Express non è nota, tornare a questo passaggio dopo la creazione dell'app e aggiornare l'URI di reindirizzamento.</span><span class="sxs-lookup"><span data-stu-id="9b9e5-114">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="9b9e5-115">Un commento viene visualizzato più avanti in questo argomento per ricordare IIS Express agli utenti di aggiornare l'URI di reindirizzamento.</span><span class="sxs-lookup"><span data-stu-id="9b9e5-115">A remark appears later in this topic to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="9b9e5-116">Disabilitare la **Permissions**  >  casella di controllo autorizzazioni**concessi da amministratore a OpenID e autorizzazioni offline_access** .</span><span class="sxs-lookup"><span data-stu-id="9b9e5-116">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="9b9e5-117">Selezionare **Registra**.</span><span class="sxs-lookup"><span data-stu-id="9b9e5-117">Select **Register**.</span></span>

<span data-ttu-id="9b9e5-118">Registrare le seguenti informazioni:</span><span class="sxs-lookup"><span data-stu-id="9b9e5-118">Record the following information:</span></span>

* <span data-ttu-id="9b9e5-119">ID applicazione (ID client) (ad esempio, `11111111-1111-1111-1111-111111111111` )</span><span class="sxs-lookup"><span data-stu-id="9b9e5-119">Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`)</span></span>
* <span data-ttu-id="9b9e5-120">ID directory (ID tenant) (ad esempio, `22222222-2222-2222-2222-222222222222` )</span><span class="sxs-lookup"><span data-stu-id="9b9e5-120">Directory ID (Tenant ID) (for example, `22222222-2222-2222-2222-222222222222`)</span></span>

<span data-ttu-id="9b9e5-121">In **Authentication**  >  **configurazioni piattaforma**di autenticazione  >  **Web**:</span><span class="sxs-lookup"><span data-stu-id="9b9e5-121">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="9b9e5-122">Verificare che l' **URI di reindirizzamento** di `https://localhost:{PORT}/authentication/login-callback` sia presente.</span><span class="sxs-lookup"><span data-stu-id="9b9e5-122">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="9b9e5-123">Per **concessione implicita**, selezionare le caselle di controllo per i token di **accesso** e i **token ID**.</span><span class="sxs-lookup"><span data-stu-id="9b9e5-123">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="9b9e5-124">Per questa esperienza sono accettabili le impostazioni predefinite rimanenti per l'app.</span><span class="sxs-lookup"><span data-stu-id="9b9e5-124">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="9b9e5-125">Fare clic sul pulsante **Salva**.</span><span class="sxs-lookup"><span data-stu-id="9b9e5-125">Select the **Save** button.</span></span>

<span data-ttu-id="9b9e5-126">Creare l'app.</span><span class="sxs-lookup"><span data-stu-id="9b9e5-126">Create the app.</span></span> <span data-ttu-id="9b9e5-127">Sostituire i segnaposto nel comando seguente con le informazioni registrate in precedenza ed eseguire il comando in una shell dei comandi:</span><span class="sxs-lookup"><span data-stu-id="9b9e5-127">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" --tenant-id "{TENANT ID}"
```

<span data-ttu-id="9b9e5-128">Per specificare il percorso di output, che crea una cartella di progetto, se non esiste, includere l'opzione di output nel comando con un percorso (ad esempio, `-o BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="9b9e5-128">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="9b9e5-129">Il nome della cartella diventa anche parte del nome del progetto.</span><span class="sxs-lookup"><span data-stu-id="9b9e5-129">The folder name also becomes part of the project's name.</span></span>

> [!NOTE]
> <span data-ttu-id="9b9e5-130">Nel portale di Azure, l'URI di reindirizzamento Web delle configurazioni della piattaforma di **autenticazione**dell'app  >  **Platform configurations**  >  **Web**  >  **Redirect URI** è configurato per la porta 5001 per le app eseguite nel server gheppio con le impostazioni predefinite.</span><span class="sxs-lookup"><span data-stu-id="9b9e5-130">In the Azure portal, the app's **Authentication** > **Platform configurations** > **Web** > **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="9b9e5-131">Se l'app viene eseguita su una porta IIS Express casuale, la porta per l'app si trova nelle proprietà dell'app nel pannello **debug** .</span><span class="sxs-lookup"><span data-stu-id="9b9e5-131">If the app is run on a random IIS Express port, the port for the app can be found in the app's properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="9b9e5-132">Se la porta non è stata configurata in precedenza con la porta nota dell'app, tornare alla registrazione dell'app nel portale di Azure e aggiornare l'URI di reindirizzamento con la porta corretta.</span><span class="sxs-lookup"><span data-stu-id="9b9e5-132">If the port wasn't configured earlier with the app's known port, return to the app's registration in the Azure portal and update the redirect URI with the correct port.</span></span>

<span data-ttu-id="9b9e5-133">Dopo aver creato l'app, dovrebbe essere possibile:</span><span class="sxs-lookup"><span data-stu-id="9b9e5-133">After creating the app, you should be able to:</span></span>

* <span data-ttu-id="9b9e5-134">Accedere all'app usando un account utente di AAD.</span><span class="sxs-lookup"><span data-stu-id="9b9e5-134">Log into the app using an AAD user account.</span></span>
* <span data-ttu-id="9b9e5-135">Richiedere i token di accesso per le API Microsoft.</span><span class="sxs-lookup"><span data-stu-id="9b9e5-135">Request access tokens for Microsoft APIs.</span></span> <span data-ttu-id="9b9e5-136">Per altre informazioni, vedere:</span><span class="sxs-lookup"><span data-stu-id="9b9e5-136">For more information, see:</span></span>
  * [<span data-ttu-id="9b9e5-137">Ambiti del token di accesso</span><span class="sxs-lookup"><span data-stu-id="9b9e5-137">Access token scopes</span></span>](#access-token-scopes)
  * <span data-ttu-id="9b9e5-138">[Guida introduttiva: configurare un'applicazione per esporre le API Web](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span><span class="sxs-lookup"><span data-stu-id="9b9e5-138">[Quickstart: Configure an application to expose web APIs](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span></span>

## <a name="authentication-package"></a><span data-ttu-id="9b9e5-139">Pacchetto di autenticazione</span><span class="sxs-lookup"><span data-stu-id="9b9e5-139">Authentication package</span></span>

<span data-ttu-id="9b9e5-140">Quando viene creata un'app per usare gli account aziendali o dell'Istituto di istruzione ( `SingleOrg` ), l'app riceve automaticamente un riferimento al pacchetto per [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([Microsoft. Authentication. webassembly. MSAL](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)).</span><span class="sxs-lookup"><span data-stu-id="9b9e5-140">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([Microsoft.Authentication.WebAssembly.Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)).</span></span> <span data-ttu-id="9b9e5-141">Il pacchetto fornisce un set di primitive che consentono all'app di autenticare gli utenti e ottenere i token per chiamare le API protette.</span><span class="sxs-lookup"><span data-stu-id="9b9e5-141">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="9b9e5-142">Se si aggiunge l'autenticazione a un'app, aggiungere manualmente il pacchetto al file di progetto dell'app:</span><span class="sxs-lookup"><span data-stu-id="9b9e5-142">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="3.2.0" />
```

<span data-ttu-id="9b9e5-143">Il pacchetto [Microsoft. Authentication. webassembly. MSAL](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) aggiunge in modo transitivo il pacchetto [Microsoft. AspNetCore. Components. webassembly. Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) all'app.</span><span class="sxs-lookup"><span data-stu-id="9b9e5-143">The [Microsoft.Authentication.WebAssembly.Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) package transitively adds the [Microsoft.AspNetCore.Components.WebAssembly.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="9b9e5-144">Supporto del servizio di autenticazione</span><span class="sxs-lookup"><span data-stu-id="9b9e5-144">Authentication service support</span></span>

<span data-ttu-id="9b9e5-145">Il supporto per l'autenticazione degli utenti viene registrato nel contenitore del servizio con il <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> metodo di estensione fornito dal pacchetto [Microsoft. Authentication. webassembly. MSAL](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) .</span><span class="sxs-lookup"><span data-stu-id="9b9e5-145">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> extension method provided by the [Microsoft.Authentication.WebAssembly.Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) package.</span></span> <span data-ttu-id="9b9e5-146">Questo metodo configura i servizi necessari per l'interazione dell'app con il Identity provider (IP).</span><span class="sxs-lookup"><span data-stu-id="9b9e5-146">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="9b9e5-147">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="9b9e5-147">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
});
```

<span data-ttu-id="9b9e5-148">Il <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> metodo accetta un callback per configurare i parametri necessari per autenticare un'app.</span><span class="sxs-lookup"><span data-stu-id="9b9e5-148">The <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="9b9e5-149">Quando si registra l'app, è possibile ottenere i valori necessari per la configurazione dell'app dalla configurazione di AAD.</span><span class="sxs-lookup"><span data-stu-id="9b9e5-149">The values required for configuring the app can be obtained from the AAD configuration when you register the app.</span></span>

<span data-ttu-id="9b9e5-150">La configurazione viene fornita dal file *wwwroot/appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="9b9e5-150">Configuration is supplied by the *wwwroot/appsettings.json* file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/{TENANT ID}",
    "ClientId": "{CLIENT ID}",
    "ValidateAuthority": true
  }
}
```

<span data-ttu-id="9b9e5-151">Esempio:</span><span class="sxs-lookup"><span data-stu-id="9b9e5-151">Example:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/e86c78e2-...-918e0565a45e",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "ValidateAuthority": true
  }
}
```

## <a name="access-token-scopes"></a><span data-ttu-id="9b9e5-152">Ambiti del token di accesso</span><span class="sxs-lookup"><span data-stu-id="9b9e5-152">Access token scopes</span></span>

<span data-ttu-id="9b9e5-153">Il Blazor modello webassembly non configura automaticamente l'app per richiedere un token di accesso per un'API protetta.</span><span class="sxs-lookup"><span data-stu-id="9b9e5-153">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="9b9e5-154">Per eseguire il provisioning di un token di accesso come parte del flusso di accesso, aggiungere l'ambito agli ambiti dei token di accesso predefiniti di <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions> :</span><span class="sxs-lookup"><span data-stu-id="9b9e5-154">To provision an access token as part of the sign-in flow, add the scope to the default access token scopes of the <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions>:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

<span data-ttu-id="9b9e5-155">Per ulteriori informazioni, vedere le sezioni seguenti dell'articolo *scenari aggiuntivi* :</span><span class="sxs-lookup"><span data-stu-id="9b9e5-155">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="9b9e5-156">Richiedere token di accesso aggiuntivi</span><span class="sxs-lookup"><span data-stu-id="9b9e5-156">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="9b9e5-157">Connetti token alle richieste in uscita</span><span class="sxs-lookup"><span data-stu-id="9b9e5-157">Attach tokens to outgoing requests</span></span>](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a><span data-ttu-id="9b9e5-158">Importa file</span><span class="sxs-lookup"><span data-stu-id="9b9e5-158">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="9b9e5-159">Pagina di indice</span><span class="sxs-lookup"><span data-stu-id="9b9e5-159">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="9b9e5-160">Componente app</span><span class="sxs-lookup"><span data-stu-id="9b9e5-160">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="9b9e5-161">Componente RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="9b9e5-161">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="9b9e5-162">Componente LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="9b9e5-162">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="9b9e5-163">Componente di autenticazione</span><span class="sxs-lookup"><span data-stu-id="9b9e5-163">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="9b9e5-164">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="9b9e5-164">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* [<span data-ttu-id="9b9e5-165">Richieste API Web non autenticate o non autorizzate in un'app con un client predefinito sicuro</span><span class="sxs-lookup"><span data-stu-id="9b9e5-165">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:security/blazor/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/blazor/webassembly/aad-groups-roles>
* <xref:security/authentication/azure-active-directory/index>
* [<span data-ttu-id="9b9e5-166">Documentazione di Microsoft Identity Platform</span><span class="sxs-lookup"><span data-stu-id="9b9e5-166">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
