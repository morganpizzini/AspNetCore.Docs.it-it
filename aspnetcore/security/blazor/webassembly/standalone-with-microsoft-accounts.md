---
title: Proteggere un'app Blazor ASP.NET Core webassembly autonoma con account Microsoft
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/23/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-microsoft-accounts
ms.openlocfilehash: a12cc8f94a97882e4a0ac3a6553628df4da2e82c
ms.sourcegitcommit: 7bb14d005155a5044c7902a08694ee8ccb20c113
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82111188"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-microsoft-accounts"></a><span data-ttu-id="cbd28-102">Proteggere un'app Blazor ASP.NET Core webassembly autonoma con account Microsoft</span><span class="sxs-lookup"><span data-stu-id="cbd28-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with Microsoft Accounts</span></span>

<span data-ttu-id="cbd28-103">Di [Javier Calvarro Nelson](https://github.com/javiercn) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="cbd28-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

> [!NOTE]
> <span data-ttu-id="cbd28-104">Le indicazioni fornite in questo articolo si applicano a ASP.NET Core 3,2 Preview 4.</span><span class="sxs-lookup"><span data-stu-id="cbd28-104">The guidance in this article applies to ASP.NET Core 3.2 Preview 4.</span></span> <span data-ttu-id="cbd28-105">Questo argomento verrà aggiornato per coprire l'anteprima 5 il venerdì 24 aprile.</span><span class="sxs-lookup"><span data-stu-id="cbd28-105">This topic will be updated to cover Preview 5 on Friday, April 24.</span></span>

<span data-ttu-id="cbd28-106">Per creare un' Blazor app webassembly autonoma che usa [account Microsoft con Azure Active Directory (AAD)](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal) per l'autenticazione:</span><span class="sxs-lookup"><span data-stu-id="cbd28-106">To create a Blazor WebAssembly standalone app that uses [Microsoft Accounts with Azure Active Directory (AAD)](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal) for authentication:</span></span>

1. [<span data-ttu-id="cbd28-107">Creare un'applicazione Web e un tenant di AAD</span><span class="sxs-lookup"><span data-stu-id="cbd28-107">Create an AAD tenant and web application</span></span>](/azure/active-directory/develop/v2-overview)

   <span data-ttu-id="cbd28-108">Registrare un'app AAD nell'area **Azure Active Directory** > **registrazioni app** del portale di Azure:</span><span class="sxs-lookup"><span data-stu-id="cbd28-108">Register a AAD app in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

   <span data-ttu-id="cbd28-109">1 \.</span><span class="sxs-lookup"><span data-stu-id="cbd28-109">1\.</span></span> <span data-ttu-id="cbd28-110">Specificare un **nome** per l'app (ad esempio, \*\* Blazor client AAD\*\*).</span><span class="sxs-lookup"><span data-stu-id="cbd28-110">Provide a **Name** for the app (for example, **Blazor Client AAD**).</span></span><br>
   <span data-ttu-id="cbd28-111">2 \.</span><span class="sxs-lookup"><span data-stu-id="cbd28-111">2\.</span></span> <span data-ttu-id="cbd28-112">In **tipi di account supportati**selezionare **account in qualsiasi directory dell'organizzazione**.</span><span class="sxs-lookup"><span data-stu-id="cbd28-112">In **Supported account types**, select **Accounts in any organizational directory**.</span></span><br>
   <span data-ttu-id="cbd28-113">3 \.</span><span class="sxs-lookup"><span data-stu-id="cbd28-113">3\.</span></span> <span data-ttu-id="cbd28-114">Lasciare l'elenco a discesa **URI di reindirizzamento** impostato su **Web**e specificare un URI di `https://localhost:5001/authentication/login-callback`reindirizzamento.</span><span class="sxs-lookup"><span data-stu-id="cbd28-114">Leave the **Redirect URI** drop down set to **Web**, and provide a redirect URI of `https://localhost:5001/authentication/login-callback`.</span></span><br>
   <span data-ttu-id="cbd28-115">4 \.</span><span class="sxs-lookup"><span data-stu-id="cbd28-115">4\.</span></span> <span data-ttu-id="cbd28-116">Disabilitare la casella di controllo **autorizzazioni** > **concessi da amministratore a OpenID e autorizzazioni offline_access** .</span><span class="sxs-lookup"><span data-stu-id="cbd28-116">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span><br>
   <span data-ttu-id="cbd28-117">5 \.</span><span class="sxs-lookup"><span data-stu-id="cbd28-117">5\.</span></span> <span data-ttu-id="cbd28-118">Selezionare **Registra**.</span><span class="sxs-lookup"><span data-stu-id="cbd28-118">Select **Register**.</span></span>

   <span data-ttu-id="cbd28-119">In **Authentication** >  > **configurazioni piattaforma**di autenticazione**Web**:</span><span class="sxs-lookup"><span data-stu-id="cbd28-119">In **Authentication** > **Platform configurations** > **Web**:</span></span>

   <span data-ttu-id="cbd28-120">1 \.</span><span class="sxs-lookup"><span data-stu-id="cbd28-120">1\.</span></span> <span data-ttu-id="cbd28-121">Verificare che l' **URI** di `https://localhost:5001/authentication/login-callback` Reindirizzamento di sia presente.</span><span class="sxs-lookup"><span data-stu-id="cbd28-121">Confirm the **Redirect URI** of `https://localhost:5001/authentication/login-callback` is present.</span></span><br>
   <span data-ttu-id="cbd28-122">2 \.</span><span class="sxs-lookup"><span data-stu-id="cbd28-122">2\.</span></span> <span data-ttu-id="cbd28-123">Per **concessione implicita**, selezionare le caselle di controllo per i token di **accesso** e i **token ID**.</span><span class="sxs-lookup"><span data-stu-id="cbd28-123">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span><br>
   <span data-ttu-id="cbd28-124">3 \.</span><span class="sxs-lookup"><span data-stu-id="cbd28-124">3\.</span></span> <span data-ttu-id="cbd28-125">Per questa esperienza sono accettabili le impostazioni predefinite rimanenti per l'app.</span><span class="sxs-lookup"><span data-stu-id="cbd28-125">The remaining defaults for the app are acceptable for this experience.</span></span><br>
   <span data-ttu-id="cbd28-126">4 \.</span><span class="sxs-lookup"><span data-stu-id="cbd28-126">4\.</span></span> <span data-ttu-id="cbd28-127">Fare clic sul pulsante **Salva**.</span><span class="sxs-lookup"><span data-stu-id="cbd28-127">Select the **Save** button.</span></span>

   <span data-ttu-id="cbd28-128">Registrare l'ID applicazione (ID client) (ad esempio, `11111111-1111-1111-1111-111111111111`).</span><span class="sxs-lookup"><span data-stu-id="cbd28-128">Record the Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`).</span></span>

1. <span data-ttu-id="cbd28-129">Sostituire i segnaposto nel comando seguente con le informazioni registrate in precedenza ed eseguire il comando in una shell dei comandi:</span><span class="sxs-lookup"><span data-stu-id="cbd28-129">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" --tenant-id "common"
   ```

   <span data-ttu-id="cbd28-130">Per specificare il percorso di output, che crea una cartella di progetto, se non esiste, includere l'opzione di output nel comando con un percorso (ad `-o BlazorSample`esempio,).</span><span class="sxs-lookup"><span data-stu-id="cbd28-130">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="cbd28-131">Il nome della cartella diventa anche parte del nome del progetto.</span><span class="sxs-lookup"><span data-stu-id="cbd28-131">The folder name also becomes part of the project's name.</span></span>

<span data-ttu-id="cbd28-132">Dopo aver creato l'app, dovrebbe essere possibile:</span><span class="sxs-lookup"><span data-stu-id="cbd28-132">After creating the app, you should be able to:</span></span>

* <span data-ttu-id="cbd28-133">Accedere all'app usando un account Microsoft.</span><span class="sxs-lookup"><span data-stu-id="cbd28-133">Log into the app using a Microsoft Account.</span></span>
* <span data-ttu-id="cbd28-134">Richiedere i token di accesso per le API Microsoft usando lo stesso approccio usato Blazor per le app autonome purché l'app sia stata configurata correttamente.</span><span class="sxs-lookup"><span data-stu-id="cbd28-134">Request access tokens for Microsoft APIs using the same approach as for standalone Blazor apps provided that you have configured the app correctly.</span></span> <span data-ttu-id="cbd28-135">Per altre informazioni, vedere [Guida introduttiva: configurare un'applicazione per esporre le API Web](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span><span class="sxs-lookup"><span data-stu-id="cbd28-135">For more information, see [Quickstart: Configure an application to expose web APIs](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span></span>

## <a name="authentication-package"></a><span data-ttu-id="cbd28-136">Pacchetto di autenticazione</span><span class="sxs-lookup"><span data-stu-id="cbd28-136">Authentication package</span></span>

<span data-ttu-id="cbd28-137">Quando viene creata un'app per usare gli account aziendali o dell'`SingleOrg`Istituto di istruzione (), l'app riceve automaticamente un riferimento al pacchetto per`Microsoft.Authentication.WebAssembly.Msal` [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ().</span><span class="sxs-lookup"><span data-stu-id="cbd28-137">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="cbd28-138">Il pacchetto fornisce un set di primitive che consentono all'app di autenticare gli utenti e ottenere i token per chiamare le API protette.</span><span class="sxs-lookup"><span data-stu-id="cbd28-138">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="cbd28-139">Se si aggiunge l'autenticazione a un'app, aggiungere manualmente il pacchetto al file di progetto dell'app:</span><span class="sxs-lookup"><span data-stu-id="cbd28-139">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="cbd28-140">Sostituire `{VERSION}` nel riferimento al pacchetto precedente con la versione del `Microsoft.AspNetCore.Blazor.Templates` pacchetto illustrata nell' <xref:blazor/get-started> articolo.</span><span class="sxs-lookup"><span data-stu-id="cbd28-140">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="cbd28-141">Il `Microsoft.Authentication.WebAssembly.Msal` pacchetto aggiunge il `Microsoft.AspNetCore.Components.WebAssembly.Authentication` pacchetto all'app in modo transitivo.</span><span class="sxs-lookup"><span data-stu-id="cbd28-141">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="cbd28-142">Supporto del servizio di autenticazione</span><span class="sxs-lookup"><span data-stu-id="cbd28-142">Authentication service support</span></span>

<span data-ttu-id="cbd28-143">Il supporto per l'autenticazione degli utenti viene registrato nel contenitore del servizio `AddMsalAuthentication` con il metodo di estensione `Microsoft.Authentication.WebAssembly.Msal` fornito dal pacchetto.</span><span class="sxs-lookup"><span data-stu-id="cbd28-143">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="cbd28-144">Questo metodo configura tutti i servizi necessari per l'interazione dell'app con il provider di identità (IP).</span><span class="sxs-lookup"><span data-stu-id="cbd28-144">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="cbd28-145">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="cbd28-145">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    var authentication = options.ProviderOptions.Authentication;
    authentication.Authority = "{AUTHORITY}";
    authentication.ClientId = "{CLIENT ID}";
});
```

<span data-ttu-id="cbd28-146">Il `AddMsalAuthentication` metodo accetta un callback per configurare i parametri necessari per autenticare un'app.</span><span class="sxs-lookup"><span data-stu-id="cbd28-146">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="cbd28-147">Quando si registra l'app, è possibile ottenere i valori necessari per la configurazione dell'app dalla configurazione degli account Microsoft.</span><span class="sxs-lookup"><span data-stu-id="cbd28-147">The values required for configuring the app can be obtained from the Microsoft Accounts configuration when you register the app.</span></span>

## <a name="access-token-scopes"></a><span data-ttu-id="cbd28-148">Ambiti del token di accesso</span><span class="sxs-lookup"><span data-stu-id="cbd28-148">Access token scopes</span></span>

<span data-ttu-id="cbd28-149">Il Blazor modello webassembly non configura automaticamente l'app per richiedere un token di accesso per un'API protetta.</span><span class="sxs-lookup"><span data-stu-id="cbd28-149">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="cbd28-150">Per eseguire il provisioning di un token come parte del flusso di accesso, aggiungere l'ambito agli ambiti dei token di `MsalProviderOptions`accesso predefiniti di:</span><span class="sxs-lookup"><span data-stu-id="cbd28-150">To provision a token as part of the sign-in flow, add the scope to the default access token scopes of the `MsalProviderOptions`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="cbd28-151">Se il portale di Azure fornisce un URI di ambito e **l'app genera un'eccezione non gestita** quando riceve una risposta *401 non autorizzata* dall'API, provare a usare un URI dell'ambito che non includa lo schema e l'host.</span><span class="sxs-lookup"><span data-stu-id="cbd28-151">If the Azure portal provides a scope URI and **the app throws an unhandled exception** when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="cbd28-152">Ad esempio, il portale di Azure può fornire uno dei seguenti formati di URI di ambito:</span><span class="sxs-lookup"><span data-stu-id="cbd28-152">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="cbd28-153">Specificare l'URI dell'ambito senza lo schema e l'host:</span><span class="sxs-lookup"><span data-stu-id="cbd28-153">Supply the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

<span data-ttu-id="cbd28-154">Per altre informazioni, vedere <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span><span class="sxs-lookup"><span data-stu-id="cbd28-154">For more information, see <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span></span>

<!--
    For more information, see <xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests>.
-->

## <a name="imports-file"></a><span data-ttu-id="cbd28-155">Importa file</span><span class="sxs-lookup"><span data-stu-id="cbd28-155">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="cbd28-156">Pagina di indice</span><span class="sxs-lookup"><span data-stu-id="cbd28-156">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="cbd28-157">Componente app</span><span class="sxs-lookup"><span data-stu-id="cbd28-157">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="cbd28-158">Componente RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="cbd28-158">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="cbd28-159">Componente LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="cbd28-159">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="cbd28-160">Componente di autenticazione</span><span class="sxs-lookup"><span data-stu-id="cbd28-160">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="cbd28-161">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="cbd28-161">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* [<span data-ttu-id="cbd28-162">Guida introduttiva: Registrare un'applicazione con Microsoft Identity Platform</span><span class="sxs-lookup"><span data-stu-id="cbd28-162">Quickstart: Register an application with the Microsoft identity platform</span></span>](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal)
* [<span data-ttu-id="cbd28-163">Guida introduttiva: configurare un'applicazione per esporre le API Web</span><span class="sxs-lookup"><span data-stu-id="cbd28-163">Quickstart: Configure an application to expose web APIs</span></span>](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)
