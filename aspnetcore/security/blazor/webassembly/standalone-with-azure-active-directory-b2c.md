---
title: Proteggere un'app Blazor ASP.NET Core webassembly autonoma con Azure Active Directory B2C
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/23/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-azure-active-directory-b2c
ms.openlocfilehash: 7d1031d3eac0e1d6790ca946809038127eb59a73
ms.sourcegitcommit: 7bb14d005155a5044c7902a08694ee8ccb20c113
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82111162"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-azure-active-directory-b2c"></a><span data-ttu-id="fed2a-102">Proteggere un'app Blazor ASP.NET Core webassembly autonoma con Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="fed2a-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory B2C</span></span>

<span data-ttu-id="fed2a-103">Di [Javier Calvarro Nelson](https://github.com/javiercn) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="fed2a-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

> [!NOTE]
> <span data-ttu-id="fed2a-104">Le indicazioni fornite in questo articolo si applicano a ASP.NET Core 3,2 Preview 4.</span><span class="sxs-lookup"><span data-stu-id="fed2a-104">The guidance in this article applies to ASP.NET Core 3.2 Preview 4.</span></span> <span data-ttu-id="fed2a-105">Questo argomento verrà aggiornato per coprire l'anteprima 5 il venerdì 24 aprile.</span><span class="sxs-lookup"><span data-stu-id="fed2a-105">This topic will be updated to cover Preview 5 on Friday, April 24.</span></span>

<span data-ttu-id="fed2a-106">Per creare un' Blazor app webassembly autonoma che usa [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) per l'autenticazione:</span><span class="sxs-lookup"><span data-stu-id="fed2a-106">To create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) for authentication:</span></span>

1. <span data-ttu-id="fed2a-107">Per creare un tenant e registrare un'app Web nel portale di Azure, seguire le istruzioni riportate negli argomenti seguenti:</span><span class="sxs-lookup"><span data-stu-id="fed2a-107">Follow the guidance in the following topics to create a tenant and register a web app in the Azure Portal:</span></span>

   * <span data-ttu-id="fed2a-108">[Creare un tenant](/azure/active-directory-b2c/tutorial-create-tenant) &ndash; AAD B2C registrare le informazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="fed2a-108">[Create an AAD B2C tenant](/azure/active-directory-b2c/tutorial-create-tenant) &ndash; Record the following information:</span></span>

     <span data-ttu-id="fed2a-109">1 \.</span><span class="sxs-lookup"><span data-stu-id="fed2a-109">1\.</span></span> <span data-ttu-id="fed2a-110">AAD B2C istanza (ad esempio, `https://contoso.b2clogin.com/`, che include la barra finale)</span><span class="sxs-lookup"><span data-stu-id="fed2a-110">AAD B2C instance (for example, `https://contoso.b2clogin.com/`, which includes the trailing slash)</span></span><br>
     <span data-ttu-id="fed2a-111">2 \.</span><span class="sxs-lookup"><span data-stu-id="fed2a-111">2\.</span></span> <span data-ttu-id="fed2a-112">AAD B2C dominio tenant (ad esempio, `contoso.onmicrosoft.com`)</span><span class="sxs-lookup"><span data-stu-id="fed2a-112">AAD B2C Tenant domain (for example, `contoso.onmicrosoft.com`)</span></span>

   * <span data-ttu-id="fed2a-113">[Registrare un'applicazione](/azure/active-directory-b2c/tutorial-register-applications) &ndash; Web effettuare le selezioni seguenti durante la registrazione dell'app:</span><span class="sxs-lookup"><span data-stu-id="fed2a-113">[Register a web application](/azure/active-directory-b2c/tutorial-register-applications) &ndash; Make the following selections during app registration:</span></span>

     <span data-ttu-id="fed2a-114">1 \.</span><span class="sxs-lookup"><span data-stu-id="fed2a-114">1\.</span></span> <span data-ttu-id="fed2a-115">Impostare **app Web/API Web** su **Sì**.</span><span class="sxs-lookup"><span data-stu-id="fed2a-115">Set **Web App / Web API** to **Yes**.</span></span><br>
     <span data-ttu-id="fed2a-116">2 \.</span><span class="sxs-lookup"><span data-stu-id="fed2a-116">2\.</span></span> <span data-ttu-id="fed2a-117">Impostare **Consenti flusso implicito** su **Sì**.</span><span class="sxs-lookup"><span data-stu-id="fed2a-117">Set **Allow implicit flow** to **Yes**.</span></span><br>
     <span data-ttu-id="fed2a-118">3 \.</span><span class="sxs-lookup"><span data-stu-id="fed2a-118">3\.</span></span> <span data-ttu-id="fed2a-119">Aggiungere un **URL** di risposta `https://localhost:5001/authentication/login-callback`di.</span><span class="sxs-lookup"><span data-stu-id="fed2a-119">Add a **Reply URL** of `https://localhost:5001/authentication/login-callback`.</span></span>

     <span data-ttu-id="fed2a-120">Registrare l'ID applicazione (ID client) (ad esempio, `11111111-1111-1111-1111-111111111111`).</span><span class="sxs-lookup"><span data-stu-id="fed2a-120">Record the Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`).</span></span>

   * <span data-ttu-id="fed2a-121">[Creare flussi](/azure/active-directory-b2c/tutorial-create-user-flows) &ndash; utente creare un flusso utente di iscrizione e accesso.</span><span class="sxs-lookup"><span data-stu-id="fed2a-121">[Create user flows](/azure/active-directory-b2c/tutorial-create-user-flows) &ndash; Create a sign-up and sign-in user flow.</span></span>

     <span data-ttu-id="fed2a-122">Selezionare almeno l'attributo utente**nome visualizzato** dell' `context.User.Identity.Name` **applicazione Claims** > per popolare nel `LoginDisplay` componente (*Shared/LoginDisplay. Razor*).</span><span class="sxs-lookup"><span data-stu-id="fed2a-122">At a minimum, select the **Application claims** > **Display Name** user attribute to populate the `context.User.Identity.Name` in the `LoginDisplay` component (*Shared/LoginDisplay.razor*).</span></span>

     <span data-ttu-id="fed2a-123">Registrare il nome del flusso utente di iscrizione e accesso creato per l'app (ad esempio, `B2C_1_signupsignin`).</span><span class="sxs-lookup"><span data-stu-id="fed2a-123">Record the sign-up and sign-in user flow name created for the app (for example, `B2C_1_signupsignin`).</span></span>

1. <span data-ttu-id="fed2a-124">Sostituire i segnaposto nel comando seguente con le informazioni registrate in precedenza ed eseguire il comando in una shell dei comandi:</span><span class="sxs-lookup"><span data-stu-id="fed2a-124">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --client-id "{CLIENT ID}" --domain "{DOMAIN}" -ssp "{SIGN UP OR SIGN IN POLICY}"
   ```

   <span data-ttu-id="fed2a-125">Per specificare il percorso di output, che crea una cartella di progetto, se non esiste, includere l'opzione di output nel comando con un percorso (ad `-o BlazorSample`esempio,).</span><span class="sxs-lookup"><span data-stu-id="fed2a-125">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="fed2a-126">Il nome della cartella diventa anche parte del nome del progetto.</span><span class="sxs-lookup"><span data-stu-id="fed2a-126">The folder name also becomes part of the project's name.</span></span>

## <a name="authentication-package"></a><span data-ttu-id="fed2a-127">Pacchetto di autenticazione</span><span class="sxs-lookup"><span data-stu-id="fed2a-127">Authentication package</span></span>

<span data-ttu-id="fed2a-128">Quando viene creata un'app per usare un singolo account B2C (`IndividualB2C`), l'app riceve automaticamente un riferimento al pacchetto per [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span><span class="sxs-lookup"><span data-stu-id="fed2a-128">When an app is created to use an Individual B2C Account (`IndividualB2C`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="fed2a-129">Il pacchetto fornisce un set di primitive che consentono all'app di autenticare gli utenti e ottenere i token per chiamare le API protette.</span><span class="sxs-lookup"><span data-stu-id="fed2a-129">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="fed2a-130">Se si aggiunge l'autenticazione a un'app, aggiungere manualmente il pacchetto al file di progetto dell'app:</span><span class="sxs-lookup"><span data-stu-id="fed2a-130">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="fed2a-131">Sostituire `{VERSION}` nel riferimento al pacchetto precedente con la versione del `Microsoft.AspNetCore.Blazor.Templates` pacchetto illustrata nell' <xref:blazor/get-started> articolo.</span><span class="sxs-lookup"><span data-stu-id="fed2a-131">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="fed2a-132">Il `Microsoft.Authentication.WebAssembly.Msal` pacchetto aggiunge il `Microsoft.AspNetCore.Components.WebAssembly.Authentication` pacchetto all'app in modo transitivo.</span><span class="sxs-lookup"><span data-stu-id="fed2a-132">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="fed2a-133">Supporto del servizio di autenticazione</span><span class="sxs-lookup"><span data-stu-id="fed2a-133">Authentication service support</span></span>

<span data-ttu-id="fed2a-134">Il supporto per l'autenticazione degli utenti viene registrato nel contenitore del servizio `AddMsalAuthentication` con il metodo di estensione `Microsoft.Authentication.WebAssembly.Msal` fornito dal pacchetto.</span><span class="sxs-lookup"><span data-stu-id="fed2a-134">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="fed2a-135">Questo metodo configura tutti i servizi necessari per l'interazione dell'app con il provider di identità (IP).</span><span class="sxs-lookup"><span data-stu-id="fed2a-135">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="fed2a-136">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="fed2a-136">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    var authentication = options.ProviderOptions.Authentication;
    authentication.Authority = 
        "{AAD B2C INSTANCE}{DOMAIN}/{SIGN UP OR SIGN IN POLICY}";
    authentication.ClientId = "{CLIENT ID}";
    authentication.ValidateAuthority = false;
});
```

<span data-ttu-id="fed2a-137">Il `AddMsalAuthentication` metodo accetta un callback per configurare i parametri necessari per autenticare un'app.</span><span class="sxs-lookup"><span data-stu-id="fed2a-137">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="fed2a-138">Quando si registra l'app, è possibile ottenere i valori necessari per la configurazione dell'app dalla configurazione di AAD del portale di Azure.</span><span class="sxs-lookup"><span data-stu-id="fed2a-138">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

## <a name="access-token-scopes"></a><span data-ttu-id="fed2a-139">Ambiti del token di accesso</span><span class="sxs-lookup"><span data-stu-id="fed2a-139">Access token scopes</span></span>

<span data-ttu-id="fed2a-140">Il Blazor modello webassembly non configura automaticamente l'app per richiedere un token di accesso per un'API protetta.</span><span class="sxs-lookup"><span data-stu-id="fed2a-140">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="fed2a-141">Per eseguire il provisioning di un token come parte del flusso di accesso, aggiungere l'ambito agli ambiti dei token di `MsalProviderOptions`accesso predefiniti di:</span><span class="sxs-lookup"><span data-stu-id="fed2a-141">To provision a token as part of the sign-in flow, add the scope to the default access token scopes of the `MsalProviderOptions`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="fed2a-142">Se il portale di Azure fornisce un URI di ambito e **l'app genera un'eccezione non gestita** quando riceve una risposta *401 non autorizzata* dall'API, provare a usare un URI dell'ambito che non includa lo schema e l'host.</span><span class="sxs-lookup"><span data-stu-id="fed2a-142">If the Azure portal provides a scope URI and **the app throws an unhandled exception** when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="fed2a-143">Ad esempio, il portale di Azure può fornire uno dei seguenti formati di URI di ambito:</span><span class="sxs-lookup"><span data-stu-id="fed2a-143">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="fed2a-144">Specificare l'URI dell'ambito senza lo schema e l'host:</span><span class="sxs-lookup"><span data-stu-id="fed2a-144">Supply the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

<span data-ttu-id="fed2a-145">Per altre informazioni, vedere <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span><span class="sxs-lookup"><span data-stu-id="fed2a-145">For more information, see <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span></span>

<!--
    For more information, see <xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests>.
-->

## <a name="imports-file"></a><span data-ttu-id="fed2a-146">Importa file</span><span class="sxs-lookup"><span data-stu-id="fed2a-146">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="fed2a-147">Pagina di indice</span><span class="sxs-lookup"><span data-stu-id="fed2a-147">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="fed2a-148">Componente app</span><span class="sxs-lookup"><span data-stu-id="fed2a-148">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="fed2a-149">Componente RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="fed2a-149">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="fed2a-150">Componente LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="fed2a-150">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="fed2a-151">Componente di autenticazione</span><span class="sxs-lookup"><span data-stu-id="fed2a-151">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/wasm-aad-b2c-userflows.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="fed2a-152">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="fed2a-152">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* <xref:security/authentication/azure-ad-b2c>
* [<span data-ttu-id="fed2a-153">Esercitazione: Creare un tenant di Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="fed2a-153">Tutorial: Create an Azure Active Directory B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)
* [<span data-ttu-id="fed2a-154">Documentazione di Microsoft Identity Platform</span><span class="sxs-lookup"><span data-stu-id="fed2a-154">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
