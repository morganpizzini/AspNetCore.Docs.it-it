---
title: Proteggere un'app autonoma ASP.NET Core WebAssembly con account MicrosoftSecure an ASP.NET Core Blazor WebAssembly standalone app with Microsoft Accounts
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/08/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-microsoft-accounts
ms.openlocfilehash: 8c409651b3338c2baeae497bef43b994823a20f9
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80977080"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-microsoft-accounts"></a><span data-ttu-id="e90ed-102">Proteggere un'app autonoma ASP.NET Core WebAssembly con account MicrosoftSecure an ASP.NET Core Blazor WebAssembly standalone app with Microsoft Accounts</span><span class="sxs-lookup"><span data-stu-id="e90ed-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with Microsoft Accounts</span></span>

<span data-ttu-id="e90ed-103">Di [Javier Calvarro Nelson](https://github.com/javiercn) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="e90ed-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="e90ed-104">Per creare Blazor un'app autonoma WebAssembly che usa [gli account Microsoft con Azure Active Directory (AAD)](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal) per l'autenticazione:</span><span class="sxs-lookup"><span data-stu-id="e90ed-104">To create a Blazor WebAssembly standalone app that uses [Microsoft Accounts with Azure Active Directory (AAD)](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal) for authentication:</span></span>

1. [<span data-ttu-id="e90ed-105">Creare un tenant AAD e un'applicazione WebCreate an AAD tenant and web application</span><span class="sxs-lookup"><span data-stu-id="e90ed-105">Create an AAD tenant and web application</span></span>](/azure/active-directory/develop/v2-overview)

   <span data-ttu-id="e90ed-106">Registrare un'app AAD nell'area Registrazioni app **di Azure Active Directory** > del portale di Azure:Register a AAD app in the Azure Active Directory**App registrations** area of the Azure Azure Portal:</span><span class="sxs-lookup"><span data-stu-id="e90ed-106">Register a AAD app in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

   <span data-ttu-id="e90ed-107">1\.</span><span class="sxs-lookup"><span data-stu-id="e90ed-107">1\.</span></span> <span data-ttu-id="e90ed-108">Specificare un **nome** per l'app, \*\* Blazor \*\*ad esempio AAD client .</span><span class="sxs-lookup"><span data-stu-id="e90ed-108">Provide a **Name** for the app (for example, **Blazor Client AAD**).</span></span><br>
   <span data-ttu-id="e90ed-109">2\.</span><span class="sxs-lookup"><span data-stu-id="e90ed-109">2\.</span></span> <span data-ttu-id="e90ed-110">In **Tipi di account supportati**selezionare Account in qualsiasi directory **organizzativa.**</span><span class="sxs-lookup"><span data-stu-id="e90ed-110">In **Supported account types**, select **Accounts in any organizational directory**.</span></span><br>
   <span data-ttu-id="e90ed-111">3\.</span><span class="sxs-lookup"><span data-stu-id="e90ed-111">3\.</span></span> <span data-ttu-id="e90ed-112">Lasciare l'elenco a discesa URI di **reindirizzamento** impostato su **Web**e specificare un URI di reindirizzamento di `https://localhost:5001/authentication/login-callback`.</span><span class="sxs-lookup"><span data-stu-id="e90ed-112">Leave the **Redirect URI** drop down set to **Web**, and provide a redirect URI of `https://localhost:5001/authentication/login-callback`.</span></span><br>
   <span data-ttu-id="e90ed-113">4\.</span><span class="sxs-lookup"><span data-stu-id="e90ed-113">4\.</span></span> <span data-ttu-id="e90ed-114">Disattivare la casella di controllo **Autorizzazioni** > **concedere all'amministratore per le autorizzazioni openid e offline_access.**</span><span class="sxs-lookup"><span data-stu-id="e90ed-114">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span><br>
   <span data-ttu-id="e90ed-115">5\.</span><span class="sxs-lookup"><span data-stu-id="e90ed-115">5\.</span></span> <span data-ttu-id="e90ed-116">Selezionare **Registra**.</span><span class="sxs-lookup"><span data-stu-id="e90ed-116">Select **Register**.</span></span>

   <span data-ttu-id="e90ed-117">In**Configurazioni** > piattaforma **di autenticazione** > **Web**:</span><span class="sxs-lookup"><span data-stu-id="e90ed-117">In **Authentication** > **Platform configurations** > **Web**:</span></span>

   <span data-ttu-id="e90ed-118">1\.</span><span class="sxs-lookup"><span data-stu-id="e90ed-118">1\.</span></span> <span data-ttu-id="e90ed-119">Verificare che `https://localhost:5001/authentication/login-callback` l'URI di **reindirizzamento** di sia presente.</span><span class="sxs-lookup"><span data-stu-id="e90ed-119">Confirm the **Redirect URI** of `https://localhost:5001/authentication/login-callback` is present.</span></span><br>
   <span data-ttu-id="e90ed-120">2\.</span><span class="sxs-lookup"><span data-stu-id="e90ed-120">2\.</span></span> <span data-ttu-id="e90ed-121">Per **Concessione implicita**, selezionare le caselle di controllo per Token di **accesso** e **token ID**.</span><span class="sxs-lookup"><span data-stu-id="e90ed-121">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span><br>
   <span data-ttu-id="e90ed-122">3\.</span><span class="sxs-lookup"><span data-stu-id="e90ed-122">3\.</span></span> <span data-ttu-id="e90ed-123">Le impostazioni predefinite rimanenti per l'app sono accettabili per questa esperienza.</span><span class="sxs-lookup"><span data-stu-id="e90ed-123">The remaining defaults for the app are acceptable for this experience.</span></span><br>
   <span data-ttu-id="e90ed-124">4\.</span><span class="sxs-lookup"><span data-stu-id="e90ed-124">4\.</span></span> <span data-ttu-id="e90ed-125">Fare clic sul pulsante **Salva**.</span><span class="sxs-lookup"><span data-stu-id="e90ed-125">Select the **Save** button.</span></span>

   <span data-ttu-id="e90ed-126">Registrare l'ID applicazione (ID `11111111-1111-1111-1111-111111111111`client) (ad esempio, ).</span><span class="sxs-lookup"><span data-stu-id="e90ed-126">Record the Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`).</span></span>

1. <span data-ttu-id="e90ed-127">Sostituire i segnaposto nel comando seguente con le informazioni registrate in precedenza ed eseguire il comando in una shell dei comandi:</span><span class="sxs-lookup"><span data-stu-id="e90ed-127">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" --tenant-id "common"
   ```

   <span data-ttu-id="e90ed-128">Per specificare il percorso di output, che crea una cartella di progetto se non esiste, `-o BlazorSample`includere l'opzione di output nel comando con un percorso, ad esempio ).</span><span class="sxs-lookup"><span data-stu-id="e90ed-128">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="e90ed-129">Il nome della cartella diventa anche parte del nome del progetto.</span><span class="sxs-lookup"><span data-stu-id="e90ed-129">The folder name also becomes part of the project's name.</span></span>

<span data-ttu-id="e90ed-130">Dopo aver creato l'app, dovresti essere in grado di:</span><span class="sxs-lookup"><span data-stu-id="e90ed-130">After creating the app, you should be able to:</span></span>

* <span data-ttu-id="e90ed-131">Accedere all'app utilizzando un account Microsoft.</span><span class="sxs-lookup"><span data-stu-id="e90ed-131">Log into the app using a Microsoft Account.</span></span>
* <span data-ttu-id="e90ed-132">Richiedere i token di accesso per le API Microsoft Blazor usando lo stesso approccio usato per le app autonome, a condizione che l'app sia stata configurata correttamente.</span><span class="sxs-lookup"><span data-stu-id="e90ed-132">Request access tokens for Microsoft APIs using the same approach as for standalone Blazor apps provided that you have configured the app correctly.</span></span> <span data-ttu-id="e90ed-133">Per ulteriori informazioni, vedere [Guida introduttiva: Configurare un'applicazione per esporre le API Web](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span><span class="sxs-lookup"><span data-stu-id="e90ed-133">For more information, see [Quickstart: Configure an application to expose web APIs](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span></span>

## <a name="authentication-package"></a><span data-ttu-id="e90ed-134">Pacchetto di autenticazione</span><span class="sxs-lookup"><span data-stu-id="e90ed-134">Authentication package</span></span>

<span data-ttu-id="e90ed-135">Quando viene creata un'app per`SingleOrg`usare gli account aziendali o dell'istituto`Microsoft.Authentication.WebAssembly.Msal`di istruzione ( ), l'app riceve automaticamente un riferimento al pacchetto per la libreria di [autenticazione Microsoft](/azure/active-directory/develop/msal-overview) ( ).</span><span class="sxs-lookup"><span data-stu-id="e90ed-135">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="e90ed-136">Il pacchetto fornisce un set di primitive che consentono all'app di autenticare gli utenti e ottenere token per chiamare API protette.</span><span class="sxs-lookup"><span data-stu-id="e90ed-136">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="e90ed-137">Se si aggiunge l'autenticazione a un'app, aggiungi manualmente il pacchetto al file di progetto dell'app:</span><span class="sxs-lookup"><span data-stu-id="e90ed-137">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="e90ed-138">Sostituire `{VERSION}` nel riferimento al pacchetto precedente `Microsoft.AspNetCore.Blazor.Templates` con la <xref:blazor/get-started> versione del pacchetto illustrata nell'articolo.</span><span class="sxs-lookup"><span data-stu-id="e90ed-138">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="e90ed-139">Il `Microsoft.Authentication.WebAssembly.Msal` pacchetto aggiunge in `Microsoft.AspNetCore.Components.WebAssembly.Authentication` modo transitivo il pacchetto all'app.</span><span class="sxs-lookup"><span data-stu-id="e90ed-139">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="e90ed-140">Supporto del servizio di autenticazione</span><span class="sxs-lookup"><span data-stu-id="e90ed-140">Authentication service support</span></span>

<span data-ttu-id="e90ed-141">Il supporto per l'autenticazione degli utenti `AddMsalAuthentication` viene registrato `Microsoft.Authentication.WebAssembly.Msal` nel contenitore dei servizi con il metodo di estensione fornito dal pacchetto.</span><span class="sxs-lookup"><span data-stu-id="e90ed-141">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="e90ed-142">Questo metodo imposta tutti i servizi necessari all'app per interagire con il provider di identità (IP).</span><span class="sxs-lookup"><span data-stu-id="e90ed-142">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="e90ed-143">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="e90ed-143">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    var authentication = options.ProviderOptions.Authentication;
    authentication.Authority = "{AUTHORITY}";
    authentication.ClientId = "{CLIENT ID}";
});
```

<span data-ttu-id="e90ed-144">Il `AddMsalAuthentication` metodo accetta un callback per configurare i parametri necessari per autenticare un'app.</span><span class="sxs-lookup"><span data-stu-id="e90ed-144">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="e90ed-145">I valori necessari per la configurazione dell'app possono essere ottenuti dalla configurazione degli account Microsoft quando si registra l'app.</span><span class="sxs-lookup"><span data-stu-id="e90ed-145">The values required for configuring the app can be obtained from the Microsoft Accounts configuration when you register the app.</span></span>

## <a name="access-token-scopes"></a><span data-ttu-id="e90ed-146">Ambiti dei token di accessoAccess token scopes</span><span class="sxs-lookup"><span data-stu-id="e90ed-146">Access token scopes</span></span>

<span data-ttu-id="e90ed-147">Il Blazor modello WebAssembly non configura automaticamente l'app per richiedere un token di accesso per un'API sicura.</span><span class="sxs-lookup"><span data-stu-id="e90ed-147">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="e90ed-148">Per eseguire il provisioning di un token come parte del flusso di accesso, aggiungere l'ambito agli ambiti predefiniti del token di accesso: `MsalProviderOptions`</span><span class="sxs-lookup"><span data-stu-id="e90ed-148">To provision a token as part of the sign-in flow, add the scope to the default access token scopes of the `MsalProviderOptions`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="e90ed-149">Se il portale di Azure fornisce un URI di ambito e **l'app genera un'eccezione non gestita** quando riceve una risposta *401 Unauthorized* dall'API, provare a usare un URI di ambito che non includa lo schema e l'host.</span><span class="sxs-lookup"><span data-stu-id="e90ed-149">If the Azure portal provides a scope URI and **the app throws an unhandled exception** when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="e90ed-150">Ad esempio, il portale di Azure può fornire uno dei seguenti formati URI di ambito:</span><span class="sxs-lookup"><span data-stu-id="e90ed-150">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="e90ed-151">Fornire l'URI dell'ambito senza lo schema e l'host:</span><span class="sxs-lookup"><span data-stu-id="e90ed-151">Supply the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

<span data-ttu-id="e90ed-152">Per altre informazioni, vedere <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span><span class="sxs-lookup"><span data-stu-id="e90ed-152">For more information, see <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span></span>

## <a name="imports-file"></a><span data-ttu-id="e90ed-153">File di importazione</span><span class="sxs-lookup"><span data-stu-id="e90ed-153">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="e90ed-154">Pagina di indice</span><span class="sxs-lookup"><span data-stu-id="e90ed-154">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="e90ed-155">Componente dell'app</span><span class="sxs-lookup"><span data-stu-id="e90ed-155">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="e90ed-156">Componente RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="e90ed-156">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="e90ed-157">Componente LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="e90ed-157">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="e90ed-158">Componente di autenticazione</span><span class="sxs-lookup"><span data-stu-id="e90ed-158">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="e90ed-159">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="e90ed-159">Additional resources</span></span>

* [<span data-ttu-id="e90ed-160">Richiedere token di accesso aggiuntiviRequest additional access tokens</span><span class="sxs-lookup"><span data-stu-id="e90ed-160">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="e90ed-161">Guida introduttiva: Registrare un'applicazione con la piattaforma di identità MicrosoftQuickstart: Register an application with the Microsoft identity platform</span><span class="sxs-lookup"><span data-stu-id="e90ed-161">Quickstart: Register an application with the Microsoft identity platform</span></span>](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal)
* [<span data-ttu-id="e90ed-162">Guida introduttiva: Configurare un'applicazione per esporre le API Web</span><span class="sxs-lookup"><span data-stu-id="e90ed-162">Quickstart: Configure an application to expose web APIs</span></span>](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)
