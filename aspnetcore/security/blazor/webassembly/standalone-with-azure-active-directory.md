---
title: Proteggere un'app autonoma ASP.NET Core WebAssembly con Azure Active DirectorySecure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/08/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-azure-active-directory
ms.openlocfilehash: 7e132723657b7e12803b67ec12c3a33f1945baa3
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80976996"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-azure-active-directory"></a>Proteggere un'app autonoma ASP.NET Core WebAssembly con Azure Active DirectorySecure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory

Di [Javier Calvarro Nelson](https://github.com/javiercn) e [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

Per creare Blazor un'app autonoma WebAssembly che usa [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) per l'autenticazione:To create a WebAssembly standalone app that uses Azure Active Directory (AAD) for authentication:

[Creare un tenant AAD e un'applicazione Web:](/azure/active-directory/develop/v2-overview)

Registrare un'app AAD nell'area Registrazioni app **di Azure Active Directory** > del portale di Azure:Register a AAD app in the Azure Active Directory**App registrations** area of the Azure Azure Portal:

1. Specificare un **nome** per l'app, ** Blazor **ad esempio AAD client .
1. Scegliere un **Tipo di account supportato**. Puoi selezionare **Account in questa directory dell'organizzazione solo** per questa esperienza.
1. Lasciare l'elenco a discesa URI di **reindirizzamento** impostato su **Web**e specificare un URI di reindirizzamento di `https://localhost:5001/authentication/login-callback`.
1. Disattivare la casella di controllo **Autorizzazioni** > **concedere all'amministratore per le autorizzazioni openid e offline_access.**
1. Selezionare **Registra**.

In**Configurazioni** > piattaforma **di autenticazione** > **Web**:

1. Verificare che `https://localhost:5001/authentication/login-callback` l'URI di **reindirizzamento** di sia presente.
1. Per **Concessione implicita**, selezionare le caselle di controllo per Token di **accesso** e **token ID**.
1. Le impostazioni predefinite rimanenti per l'app sono accettabili per questa esperienza.
1. Fare clic sul pulsante **Salva**.

Registrare le seguenti informazioni:

* ID applicazione (ID client) `11111111-1111-1111-1111-111111111111`(ad esempio, )
* ID directory (ID tenant) `22222222-2222-2222-2222-222222222222`(ad esempio, )

Sostituire i segnaposto nel comando seguente con le informazioni registrate in precedenza ed eseguire il comando in una shell dei comandi:

```dotnetcli
dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" --tenant-id "{TENANT ID}"
```

Per specificare il percorso di output, che crea una cartella di progetto se non esiste, `-o BlazorSample`includere l'opzione di output nel comando con un percorso, ad esempio ). Il nome della cartella diventa anche parte del nome del progetto.

## <a name="authentication-package"></a>Pacchetto di autenticazione

Quando viene creata un'app per`SingleOrg`usare gli account aziendali o dell'istituto`Microsoft.Authentication.WebAssembly.Msal`di istruzione ( ), l'app riceve automaticamente un riferimento al pacchetto per la libreria di [autenticazione Microsoft](/azure/active-directory/develop/msal-overview) ( ). Il pacchetto fornisce un set di primitive che consentono all'app di autenticare gli utenti e ottenere token per chiamare API protette.

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
    authentication.Authority = "https://login.microsoftonline.com/{TENANT ID}";
    authentication.ClientId = "{CLIENT ID}";
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
* <xref:security/authentication/azure-active-directory/index>
* [Documentazione di Microsoft Identity Platform](/azure/active-directory/develop/)
