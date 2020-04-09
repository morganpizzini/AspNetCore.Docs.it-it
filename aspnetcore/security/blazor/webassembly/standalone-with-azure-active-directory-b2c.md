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
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-azure-active-directory-b2c"></a>Proteggere un'app autonoma ASP.NET Core WebAssembly con Azure Active Directory B2CSecure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory B2C

Di [Javier Calvarro Nelson](https://github.com/javiercn) e [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

Per creare Blazor un'app autonoma WebAssembly che usa Azure Active Directory (AAD) B2C per l'autenticazione:To create a WebAssembly standalone app that uses [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) for authentication:

1. Seguire le indicazioni negli argomenti seguenti per creare un tenant e registrare un'app Web nel portale di Azure:Follow the guidance in the following topics to create a tenant and register a web app in the Azure Portal:

   * [Creare un tenant AAD B2C](/azure/active-directory-b2c/tutorial-create-tenant) &ndash; Registrare le informazioni seguenti:Create an AAD B2C tenant Record the following information:

     1\. AAD B2C (ad `https://contoso.b2clogin.com/`esempio, , che include la barra finale)<br>
     2\. AAD B2C Dominio tenant `contoso.onmicrosoft.com`(ad esempio, )

   * [Registrare un'applicazione](/azure/active-directory-b2c/tutorial-register-applications) &ndash; Web Effettuare le seguenti selezioni durante la registrazione dell'app:

     1\. Impostare **App Web / API Web su** **Sì**.<br>
     2\. Impostare **Consenti flusso implicito su** **Sì**.<br>
     3\. Aggiungere un URL `https://localhost:5001/authentication/login-callback`di **risposta** di .

     Registrare l'ID applicazione (ID `11111111-1111-1111-1111-111111111111`client) (ad esempio, ).

   * [Creare flussi](/azure/active-directory-b2c/tutorial-create-user-flows) &ndash; utente Creare un flusso utente di iscrizione e accesso.

     Come minimo, selezionare **l'attributo** > utente `context.User.Identity.Name` Nome `LoginDisplay` **visualizzato** attestazioni applicazione per popolare l'oggetto nel componente (*Shared/LoginDisplay.razor*).

     Registrare il nome del flusso utente di iscrizione e accesso `B2C_1_signupsignin`creato per l'app, ad esempio ).

1. Sostituire i segnaposto nel comando seguente con le informazioni registrate in precedenza ed eseguire il comando in una shell dei comandi:

   ```dotnetcli
   dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --client-id "{CLIENT ID}" --domain "{DOMAIN}" -ssp "{SIGN UP OR SIGN IN POLICY}"
   ```

   Per specificare il percorso di output, che crea una cartella di progetto se non esiste, `-o BlazorSample`includere l'opzione di output nel comando con un percorso, ad esempio ). Il nome della cartella diventa anche parte del nome del progetto.

## <a name="authentication-package"></a>Pacchetto di autenticazione

Quando viene creata un'app per usare`IndividualB2C`un account B2C individuale ( ), l'app riceve automaticamente un riferimento al pacchetto per la libreria di [autenticazione Microsoft](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`). Il pacchetto fornisce un set di primitive che consentono all'app di autenticare gli utenti e ottenere token per chiamare API protette.

Se si aggiunge l'autenticazione a un'app, aggiungi manualmente il pacchetto al file di progetto dell'app:

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

Sostituire `{VERSION}` nel riferimento al pacchetto precedente `Microsoft.AspNetCore.Blazor.Templates` con la <xref:blazor/get-started> versione del pacchetto illustrata nell'articolo.

Il `Microsoft.Authentication.WebAssembly.Msal` pacchetto aggiunge in `Microsoft.AspNetCore.Components.WebAssembly.Authentication` modo transitivo il pacchetto all'app.

## <a name="authentication-service-support"></a>Supporto del servizio di autenticazione

Il supporto per l'autenticazione degli utenti `AddMsalAuthentication` viene registrato `Microsoft.Authentication.WebAssembly.Msal` nel contenitore dei servizi con il metodo di estensione fornito dal pacchetto. Questo metodo imposta tutti i servizi necessari all'app per interagire con il provider di identità (IP).

*Program.cs*:

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

Il `AddMsalAuthentication` metodo accetta un callback per configurare i parametri necessari per autenticare un'app. I valori necessari per la configurazione dell'app possono essere ottenuti dalla configurazione di Azure Portal AAD quando si registra l'app.

## <a name="access-token-scopes"></a>Ambiti dei token di accessoAccess token scopes

Il Blazor modello WebAssembly non configura automaticamente l'app per richiedere un token di accesso per un'API sicura. Per eseguire il provisioning di un token come parte del flusso di accesso, aggiungere l'ambito agli ambiti predefiniti del token di accesso: `MsalProviderOptions`

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
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
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

Per altre informazioni, vedere <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.

## <a name="imports-file"></a>File di importazione

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a>Pagina di indice

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

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
* <xref:security/authentication/azure-ad-b2c>
* [Esercitazione: Creare un tenant di Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-create-tenant)
* [Documentazione di Microsoft Identity Platform](/azure/active-directory/develop/)
