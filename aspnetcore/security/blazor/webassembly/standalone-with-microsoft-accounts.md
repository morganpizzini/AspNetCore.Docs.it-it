---
title: Proteggere un'app autonoma ASP.NET Core WebAssembly con account MicrosoftSecure an ASP.NET Core Blazor WebAssembly standalone app with Microsoft Accounts
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/08/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-microsoft-accounts
ms.openlocfilehash: 8c409651b3338c2baeae497bef43b994823a20f9
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80977080"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-microsoft-accounts"></a>Proteggere un'app autonoma ASP.NET Core WebAssembly con account MicrosoftSecure an ASP.NET Core Blazor WebAssembly standalone app with Microsoft Accounts

Di [Javier Calvarro Nelson](https://github.com/javiercn) e [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

Per creare Blazor un'app autonoma WebAssembly che usa [gli account Microsoft con Azure Active Directory (AAD)](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal) per l'autenticazione:

1. [Creare un tenant AAD e un'applicazione WebCreate an AAD tenant and web application](/azure/active-directory/develop/v2-overview)

   Registrare un'app AAD nell'area Registrazioni app **di Azure Active Directory** > del portale di Azure:Register a AAD app in the Azure Active Directory**App registrations** area of the Azure Azure Portal:

   1\. Specificare un **nome** per l'app, ** Blazor **ad esempio AAD client .<br>
   2\. In **Tipi di account supportati**selezionare Account in qualsiasi directory **organizzativa.**<br>
   3\. Lasciare l'elenco a discesa URI di **reindirizzamento** impostato su **Web**e specificare un URI di reindirizzamento di `https://localhost:5001/authentication/login-callback`.<br>
   4\. Disattivare la casella di controllo **Autorizzazioni** > **concedere all'amministratore per le autorizzazioni openid e offline_access.**<br>
   5\. Selezionare **Registra**.

   In**Configurazioni** > piattaforma **di autenticazione** > **Web**:

   1\. Verificare che `https://localhost:5001/authentication/login-callback` l'URI di **reindirizzamento** di sia presente.<br>
   2\. Per **Concessione implicita**, selezionare le caselle di controllo per Token di **accesso** e **token ID**.<br>
   3\. Le impostazioni predefinite rimanenti per l'app sono accettabili per questa esperienza.<br>
   4\. Fare clic sul pulsante **Salva**.

   Registrare l'ID applicazione (ID `11111111-1111-1111-1111-111111111111`client) (ad esempio, ).

1. Sostituire i segnaposto nel comando seguente con le informazioni registrate in precedenza ed eseguire il comando in una shell dei comandi:

   ```dotnetcli
   dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" --tenant-id "common"
   ```

   Per specificare il percorso di output, che crea una cartella di progetto se non esiste, `-o BlazorSample`includere l'opzione di output nel comando con un percorso, ad esempio ). Il nome della cartella diventa anche parte del nome del progetto.

Dopo aver creato l'app, dovresti essere in grado di:

* Accedere all'app utilizzando un account Microsoft.
* Richiedere i token di accesso per le API Microsoft Blazor usando lo stesso approccio usato per le app autonome, a condizione che l'app sia stata configurata correttamente. Per ulteriori informazioni, vedere [Guida introduttiva: Configurare un'applicazione per esporre le API Web](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).

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
    authentication.Authority = "{AUTHORITY}";
    authentication.ClientId = "{CLIENT ID}";
});
```

Il `AddMsalAuthentication` metodo accetta un callback per configurare i parametri necessari per autenticare un'app. I valori necessari per la configurazione dell'app possono essere ottenuti dalla configurazione degli account Microsoft quando si registra l'app.

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
* [Guida introduttiva: Registrare un'applicazione con la piattaforma di identità MicrosoftQuickstart: Register an application with the Microsoft identity platform](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal)
* [Guida introduttiva: Configurare un'applicazione per esporre le API Web](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)
