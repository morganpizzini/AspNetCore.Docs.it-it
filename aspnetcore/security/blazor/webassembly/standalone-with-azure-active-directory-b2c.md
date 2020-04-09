---
title: Proteggere un'app autonoma ASP.NET Core WebAssembly con Azure Active Directory B2CSecure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory B2C
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/08/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-azure-active-directory-b2c
ms.openlocfilehash: 0734bad2d4281eb856783a362ef8c608a303c17a
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80977054"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-azure-active-directory-b2c"></a><span data-ttu-id="e879c-102">Proteggere un'app autonoma ASP.NET Core WebAssembly con Azure Active Directory B2CSecure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="e879c-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory B2C</span></span>

<span data-ttu-id="e879c-103">Di [Javier Calvarro Nelson](https://github.com/javiercn) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="e879c-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="e879c-104">Per creare Blazor un'app autonoma WebAssembly che usa Azure Active Directory (AAD) B2C per l'autenticazione:To create a WebAssembly standalone app that uses [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) for authentication:</span><span class="sxs-lookup"><span data-stu-id="e879c-104">To create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) for authentication:</span></span>

1. <span data-ttu-id="e879c-105">Seguire le indicazioni negli argomenti seguenti per creare un tenant e registrare un'app Web nel portale di Azure:Follow the guidance in the following topics to create a tenant and register a web app in the Azure Portal:</span><span class="sxs-lookup"><span data-stu-id="e879c-105">Follow the guidance in the following topics to create a tenant and register a web app in the Azure Portal:</span></span>

   * <span data-ttu-id="e879c-106">[Creare un tenant AAD B2C](/azure/active-directory-b2c/tutorial-create-tenant) &ndash; Registrare le informazioni seguenti:Create an AAD B2C tenant Record the following information:</span><span class="sxs-lookup"><span data-stu-id="e879c-106">[Create an AAD B2C tenant](/azure/active-directory-b2c/tutorial-create-tenant) &ndash; Record the following information:</span></span>

     <span data-ttu-id="e879c-107">1\.</span><span class="sxs-lookup"><span data-stu-id="e879c-107">1\.</span></span> <span data-ttu-id="e879c-108">AAD B2C (ad `https://contoso.b2clogin.com/`esempio, , che include la barra finale)</span><span class="sxs-lookup"><span data-stu-id="e879c-108">AAD B2C instance (for example, `https://contoso.b2clogin.com/`, which includes the trailing slash)</span></span><br>
     <span data-ttu-id="e879c-109">2\.</span><span class="sxs-lookup"><span data-stu-id="e879c-109">2\.</span></span> <span data-ttu-id="e879c-110">AAD B2C Dominio tenant `contoso.onmicrosoft.com`(ad esempio, )</span><span class="sxs-lookup"><span data-stu-id="e879c-110">AAD B2C Tenant domain (for example, `contoso.onmicrosoft.com`)</span></span>

   * <span data-ttu-id="e879c-111">[Registrare un'applicazione](/azure/active-directory-b2c/tutorial-register-applications) &ndash; Web Effettuare le seguenti selezioni durante la registrazione dell'app:</span><span class="sxs-lookup"><span data-stu-id="e879c-111">[Register a web application](/azure/active-directory-b2c/tutorial-register-applications) &ndash; Make the following selections during app registration:</span></span>

     <span data-ttu-id="e879c-112">1\.</span><span class="sxs-lookup"><span data-stu-id="e879c-112">1\.</span></span> <span data-ttu-id="e879c-113">Impostare **App Web / API Web su** **Sì**.</span><span class="sxs-lookup"><span data-stu-id="e879c-113">Set **Web App / Web API** to **Yes**.</span></span><br>
     <span data-ttu-id="e879c-114">2\.</span><span class="sxs-lookup"><span data-stu-id="e879c-114">2\.</span></span> <span data-ttu-id="e879c-115">Impostare **Consenti flusso implicito su** **Sì**.</span><span class="sxs-lookup"><span data-stu-id="e879c-115">Set **Allow implicit flow** to **Yes**.</span></span><br>
     <span data-ttu-id="e879c-116">3\.</span><span class="sxs-lookup"><span data-stu-id="e879c-116">3\.</span></span> <span data-ttu-id="e879c-117">Aggiungere un URL `https://localhost:5001/authentication/login-callback`di **risposta** di .</span><span class="sxs-lookup"><span data-stu-id="e879c-117">Add a **Reply URL** of `https://localhost:5001/authentication/login-callback`.</span></span>

     <span data-ttu-id="e879c-118">Registrare l'ID applicazione (ID `11111111-1111-1111-1111-111111111111`client) (ad esempio, ).</span><span class="sxs-lookup"><span data-stu-id="e879c-118">Record the Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`).</span></span>

   * <span data-ttu-id="e879c-119">[Creare flussi](/azure/active-directory-b2c/tutorial-create-user-flows) &ndash; utente Creare un flusso utente di iscrizione e accesso.</span><span class="sxs-lookup"><span data-stu-id="e879c-119">[Create user flows](/azure/active-directory-b2c/tutorial-create-user-flows) &ndash; Create a sign-up and sign-in user flow.</span></span>

     <span data-ttu-id="e879c-120">Come minimo, selezionare **l'attributo** > utente `context.User.Identity.Name` Nome `LoginDisplay` **visualizzato** attestazioni applicazione per popolare l'oggetto nel componente (*Shared/LoginDisplay.razor*).</span><span class="sxs-lookup"><span data-stu-id="e879c-120">At a minimum, select the **Application claims** > **Display Name** user attribute to populate the `context.User.Identity.Name` in the `LoginDisplay` component (*Shared/LoginDisplay.razor*).</span></span>

     <span data-ttu-id="e879c-121">Registrare il nome del flusso utente di iscrizione e accesso `B2C_1_signupsignin`creato per l'app, ad esempio ).</span><span class="sxs-lookup"><span data-stu-id="e879c-121">Record the sign-up and sign-in user flow name created for the app (for example, `B2C_1_signupsignin`).</span></span>

1. <span data-ttu-id="e879c-122">Sostituire i segnaposto nel comando seguente con le informazioni registrate in precedenza ed eseguire il comando in una shell dei comandi:</span><span class="sxs-lookup"><span data-stu-id="e879c-122">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --client-id "{CLIENT ID}" --domain "{DOMAIN}" -ssp "{SIGN UP OR SIGN IN POLICY}"
   ```

   <span data-ttu-id="e879c-123">Per specificare il percorso di output, che crea una cartella di progetto se non esiste, `-o BlazorSample`includere l'opzione di output nel comando con un percorso, ad esempio ).</span><span class="sxs-lookup"><span data-stu-id="e879c-123">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="e879c-124">Il nome della cartella diventa anche parte del nome del progetto.</span><span class="sxs-lookup"><span data-stu-id="e879c-124">The folder name also becomes part of the project's name.</span></span>

## <a name="authentication-package"></a><span data-ttu-id="e879c-125">Pacchetto di autenticazione</span><span class="sxs-lookup"><span data-stu-id="e879c-125">Authentication package</span></span>

<span data-ttu-id="e879c-126">Quando viene creata un'app per usare`IndividualB2C`un account B2C individuale ( ), l'app riceve automaticamente un riferimento al pacchetto per la libreria di [autenticazione Microsoft](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span><span class="sxs-lookup"><span data-stu-id="e879c-126">When an app is created to use an Individual B2C Account (`IndividualB2C`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="e879c-127">Il pacchetto fornisce un set di primitive che consentono all'app di autenticare gli utenti e ottenere token per chiamare API protette.</span><span class="sxs-lookup"><span data-stu-id="e879c-127">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="e879c-128">Se si aggiunge l'autenticazione a un'app, aggiungi manualmente il pacchetto al file di progetto dell'app:</span><span class="sxs-lookup"><span data-stu-id="e879c-128">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="e879c-129">Sostituire `{VERSION}` nel riferimento al pacchetto precedente `Microsoft.AspNetCore.Blazor.Templates` con la <xref:blazor/get-started> versione del pacchetto illustrata nell'articolo.</span><span class="sxs-lookup"><span data-stu-id="e879c-129">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="e879c-130">Il `Microsoft.Authentication.WebAssembly.Msal` pacchetto aggiunge in `Microsoft.AspNetCore.Components.WebAssembly.Authentication` modo transitivo il pacchetto all'app.</span><span class="sxs-lookup"><span data-stu-id="e879c-130">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="e879c-131">Supporto del servizio di autenticazione</span><span class="sxs-lookup"><span data-stu-id="e879c-131">Authentication service support</span></span>

<span data-ttu-id="e879c-132">Il supporto per l'autenticazione degli utenti `AddMsalAuthentication` viene registrato `Microsoft.Authentication.WebAssembly.Msal` nel contenitore dei servizi con il metodo di estensione fornito dal pacchetto.</span><span class="sxs-lookup"><span data-stu-id="e879c-132">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="e879c-133">Questo metodo imposta tutti i servizi necessari all'app per interagire con il provider di identità (IP).</span><span class="sxs-lookup"><span data-stu-id="e879c-133">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="e879c-134">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="e879c-134">*Program.cs*:</span></span>

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

<span data-ttu-id="e879c-135">Il `AddMsalAuthentication` metodo accetta un callback per configurare i parametri necessari per autenticare un'app.</span><span class="sxs-lookup"><span data-stu-id="e879c-135">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="e879c-136">I valori necessari per la configurazione dell'app possono essere ottenuti dalla configurazione di Azure Portal AAD quando si registra l'app.</span><span class="sxs-lookup"><span data-stu-id="e879c-136">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

## <a name="access-token-scopes"></a><span data-ttu-id="e879c-137">Ambiti dei token di accessoAccess token scopes</span><span class="sxs-lookup"><span data-stu-id="e879c-137">Access token scopes</span></span>

<span data-ttu-id="e879c-138">Il Blazor modello WebAssembly non configura automaticamente l'app per richiedere un token di accesso per un'API sicura.</span><span class="sxs-lookup"><span data-stu-id="e879c-138">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="e879c-139">Per eseguire il provisioning di un token come parte del flusso di accesso, aggiungere l'ambito agli ambiti predefiniti del token di accesso: `MsalProviderOptions`</span><span class="sxs-lookup"><span data-stu-id="e879c-139">To provision a token as part of the sign-in flow, add the scope to the default access token scopes of the `MsalProviderOptions`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="e879c-140">Se il portale di Azure fornisce un URI di ambito e **l'app genera un'eccezione non gestita** quando riceve una risposta *401 Unauthorized* dall'API, provare a usare un URI di ambito che non includa lo schema e l'host.</span><span class="sxs-lookup"><span data-stu-id="e879c-140">If the Azure portal provides a scope URI and **the app throws an unhandled exception** when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="e879c-141">Ad esempio, il portale di Azure può fornire uno dei seguenti formati URI di ambito:</span><span class="sxs-lookup"><span data-stu-id="e879c-141">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="e879c-142">Fornire l'URI dell'ambito senza lo schema e l'host:</span><span class="sxs-lookup"><span data-stu-id="e879c-142">Supply the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

<span data-ttu-id="e879c-143">Per altre informazioni, vedere <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span><span class="sxs-lookup"><span data-stu-id="e879c-143">For more information, see <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span></span>

## <a name="imports-file"></a><span data-ttu-id="e879c-144">File di importazione</span><span class="sxs-lookup"><span data-stu-id="e879c-144">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="e879c-145">Pagina di indice</span><span class="sxs-lookup"><span data-stu-id="e879c-145">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="e879c-146">Componente dell'app</span><span class="sxs-lookup"><span data-stu-id="e879c-146">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="e879c-147">Componente RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="e879c-147">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="e879c-148">Componente LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="e879c-148">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="e879c-149">Componente di autenticazione</span><span class="sxs-lookup"><span data-stu-id="e879c-149">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="e879c-150">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="e879c-150">Additional resources</span></span>

* [<span data-ttu-id="e879c-151">Richiedere token di accesso aggiuntiviRequest additional access tokens</span><span class="sxs-lookup"><span data-stu-id="e879c-151">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* <xref:security/authentication/azure-ad-b2c>
* [<span data-ttu-id="e879c-152">Esercitazione: Creare un tenant di Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="e879c-152">Tutorial: Create an Azure Active Directory B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)
* [<span data-ttu-id="e879c-153">Documentazione di Microsoft Identity Platform</span><span class="sxs-lookup"><span data-stu-id="e879c-153">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
