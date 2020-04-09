---
title: Proteggere un'app autonoma ASP.NET Core WebAssembly con Azure Active DirectorySecure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/08/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-azure-active-directory
ms.openlocfilehash: 7e132723657b7e12803b67ec12c3a33f1945baa3
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80976996"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-azure-active-directory"></a><span data-ttu-id="b8a13-102">Proteggere un'app autonoma ASP.NET Core WebAssembly con Azure Active DirectorySecure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="b8a13-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory</span></span>

<span data-ttu-id="b8a13-103">Di [Javier Calvarro Nelson](https://github.com/javiercn) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="b8a13-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="b8a13-104">Per creare Blazor un'app autonoma WebAssembly che usa [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) per l'autenticazione:To create a WebAssembly standalone app that uses Azure Active Directory (AAD) for authentication:</span><span class="sxs-lookup"><span data-stu-id="b8a13-104">To create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) for authentication:</span></span>

<span data-ttu-id="b8a13-105">[Creare un tenant AAD e un'applicazione Web:](/azure/active-directory/develop/v2-overview)</span><span class="sxs-lookup"><span data-stu-id="b8a13-105">[Create an AAD tenant and web application](/azure/active-directory/develop/v2-overview):</span></span>

<span data-ttu-id="b8a13-106">Registrare un'app AAD nell'area Registrazioni app **di Azure Active Directory** > del portale di Azure:Register a AAD app in the Azure Active Directory**App registrations** area of the Azure Azure Portal:</span><span class="sxs-lookup"><span data-stu-id="b8a13-106">Register a AAD app in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="b8a13-107">Specificare un **nome** per l'app, \*\* Blazor \*\*ad esempio AAD client .</span><span class="sxs-lookup"><span data-stu-id="b8a13-107">Provide a **Name** for the app (for example, **Blazor Client AAD**).</span></span>
1. <span data-ttu-id="b8a13-108">Scegliere un **Tipo di account supportato**.</span><span class="sxs-lookup"><span data-stu-id="b8a13-108">Choose a **Supported account types**.</span></span> <span data-ttu-id="b8a13-109">Puoi selezionare **Account in questa directory dell'organizzazione solo** per questa esperienza.</span><span class="sxs-lookup"><span data-stu-id="b8a13-109">You may select **Accounts in this organizational directory only** for this experience.</span></span>
1. <span data-ttu-id="b8a13-110">Lasciare l'elenco a discesa URI di **reindirizzamento** impostato su **Web**e specificare un URI di reindirizzamento di `https://localhost:5001/authentication/login-callback`.</span><span class="sxs-lookup"><span data-stu-id="b8a13-110">Leave the **Redirect URI** drop down set to **Web**, and provide a redirect URI of `https://localhost:5001/authentication/login-callback`.</span></span>
1. <span data-ttu-id="b8a13-111">Disattivare la casella di controllo **Autorizzazioni** > **concedere all'amministratore per le autorizzazioni openid e offline_access.**</span><span class="sxs-lookup"><span data-stu-id="b8a13-111">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="b8a13-112">Selezionare **Registra**.</span><span class="sxs-lookup"><span data-stu-id="b8a13-112">Select **Register**.</span></span>

<span data-ttu-id="b8a13-113">In**Configurazioni** > piattaforma **di autenticazione** > **Web**:</span><span class="sxs-lookup"><span data-stu-id="b8a13-113">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="b8a13-114">Verificare che `https://localhost:5001/authentication/login-callback` l'URI di **reindirizzamento** di sia presente.</span><span class="sxs-lookup"><span data-stu-id="b8a13-114">Confirm the **Redirect URI** of `https://localhost:5001/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="b8a13-115">Per **Concessione implicita**, selezionare le caselle di controllo per Token di **accesso** e **token ID**.</span><span class="sxs-lookup"><span data-stu-id="b8a13-115">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="b8a13-116">Le impostazioni predefinite rimanenti per l'app sono accettabili per questa esperienza.</span><span class="sxs-lookup"><span data-stu-id="b8a13-116">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="b8a13-117">Fare clic sul pulsante **Salva**.</span><span class="sxs-lookup"><span data-stu-id="b8a13-117">Select the **Save** button.</span></span>

<span data-ttu-id="b8a13-118">Registrare le seguenti informazioni:</span><span class="sxs-lookup"><span data-stu-id="b8a13-118">Record the following information:</span></span>

* <span data-ttu-id="b8a13-119">ID applicazione (ID client) `11111111-1111-1111-1111-111111111111`(ad esempio, )</span><span class="sxs-lookup"><span data-stu-id="b8a13-119">Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`)</span></span>
* <span data-ttu-id="b8a13-120">ID directory (ID tenant) `22222222-2222-2222-2222-222222222222`(ad esempio, )</span><span class="sxs-lookup"><span data-stu-id="b8a13-120">Directory ID (Tenant ID) (for example, `22222222-2222-2222-2222-222222222222`)</span></span>

<span data-ttu-id="b8a13-121">Sostituire i segnaposto nel comando seguente con le informazioni registrate in precedenza ed eseguire il comando in una shell dei comandi:</span><span class="sxs-lookup"><span data-stu-id="b8a13-121">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" --tenant-id "{TENANT ID}"
```

<span data-ttu-id="b8a13-122">Per specificare il percorso di output, che crea una cartella di progetto se non esiste, `-o BlazorSample`includere l'opzione di output nel comando con un percorso, ad esempio ).</span><span class="sxs-lookup"><span data-stu-id="b8a13-122">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="b8a13-123">Il nome della cartella diventa anche parte del nome del progetto.</span><span class="sxs-lookup"><span data-stu-id="b8a13-123">The folder name also becomes part of the project's name.</span></span>

## <a name="authentication-package"></a><span data-ttu-id="b8a13-124">Pacchetto di autenticazione</span><span class="sxs-lookup"><span data-stu-id="b8a13-124">Authentication package</span></span>

<span data-ttu-id="b8a13-125">Quando viene creata un'app per`SingleOrg`usare gli account aziendali o dell'istituto`Microsoft.Authentication.WebAssembly.Msal`di istruzione ( ), l'app riceve automaticamente un riferimento al pacchetto per la libreria di [autenticazione Microsoft](/azure/active-directory/develop/msal-overview) ( ).</span><span class="sxs-lookup"><span data-stu-id="b8a13-125">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="b8a13-126">Il pacchetto fornisce un set di primitive che consentono all'app di autenticare gli utenti e ottenere token per chiamare API protette.</span><span class="sxs-lookup"><span data-stu-id="b8a13-126">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="b8a13-127">Se si aggiunge l'autenticazione a un'app, aggiungi manualmente il pacchetto al file di progetto dell'app:</span><span class="sxs-lookup"><span data-stu-id="b8a13-127">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="b8a13-128">Sostituire `{VERSION}` nel riferimento al pacchetto precedente `Microsoft.AspNetCore.Blazor.Templates` con la <xref:blazor/get-started> versione del pacchetto illustrata nell'articolo.</span><span class="sxs-lookup"><span data-stu-id="b8a13-128">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="b8a13-129">Il `Microsoft.Authentication.WebAssembly.Msal` pacchetto aggiunge in `Microsoft.AspNetCore.Components.WebAssembly.Authentication` modo transitivo il pacchetto all'app.</span><span class="sxs-lookup"><span data-stu-id="b8a13-129">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="b8a13-130">Supporto del servizio di autenticazione</span><span class="sxs-lookup"><span data-stu-id="b8a13-130">Authentication service support</span></span>

<span data-ttu-id="b8a13-131">Il supporto per l'autenticazione degli utenti `AddMsalAuthentication` viene registrato `Microsoft.Authentication.WebAssembly.Msal` nel contenitore dei servizi con il metodo di estensione fornito dal pacchetto.</span><span class="sxs-lookup"><span data-stu-id="b8a13-131">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="b8a13-132">Questo metodo imposta tutti i servizi necessari all'app per interagire con il provider di identità (IP).</span><span class="sxs-lookup"><span data-stu-id="b8a13-132">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="b8a13-133">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="b8a13-133">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    var authentication = options.ProviderOptions.Authentication;
    authentication.Authority = "https://login.microsoftonline.com/{TENANT ID}";
    authentication.ClientId = "{CLIENT ID}";
});
```

<span data-ttu-id="b8a13-134">Il `AddMsalAuthentication` metodo accetta un callback per configurare i parametri necessari per autenticare un'app.</span><span class="sxs-lookup"><span data-stu-id="b8a13-134">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="b8a13-135">I valori necessari per la configurazione dell'app possono essere ottenuti dalla configurazione di Azure Portal AAD quando si registra l'app.</span><span class="sxs-lookup"><span data-stu-id="b8a13-135">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

## <a name="access-token-scopes"></a><span data-ttu-id="b8a13-136">Ambiti dei token di accessoAccess token scopes</span><span class="sxs-lookup"><span data-stu-id="b8a13-136">Access token scopes</span></span>

<span data-ttu-id="b8a13-137">Il Blazor modello WebAssembly non configura automaticamente l'app per richiedere un token di accesso per un'API sicura.</span><span class="sxs-lookup"><span data-stu-id="b8a13-137">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="b8a13-138">Per eseguire il provisioning di un token come parte del flusso di accesso, aggiungere l'ambito agli ambiti predefiniti del token di accesso: `MsalProviderOptions`</span><span class="sxs-lookup"><span data-stu-id="b8a13-138">To provision a token as part of the sign-in flow, add the scope to the default access token scopes of the `MsalProviderOptions`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="b8a13-139">Se il portale di Azure fornisce un URI di ambito e **l'app genera un'eccezione non gestita** quando riceve una risposta *401 Unauthorized* dall'API, provare a usare un URI di ambito che non includa lo schema e l'host.</span><span class="sxs-lookup"><span data-stu-id="b8a13-139">If the Azure portal provides a scope URI and **the app throws an unhandled exception** when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="b8a13-140">Ad esempio, il portale di Azure può fornire uno dei seguenti formati URI di ambito:</span><span class="sxs-lookup"><span data-stu-id="b8a13-140">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="b8a13-141">Fornire l'URI dell'ambito senza lo schema e l'host:</span><span class="sxs-lookup"><span data-stu-id="b8a13-141">Supply the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

<span data-ttu-id="b8a13-142">Per altre informazioni, vedere <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span><span class="sxs-lookup"><span data-stu-id="b8a13-142">For more information, see <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span></span>

## <a name="imports-file"></a><span data-ttu-id="b8a13-143">File di importazione</span><span class="sxs-lookup"><span data-stu-id="b8a13-143">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="b8a13-144">Pagina di indice</span><span class="sxs-lookup"><span data-stu-id="b8a13-144">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="b8a13-145">Componente dell'app</span><span class="sxs-lookup"><span data-stu-id="b8a13-145">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="b8a13-146">Componente RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="b8a13-146">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="b8a13-147">Componente LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="b8a13-147">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="b8a13-148">Componente di autenticazione</span><span class="sxs-lookup"><span data-stu-id="b8a13-148">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="b8a13-149">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="b8a13-149">Additional resources</span></span>

* [<span data-ttu-id="b8a13-150">Richiedere token di accesso aggiuntiviRequest additional access tokens</span><span class="sxs-lookup"><span data-stu-id="b8a13-150">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* <xref:security/authentication/azure-active-directory/index>
* [<span data-ttu-id="b8a13-151">Documentazione di Microsoft Identity Platform</span><span class="sxs-lookup"><span data-stu-id="b8a13-151">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
