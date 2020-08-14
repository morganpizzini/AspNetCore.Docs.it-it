---
title: Proteggere un' Blazor WebAssembly app ASP.NET Core autonoma con account Microsoft
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/08/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/security/webassembly/standalone-with-microsoft-accounts
ms.openlocfilehash: 6c3b396ea5c5fcf1082a392d3fd8a0d2c5dcfd15
ms.sourcegitcommit: ec41ab354952b75557240923756a8c2ac79b49f8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/13/2020
ms.locfileid: "88202683"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-standalone-app-with-microsoft-accounts"></a><span data-ttu-id="e5209-102">Proteggere un' Blazor WebAssembly app ASP.NET Core autonoma con account Microsoft</span><span class="sxs-lookup"><span data-stu-id="e5209-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with Microsoft Accounts</span></span>

<span data-ttu-id="e5209-103">Di [Javier Calvarro Nelson](https://github.com/javiercn) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="e5209-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="e5209-104">Per creare un' [ Blazor WebAssembly app autonoma](xref:blazor/hosting-models#blazor-webassembly) che usa [account Microsoft con Azure Active Directory (AAD)](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal) per l'autenticazione:</span><span class="sxs-lookup"><span data-stu-id="e5209-104">To create a [standalone Blazor WebAssembly app](xref:blazor/hosting-models#blazor-webassembly) that uses [Microsoft Accounts with Azure Active Directory (AAD)](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal) for authentication:</span></span>

[<span data-ttu-id="e5209-105">Creare un'applicazione Web e un tenant di AAD</span><span class="sxs-lookup"><span data-stu-id="e5209-105">Create an AAD tenant and web application</span></span>](/azure/active-directory/develop/v2-overview)

<span data-ttu-id="e5209-106">Registrare un'app AAD nell'area **Azure Active Directory**  >  **registrazioni app** del portale di Azure:</span><span class="sxs-lookup"><span data-stu-id="e5209-106">Register a AAD app in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="e5209-107">Specificare un **nome** per l'app (ad esempio, \*\* Blazor account Microsoft AAD autonomi\*\*).</span><span class="sxs-lookup"><span data-stu-id="e5209-107">Provide a **Name** for the app (for example, **Blazor Standalone AAD Microsoft Accounts**).</span></span>
1. <span data-ttu-id="e5209-108">In **tipi di account supportati**selezionare **account in qualsiasi directory dell'organizzazione**.</span><span class="sxs-lookup"><span data-stu-id="e5209-108">In **Supported account types**, select **Accounts in any organizational directory**.</span></span>
1. <span data-ttu-id="e5209-109">Lasciare l'elenco a discesa **URI di reindirizzamento** impostato su **Web** e specificare l'URI di reindirizzamento seguente: `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="e5209-109">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="e5209-110">La porta predefinita per un'app in esecuzione su gheppio è 5001.</span><span class="sxs-lookup"><span data-stu-id="e5209-110">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="e5209-111">Se l'app viene eseguita su una porta di Gheppio diversa, usare la porta dell'app.</span><span class="sxs-lookup"><span data-stu-id="e5209-111">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="e5209-112">Per IIS Express, la porta generata in modo casuale per l'app si trova nelle proprietà dell'app nel pannello **debug** .</span><span class="sxs-lookup"><span data-stu-id="e5209-112">For IIS Express, the randomly generated port for the app can be found in the app's properties in the **Debug** panel.</span></span> <span data-ttu-id="e5209-113">Poiché l'app non esiste in questo momento e la porta IIS Express non è nota, tornare a questo passaggio dopo la creazione dell'app e aggiornare l'URI di reindirizzamento.</span><span class="sxs-lookup"><span data-stu-id="e5209-113">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="e5209-114">Un commento viene visualizzato più avanti in questo argomento per ricordare IIS Express agli utenti di aggiornare l'URI di reindirizzamento.</span><span class="sxs-lookup"><span data-stu-id="e5209-114">A remark appears later in this topic to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="e5209-115">Disabilitare la **Permissions**  >  casella di controllo autorizzazioni**Concedi amministratore per OpenID e autorizzazioni offline_access** .</span><span class="sxs-lookup"><span data-stu-id="e5209-115">Disable the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="e5209-116">Selezionare **Registra**.</span><span class="sxs-lookup"><span data-stu-id="e5209-116">Select **Register**.</span></span>

<span data-ttu-id="e5209-117">Registrare l'ID dell'applicazione (client) (ad esempio, `41451fa7-82d9-4673-8fa5-69eff5a761fd` ).</span><span class="sxs-lookup"><span data-stu-id="e5209-117">Record the Application (client) ID (for example, `41451fa7-82d9-4673-8fa5-69eff5a761fd`).</span></span>

<span data-ttu-id="e5209-118">In **Authentication**  >  **configurazioni piattaforma**di autenticazione  >  **Web**:</span><span class="sxs-lookup"><span data-stu-id="e5209-118">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="e5209-119">Verificare che l' **URI di reindirizzamento** di `https://localhost:{PORT}/authentication/login-callback` sia presente.</span><span class="sxs-lookup"><span data-stu-id="e5209-119">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="e5209-120">Per **concessione implicita**, selezionare le caselle di controllo per i token di **accesso** e i **token ID**.</span><span class="sxs-lookup"><span data-stu-id="e5209-120">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="e5209-121">Per questa esperienza sono accettabili le impostazioni predefinite rimanenti per l'app.</span><span class="sxs-lookup"><span data-stu-id="e5209-121">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="e5209-122">Fare clic sul pulsante **Salva**.</span><span class="sxs-lookup"><span data-stu-id="e5209-122">Select the **Save** button.</span></span>

<span data-ttu-id="e5209-123">Creare l'app.</span><span class="sxs-lookup"><span data-stu-id="e5209-123">Create the app.</span></span> <span data-ttu-id="e5209-124">Sostituire i segnaposto nel comando seguente con le informazioni registrate in precedenza ed eseguire il comando seguente in una shell dei comandi:</span><span class="sxs-lookup"><span data-stu-id="e5209-124">Replace the placeholders in the following command with the information recorded earlier and execute the following command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" --tenant-id "common" -o {APP NAME}
```

| <span data-ttu-id="e5209-125">Segnaposto</span><span class="sxs-lookup"><span data-stu-id="e5209-125">Placeholder</span></span>   | <span data-ttu-id="e5209-126">Nome portale di Azure</span><span class="sxs-lookup"><span data-stu-id="e5209-126">Azure portal name</span></span>       | <span data-ttu-id="e5209-127">Esempio</span><span class="sxs-lookup"><span data-stu-id="e5209-127">Example</span></span>                                |
| ------------- | ----------------------- | -------------------------------------- |
| `{APP NAME}`  | &mdash;                 | `BlazorSample`                         |
| `{CLIENT ID}` | <span data-ttu-id="e5209-128">ID applicazione (client)</span><span class="sxs-lookup"><span data-stu-id="e5209-128">Application (client) ID</span></span> | `41451fa7-82d9-4673-8fa5-69eff5a761fd` |

<span data-ttu-id="e5209-129">Il percorso di output specificato con l' `-o|--output` opzione Crea una cartella di progetto se non esiste e diventa parte del nome dell'app.</span><span class="sxs-lookup"><span data-stu-id="e5209-129">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

> [!NOTE]
> <span data-ttu-id="e5209-130">Nel portale di Azure, l'URI di reindirizzamento Web delle configurazioni della piattaforma di **autenticazione**dell'app  >  **Platform configurations**  >  **Web**  >  **Redirect URI** è configurato per la porta 5001 per le app eseguite nel server gheppio con le impostazioni predefinite.</span><span class="sxs-lookup"><span data-stu-id="e5209-130">In the Azure portal, the app's **Authentication** > **Platform configurations** > **Web** > **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="e5209-131">Se l'app viene eseguita su una porta IIS Express casuale, la porta per l'app si trova nelle proprietà dell'app nel pannello **debug** .</span><span class="sxs-lookup"><span data-stu-id="e5209-131">If the app is run on a random IIS Express port, the port for the app can be found in the app's properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="e5209-132">Se la porta non è stata configurata in precedenza con la porta nota dell'app, tornare alla registrazione dell'app nel portale di Azure e aggiornare l'URI di reindirizzamento con la porta corretta.</span><span class="sxs-lookup"><span data-stu-id="e5209-132">If the port wasn't configured earlier with the app's known port, return to the app's registration in the Azure portal and update the redirect URI with the correct port.</span></span>

<span data-ttu-id="e5209-133">Dopo aver creato l'app, dovrebbe essere possibile:</span><span class="sxs-lookup"><span data-stu-id="e5209-133">After creating the app, you should be able to:</span></span>

* <span data-ttu-id="e5209-134">Accedere all'app usando un account Microsoft.</span><span class="sxs-lookup"><span data-stu-id="e5209-134">Log into the app using a Microsoft account.</span></span>
* <span data-ttu-id="e5209-135">Richiedere i token di accesso per le API Microsoft.</span><span class="sxs-lookup"><span data-stu-id="e5209-135">Request access tokens for Microsoft APIs.</span></span> <span data-ttu-id="e5209-136">Per altre informazioni, vedere:</span><span class="sxs-lookup"><span data-stu-id="e5209-136">For more information, see:</span></span>
  * [<span data-ttu-id="e5209-137">Ambiti del token di accesso</span><span class="sxs-lookup"><span data-stu-id="e5209-137">Access token scopes</span></span>](#access-token-scopes)
  * <span data-ttu-id="e5209-138">[Guida introduttiva: configurare un'applicazione per esporre le API Web](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span><span class="sxs-lookup"><span data-stu-id="e5209-138">[Quickstart: Configure an application to expose web APIs](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span></span>

## <a name="authentication-package"></a><span data-ttu-id="e5209-139">Pacchetto di autenticazione</span><span class="sxs-lookup"><span data-stu-id="e5209-139">Authentication package</span></span>

<span data-ttu-id="e5209-140">Quando viene creata un'app per usare gli account aziendali o dell'Istituto di istruzione ( `SingleOrg` ), l'app riceve automaticamente un riferimento al pacchetto per [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ( [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) ).</span><span class="sxs-lookup"><span data-stu-id="e5209-140">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)).</span></span> <span data-ttu-id="e5209-141">Il pacchetto fornisce un set di primitive che consentono all'app di autenticare gli utenti e ottenere i token per chiamare le API protette.</span><span class="sxs-lookup"><span data-stu-id="e5209-141">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="e5209-142">Se si aggiunge l'autenticazione a un'app, aggiungere manualmente il pacchetto al file di progetto dell'app:</span><span class="sxs-lookup"><span data-stu-id="e5209-142">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="3.2.0" />
```

<span data-ttu-id="e5209-143">Il [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) pacchetto aggiunge il [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) pacchetto all'app in modo transitivo.</span><span class="sxs-lookup"><span data-stu-id="e5209-143">The [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) package transitively adds the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="e5209-144">Supporto del servizio di autenticazione</span><span class="sxs-lookup"><span data-stu-id="e5209-144">Authentication service support</span></span>

<span data-ttu-id="e5209-145">Il supporto per l'autenticazione degli utenti viene registrato nel contenitore del servizio con il <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> metodo di estensione fornito dal [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) pacchetto.</span><span class="sxs-lookup"><span data-stu-id="e5209-145">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> extension method provided by the [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) package.</span></span> <span data-ttu-id="e5209-146">Questo metodo configura tutti i servizi necessari per l'interazione dell'app con il Identity provider (IP).</span><span class="sxs-lookup"><span data-stu-id="e5209-146">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="e5209-147">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="e5209-147">`Program.cs`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
});
```

<span data-ttu-id="e5209-148">Il <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> metodo accetta un callback per configurare i parametri necessari per autenticare un'app.</span><span class="sxs-lookup"><span data-stu-id="e5209-148">The <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="e5209-149">Quando si registra l'app, è possibile ottenere i valori necessari per la configurazione dell'app dalla configurazione di AAD.</span><span class="sxs-lookup"><span data-stu-id="e5209-149">The values required for configuring the app can be obtained from the AAD configuration when you register the app.</span></span>

<span data-ttu-id="e5209-150">La configurazione viene fornita dal `wwwroot/appsettings.json` file:</span><span class="sxs-lookup"><span data-stu-id="e5209-150">Configuration is supplied by the `wwwroot/appsettings.json` file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/common",
    "ClientId": "{CLIENT ID}",
    "ValidateAuthority": true
  }
}
```

<span data-ttu-id="e5209-151">Esempio:</span><span class="sxs-lookup"><span data-stu-id="e5209-151">Example:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/common",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "ValidateAuthority": true
  }
}
```

## <a name="access-token-scopes"></a><span data-ttu-id="e5209-152">Ambiti del token di accesso</span><span class="sxs-lookup"><span data-stu-id="e5209-152">Access token scopes</span></span>

<span data-ttu-id="e5209-153">Il Blazor WebAssembly modello non configura automaticamente l'app per richiedere un token di accesso per un'API protetta.</span><span class="sxs-lookup"><span data-stu-id="e5209-153">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="e5209-154">Per eseguire il provisioning di un token di accesso come parte del flusso di accesso, aggiungere l'ambito agli ambiti dei token di accesso predefiniti di <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions> :</span><span class="sxs-lookup"><span data-stu-id="e5209-154">To provision an access token as part of the sign-in flow, add the scope to the default access token scopes of the <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions>:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

<span data-ttu-id="e5209-155">Per ulteriori informazioni, vedere le sezioni seguenti dell'articolo *scenari aggiuntivi* :</span><span class="sxs-lookup"><span data-stu-id="e5209-155">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="e5209-156">Richiedere token di accesso aggiuntivi</span><span class="sxs-lookup"><span data-stu-id="e5209-156">Request additional access tokens</span></span>](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="e5209-157">Connetti token alle richieste in uscita</span><span class="sxs-lookup"><span data-stu-id="e5209-157">Attach tokens to outgoing requests</span></span>](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

::: moniker range=">= aspnetcore-5.0"

## <a name="login-mode"></a><span data-ttu-id="e5209-158">Modalità di accesso</span><span class="sxs-lookup"><span data-stu-id="e5209-158">Login mode</span></span>

[!INCLUDE[](~/includes/blazor-security/msal-login-mode.md)]

::: moniker-end

## <a name="imports-file"></a><span data-ttu-id="e5209-159">Importa file</span><span class="sxs-lookup"><span data-stu-id="e5209-159">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="e5209-160">Pagina di indice</span><span class="sxs-lookup"><span data-stu-id="e5209-160">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="e5209-161">Componente app</span><span class="sxs-lookup"><span data-stu-id="e5209-161">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="e5209-162">Componente RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="e5209-162">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="e5209-163">Componente LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="e5209-163">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="e5209-164">Componente di autenticazione</span><span class="sxs-lookup"><span data-stu-id="e5209-164">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="e5209-165">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="e5209-165">Additional resources</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="e5209-166">Richieste API Web non autenticate o non autorizzate in un'app con un client predefinito sicuro</span><span class="sxs-lookup"><span data-stu-id="e5209-166">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:blazor/security/webassembly/aad-groups-roles>
* [<span data-ttu-id="e5209-167">Avvio rapido: Registrare un'applicazione con Microsoft Identity Platform</span><span class="sxs-lookup"><span data-stu-id="e5209-167">Quickstart: Register an application with the Microsoft identity platform</span></span>](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal)
* [<span data-ttu-id="e5209-168">Avvio rapido: Configurare un'applicazione per l'esposizione di API Web</span><span class="sxs-lookup"><span data-stu-id="e5209-168">Quickstart: Configure an application to expose web APIs</span></span>](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)
