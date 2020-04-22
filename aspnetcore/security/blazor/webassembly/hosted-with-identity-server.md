---
title: Proteggere un'app ospitata ASP.NET Core WebAssembly con Identity ServerSecure an ASP.NET Core Blazor WebAssembly hosted app with Identity Server
author: guardrex
description: Per creare Blazor una nuova app ospitata con autenticazione da Visual Studio che usa un back-end [IdentityServerTo](https://identityserver.io/) create a new hosted app with authentication from within Visual Studio that uses an IdentityServer backend
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/30/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/hosted-with-identity-server
ms.openlocfilehash: 4c51200159ced16132e15bb4a1f0915ca0cf5945
ms.sourcegitcommit: c9d1208e86160615b2d914cce74a839ae41297a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81791623"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-hosted-app-with-identity-server"></a>Proteggere un'app ospitata ASP.NET Core WebAssembly con Identity ServerSecure an ASP.NET Core Blazor WebAssembly hosted app with Identity Server

Di [Javier Calvarro Nelson](https://github.com/javiercn) e [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

Per creare Blazor una nuova app ospitata in Visual Studio che usa IdentityServer per autenticare utenti e chiamate API:To create a new hosted app in Visual Studio that uses [IdentityServer](https://identityserver.io/) to authenticate users and API calls:

1. Usare Visual Studio per ** Blazor ** creare una nuova app WebAssembly. Per altre informazioni, vedere <xref:blazor/get-started>.
1. Nella finestra di dialogo **Crea una nuova app Blazor ** selezionare Modifica nella sezione **Autenticazione.In** the Create a new app dialog, select **Change** in the Authentication section.
1. Selezionare **Singoli account utente** seguiti da **OK**.
1. Selezionare la casella di **controllo ASP.NET Core ospitato** nella sezione **Avanzate.Select** the Core hosted checkbox in the Advanced section.
1. Selezionare il pulsante **Crea**.

Per creare l'app in una shell dei comandi, eseguire il comando seguente:

```dotnetcli
dotnet new blazorwasm -au Individual -ho
```

Per specificare il percorso di output, che crea una cartella di progetto se non esiste, `-o BlazorSample`includere l'opzione di output nel comando con un percorso, ad esempio ). Il nome della cartella diventa anche parte del nome del progetto.

## <a name="server-app-configuration"></a>Configurazione dell'app server

Nelle sezioni seguenti vengono descritte le aggiunte al progetto quando è incluso il supporto dell'autenticazione.

### <a name="startup-class"></a>Classe di avvio

La `Startup` classe ha le seguenti aggiunte:

* In `Startup.ConfigureServices`:

  * Identità:

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>()
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * IdentityServer con <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> un metodo helper aggiuntivo che imposta alcune convenzioni predefinite ASP.NET Core sopra IdentityServer:

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * Autenticazione con <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> un metodo helper aggiuntivo che configura l'app per convalidare i token JWT prodotti da IdentityServer:Authentication with an additional helper method that configures the app to validate JWT tokens produced by IdentityServer:

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* In `Startup.Configure`:

  * Middleware di autenticazione responsabile della convalida delle credenziali della richiesta e dell'impostazione dell'utente nel contesto della richiesta:

    ```csharp
    app.UseAuthentication();
    ```

  * Middleware di IdentityServer che espone gli endpoint Open ID Connect (OIDC):

    ```csharp
    app.UseIdentityServer();
    ```

### <a name="addapiauthorization"></a>AddApiAuthorization

Il <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> metodo helper configura [IdentityServer](https://identityserver.io/) per gli scenari di ASP.NET Core. IdentityServer è un framework potente ed estensibile per la gestione dei problemi di sicurezza delle app. IdentityServer espone la complessità non necessaria per gli scenari più comuni. Di conseguenza, viene fornito un insieme di convenzioni e opzioni di configurazione che consideriamo un buon punto di partenza. Una volta modificate le esigenze di autenticazione, la potenza completa di IdentityServer è ancora disponibile per personalizzare l'autenticazione in base ai requisiti di un'app.

### <a name="addidentityserverjwt"></a>AddIdentityServerJwt

Il <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> metodo helper configura uno schema di criteri per l'app come gestore di autenticazione predefinito. Il criterio è configurato per consentire a Identity di gestire tutte `/Identity`le richieste indirizzate a qualsiasi sottopercorso nello spazio URL identità. L'oggetto <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> gestisce tutte le altre richieste. Inoltre, questo metodo:

* Registra una `{APPLICATION NAME}API` risorsa API con IdentityServer `{APPLICATION NAME}API`con un ambito predefinito di .
* Configura il middleware del token di bearer JWT per convalidare i token emessi da IdentityServer per l'app.

### <a name="weatherforecastcontroller"></a>WeatherForecastController

In `WeatherForecastController` (*Controllers/WeatherForecastController.cs* [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) ), l'attributo viene applicato alla classe . L'attributo indica che l'utente deve essere autorizzato in base ai criteri predefiniti per accedere alla risorsa. Il criterio di autorizzazione predefinito è configurato per utilizzare <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> lo schema di autenticazione predefinito, che viene impostato dallo schema di criteri menzionato in precedenza. Il metodo helper <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> viene configurato come gestore predefinito per le richieste all'app.

### <a name="applicationdbcontext"></a>ApplicationDbContext

In `ApplicationDbContext` (*Data/ApplicationDbContext.cs*), <xref:Microsoft.EntityFrameworkCore.DbContext> lo stesso viene utilizzato in <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> Identity con l'eccezione che estende per includere lo schema per IdentityServer. L'oggetto <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> è derivato da <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>.

Per ottenere il controllo completo dello schema del <xref:Microsoft.EntityFrameworkCore.DbContext> database, ereditare da una delle `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` classi `OnModelCreating` Identity disponibili e configurare il contesto in modo da includere lo schema Identity chiamando nel metodo .

### <a name="oidcconfigurationcontroller"></a>OidcConfigurationController

In `OidcConfigurationController` (*Controllers/OidcConfigurationController.cs*) viene eseguito il provisioning dell'endpoint client per la gestione dei parametri OIDC.

### <a name="app-settings-files"></a>File di impostazioni dell'app

Nel file di impostazioni dell'app (*appsettings.json*) nella radice del progetto, la `IdentityServer` sezione descrive l'elenco dei client configurati. Nell'esempio seguente è presente un singolo client. Il nome del client corrisponde al nome dell'app `ClientId` ed è mappato per convenzione al parametro OAuth. Il profilo indica il tipo di app da configurare. Il profilo viene utilizzato internamente per guidare le convenzioni che semplificano il processo di configurazione per il server. <!-- There are several profiles available, as explained in the [Application profiles](#application-profiles) section. -->

```json
"IdentityServer": {
  "Clients": {
    "BlazorApplicationWithAuthentication.Client": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

Nel file di impostazioni dell'app Ambiente di sviluppo *(impostazioni app. Development.json*) nella radice `IdentityServer` del progetto, la sezione descrive la chiave utilizzata per firmare i token. <!-- When deploying to production, a key needs to be provisioned and deployed alongside the app, as explained in the [Deploy to production](#deploy-to-production) section. -->

```json
"IdentityServer": {
  "Key": {
    "Type": "Development"
  }
}
```

## <a name="client-app-configuration"></a>Configurazione dell'app client

### <a name="authentication-package"></a>Pacchetto di autenticazione

Quando viene creata un'app per`Individual`usare singoli account utente ( `Microsoft.AspNetCore.Components.WebAssembly.Authentication` ), l'app riceve automaticamente un riferimento al pacchetto per il pacchetto nel file di progetto dell'app. Il pacchetto fornisce un set di primitive che consentono all'app di autenticare gli utenti e ottenere token per chiamare API protette.

Se si aggiunge l'autenticazione a un'app, aggiungi manualmente il pacchetto al file di progetto dell'app:

```xml
<PackageReference 
    Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
    Version="{VERSION}" />
```

Sostituire `{VERSION}` nel riferimento al pacchetto precedente `Microsoft.AspNetCore.Blazor.Templates` con la <xref:blazor/get-started> versione del pacchetto illustrata nell'articolo.

### <a name="api-authorization-support"></a>Supporto per l'autorizzazione API

Il supporto per l'autenticazione degli utenti viene inserito nel `Microsoft.AspNetCore.Components.WebAssembly.Authentication` contenitore dei servizi dal metodo di estensione fornito all'interno del pacchetto. Questo metodo imposta tutti i servizi necessari per l'interazione dell'app con il sistema di autorizzazione esistente.

```csharp
builder.Services.AddApiAuthorization();
```

Per impostazione predefinita, carica la configurazione `_configuration/{client-id}`per l'app per convenzione da . Per convenzione, l'ID client è impostato sul nome dell'assembly dell'app. Questo URL può essere modificato in modo da puntare a un endpoint separato chiamando l'overload con le opzioni.

### <a name="imports-file"></a>File di importazione

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a>Pagina di indice

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

### <a name="app-component"></a>Componente dell'app

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a>Componente RedirectToLogin

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a>Componente LoginDisplay

Il `LoginDisplay` rendering del `MainLayout` componente (*Shared/LoginDisplay.razor*) viene eseguito nel componente (*Shared/MainLayout.razor*) e gestisce i comportamenti seguenti:

* Per gli utenti autenticati:
  * Visualizza il nome utente corrente.
  * Offre un collegamento alla pagina del profilo utente in ASP.NET Core Identity.
  * Offre un pulsante per uscire dall'app.
* Per gli utenti anonimi:
  * Offre la possibilità di registrarsi.
  * Offre la possibilità di effettuare il login.

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

Eseguire l'app dal progetto Server. Quando si usa Visual Studio, selezionare il progetto Server in **Esplora soluzioni** e selezionare il pulsante **Esegui** nella barra degli strumenti o avviare l'app dal menu **Debug.**

[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>Risorse aggiuntive

* [Richiedere token di accesso aggiuntiviRequest additional access tokens](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
