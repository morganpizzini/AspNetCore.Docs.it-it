---
title: Proteggere un' Blazor app ospitata ASP.NET Core webassembly con Azure Active Directory B2C
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/11/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/blazor/webassembly/hosted-with-azure-active-directory-b2c
ms.openlocfilehash: e8b1a1f86becb1e9f0affe14a667253bd0ec16bf
ms.sourcegitcommit: 1250c90c8d87c2513532be5683640b65bfdf9ddb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83153663"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-hosted-app-with-azure-active-directory-b2c"></a>Proteggere un' Blazor app ospitata ASP.NET Core webassembly con Azure Active Directory B2C

Di [Javier Calvarro Nelson](https://github.com/javiercn) e [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

Questo articolo descrive come creare un' Blazor app webassembly autonoma che usa [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) per l'autenticazione.

## <a name="register-apps-in-aad-b2c-and-create-solution"></a>Registrare le app in AAD B2C e creare la soluzione

### <a name="create-a-tenant"></a>Creare un tenant

Seguire le istruzioni in [esercitazione: creare un tenant di Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-create-tenant) per creare un tenant di AAD B2C e registrare le informazioni seguenti:

* AAD B2C istanza (ad esempio, `https://contoso.b2clogin.com/` , che include la barra finale)
* AAD B2C dominio tenant (ad esempio, `contoso.onmicrosoft.com` )

### <a name="register-a-server-api-app"></a>Registrare un'app per le API server

Seguire le istruzioni riportate in [esercitazione: registrare un'applicazione in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) per registrare un'app AAD per l'app per le *API server* nell'area **Azure Active Directory**  >  **registrazioni app** della portale di Azure:

1. Selezionare **Nuova registrazione**.
1. Specificare un **nome** per l'app, ad esempio ** Blazor Server AAD B2C**.
1. Per i **tipi di account supportati**, selezionare **account in qualsiasi directory organizzativa o provider di identità. Per l'autenticazione degli utenti con Azure AD B2C.** (multi-tenant) per questa esperienza.
1. L' *app* per le API del server non richiede un **URI di reindirizzamento** in questo scenario, quindi lasciare l'elenco a discesa impostato su **Web** e non immettere un URI di reindirizzamento.
1. Verificare che **Permissions**  >  **le autorizzazioni concessino l'amministratore concent a OpenID e offline_access autorizzazioni** siano abilitate.
1. Selezionare **Registra**.

In **esporre un'API**:

1. Selezionare **Aggiungi un ambito**.
1. Fai clic su **Salva e continua**.
1. Specificare un **nome** per l'ambito (ad esempio, `API.Access` ).
1. Fornire un **nome visualizzato** per il consenso dell'amministratore, ad esempio `Access API` .
1. Fornire una **Descrizione del consenso dell'amministratore** , ad esempio `Allows the app to access server app API endpoints.` .
1. Verificare che lo **stato** sia impostato su **abilitato**.
1. Selezionare **Aggiungi ambito**.

Registrare le seguenti informazioni:

* *App per le API server* ID applicazione (ID client) (ad esempio, `11111111-1111-1111-1111-111111111111` )
* URI ID app (ad esempio, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111` , `api://11111111-1111-1111-1111-111111111111` o il valore personalizzato specificato)
* ID directory (ID tenant) (ad esempio, `222222222-2222-2222-2222-222222222222` )
* *App per le API server* URI ID app (ad esempio, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111` il portale di Azure potrebbe avere come valore predefinito l'ID client)
* Ambito predefinito (ad esempio, `API.Access` )

### <a name="register-a-client-app"></a>Registrare un'app client

Seguire le istruzioni riportate in [esercitazione: registrare un'applicazione in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) di nuovo per registrare un'app AAD per l' *app client* nell'area **Azure Active Directory**  >  **registrazioni app** della portale di Azure:

1. Selezionare **Nuova registrazione**.
1. Specificare un **nome** per l'app, ad esempio ** Blazor AAD B2C client**.
1. Per i **tipi di account supportati**, selezionare **account in qualsiasi directory organizzativa o provider di identità. Per l'autenticazione degli utenti con Azure AD B2C.** (multi-tenant) per questa esperienza.
1. Lasciare l'elenco a discesa **URI di reindirizzamento** impostato su **Web**e specificare un URI di Reindirizzamento `https://localhost:5001/authentication/login-callback` .
1. Verificare che **Permissions**  >  **le autorizzazioni concessino l'amministratore concent a OpenID e offline_access autorizzazioni** siano abilitate.
1. Selezionare **Registra**.

In **Authentication**  >  **configurazioni piattaforma**di autenticazione  >  **Web**:

1. Verificare che l' **URI di reindirizzamento** di `https://localhost:5001/authentication/login-callback` sia presente.
1. Per **concessione implicita**, selezionare le caselle di controllo per i token di **accesso** e i **token ID**.
1. Per questa esperienza sono accettabili le impostazioni predefinite rimanenti per l'app.
1. Fare clic sul pulsante **Salva**.

In **autorizzazioni API**:

1. Verificare che l'app disponga **Microsoft Graph**  >  autorizzazione**User. Read** .
1. Selezionare **Aggiungi un'autorizzazione** seguita da **API personali**.
1. Selezionare l' *app per le API server* dalla colonna **nome** , ad esempio ** Blazor Server AAD B2C**.
1. Aprire l'elenco di **API** .
1. Abilitare l'accesso all'API (ad esempio, `API.Access` ).
1. Selezionare **Aggiungi autorizzazioni**.
1. Selezionare il pulsante **Concedi contenuto amministratore per {tenant Name}** . Selezionare **Sì** per confermare.

In **Home**  >  **Azure ad B2C**  >  **flussi utente**:

[Creare un flusso utente di iscrizione e accesso](/azure/active-directory-b2c/tutorial-create-user-flows)

Selezionare almeno l'attributo utente nome visualizzato dell' **applicazione Claims**  >  **Display Name** per popolare `context.User.Identity.Name` nel `LoginDisplay` componente (*Shared/LoginDisplay. Razor*).

Registrare le seguenti informazioni:

* Registrare l'ID applicazione dell' *app client* (ID client) (ad esempio, `33333333-3333-3333-3333-333333333333` ).
* Registrare il nome del flusso utente di iscrizione e accesso creato per l'app (ad esempio, `B2C_1_signupsignin` ).

### <a name="create-the-app"></a>Creare l'app

Sostituire i segnaposto nel comando seguente con le informazioni registrate in precedenza ed eseguire il comando in una shell dei comandi:

```dotnetcli
dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{DOMAIN}" -ho -ssp "{SIGN UP OR SIGN IN POLICY}" --tenant-id "{TENANT ID}"
```

Per specificare il percorso di output, che crea una cartella di progetto, se non esiste, includere l'opzione di output nel comando con un percorso (ad esempio, `-o BlazorSample` ). Il nome della cartella diventa anche parte del nome del progetto.

> [!NOTE]
> Passare l'URI ID app all' `app-id-uri` opzione, ma si noti che potrebbe essere necessaria una modifica alla configurazione nell'app client, descritta nella sezione [ambiti dei token di accesso](#access-token-scopes) .

## <a name="server-app-configuration"></a>Configurazione dell'app Server

*Questa sezione è relativa all'app **Server** della soluzione.*

### <a name="authentication-package"></a>Pacchetto di autenticazione

Il supporto per l'autenticazione e l'autorizzazione delle chiamate a ASP.NET Core API Web viene fornito da `Microsoft.AspNetCore.Authentication.AzureADB2C.UI` :

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureADB2C.UI" 
    Version="{VERSION}" />
```

### <a name="authentication-service-support"></a>Supporto del servizio di autenticazione

Il `AddAuthentication` metodo imposta i servizi di autenticazione all'interno dell'app e configura il gestore di JWT Bearer come metodo di autenticazione predefinito. Il `AddAzureADB2CBearer` metodo imposta i parametri specifici nel gestore di JWT Bearer necessario per convalidare i token emessi dal Azure Active Directory B2C:

```csharp
services.AddAuthentication(AzureADB2CDefaults.BearerAuthenticationScheme)
    .AddAzureADB2CBearer(options => Configuration.Bind("AzureAdB2C", options));
```

`UseAuthentication`e `UseAuthorization` verificare che:

* L'app tenta di analizzare e convalidare i token nelle richieste in ingresso.
* Eventuali richieste che tentano di accedere a una risorsa protetta senza credenziali appropriate hanno esito negativo.

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="useridentityname"></a>Utente. Identity . Nome

Per impostazione predefinita, `User.Identity.Name` non viene popolato.

Per configurare l'app per la ricezione del valore dal `name` tipo di attestazione, configurare [TokenValidationParameters. NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) di <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices` :

```csharp
services.Configure<JwtBearerOptions>(
    AzureADB2CDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a>Impostazioni dell'app

Il file *appSettings. JSON* contiene le opzioni per configurare il gestore di connessione JWT usato per convalidare i token di accesso.

```json
{
  "AzureAdB2C": {
    "Instance": "https://{ORGANIZATION}.b2clogin.com/",
    "ClientId": "{SERVER API APP CLIENT ID}",
    "Domain": "{DOMAIN}",
    "SignUpSignInPolicyId": "{SIGN UP OR SIGN IN POLICY}"
  }
}
```

Esempio:

```json
{
  "AzureAdB2C": {
    "Instance": "https://contoso.b2clogin.com/",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "Domain": "contoso.onmicrosoft.com",
    "SignUpSignInPolicyId": "B2C_1_signupsignin1",
  }
}
```

### <a name="weatherforecast-controller"></a>Controller WeatherForecast

Il controller WeatherForecast (*Controllers/WeatherForecastController. cs*) espone un'API protetta con l' `[Authorize]` attributo applicato al controller. È **importante** comprendere che:

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

Quando viene creata un'app per usare un singolo account B2C ( `IndividualB2C` ), l'app riceve automaticamente un riferimento al pacchetto per [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ( `Microsoft.Authentication.WebAssembly.Msal` ). Il pacchetto fornisce un set di primitive che consentono all'app di autenticare gli utenti e ottenere i token per chiamare le API protette.

Se si aggiunge l'autenticazione a un'app, aggiungere manualmente il pacchetto al file di progetto dell'app:

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

Sostituire `{VERSION}` nel riferimento al pacchetto precedente con la versione del `Microsoft.AspNetCore.Blazor.Templates` pacchetto illustrata nell' <xref:blazor/get-started> articolo.

Il `Microsoft.Authentication.WebAssembly.Msal` pacchetto aggiunge il `Microsoft.AspNetCore.Components.WebAssembly.Authentication` pacchetto all'app in modo transitivo.

### <a name="authentication-service-support"></a>Supporto del servizio di autenticazione

`HttpClient`Viene aggiunto il supporto per le istanze di che includono token di accesso quando si effettuano richieste al progetto server.

*Program.cs*:

```csharp
builder.Services.AddHttpClient("{APP ASSEMBLY}.ServerAPI", client => 
        client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddTransient(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("{APP ASSEMBLY}.ServerAPI"));
```

Il supporto per l'autenticazione degli utenti viene registrato nel contenitore del servizio con il `AddMsalAuthentication` metodo di estensione fornito dal `Microsoft.Authentication.WebAssembly.Msal` pacchetto. Questo metodo configura tutti i servizi necessari per l'interazione dell'app con il Identity provider (IP).

*Program.cs*:

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAdB2C", options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

Il `AddMsalAuthentication` metodo accetta un callback per configurare i parametri necessari per autenticare un'app. Quando si registra l'app, è possibile ottenere i valori necessari per la configurazione dell'app dalla configurazione di AAD del portale di Azure.

La configurazione viene fornita dal file *wwwroot/appSettings. JSON* :

```json
{
  "AzureAdB2C": {
    "Authority": "{AAD B2C INSTANCE}{DOMAIN}/{SIGN UP OR SIGN IN POLICY}",
    "ClientId": "{CLIENT APP CLIENT ID}",
    "ValidateAuthority": false
  }
}
```

Esempio:

```json
{
  "AzureAdB2C": {
    "Authority": "https://contoso.b2clogin.com/contoso.onmicrosoft.com/B2C_1_signupsignin1",
    "ClientId": "4369008b-21fa-427c-abaa-9b53bf58e538",
    "ValidateAuthority": false
  }
}
```

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

Per ulteriori informazioni, vedere le sezioni seguenti dell'articolo *scenari aggiuntivi* :

* [Richiedere token di accesso aggiuntivi](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [Connetti token alle richieste in uscita](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)


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

[!INCLUDE[](~/includes/blazor-security/wasm-aad-b2c-userflows.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>Risorse aggiuntive

* <xref:security/blazor/webassembly/additional-scenarios>
* [Richieste API Web non autenticate o non autorizzate in un'app con un client predefinito sicuro](xref:security/blazor/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/authentication/azure-ad-b2c>
* [Esercitazione: Creare un tenant di Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-create-tenant)
* [Documentazione di Microsoft Identity Platform](/azure/active-directory/develop/)
