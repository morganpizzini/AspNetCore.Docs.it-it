---
title: Introduzione all'autenticazione per le app a pagina singola in ASP.NET Core
author: javiercn
description: IdentityDa usare con un'app a singola pagina ospitata in un'app ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: scaddie
ms.custom: mvc
ms.date: 11/08/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/identity/spa
ms.openlocfilehash: 26d371161bf5f926e50cbc141ccfaac40ee96977
ms.sourcegitcommit: ff5c47beded9264c1395beb9c905f826261f3ba3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/16/2020
ms.locfileid: "83440178"
---
# <a name="authentication-and-authorization-for-spas"></a>Autenticazione e autorizzazione per le ZPS

ASP.NET Core 3,0 o versione successiva offre l'autenticazione in app a pagina singola (Spa) usando il supporto per l'autorizzazione dell'API. ASP.NET Core Identity per l'autenticazione e l'archiviazione degli utenti è combinato con [IdentityServer](https://identityserver.io/) per l'implementazione di Open ID Connect.

Un parametro di autenticazione è stato aggiunto ai modelli di progetto **angolari** e **React** , che è simile al parametro Authentication nei modelli di progetto **applicazione Web** (MVC) e **applicazione Web** ( Razor pagine). I valori dei parametri consentiti sono **None** e **individual**. Il modello di progetto **React. js e Redux** non supporta il parametro Authentication al momento.

## <a name="create-an-app-with-api-authorization-support"></a>Creare un'app con supporto per l'autorizzazione API

L'autenticazione e l'autorizzazione degli utenti possono essere usate sia con le ZPS angolari che React. Aprire una shell dei comandi ed eseguire il comando seguente:

**Angolare**:

```dotnetcli
dotnet new angular -o <output_directory_name> -au Individual
```

**Reagire**:

```dotnetcli
dotnet new react -o <output_directory_name> -au Individual
```

Il comando precedente crea un'app ASP.NET Core con una directory *ClientApp* contenente la Spa.

## <a name="general-description-of-the-aspnet-core-components-of-the-app"></a>Descrizione generale dei componenti ASP.NET Core dell'app

Le sezioni seguenti descrivono le aggiunte al progetto quando è incluso il supporto per l'autenticazione:

### <a name="startup-class"></a>Classe di avvio

La `Startup` classe presenta le aggiunte seguenti:

* All'interno del `Startup.ConfigureServices` Metodo:
  * Identitycon l'interfaccia utente predefinita:

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>()
        .AddDefaultUI(UIFramework.Bootstrap4)
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * IdentityServer con un `AddApiAuthorization` metodo helper aggiuntivo che configura alcune convenzioni ASP.NET Core predefinite in IdentityServer:

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * Autenticazione con un `AddIdentityServerJwt` metodo helper aggiuntivo che configura l'app per convalidare i token JWT prodotti da IdentityServer:

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* All'interno del `Startup.Configure` Metodo:
  * Il middleware di autenticazione responsabile della convalida delle credenziali della richiesta e dell'impostazione dell'utente nel contesto della richiesta:

    ```csharp
    app.UseAuthentication();
    ```

  * Il middleware IdentityServer che espone gli endpoint di connessione Open ID:

    ```csharp
    app.UseIdentityServer();
    ```

### <a name="addapiauthorization"></a>AddApiAuthorization

Questo metodo helper configura IdentityServer in modo da usare la configurazione supportata. IdentityServer è un Framework potente ed estendibile per la gestione dei problemi di sicurezza delle app. Allo stesso tempo, che espone complessità superflua per gli scenari più comuni. Di conseguenza, viene fornito un set di convenzioni e opzioni di configurazione che sono considerati un valido punto di partenza. Una volta modificate le esigenze di autenticazione, è ancora disponibile la potenza completa di IdentityServer per personalizzare l'autenticazione in base alle esigenze.

### <a name="addidentityserverjwt"></a>AddIdentityServerJwt

Questo metodo helper configura uno schema di criteri per l'app come gestore di autenticazione predefinito. Il criterio è configurato in modo da consentire la Identity gestione di tutte le richieste indirizzate a qualsiasi sottopercorso nello Identity spazio URL "/ Identity ". `JwtBearerHandler`Gestisce tutte le altre richieste. Questo metodo registra inoltre una `<<ApplicationName>>API` risorsa API con IdentityServer con un ambito predefinito `<<ApplicationName>>API` e configura il middleware del token di porta JWT per convalidare i token emessi da IdentityServer per l'app.

### <a name="weatherforecastcontroller"></a>WeatherForecastController

Nel file *Controllers\WeatherForecastController.cs* , si noti l' `[Authorize]` attributo applicato alla classe che indica che l'utente deve essere autorizzato in base ai criteri predefiniti per accedere alla risorsa. I criteri di autorizzazione predefiniti vengono configurati per l'utilizzo dello schema di autenticazione predefinito, configurato da `AddIdentityServerJwt` allo schema dei criteri menzionato in precedenza, rendendo `JwtBearerHandler` configurato da tale metodo helper il gestore predefinito per le richieste all'app.

### <a name="applicationdbcontext"></a>ApplicationDbContext

Nel file *Data\ApplicationDbContext.cs* si noti che lo stesso `DbContext` viene usato in Identity con l'eccezione che estende `ApiAuthorizationDbContext` (una classe più derivata da `IdentityDbContext` ) per includere lo schema per IdentityServer.

Per ottenere il controllo completo dello schema del database, ereditare da una delle Identity `DbContext` classi disponibili e configurare il contesto per includere lo Identity schema chiamando `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` sul `OnModelCreating` metodo.

### <a name="oidcconfigurationcontroller"></a>OidcConfigurationController

Nel file *Controllers\OidcConfigurationController.cs* , si noti l'endpoint di cui è stato effettuato il provisioning per gestire i parametri di OIDC che il client deve usare.

### <a name="appsettingsjson"></a>appsettings.json

Nel file *appSettings. JSON* della radice del progetto è presente una nuova `IdentityServer` sezione che descrive l'elenco dei client configurati. Nell'esempio seguente è presente un singolo client. Il nome del client corrisponde al nome dell'app e viene mappato per convenzione al `ClientId` parametro OAuth. Il profilo indica il tipo di app da configurare. Viene usato internamente per guidare le convenzioni che semplificano il processo di configurazione per il server. Sono disponibili diversi profili, come illustrato nella sezione [profili applicazione](#application-profiles) .

```json
"IdentityServer": {
  "Clients": {
    "angularindividualpreview3final": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

### <a name="appsettingsdevelopmentjson"></a>appSettings. Development. JSON

In *appSettings. File Development. JSON* della radice del progetto, è presente una `IdentityServer` sezione che descrive la chiave usata per firmare i token. Quando si esegue la distribuzione nell'ambiente di produzione, è necessario eseguire il provisioning di una chiave e distribuirla insieme all'app, come illustrato nella sezione [Deploy to Production](#deploy-to-production) .

```json
"IdentityServer": {
  "Key": {
    "Type": "Development"
  }
}
```

## <a name="general-description-of-the-angular-app"></a>Descrizione generale dell'app angolare

Il supporto per l'autenticazione e l'autorizzazione API nel modello angolare si trova nel proprio modulo angolare nella directory *ClientApp\src\api-Authorization* Il modulo è costituito dagli elementi seguenti:

* 3 componenti:
  * *login. Component. TS*: gestisce il flusso di accesso dell'app.
  * *Logout. Component. TS*: gestisce il flusso di disconnessione dell'app.
  * *login-menu. Component. TS*: widget che visualizza uno dei seguenti set di collegamenti:
    * Collegamenti di gestione dei profili utente e disconnessione quando l'utente viene autenticato.
    * Collegamenti per la registrazione e l'accesso quando l'utente non è autenticato.
* Una protezione `AuthorizeGuard` della route che può essere aggiunta alle route e richiede che un utente venga autenticato prima di visitare la route.
* Un intercettore HTTP `AuthorizeInterceptor` che connette il token di accesso alle richieste HTTP in uscita destinate all'API quando l'utente viene autenticato.
* Un servizio `AuthorizeService` che gestisce i dettagli di basso livello del processo di autenticazione ed espone le informazioni relative all'utente autenticato al resto dell'app per l'utilizzo.
* Un modulo angolare che definisce le route associate alle parti di autenticazione dell'app. Espone il componente del menu di accesso, l'intercettore, il Guard e il servizio per l'utilizzo dal resto dell'app.

## <a name="general-description-of-the-react-app"></a>Descrizione generale dell'app React

Il supporto per l'autenticazione e l'autorizzazione API nel modello React risiede nella directory *ClientApp\src\components\api-Authorization* È costituito dagli elementi seguenti:

* 4 componenti:
  * *Login. js*: gestisce il flusso di accesso dell'app.
  * *Logout. js*: gestisce il flusso di disconnessione dell'app.
  * *LoginMenu. js*: widget che visualizza uno dei seguenti set di collegamenti:
    * Collegamenti di gestione dei profili utente e disconnessione quando l'utente viene autenticato.
    * Collegamenti per la registrazione e l'accesso quando l'utente non è autenticato.
  * *AuthorizeRoute. js*: componente di route che richiede l'autenticazione di un utente prima di eseguire il rendering del componente indicato nel `Component` parametro.
* Istanza esportata `authService` della classe `AuthorizeService` che gestisce i dettagli di basso livello del processo di autenticazione ed espone le informazioni relative all'utente autenticato al resto dell'app per l'utilizzo.

Ora che sono stati esaminati i componenti principali della soluzione, è possibile approfondire i singoli scenari per l'app.

## <a name="require-authorization-on-a-new-api"></a>Richiedi autorizzazione per una nuova API

Per impostazione predefinita, il sistema è configurato in modo da richiedere facilmente l'autorizzazione per le nuove API. A tale scopo, creare un nuovo controller e aggiungere l' `[Authorize]` attributo alla classe controller o a qualsiasi azione all'interno del controller.

## <a name="customize-the-api-authentication-handler"></a>Personalizzare il gestore di autenticazione API

Per personalizzare la configurazione del gestore JWT dell'API, configurare la relativa <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> istanza:

```csharp
services.AddAuthentication()
    .AddIdentityServerJwt();

services.Configure<JwtBearerOptions>(
    IdentityServerJwtConstants.IdentityServerJwtBearerScheme,
    options =>
    {
        ...
    });
```

Il gestore JWT dell'API genera eventi che consentono di controllare il processo di autenticazione tramite `JwtBearerEvents` . Per fornire il supporto per l'autorizzazione dell'API, `AddIdentityServerJwt` registra i relativi gestori eventi.

Per personalizzare la gestione di un evento, eseguire il wrapping del gestore eventi esistente con logica aggiuntiva, se necessario. Ad esempio:

```csharp
services.Configure<JwtBearerOptions>(
    IdentityServerJwtConstants.IdentityServerJwtBearerScheme,
    options =>
    {
        var onTokenValidated = options.Events.OnTokenValidated;       
        
        options.Events.OnTokenValidated = async context =>
        {
            await onTokenValidated(context);
            ...
        }
    });
```

Nel codice precedente, il `OnTokenValidated` gestore eventi viene sostituito con un'implementazione personalizzata. Questa implementazione:

1. Chiama l'implementazione originale fornita dal supporto dell'autorizzazione dell'API.
1. Eseguire la propria logica personalizzata.

## <a name="protect-a-client-side-route-angular"></a>Proteggere una route lato client (angolare)

Per la protezione di una route sul lato client, è necessario aggiungere l'autorizzazione Guard all'elenco delle protezioni da eseguire durante la configurazione di una route. Come esempio, è possibile vedere come la `fetch-data` Route è configurata nel modulo principale dell'app:

```typescript
RouterModule.forRoot([
  // ...
  { path: 'fetch-data', component: FetchDataComponent, canActivate: [AuthorizeGuard] },
])
```

È importante ricordare che la protezione di una route non protegge l'endpoint effettivo (a cui è ancora necessario `[Authorize]` applicare un attributo) ma impedisce solo all'utente di passare alla route specificata sul lato client quando non è autenticato.

## <a name="authenticate-api-requests-angular"></a>Autenticare le richieste API (angolare)

L'autenticazione delle richieste alle API ospitate insieme all'app viene eseguita automaticamente tramite l'uso dell'intercettore client HTTP definito dall'app.

## <a name="protect-a-client-side-route-react"></a>Proteggere una route lato client (React)

Proteggere una route sul lato client usando il `AuthorizeRoute` componente anziché il `Route` componente normale. Si noti, ad esempio, il modo in cui la `fetch-data` Route è configurata all'interno del `App` componente:

```jsx
<AuthorizeRoute path='/fetch-data' component={FetchData} />
```

Protezione di una route:

* Non protegge l'endpoint effettivo (a cui è ancora necessario `[Authorize]` applicare un attributo).
* Impedisce solo all'utente di passare alla route specificata sul lato client quando non viene autenticata.

## <a name="authenticate-api-requests-react"></a>Autenticare le richieste API (React)

L'autenticazione delle richieste con React viene eseguita importando innanzitutto l' `authService` istanza da `AuthorizeService` . Il token di accesso viene recuperato da `authService` ed è associato alla richiesta, come illustrato di seguito. Nei componenti React questa operazione viene in genere eseguita nel `componentDidMount` Metodo Lifecycle o come risultato di un'interazione dell'utente.

### <a name="import-the-authservice-into-your-component"></a>Importare authService nel componente

```javascript
import authService from './api-authorization/AuthorizeService'
```

### <a name="retrieve-and-attach-the-access-token-to-the-response"></a>Recupera e alleghi il token di accesso alla risposta

```javascript
async populateWeatherData() {
  const token = await authService.getAccessToken();
  const response = await fetch('api/SampleData/WeatherForecasts', {
    headers: !token ? {} : { 'Authorization': `Bearer ${token}` }
  });
  const data = await response.json();
  this.setState({ forecasts: data, loading: false });
}
```

## <a name="deploy-to-production"></a>Distribuire nell'ambiente di produzione

Per distribuire l'app nell'ambiente di produzione, è necessario eseguire il provisioning delle risorse seguenti:

* Un database in cui archiviare gli Identity account utente e le concessioni IdentityServer.
* Un certificato di produzione da usare per firmare i token.
  * Non sono previsti requisiti specifici per il certificato. può essere un certificato autofirmato o un certificato sottoposti a provisioning tramite un'autorità di certificazione.
  * Può essere generato tramite strumenti standard come PowerShell o OpenSSL.
  * Può essere installata nell'archivio certificati nei computer di destinazione o distribuita come file con *estensione pfx* con una password complessa.

### <a name="example-deploy-to-azure-app-service"></a>Esempio: eseguire la distribuzione nel servizio app Azure

Questa sezione descrive come distribuire l'app nel servizio app Azure usando un certificato archiviato nell'archivio certificati. Per modificare l'app per caricare un certificato dall'archivio certificati, è necessario un piano di servizio di livello standard o superiore quando si configura l'app nel portale di Azure in un passaggio successivo.

Nel file *appSettings. JSON* dell'app modificare la `IdentityServer` sezione in modo da includere i dettagli della chiave:

```json
"IdentityServer": {
  "Key": {
    "Type": "Store",
    "StoreName": "My",
    "StoreLocation": "CurrentUser",
    "Name": "CN=MyApplication"
  }
}
```

* Il nome dell'archivio rappresenta il nome dell'archivio certificati in cui è archiviato il certificato. In questo caso, fa riferimento all'archivio utenti personali.
* Il percorso dell'archivio rappresenta il punto da cui caricare il certificato ( `CurrentUser` o `LocalMachine` ).
* La proprietà Name del certificato corrisponde all'oggetto distinto per il certificato.

Per eseguire la distribuzione nel servizio app Azure, seguire la procedura descritta in [distribuire l'app in Azure](xref:tutorials/publish-to-azure-webapp-using-vs#deploy-the-app-to-azure), che spiega come creare le risorse di Azure necessarie e distribuire l'app nell'ambiente di produzione.

Dopo aver seguito le istruzioni precedenti, l'app viene distribuita in Azure, ma non è ancora funzionante. Il certificato usato dall'app deve essere configurato nel portale di Azure. Individuare l'identificazione personale per il certificato e seguire i passaggi descritti in [caricare i certificati](/azure/app-service/app-service-web-ssl-cert-load#load-the-certificate-in-code).

Sebbene questi passaggi menzionino SSL, nella portale di Azure è possibile caricare il certificato di cui è stato **effettuato il** provisioning da usare con l'app.

Dopo aver configurato l'app e le impostazioni dell'app nel portale di Azure, riavviare l'app nel portale.

## <a name="other-configuration-options"></a>Altre opzioni di configurazione

Il supporto per l'autorizzazione API si basa su IdentityServer con un set di convenzioni, valori predefiniti e miglioramenti per semplificare l'esperienza per le Spa. Inutile dire che la potenza completa di IdentityServer è disponibile dietro le quinte se le integrazioni ASP.NET Core non coprono lo scenario. Il supporto ASP.NET Core si concentra sulle app "First-Party", in cui tutte le app vengono create e distribuite dall'organizzazione. Di conseguenza, il supporto non viene offerto per elementi come il consenso o la Federazione. Per questi scenari, usare IdentityServer e seguire la relativa documentazione.

### <a name="application-profiles"></a>Profili applicazione

I profili dell'applicazione sono configurazioni predefinite per le app che definiscono ulteriormente i parametri. A questo punto sono supportati i profili seguenti:

* `IdentityServerSPA`: Rappresenta una SPA ospitata insieme a IdentityServer come singola unità.
  * Il `redirect_uri` valore predefinito è `/authentication/login-callback` .
  * Il `post_logout_redirect_uri` valore predefinito è `/authentication/logout-callback` .
  * Il set di ambiti include `openid` , `profile` e ogni ambito definito per le API nell'app.
  * Il set di tipi di risposta OIDC consentiti è `id_token token` o singolarmente ( `id_token` , `token` ).
  * La modalità di risposta consentita è `fragment` .
* `SPA`: Rappresenta una SPA non ospitata con IdentityServer.
  * Il set di ambiti include `openid` , `profile` e ogni ambito definito per le API nell'app.
  * Il set di tipi di risposta OIDC consentiti è `id_token token` o singolarmente ( `id_token` , `token` ).
  * La modalità di risposta consentita è `fragment` .
* `IdentityServerJwt`: Rappresenta un'API ospitata insieme a IdentityServer.
  * L'app è configurata in modo da avere un singolo ambito per impostazione predefinita sul nome dell'app.
* `API`: Rappresenta un'API non ospitata con IdentityServer.
  * L'app è configurata in modo da avere un singolo ambito per impostazione predefinita sul nome dell'app.

### <a name="configuration-through-appsettings"></a>Configurazione tramite AppSettings

Configurare le app tramite il sistema di configurazione aggiungendole all'elenco di `Clients` o `Resources` .

Configurare `redirect_uri` la proprietà e di ogni client `post_logout_redirect_uri` , come illustrato nell'esempio seguente:

```json
"IdentityServer": {
  "Clients": {
    "MySPA": {
      "Profile": "SPA",
      "RedirectUri": "https://www.example.com/authentication/login-callback",
      "LogoutUri": "https://www.example.com/authentication/logout-callback"
    }
  }
}
```

Quando si configurano le risorse, è possibile configurare gli ambiti per la risorsa come illustrato di seguito:

```json
"IdentityServer": {
  "Resources": {
    "MyExternalApi": {
      "Profile": "API",
      "Scopes": "a b c"
    }
  }
}
```

### <a name="configuration-through-code"></a>Configurazione tramite codice

È anche possibile configurare i client e le risorse tramite codice usando un overload di `AddApiAuthorization` che esegue un'azione per configurare le opzioni.

```csharp
AddApiAuthorization<ApplicationUser, ApplicationDbContext>(options =>
{
    options.Clients.AddSPA(
        "My SPA", spa =>
        spa.WithRedirectUri("http://www.example.com/authentication/login-callback")
           .WithLogoutRedirectUri(
               "http://www.example.com/authentication/logout-callback"));

    options.ApiResources.AddApiResource("MyExternalApi", resource =>
        resource.WithScopes("a", "b", "c"));
});
```

## <a name="additional-resources"></a>Risorse aggiuntive

* <xref:spa/angular>
* <xref:spa/react>
* <xref:security/authentication/scaffold-identity>
