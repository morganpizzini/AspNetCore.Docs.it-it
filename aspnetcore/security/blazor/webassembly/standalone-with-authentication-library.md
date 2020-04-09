---
title: Proteggere un'applicazione autonoma ASP.NET Core WebAssembly con la libreria di autenticazioneSecure an ASP.NET Core Blazor Core WebAssembly standalone app with the Authentication library
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/08/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-authentication-library
ms.openlocfilehash: 893fff10df37e1c2be549604f4cb83cd20049108
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80977041"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-the-authentication-library"></a><span data-ttu-id="0a83d-102">Proteggere un'applicazione autonoma ASP.NET Core WebAssembly con la libreria di autenticazioneSecure an ASP.NET Core Blazor Core WebAssembly standalone app with the Authentication library</span><span class="sxs-lookup"><span data-stu-id="0a83d-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with the Authentication library</span></span>

<span data-ttu-id="0a83d-103">Di [Javier Calvarro Nelson](https://github.com/javiercn) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="0a83d-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="0a83d-104">*Per Azure Active Directory (AAD) e Azure Active Directory B2C (AAD B2C), non seguire le indicazioni in questo argomento. Vedere gli argomenti AAD e AAD B2C in questo nodo del sommario.*</span><span class="sxs-lookup"><span data-stu-id="0a83d-104">*For Azure Active Directory (AAD) and Azure Active Directory B2C (AAD B2C), don't follow the guidance in this topic. See the AAD and AAD B2C topics in this table of contents node.*</span></span>

<span data-ttu-id="0a83d-105">Per creare Blazor un'app autonoma `Microsoft.AspNetCore.Components.WebAssembly.Authentication` WebAssembly che utilizza la libreria, eseguire il comando seguente in una shell dei comandi:</span><span class="sxs-lookup"><span data-stu-id="0a83d-105">To create a Blazor WebAssembly standalone app that uses `Microsoft.AspNetCore.Components.WebAssembly.Authentication` library, execute the following command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au Individual
```

<span data-ttu-id="0a83d-106">Per specificare il percorso di output, che crea una cartella di progetto se non esiste, `-o BlazorSample`includere l'opzione di output nel comando con un percorso, ad esempio ).</span><span class="sxs-lookup"><span data-stu-id="0a83d-106">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="0a83d-107">Il nome della cartella diventa anche parte del nome del progetto.</span><span class="sxs-lookup"><span data-stu-id="0a83d-107">The folder name also becomes part of the project's name.</span></span>

<span data-ttu-id="0a83d-108">In Visual Studio [creare un'app Blazor WebAssembly.](xref:blazor/get-started)</span><span class="sxs-lookup"><span data-stu-id="0a83d-108">In Visual Studio, [create a Blazor WebAssembly app](xref:blazor/get-started).</span></span> <span data-ttu-id="0a83d-109">Impostare **Autenticazione** su **Singoli account utente** con l'opzione Store user accounts **in-app.**</span><span class="sxs-lookup"><span data-stu-id="0a83d-109">Set **Authentication** to **Individual User Accounts** with the **Store user accounts in-app** option.</span></span>

## <a name="authentication-package"></a><span data-ttu-id="0a83d-110">Pacchetto di autenticazione</span><span class="sxs-lookup"><span data-stu-id="0a83d-110">Authentication package</span></span>

<span data-ttu-id="0a83d-111">Quando viene creata un'app per usare singoli account utente, `Microsoft.AspNetCore.Components.WebAssembly.Authentication` l'app riceve automaticamente un riferimento al pacchetto per il pacchetto nel file di progetto dell'app.</span><span class="sxs-lookup"><span data-stu-id="0a83d-111">When an app is created to use Individual User Accounts, the app automatically receives a package reference for the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package in the app's project file.</span></span> <span data-ttu-id="0a83d-112">Il pacchetto fornisce un set di primitive che consentono all'app di autenticare gli utenti e ottenere token per chiamare API protette.</span><span class="sxs-lookup"><span data-stu-id="0a83d-112">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="0a83d-113">Se si aggiunge l'autenticazione a un'app, aggiungi manualmente il pacchetto al file di progetto dell'app:</span><span class="sxs-lookup"><span data-stu-id="0a83d-113">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference 
    Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
    Version="{VERSION}" />
```

<span data-ttu-id="0a83d-114">Sostituire `{VERSION}` nel riferimento al pacchetto precedente `Microsoft.AspNetCore.Blazor.Templates` con la <xref:blazor/get-started> versione del pacchetto illustrata nell'articolo.</span><span class="sxs-lookup"><span data-stu-id="0a83d-114">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="0a83d-115">Supporto del servizio di autenticazione</span><span class="sxs-lookup"><span data-stu-id="0a83d-115">Authentication service support</span></span>

<span data-ttu-id="0a83d-116">Il supporto per l'autenticazione degli utenti `AddOidcAuthentication` viene registrato `Microsoft.AspNetCore.Components.WebAssembly.Authentication` nel contenitore dei servizi con il metodo di estensione fornito dal pacchetto.</span><span class="sxs-lookup"><span data-stu-id="0a83d-116">Support for authenticating users is registered in the service container with the `AddOidcAuthentication` extension method provided by the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package.</span></span> <span data-ttu-id="0a83d-117">Questo metodo imposta tutti i servizi necessari all'app per interagire con il provider di identità (IP).</span><span class="sxs-lookup"><span data-stu-id="0a83d-117">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="0a83d-118">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="0a83d-118">*Program.cs*:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    options.ProviderOptions.Authority = "{AUTHORITY}";
    options.ProviderOptions.ClientId = "{CLIENT ID}";
});
```

<span data-ttu-id="0a83d-119">Il supporto dell'autenticazione per le app autonome viene offerto tramite Open ID Connect (OIDC).</span><span class="sxs-lookup"><span data-stu-id="0a83d-119">Authentication support for standalone apps is offered using Open ID Connect (OIDC).</span></span> <span data-ttu-id="0a83d-120">Il `AddOidcAuthentication` metodo accetta un callback per configurare i parametri necessari per autenticare un'app tramite OIDC.</span><span class="sxs-lookup"><span data-stu-id="0a83d-120">The `AddOidcAuthentication` method accepts a callback to configure the parameters required to authenticate an app using OIDC.</span></span> <span data-ttu-id="0a83d-121">I valori necessari per la configurazione dell'app possono essere ottenuti dall'IP compatibile con OIDC.</span><span class="sxs-lookup"><span data-stu-id="0a83d-121">The values required for configuring the app can be obtained from the OIDC-compliant IP.</span></span> <span data-ttu-id="0a83d-122">Ottenere i valori quando si registra l'app, che in genere si verifica nel portale online.</span><span class="sxs-lookup"><span data-stu-id="0a83d-122">Obtain the values when you register the app, which typically occurs in their online portal.</span></span>

## <a name="access-token-scopes"></a><span data-ttu-id="0a83d-123">Ambiti dei token di accessoAccess token scopes</span><span class="sxs-lookup"><span data-stu-id="0a83d-123">Access token scopes</span></span>

<span data-ttu-id="0a83d-124">Il Blazor modello WebAssembly non configura automaticamente l'app per richiedere un token di accesso per un'API sicura.</span><span class="sxs-lookup"><span data-stu-id="0a83d-124">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="0a83d-125">Per eseguire il provisioning di un token come parte del flusso di `OidcProviderOptions`accesso, aggiungere l'ambito agli ambiti di token predefiniti di :</span><span class="sxs-lookup"><span data-stu-id="0a83d-125">To provision a token as part of the sign-in flow, add the scope to the default token scopes of the `OidcProviderOptions`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="0a83d-126">Se il portale di Azure fornisce un URI di ambito e **l'app genera un'eccezione non gestita** quando riceve una risposta *401 Unauthorized* dall'API, provare a usare un URI di ambito che non includa lo schema e l'host.</span><span class="sxs-lookup"><span data-stu-id="0a83d-126">If the Azure portal provides a scope URI and **the app throws an unhandled exception** when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="0a83d-127">Ad esempio, il portale di Azure può fornire uno dei seguenti formati URI di ambito:</span><span class="sxs-lookup"><span data-stu-id="0a83d-127">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="0a83d-128">Fornire l'URI dell'ambito senza lo schema e l'host:</span><span class="sxs-lookup"><span data-stu-id="0a83d-128">Supply the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

<span data-ttu-id="0a83d-129">Per altre informazioni, vedere <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span><span class="sxs-lookup"><span data-stu-id="0a83d-129">For more information, see <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span></span>

## <a name="imports-file"></a><span data-ttu-id="0a83d-130">File di importazione</span><span class="sxs-lookup"><span data-stu-id="0a83d-130">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="0a83d-131">Pagina di indice</span><span class="sxs-lookup"><span data-stu-id="0a83d-131">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

## <a name="app-component"></a><span data-ttu-id="0a83d-132">Componente dell'app</span><span class="sxs-lookup"><span data-stu-id="0a83d-132">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="0a83d-133">Componente RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="0a83d-133">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="0a83d-134">Componente LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="0a83d-134">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="0a83d-135">Componente di autenticazione</span><span class="sxs-lookup"><span data-stu-id="0a83d-135">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="0a83d-136">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="0a83d-136">Additional resources</span></span>

* [<span data-ttu-id="0a83d-137">Richiedere token di accesso aggiuntiviRequest additional access tokens</span><span class="sxs-lookup"><span data-stu-id="0a83d-137">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
