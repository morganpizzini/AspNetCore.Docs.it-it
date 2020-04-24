---
title: Proteggere un'app Blazor ospitata in un ASP.NET Core webassembly con Identity Server
author: guardrex
description: Per creare una nuova Blazor app ospitata con autenticazione da Visual Studio che usa un back-end [IdentityServer](https://identityserver.io/)
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/24/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/hosted-with-identity-server
ms.openlocfilehash: ffdcd30ae9ce5350113569a500e99cf8db82ad65
ms.sourcegitcommit: 4f91da9ce4543b39dba5e8920a9500d3ce959746
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82138606"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-hosted-app-with-identity-server"></a>Proteggere un'app Blazor ospitata in un ASP.NET Core webassembly con Identity Server

Di [Javier Calvarro Nelson](https://github.com/javiercn) e [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

Per creare una nuova Blazor app ospitata in Visual Studio che usa [IdentityServer](https://identityserver.io/) per autenticare gli utenti e le chiamate API:

1. Usare Visual Studio per creare una nuova ** Blazor app webassembly** . Per altre informazioni, vedere <xref:blazor/get-started>.
1. Nella finestra di dialogo **Crea Blazor una nuova app** Selezionare **modifica** nella sezione **autenticazione** .
1. Selezionare **account utente singoli** e quindi **OK**.
1. Nella sezione **Avanzate** Selezionare la casella di controllo **ASP.NET Core Hosted** .
1. Selezionare il pulsante **Crea**.

Per creare l'app in una shell dei comandi, eseguire il comando seguente:

```dotnetcli
dotnet new blazorwasm -au Individual -ho
```

Per specificare il percorso di output, che crea una cartella di progetto, se non esiste, includere l'opzione di output nel comando con un percorso (ad `-o BlazorSample`esempio,). Il nome della cartella diventa anche parte del nome del progetto.

## <a name="server-app-configuration"></a>Configurazione dell'app Server

Le sezioni seguenti descrivono le aggiunte al progetto quando è incluso il supporto per l'autenticazione.

### <a name="startup-class"></a>Classe di avvio

La `Startup` classe presenta le aggiunte seguenti:

* In `Startup.ConfigureServices`:

  * Identità:

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(
            Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>(options => 
            options.SignIn.RequireConfirmedAccount = true)
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * IdentityServer con un metodo <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> Helper aggiuntivo che configura alcune convenzioni ASP.NET Core predefinite in IdentityServer:

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * Autenticazione con un metodo <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> Helper aggiuntivo che configura l'app per convalidare i token JWT prodotti da IdentityServer:

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* In `Startup.Configure`:

  * Il middleware di autenticazione responsabile della convalida delle credenziali della richiesta e dell'impostazione dell'utente nel contesto della richiesta:

    ```csharp
    app.UseAuthentication();
    ```

  * Il middleware IdentityServer che espone gli endpoint Open ID Connect (OIDC):

    ```csharp
    app.UseIdentityServer();
    ```

  * Middleware di autenticazione e autorizzazione:

    ```csharp
    app.UseAuthentication();
    app.UseAuthorization();
    ```

### <a name="addapiauthorization"></a>AddApiAuthorization

Il <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> metodo helper Configura [IdentityServer](https://identityserver.io/) per scenari ASP.NET Core. IdentityServer è un Framework potente ed estendibile per la gestione dei problemi di sicurezza delle app. IdentityServer espone una complessità superflua per gli scenari più comuni. Di conseguenza, viene fornito un set di convenzioni e opzioni di configurazione che consideriamo un valido punto di partenza. Una volta modificate le esigenze di autenticazione, è ancora disponibile la potenza completa di IdentityServer per personalizzare l'autenticazione per soddisfare i requisiti di un'app.

### <a name="addidentityserverjwt"></a>AddIdentityServerJwt

Il <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> metodo helper configura uno schema di criteri per l'app come gestore di autenticazione predefinito. Il criterio è configurato in modo da consentire all'identità di gestire tutte le richieste indirizzate a qualsiasi sottopercorso nello `/Identity`spazio URL identità. <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> Gestisce tutte le altre richieste. Inoltre, questo metodo:

* Registra una `{APPLICATION NAME}API` risorsa API con IdentityServer con un ambito predefinito `{APPLICATION NAME}API`.
* Configura il middleware del token di porta JWT per convalidare i token emessi da IdentityServer per l'app.

### <a name="weatherforecastcontroller"></a>WeatherForecastController

In `WeatherForecastController` (*Controllers/WeatherForecastController. cs*) l' [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attributo viene applicato alla classe. L'attributo indica che l'utente deve essere autorizzato in base ai criteri predefiniti per accedere alla risorsa. I criteri di autorizzazione predefiniti sono configurati per l'utilizzo dello schema di autenticazione predefinito, <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> configurato in base allo schema dei criteri indicato in precedenza. Il metodo helper viene configurato <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> come gestore predefinito per le richieste all'app.

### <a name="applicationdbcontext"></a>ApplicationDbContext

In `ApplicationDbContext` (*Data/ApplicationDbContext. cs*) lo stesso <xref:Microsoft.EntityFrameworkCore.DbContext> viene usato nell'identità con l'eccezione che estende <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> per includere lo schema per IdentityServer. L'oggetto <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> è derivato da <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>.

Per ottenere il controllo completo dello schema del database, ereditare da una delle classi <xref:Microsoft.EntityFrameworkCore.DbContext> di identità disponibili e configurare il contesto per includere lo schema di `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` identità chiamando `OnModelCreating` nel metodo.

### <a name="oidcconfigurationcontroller"></a>OidcConfigurationController

In `OidcConfigurationController` (*Controllers/OidcConfigurationController. cs*), viene eseguito il provisioning dell'endpoint client per gestire i parametri di OIDC.

### <a name="app-settings-files"></a>File di impostazioni dell'app

Nel file di impostazioni dell'app (*appSettings. JSON*) nella radice del progetto, `IdentityServer` la sezione descrive l'elenco dei client configurati. Nell'esempio seguente è presente un singolo client. Il nome del client corrisponde al nome dell'app e viene mappato per convenzione al `ClientId` parametro OAuth. Il profilo indica il tipo di app da configurare. Il profilo viene utilizzato internamente per guidare le convenzioni che semplificano il processo di configurazione per il server. <!-- There are several profiles available, as explained in the [Application profiles](#application-profiles) section. -->

```json
"IdentityServer": {
  "Clients": {
    "{APP ASSEMBLY}.Client": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

## <a name="client-app-configuration"></a>Configurazione dell'app client

### <a name="authentication-package"></a>Pacchetto di autenticazione

Quando viene creata un'app per l'`Individual` `Microsoft.AspNetCore.Components.WebAssembly.Authentication` uso di singoli account utente (), l'app riceve automaticamente un riferimento al pacchetto nel file di progetto dell'app. Il pacchetto fornisce un set di primitive che consentono all'app di autenticare gli utenti e ottenere i token per chiamare le API protette.

Se si aggiunge l'autenticazione a un'app, aggiungere manualmente il pacchetto al file di progetto dell'app:

```xml
<PackageReference 
    Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
    Version="{VERSION}" />
```

Sostituire `{VERSION}` nel riferimento al pacchetto precedente con la versione del `Microsoft.AspNetCore.Blazor.Templates` pacchetto illustrata nell' <xref:blazor/get-started> articolo.

### <a name="api-authorization-support"></a>Supporto dell'autorizzazione API

Il supporto per l'autenticazione degli utenti è collegato al contenitore del servizio mediante il metodo di estensione fornito nel `Microsoft.AspNetCore.Components.WebAssembly.Authentication` pacchetto. Questo metodo configura tutti i servizi necessari per l'interazione dell'app con il sistema di autorizzazione esistente.

```csharp
builder.Services.AddApiAuthorization();
```

Per impostazione predefinita, carica la configurazione per l'app per convenzione da `_configuration/{client-id}`. Per convenzione, l'ID client è impostato sul nome dell'assembly dell'app. Questo URL può essere modificato in modo che punti a un endpoint separato chiamando l'overload con options.

### <a name="imports-file"></a>Importa file

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a>Pagina di indice

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

### <a name="app-component"></a>Componente app

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a>Componente RedirectToLogin

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a>Componente LoginDisplay

Il `LoginDisplay` componente (*Shared/LoginDisplay. Razor*) viene sottoposto a `MainLayout` rendering nel componente (*Shared/MainLayout. Razor*) e gestisce i comportamenti seguenti:

* Per utenti autenticati:
  * Visualizza il nome utente corrente.
  * Offre un collegamento alla pagina del profilo utente nell'identità del ASP.NET Core.
  * Offre un pulsante per disconnettersi dall'app.
* Per utenti anonimi:
  * Offre la possibilità di effettuare la registrazione.
  * Offre la possibilità di effettuare l'accesso.

```razor
@using Microsoft.AspNetCore.Components.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject NavigationManager Navigation
@inject SignOutSessionStateManager SignOutManager

<AuthorizeView>
    <Authorized>
        <a href="authentication/profile">Hello, @context.User.Identity.Name!</a>
        <button class="nav-link btn btn-link" @onclick="BeginSignOut">
            Log out
        </button>
    </Authorized>
    <NotAuthorized>
        <a href="authentication/register">Register</a>
        <a href="authentication/login">Log in</a>
    </NotAuthorized>
</AuthorizeView>

@code {
    private async Task BeginSignOut(MouseEventArgs args)
    {
        await SignOutManager.SetSignOutState();
        Navigation.NavigateTo("authentication/logout");
    }
}
```

### <a name="authentication-component"></a>Componente di autenticazione

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a>Componente FetchData

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a>Eseguire l'app

Eseguire l'app dal progetto server. Quando si usa Visual Studio, selezionare il progetto server in **Esplora soluzioni** e selezionare il pulsante **Esegui** sulla barra degli strumenti o avviare l'app dal menu **debug** .

[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>Risorse aggiuntive

* <xref:security/blazor/webassembly/additional-scenarios>
