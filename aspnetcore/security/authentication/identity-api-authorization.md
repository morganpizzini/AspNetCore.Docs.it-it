---
title: Introduzione all'autenticazione per le app a pagina singola in ASP.NET Core
author: javiercn
description: IdentityDa usare con un'app a singola pagina ospitata in un'app ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: scaddie
ms.custom: mvc
ms.date: 10/27/2020
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/identity/spa
ms.openlocfilehash: 5a6c160ebdda3ec600980aa839770f4f22a9c2fc
ms.sourcegitcommit: cc405f20537484744423ddaf87bd1e7d82b6bdf0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/21/2021
ms.locfileid: "98658664"
---
# <a name="authentication-and-authorization-for-spas"></a><span data-ttu-id="4d0e3-103">Autenticazione e autorizzazione per le ZPS</span><span class="sxs-lookup"><span data-stu-id="4d0e3-103">Authentication and authorization for SPAs</span></span>

<span data-ttu-id="4d0e3-104">I modelli ASP.NET Core 3,1 e versioni successive offrono l'autenticazione in app a pagina singola (Spa) usando il supporto per l'autorizzazione dell'API.</span><span class="sxs-lookup"><span data-stu-id="4d0e3-104">The ASP.NET Core 3.1 and later templates offer authentication in Single Page Apps (SPAs) using the support for API authorization.</span></span> <span data-ttu-id="4d0e3-105">ASP.NET Core Identityper l'autenticazione e l'archiviazione degli utenti è combinato con [ Identity Server](https://identityserver.io/) per l'implementazione di OpenID Connect.</span><span class="sxs-lookup"><span data-stu-id="4d0e3-105">ASP.NET Core Identity for authenticating and storing users is combined with [IdentityServer](https://identityserver.io/) for implementing OpenID Connect.</span></span>

<span data-ttu-id="4d0e3-106">Un parametro di autenticazione è stato aggiunto ai modelli di progetto **angolari** e **React** , che è simile al parametro Authentication nei modelli di progetto **applicazione Web** (MVC) e **applicazione Web** ( Razor pagine).</span><span class="sxs-lookup"><span data-stu-id="4d0e3-106">An authentication parameter was added to the **Angular** and **React** project templates that is similar to the authentication parameter in the **Web Application (Model-View-Controller)** (MVC) and **Web Application** (Razor Pages) project templates.</span></span> <span data-ttu-id="4d0e3-107">I valori dei parametri consentiti sono **None** e **individual**.</span><span class="sxs-lookup"><span data-stu-id="4d0e3-107">The allowed parameter values are **None** and **Individual**.</span></span> <span data-ttu-id="4d0e3-108">Il modello di progetto **React.js e Redux** non supporta il parametro Authentication al momento.</span><span class="sxs-lookup"><span data-stu-id="4d0e3-108">The **React.js and Redux** project template doesn't support the authentication parameter at this time.</span></span>

## <a name="create-an-app-with-api-authorization-support"></a><span data-ttu-id="4d0e3-109">Creare un'app con supporto per l'autorizzazione API</span><span class="sxs-lookup"><span data-stu-id="4d0e3-109">Create an app with API authorization support</span></span>

<span data-ttu-id="4d0e3-110">L'autenticazione e l'autorizzazione degli utenti possono essere usate sia con le ZPS angolari che React.</span><span class="sxs-lookup"><span data-stu-id="4d0e3-110">User authentication and authorization can be used with both Angular and React SPAs.</span></span> <span data-ttu-id="4d0e3-111">Aprire una shell dei comandi ed eseguire il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="4d0e3-111">Open a command shell, and run the following command:</span></span>

<span data-ttu-id="4d0e3-112">**Angolare**:</span><span class="sxs-lookup"><span data-stu-id="4d0e3-112">**Angular**:</span></span>

```dotnetcli
dotnet new angular -o <output_directory_name> -au Individual
```

<span data-ttu-id="4d0e3-113">**Reagire**:</span><span class="sxs-lookup"><span data-stu-id="4d0e3-113">**React**:</span></span>

```dotnetcli
dotnet new react -o <output_directory_name> -au Individual
```

<span data-ttu-id="4d0e3-114">Il comando precedente crea un'app ASP.NET Core con una directory *ClientApp* contenente la Spa.</span><span class="sxs-lookup"><span data-stu-id="4d0e3-114">The preceding command creates an ASP.NET Core app with a *ClientApp* directory containing the SPA.</span></span>

## <a name="general-description-of-the-aspnet-core-components-of-the-app"></a><span data-ttu-id="4d0e3-115">Descrizione generale dei componenti ASP.NET Core dell'app</span><span class="sxs-lookup"><span data-stu-id="4d0e3-115">General description of the ASP.NET Core components of the app</span></span>

<span data-ttu-id="4d0e3-116">Le sezioni seguenti descrivono le aggiunte al progetto quando è incluso il supporto per l'autenticazione:</span><span class="sxs-lookup"><span data-stu-id="4d0e3-116">The following sections describe additions to the project when authentication support is included:</span></span>

### <a name="startup-class"></a><span data-ttu-id="4d0e3-117">Classe di avvio</span><span class="sxs-lookup"><span data-stu-id="4d0e3-117">Startup class</span></span>

<span data-ttu-id="4d0e3-118">Gli esempi di codice seguenti si basano su [Microsoft. AspNetCore. ApiAuthorization. Identity ](https://www.nuget.org/packages/Microsoft.AspNetCore.ApiAuthorization.IdentityServer) Pacchetto NuGet del server.</span><span class="sxs-lookup"><span data-stu-id="4d0e3-118">The following code examples rely on the [Microsoft.AspNetCore.ApiAuthorization.IdentityServer](https://www.nuget.org/packages/Microsoft.AspNetCore.ApiAuthorization.IdentityServer) NuGet package.</span></span> <span data-ttu-id="4d0e3-119">Gli esempi configurano l'autenticazione e l'autorizzazione dell'API usando i <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiResourceCollection.AddIdentityServerJwt%2A> metodi di estensione e.</span><span class="sxs-lookup"><span data-stu-id="4d0e3-119">The examples configure API authentication and authorization using the <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> and <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiResourceCollection.AddIdentityServerJwt%2A> extension methods.</span></span> <span data-ttu-id="4d0e3-120">I progetti che usano i modelli di progetto REACT o unangolar SPA con autenticazione includono un riferimento a questo pacchetto.</span><span class="sxs-lookup"><span data-stu-id="4d0e3-120">Projects using the React or Angular SPA project templates with authentication include a reference to this package.</span></span>

<span data-ttu-id="4d0e3-121">La `Startup` classe presenta le aggiunte seguenti:</span><span class="sxs-lookup"><span data-stu-id="4d0e3-121">The `Startup` class has the following additions:</span></span>

* <span data-ttu-id="4d0e3-122">All'interno del `Startup.ConfigureServices` Metodo:</span><span class="sxs-lookup"><span data-stu-id="4d0e3-122">Inside the `Startup.ConfigureServices` method:</span></span>
  * <span data-ttu-id="4d0e3-123">Identity con l'interfaccia utente predefinita:</span><span class="sxs-lookup"><span data-stu-id="4d0e3-123">Identity with the default UI:</span></span>

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>()
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * <span data-ttu-id="4d0e3-124">IdentityServer con un `AddApiAuthorization` metodo helper aggiuntivo che configura alcune convenzioni ASP.NET Core predefinite sul Identity Server:</span><span class="sxs-lookup"><span data-stu-id="4d0e3-124">IdentityServer with an additional `AddApiAuthorization` helper method that sets up some default ASP.NET Core conventions on top of IdentityServer:</span></span>

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * <span data-ttu-id="4d0e3-125">Autenticazione con un `AddIdentityServerJwt` metodo helper aggiuntivo che configura l'app per convalidare i token JWT prodotti dal Identity Server:</span><span class="sxs-lookup"><span data-stu-id="4d0e3-125">Authentication with an additional `AddIdentityServerJwt` helper method that configures the app to validate JWT tokens produced by IdentityServer:</span></span>

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* <span data-ttu-id="4d0e3-126">All'interno del `Startup.Configure` Metodo:</span><span class="sxs-lookup"><span data-stu-id="4d0e3-126">Inside the `Startup.Configure` method:</span></span>
  * <span data-ttu-id="4d0e3-127">Il middleware di autenticazione responsabile della convalida delle credenziali della richiesta e dell'impostazione dell'utente nel contesto della richiesta:</span><span class="sxs-lookup"><span data-stu-id="4d0e3-127">The authentication middleware that is responsible for validating the request credentials and setting the user on the request context:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

  * <span data-ttu-id="4d0e3-128">Il Identity middleware server che espone gli endpoint di OpenID Connect:</span><span class="sxs-lookup"><span data-stu-id="4d0e3-128">The IdentityServer middleware that exposes the OpenID Connect endpoints:</span></span>

    ```csharp
    app.UseIdentityServer();
    ```

### <a name="azure-app-service-on-linux"></a><span data-ttu-id="4d0e3-129">Servizio app di Azure in Linux</span><span class="sxs-lookup"><span data-stu-id="4d0e3-129">Azure App Service on Linux</span></span>

<span data-ttu-id="4d0e3-130">Per le distribuzioni del servizio app Azure in Linux, specificare in modo esplicito l'autorità di certificazione in `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="4d0e3-130">For Azure App Service deployments on Linux, specify the issuer explicitly in `Startup.ConfigureServices`:</span></span>

```csharp
services.Configure<JwtBearerOptions>(
    IdentityServerJwtConstants.IdentityServerJwtBearerScheme, 
    options =>
    {
        options.Authority = "{AUTHORITY}";
    });
```

<span data-ttu-id="4d0e3-131">Nel codice precedente, il `{AUTHORITY}` segnaposto è l'oggetto <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions.Authority> da usare quando si effettuano chiamate a OpenID Connect.</span><span class="sxs-lookup"><span data-stu-id="4d0e3-131">In the preceding code, the `{AUTHORITY}` placeholder is the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions.Authority> to use when making OpenID Connect calls.</span></span>

<span data-ttu-id="4d0e3-132">Esempio:</span><span class="sxs-lookup"><span data-stu-id="4d0e3-132">Example:</span></span>

```csharp
options.Authority = "https://contoso-service.azurewebsites.net";
```

### <a name="addapiauthorization"></a><span data-ttu-id="4d0e3-133">AddApiAuthorization</span><span class="sxs-lookup"><span data-stu-id="4d0e3-133">AddApiAuthorization</span></span>

<span data-ttu-id="4d0e3-134">Questo metodo helper configura il Identity Server per l'uso della configurazione supportata.</span><span class="sxs-lookup"><span data-stu-id="4d0e3-134">This helper method configures IdentityServer to use our supported configuration.</span></span> <span data-ttu-id="4d0e3-135">IdentityServer è un Framework potente ed estendibile per la gestione dei problemi di sicurezza delle app.</span><span class="sxs-lookup"><span data-stu-id="4d0e3-135">IdentityServer is a powerful and extensible framework for handling app security concerns.</span></span> <span data-ttu-id="4d0e3-136">Allo stesso tempo, che espone complessità superflua per gli scenari più comuni.</span><span class="sxs-lookup"><span data-stu-id="4d0e3-136">At the same time, that exposes unnecessary complexity for the most common scenarios.</span></span> <span data-ttu-id="4d0e3-137">Di conseguenza, viene fornito un set di convenzioni e opzioni di configurazione che sono considerati un valido punto di partenza.</span><span class="sxs-lookup"><span data-stu-id="4d0e3-137">Consequently, a set of conventions and configuration options is provided to you that are considered a good starting point.</span></span> <span data-ttu-id="4d0e3-138">Una volta che le esigenze di autenticazione cambiano, l'intera potenza del Identity Server è ancora disponibile per personalizzare l'autenticazione in base alle esigenze.</span><span class="sxs-lookup"><span data-stu-id="4d0e3-138">Once your authentication needs change, the full power of IdentityServer is still available to customize authentication to suit your needs.</span></span>

### <a name="addno-locidentityserverjwt"></a><span data-ttu-id="4d0e3-139">Aggiungi Identity ServerJwt</span><span class="sxs-lookup"><span data-stu-id="4d0e3-139">AddIdentityServerJwt</span></span>

<span data-ttu-id="4d0e3-140">Questo metodo helper configura uno schema di criteri per l'app come gestore di autenticazione predefinito.</span><span class="sxs-lookup"><span data-stu-id="4d0e3-140">This helper method configures a policy scheme for the app as the default authentication handler.</span></span> <span data-ttu-id="4d0e3-141">Il criterio è configurato in modo da consentire la Identity gestione di tutte le richieste indirizzate a qualsiasi sottopercorso nello Identity spazio URL "/ Identity ".</span><span class="sxs-lookup"><span data-stu-id="4d0e3-141">The policy is configured to let Identity handle all requests routed to any subpath in the Identity URL space "/Identity".</span></span> <span data-ttu-id="4d0e3-142">`JwtBearerHandler`Gestisce tutte le altre richieste.</span><span class="sxs-lookup"><span data-stu-id="4d0e3-142">The `JwtBearerHandler` handles all other requests.</span></span> <span data-ttu-id="4d0e3-143">Questo metodo registra inoltre una `<<ApplicationName>>API` risorsa API con Identity un server con un ambito predefinito `<<ApplicationName>>API` e configura il middleware del token di porta JWT per convalidare i token emessi dal Identity Server per l'app.</span><span class="sxs-lookup"><span data-stu-id="4d0e3-143">Additionally, this method registers an `<<ApplicationName>>API` API resource with IdentityServer with a default scope of `<<ApplicationName>>API` and configures the JWT Bearer token middleware to validate tokens issued by IdentityServer for the app.</span></span>

### <a name="weatherforecastcontroller"></a><span data-ttu-id="4d0e3-144">WeatherForecastController</span><span class="sxs-lookup"><span data-stu-id="4d0e3-144">WeatherForecastController</span></span>

<span data-ttu-id="4d0e3-145">Nel file *Controllers\WeatherForecastController.cs* , si noti l' `[Authorize]` attributo applicato alla classe che indica che l'utente deve essere autorizzato in base ai criteri predefiniti per accedere alla risorsa.</span><span class="sxs-lookup"><span data-stu-id="4d0e3-145">In the *Controllers\WeatherForecastController.cs* file, notice the `[Authorize]` attribute applied to the class that indicates that the user needs to be authorized based on the default policy to access the resource.</span></span> <span data-ttu-id="4d0e3-146">I criteri di autorizzazione predefiniti vengono configurati per l'utilizzo dello schema di autenticazione predefinito, configurato da `AddIdentityServerJwt` allo schema dei criteri menzionato in precedenza, rendendo `JwtBearerHandler` configurato da tale metodo helper il gestore predefinito per le richieste all'app.</span><span class="sxs-lookup"><span data-stu-id="4d0e3-146">The default authorization policy happens to be configured to use the default authentication scheme, which is set up by `AddIdentityServerJwt` to the policy scheme that was mentioned above, making the `JwtBearerHandler` configured by such helper method the default handler for requests to the app.</span></span>

### <a name="applicationdbcontext"></a><span data-ttu-id="4d0e3-147">ApplicationDbContext</span><span class="sxs-lookup"><span data-stu-id="4d0e3-147">ApplicationDbContext</span></span>

<span data-ttu-id="4d0e3-148">Nel file *Data\ApplicationDbContext.cs* si noti che lo stesso `DbContext` viene usato in Identity con l'eccezione che estende `ApiAuthorizationDbContext` (una classe più derivata da `IdentityDbContext` ) per includere lo schema per il Identity Server.</span><span class="sxs-lookup"><span data-stu-id="4d0e3-148">In the *Data\ApplicationDbContext.cs* file, notice the same `DbContext` is used in Identity with the exception that it extends `ApiAuthorizationDbContext` (a more derived class from `IdentityDbContext`) to include the schema for IdentityServer.</span></span>

<span data-ttu-id="4d0e3-149">Per ottenere il controllo completo dello schema del database, ereditare da una delle Identity `DbContext` classi disponibili e configurare il contesto per includere lo Identity schema chiamando `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` sul `OnModelCreating` metodo.</span><span class="sxs-lookup"><span data-stu-id="4d0e3-149">To gain full control of the database schema, inherit from one of the available Identity `DbContext` classes and configure the context to include the Identity schema by calling `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` on the `OnModelCreating` method.</span></span>

### <a name="oidcconfigurationcontroller"></a><span data-ttu-id="4d0e3-150">OidcConfigurationController</span><span class="sxs-lookup"><span data-stu-id="4d0e3-150">OidcConfigurationController</span></span>

<span data-ttu-id="4d0e3-151">Nel file *Controllers\OidcConfigurationController.cs* , si noti l'endpoint di cui è stato effettuato il provisioning per gestire i parametri di OIDC che il client deve usare.</span><span class="sxs-lookup"><span data-stu-id="4d0e3-151">In the *Controllers\OidcConfigurationController.cs* file, notice the endpoint that's provisioned to serve the OIDC parameters that the client needs to use.</span></span>

### appsettings.json

<span data-ttu-id="4d0e3-152">Nel *appsettings.json* file della radice del progetto è presente una nuova `IdentityServer` sezione che descrive l'elenco dei client configurati.</span><span class="sxs-lookup"><span data-stu-id="4d0e3-152">In the *appsettings.json* file of the project root, there's a new `IdentityServer` section that describes the list of configured clients.</span></span> <span data-ttu-id="4d0e3-153">Nell'esempio seguente è presente un singolo client.</span><span class="sxs-lookup"><span data-stu-id="4d0e3-153">In the following example, there's a single client.</span></span> <span data-ttu-id="4d0e3-154">Il nome del client corrisponde al nome dell'app e viene mappato per convenzione al `ClientId` parametro OAuth.</span><span class="sxs-lookup"><span data-stu-id="4d0e3-154">The client name corresponds to the app name and is mapped by convention to the OAuth `ClientId` parameter.</span></span> <span data-ttu-id="4d0e3-155">Il profilo indica il tipo di app da configurare.</span><span class="sxs-lookup"><span data-stu-id="4d0e3-155">The profile indicates the app type being configured.</span></span> <span data-ttu-id="4d0e3-156">Viene usato internamente per guidare le convenzioni che semplificano il processo di configurazione per il server.</span><span class="sxs-lookup"><span data-stu-id="4d0e3-156">It's used internally to drive conventions that simplify the configuration process for the server.</span></span> <span data-ttu-id="4d0e3-157">Sono disponibili diversi profili, come illustrato nella sezione [profili applicazione](#application-profiles) .</span><span class="sxs-lookup"><span data-stu-id="4d0e3-157">There are several profiles available, as explained in the [Application profiles](#application-profiles) section.</span></span>

```json
"IdentityServer": {
  "Clients": {
    "angularindividualpreview3final": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

### <a name="appsettingsdevelopmentjson"></a><span data-ttu-id="4d0e3-158">appsettings.Development.js</span><span class="sxs-lookup"><span data-stu-id="4d0e3-158">appsettings.Development.json</span></span>

<span data-ttu-id="4d0e3-159">Nella *appsettings.Development.jssul* file della radice del progetto è presente una `IdentityServer` sezione che descrive la chiave usata per firmare i token.</span><span class="sxs-lookup"><span data-stu-id="4d0e3-159">In the *appsettings.Development.json* file of the project root, there's an `IdentityServer` section that describes the key used to sign tokens.</span></span> <span data-ttu-id="4d0e3-160">Quando si esegue la distribuzione nell'ambiente di produzione, è necessario eseguire il provisioning di una chiave e distribuirla insieme all'app, come illustrato nella sezione [Deploy to Production](#deploy-to-production) .</span><span class="sxs-lookup"><span data-stu-id="4d0e3-160">When deploying to production, a key needs to be provisioned and deployed alongside the app, as explained in the [Deploy to production](#deploy-to-production) section.</span></span>

```json
"IdentityServer": {
  "Key": {
    "Type": "Development"
  }
}
```

## <a name="general-description-of-the-angular-app"></a><span data-ttu-id="4d0e3-161">Descrizione generale dell'app angolare</span><span class="sxs-lookup"><span data-stu-id="4d0e3-161">General description of the Angular app</span></span>

<span data-ttu-id="4d0e3-162">Il supporto per l'autenticazione e l'autorizzazione API nel modello angolare si trova nel proprio modulo angolare nella directory *ClientApp\src\api-Authorization*</span><span class="sxs-lookup"><span data-stu-id="4d0e3-162">The authentication and API authorization support in the Angular template resides in its own Angular module in the *ClientApp\src\api-authorization* directory.</span></span> <span data-ttu-id="4d0e3-163">Il modulo è costituito dagli elementi seguenti:</span><span class="sxs-lookup"><span data-stu-id="4d0e3-163">The module is composed of the following elements:</span></span>

* <span data-ttu-id="4d0e3-164">3 componenti:</span><span class="sxs-lookup"><span data-stu-id="4d0e3-164">3 components:</span></span>
  * <span data-ttu-id="4d0e3-165">*login. Component. TS*: gestisce il flusso di accesso dell'app.</span><span class="sxs-lookup"><span data-stu-id="4d0e3-165">*login.component.ts*: Handles the app's login flow.</span></span>
  * <span data-ttu-id="4d0e3-166">*Logout. Component. TS*: gestisce il flusso di disconnessione dell'app.</span><span class="sxs-lookup"><span data-stu-id="4d0e3-166">*logout.component.ts*: Handles the app's logout flow.</span></span>
  * <span data-ttu-id="4d0e3-167">*login-menu. Component. TS*: widget che visualizza uno dei seguenti set di collegamenti:</span><span class="sxs-lookup"><span data-stu-id="4d0e3-167">*login-menu.component.ts*: A widget that displays one of the following sets of links:</span></span>
    * <span data-ttu-id="4d0e3-168">Collegamenti di gestione dei profili utente e disconnessione quando l'utente viene autenticato.</span><span class="sxs-lookup"><span data-stu-id="4d0e3-168">User profile management and log out links when the user is authenticated.</span></span>
    * <span data-ttu-id="4d0e3-169">Collegamenti per la registrazione e l'accesso quando l'utente non è autenticato.</span><span class="sxs-lookup"><span data-stu-id="4d0e3-169">Registration and log in links when the user isn't authenticated.</span></span>
* <span data-ttu-id="4d0e3-170">Una protezione `AuthorizeGuard` della route che può essere aggiunta alle route e richiede che un utente venga autenticato prima di visitare la route.</span><span class="sxs-lookup"><span data-stu-id="4d0e3-170">A route guard `AuthorizeGuard` that can be added to routes and requires a user to be authenticated before visiting the route.</span></span>
* <span data-ttu-id="4d0e3-171">Un intercettore HTTP `AuthorizeInterceptor` che connette il token di accesso alle richieste HTTP in uscita destinate all'API quando l'utente viene autenticato.</span><span class="sxs-lookup"><span data-stu-id="4d0e3-171">An HTTP interceptor `AuthorizeInterceptor` that attaches the access token to outgoing HTTP requests targeting the API when the user is authenticated.</span></span>
* <span data-ttu-id="4d0e3-172">Un servizio `AuthorizeService` che gestisce i dettagli di basso livello del processo di autenticazione ed espone le informazioni relative all'utente autenticato al resto dell'app per l'utilizzo.</span><span class="sxs-lookup"><span data-stu-id="4d0e3-172">A service `AuthorizeService` that handles the lower-level details of the authentication process and exposes information about the authenticated user to the rest of the app for consumption.</span></span>
* <span data-ttu-id="4d0e3-173">Un modulo angolare che definisce le route associate alle parti di autenticazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="4d0e3-173">An Angular module that defines routes associated with the authentication parts of the app.</span></span> <span data-ttu-id="4d0e3-174">Espone il componente del menu di accesso, l'intercettore, il Guard e il servizio per l'utilizzo dal resto dell'app.</span><span class="sxs-lookup"><span data-stu-id="4d0e3-174">It exposes the login menu component, the interceptor, the guard, and the service for consumption from the rest of the app.</span></span>

## <a name="general-description-of-the-react-app"></a><span data-ttu-id="4d0e3-175">Descrizione generale dell'app React</span><span class="sxs-lookup"><span data-stu-id="4d0e3-175">General description of the React app</span></span>

<span data-ttu-id="4d0e3-176">Il supporto per l'autenticazione e l'autorizzazione API nel modello React risiede nella directory *ClientApp\src\components\api-Authorization*</span><span class="sxs-lookup"><span data-stu-id="4d0e3-176">The support for authentication and API authorization in the React template resides in the *ClientApp\src\components\api-authorization* directory.</span></span> <span data-ttu-id="4d0e3-177">È costituito dagli elementi seguenti:</span><span class="sxs-lookup"><span data-stu-id="4d0e3-177">It's composed of the following elements:</span></span>

* <span data-ttu-id="4d0e3-178">4 componenti:</span><span class="sxs-lookup"><span data-stu-id="4d0e3-178">4 components:</span></span>
  * <span data-ttu-id="4d0e3-179">*Login.js*: gestisce il flusso di accesso dell'app.</span><span class="sxs-lookup"><span data-stu-id="4d0e3-179">*Login.js*: Handles the app's login flow.</span></span>
  * <span data-ttu-id="4d0e3-180">*Logout.js*: gestisce il flusso di disconnessione dell'app.</span><span class="sxs-lookup"><span data-stu-id="4d0e3-180">*Logout.js*: Handles the app's logout flow.</span></span>
  * <span data-ttu-id="4d0e3-181">*LoginMenu.js*: widget che visualizza uno dei seguenti set di collegamenti:</span><span class="sxs-lookup"><span data-stu-id="4d0e3-181">*LoginMenu.js*: A widget that displays one of the following sets of links:</span></span>
    * <span data-ttu-id="4d0e3-182">Collegamenti di gestione dei profili utente e disconnessione quando l'utente viene autenticato.</span><span class="sxs-lookup"><span data-stu-id="4d0e3-182">User profile management and log out links when the user is authenticated.</span></span>
    * <span data-ttu-id="4d0e3-183">Collegamenti per la registrazione e l'accesso quando l'utente non è autenticato.</span><span class="sxs-lookup"><span data-stu-id="4d0e3-183">Registration and log in links when the user isn't authenticated.</span></span>
  * <span data-ttu-id="4d0e3-184">*AuthorizeRoute.js*: componente di route che richiede l'autenticazione di un utente prima di eseguire il rendering del componente indicato nel `Component` parametro.</span><span class="sxs-lookup"><span data-stu-id="4d0e3-184">*AuthorizeRoute.js*: A route component that requires a user to be authenticated before rendering the component indicated in the `Component` parameter.</span></span>
* <span data-ttu-id="4d0e3-185">Istanza esportata `authService` della classe `AuthorizeService` che gestisce i dettagli di basso livello del processo di autenticazione ed espone le informazioni relative all'utente autenticato al resto dell'app per l'utilizzo.</span><span class="sxs-lookup"><span data-stu-id="4d0e3-185">An exported `authService` instance of class `AuthorizeService` that handles the lower-level details of the authentication process and exposes information about the authenticated user to the rest of the app for consumption.</span></span>

<span data-ttu-id="4d0e3-186">Ora che sono stati esaminati i componenti principali della soluzione, è possibile approfondire i singoli scenari per l'app.</span><span class="sxs-lookup"><span data-stu-id="4d0e3-186">Now that you've seen the main components of the solution, you can take a deeper look at individual scenarios for the app.</span></span>

## <a name="require-authorization-on-a-new-api"></a><span data-ttu-id="4d0e3-187">Richiedi autorizzazione per una nuova API</span><span class="sxs-lookup"><span data-stu-id="4d0e3-187">Require authorization on a new API</span></span>

<span data-ttu-id="4d0e3-188">Per impostazione predefinita, il sistema è configurato in modo da richiedere facilmente l'autorizzazione per le nuove API.</span><span class="sxs-lookup"><span data-stu-id="4d0e3-188">By default, the system is configured to easily require authorization for new APIs.</span></span> <span data-ttu-id="4d0e3-189">A tale scopo, creare un nuovo controller e aggiungere l' `[Authorize]` attributo alla classe controller o a qualsiasi azione all'interno del controller.</span><span class="sxs-lookup"><span data-stu-id="4d0e3-189">To do so, create a new controller and add the `[Authorize]` attribute to the controller class or to any action within the controller.</span></span>

## <a name="customize-the-api-authentication-handler"></a><span data-ttu-id="4d0e3-190">Personalizzare il gestore di autenticazione API</span><span class="sxs-lookup"><span data-stu-id="4d0e3-190">Customize the API authentication handler</span></span>

<span data-ttu-id="4d0e3-191">Per personalizzare la configurazione del gestore JWT dell'API, configurare la relativa <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> istanza:</span><span class="sxs-lookup"><span data-stu-id="4d0e3-191">To customize the configuration of the API's JWT handler, configure its <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> instance:</span></span>

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

<span data-ttu-id="4d0e3-192">Il gestore JWT dell'API genera eventi che consentono di controllare il processo di autenticazione tramite `JwtBearerEvents` .</span><span class="sxs-lookup"><span data-stu-id="4d0e3-192">The API's JWT handler raises events that enable control over the authentication process using `JwtBearerEvents`.</span></span> <span data-ttu-id="4d0e3-193">Per fornire il supporto per l'autorizzazione dell'API, `AddIdentityServerJwt` registra i relativi gestori eventi.</span><span class="sxs-lookup"><span data-stu-id="4d0e3-193">To provide support for API authorization, `AddIdentityServerJwt` registers its own event handlers.</span></span>

<span data-ttu-id="4d0e3-194">Per personalizzare la gestione di un evento, eseguire il wrapping del gestore eventi esistente con logica aggiuntiva, se necessario.</span><span class="sxs-lookup"><span data-stu-id="4d0e3-194">To customize the handling of an event, wrap the existing event handler with additional logic as required.</span></span> <span data-ttu-id="4d0e3-195">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="4d0e3-195">For example:</span></span>

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

<span data-ttu-id="4d0e3-196">Nel codice precedente, il `OnTokenValidated` gestore eventi viene sostituito con un'implementazione personalizzata.</span><span class="sxs-lookup"><span data-stu-id="4d0e3-196">In the preceding code, the `OnTokenValidated` event handler is replaced with a custom implementation.</span></span> <span data-ttu-id="4d0e3-197">Questa implementazione:</span><span class="sxs-lookup"><span data-stu-id="4d0e3-197">This implementation:</span></span>

1. <span data-ttu-id="4d0e3-198">Chiama l'implementazione originale fornita dal supporto dell'autorizzazione dell'API.</span><span class="sxs-lookup"><span data-stu-id="4d0e3-198">Calls the original implementation provided by the API authorization support.</span></span>
1. <span data-ttu-id="4d0e3-199">Eseguire la propria logica personalizzata.</span><span class="sxs-lookup"><span data-stu-id="4d0e3-199">Run its own custom logic.</span></span>

## <a name="protect-a-client-side-route-angular"></a><span data-ttu-id="4d0e3-200">Proteggere una route lato client (angolare)</span><span class="sxs-lookup"><span data-stu-id="4d0e3-200">Protect a client-side route (Angular)</span></span>

<span data-ttu-id="4d0e3-201">Per la protezione di una route sul lato client, è necessario aggiungere l'autorizzazione Guard all'elenco delle protezioni da eseguire durante la configurazione di una route.</span><span class="sxs-lookup"><span data-stu-id="4d0e3-201">Protecting a client-side route is done by adding the authorize guard to the list of guards to run when configuring a route.</span></span> <span data-ttu-id="4d0e3-202">Come esempio, è possibile vedere come la `fetch-data` Route è configurata nel modulo principale dell'app:</span><span class="sxs-lookup"><span data-stu-id="4d0e3-202">As an example, you can see how the `fetch-data` route is configured within the main app Angular module:</span></span>

```typescript
RouterModule.forRoot([
  // ...
  { path: 'fetch-data', component: FetchDataComponent, canActivate: [AuthorizeGuard] },
])
```

<span data-ttu-id="4d0e3-203">È importante ricordare che la protezione di una route non protegge l'endpoint effettivo (a cui è ancora necessario `[Authorize]` applicare un attributo) ma impedisce solo all'utente di passare alla route specificata sul lato client quando non è autenticato.</span><span class="sxs-lookup"><span data-stu-id="4d0e3-203">It's important to mention that protecting a route doesn't protect the actual endpoint (which still requires an `[Authorize]` attribute applied to it) but that it only prevents the user from navigating to the given client-side route when it isn't authenticated.</span></span>

## <a name="authenticate-api-requests-angular"></a><span data-ttu-id="4d0e3-204">Autenticare le richieste API (angolare)</span><span class="sxs-lookup"><span data-stu-id="4d0e3-204">Authenticate API requests (Angular)</span></span>

<span data-ttu-id="4d0e3-205">L'autenticazione delle richieste alle API ospitate insieme all'app viene eseguita automaticamente tramite l'uso dell'intercettore client HTTP definito dall'app.</span><span class="sxs-lookup"><span data-stu-id="4d0e3-205">Authenticating requests to APIs hosted alongside the app is done automatically through the use of the HTTP client interceptor defined by the app.</span></span>

## <a name="protect-a-client-side-route-react"></a><span data-ttu-id="4d0e3-206">Proteggere una route lato client (React)</span><span class="sxs-lookup"><span data-stu-id="4d0e3-206">Protect a client-side route (React)</span></span>

<span data-ttu-id="4d0e3-207">Proteggere una route sul lato client usando il `AuthorizeRoute` componente anziché il `Route` componente normale.</span><span class="sxs-lookup"><span data-stu-id="4d0e3-207">Protect a client-side route by using the `AuthorizeRoute` component instead of the plain `Route` component.</span></span> <span data-ttu-id="4d0e3-208">Si noti, ad esempio, il modo in cui la `fetch-data` Route è configurata all'interno del `App` componente:</span><span class="sxs-lookup"><span data-stu-id="4d0e3-208">For example, notice how the `fetch-data` route is configured within the `App` component:</span></span>

```jsx
<AuthorizeRoute path='/fetch-data' component={FetchData} />
```

<span data-ttu-id="4d0e3-209">Protezione di una route:</span><span class="sxs-lookup"><span data-stu-id="4d0e3-209">Protecting a route:</span></span>

* <span data-ttu-id="4d0e3-210">Non protegge l'endpoint effettivo (a cui è ancora necessario `[Authorize]` applicare un attributo).</span><span class="sxs-lookup"><span data-stu-id="4d0e3-210">Doesn't protect the actual endpoint (which still requires an `[Authorize]` attribute applied to it).</span></span>
* <span data-ttu-id="4d0e3-211">Impedisce solo all'utente di passare alla route specificata sul lato client quando non viene autenticata.</span><span class="sxs-lookup"><span data-stu-id="4d0e3-211">Only prevents the user from navigating to the given client-side route when it isn't authenticated.</span></span>

## <a name="authenticate-api-requests-react"></a><span data-ttu-id="4d0e3-212">Autenticare le richieste API (React)</span><span class="sxs-lookup"><span data-stu-id="4d0e3-212">Authenticate API requests (React)</span></span>

<span data-ttu-id="4d0e3-213">L'autenticazione delle richieste con React viene eseguita importando innanzitutto l' `authService` istanza da `AuthorizeService` .</span><span class="sxs-lookup"><span data-stu-id="4d0e3-213">Authenticating requests with React is done by first importing the `authService` instance from the `AuthorizeService`.</span></span> <span data-ttu-id="4d0e3-214">Il token di accesso viene recuperato da `authService` ed è associato alla richiesta, come illustrato di seguito.</span><span class="sxs-lookup"><span data-stu-id="4d0e3-214">The access token is retrieved from the `authService` and is attached to the request as shown below.</span></span> <span data-ttu-id="4d0e3-215">Nei componenti React questa operazione viene in genere eseguita nel `componentDidMount` Metodo Lifecycle o come risultato di un'interazione dell'utente.</span><span class="sxs-lookup"><span data-stu-id="4d0e3-215">In React components, this work is typically done in the `componentDidMount` lifecycle method or as the result from some user interaction.</span></span>

### <a name="import-the-authservice-into-your-component"></a><span data-ttu-id="4d0e3-216">Importare authService nel componente</span><span class="sxs-lookup"><span data-stu-id="4d0e3-216">Import the authService into your component</span></span>

```javascript
import authService from './api-authorization/AuthorizeService'
```

### <a name="retrieve-and-attach-the-access-token-to-the-response"></a><span data-ttu-id="4d0e3-217">Recupera e alleghi il token di accesso alla risposta</span><span class="sxs-lookup"><span data-stu-id="4d0e3-217">Retrieve and attach the access token to the response</span></span>

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

## <a name="deploy-to-production"></a><span data-ttu-id="4d0e3-218">Distribuire nell'ambiente di produzione</span><span class="sxs-lookup"><span data-stu-id="4d0e3-218">Deploy to production</span></span>

<span data-ttu-id="4d0e3-219">Per distribuire l'app nell'ambiente di produzione, è necessario eseguire il provisioning delle risorse seguenti:</span><span class="sxs-lookup"><span data-stu-id="4d0e3-219">To deploy the app to production, the following resources need to be provisioned:</span></span>

* <span data-ttu-id="4d0e3-220">Un database in cui archiviare gli Identity account utente e le Identity concessioni del server.</span><span class="sxs-lookup"><span data-stu-id="4d0e3-220">A database to store the Identity user accounts and the IdentityServer grants.</span></span>
* <span data-ttu-id="4d0e3-221">Un certificato di produzione da usare per firmare i token.</span><span class="sxs-lookup"><span data-stu-id="4d0e3-221">A production certificate to use for signing tokens.</span></span>
  * <span data-ttu-id="4d0e3-222">Non sono previsti requisiti specifici per il certificato. può essere un certificato autofirmato o un certificato sottoposti a provisioning tramite un'autorità di certificazione.</span><span class="sxs-lookup"><span data-stu-id="4d0e3-222">There are no specific requirements for this certificate; it can be a self-signed certificate or a certificate provisioned through a CA authority.</span></span>
  * <span data-ttu-id="4d0e3-223">Può essere generato tramite strumenti standard come PowerShell o OpenSSL.</span><span class="sxs-lookup"><span data-stu-id="4d0e3-223">It can be generated through standard tools like PowerShell or OpenSSL.</span></span>
  * <span data-ttu-id="4d0e3-224">Può essere installata nell'archivio certificati nei computer di destinazione o distribuita come file con *estensione pfx* con una password complessa.</span><span class="sxs-lookup"><span data-stu-id="4d0e3-224">It can be installed into the certificate store on the target machines or deployed as a *.pfx* file with a strong password.</span></span>

### <a name="example-deploy-to-a-non-azure-web-hosting-provider"></a><span data-ttu-id="4d0e3-225">Esempio: eseguire la distribuzione in un provider di hosting Web non di Azure</span><span class="sxs-lookup"><span data-stu-id="4d0e3-225">Example: Deploy to a non-Azure web hosting provider</span></span>

<span data-ttu-id="4d0e3-226">Nel pannello hosting Web creare o caricare il certificato.</span><span class="sxs-lookup"><span data-stu-id="4d0e3-226">In your web hosting panel, create or load your certificate.</span></span> <span data-ttu-id="4d0e3-227">Quindi, nel file dell'app *appsettings.json* , modificare la `IdentityServer` sezione in modo da includere i dettagli della chiave.</span><span class="sxs-lookup"><span data-stu-id="4d0e3-227">Then in the app's *appsettings.json* file, modify the `IdentityServer` section to include the key details.</span></span> <span data-ttu-id="4d0e3-228">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="4d0e3-228">For example:</span></span>

```json
"IdentityServer": {
  "Key": {
    "Type": "Store",
    "StoreName": "WebHosting",
    "StoreLocation": "CurrentUser",
    "Name": "CN=MyApplication"
  }
}
```

<span data-ttu-id="4d0e3-229">Nell'esempio precedente:</span><span class="sxs-lookup"><span data-stu-id="4d0e3-229">In the preceding example:</span></span>

* <span data-ttu-id="4d0e3-230">`StoreName` rappresenta il nome dell'archivio certificati in cui è archiviato il certificato.</span><span class="sxs-lookup"><span data-stu-id="4d0e3-230">`StoreName` represents the name of the certificate store where the certificate is stored.</span></span> <span data-ttu-id="4d0e3-231">In questo caso, punta all'archivio di hosting Web.</span><span class="sxs-lookup"><span data-stu-id="4d0e3-231">In this case, it points to the web hosting store.</span></span>
* <span data-ttu-id="4d0e3-232">`StoreLocation` rappresenta la posizione in cui caricare il certificato ( `CurrentUser` in questo caso).</span><span class="sxs-lookup"><span data-stu-id="4d0e3-232">`StoreLocation` represents where to load the certificate from (`CurrentUser` in this case).</span></span>
* <span data-ttu-id="4d0e3-233">`Name` corrisponde al soggetto distinto per il certificato.</span><span class="sxs-lookup"><span data-stu-id="4d0e3-233">`Name` corresponds with the distinguished subject for the certificate.</span></span>

### <a name="example-deploy-to-azure-app-service"></a><span data-ttu-id="4d0e3-234">Esempio: eseguire la distribuzione nel servizio app Azure</span><span class="sxs-lookup"><span data-stu-id="4d0e3-234">Example: Deploy to Azure App Service</span></span>

<span data-ttu-id="4d0e3-235">Questa sezione descrive come distribuire l'app nel servizio app Azure usando un certificato archiviato nell'archivio certificati.</span><span class="sxs-lookup"><span data-stu-id="4d0e3-235">This section describes deploying the app to Azure App Service using a certificate stored in the certificate store.</span></span> <span data-ttu-id="4d0e3-236">Per modificare l'app per caricare un certificato dall'archivio certificati, è necessario un piano di servizio di livello standard o superiore quando si configura l'app nel portale di Azure in un passaggio successivo.</span><span class="sxs-lookup"><span data-stu-id="4d0e3-236">To modify the app to load a certificate from the certificate store, a Standard tier service plan or better is required when you configure the app in the Azure portal in a later step.</span></span>

<span data-ttu-id="4d0e3-237">Nel file dell'app *appsettings.json* modificare la `IdentityServer` sezione in modo da includere i dettagli della chiave:</span><span class="sxs-lookup"><span data-stu-id="4d0e3-237">In the app's *appsettings.json* file, modify the `IdentityServer` section to include the key details:</span></span>

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

* <span data-ttu-id="4d0e3-238">Il nome dell'archivio rappresenta il nome dell'archivio certificati in cui è archiviato il certificato.</span><span class="sxs-lookup"><span data-stu-id="4d0e3-238">The store name represents the name of the certificate store where the certificate is stored.</span></span> <span data-ttu-id="4d0e3-239">In questo caso, fa riferimento all'archivio utenti personali.</span><span class="sxs-lookup"><span data-stu-id="4d0e3-239">In this case, it points to the personal user store.</span></span>
* <span data-ttu-id="4d0e3-240">Il percorso dell'archivio rappresenta il punto da cui caricare il certificato ( `CurrentUser` o `LocalMachine` ).</span><span class="sxs-lookup"><span data-stu-id="4d0e3-240">The store location represents where to load the certificate from (`CurrentUser` or `LocalMachine`).</span></span>
* <span data-ttu-id="4d0e3-241">La proprietà Name del certificato corrisponde all'oggetto distinto per il certificato.</span><span class="sxs-lookup"><span data-stu-id="4d0e3-241">The name property on certificate corresponds with the distinguished subject for the certificate.</span></span>

<span data-ttu-id="4d0e3-242">Per eseguire la distribuzione nel servizio app Azure, seguire la procedura descritta in [distribuire l'app in Azure](xref:tutorials/publish-to-azure-webapp-using-vs#deploy-the-app-to-azure), che spiega come creare le risorse di Azure necessarie e distribuire l'app nell'ambiente di produzione.</span><span class="sxs-lookup"><span data-stu-id="4d0e3-242">To deploy to Azure App Service, follow the steps in [Deploy the app to Azure](xref:tutorials/publish-to-azure-webapp-using-vs#deploy-the-app-to-azure), which explains how to create the necessary Azure resources and deploy the app to production.</span></span>

<span data-ttu-id="4d0e3-243">Dopo aver seguito le istruzioni precedenti, l'app viene distribuita in Azure, ma non è ancora funzionante.</span><span class="sxs-lookup"><span data-stu-id="4d0e3-243">After following the preceding instructions, the app is deployed to Azure but isn't yet functional.</span></span> <span data-ttu-id="4d0e3-244">Il certificato usato dall'app deve essere configurato nel portale di Azure.</span><span class="sxs-lookup"><span data-stu-id="4d0e3-244">The certificate used by the app must be configured in the Azure portal.</span></span> <span data-ttu-id="4d0e3-245">Individuare l'identificazione personale per il certificato e seguire i passaggi descritti in [caricare i certificati](/azure/app-service/app-service-web-ssl-cert-load#load-the-certificate-in-code).</span><span class="sxs-lookup"><span data-stu-id="4d0e3-245">Locate the thumbprint for the certificate and follow the steps described in [Load your certificates](/azure/app-service/app-service-web-ssl-cert-load#load-the-certificate-in-code).</span></span>

<span data-ttu-id="4d0e3-246">Sebbene questi passaggi menzionino SSL, nella portale di Azure è possibile caricare il certificato di cui è stato **effettuato il** provisioning da usare con l'app.</span><span class="sxs-lookup"><span data-stu-id="4d0e3-246">While these steps mention SSL, there's a **Private certificates** section in the Azure portal where you can upload the provisioned certificate to use with the app.</span></span>

<span data-ttu-id="4d0e3-247">Dopo aver configurato l'app e le impostazioni dell'app nel portale di Azure, riavviare l'app nel portale.</span><span class="sxs-lookup"><span data-stu-id="4d0e3-247">After configuring the app and the app's settings in the Azure portal, restart the app in the portal.</span></span>

## <a name="other-configuration-options"></a><span data-ttu-id="4d0e3-248">Altre opzioni di configurazione</span><span class="sxs-lookup"><span data-stu-id="4d0e3-248">Other configuration options</span></span>

<span data-ttu-id="4d0e3-249">Il supporto per l'autorizzazione API si basa su Identity server con un set di convenzioni, valori predefiniti e miglioramenti per semplificare l'esperienza per le Spa.</span><span class="sxs-lookup"><span data-stu-id="4d0e3-249">The support for API authorization builds on top of IdentityServer with a set of conventions, default values, and enhancements to simplify the experience for SPAs.</span></span> <span data-ttu-id="4d0e3-250">Inutile dire che la piena potenza del Identity Server è disponibile dietro le quinte se le integrazioni ASP.NET Core non coprono lo scenario.</span><span class="sxs-lookup"><span data-stu-id="4d0e3-250">Needless to say, the full power of IdentityServer is available behind the scenes if the ASP.NET Core integrations don't cover your scenario.</span></span> <span data-ttu-id="4d0e3-251">Il supporto ASP.NET Core si concentra sulle app "First-Party", in cui tutte le app vengono create e distribuite dall'organizzazione.</span><span class="sxs-lookup"><span data-stu-id="4d0e3-251">The ASP.NET Core support is focused on "first-party" apps, where all the apps are created and deployed by our organization.</span></span> <span data-ttu-id="4d0e3-252">Di conseguenza, il supporto non viene offerto per elementi come il consenso o la Federazione.</span><span class="sxs-lookup"><span data-stu-id="4d0e3-252">As such, support isn't offered for things like consent or federation.</span></span> <span data-ttu-id="4d0e3-253">Per questi scenari, usare Identity Server e seguire la relativa documentazione.</span><span class="sxs-lookup"><span data-stu-id="4d0e3-253">For those scenarios, use IdentityServer and follow their documentation.</span></span>

### <a name="application-profiles"></a><span data-ttu-id="4d0e3-254">Profili applicazione</span><span class="sxs-lookup"><span data-stu-id="4d0e3-254">Application profiles</span></span>

<span data-ttu-id="4d0e3-255">I profili dell'applicazione sono configurazioni predefinite per le app che definiscono ulteriormente i parametri.</span><span class="sxs-lookup"><span data-stu-id="4d0e3-255">Application profiles are predefined configurations for apps that further define their parameters.</span></span> <span data-ttu-id="4d0e3-256">A questo punto sono supportati i profili seguenti:</span><span class="sxs-lookup"><span data-stu-id="4d0e3-256">At this time, the following profiles are supported:</span></span>

* <span data-ttu-id="4d0e3-257">`IdentityServerSPA`: Rappresenta una SPA ospitata insieme al Identity Server come singola unità.</span><span class="sxs-lookup"><span data-stu-id="4d0e3-257">`IdentityServerSPA`: Represents a SPA hosted alongside IdentityServer as a single unit.</span></span>
  * <span data-ttu-id="4d0e3-258">Il `redirect_uri` valore predefinito è `/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="4d0e3-258">The `redirect_uri` defaults to `/authentication/login-callback`.</span></span>
  * <span data-ttu-id="4d0e3-259">Il `post_logout_redirect_uri` valore predefinito è `/authentication/logout-callback` .</span><span class="sxs-lookup"><span data-stu-id="4d0e3-259">The `post_logout_redirect_uri` defaults to `/authentication/logout-callback`.</span></span>
  * <span data-ttu-id="4d0e3-260">Il set di ambiti include `openid` , `profile` e ogni ambito definito per le API nell'app.</span><span class="sxs-lookup"><span data-stu-id="4d0e3-260">The set of scopes includes the `openid`, `profile`, and every scope defined for the APIs in the app.</span></span>
  * <span data-ttu-id="4d0e3-261">Il set di tipi di risposta OIDC consentiti è `id_token token` o singolarmente ( `id_token` , `token` ).</span><span class="sxs-lookup"><span data-stu-id="4d0e3-261">The set of allowed OIDC response types is `id_token token` or each of them individually (`id_token`, `token`).</span></span>
  * <span data-ttu-id="4d0e3-262">La modalità di risposta consentita è `fragment` .</span><span class="sxs-lookup"><span data-stu-id="4d0e3-262">The allowed response mode is `fragment`.</span></span>
* <span data-ttu-id="4d0e3-263">`SPA`: Rappresenta una SPA non ospitata con il Identity Server.</span><span class="sxs-lookup"><span data-stu-id="4d0e3-263">`SPA`: Represents a SPA that isn't hosted with IdentityServer.</span></span>
  * <span data-ttu-id="4d0e3-264">Il set di ambiti include `openid` , `profile` e ogni ambito definito per le API nell'app.</span><span class="sxs-lookup"><span data-stu-id="4d0e3-264">The set of scopes includes the `openid`, `profile`, and every scope defined for the APIs in the app.</span></span>
  * <span data-ttu-id="4d0e3-265">Il set di tipi di risposta OIDC consentiti è `id_token token` o singolarmente ( `id_token` , `token` ).</span><span class="sxs-lookup"><span data-stu-id="4d0e3-265">The set of allowed OIDC response types is `id_token token` or each of them individually (`id_token`, `token`).</span></span>
  * <span data-ttu-id="4d0e3-266">La modalità di risposta consentita è `fragment` .</span><span class="sxs-lookup"><span data-stu-id="4d0e3-266">The allowed response mode is `fragment`.</span></span>
* <span data-ttu-id="4d0e3-267">`IdentityServerJwt`: Rappresenta un'API ospitata insieme al Identity Server.</span><span class="sxs-lookup"><span data-stu-id="4d0e3-267">`IdentityServerJwt`: Represents an API that is hosted alongside with IdentityServer.</span></span>
  * <span data-ttu-id="4d0e3-268">L'app è configurata in modo da avere un singolo ambito per impostazione predefinita sul nome dell'app.</span><span class="sxs-lookup"><span data-stu-id="4d0e3-268">The app is configured to have a single scope that defaults to the app name.</span></span>
* <span data-ttu-id="4d0e3-269">`API`: Rappresenta un'API non ospitata con il Identity Server.</span><span class="sxs-lookup"><span data-stu-id="4d0e3-269">`API`: Represents an API that isn't hosted with IdentityServer.</span></span>
  * <span data-ttu-id="4d0e3-270">L'app è configurata in modo da avere un singolo ambito per impostazione predefinita sul nome dell'app.</span><span class="sxs-lookup"><span data-stu-id="4d0e3-270">The app is configured to have a single scope that defaults to the app name.</span></span>

### <a name="configuration-through-appsettings"></a><span data-ttu-id="4d0e3-271">Configurazione tramite AppSettings</span><span class="sxs-lookup"><span data-stu-id="4d0e3-271">Configuration through AppSettings</span></span>

<span data-ttu-id="4d0e3-272">Configurare le app tramite il sistema di configurazione aggiungendole all'elenco di `Clients` o `Resources` .</span><span class="sxs-lookup"><span data-stu-id="4d0e3-272">Configure the apps through the configuration system by adding them to the list of `Clients` or `Resources`.</span></span>

<span data-ttu-id="4d0e3-273">Configurare `redirect_uri` la proprietà e di ogni client `post_logout_redirect_uri` , come illustrato nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="4d0e3-273">Configure each client's `redirect_uri` and `post_logout_redirect_uri` property, as shown in the following example:</span></span>

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

<span data-ttu-id="4d0e3-274">Quando si configurano le risorse, è possibile configurare gli ambiti per la risorsa come illustrato di seguito:</span><span class="sxs-lookup"><span data-stu-id="4d0e3-274">When configuring resources, you can configure the scopes for the resource as shown below:</span></span>

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

### <a name="configuration-through-code"></a><span data-ttu-id="4d0e3-275">Configurazione tramite codice</span><span class="sxs-lookup"><span data-stu-id="4d0e3-275">Configuration through code</span></span>

<span data-ttu-id="4d0e3-276">È anche possibile configurare i client e le risorse tramite codice usando un overload di `AddApiAuthorization` che esegue un'azione per configurare le opzioni.</span><span class="sxs-lookup"><span data-stu-id="4d0e3-276">You can also configure the clients and resources through code using an overload of `AddApiAuthorization` that takes an action to configure options.</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="4d0e3-277">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="4d0e3-277">Additional resources</span></span>

* <xref:spa/angular>
* <xref:spa/react>
* <xref:security/authentication/scaffold-identity>
