---
title: Proteggere un' Blazor WebAssembly app ASP.NET Core autonoma con la libreria di autenticazione
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/08/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/security/webassembly/standalone-with-authentication-library
ms.openlocfilehash: 02960e6c7d70be3ea1be3ed9e2280e5b5847c926
ms.sourcegitcommit: f7873c02c1505c99106cbc708f37e18fc0a496d1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86147688"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-standalone-app-with-the-authentication-library"></a><span data-ttu-id="1dca9-102">Proteggere un' Blazor WebAssembly app ASP.NET Core autonoma con la libreria di autenticazione</span><span class="sxs-lookup"><span data-stu-id="1dca9-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with the Authentication library</span></span>

<span data-ttu-id="1dca9-103">Di [Javier Calvarro Nelson](https://github.com/javiercn) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="1dca9-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="1dca9-104">*Per Azure Active Directory (AAD) e Azure Active Directory B2C (AAD B2C), non seguire le istruzioni riportate in questo argomento. Vedere gli argomenti di AAD e AAD B2C in questo nodo Sommario.*</span><span class="sxs-lookup"><span data-stu-id="1dca9-104">*For Azure Active Directory (AAD) and Azure Active Directory B2C (AAD B2C), don't follow the guidance in this topic. See the AAD and AAD B2C topics in this table of contents node.*</span></span>

<span data-ttu-id="1dca9-105">Per creare un' Blazor WebAssembly app autonoma che usa [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) la libreria, seguire le istruzioni per la scelta degli strumenti.</span><span class="sxs-lookup"><span data-stu-id="1dca9-105">To create a Blazor WebAssembly standalone app that uses [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) library, follow the guidance for your choice of tooling.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1dca9-106">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1dca9-106">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="1dca9-107">Per creare un nuovo Blazor WebAssembly progetto con un meccanismo di autenticazione:</span><span class="sxs-lookup"><span data-stu-id="1dca9-107">To create a new Blazor WebAssembly project with an authentication mechanism:</span></span>

1. <span data-ttu-id="1dca9-108">Dopo aver scelto il modello \*\* Blazor WebAssembly app\*\* nella finestra di dialogo **Crea un nuovo ASP.NET Core applicazione Web** , selezionare **Cambia** in **autenticazione**.</span><span class="sxs-lookup"><span data-stu-id="1dca9-108">After choosing the **Blazor WebAssembly App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication**.</span></span>

1. <span data-ttu-id="1dca9-109">Selezionare **singoli account utente** con l'opzione **Archivia account utente in-app** per archiviare gli utenti all'interno dell'app usando il sistema di ASP.NET Core [Identity](xref:security/authentication/identity) .</span><span class="sxs-lookup"><span data-stu-id="1dca9-109">Select **Individual User Accounts** with the **Store user accounts in-app** option to store users within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system.</span></span>

# <a name="visual-studio-code--net-core-cli"></a>[<span data-ttu-id="1dca9-110">Visual Studio Code/Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="1dca9-110">Visual Studio Code / .NET Core CLI</span></span>](#tab/visual-studio-code+netcore-cli)

<span data-ttu-id="1dca9-111">Creare un nuovo Blazor WebAssembly progetto con un meccanismo di autenticazione in una cartella vuota.</span><span class="sxs-lookup"><span data-stu-id="1dca9-111">Create a new Blazor WebAssembly project with an authentication mechanism in an empty folder.</span></span> <span data-ttu-id="1dca9-112">Specificare il `Individual` meccanismo di autenticazione con l' `-au|--auth` opzione per archiviare gli utenti all'interno dell'app usando il sistema di ASP.NET Core [Identity](xref:security/authentication/identity) :</span><span class="sxs-lookup"><span data-stu-id="1dca9-112">Specify the `Individual` authentication mechanism with the `-au|--auth` option to store users within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system:</span></span>

```dotnetcli
dotnet new blazorwasm -au Individual -o {APP NAME}
```

| <span data-ttu-id="1dca9-113">Segnaposto</span><span class="sxs-lookup"><span data-stu-id="1dca9-113">Placeholder</span></span>  | <span data-ttu-id="1dca9-114">Esempio</span><span class="sxs-lookup"><span data-stu-id="1dca9-114">Example</span></span>        |
| ------------ | -------------- |
| `{APP NAME}` | `BlazorSample` |

<span data-ttu-id="1dca9-115">Il percorso di output specificato con l' `-o|--output` opzione Crea una cartella di progetto se non esiste e diventa parte del nome dell'app.</span><span class="sxs-lookup"><span data-stu-id="1dca9-115">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

<span data-ttu-id="1dca9-116">Per ulteriori informazioni, vedere il [`dotnet new`](/dotnet/core/tools/dotnet-new) comando nella Guida di .NET Core.</span><span class="sxs-lookup"><span data-stu-id="1dca9-116">For more information, see the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="1dca9-117">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="1dca9-117">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="1dca9-118">Per creare un nuovo Blazor WebAssembly progetto con un meccanismo di autenticazione:</span><span class="sxs-lookup"><span data-stu-id="1dca9-118">To create a new Blazor WebAssembly project with an authentication mechanism:</span></span>

1. <span data-ttu-id="1dca9-119">Nel passaggio **configurare la nuova Blazor WebAssembly app** selezionare **autenticazione singola (in-app) nell'elenco a** discesa **autenticazione** .</span><span class="sxs-lookup"><span data-stu-id="1dca9-119">On the **Configure your new Blazor WebAssembly App** step, select **Individual Authentication (in-app)** from the **Authentication** drop down.</span></span>

1. <span data-ttu-id="1dca9-120">L'app viene creata per i singoli utenti archiviati nell'app con ASP.NET Core [Identity](xref:security/authentication/identity) .</span><span class="sxs-lookup"><span data-stu-id="1dca9-120">The app is created for individual users stored in the app with ASP.NET Core [Identity](xref:security/authentication/identity).</span></span>

---

## <a name="authentication-package"></a><span data-ttu-id="1dca9-121">Pacchetto di autenticazione</span><span class="sxs-lookup"><span data-stu-id="1dca9-121">Authentication package</span></span>

<span data-ttu-id="1dca9-122">Quando si crea un'app per usare account utente singoli, l'app riceve automaticamente un riferimento al pacchetto per il [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) pacchetto nel file di progetto dell'app.</span><span class="sxs-lookup"><span data-stu-id="1dca9-122">When an app is created to use Individual User Accounts, the app automatically receives a package reference for the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package in the app's project file.</span></span> <span data-ttu-id="1dca9-123">Il pacchetto fornisce un set di primitive che consentono all'app di autenticare gli utenti e ottenere i token per chiamare le API protette.</span><span class="sxs-lookup"><span data-stu-id="1dca9-123">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="1dca9-124">Se si aggiunge l'autenticazione a un'app, aggiungere manualmente il pacchetto al file di progetto dell'app:</span><span class="sxs-lookup"><span data-stu-id="1dca9-124">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference 
  Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
  Version="3.2.0" />
```

## <a name="authentication-service-support"></a><span data-ttu-id="1dca9-125">Supporto del servizio di autenticazione</span><span class="sxs-lookup"><span data-stu-id="1dca9-125">Authentication service support</span></span>

<span data-ttu-id="1dca9-126">Il supporto per l'autenticazione degli utenti viene registrato nel contenitore del servizio con il <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> metodo di estensione fornito dal [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) pacchetto.</span><span class="sxs-lookup"><span data-stu-id="1dca9-126">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> extension method provided by the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package.</span></span> <span data-ttu-id="1dca9-127">Questo metodo configura i servizi necessari per l'interazione dell'app con il Identity provider (IP).</span><span class="sxs-lookup"><span data-stu-id="1dca9-127">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="1dca9-128">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="1dca9-128">`Program.cs`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    builder.Configuration.Bind("Local", options.ProviderOptions);
});
```

<span data-ttu-id="1dca9-129">La configurazione viene fornita dal `wwwroot/appsettings.json` file:</span><span class="sxs-lookup"><span data-stu-id="1dca9-129">Configuration is supplied by the `wwwroot/appsettings.json` file:</span></span>

```json
{
    "Local": {
        "Authority": "{AUTHORITY}",
        "ClientId": "{CLIENT ID}"
    }
}
```

<span data-ttu-id="1dca9-130">Il supporto per l'autenticazione per le app autonome è disponibile con Open ID Connect (OIDC).</span><span class="sxs-lookup"><span data-stu-id="1dca9-130">Authentication support for standalone apps is offered using Open ID Connect (OIDC).</span></span> <span data-ttu-id="1dca9-131">Il <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> metodo accetta un callback per configurare i parametri necessari per autenticare un'app usando OIDC.</span><span class="sxs-lookup"><span data-stu-id="1dca9-131">The <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app using OIDC.</span></span> <span data-ttu-id="1dca9-132">I valori necessari per la configurazione dell'app possono essere ottenuti dall'IP conforme a OIDC.</span><span class="sxs-lookup"><span data-stu-id="1dca9-132">The values required for configuring the app can be obtained from the OIDC-compliant IP.</span></span> <span data-ttu-id="1dca9-133">Ottenere i valori quando si registra l'app, che in genere si verifica nel portale online.</span><span class="sxs-lookup"><span data-stu-id="1dca9-133">Obtain the values when you register the app, which typically occurs in their online portal.</span></span>

## <a name="access-token-scopes"></a><span data-ttu-id="1dca9-134">Ambiti del token di accesso</span><span class="sxs-lookup"><span data-stu-id="1dca9-134">Access token scopes</span></span>

<span data-ttu-id="1dca9-135">Il Blazor WebAssembly modello non configura automaticamente l'app per richiedere un token di accesso per un'API protetta.</span><span class="sxs-lookup"><span data-stu-id="1dca9-135">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="1dca9-136">Per eseguire il provisioning di un token di accesso come parte del flusso di accesso, aggiungere l'ambito agli ambiti dei token predefiniti di <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.OidcProviderOptions> :</span><span class="sxs-lookup"><span data-stu-id="1dca9-136">To provision an access token as part of the sign-in flow, add the scope to the default token scopes of the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.OidcProviderOptions>:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

<span data-ttu-id="1dca9-137">Per ulteriori informazioni, vedere le sezioni seguenti dell'articolo *scenari aggiuntivi* :</span><span class="sxs-lookup"><span data-stu-id="1dca9-137">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="1dca9-138">Richiedere token di accesso aggiuntivi</span><span class="sxs-lookup"><span data-stu-id="1dca9-138">Request additional access tokens</span></span>](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="1dca9-139">Connetti token alle richieste in uscita</span><span class="sxs-lookup"><span data-stu-id="1dca9-139">Attach tokens to outgoing requests</span></span>](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a><span data-ttu-id="1dca9-140">Importa file</span><span class="sxs-lookup"><span data-stu-id="1dca9-140">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="1dca9-141">Pagina di indice</span><span class="sxs-lookup"><span data-stu-id="1dca9-141">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

## <a name="app-component"></a><span data-ttu-id="1dca9-142">Componente app</span><span class="sxs-lookup"><span data-stu-id="1dca9-142">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="1dca9-143">Componente RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="1dca9-143">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="1dca9-144">Componente LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="1dca9-144">LoginDisplay component</span></span>

<span data-ttu-id="1dca9-145">Il `LoginDisplay` componente ( `Shared/LoginDisplay.razor` ) viene sottoposto a rendering nel `MainLayout` componente ( `Shared/MainLayout.razor` ) e gestisce i comportamenti seguenti:</span><span class="sxs-lookup"><span data-stu-id="1dca9-145">The `LoginDisplay` component (`Shared/LoginDisplay.razor`) is rendered in the `MainLayout` component (`Shared/MainLayout.razor`) and manages the following behaviors:</span></span>

* <span data-ttu-id="1dca9-146">Per utenti autenticati:</span><span class="sxs-lookup"><span data-stu-id="1dca9-146">For authenticated users:</span></span>
  * <span data-ttu-id="1dca9-147">Visualizza il nome utente corrente.</span><span class="sxs-lookup"><span data-stu-id="1dca9-147">Displays the current username.</span></span>
  * <span data-ttu-id="1dca9-148">Offre un pulsante per disconnettersi dall'app.</span><span class="sxs-lookup"><span data-stu-id="1dca9-148">Offers a button to log out of the app.</span></span>
* <span data-ttu-id="1dca9-149">Per gli utenti anonimi, offre la possibilità di eseguire l'accesso.</span><span class="sxs-lookup"><span data-stu-id="1dca9-149">For anonymous users, offers the option to log in.</span></span>

```razor
@using Microsoft.AspNetCore.Components.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject NavigationManager Navigation
@inject SignOutSessionStateManager SignOutManager

<AuthorizeView>
    <Authorized>
        Hello, @context.User.Identity.Name!
        <button class="nav-link btn btn-link" @onclick="BeginSignOut">
            Log out
        </button>
    </Authorized>
    <NotAuthorized>
        <a href="authentication/login">Log in</a>
    </NotAuthorized>
</AuthorizeView>

@code {
    private async Task BeginSignOut(MouseEventArgs args)
    {
        await SignOutManager.SetSignOutState();
        Navigation.NavigateTo("authentication/logout");
    }
}
```

## <a name="authentication-component"></a><span data-ttu-id="1dca9-150">Componente di autenticazione</span><span class="sxs-lookup"><span data-stu-id="1dca9-150">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="1dca9-151">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="1dca9-151">Additional resources</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="1dca9-152">Richieste API Web non autenticate o non autorizzate in un'app con un client predefinito sicuro</span><span class="sxs-lookup"><span data-stu-id="1dca9-152">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
