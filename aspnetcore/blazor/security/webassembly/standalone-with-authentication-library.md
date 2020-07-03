---
title: Proteggere un' Blazor WebAssembly app ASP.NET Core autonoma con la libreria di autenticazione
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/security/webassembly/standalone-with-authentication-library
ms.openlocfilehash: df957c5ee385b29ca390c014187a4c10e79d37f4
ms.sourcegitcommit: 66fca14611eba141d455fe0bd2c37803062e439c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/03/2020
ms.locfileid: "85944627"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-standalone-app-with-the-authentication-library"></a>Proteggere un' Blazor WebAssembly app ASP.NET Core autonoma con la libreria di autenticazione

Di [Javier Calvarro Nelson](https://github.com/javiercn) e [Luke Latham](https://github.com/guardrex)

*Per Azure Active Directory (AAD) e Azure Active Directory B2C (AAD B2C), non seguire le istruzioni riportate in questo argomento. Vedere gli argomenti di AAD e AAD B2C in questo nodo Sommario.*

Per creare un' Blazor WebAssembly app autonoma che usa [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) la libreria, eseguire il comando seguente in una shell dei comandi:

```dotnetcli
dotnet new blazorwasm -au Individual
```

Per specificare il percorso di output, che crea una cartella di progetto, se non esiste, includere l'opzione di output nel comando con un percorso (ad esempio, `-o BlazorSample` ). Il nome della cartella diventa anche parte del nome del progetto.

Se si usa Visual Studio per creare un' Blazor WebAssembly app, impostare l' **autenticazione** su **singoli account utente** con l'opzione **Archivia account utente in-app** .

## <a name="authentication-package"></a>Pacchetto di autenticazione

Quando si crea un'app per usare account utente singoli, l'app riceve automaticamente un riferimento al pacchetto per il [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) pacchetto nel file di progetto dell'app. Il pacchetto fornisce un set di primitive che consentono all'app di autenticare gli utenti e ottenere i token per chiamare le API protette.

Se si aggiunge l'autenticazione a un'app, aggiungere manualmente il pacchetto al file di progetto dell'app:

```xml
<PackageReference 
  Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
  Version="3.2.0" />
```

## <a name="authentication-service-support"></a>Supporto del servizio di autenticazione

Il supporto per l'autenticazione degli utenti viene registrato nel contenitore del servizio con il <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> metodo di estensione fornito dal [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) pacchetto. Questo metodo configura i servizi necessari per l'interazione dell'app con il Identity provider (IP).

`Program.cs`:

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    builder.Configuration.Bind("Local", options.ProviderOptions);
});
```

La configurazione viene fornita dal `wwwroot/appsettings.json` file:

```json
{
    "Local": {
        "Authority": "{AUTHORITY}",
        "ClientId": "{CLIENT ID}"
    }
}
```

Il supporto per l'autenticazione per le app autonome è disponibile con Open ID Connect (OIDC). Il <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> metodo accetta un callback per configurare i parametri necessari per autenticare un'app usando OIDC. I valori necessari per la configurazione dell'app possono essere ottenuti dall'IP conforme a OIDC. Ottenere i valori quando si registra l'app, che in genere si verifica nel portale online.

## <a name="access-token-scopes"></a>Ambiti del token di accesso

Il Blazor WebAssembly modello non configura automaticamente l'app per richiedere un token di accesso per un'API protetta. Per eseguire il provisioning di un token di accesso come parte del flusso di accesso, aggiungere l'ambito agli ambiti dei token predefiniti di <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.OidcProviderOptions> :

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

Per ulteriori informazioni, vedere le sezioni seguenti dell'articolo *scenari aggiuntivi* :

* [Richiedere token di accesso aggiuntivi](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [Connetti token alle richieste in uscita](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a>Importa file

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a>Pagina di indice

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

## <a name="app-component"></a>Componente app

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a>Componente RedirectToLogin

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a>Componente LoginDisplay

Il `LoginDisplay` componente ( `Shared/LoginDisplay.razor` ) viene sottoposto a rendering nel `MainLayout` componente ( `Shared/MainLayout.razor` ) e gestisce i comportamenti seguenti:

* Per utenti autenticati:
  * Visualizza il nome utente corrente.
  * Offre un pulsante per disconnettersi dall'app.
* Per gli utenti anonimi, offre la possibilità di eseguire l'accesso.

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

## <a name="authentication-component"></a>Componente di autenticazione

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>Risorse aggiuntive

* <xref:blazor/security/webassembly/additional-scenarios>
* [Richieste API Web non autenticate o non autorizzate in un'app con un client predefinito sicuro](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
