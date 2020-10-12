---
title: Proteggere un' Blazor WebAssembly app autonoma ASP.NET Core con Azure Active Directory
author: guardrex
description: Informazioni su come proteggere un' Blazor WebAssembly app autonoma ASP.NET Core con Azure Active Directory.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: devx-track-csharp, mvc
ms.date: 10/08/2020
no-loc:
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
uid: blazor/security/webassembly/standalone-with-azure-active-directory
ms.openlocfilehash: b44c5372d694dcc16ff66e24233171e3320d7294
ms.sourcegitcommit: daa9ccf580df531254da9dce8593441ac963c674
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91900902"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-standalone-app-with-azure-active-directory"></a><span data-ttu-id="17615-103">Proteggere un' Blazor WebAssembly app autonoma ASP.NET Core con Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="17615-103">Secure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory</span></span>

<span data-ttu-id="17615-104">Di [Javier Calvarro Nelson](https://github.com/javiercn) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="17615-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="17615-105">Per creare un' [ Blazor WebAssembly app autonoma](xref:blazor/hosting-models#blazor-webassembly) che usa [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) per l'autenticazione:</span><span class="sxs-lookup"><span data-stu-id="17615-105">To create a [standalone Blazor WebAssembly app](xref:blazor/hosting-models#blazor-webassembly) that uses [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) for authentication:</span></span>

<span data-ttu-id="17615-106">[Creare un'applicazione Web e un tenant di AAD](/azure/active-directory/develop/v2-overview):</span><span class="sxs-lookup"><span data-stu-id="17615-106">[Create an AAD tenant and web application](/azure/active-directory/develop/v2-overview):</span></span>

<span data-ttu-id="17615-107">Registrare un'app AAD nell'area **Azure Active Directory**  >  **registrazioni app** del portale di Azure:</span><span class="sxs-lookup"><span data-stu-id="17615-107">Register a AAD app in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="17615-108">Specificare un **nome** per l'app, ad esempio \*\* Blazor AAD autonomo\*\*.</span><span class="sxs-lookup"><span data-stu-id="17615-108">Provide a **Name** for the app (for example, **Blazor Standalone AAD**).</span></span>
1. <span data-ttu-id="17615-109">Scegliere i **tipi di conto supportati**.</span><span class="sxs-lookup"><span data-stu-id="17615-109">Choose a **Supported account types**.</span></span> <span data-ttu-id="17615-110">È possibile selezionare gli **account in questa directory organizzativa solo** per questa esperienza.</span><span class="sxs-lookup"><span data-stu-id="17615-110">You may select **Accounts in this organizational directory only** for this experience.</span></span>
1. <span data-ttu-id="17615-111">Impostare l'elenco a discesa **URI di reindirizzamento** sull' **applicazione a pagina singola (Spa)** e specificare l'URI di reindirizzamento seguente: `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="17615-111">Set the **Redirect URI** drop down to **Single-page application (SPA)** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="17615-112">La porta predefinita per un'app in esecuzione su gheppio è 5001.</span><span class="sxs-lookup"><span data-stu-id="17615-112">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="17615-113">Se l'app viene eseguita su una porta di Gheppio diversa, usare la porta dell'app.</span><span class="sxs-lookup"><span data-stu-id="17615-113">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="17615-114">Per IIS Express, la porta generata in modo casuale per l'app si trova nelle proprietà dell'app nel pannello **debug** .</span><span class="sxs-lookup"><span data-stu-id="17615-114">For IIS Express, the randomly generated port for the app can be found in the app's properties in the **Debug** panel.</span></span> <span data-ttu-id="17615-115">Poiché l'app non esiste in questo momento e la porta IIS Express non è nota, tornare a questo passaggio dopo la creazione dell'app e aggiornare l'URI di reindirizzamento.</span><span class="sxs-lookup"><span data-stu-id="17615-115">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="17615-116">Un commento viene visualizzato più avanti in questo argomento per ricordare IIS Express agli utenti di aggiornare l'URI di reindirizzamento.</span><span class="sxs-lookup"><span data-stu-id="17615-116">A remark appears later in this topic to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="17615-117">Deselezionare la **Permissions** > casella di controllo autorizzazioni **Concedi amministratore per OpenID e autorizzazioni offline_access** .</span><span class="sxs-lookup"><span data-stu-id="17615-117">Clear the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="17615-118">Selezionare **Registra**.</span><span class="sxs-lookup"><span data-stu-id="17615-118">Select **Register**.</span></span>

<span data-ttu-id="17615-119">Registrare le seguenti informazioni:</span><span class="sxs-lookup"><span data-stu-id="17615-119">Record the following information:</span></span>

* <span data-ttu-id="17615-120">ID applicazione (client) (ad esempio, `41451fa7-82d9-4673-8fa5-69eff5a761fd` )</span><span class="sxs-lookup"><span data-stu-id="17615-120">Application (client) ID (for example, `41451fa7-82d9-4673-8fa5-69eff5a761fd`)</span></span>
* <span data-ttu-id="17615-121">ID directory (tenant) (ad esempio, `e86c78e2-8bb4-4c41-aefd-918e0565a45e` )</span><span class="sxs-lookup"><span data-stu-id="17615-121">Directory (tenant) ID (for example, `e86c78e2-8bb4-4c41-aefd-918e0565a45e`)</span></span>

<span data-ttu-id="17615-122">Nelle configurazioni della piattaforma di **autenticazione** > **Platform configurations** > **applicazione a pagina singola (Spa)**:</span><span class="sxs-lookup"><span data-stu-id="17615-122">In **Authentication** > **Platform configurations** > **Single-page application (SPA)**:</span></span>

1. <span data-ttu-id="17615-123">Verificare che l' **URI di reindirizzamento** di `https://localhost:{PORT}/authentication/login-callback` sia presente.</span><span class="sxs-lookup"><span data-stu-id="17615-123">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="17615-124">Per **concessione implicita**, assicurarsi che le caselle di controllo per i **token di accesso** e i **token ID** **non** siano selezionate.</span><span class="sxs-lookup"><span data-stu-id="17615-124">For **Implicit grant**, ensure that the check boxes for **Access tokens** and **ID tokens** are **not** selected.</span></span>
1. <span data-ttu-id="17615-125">Per questa esperienza sono accettabili le impostazioni predefinite rimanenti per l'app.</span><span class="sxs-lookup"><span data-stu-id="17615-125">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="17615-126">Fare clic sul pulsante **Salva**.</span><span class="sxs-lookup"><span data-stu-id="17615-126">Select the **Save** button.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="17615-127">Specificare un **nome** per l'app, ad esempio \*\* Blazor AAD autonomo\*\*.</span><span class="sxs-lookup"><span data-stu-id="17615-127">Provide a **Name** for the app (for example, **Blazor Standalone AAD**).</span></span>
1. <span data-ttu-id="17615-128">Scegliere i **tipi di conto supportati**.</span><span class="sxs-lookup"><span data-stu-id="17615-128">Choose a **Supported account types**.</span></span> <span data-ttu-id="17615-129">È possibile selezionare gli **account in questa directory organizzativa solo** per questa esperienza.</span><span class="sxs-lookup"><span data-stu-id="17615-129">You may select **Accounts in this organizational directory only** for this experience.</span></span>
1. <span data-ttu-id="17615-130">Lasciare l'elenco a discesa **URI di reindirizzamento** impostato su **Web** e specificare l'URI di reindirizzamento seguente: `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="17615-130">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="17615-131">La porta predefinita per un'app in esecuzione su gheppio è 5001.</span><span class="sxs-lookup"><span data-stu-id="17615-131">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="17615-132">Se l'app viene eseguita su una porta di Gheppio diversa, usare la porta dell'app.</span><span class="sxs-lookup"><span data-stu-id="17615-132">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="17615-133">Per IIS Express, la porta generata in modo casuale per l'app si trova nelle proprietà dell'app nel pannello **debug** .</span><span class="sxs-lookup"><span data-stu-id="17615-133">For IIS Express, the randomly generated port for the app can be found in the app's properties in the **Debug** panel.</span></span> <span data-ttu-id="17615-134">Poiché l'app non esiste in questo momento e la porta IIS Express non è nota, tornare a questo passaggio dopo la creazione dell'app e aggiornare l'URI di reindirizzamento.</span><span class="sxs-lookup"><span data-stu-id="17615-134">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="17615-135">Un commento viene visualizzato più avanti in questo argomento per ricordare IIS Express agli utenti di aggiornare l'URI di reindirizzamento.</span><span class="sxs-lookup"><span data-stu-id="17615-135">A remark appears later in this topic to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="17615-136">Deselezionare la **Permissions** > casella di controllo autorizzazioni **Concedi amministratore per OpenID e autorizzazioni offline_access** .</span><span class="sxs-lookup"><span data-stu-id="17615-136">Clear the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="17615-137">Selezionare **Registra**.</span><span class="sxs-lookup"><span data-stu-id="17615-137">Select **Register**.</span></span>

<span data-ttu-id="17615-138">Registrare le seguenti informazioni:</span><span class="sxs-lookup"><span data-stu-id="17615-138">Record the following information:</span></span>

* <span data-ttu-id="17615-139">ID applicazione (client) (ad esempio, `41451fa7-82d9-4673-8fa5-69eff5a761fd` )</span><span class="sxs-lookup"><span data-stu-id="17615-139">Application (client) ID (for example, `41451fa7-82d9-4673-8fa5-69eff5a761fd`)</span></span>
* <span data-ttu-id="17615-140">ID directory (tenant) (ad esempio, `e86c78e2-8bb4-4c41-aefd-918e0565a45e` )</span><span class="sxs-lookup"><span data-stu-id="17615-140">Directory (tenant) ID (for example, `e86c78e2-8bb4-4c41-aefd-918e0565a45e`)</span></span>

<span data-ttu-id="17615-141">In **Authentication** > **configurazioni piattaforma** di autenticazione > **Web**:</span><span class="sxs-lookup"><span data-stu-id="17615-141">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="17615-142">Verificare che l' **URI di reindirizzamento** di `https://localhost:{PORT}/authentication/login-callback` sia presente.</span><span class="sxs-lookup"><span data-stu-id="17615-142">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="17615-143">Per **concessione implicita**, selezionare le caselle di controllo per i token di **accesso** e i **token ID**.</span><span class="sxs-lookup"><span data-stu-id="17615-143">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="17615-144">Per questa esperienza sono accettabili le impostazioni predefinite rimanenti per l'app.</span><span class="sxs-lookup"><span data-stu-id="17615-144">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="17615-145">Fare clic sul pulsante **Salva**.</span><span class="sxs-lookup"><span data-stu-id="17615-145">Select the **Save** button.</span></span>

::: moniker-end

<span data-ttu-id="17615-146">Creare l'app in una cartella vuota.</span><span class="sxs-lookup"><span data-stu-id="17615-146">Create the app in an empty folder.</span></span> <span data-ttu-id="17615-147">Sostituire i segnaposto nel comando seguente con le informazioni registrate in precedenza ed eseguire il comando in una shell dei comandi:</span><span class="sxs-lookup"><span data-stu-id="17615-147">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" -o {APP NAME} --tenant-id "{TENANT ID}"
```

| <span data-ttu-id="17615-148">Segnaposto</span><span class="sxs-lookup"><span data-stu-id="17615-148">Placeholder</span></span>   | <span data-ttu-id="17615-149">Nome portale di Azure</span><span class="sxs-lookup"><span data-stu-id="17615-149">Azure portal name</span></span>       | <span data-ttu-id="17615-150">Esempio</span><span class="sxs-lookup"><span data-stu-id="17615-150">Example</span></span>                                |
| ------------- | ----------------------- | -------------------------------------- |
| `{APP NAME}`  | &mdash;                 | `BlazorSample`                         |
| `{CLIENT ID}` | <span data-ttu-id="17615-151">ID applicazione (client)</span><span class="sxs-lookup"><span data-stu-id="17615-151">Application (client) ID</span></span> | `41451fa7-82d9-4673-8fa5-69eff5a761fd` |
| `{TENANT ID}` | <span data-ttu-id="17615-152">ID directory (tenant)</span><span class="sxs-lookup"><span data-stu-id="17615-152">Directory (tenant) ID</span></span>   | `e86c78e2-8bb4-4c41-aefd-918e0565a45e` |

<span data-ttu-id="17615-153">Il percorso di output specificato con l' `-o|--output` opzione Crea una cartella di progetto se non esiste e diventa parte del nome dell'app.</span><span class="sxs-lookup"><span data-stu-id="17615-153">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

> [!NOTE]
> <span data-ttu-id="17615-154">Nell'portale di Azure l' **URI di reindirizzamento** della configurazione della piattaforma dell'app è configurato per la porta 5001 per le app eseguite nel server gheppio con le impostazioni predefinite.</span><span class="sxs-lookup"><span data-stu-id="17615-154">In the Azure portal, the app's platform configuration **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="17615-155">Se l'app viene eseguita su una porta IIS Express casuale, la porta per l'app si trova nelle proprietà dell'app nel pannello **debug** .</span><span class="sxs-lookup"><span data-stu-id="17615-155">If the app is run on a random IIS Express port, the port for the app can be found in the app's properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="17615-156">Se la porta non è stata configurata in precedenza con la porta nota dell'app, tornare alla registrazione dell'app nel portale di Azure e aggiornare l'URI di reindirizzamento con la porta corretta.</span><span class="sxs-lookup"><span data-stu-id="17615-156">If the port wasn't configured earlier with the app's known port, return to the app's registration in the Azure portal and update the redirect URI with the correct port.</span></span>

::: moniker range=">= aspnetcore-5.0"

[!INCLUDE[](~/includes/blazor-security/additional-scopes-standalone-AAD.md)]

::: moniker-end

<span data-ttu-id="17615-157">Dopo aver creato l'app, dovrebbe essere possibile:</span><span class="sxs-lookup"><span data-stu-id="17615-157">After creating the app, you should be able to:</span></span>

* <span data-ttu-id="17615-158">Accedere all'app usando un account utente di AAD.</span><span class="sxs-lookup"><span data-stu-id="17615-158">Log into the app using an AAD user account.</span></span>
* <span data-ttu-id="17615-159">Richiedere i token di accesso per le API Microsoft.</span><span class="sxs-lookup"><span data-stu-id="17615-159">Request access tokens for Microsoft APIs.</span></span> <span data-ttu-id="17615-160">Per altre informazioni, vedere:</span><span class="sxs-lookup"><span data-stu-id="17615-160">For more information, see:</span></span>
  * [<span data-ttu-id="17615-161">Ambiti del token di accesso</span><span class="sxs-lookup"><span data-stu-id="17615-161">Access token scopes</span></span>](#access-token-scopes)
  * <span data-ttu-id="17615-162">[Guida introduttiva: configurare un'applicazione per esporre le API Web](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span><span class="sxs-lookup"><span data-stu-id="17615-162">[Quickstart: Configure an application to expose web APIs](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span></span>

## <a name="authentication-package"></a><span data-ttu-id="17615-163">Pacchetto di autenticazione</span><span class="sxs-lookup"><span data-stu-id="17615-163">Authentication package</span></span>

<span data-ttu-id="17615-164">Quando viene creata un'app per usare gli account aziendali o dell'Istituto di istruzione ( `SingleOrg` ), l'app riceve automaticamente un riferimento al pacchetto per [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ( [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) ).</span><span class="sxs-lookup"><span data-stu-id="17615-164">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)).</span></span> <span data-ttu-id="17615-165">Il pacchetto fornisce un set di primitive che consentono all'app di autenticare gli utenti e ottenere i token per chiamare le API protette.</span><span class="sxs-lookup"><span data-stu-id="17615-165">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="17615-166">Se si aggiunge l'autenticazione a un'app, aggiungere manualmente il pacchetto al file di progetto dell'app:</span><span class="sxs-lookup"><span data-stu-id="17615-166">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="{VERSION}" />
```

<span data-ttu-id="17615-167">Per il segnaposto `{VERSION}` , la versione stabile più recente del pacchetto che corrisponde alla versione del Framework condiviso dell'app è disponibile nella cronologia delle **versioni** del pacchetto in [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal).</span><span class="sxs-lookup"><span data-stu-id="17615-167">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal).</span></span>

<span data-ttu-id="17615-168">Il [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) pacchetto aggiunge il [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) pacchetto all'app in modo transitivo.</span><span class="sxs-lookup"><span data-stu-id="17615-168">The [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package transitively adds the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="17615-169">Supporto del servizio di autenticazione</span><span class="sxs-lookup"><span data-stu-id="17615-169">Authentication service support</span></span>

<span data-ttu-id="17615-170">Il supporto per l'autenticazione degli utenti viene registrato nel contenitore del servizio con il <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> metodo di estensione fornito dal [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) pacchetto.</span><span class="sxs-lookup"><span data-stu-id="17615-170">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> extension method provided by the [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package.</span></span> <span data-ttu-id="17615-171">Questo metodo configura i servizi necessari per l'interazione dell'app con il Identity provider (IP).</span><span class="sxs-lookup"><span data-stu-id="17615-171">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="17615-172">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="17615-172">`Program.cs`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
});
```

<span data-ttu-id="17615-173">Il <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> metodo accetta un callback per configurare i parametri necessari per autenticare un'app.</span><span class="sxs-lookup"><span data-stu-id="17615-173">The <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="17615-174">Quando si registra l'app, è possibile ottenere i valori necessari per la configurazione dell'app dalla configurazione di AAD.</span><span class="sxs-lookup"><span data-stu-id="17615-174">The values required for configuring the app can be obtained from the AAD configuration when you register the app.</span></span>

<span data-ttu-id="17615-175">La configurazione viene fornita dal `wwwroot/appsettings.json` file:</span><span class="sxs-lookup"><span data-stu-id="17615-175">Configuration is supplied by the `wwwroot/appsettings.json` file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/{TENANT ID}",
    "ClientId": "{CLIENT ID}",
    "ValidateAuthority": true
  }
}
```

<span data-ttu-id="17615-176">Esempio:</span><span class="sxs-lookup"><span data-stu-id="17615-176">Example:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/e86c78e2-...-918e0565a45e",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "ValidateAuthority": true
  }
}
```

## <a name="access-token-scopes"></a><span data-ttu-id="17615-177">Ambiti del token di accesso</span><span class="sxs-lookup"><span data-stu-id="17615-177">Access token scopes</span></span>

<span data-ttu-id="17615-178">Il Blazor WebAssembly modello non configura automaticamente l'app per richiedere un token di accesso per un'API protetta.</span><span class="sxs-lookup"><span data-stu-id="17615-178">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="17615-179">Per eseguire il provisioning di un token di accesso come parte del flusso di accesso, aggiungere l'ambito agli ambiti dei token di accesso predefiniti di <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions> :</span><span class="sxs-lookup"><span data-stu-id="17615-179">To provision an access token as part of the sign-in flow, add the scope to the default access token scopes of the <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions>:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="17615-180">Specificare gli ambiti aggiuntivi con `AdditionalScopesToConsent` :</span><span class="sxs-lookup"><span data-stu-id="17615-180">Specify additional scopes with `AdditionalScopesToConsent`:</span></span>

```csharp
options.ProviderOptions.AdditionalScopesToConsent.Add("{ADDITIONAL SCOPE URI}");
```

::: moniker range="< aspnetcore-5.0"

[!INCLUDE[](~/includes/blazor-security/azure-scope-3x.md)]

::: moniker-end

<span data-ttu-id="17615-181">Per ulteriori informazioni, vedere le sezioni seguenti dell'articolo *scenari aggiuntivi* :</span><span class="sxs-lookup"><span data-stu-id="17615-181">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="17615-182">Richiedere token di accesso aggiuntivi</span><span class="sxs-lookup"><span data-stu-id="17615-182">Request additional access tokens</span></span>](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="17615-183">Connetti token alle richieste in uscita</span><span class="sxs-lookup"><span data-stu-id="17615-183">Attach tokens to outgoing requests</span></span>](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

::: moniker range=">= aspnetcore-5.0"

## <a name="login-mode"></a><span data-ttu-id="17615-184">Modalità di accesso</span><span class="sxs-lookup"><span data-stu-id="17615-184">Login mode</span></span>

[!INCLUDE[](~/includes/blazor-security/msal-login-mode.md)]

::: moniker-end

## <a name="imports-file"></a><span data-ttu-id="17615-185">Importa file</span><span class="sxs-lookup"><span data-stu-id="17615-185">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="17615-186">Pagina di indice</span><span class="sxs-lookup"><span data-stu-id="17615-186">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="17615-187">Componente app</span><span class="sxs-lookup"><span data-stu-id="17615-187">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="17615-188">Componente RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="17615-188">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="17615-189">Componente LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="17615-189">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="17615-190">Componente di autenticazione</span><span class="sxs-lookup"><span data-stu-id="17615-190">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="17615-191">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="17615-191">Additional resources</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="17615-192">Richieste API Web non autenticate o non autorizzate in un'app con un client predefinito sicuro</span><span class="sxs-lookup"><span data-stu-id="17615-192">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:blazor/security/webassembly/aad-groups-roles>
* <xref:security/authentication/azure-active-directory/index>
* [<span data-ttu-id="17615-193">Documentazione di Microsoft Identity Platform</span><span class="sxs-lookup"><span data-stu-id="17615-193">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
