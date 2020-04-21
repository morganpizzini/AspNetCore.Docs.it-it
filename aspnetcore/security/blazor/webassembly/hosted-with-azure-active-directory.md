---
title: Proteggere un'app ospitata ASP.NET Core WebAssembly con Azure Active DirectorySecure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/08/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/hosted-with-azure-active-directory
ms.openlocfilehash: a80be8d145b7c58be35e2c353a448db7e234e20b
ms.sourcegitcommit: 5547d920f322e5a823575c031529e4755ab119de
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81661819"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-hosted-app-with-azure-active-directory"></a>Proteggere un'app ospitata ASP.NET Core WebAssembly con Azure Active DirectorySecure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory

Di [Javier Calvarro Nelson](https://github.com/javiercn) e [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

Questo articolo descrive come creare [ Blazor un'app ospitata WebAssembly](xref:blazor/hosting-models#blazor-webassembly) che usa [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) per l'autenticazione.

## <a name="register-apps-in-aad-b2c-and-create-solution"></a>Registrare le app in AAD B2C e creare una soluzione

### <a name="create-a-tenant"></a>Creare un tenant

Seguire le indicazioni in [Guida introduttiva: Configurare un tenant](/azure/active-directory/develop/quickstart-create-new-tenant) per creare un tenant in AAD.

### <a name="register-a-server-api-app"></a>Registrare un'app per le API del serverRegister a server API app

Seguire le indicazioni in [Guida introduttiva: Registrare un'applicazione con la piattaforma](/azure/active-directory/develop/quickstart-register-app) di identità Microsoft e i successivi argomenti di Azure AAD per registrare un'app AAD per *l'app App server* nell'area Registrazioni di Azure Active Directory App del portale di Azure:Follow the guidance in Quickstart: Register an application with the Microsoft identity platform and subsequent Azure AAD topics to register an AAD app for the Server API app in the **Azure Active Directory** > **App registrations** area of the Azure portal:

1. Selezionare **Nuova registrazione**.
1. Specificare un **nome** per l'app, ** Blazor **ad esempio Server AAD .
1. Scegliere un **Tipo di account supportato**. È possibile selezionare **Account solo in questa directory dell'organizzazione** (tenant singolo) per questa esperienza.
1. *L'app api server* non richiede un URI di **reindirizzamento** in questo scenario, quindi lasciare l'elenco a discesa impostato su **Web** e non immettere un URI di reindirizzamento.
1. Disattivare la casella di controllo **Autorizzazioni** > **concedere all'amministratore per le autorizzazioni openid e offline_access.**
1. Selezionare **Registra**.

In **Autorizzazioni API**rimuovere l'autorizzazione Microsoft **Graph** > **User.Read,** poiché l'app non richiede l'accesso o l'accesso al profilo uer.

In **Esporre un'API**:

1. Selezionare **Aggiungi un ambito**.
1. Selezionare **Salva e continua**.
1. Specificare un nome di `API.Access` **ambito,** ad esempio ).
1. Specificare un nome visualizzato di `Access API` **consenso dell'amministratore,** ad esempio ).
1. Fornire una descrizione del consenso `Allows the app to access server app API endpoints.`dell'amministratore, ad esempio ). **Admin consent description**
1. Verificare che **lo stato** sia impostato su **Abilitato**.
1. Selezionare **Aggiungi ambito**.

Registrare le seguenti informazioni:

* *App PER le API del server* ID applicazione (ID client) `11111111-1111-1111-1111-111111111111`(ad esempio, )
* URI ID app (ad esempio, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, `api://11111111-1111-1111-1111-111111111111`o il valore personalizzato fornito)
* ID directory (ID tenant) `222222222-2222-2222-2222-222222222222`(ad esempio, )
* Dominio tenant AAD (ad esempio, `contoso.onmicrosoft.com`)
* Ambito predefinito (ad `API.Access`esempio, )

### <a name="register-a-client-app"></a>Registrare un'app client

Seguire le indicazioni in [Guida introduttiva: Registrare un'applicazione con la piattaforma](/azure/active-directory/develop/quickstart-register-app) di identità Microsoft e i successivi argomenti di Azure AAD per registrare un'app AAD per *l'app Client* nell'area Registrazioni di Azure Active Directory App del portale di Azure:Follow the guidance in Quickstart: Register an application with the Microsoft identity platform and subsequent Azure AAD topics to register a AAD app for the Client app in the **Azure Active Directory** > **App registrations** area of the Azure portal:

1. Selezionare **Nuova registrazione**.
1. Specificare un **nome** per l'app, ** Blazor **ad esempio AAD client .
1. Scegliere un **Tipo di account supportato**. È possibile selezionare **Account solo in questa directory dell'organizzazione** (tenant singolo) per questa esperienza.
1. Lasciare l'elenco a discesa URI di **reindirizzamento** impostato su **Web**e specificare un URI di reindirizzamento di `https://localhost:5001/authentication/login-callback`.
1. Disattivare la casella di controllo **Autorizzazioni** > **concedere all'amministratore per le autorizzazioni openid e offline_access.**
1. Selezionare **Registra**.

In**Configurazioni** > piattaforma **di autenticazione** > **Web**:

1. Verificare che `https://localhost:5001/authentication/login-callback` l'URI di **reindirizzamento** di sia presente.
1. Per **Concessione implicita**, selezionare le caselle di controllo per Token di **accesso** e **token ID**.
1. Le impostazioni predefinite rimanenti per l'app sono accettabili per questa esperienza.
1. Fare clic sul pulsante **Salva**.

In **Autorizzazioni API**:

1. Verificare che l'app disponga dell'autorizzazione **Microsoft Graph** > **User.Read.**
1. Selezionare **Aggiungi un'autorizzazione** seguita da **API personali**.
1. Selezionare *l'app API server* nella colonna **Nome** (ad esempio, ** Blazor AAD del server).**
1. Aprire l'elenco **delle API.**
1. Abilitare l'accesso all'API, `API.Access`ad esempio ).
1. Selezionare **Aggiungi autorizzazioni**.
1. Selezionare il pulsante **Concedi il contenuto dell'amministratore per il** nome di TENANT. Selezionare **Sì** per confermare.

Registrare *l'ID applicazione dell'app* client `33333333-3333-3333-3333-333333333333`(ID client) (ad esempio, ).

### <a name="create-the-app"></a>Creare l'app

Sostituire i segnaposto nel comando seguente con le informazioni registrate in precedenza ed eseguire il comando in una shell dei comandi:

```dotnetcli
dotnet new blazorwasm -au SingleOrg --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{DOMAIN}" -ho --tenant-id "{TENANT ID}"
```

Per specificare il percorso di output, che crea una cartella di progetto se non esiste, `-o BlazorSample`includere l'opzione di output nel comando con un percorso, ad esempio ). Il nome della cartella diventa anche parte del nome del progetto.

> [!NOTE]
> Passare l'URI dell'ID app all'opzione, `app-id-uri` ma si noti che potrebbe essere necessaria una modifica alla configurazione nell'app client, descritta nella sezione Ambiti del token di [accesso.](#access-token-scopes)

## <a name="server-app-configuration"></a>Configurazione dell'app server

*Questa sezione riguarda l'app **Server** della soluzione.*

### <a name="authentication-package"></a>Pacchetto di autenticazione

Il supporto per l'autenticazione e l'autorizzazione delle chiamate `Microsoft.AspNetCore.Authentication.AzureAD.UI`a ASP.NET API Web principali viene fornito dal:

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureAD.UI" 
    Version="3.1.0" />
```

### <a name="authentication-service-support"></a>Supporto del servizio di autenticazione

Il `AddAuthentication` metodo imposta i servizi di autenticazione all'interno dell'app e configura il gestore del bearer JWT come metodo di autenticazione predefinito. Il `AddAzureADBearer` metodo imposta i parametri specifici nel gestore JWT Bearer necessari per convalidare i token generati da Azure Active Directory:

```csharp
services.AddAuthentication(AzureADDefaults.BearerAuthenticationScheme)
    .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
```

`UseAuthentication`e `UseAuthorization` assicurarsi che:

* L'app tenta di analizzare e convalidare i token nelle richieste in ingresso.
* Qualsiasi richiesta che tenti di accedere a una risorsa protetta senza credenziali appropriate ha esito negativo.

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="useridentityname"></a>User.Identity.Name

Per impostazione predefinita, l'API dell'app Server popola `User.Identity.Name` con il valore del `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` tipo di attestazione, `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com`ad esempio ).

Per configurare l'app per `name` la ricezione del valore dal tipo di <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> `Startup.ConfigureServices`attestazione, configurare il [TokenValidationParameters.NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) di in :

```csharp
services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a>Impostazioni app

Il file *appsettings.json* contiene le opzioni per configurare il gestore di connessione JWT utilizzato per convalidare i token di accesso.

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

Il controller WeatherForecast (*Controllers/WeatherForecastController.cs*) espone `[Authorize]` un'API protetta con l'attributo applicato al controller. È **importante** comprendere che:

* L'attributo `[Authorize]` in questo controller API è l'unica cosa che protegge questa API da accessi non autorizzati.
* L'attributo `[Authorize]` Blazor utilizzato nell'app WebAssembly funge solo da suggerimento per l'app che l'utente deve essere autorizzato per il corretto funzionamento dell'app.

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

*Questa sezione riguarda l'app **Client** della soluzione.*

### <a name="authentication-package"></a>Pacchetto di autenticazione

Quando viene creata un'app per`SingleOrg`usare gli account aziendali o dell'istituto`Microsoft.Authentication.WebAssembly.Msal`di istruzione ( ), l'app riceve automaticamente un riferimento al pacchetto per la libreria di [autenticazione Microsoft](/azure/active-directory/develop/msal-overview) ( ). Il pacchetto fornisce un set di primitive che consentono all'app di autenticare gli utenti e ottenere token per chiamare API protette.

Se si aggiunge l'autenticazione a un'app, aggiungi manualmente il pacchetto al file di progetto dell'app:

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

Sostituire `{VERSION}` nel riferimento al pacchetto precedente `Microsoft.AspNetCore.Blazor.Templates` con la <xref:blazor/get-started> versione del pacchetto illustrata nell'articolo.

Il `Microsoft.Authentication.WebAssembly.Msal` pacchetto aggiunge in `Microsoft.AspNetCore.Components.WebAssembly.Authentication` modo transitivo il pacchetto all'app.

### <a name="authentication-service-support"></a>Supporto del servizio di autenticazione

Il supporto per l'autenticazione degli utenti `AddMsalAuthentication` viene registrato `Microsoft.Authentication.WebAssembly.Msal` nel contenitore dei servizi con il metodo di estensione fornito dal pacchetto. Questo metodo imposta tutti i servizi necessari all'app per interagire con il provider di identità (IP).

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

Il `AddMsalAuthentication` metodo accetta un callback per configurare i parametri necessari per autenticare un'app. I valori necessari per la configurazione dell'app possono essere ottenuti dalla configurazione di Azure Portal AAD quando si registra l'app.

### <a name="access-token-scopes"></a>Ambiti dei token di accessoAccess token scopes

Gli ambiti di token di accesso predefiniti rappresentano l'elenco di ambiti di token di accesso:The default access token scopes represent the list of access token scopes that are:

* Incluso per impostazione predefinita nella richiesta di accesso.
* Utilizzato per eseguire il provisioning di un token di accesso immediatamente dopo l'autenticazione.

Tutti gli ambiti devono appartenere alla stessa app per ogni regola di Azure Active Directory.All scopes must belong to the same app per Azure Active Directory rules. È possibile aggiungere ambiti aggiuntivi per altre app API in base alle esigenze:Additional scopes can be added for additional API apps as needed:

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

### <a name="imports-file"></a>File di importazione

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a>Pagina di indice

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a>Componente dell'app

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

Eseguire l'app dal progetto Server. Quando si usa Visual Studio, selezionare il progetto Server in **Esplora soluzioni** e selezionare il pulsante **Esegui** nella barra degli strumenti o avviare l'app dal menu **Debug.**

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>Risorse aggiuntive

* [Richiedere token di accesso aggiuntiviRequest additional access tokens](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* <xref:security/authentication/azure-active-directory/index>
* [Documentazione di Microsoft Identity Platform](/azure/active-directory/develop/)
