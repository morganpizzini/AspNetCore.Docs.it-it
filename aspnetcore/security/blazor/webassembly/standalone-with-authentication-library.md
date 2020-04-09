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
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-the-authentication-library"></a>Proteggere un'applicazione autonoma ASP.NET Core WebAssembly con la libreria di autenticazioneSecure an ASP.NET Core Blazor Core WebAssembly standalone app with the Authentication library

Di [Javier Calvarro Nelson](https://github.com/javiercn) e [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

*Per Azure Active Directory (AAD) e Azure Active Directory B2C (AAD B2C), non seguire le indicazioni in questo argomento. Vedere gli argomenti AAD e AAD B2C in questo nodo del sommario.*

Per creare Blazor un'app autonoma `Microsoft.AspNetCore.Components.WebAssembly.Authentication` WebAssembly che utilizza la libreria, eseguire il comando seguente in una shell dei comandi:

```dotnetcli
dotnet new blazorwasm -au Individual
```

Per specificare il percorso di output, che crea una cartella di progetto se non esiste, `-o BlazorSample`includere l'opzione di output nel comando con un percorso, ad esempio ). Il nome della cartella diventa anche parte del nome del progetto.

In Visual Studio [creare un'app Blazor WebAssembly.](xref:blazor/get-started) Impostare **Autenticazione** su **Singoli account utente** con l'opzione Store user accounts **in-app.**

## <a name="authentication-package"></a>Pacchetto di autenticazione

Quando viene creata un'app per usare singoli account utente, `Microsoft.AspNetCore.Components.WebAssembly.Authentication` l'app riceve automaticamente un riferimento al pacchetto per il pacchetto nel file di progetto dell'app. Il pacchetto fornisce un set di primitive che consentono all'app di autenticare gli utenti e ottenere token per chiamare API protette.

Se si aggiunge l'autenticazione a un'app, aggiungi manualmente il pacchetto al file di progetto dell'app:

```xml
<PackageReference 
    Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
    Version="{VERSION}" />
```

Sostituire `{VERSION}` nel riferimento al pacchetto precedente `Microsoft.AspNetCore.Blazor.Templates` con la <xref:blazor/get-started> versione del pacchetto illustrata nell'articolo.

## <a name="authentication-service-support"></a>Supporto del servizio di autenticazione

Il supporto per l'autenticazione degli utenti `AddOidcAuthentication` viene registrato `Microsoft.AspNetCore.Components.WebAssembly.Authentication` nel contenitore dei servizi con il metodo di estensione fornito dal pacchetto. Questo metodo imposta tutti i servizi necessari all'app per interagire con il provider di identità (IP).

*Program.cs*:

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    options.ProviderOptions.Authority = "{AUTHORITY}";
    options.ProviderOptions.ClientId = "{CLIENT ID}";
});
```

Il supporto dell'autenticazione per le app autonome viene offerto tramite Open ID Connect (OIDC). Il `AddOidcAuthentication` metodo accetta un callback per configurare i parametri necessari per autenticare un'app tramite OIDC. I valori necessari per la configurazione dell'app possono essere ottenuti dall'IP compatibile con OIDC. Ottenere i valori quando si registra l'app, che in genere si verifica nel portale online.

## <a name="access-token-scopes"></a>Ambiti dei token di accessoAccess token scopes

Il Blazor modello WebAssembly non configura automaticamente l'app per richiedere un token di accesso per un'API sicura. Per eseguire il provisioning di un token come parte del flusso di `OidcProviderOptions`accesso, aggiungere l'ambito agli ambiti di token predefiniti di :

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> Se il portale di Azure fornisce un URI di ambito e **l'app genera un'eccezione non gestita** quando riceve una risposta *401 Unauthorized* dall'API, provare a usare un URI di ambito che non includa lo schema e l'host. Ad esempio, il portale di Azure può fornire uno dei seguenti formati URI di ambito:
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> Fornire l'URI dell'ambito senza lo schema e l'host:
>
> ```csharp
> options.ProviderOptions.DefaultScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

Per altre informazioni, vedere <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.

## <a name="imports-file"></a>File di importazione

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a>Pagina di indice

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

## <a name="app-component"></a>Componente dell'app

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a>Componente RedirectToLogin

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a>Componente LoginDisplay

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a>Componente di autenticazione

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>Risorse aggiuntive

* [Richiedere token di accesso aggiuntiviRequest additional access tokens](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
