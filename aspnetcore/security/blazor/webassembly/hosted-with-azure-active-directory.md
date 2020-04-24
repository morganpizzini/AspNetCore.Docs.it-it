---
title: Proteggere un'app Blazor ospitata ASP.NET Core webassembly con Azure Active Directory
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/23/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/hosted-with-azure-active-directory
ms.openlocfilehash: 8c24546da50607d692a9cdc9f9c007d6ac8645ad
ms.sourcegitcommit: 7bb14d005155a5044c7902a08694ee8ccb20c113
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82110928"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-hosted-app-with-azure-active-directory"></a>Proteggere un'app Blazor ospitata ASP.NET Core webassembly con Azure Active Directory

Di [Javier Calvarro Nelson](https://github.com/javiercn) e [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

> [!NOTE]
> Le indicazioni fornite in questo articolo si applicano a ASP.NET Core 3,2 Preview 4. Questo argomento verrà aggiornato per coprire l'anteprima 5 il venerdì 24 aprile.

Questo articolo descrive come creare un' [ Blazor app ospitata da webassembly](xref:blazor/hosting-models#blazor-webassembly) che usa [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) per l'autenticazione.

## <a name="register-apps-in-aad-b2c-and-create-solution"></a>Registrare le app in AAD B2C e creare la soluzione

### <a name="create-a-tenant"></a>Creare un tenant

Seguire le istruzioni riportate nella [Guida introduttiva: configurare un tenant](/azure/active-directory/develop/quickstart-create-new-tenant) per creare un tenant in AAD.

### <a name="register-a-server-api-app"></a>Registrare un'app per le API server

Seguire le istruzioni riportate nella [Guida introduttiva: registrare un'applicazione con la piattaforma di identità Microsoft](/azure/active-directory/develop/quickstart-register-app) e gli argomenti di Azure AAD successivi per registrare un'app AAD per l'app per le *API server* nell'area **Azure Active Directory** > **registrazioni app** del portale di Azure:

1. Selezionare **Nuova registrazione**.
1. Specificare un **nome** per l'app (ad esempio, ** Blazor server AAD**).
1. Scegliere i **tipi di conto supportati**. Per questa esperienza è possibile selezionare **solo gli account in questa directory aziendale** (tenant singolo).
1. L' *app* per le API del server non richiede un **URI di reindirizzamento** in questo scenario, quindi lasciare l'elenco a discesa impostato su **Web** e non immettere un URI di reindirizzamento.
1. Disabilitare la casella di controllo **autorizzazioni** > **concessi da amministratore a OpenID e autorizzazioni offline_access** .
1. Selezionare **Registra**.

In **autorizzazioni API**rimuovere l'autorizzazione **Microsoft Graph** > **utente. Read** , perché l'app non richiede l'accesso al profilo di accesso o UER.

In **esporre un'API**:

1. Selezionare **Aggiungi un ambito**.
1. Fai clic su **Salva e continua**.
1. Specificare un **nome** per l'ambito (ad `API.Access`esempio,).
1. Fornire un **nome visualizzato** per il consenso dell'amministratore, `Access API`ad esempio.
1. Fornire una **Descrizione del consenso dell'amministratore** , `Allows the app to access server app API endpoints.`ad esempio.
1. Verificare che lo **stato** sia impostato su **abilitato**.
1. Selezionare **Aggiungi ambito**.

Registrare le seguenti informazioni:

* *App per le API server* ID applicazione (ID client) (ad esempio, `11111111-1111-1111-1111-111111111111`)
* URI ID app (ad esempio `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111` `api://11111111-1111-1111-1111-111111111111`,, o il valore personalizzato specificato)
* ID directory (ID tenant) (ad esempio, `222222222-2222-2222-2222-222222222222`)
* Dominio del tenant AAD (ad esempio `contoso.onmicrosoft.com`,)
* Ambito predefinito (ad esempio, `API.Access`)

### <a name="register-a-client-app"></a>Registrare un'app client

Seguire le istruzioni disponibili in [Guida introduttiva: registrare un'applicazione con la piattaforma di identità Microsoft](/azure/active-directory/develop/quickstart-register-app) e gli argomenti di Azure AAD successivi per registrare un'app AAD per l' *app client* nell'area **Azure Active Directory** > **registrazioni app** del portale di Azure:

1. Selezionare **Nuova registrazione**.
1. Specificare un **nome** per l'app (ad esempio, ** Blazor client AAD**).
1. Scegliere i **tipi di conto supportati**. Per questa esperienza è possibile selezionare **solo gli account in questa directory aziendale** (tenant singolo).
1. Lasciare l'elenco a discesa **URI di reindirizzamento** impostato su **Web**e specificare un URI di `https://localhost:5001/authentication/login-callback`reindirizzamento.
1. Disabilitare la casella di controllo **autorizzazioni** > **concessi da amministratore a OpenID e autorizzazioni offline_access** .
1. Selezionare **Registra**.

In **Authentication** >  > **configurazioni piattaforma**di autenticazione**Web**:

1. Verificare che l' **URI** di `https://localhost:5001/authentication/login-callback` Reindirizzamento di sia presente.
1. Per **concessione implicita**, selezionare le caselle di controllo per i token di **accesso** e i **token ID**.
1. Per questa esperienza sono accettabili le impostazioni predefinite rimanenti per l'app.
1. Fare clic sul pulsante **Salva**.

In **autorizzazioni API**:

1. Verificare che l'app disponga **Microsoft Graph** > autorizzazione**User. Read** .
1. Selezionare **Aggiungi un'autorizzazione** seguita da **API personali**.
1. Selezionare l' *app per le API server* dalla colonna **nome** (ad esempio, ** Blazor server AAD**).
1. Aprire l'elenco di **API** .
1. Abilitare l'accesso all'API (ad esempio, `API.Access`).
1. Selezionare **Aggiungi autorizzazioni**.
1. Selezionare il pulsante **Concedi contenuto amministratore per {tenant Name}** . Selezionare **Sì** per confermare.

Registrare l'ID applicazione dell' *app client* (ID client) (ad esempio `33333333-3333-3333-3333-333333333333`,).

### <a name="create-the-app"></a>Creare l'app

Sostituire i segnaposto nel comando seguente con le informazioni registrate in precedenza ed eseguire il comando in una shell dei comandi:

```dotnetcli
dotnet new blazorwasm -au SingleOrg --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{DOMAIN}" -ho --tenant-id "{TENANT ID}"
```

Per specificare il percorso di output, che crea una cartella di progetto, se non esiste, includere l'opzione di output nel comando con un percorso (ad `-o BlazorSample`esempio,). Il nome della cartella diventa anche parte del nome del progetto.

> [!NOTE]
> Passare l'URI ID app all' `app-id-uri` opzione, ma si noti che potrebbe essere necessaria una modifica alla configurazione nell'app client, descritta nella sezione [ambiti dei token di accesso](#access-token-scopes) .

## <a name="server-app-configuration"></a>Configurazione dell'app Server

*Questa sezione è relativa all'app **Server** della soluzione.*

### <a name="authentication-package"></a>Pacchetto di autenticazione

Il supporto per l'autenticazione e l'autorizzazione delle chiamate a ASP.NET Core API Web viene fornito da `Microsoft.AspNetCore.Authentication.AzureAD.UI`:

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureAD.UI" 
    Version="3.1.0" />
```

### <a name="authentication-service-support"></a>Supporto del servizio di autenticazione

Il `AddAuthentication` metodo imposta i servizi di autenticazione all'interno dell'app e configura il gestore di JWT Bearer come metodo di autenticazione predefinito. Il `AddAzureADBearer` metodo imposta i parametri specifici nel gestore di JWT Bearer necessario per convalidare i token emessi dal Azure Active Directory:

```csharp
services.AddAuthentication(AzureADDefaults.BearerAuthenticationScheme)
    .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
```

`UseAuthentication`e `UseAuthorization` verificare che:

* L'app tenta di analizzare e convalidare i token nelle richieste in ingresso.
* Eventuali richieste che tentano di accedere a una risorsa protetta senza credenziali appropriate hanno esito negativo.

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="useridentityname"></a>User.Identity.Name

Per impostazione predefinita, l'API dell'app Server `User.Identity.Name` viene popolata con il `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` valore del tipo di attestazione `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com`(ad esempio,).

Per configurare l'app per la ricezione del valore dal `name` tipo di attestazione, configurare [TokenValidationParameters. NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) di <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:

```csharp
services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a>Impostazioni app

Il file *appSettings. JSON* contiene le opzioni per configurare il gestore di connessione JWT usato per convalidare i token di accesso.

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "{DOMAIN}",
    "TenantId": "{TENANT ID}",
    "ClientId": "{API CLIENT ID}",
  }
}
```

### <a name="weatherforecast-controller"></a>Controller WeatherForecast

Il controller WeatherForecast (*Controllers/WeatherForecastController. cs*) espone un'API protetta con `[Authorize]` l'attributo applicato al controller. È **importante** comprendere che:

* L' `[Authorize]` attributo in questo controller API è l'unico elemento che protegge questa API da accessi non autorizzati.
* L' `[Authorize]` attributo usato nell' Blazor app webassembly funge solo da hint per l'app che l'utente deve essere autorizzato affinché l'app funzioni correttamente.

```csharp
[Authorize]
[ApiController]
[Route("[controller]")]
public class WeatherForecastController : ControllerBase
{
    [HttpGet]
    public IEnumerable<WeatherForecast> Get()
    {
        ...
    }
}
```

## <a name="client-app-configuration"></a>Configurazione dell'app client

*Questa sezione riguarda l'app **client** della soluzione.*

### <a name="authentication-package"></a>Pacchetto di autenticazione

Quando viene creata un'app per usare gli account aziendali o dell'`SingleOrg`Istituto di istruzione (), l'app riceve automaticamente un riferimento al pacchetto per`Microsoft.Authentication.WebAssembly.Msal` [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (). Il pacchetto fornisce un set di primitive che consentono all'app di autenticare gli utenti e ottenere i token per chiamare le API protette.

Se si aggiunge l'autenticazione a un'app, aggiungere manualmente il pacchetto al file di progetto dell'app:

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

Sostituire `{VERSION}` nel riferimento al pacchetto precedente con la versione del `Microsoft.AspNetCore.Blazor.Templates` pacchetto illustrata nell' <xref:blazor/get-started> articolo.

Il `Microsoft.Authentication.WebAssembly.Msal` pacchetto aggiunge il `Microsoft.AspNetCore.Components.WebAssembly.Authentication` pacchetto all'app in modo transitivo.

### <a name="authentication-service-support"></a>Supporto del servizio di autenticazione

Il supporto per l'autenticazione degli utenti viene registrato nel contenitore del servizio `AddMsalAuthentication` con il metodo di estensione `Microsoft.Authentication.WebAssembly.Msal` fornito dal pacchetto. Questo metodo configura tutti i servizi necessari per l'interazione dell'app con il provider di identità (IP).

*Program.cs*:

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    var authentication = options.ProviderOptions.Authentication;
    authentication.Authority = "https://login.microsoftonline.com/{TENANT ID}";
    authentication.ClientId = "{CLIENT ID}";
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

Il `AddMsalAuthentication` metodo accetta un callback per configurare i parametri necessari per autenticare un'app. Quando si registra l'app, è possibile ottenere i valori necessari per la configurazione dell'app dalla configurazione di AAD del portale di Azure.

### <a name="access-token-scopes"></a>Ambiti del token di accesso

Gli ambiti dei token di accesso predefiniti rappresentano l'elenco degli ambiti dei token di accesso:

* Incluso per impostazione predefinita nella richiesta di accesso.
* Utilizzato per eseguire il provisioning di un token di accesso immediatamente dopo l'autenticazione.

Tutti gli ambiti devono appartenere alla stessa app per ogni regola di Azure Active Directory. Per altre app per le API è possibile aggiungere altri ambiti, se necessario:

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

Per altre informazioni, vedere <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.

<!--
    For more information, see <xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests>.
-->

### <a name="imports-file"></a>Importa file

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a>Pagina di indice

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a>Componente app

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a>Componente RedirectToLogin

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a>Componente LoginDisplay

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a>Componente di autenticazione

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a>Componente FetchData

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a>Eseguire l'app

Eseguire l'app dal progetto server. Quando si usa Visual Studio, selezionare il progetto server in **Esplora soluzioni** e selezionare il pulsante **Esegui** sulla barra degli strumenti o avviare l'app dal menu **debug** .

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>Risorse aggiuntive

* <xref:security/blazor/webassembly/additional-scenarios>
* <xref:security/authentication/azure-active-directory/index>
* [Documentazione di Microsoft Identity Platform](/azure/active-directory/develop/)
