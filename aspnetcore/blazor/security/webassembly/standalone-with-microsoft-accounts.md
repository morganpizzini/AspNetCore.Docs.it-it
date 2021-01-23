---
title: Proteggere un' Blazor WebAssembly app ASP.NET Core autonoma con account Microsoft
author: guardrex
description: Informazioni su come proteggere un' Blazor WebAssembly app autonoma ASP.NET Core con gli account Microsoft.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/27/2020
no-loc:
- appsettings.json
- ASP.NET Core Identity
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
ms.openlocfilehash: 268debd51b6828aad0bcfe917bdf95b691ac7365
ms.sourcegitcommit: da5a5bed5718a9f8db59356ef8890b4b60ced6e9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/22/2021
ms.locfileid: "98710517"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-standalone-app-with-microsoft-accounts"></a><span data-ttu-id="5f13a-103">Proteggere un' Blazor WebAssembly app ASP.NET Core autonoma con account Microsoft</span><span class="sxs-lookup"><span data-stu-id="5f13a-103">Secure an ASP.NET Core Blazor WebAssembly standalone app with Microsoft Accounts</span></span>

<span data-ttu-id="5f13a-104">Di [Javier Calvarro Nelson](https://github.com/javiercn) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="5f13a-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="5f13a-105">Questo articolo illustra come creare un' [ Blazor WebAssembly app autonoma](xref:blazor/hosting-models#blazor-webassembly) che usa [account Microsoft con Azure Active Directory (AAD)](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal) per l'autenticazione.</span><span class="sxs-lookup"><span data-stu-id="5f13a-105">This article covers how to create a [standalone Blazor WebAssembly app](xref:blazor/hosting-models#blazor-webassembly) that uses [Microsoft Accounts with Azure Active Directory (AAD)](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal) for authentication.</span></span>

<span data-ttu-id="5f13a-106">Registrare un'app AAD nell'area **Azure Active Directory**  >  **registrazioni app** del portale di Azure:</span><span class="sxs-lookup"><span data-stu-id="5f13a-106">Register an AAD app in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="5f13a-107">Specificare un **nome** per l'app (ad esempio, **Blazor account Microsoft AAD autonomi**).</span><span class="sxs-lookup"><span data-stu-id="5f13a-107">Provide a **Name** for the app (for example, **Blazor Standalone AAD Microsoft Accounts**).</span></span>
1. <span data-ttu-id="5f13a-108">In **tipi di account supportati** selezionare **account in qualsiasi directory dell'organizzazione**.</span><span class="sxs-lookup"><span data-stu-id="5f13a-108">In **Supported account types**, select **Accounts in any organizational directory**.</span></span>
1. <span data-ttu-id="5f13a-109">Impostare l'elenco a discesa **URI di reindirizzamento** sull' **applicazione a pagina singola (Spa)** e specificare l'URI di reindirizzamento seguente: `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="5f13a-109">Set the **Redirect URI** drop down to **Single-page application (SPA)** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="5f13a-110">La porta predefinita per un'app in esecuzione in Kestrel è la 5001.</span><span class="sxs-lookup"><span data-stu-id="5f13a-110">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="5f13a-111">Se l'app viene eseguita su una porta di Gheppio diversa, usare la porta dell'app.</span><span class="sxs-lookup"><span data-stu-id="5f13a-111">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="5f13a-112">Per IIS Express, la porta generata in modo casuale per l'app si trova nelle proprietà dell'app nel pannello **debug** .</span><span class="sxs-lookup"><span data-stu-id="5f13a-112">For IIS Express, the randomly generated port for the app can be found in the app's properties in the **Debug** panel.</span></span> <span data-ttu-id="5f13a-113">Poiché l'app non esiste in questo momento e la porta IIS Express non è nota, tornare a questo passaggio dopo la creazione dell'app e aggiornare l'URI di reindirizzamento.</span><span class="sxs-lookup"><span data-stu-id="5f13a-113">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="5f13a-114">Un commento viene visualizzato più avanti in questo argomento per ricordare IIS Express agli utenti di aggiornare l'URI di reindirizzamento.</span><span class="sxs-lookup"><span data-stu-id="5f13a-114">A remark appears later in this topic to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="5f13a-115">Deselezionare la  > casella di controllo autorizzazioni **Concedi amministratore per OpenID e autorizzazioni offline_access** .</span><span class="sxs-lookup"><span data-stu-id="5f13a-115">Clear the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="5f13a-116">Selezionare **Registra**.</span><span class="sxs-lookup"><span data-stu-id="5f13a-116">Select **Register**.</span></span>

<span data-ttu-id="5f13a-117">Registrare l'ID dell'applicazione (client) (ad esempio, `41451fa7-82d9-4673-8fa5-69eff5a761fd` ).</span><span class="sxs-lookup"><span data-stu-id="5f13a-117">Record the Application (client) ID (for example, `41451fa7-82d9-4673-8fa5-69eff5a761fd`).</span></span>

<span data-ttu-id="5f13a-118">Nelle configurazioni della piattaforma di **autenticazione** >  > **applicazione a pagina singola (Spa)**:</span><span class="sxs-lookup"><span data-stu-id="5f13a-118">In **Authentication** > **Platform configurations** > **Single-page application (SPA)**:</span></span>

1. <span data-ttu-id="5f13a-119">Verificare che l' **URI di reindirizzamento** di `https://localhost:{PORT}/authentication/login-callback` sia presente.</span><span class="sxs-lookup"><span data-stu-id="5f13a-119">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="5f13a-120">Per **concessione implicita**, assicurarsi che le caselle di controllo per i **token di accesso** e i **token ID** **non** siano selezionate.</span><span class="sxs-lookup"><span data-stu-id="5f13a-120">For **Implicit grant**, ensure that the check boxes for **Access tokens** and **ID tokens** are **not** selected.</span></span>
1. <span data-ttu-id="5f13a-121">Per questa esperienza sono accettabili le impostazioni predefinite rimanenti per l'app.</span><span class="sxs-lookup"><span data-stu-id="5f13a-121">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="5f13a-122">Fare clic sul pulsante **Salva**.</span><span class="sxs-lookup"><span data-stu-id="5f13a-122">Select the **Save** button.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="5f13a-123">Specificare un **nome** per l'app (ad esempio, **Blazor account Microsoft AAD autonomi**).</span><span class="sxs-lookup"><span data-stu-id="5f13a-123">Provide a **Name** for the app (for example, **Blazor Standalone AAD Microsoft Accounts**).</span></span>
1. <span data-ttu-id="5f13a-124">In **tipi di account supportati** selezionare **account in qualsiasi directory dell'organizzazione**.</span><span class="sxs-lookup"><span data-stu-id="5f13a-124">In **Supported account types**, select **Accounts in any organizational directory**.</span></span>
1. <span data-ttu-id="5f13a-125">Lasciare l'elenco a discesa **URI di reindirizzamento** impostato su **Web** e specificare l'URI di reindirizzamento seguente: `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="5f13a-125">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="5f13a-126">La porta predefinita per un'app in esecuzione in Kestrel è la 5001.</span><span class="sxs-lookup"><span data-stu-id="5f13a-126">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="5f13a-127">Se l'app viene eseguita su una porta di Gheppio diversa, usare la porta dell'app.</span><span class="sxs-lookup"><span data-stu-id="5f13a-127">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="5f13a-128">Per IIS Express, la porta generata in modo casuale per l'app si trova nelle proprietà dell'app nel pannello **debug** .</span><span class="sxs-lookup"><span data-stu-id="5f13a-128">For IIS Express, the randomly generated port for the app can be found in the app's properties in the **Debug** panel.</span></span> <span data-ttu-id="5f13a-129">Poiché l'app non esiste in questo momento e la porta IIS Express non è nota, tornare a questo passaggio dopo la creazione dell'app e aggiornare l'URI di reindirizzamento.</span><span class="sxs-lookup"><span data-stu-id="5f13a-129">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="5f13a-130">Un commento viene visualizzato più avanti in questo argomento per ricordare IIS Express agli utenti di aggiornare l'URI di reindirizzamento.</span><span class="sxs-lookup"><span data-stu-id="5f13a-130">A remark appears later in this topic to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="5f13a-131">Deselezionare la  > casella di controllo autorizzazioni **Concedi amministratore per OpenID e autorizzazioni offline_access** .</span><span class="sxs-lookup"><span data-stu-id="5f13a-131">Clear the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="5f13a-132">Selezionare **Registra**.</span><span class="sxs-lookup"><span data-stu-id="5f13a-132">Select **Register**.</span></span>

<span data-ttu-id="5f13a-133">Registrare l'ID dell'applicazione (client) (ad esempio, `41451fa7-82d9-4673-8fa5-69eff5a761fd` ).</span><span class="sxs-lookup"><span data-stu-id="5f13a-133">Record the Application (client) ID (for example, `41451fa7-82d9-4673-8fa5-69eff5a761fd`).</span></span>

<span data-ttu-id="5f13a-134">In  > **configurazioni piattaforma** di autenticazione > **Web**:</span><span class="sxs-lookup"><span data-stu-id="5f13a-134">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="5f13a-135">Verificare che l' **URI di reindirizzamento** di `https://localhost:{PORT}/authentication/login-callback` sia presente.</span><span class="sxs-lookup"><span data-stu-id="5f13a-135">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="5f13a-136">Per **concessione implicita**, selezionare le caselle di controllo per i token di **accesso** e i **token ID**.</span><span class="sxs-lookup"><span data-stu-id="5f13a-136">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="5f13a-137">Per questa esperienza sono accettabili le impostazioni predefinite rimanenti per l'app.</span><span class="sxs-lookup"><span data-stu-id="5f13a-137">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="5f13a-138">Fare clic sul pulsante **Salva**.</span><span class="sxs-lookup"><span data-stu-id="5f13a-138">Select the **Save** button.</span></span>

::: moniker-end

<span data-ttu-id="5f13a-139">Creare l'app.</span><span class="sxs-lookup"><span data-stu-id="5f13a-139">Create the app.</span></span> <span data-ttu-id="5f13a-140">Sostituire i segnaposto nel comando seguente con le informazioni registrate in precedenza ed eseguire il comando seguente in una shell dei comandi:</span><span class="sxs-lookup"><span data-stu-id="5f13a-140">Replace the placeholders in the following command with the information recorded earlier and execute the following command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" --tenant-id "common" -o {APP NAME}
```

| <span data-ttu-id="5f13a-141">Segnaposto</span><span class="sxs-lookup"><span data-stu-id="5f13a-141">Placeholder</span></span>   | <span data-ttu-id="5f13a-142">Nome portale di Azure</span><span class="sxs-lookup"><span data-stu-id="5f13a-142">Azure portal name</span></span>       | <span data-ttu-id="5f13a-143">Esempio</span><span class="sxs-lookup"><span data-stu-id="5f13a-143">Example</span></span>                                |
| ------------- | ----------------------- | -------------------------------------- |
| `{APP NAME}`  | &mdash;                 | `BlazorSample`                         |
| `{CLIENT ID}` | <span data-ttu-id="5f13a-144">ID applicazione (client)</span><span class="sxs-lookup"><span data-stu-id="5f13a-144">Application (client) ID</span></span> | `41451fa7-82d9-4673-8fa5-69eff5a761fd` |

<span data-ttu-id="5f13a-145">Il percorso di output specificato con l'opzione `-o|--output` consente di creare una cartella di progetto se non esiste e viene incluso nel nome dell'app.</span><span class="sxs-lookup"><span data-stu-id="5f13a-145">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

> [!NOTE]
> <span data-ttu-id="5f13a-146">Nell'portale di Azure l' **URI di reindirizzamento** della configurazione della piattaforma dell'app è configurato per la porta 5001 per le app eseguite nel server gheppio con le impostazioni predefinite.</span><span class="sxs-lookup"><span data-stu-id="5f13a-146">In the Azure portal, the app's platform configuration **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="5f13a-147">Se l'app viene eseguita su una porta IIS Express casuale, la porta per l'app si trova nelle proprietà dell'app nel pannello **debug** .</span><span class="sxs-lookup"><span data-stu-id="5f13a-147">If the app is run on a random IIS Express port, the port for the app can be found in the app's properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="5f13a-148">Se la porta non è stata configurata in precedenza con la porta nota dell'app, tornare alla registrazione dell'app nel portale di Azure e aggiornare l'URI di reindirizzamento con la porta corretta.</span><span class="sxs-lookup"><span data-stu-id="5f13a-148">If the port wasn't configured earlier with the app's known port, return to the app's registration in the Azure portal and update the redirect URI with the correct port.</span></span>

::: moniker range=">= aspnetcore-5.0"

[!INCLUDE[](~/blazor/includes/security/additional-scopes-standalone-nonAAD.md)]

::: moniker-end

<span data-ttu-id="5f13a-149">Dopo aver creato l'app, dovrebbe essere possibile:</span><span class="sxs-lookup"><span data-stu-id="5f13a-149">After creating the app, you should be able to:</span></span>

* <span data-ttu-id="5f13a-150">Accedere all'app usando un account Microsoft.</span><span class="sxs-lookup"><span data-stu-id="5f13a-150">Log into the app using a Microsoft account.</span></span>
* <span data-ttu-id="5f13a-151">Richiedere i token di accesso per le API Microsoft.</span><span class="sxs-lookup"><span data-stu-id="5f13a-151">Request access tokens for Microsoft APIs.</span></span> <span data-ttu-id="5f13a-152">Per altre informazioni, vedere:</span><span class="sxs-lookup"><span data-stu-id="5f13a-152">For more information, see:</span></span>
  * [<span data-ttu-id="5f13a-153">Ambiti del token di accesso</span><span class="sxs-lookup"><span data-stu-id="5f13a-153">Access token scopes</span></span>](#access-token-scopes)
  * <span data-ttu-id="5f13a-154">[Guida introduttiva: configurare un'applicazione per esporre le API Web](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span><span class="sxs-lookup"><span data-stu-id="5f13a-154">[Quickstart: Configure an application to expose web APIs](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span></span>

## <a name="authentication-package"></a><span data-ttu-id="5f13a-155">Pacchetto di autenticazione</span><span class="sxs-lookup"><span data-stu-id="5f13a-155">Authentication package</span></span>

<span data-ttu-id="5f13a-156">Quando viene creata un'app per usare gli account aziendali o dell'Istituto di istruzione ( `SingleOrg` ), l'app riceve automaticamente un riferimento al pacchetto per [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ( [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) ).</span><span class="sxs-lookup"><span data-stu-id="5f13a-156">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)).</span></span> <span data-ttu-id="5f13a-157">Il pacchetto fornisce un set di primitive che consentono all'app di autenticare gli utenti e ottenere i token per chiamare le API protette.</span><span class="sxs-lookup"><span data-stu-id="5f13a-157">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="5f13a-158">Se si aggiunge l'autenticazione a un'app, aggiungere manualmente il pacchetto al file di progetto dell'app:</span><span class="sxs-lookup"><span data-stu-id="5f13a-158">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="{VERSION}" />
```

<span data-ttu-id="5f13a-159">Per il segnaposto `{VERSION}` , la versione stabile più recente del pacchetto che corrisponde alla versione del Framework condiviso dell'app è disponibile nella cronologia delle **versioni** del pacchetto in [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal).</span><span class="sxs-lookup"><span data-stu-id="5f13a-159">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal).</span></span>

<span data-ttu-id="5f13a-160">Il [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) pacchetto aggiunge il [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) pacchetto all'app in modo transitivo.</span><span class="sxs-lookup"><span data-stu-id="5f13a-160">The [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package transitively adds the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="5f13a-161">Supporto del servizio di autenticazione</span><span class="sxs-lookup"><span data-stu-id="5f13a-161">Authentication service support</span></span>

<span data-ttu-id="5f13a-162">Il supporto per l'autenticazione degli utenti viene registrato nel contenitore del servizio con il <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> metodo di estensione fornito dal [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) pacchetto.</span><span class="sxs-lookup"><span data-stu-id="5f13a-162">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> extension method provided by the [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package.</span></span> <span data-ttu-id="5f13a-163">Questo metodo configura tutti i servizi necessari per l'interazione dell'app con il Identity provider (IP).</span><span class="sxs-lookup"><span data-stu-id="5f13a-163">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="5f13a-164">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="5f13a-164">`Program.cs`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
});
```

<span data-ttu-id="5f13a-165">Il <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> metodo accetta un callback per configurare i parametri necessari per autenticare un'app.</span><span class="sxs-lookup"><span data-stu-id="5f13a-165">The <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="5f13a-166">Quando si registra l'app, è possibile ottenere i valori necessari per la configurazione dell'app dalla configurazione di AAD.</span><span class="sxs-lookup"><span data-stu-id="5f13a-166">The values required for configuring the app can be obtained from the AAD configuration when you register the app.</span></span>

<span data-ttu-id="5f13a-167">La configurazione viene fornita dal `wwwroot/appsettings.json` file:</span><span class="sxs-lookup"><span data-stu-id="5f13a-167">Configuration is supplied by the `wwwroot/appsettings.json` file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/common",
    "ClientId": "{CLIENT ID}",
    "ValidateAuthority": true
  }
}
```

<span data-ttu-id="5f13a-168">Esempio:</span><span class="sxs-lookup"><span data-stu-id="5f13a-168">Example:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/common",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "ValidateAuthority": true
  }
}
```

## <a name="access-token-scopes"></a><span data-ttu-id="5f13a-169">Ambiti del token di accesso</span><span class="sxs-lookup"><span data-stu-id="5f13a-169">Access token scopes</span></span>

<span data-ttu-id="5f13a-170">Il Blazor WebAssembly modello non configura automaticamente l'app per richiedere un token di accesso per un'API protetta.</span><span class="sxs-lookup"><span data-stu-id="5f13a-170">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="5f13a-171">Per eseguire il provisioning di un token di accesso come parte del flusso di accesso, aggiungere l'ambito agli ambiti dei token di accesso predefiniti di <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions> :</span><span class="sxs-lookup"><span data-stu-id="5f13a-171">To provision an access token as part of the sign-in flow, add the scope to the default access token scopes of the <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions>:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="5f13a-172">Specificare gli ambiti aggiuntivi con `AdditionalScopesToConsent` :</span><span class="sxs-lookup"><span data-stu-id="5f13a-172">Specify additional scopes with `AdditionalScopesToConsent`:</span></span>

```csharp
options.ProviderOptions.AdditionalScopesToConsent.Add("{ADDITIONAL SCOPE URI}");
```

::: moniker range="< aspnetcore-5.0"

[!INCLUDE[](~/blazor/includes/security/azure-scope-3x.md)]

::: moniker-end

<span data-ttu-id="5f13a-173">Per ulteriori informazioni, vedere le sezioni seguenti dell'articolo *scenari aggiuntivi* :</span><span class="sxs-lookup"><span data-stu-id="5f13a-173">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="5f13a-174">Richiedere token di accesso aggiuntivi</span><span class="sxs-lookup"><span data-stu-id="5f13a-174">Request additional access tokens</span></span>](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="5f13a-175">Connetti token alle richieste in uscita</span><span class="sxs-lookup"><span data-stu-id="5f13a-175">Attach tokens to outgoing requests</span></span>](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

::: moniker range=">= aspnetcore-5.0"

## <a name="login-mode"></a><span data-ttu-id="5f13a-176">Modalità di accesso</span><span class="sxs-lookup"><span data-stu-id="5f13a-176">Login mode</span></span>

[!INCLUDE[](~/blazor/includes/security/msal-login-mode.md)]

::: moniker-end

## <a name="imports-file"></a><span data-ttu-id="5f13a-177">Importa file</span><span class="sxs-lookup"><span data-stu-id="5f13a-177">Imports file</span></span>

[!INCLUDE[](~/blazor/includes/security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="5f13a-178">Pagina di indice</span><span class="sxs-lookup"><span data-stu-id="5f13a-178">Index page</span></span>

[!INCLUDE[](~/blazor/includes/security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="5f13a-179">Componente app</span><span class="sxs-lookup"><span data-stu-id="5f13a-179">App component</span></span>

[!INCLUDE[](~/blazor/includes/security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="5f13a-180">Componente RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="5f13a-180">RedirectToLogin component</span></span>

[!INCLUDE[](~/blazor/includes/security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="5f13a-181">Componente LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="5f13a-181">LoginDisplay component</span></span>

[!INCLUDE[](~/blazor/includes/security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="5f13a-182">Componente di autenticazione</span><span class="sxs-lookup"><span data-stu-id="5f13a-182">Authentication component</span></span>

[!INCLUDE[](~/blazor/includes/security/authentication-component.md)]

[!INCLUDE[](~/blazor/includes/security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="5f13a-183">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="5f13a-183">Additional resources</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="5f13a-184">Compilare una versione personalizzata della libreria JavaScript Authentication. MSAL</span><span class="sxs-lookup"><span data-stu-id="5f13a-184">Build a custom version of the Authentication.MSAL JavaScript library</span></span>](xref:blazor/security/webassembly/additional-scenarios#build-a-custom-version-of-the-authenticationmsal-javascript-library)
* [<span data-ttu-id="5f13a-185">Richieste API Web non autenticate o non autorizzate in un'app con un client predefinito sicuro</span><span class="sxs-lookup"><span data-stu-id="5f13a-185">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:blazor/security/webassembly/aad-groups-roles>
* [<span data-ttu-id="5f13a-186">Avvio rapido: Registrare un'applicazione con Microsoft Identity Platform</span><span class="sxs-lookup"><span data-stu-id="5f13a-186">Quickstart: Register an application with the Microsoft identity platform</span></span>](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal)
* [<span data-ttu-id="5f13a-187">Avvio rapido: Configurare un'applicazione per l'esposizione di API Web</span><span class="sxs-lookup"><span data-stu-id="5f13a-187">Quickstart: Configure an application to expose web APIs</span></span>](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)
