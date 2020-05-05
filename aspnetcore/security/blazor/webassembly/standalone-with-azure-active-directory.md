---
title: Proteggere un'app Blazor ASP.NET Core webassembly autonoma con Azure Active Directory
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/24/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/blazor/webassembly/standalone-with-azure-active-directory
ms.openlocfilehash: 77584c7102fd2b288227877a052addb9f2296fa6
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776656"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-standalone-app-with-azure-active-directory"></a><span data-ttu-id="91e24-102">Proteggere un'app Blazor ASP.NET Core webassembly autonoma con Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="91e24-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory</span></span>

<span data-ttu-id="91e24-103">Di [Javier Calvarro Nelson](https://github.com/javiercn) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="91e24-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="91e24-104">Per creare un' Blazor app webassembly autonoma che usa [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) per l'autenticazione:</span><span class="sxs-lookup"><span data-stu-id="91e24-104">To create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) for authentication:</span></span>

<span data-ttu-id="91e24-105">[Creare un'applicazione Web e un tenant di AAD](/azure/active-directory/develop/v2-overview):</span><span class="sxs-lookup"><span data-stu-id="91e24-105">[Create an AAD tenant and web application](/azure/active-directory/develop/v2-overview):</span></span>

<span data-ttu-id="91e24-106">Registrare un'app AAD nell'area **Azure Active Directory** > **registrazioni app** del portale di Azure:</span><span class="sxs-lookup"><span data-stu-id="91e24-106">Register a AAD app in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="91e24-107">Specificare un **nome** per l'app (ad esempio, \*\* Blazor client AAD\*\*).</span><span class="sxs-lookup"><span data-stu-id="91e24-107">Provide a **Name** for the app (for example, **Blazor Client AAD**).</span></span>
1. <span data-ttu-id="91e24-108">Scegliere i **tipi di conto supportati**.</span><span class="sxs-lookup"><span data-stu-id="91e24-108">Choose a **Supported account types**.</span></span> <span data-ttu-id="91e24-109">È possibile selezionare gli **account in questa directory organizzativa solo** per questa esperienza.</span><span class="sxs-lookup"><span data-stu-id="91e24-109">You may select **Accounts in this organizational directory only** for this experience.</span></span>
1. <span data-ttu-id="91e24-110">Lasciare l'elenco a discesa **URI di reindirizzamento** impostato su **Web**e specificare un URI di `https://localhost:5001/authentication/login-callback`reindirizzamento.</span><span class="sxs-lookup"><span data-stu-id="91e24-110">Leave the **Redirect URI** drop down set to **Web**, and provide a redirect URI of `https://localhost:5001/authentication/login-callback`.</span></span>
1. <span data-ttu-id="91e24-111">Disabilitare la casella di controllo **autorizzazioni** > **concessi da amministratore a OpenID e autorizzazioni offline_access** .</span><span class="sxs-lookup"><span data-stu-id="91e24-111">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="91e24-112">Selezionare **Registra**.</span><span class="sxs-lookup"><span data-stu-id="91e24-112">Select **Register**.</span></span>

<span data-ttu-id="91e24-113">In **Authentication** >  > **configurazioni piattaforma**di autenticazione**Web**:</span><span class="sxs-lookup"><span data-stu-id="91e24-113">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="91e24-114">Verificare che l' **URI** di `https://localhost:5001/authentication/login-callback` Reindirizzamento di sia presente.</span><span class="sxs-lookup"><span data-stu-id="91e24-114">Confirm the **Redirect URI** of `https://localhost:5001/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="91e24-115">Per **concessione implicita**, selezionare le caselle di controllo per i token di **accesso** e i **token ID**.</span><span class="sxs-lookup"><span data-stu-id="91e24-115">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="91e24-116">Per questa esperienza sono accettabili le impostazioni predefinite rimanenti per l'app.</span><span class="sxs-lookup"><span data-stu-id="91e24-116">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="91e24-117">Fare clic sul pulsante **Salva**.</span><span class="sxs-lookup"><span data-stu-id="91e24-117">Select the **Save** button.</span></span>

<span data-ttu-id="91e24-118">Registrare le seguenti informazioni:</span><span class="sxs-lookup"><span data-stu-id="91e24-118">Record the following information:</span></span>

* <span data-ttu-id="91e24-119">ID applicazione (ID client) (ad esempio, `11111111-1111-1111-1111-111111111111`)</span><span class="sxs-lookup"><span data-stu-id="91e24-119">Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`)</span></span>
* <span data-ttu-id="91e24-120">ID directory (ID tenant) (ad esempio, `22222222-2222-2222-2222-222222222222`)</span><span class="sxs-lookup"><span data-stu-id="91e24-120">Directory ID (Tenant ID) (for example, `22222222-2222-2222-2222-222222222222`)</span></span>

<span data-ttu-id="91e24-121">Sostituire i segnaposto nel comando seguente con le informazioni registrate in precedenza ed eseguire il comando in una shell dei comandi:</span><span class="sxs-lookup"><span data-stu-id="91e24-121">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" --tenant-id "{TENANT ID}"
```

<span data-ttu-id="91e24-122">Per specificare il percorso di output, che crea una cartella di progetto, se non esiste, includere l'opzione di output nel comando con un percorso (ad `-o BlazorSample`esempio,).</span><span class="sxs-lookup"><span data-stu-id="91e24-122">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="91e24-123">Il nome della cartella diventa anche parte del nome del progetto.</span><span class="sxs-lookup"><span data-stu-id="91e24-123">The folder name also becomes part of the project's name.</span></span>

## <a name="authentication-package"></a><span data-ttu-id="91e24-124">Pacchetto di autenticazione</span><span class="sxs-lookup"><span data-stu-id="91e24-124">Authentication package</span></span>

<span data-ttu-id="91e24-125">Quando viene creata un'app per usare gli account aziendali o dell'`SingleOrg`Istituto di istruzione (), l'app riceve automaticamente un riferimento al pacchetto per`Microsoft.Authentication.WebAssembly.Msal` [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ().</span><span class="sxs-lookup"><span data-stu-id="91e24-125">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="91e24-126">Il pacchetto fornisce un set di primitive che consentono all'app di autenticare gli utenti e ottenere i token per chiamare le API protette.</span><span class="sxs-lookup"><span data-stu-id="91e24-126">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="91e24-127">Se si aggiunge l'autenticazione a un'app, aggiungere manualmente il pacchetto al file di progetto dell'app:</span><span class="sxs-lookup"><span data-stu-id="91e24-127">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="91e24-128">Sostituire `{VERSION}` nel riferimento al pacchetto precedente con la versione del `Microsoft.AspNetCore.Blazor.Templates` pacchetto illustrata nell' <xref:blazor/get-started> articolo.</span><span class="sxs-lookup"><span data-stu-id="91e24-128">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="91e24-129">Il `Microsoft.Authentication.WebAssembly.Msal` pacchetto aggiunge il `Microsoft.AspNetCore.Components.WebAssembly.Authentication` pacchetto all'app in modo transitivo.</span><span class="sxs-lookup"><span data-stu-id="91e24-129">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="91e24-130">Supporto del servizio di autenticazione</span><span class="sxs-lookup"><span data-stu-id="91e24-130">Authentication service support</span></span>

<span data-ttu-id="91e24-131">Il supporto per l'autenticazione degli utenti viene registrato nel contenitore del servizio `AddMsalAuthentication` con il metodo di estensione `Microsoft.Authentication.WebAssembly.Msal` fornito dal pacchetto.</span><span class="sxs-lookup"><span data-stu-id="91e24-131">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="91e24-132">Questo metodo configura tutti i servizi necessari per l'interazione dell'app con il Identity provider (IP).</span><span class="sxs-lookup"><span data-stu-id="91e24-132">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="91e24-133">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="91e24-133">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
});
```

<span data-ttu-id="91e24-134">Il `AddMsalAuthentication` metodo accetta un callback per configurare i parametri necessari per autenticare un'app.</span><span class="sxs-lookup"><span data-stu-id="91e24-134">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="91e24-135">Quando si registra l'app, è possibile ottenere i valori necessari per la configurazione dell'app dalla configurazione degli account Microsoft.</span><span class="sxs-lookup"><span data-stu-id="91e24-135">The values required for configuring the app can be obtained from the Microsoft Accounts configuration when you register the app.</span></span>

<span data-ttu-id="91e24-136">La configurazione viene fornita dal file *wwwroot/appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="91e24-136">Configuration is supplied by the *wwwroot/appsettings.json* file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/{TENANT ID}",
    "ClientId": "{CLIENT ID}"
  }
}
```

<span data-ttu-id="91e24-137">Esempio:</span><span class="sxs-lookup"><span data-stu-id="91e24-137">Example:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/e86c78e2-...-918e0565a45e",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd"
  }
}
```

## <a name="access-token-scopes"></a><span data-ttu-id="91e24-138">Ambiti del token di accesso</span><span class="sxs-lookup"><span data-stu-id="91e24-138">Access token scopes</span></span>

<span data-ttu-id="91e24-139">Il Blazor modello webassembly non configura automaticamente l'app per richiedere un token di accesso per un'API protetta.</span><span class="sxs-lookup"><span data-stu-id="91e24-139">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="91e24-140">Per eseguire il provisioning di un token di accesso come parte del flusso di accesso, aggiungere l'ambito agli ambiti dei token `MsalProviderOptions`di accesso predefiniti di:</span><span class="sxs-lookup"><span data-stu-id="91e24-140">To provision an access token as part of the sign-in flow, add the scope to the default access token scopes of the `MsalProviderOptions`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="91e24-141">Se il portale di Azure fornisce un URI di ambito e **l'app genera un'eccezione non gestita** quando riceve una risposta *401 non autorizzata* dall'API, provare a usare un URI dell'ambito che non includa lo schema e l'host.</span><span class="sxs-lookup"><span data-stu-id="91e24-141">If the Azure portal provides a scope URI and **the app throws an unhandled exception** when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="91e24-142">Ad esempio, il portale di Azure può fornire uno dei seguenti formati di URI di ambito:</span><span class="sxs-lookup"><span data-stu-id="91e24-142">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="91e24-143">Specificare l'URI dell'ambito senza lo schema e l'host:</span><span class="sxs-lookup"><span data-stu-id="91e24-143">Supply the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

<span data-ttu-id="91e24-144">Per ulteriori informazioni, vedere le sezioni seguenti dell'articolo *scenari aggiuntivi* :</span><span class="sxs-lookup"><span data-stu-id="91e24-144">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="91e24-145">Richiedere token di accesso aggiuntivi</span><span class="sxs-lookup"><span data-stu-id="91e24-145">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="91e24-146">Connetti token alle richieste in uscita</span><span class="sxs-lookup"><span data-stu-id="91e24-146">Attach tokens to outgoing requests</span></span>](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a><span data-ttu-id="91e24-147">Importa file</span><span class="sxs-lookup"><span data-stu-id="91e24-147">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="91e24-148">Pagina di indice</span><span class="sxs-lookup"><span data-stu-id="91e24-148">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="91e24-149">Componente app</span><span class="sxs-lookup"><span data-stu-id="91e24-149">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="91e24-150">Componente RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="91e24-150">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="91e24-151">Componente LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="91e24-151">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="91e24-152">Componente di autenticazione</span><span class="sxs-lookup"><span data-stu-id="91e24-152">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="91e24-153">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="91e24-153">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* <xref:security/authentication/azure-active-directory/index>
* [<span data-ttu-id="91e24-154">Documentazione di Microsoft Identity Platform</span><span class="sxs-lookup"><span data-stu-id="91e24-154">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
