---
title: Proteggere un'app Blazor ASP.NET Core webassembly autonoma con la libreria di autenticazione
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
uid: security/blazor/webassembly/standalone-with-authentication-library
ms.openlocfilehash: 6907a1213a6a9089e2aed885093c2fd38f972ad0
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82768052"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-standalone-app-with-the-authentication-library"></a><span data-ttu-id="6bd34-102">Proteggere un'app Blazor ASP.NET Core webassembly autonoma con la libreria di autenticazione</span><span class="sxs-lookup"><span data-stu-id="6bd34-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with the Authentication library</span></span>

<span data-ttu-id="6bd34-103">Di [Javier Calvarro Nelson](https://github.com/javiercn) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="6bd34-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="6bd34-104">*Per Azure Active Directory (AAD) e Azure Active Directory B2C (AAD B2C), non seguire le istruzioni riportate in questo argomento. Vedere gli argomenti di AAD e AAD B2C in questo nodo Sommario.*</span><span class="sxs-lookup"><span data-stu-id="6bd34-104">*For Azure Active Directory (AAD) and Azure Active Directory B2C (AAD B2C), don't follow the guidance in this topic. See the AAD and AAD B2C topics in this table of contents node.*</span></span>

<span data-ttu-id="6bd34-105">Per creare un' Blazor app webassembly autonoma che `Microsoft.AspNetCore.Components.WebAssembly.Authentication` usa la libreria, eseguire il comando seguente in una shell dei comandi:</span><span class="sxs-lookup"><span data-stu-id="6bd34-105">To create a Blazor WebAssembly standalone app that uses `Microsoft.AspNetCore.Components.WebAssembly.Authentication` library, execute the following command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au Individual
```

<span data-ttu-id="6bd34-106">Per specificare il percorso di output, che crea una cartella di progetto, se non esiste, includere l'opzione di output nel comando con un percorso (ad `-o BlazorSample`esempio,).</span><span class="sxs-lookup"><span data-stu-id="6bd34-106">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="6bd34-107">Il nome della cartella diventa anche parte del nome del progetto.</span><span class="sxs-lookup"><span data-stu-id="6bd34-107">The folder name also becomes part of the project's name.</span></span>

<span data-ttu-id="6bd34-108">In Visual Studio [creare un' Blazor app webassembly](xref:blazor/get-started).</span><span class="sxs-lookup"><span data-stu-id="6bd34-108">In Visual Studio, [create a Blazor WebAssembly app](xref:blazor/get-started).</span></span> <span data-ttu-id="6bd34-109">Impostare l' **autenticazione** per **singoli account utente** con l'opzione **Archivia account utente in-app** .</span><span class="sxs-lookup"><span data-stu-id="6bd34-109">Set **Authentication** to **Individual User Accounts** with the **Store user accounts in-app** option.</span></span>

## <a name="authentication-package"></a><span data-ttu-id="6bd34-110">Pacchetto di autenticazione</span><span class="sxs-lookup"><span data-stu-id="6bd34-110">Authentication package</span></span>

<span data-ttu-id="6bd34-111">Quando si crea un'app per usare account utente singoli, l'app riceve automaticamente un riferimento al pacchetto per `Microsoft.AspNetCore.Components.WebAssembly.Authentication` il pacchetto nel file di progetto dell'app.</span><span class="sxs-lookup"><span data-stu-id="6bd34-111">When an app is created to use Individual User Accounts, the app automatically receives a package reference for the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package in the app's project file.</span></span> <span data-ttu-id="6bd34-112">Il pacchetto fornisce un set di primitive che consentono all'app di autenticare gli utenti e ottenere i token per chiamare le API protette.</span><span class="sxs-lookup"><span data-stu-id="6bd34-112">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="6bd34-113">Se si aggiunge l'autenticazione a un'app, aggiungere manualmente il pacchetto al file di progetto dell'app:</span><span class="sxs-lookup"><span data-stu-id="6bd34-113">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference 
    Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
    Version="{VERSION}" />
```

<span data-ttu-id="6bd34-114">Sostituire `{VERSION}` nel riferimento al pacchetto precedente con la versione del `Microsoft.AspNetCore.Blazor.Templates` pacchetto illustrata nell' <xref:blazor/get-started> articolo.</span><span class="sxs-lookup"><span data-stu-id="6bd34-114">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="6bd34-115">Supporto del servizio di autenticazione</span><span class="sxs-lookup"><span data-stu-id="6bd34-115">Authentication service support</span></span>

<span data-ttu-id="6bd34-116">Il supporto per l'autenticazione degli utenti viene registrato nel contenitore del servizio `AddOidcAuthentication` con il metodo di estensione `Microsoft.AspNetCore.Components.WebAssembly.Authentication` fornito dal pacchetto.</span><span class="sxs-lookup"><span data-stu-id="6bd34-116">Support for authenticating users is registered in the service container with the `AddOidcAuthentication` extension method provided by the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package.</span></span> <span data-ttu-id="6bd34-117">Questo metodo configura tutti i servizi necessari per l'interazione dell'app con il Identity provider (IP).</span><span class="sxs-lookup"><span data-stu-id="6bd34-117">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="6bd34-118">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="6bd34-118">*Program.cs*:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    builder.Configuration.Bind("Local", options.ProviderOptions);
});
```

<span data-ttu-id="6bd34-119">La configurazione viene fornita dal file *wwwroot/appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="6bd34-119">Configuration is supplied by the *wwwroot/appsettings.json* file:</span></span>

```json
{
    "Local": {
        "Authority": "{AUTHORITY}",
        "ClientId": "{CLIENT ID}"
    }
}
```

<span data-ttu-id="6bd34-120">Il supporto per l'autenticazione per le app autonome è disponibile con Open ID Connect (OIDC).</span><span class="sxs-lookup"><span data-stu-id="6bd34-120">Authentication support for standalone apps is offered using Open ID Connect (OIDC).</span></span> <span data-ttu-id="6bd34-121">Il `AddOidcAuthentication` metodo accetta un callback per configurare i parametri necessari per autenticare un'app usando OIDC.</span><span class="sxs-lookup"><span data-stu-id="6bd34-121">The `AddOidcAuthentication` method accepts a callback to configure the parameters required to authenticate an app using OIDC.</span></span> <span data-ttu-id="6bd34-122">I valori necessari per la configurazione dell'app possono essere ottenuti dall'IP conforme a OIDC.</span><span class="sxs-lookup"><span data-stu-id="6bd34-122">The values required for configuring the app can be obtained from the OIDC-compliant IP.</span></span> <span data-ttu-id="6bd34-123">Ottenere i valori quando si registra l'app, che in genere si verifica nel portale online.</span><span class="sxs-lookup"><span data-stu-id="6bd34-123">Obtain the values when you register the app, which typically occurs in their online portal.</span></span>

## <a name="access-token-scopes"></a><span data-ttu-id="6bd34-124">Ambiti del token di accesso</span><span class="sxs-lookup"><span data-stu-id="6bd34-124">Access token scopes</span></span>

<span data-ttu-id="6bd34-125">Il Blazor modello webassembly non configura automaticamente l'app per richiedere un token di accesso per un'API protetta.</span><span class="sxs-lookup"><span data-stu-id="6bd34-125">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="6bd34-126">Per eseguire il provisioning di un token di accesso come parte del flusso di accesso, aggiungere l'ambito agli ambiti dei `OidcProviderOptions`token predefiniti di:</span><span class="sxs-lookup"><span data-stu-id="6bd34-126">To provision an access token as part of the sign-in flow, add the scope to the default token scopes of the `OidcProviderOptions`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="6bd34-127">Se il portale di Azure fornisce un URI di ambito e **l'app genera un'eccezione non gestita** quando riceve una risposta *401 non autorizzata* dall'API, provare a usare un URI dell'ambito che non includa lo schema e l'host.</span><span class="sxs-lookup"><span data-stu-id="6bd34-127">If the Azure portal provides a scope URI and **the app throws an unhandled exception** when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="6bd34-128">Ad esempio, il portale di Azure può fornire uno dei seguenti formati di URI di ambito:</span><span class="sxs-lookup"><span data-stu-id="6bd34-128">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="6bd34-129">Specificare l'URI dell'ambito senza lo schema e l'host:</span><span class="sxs-lookup"><span data-stu-id="6bd34-129">Supply the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

<span data-ttu-id="6bd34-130">Per ulteriori informazioni, vedere le sezioni seguenti dell'articolo *scenari aggiuntivi* :</span><span class="sxs-lookup"><span data-stu-id="6bd34-130">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="6bd34-131">Richiedere token di accesso aggiuntivi</span><span class="sxs-lookup"><span data-stu-id="6bd34-131">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="6bd34-132">Connetti token alle richieste in uscita</span><span class="sxs-lookup"><span data-stu-id="6bd34-132">Attach tokens to outgoing requests</span></span>](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a><span data-ttu-id="6bd34-133">Importa file</span><span class="sxs-lookup"><span data-stu-id="6bd34-133">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="6bd34-134">Pagina di indice</span><span class="sxs-lookup"><span data-stu-id="6bd34-134">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

## <a name="app-component"></a><span data-ttu-id="6bd34-135">Componente app</span><span class="sxs-lookup"><span data-stu-id="6bd34-135">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="6bd34-136">Componente RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="6bd34-136">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="6bd34-137">Componente LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="6bd34-137">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="6bd34-138">Componente di autenticazione</span><span class="sxs-lookup"><span data-stu-id="6bd34-138">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="6bd34-139">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="6bd34-139">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
