---
title: Proteggere un'app Blazor ASP.NET Core webassembly autonoma con Azure Active Directory
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/06/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/blazor/webassembly/standalone-with-azure-active-directory
ms.openlocfilehash: adc102edb457059f4d9686a48103483547b667fd
ms.sourcegitcommit: 363e3a2a035f4082cb92e7b75ed150ba304258b3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/08/2020
ms.locfileid: "82976857"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-standalone-app-with-azure-active-directory"></a>Proteggere un'app Blazor ASP.NET Core webassembly autonoma con Azure Active Directory

Di [Javier Calvarro Nelson](https://github.com/javiercn) e [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

Per creare un' Blazor app webassembly autonoma che usa [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) per l'autenticazione:

[Creare un'applicazione Web e un tenant di AAD](/azure/active-directory/develop/v2-overview):

Registrare un'app AAD nell'area **Azure Active Directory** > **registrazioni app** del portale di Azure:

1. Specificare un **nome** per l'app (ad esempio, ** Blazor client AAD**).
1. Scegliere i **tipi di conto supportati**. È possibile selezionare gli **account in questa directory organizzativa solo** per questa esperienza.
1. Lasciare l'elenco a discesa **URI di reindirizzamento** impostato su **Web**e specificare un URI di `https://localhost:5001/authentication/login-callback`reindirizzamento.
1. Disabilitare la casella di controllo **autorizzazioni** > **concessi da amministratore a OpenID e autorizzazioni offline_access** .
1. Selezionare **Registra**.

In **Authentication** >  > **configurazioni piattaforma**di autenticazione**Web**:

1. Verificare che l' **URI** di `https://localhost:5001/authentication/login-callback` Reindirizzamento di sia presente.
1. Per **concessione implicita**, selezionare le caselle di controllo per i token di **accesso** e i **token ID**.
1. Per questa esperienza sono accettabili le impostazioni predefinite rimanenti per l'app.
1. Fare clic sul pulsante **Salva**.

Registrare le seguenti informazioni:

* ID applicazione (ID client) (ad esempio, `11111111-1111-1111-1111-111111111111`)
* ID directory (ID tenant) (ad esempio, `22222222-2222-2222-2222-222222222222`)

Sostituire i segnaposto nel comando seguente con le informazioni registrate in precedenza ed eseguire il comando in una shell dei comandi:

```dotnetcli
dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" --tenant-id "{TENANT ID}"
```

Per specificare il percorso di output, che crea una cartella di progetto, se non esiste, includere l'opzione di output nel comando con un percorso (ad `-o BlazorSample`esempio,). Il nome della cartella diventa anche parte del nome del progetto.

## <a name="authentication-package"></a>Pacchetto di autenticazione

Quando viene creata un'app per usare gli account aziendali o dell'`SingleOrg`Istituto di istruzione (), l'app riceve automaticamente un riferimento al pacchetto per`Microsoft.Authentication.WebAssembly.Msal` [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (). Il pacchetto fornisce un set di primitive che consentono all'app di autenticare gli utenti e ottenere i token per chiamare le API protette.

Se si aggiunge l'autenticazione a un'app, aggiungere manualmente il pacchetto al file di progetto dell'app:

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

Sostituire `{VERSION}` nel riferimento al pacchetto precedente con la versione del `Microsoft.AspNetCore.Blazor.Templates` pacchetto illustrata nell' <xref:blazor/get-started> articolo.

Il `Microsoft.Authentication.WebAssembly.Msal` pacchetto aggiunge il `Microsoft.AspNetCore.Components.WebAssembly.Authentication` pacchetto all'app in modo transitivo.

## <a name="authentication-service-support"></a>Supporto del servizio di autenticazione

Il supporto per l'autenticazione degli utenti viene registrato nel contenitore del servizio `AddMsalAuthentication` con il metodo di estensione `Microsoft.Authentication.WebAssembly.Msal` fornito dal pacchetto. Questo metodo configura tutti i servizi necessari per l'interazione dell'app con il Identity provider (IP).

*Program.cs*:

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
});
```

Il `AddMsalAuthentication` metodo accetta un callback per configurare i parametri necessari per autenticare un'app. Quando si registra l'app, è possibile ottenere i valori necessari per la configurazione dell'app dalla configurazione degli account Microsoft.

La configurazione viene fornita dal file *wwwroot/appSettings. JSON* :

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/{TENANT ID}",
    "ClientId": "{CLIENT ID}"
  }
}
```

Esempio:

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/e86c78e2-...-918e0565a45e",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd"
  }
}
```

## <a name="access-token-scopes"></a>Ambiti del token di accesso

Il Blazor modello webassembly non configura automaticamente l'app per richiedere un token di accesso per un'API protetta. Per eseguire il provisioning di un token di accesso come parte del flusso di accesso, aggiungere l'ambito agli ambiti dei token `MsalProviderOptions`di accesso predefiniti di:

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> Se il portale di Azure fornisce un URI di ambito e **l'app genera un'eccezione non gestita** quando riceve una risposta *401 non autorizzata* dall'API, provare a usare un URI dell'ambito che non includa lo schema e l'host. Ad esempio, il portale di Azure può fornire uno dei seguenti formati di URI di ambito:
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> Specificare l'URI dell'ambito senza lo schema e l'host:
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

Per ulteriori informazioni, vedere le sezioni seguenti dell'articolo *scenari aggiuntivi* :

* [Richiedere token di accesso aggiuntivi](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [Connetti token alle richieste in uscita](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a>Importa file

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a>Pagina di indice

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a>Componente app

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a>Componente RedirectToLogin

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a>Componente LoginDisplay

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a>Componente di autenticazione

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>Risorse aggiuntive

* <xref:security/blazor/webassembly/additional-scenarios>
* <xref:security/blazor/webassembly/aad-groups-roles>
* <xref:security/authentication/azure-active-directory/index>
* [Documentazione di Microsoft Identity Platform](/azure/active-directory/develop/)
