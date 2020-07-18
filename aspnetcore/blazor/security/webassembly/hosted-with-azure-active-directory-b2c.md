---
title: Proteggere un' Blazor WebAssembly app ospitata ASP.NET Core con Azure Active Directory B2C
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
uid: blazor/security/webassembly/hosted-with-azure-active-directory-b2c
ms.openlocfilehash: 40ec40423de82926bd861ef352546b3dfe63dae0
ms.sourcegitcommit: 384833762c614851db653b841cc09fbc944da463
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/17/2020
ms.locfileid: "86445255"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-hosted-app-with-azure-active-directory-b2c"></a>Proteggere un' Blazor WebAssembly app ospitata ASP.NET Core con Azure Active Directory B2C

Di [Javier Calvarro Nelson](https://github.com/javiercn) e [Luke Latham](https://github.com/guardrex)

Questo articolo descrive come creare un' Blazor WebAssembly app autonoma che usa [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) per l'autenticazione.

## <a name="register-apps-in-aad-b2c-and-create-solution"></a>Registrare le app in AAD B2C e creare la soluzione

### <a name="create-a-tenant"></a>Creare un tenant

Seguire le istruzioni riportate in [esercitazione: creare un tenant di Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-create-tenant) per creare un tenant di AAD B2C.

Registrare l'istanza di AAD B2C, ad esempio, `https://contoso.b2clogin.com/` che include la barra finale. L'istanza è lo schema e l'host di una registrazione di app di Azure B2C, che è possibile trovare aprendo la finestra **endpoint** dalla pagina **registrazioni app** nella portale di Azure.

### <a name="register-a-server-api-app"></a>Registrare un'app per le API server

Seguire le istruzioni riportate in [esercitazione: registrare un'applicazione in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) per registrare un'app AAD per l'app per le *API del server* e quindi eseguire le operazioni seguenti:

1. In **Azure Active Directory**  >  **registrazioni app**selezionare **nuova registrazione**.
1. Specificare un **nome** per l'app, ad esempio ** Blazor Server AAD B2C**.
1. Per i **tipi di account supportati**, selezionare l'opzione multi-tenant: **account in qualsiasi directory organizzativa o provider di identità. Per l'autenticazione degli utenti con Azure AD B2C.**
1. L' *app* per le API del server non richiede un **URI di reindirizzamento** in questo scenario, quindi lasciare l'elenco a discesa impostato su **Web** e non immettere un URI di reindirizzamento.
1. Verificare che **Permissions**  >  **le autorizzazioni concedano il consenso dell'amministratore a OpenID e offline_access autorizzazioni** siano abilitate.
1. Selezionare **Registra**.

Registrare le seguenti informazioni:

* *App per le API server* ID applicazione (client) (ad esempio, `41451fa7-82d9-4673-8fa5-69eff5a761fd` )
* Dominio primario/editore/tenant di AAD (ad esempio, `contoso.onmicrosoft.com` ): il dominio è disponibile come **dominio del server di pubblicazione** nel pannello **personalizzazione** del portale di Azure per l'app registrata.

In **esporre un'API**:

1. Selezionare **Aggiungi un ambito**.
1. Fai clic su **Salva e continua**.
1. Specificare un **nome** per l'ambito (ad esempio, `API.Access` ).
1. Fornire un **nome visualizzato** per il consenso dell'amministratore, ad esempio `Access API` .
1. Fornire una **Descrizione del consenso dell'amministratore** , ad esempio `Allows the app to access server app API endpoints.` .
1. Verificare che lo **stato** sia impostato su **abilitato**.
1. Selezionare **Aggiungi ambito**.

Registrare le seguenti informazioni:

* URI ID app (ad esempio, `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd` , `api://41451fa7-82d9-4673-8fa5-69eff5a761fd` o il valore personalizzato specificato)
* Ambito predefinito (ad esempio, `API.Access` )

L'URI ID app potrebbe richiedere una configurazione speciale nell'app client, descritta nella sezione [ambiti dei token di accesso](#access-token-scopes) più avanti in questo argomento.

### <a name="register-a-client-app"></a>Registrare un'app client

Seguire le istruzioni riportate in [esercitazione: registrare un'applicazione in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) di nuovo per registrare un'app AAD per l' *app client* e quindi eseguire le operazioni seguenti:

1. In **Azure Active Directory**  >  **registrazioni app**selezionare **nuova registrazione**.
1. Specificare un **nome** per l'app, ad esempio ** Blazor AAD B2C client**.
1. Per i **tipi di account supportati**, selezionare l'opzione multi-tenant: **account in qualsiasi directory organizzativa o provider di identità. Per l'autenticazione degli utenti con Azure AD B2C.**
1. Lasciare l'elenco a discesa **URI di reindirizzamento** impostato su **Web** e specificare l'URI di reindirizzamento seguente: `https://localhost:{PORT}/authentication/login-callback` . La porta predefinita per un'app in esecuzione su gheppio è 5001. Se l'app viene eseguita su una porta di Gheppio diversa, usare la porta dell'app. Per IIS Express, la porta generata in modo casuale per l'app si trova nelle proprietà dell'app Server nel pannello **debug** . Poiché l'app non esiste in questo momento e la porta IIS Express non è nota, tornare a questo passaggio dopo la creazione dell'app e aggiornare l'URI di reindirizzamento. Un contrassegno viene visualizzato nella sezione [creare l'app](#create-the-app) per ricordare IIS Express agli utenti di aggiornare l'URI di reindirizzamento.
1. Verificare che **Permissions**  >  **le autorizzazioni concedano il consenso dell'amministratore a OpenID e offline_access autorizzazioni** siano abilitate.
1. Selezionare **Registra**.

Registrare l'ID dell'applicazione (client) (ad esempio, `4369008b-21fa-427c-abaa-9b53bf58e538` ).

In **Authentication**  >  **configurazioni piattaforma**di autenticazione  >  **Web**:

1. Verificare che l' **URI di reindirizzamento** di `https://localhost:{PORT}/authentication/login-callback` sia presente.
1. Per **concessione implicita**, selezionare le caselle di controllo per i token di **accesso** e i **token ID**.
1. Per questa esperienza sono accettabili le impostazioni predefinite rimanenti per l'app.
1. Fare clic sul pulsante **Salva**.

In **autorizzazioni API**:

1. Selezionare **Aggiungi un'autorizzazione** seguita da **API personali**.
1. Selezionare l' *app per le API server* dalla colonna **nome** , ad esempio ** Blazor Server AAD B2C**.
1. Aprire l'elenco di **API** .
1. Abilitare l'accesso all'API (ad esempio, `API.Access` ).
1. Selezionare **Aggiungi autorizzazioni**.
1. Selezionare il pulsante **concedere il consenso dell'amministratore per {tenant Name}** . Selezionare **Sì** per confermare.

In **Home**  >  **Azure ad B2C**  >  **flussi utente**:

[Creare un flusso utente di iscrizione e accesso](/azure/active-directory-b2c/tutorial-create-user-flows)

Selezionare almeno l'attributo utente nome visualizzato dell' **applicazione Claims**  >  **Display Name** per popolare `context.User.Identity.Name` nel `LoginDisplay` componente ( `Shared/LoginDisplay.razor` ).

Registrare il nome del flusso utente di iscrizione e accesso creato per l'app (ad esempio, `B2C_1_signupsignin` ).

### <a name="create-the-app"></a>Creare l'app

Sostituire i segnaposto nel comando seguente con le informazioni registrate in precedenza ed eseguire il comando in una shell dei comandi:

```dotnetcli
dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{TENANT DOMAIN}" -ho -o {APP NAME} -ssp "{SIGN UP OR SIGN IN POLICY}"
```

| Segnaposto                   | Nome portale di Azure                                     | Esempio                                |
| ----------------------------- | ----------------------------------------------------- | -------------------------------------- |
| `{AAD B2C INSTANCE}`          | Istanza                                              | `https://contoso.b2clogin.com/`        |
| `{APP NAME}`                  | &mdash;                                               | `BlazorSample`                         |
| `{CLIENT APP CLIENT ID}`      | ID applicazione (client) per l' *app client*          | `4369008b-21fa-427c-abaa-9b53bf58e538` |
| `{DEFAULT SCOPE}`             | Nome ambito                                            | `API.Access`                           |
| `{SERVER API APP CLIENT ID}`  | ID applicazione (client) per l' *app per le API server*      | `41451fa7-82d9-4673-8fa5-69eff5a761fd` |
| `{SERVER API APP ID URI}`     | URI ID applicazione ([vedere la nota](#access-token-scopes)) | `41451fa7-82d9-4673-8fa5-69eff5a761fd` |
| `{SIGN UP OR SIGN IN POLICY}` | Flusso utente di iscrizione/accesso                             | `B2C_1_signupsignin1`                  |
| `{TENANT DOMAIN}`             | Dominio primario/server di pubblicazione/tenant                       | `contoso.onmicrosoft.com`              |

Il percorso di output specificato con l' `-o|--output` opzione Crea una cartella di progetto se non esiste e diventa parte del nome dell'app.

> [!NOTE]
> Passare l'URI ID app all' `app-id-uri` opzione, ma si noti che potrebbe essere necessaria una modifica alla configurazione nell'app client, descritta nella sezione [ambiti dei token di accesso](#access-token-scopes) .
>
> Inoltre, l'ambito impostato dal Blazor modello ospitato potrebbe avere ripetutamente l'host Uri ID app. Verificare che l'ambito configurato per la `DefaultAccessTokenScopes` raccolta sia corretto in `Program.Main` ( `Program.cs` ) dell' *app client*.

> [!NOTE]
> Nel portale di Azure, l'URI di reindirizzamento Web delle configurazioni della piattaforma di autenticazione dell' *app client* **Authentication**  >  **Platform configurations**  >  **Web**  >  **Redirect URI** è configurato per la porta 5001 per le app eseguite nel server gheppio con le impostazioni predefinite.
>
> Se l' *app client* viene eseguita su una porta IIS Express casuale, la porta per l'app si trova nelle proprietà dell' *app* per le API del server nel pannello **debug** .
>
> Se la porta non è stata configurata in precedenza con la porta nota dell' *app client* , tornare alla registrazione dell' *app client* nell'portale di Azure e aggiornare l'URI di reindirizzamento con la porta corretta.

## <a name="server-app-configuration"></a>Configurazione dell'app Server

*Questa sezione è relativa all'app della soluzione **`Server`** .*

### <a name="authentication-package"></a>Pacchetto di autenticazione

Il pacchetto fornisce il supporto per l'autenticazione e l'autorizzazione delle chiamate a ASP.NET Core API Web [`Microsoft.AspNetCore.Authentication.AzureADB2C.UI`](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureADB2C.UI/) :

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureADB2C.UI" 
  Version="3.1.4" />
```

### <a name="authentication-service-support"></a>Supporto del servizio di autenticazione

Il `AddAuthentication` metodo imposta i servizi di autenticazione all'interno dell'app e configura il gestore di JWT Bearer come metodo di autenticazione predefinito. Il <xref:Microsoft.AspNetCore.Authentication.AzureADB2CAuthenticationBuilderExtensions.AddAzureADB2CBearer%2A> metodo imposta i parametri specifici nel gestore di JWT Bearer necessario per convalidare i token emessi dal Azure Active Directory B2C:

```csharp
services.AddAuthentication(AzureADB2CDefaults.BearerAuthenticationScheme)
    .AddAzureADB2CBearer(options => Configuration.Bind("AzureAdB2C", options));
```

<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A>e <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> verificare che:

* L'app tenta di analizzare e convalidare i token nelle richieste in ingresso.
* Eventuali richieste che tentano di accedere a una risorsa protetta senza credenziali appropriate hanno esito negativo.

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="useridentityname"></a>Utente. Identity . Nome

Per impostazione predefinita, `User.Identity.Name` non viene popolato.

Per configurare l'app per la ricezione del valore dal `name` tipo di attestazione, configurare il <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType> di <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices` :

```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;

...

services.Configure<JwtBearerOptions>(
    AzureADB2CDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a>Impostazioni app

Il `appsettings.json` file contiene le opzioni per configurare il gestore di connessione JWT usato per convalidare i token di accesso.

```json
{
  "AzureAdB2C": {
    "Instance": "https://{TENANT}.b2clogin.com/",
    "ClientId": "{SERVER API APP CLIENT ID}",
    "Domain": "{TENANT DOMAIN}",
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

Il controller WeatherForecast (*Controllers/WeatherForecastController. cs*) espone un'API protetta con l' [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attributo applicato al controller. È **importante** comprendere che:

* L' [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attributo in questo controller API è l'unico elemento che protegge questa API da accessi non autorizzati.
* L' [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attributo usato nell' Blazor WebAssembly app funge solo da hint per l'app che l'utente deve essere autorizzato affinché l'app funzioni correttamente.

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

*Questa sezione è relativa all'app della soluzione **`Client`** .*

### <a name="authentication-package"></a>Pacchetto di autenticazione

Quando viene creata un'app per usare un singolo account B2C ( `IndividualB2C` ), l'app riceve automaticamente un riferimento al pacchetto per [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ( [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) ). Il pacchetto fornisce un set di primitive che consentono all'app di autenticare gli utenti e ottenere i token per chiamare le API protette.

Se si aggiunge l'autenticazione a un'app, aggiungere manualmente il pacchetto al file di progetto dell'app:

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="3.2.0" />
```

Il [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) pacchetto aggiunge il [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) pacchetto all'app in modo transitivo.

### <a name="authentication-service-support"></a>Supporto del servizio di autenticazione

<xref:System.Net.Http.HttpClient>Viene aggiunto il supporto per le istanze di che includono token di accesso quando si effettuano richieste al progetto server.

`Program.cs`:

```csharp
builder.Services.AddHttpClient("{APP ASSEMBLY}.ServerAPI", client => 
    client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddScoped(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("{APP ASSEMBLY}.ServerAPI"));
```

Il segnaposto `{APP ASSEMBLY}` è il nome dell'assembly dell'app (ad esempio, `BlazorSample.ServerAPI` ).

Il supporto per l'autenticazione degli utenti viene registrato nel contenitore del servizio con il <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> metodo di estensione fornito dal [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) pacchetto. Questo metodo configura i servizi necessari per l'interazione dell'app con il Identity provider (IP).

`Program.cs`:

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAdB2C", options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

Il <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> metodo accetta un callback per configurare i parametri necessari per autenticare un'app. Quando si registra l'app, è possibile ottenere i valori necessari per la configurazione dell'app dalla configurazione di AAD del portale di Azure.

La configurazione viene fornita dal `wwwroot/appsettings.json` file:

```json
{
  "AzureAdB2C": {
    "Authority": "{AAD B2C INSTANCE}{TENANT DOMAIN}/{SIGN UP OR SIGN IN POLICY}",
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

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

Per ulteriori informazioni, vedere le sezioni seguenti dell'articolo *scenari aggiuntivi* :

* [Richiedere token di accesso aggiuntivi](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [Connetti token alle richieste in uscita](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)


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

Eseguire l'app dal progetto server. Quando si usa Visual Studio, eseguire una delle operazioni seguenti:

* Impostare l'elenco a discesa **progetti di avvio** nella barra degli strumenti sull'app per le *API server* e selezionare il pulsante **Esegui** .
* Selezionare il progetto server in **Esplora soluzioni** , quindi fare clic sul pulsante **Esegui** nella barra degli strumenti o avviare l'app dal menu **debug** .

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/wasm-aad-b2c-userflows.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>Risorse aggiuntive

* <xref:blazor/security/webassembly/additional-scenarios>
* [Richieste API Web non autenticate o non autorizzate in un'app con un client predefinito sicuro](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/authentication/azure-ad-b2c>
* [Esercitazione: Creare un tenant di Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-create-tenant)
* [Documentazione di Microsoft Identity Platform](/azure/active-directory/develop/)
