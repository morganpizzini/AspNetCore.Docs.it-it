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
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/identity/spa
ms.openlocfilehash: 2b587517268208dcf66cd2895b7aa22bfa381f84
ms.sourcegitcommit: fa89d6553378529ae86b388689ac2c6f38281bb9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/07/2020
ms.locfileid: "86060358"
---
# <a name="authentication-and-authorization-for-spas"></a><span data-ttu-id="0b9a7-103">Autenticazione e autorizzazione per le ZPS</span><span class="sxs-lookup"><span data-stu-id="0b9a7-103">Authentication and authorization for SPAs</span></span>

<span data-ttu-id="0b9a7-104">ASP.NET Core 3,0 o versione successiva offre l'autenticazione in app a pagina singola (Spa) usando il supporto per l'autorizzazione dell'API.</span><span class="sxs-lookup"><span data-stu-id="0b9a7-104">ASP.NET Core 3.0 or later offers authentication in Single Page Apps (SPAs) using the support for API authorization.</span></span> <span data-ttu-id="0b9a7-105">ASP.NET Core Identity per l'autenticazione e l'archiviazione degli utenti è combinato con [IdentityServer](https://identityserver.io/) per l'implementazione di Open ID Connect.</span><span class="sxs-lookup"><span data-stu-id="0b9a7-105">ASP.NET Core Identity for authenticating and storing users is combined with [IdentityServer](https://identityserver.io/) for implementing Open ID Connect.</span></span>

<span data-ttu-id="0b9a7-106">Un parametro di autenticazione è stato aggiunto ai modelli di progetto **angolari** e **React** , che è simile al parametro Authentication nei modelli di progetto **applicazione Web** (MVC) e **applicazione Web** ( Razor pagine).</span><span class="sxs-lookup"><span data-stu-id="0b9a7-106">An authentication parameter was added to the **Angular** and **React** project templates that is similar to the authentication parameter in the **Web Application (Model-View-Controller)** (MVC) and **Web Application** (Razor Pages) project templates.</span></span> <span data-ttu-id="0b9a7-107">I valori dei parametri consentiti sono **None** e **individual**.</span><span class="sxs-lookup"><span data-stu-id="0b9a7-107">The allowed parameter values are **None** and **Individual**.</span></span> <span data-ttu-id="0b9a7-108">Il modello di progetto **React.js e Redux** non supporta il parametro Authentication al momento.</span><span class="sxs-lookup"><span data-stu-id="0b9a7-108">The **React.js and Redux** project template doesn't support the authentication parameter at this time.</span></span>

## <a name="create-an-app-with-api-authorization-support"></a><span data-ttu-id="0b9a7-109">Creare un'app con supporto per l'autorizzazione API</span><span class="sxs-lookup"><span data-stu-id="0b9a7-109">Create an app with API authorization support</span></span>

<span data-ttu-id="0b9a7-110">L'autenticazione e l'autorizzazione degli utenti possono essere usate sia con le ZPS angolari che React.</span><span class="sxs-lookup"><span data-stu-id="0b9a7-110">User authentication and authorization can be used with both Angular and React SPAs.</span></span> <span data-ttu-id="0b9a7-111">Aprire una shell dei comandi ed eseguire il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="0b9a7-111">Open a command shell, and run the following command:</span></span>

<span data-ttu-id="0b9a7-112">**Angolare**:</span><span class="sxs-lookup"><span data-stu-id="0b9a7-112">**Angular**:</span></span>

```dotnetcli
dotnet new angular -o <output_directory_name> -au Individual
```

<span data-ttu-id="0b9a7-113">**Reagire**:</span><span class="sxs-lookup"><span data-stu-id="0b9a7-113">**React**:</span></span>

```dotnetcli
dotnet new react -o <output_directory_name> -au Individual
```

<span data-ttu-id="0b9a7-114">Il comando precedente crea un'app ASP.NET Core con una directory *ClientApp* contenente la Spa.</span><span class="sxs-lookup"><span data-stu-id="0b9a7-114">The preceding command creates an ASP.NET Core app with a *ClientApp* directory containing the SPA.</span></span>

## <a name="general-description-of-the-aspnet-core-components-of-the-app"></a><span data-ttu-id="0b9a7-115">Descrizione generale dei componenti ASP.NET Core dell'app</span><span class="sxs-lookup"><span data-stu-id="0b9a7-115">General description of the ASP.NET Core components of the app</span></span>

<span data-ttu-id="0b9a7-116">Le sezioni seguenti descrivono le aggiunte al progetto quando è incluso il supporto per l'autenticazione:</span><span class="sxs-lookup"><span data-stu-id="0b9a7-116">The following sections describe additions to the project when authentication support is included:</span></span>

### <a name="startup-class"></a><span data-ttu-id="0b9a7-117">Classe di avvio</span><span class="sxs-lookup"><span data-stu-id="0b9a7-117">Startup class</span></span>

<span data-ttu-id="0b9a7-118">Gli esempi di codice seguenti si basano sul pacchetto NuGet [Microsoft. AspNetCore. ApiAuthorization. IdentityServer](https://www.nuget.org/packages/Microsoft.AspNetCore.ApiAuthorization.IdentityServer) .</span><span class="sxs-lookup"><span data-stu-id="0b9a7-118">The following code examples rely on the [Microsoft.AspNetCore.ApiAuthorization.IdentityServer](https://www.nuget.org/packages/Microsoft.AspNetCore.ApiAuthorization.IdentityServer) NuGet package.</span></span> <span data-ttu-id="0b9a7-119">Gli esempi configurano l'autenticazione e l'autorizzazione dell'API usando i <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiResourceCollection.AddIdentityServerJwt%2A> metodi di estensione e.</span><span class="sxs-lookup"><span data-stu-id="0b9a7-119">The examples configure API authentication and authorization using the <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> and <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiResourceCollection.AddIdentityServerJwt%2A> extension methods.</span></span> <span data-ttu-id="0b9a7-120">I progetti che usano i modelli di progetto REACT o unangolar SPA con autenticazione includono un riferimento a questo pacchetto.</span><span class="sxs-lookup"><span data-stu-id="0b9a7-120">Projects using the React or Angular SPA project templates with authentication include a reference to this package.</span></span>

<span data-ttu-id="0b9a7-121">La `Startup` classe presenta le aggiunte seguenti:</span><span class="sxs-lookup"><span data-stu-id="0b9a7-121">The `Startup` class has the following additions:</span></span>

* <span data-ttu-id="0b9a7-122">All'interno del `Startup.ConfigureServices` Metodo:</span><span class="sxs-lookup"><span data-stu-id="0b9a7-122">Inside the `Startup.ConfigureServices` method:</span></span>
  * Identity<span data-ttu-id="0b9a7-123">con l'interfaccia utente predefinita:</span><span class="sxs-lookup"><span data-stu-id="0b9a7-123"> with the default UI:</span></span>

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>()
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * <span data-ttu-id="0b9a7-124">IdentityServer con un `AddApiAuthorization` metodo helper aggiuntivo che configura alcune convenzioni ASP.NET Core predefinite in IdentityServer:</span><span class="sxs-lookup"><span data-stu-id="0b9a7-124">IdentityServer with an additional `AddApiAuthorization` helper method that sets up some default ASP.NET Core conventions on top of IdentityServer:</span></span>

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * <span data-ttu-id="0b9a7-125">Autenticazione con un `AddIdentityServerJwt` metodo helper aggiuntivo che configura l'app per convalidare i token JWT prodotti da IdentityServer:</span><span class="sxs-lookup"><span data-stu-id="0b9a7-125">Authentication with an additional `AddIdentityServerJwt` helper method that configures the app to validate JWT tokens produced by IdentityServer:</span></span>

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* <span data-ttu-id="0b9a7-126">All'interno del `Startup.Configure` Metodo:</span><span class="sxs-lookup"><span data-stu-id="0b9a7-126">Inside the `Startup.Configure` method:</span></span>
  * <span data-ttu-id="0b9a7-127">Il middleware di autenticazione responsabile della convalida delle credenziali della richiesta e dell'impostazione dell'utente nel contesto della richiesta:</span><span class="sxs-lookup"><span data-stu-id="0b9a7-127">The authentication middleware that is responsible for validating the request credentials and setting the user on the request context:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

  * <span data-ttu-id="0b9a7-128">Il middleware IdentityServer che espone gli endpoint di connessione Open ID:</span><span class="sxs-lookup"><span data-stu-id="0b9a7-128">The IdentityServer middleware that exposes the Open ID Connect endpoints:</span></span>

    ```csharp
    app.UseIdentityServer();
    ```

### <a name="addapiauthorization"></a><span data-ttu-id="0b9a7-129">AddApiAuthorization</span><span class="sxs-lookup"><span data-stu-id="0b9a7-129">AddApiAuthorization</span></span>

<span data-ttu-id="0b9a7-130">Questo metodo helper configura IdentityServer in modo da usare la configurazione supportata.</span><span class="sxs-lookup"><span data-stu-id="0b9a7-130">This helper method configures IdentityServer to use our supported configuration.</span></span> <span data-ttu-id="0b9a7-131">IdentityServer è un Framework potente ed estendibile per la gestione dei problemi di sicurezza delle app.</span><span class="sxs-lookup"><span data-stu-id="0b9a7-131">IdentityServer is a powerful and extensible framework for handling app security concerns.</span></span> <span data-ttu-id="0b9a7-132">Allo stesso tempo, che espone complessità superflua per gli scenari più comuni.</span><span class="sxs-lookup"><span data-stu-id="0b9a7-132">At the same time, that exposes unnecessary complexity for the most common scenarios.</span></span> <span data-ttu-id="0b9a7-133">Di conseguenza, viene fornito un set di convenzioni e opzioni di configurazione che sono considerati un valido punto di partenza.</span><span class="sxs-lookup"><span data-stu-id="0b9a7-133">Consequently, a set of conventions and configuration options is provided to you that are considered a good starting point.</span></span> <span data-ttu-id="0b9a7-134">Una volta modificate le esigenze di autenticazione, è ancora disponibile la potenza completa di IdentityServer per personalizzare l'autenticazione in base alle esigenze.</span><span class="sxs-lookup"><span data-stu-id="0b9a7-134">Once your authentication needs change, the full power of IdentityServer is still available to customize authentication to suit your needs.</span></span>

### <a name="addidentityserverjwt"></a><span data-ttu-id="0b9a7-135">AddIdentityServerJwt</span><span class="sxs-lookup"><span data-stu-id="0b9a7-135">AddIdentityServerJwt</span></span>

<span data-ttu-id="0b9a7-136">Questo metodo helper configura uno schema di criteri per l'app come gestore di autenticazione predefinito.</span><span class="sxs-lookup"><span data-stu-id="0b9a7-136">This helper method configures a policy scheme for the app as the default authentication handler.</span></span> <span data-ttu-id="0b9a7-137">Il criterio è configurato in modo da consentire la Identity gestione di tutte le richieste indirizzate a qualsiasi sottopercorso nello Identity spazio URL "/ Identity ".</span><span class="sxs-lookup"><span data-stu-id="0b9a7-137">The policy is configured to let Identity handle all requests routed to any subpath in the Identity URL space "/Identity".</span></span> <span data-ttu-id="0b9a7-138">`JwtBearerHandler`Gestisce tutte le altre richieste.</span><span class="sxs-lookup"><span data-stu-id="0b9a7-138">The `JwtBearerHandler` handles all other requests.</span></span> <span data-ttu-id="0b9a7-139">Questo metodo registra inoltre una `<<ApplicationName>>API` risorsa API con IdentityServer con un ambito predefinito `<<ApplicationName>>API` e configura il middleware del token di porta JWT per convalidare i token emessi da IdentityServer per l'app.</span><span class="sxs-lookup"><span data-stu-id="0b9a7-139">Additionally, this method registers an `<<ApplicationName>>API` API resource with IdentityServer with a default scope of `<<ApplicationName>>API` and configures the JWT Bearer token middleware to validate tokens issued by IdentityServer for the app.</span></span>

### <a name="weatherforecastcontroller"></a><span data-ttu-id="0b9a7-140">WeatherForecastController</span><span class="sxs-lookup"><span data-stu-id="0b9a7-140">WeatherForecastController</span></span>

<span data-ttu-id="0b9a7-141">Nel file *Controllers\WeatherForecastController.cs* , si noti l' `[Authorize]` attributo applicato alla classe che indica che l'utente deve essere autorizzato in base ai criteri predefiniti per accedere alla risorsa.</span><span class="sxs-lookup"><span data-stu-id="0b9a7-141">In the *Controllers\WeatherForecastController.cs* file, notice the `[Authorize]` attribute applied to the class that indicates that the user needs to be authorized based on the default policy to access the resource.</span></span> <span data-ttu-id="0b9a7-142">I criteri di autorizzazione predefiniti vengono configurati per l'utilizzo dello schema di autenticazione predefinito, configurato da `AddIdentityServerJwt` allo schema dei criteri menzionato in precedenza, rendendo `JwtBearerHandler` configurato da tale metodo helper il gestore predefinito per le richieste all'app.</span><span class="sxs-lookup"><span data-stu-id="0b9a7-142">The default authorization policy happens to be configured to use the default authentication scheme, which is set up by `AddIdentityServerJwt` to the policy scheme that was mentioned above, making the `JwtBearerHandler` configured by such helper method the default handler for requests to the app.</span></span>

### <a name="applicationdbcontext"></a><span data-ttu-id="0b9a7-143">ApplicationDbContext</span><span class="sxs-lookup"><span data-stu-id="0b9a7-143">ApplicationDbContext</span></span>

<span data-ttu-id="0b9a7-144">Nel file *Data\ApplicationDbContext.cs* si noti che lo stesso `DbContext` viene usato in Identity con l'eccezione che estende `ApiAuthorizationDbContext` (una classe più derivata da `IdentityDbContext` ) per includere lo schema per IdentityServer.</span><span class="sxs-lookup"><span data-stu-id="0b9a7-144">In the *Data\ApplicationDbContext.cs* file, notice the same `DbContext` is used in Identity with the exception that it extends `ApiAuthorizationDbContext` (a more derived class from `IdentityDbContext`) to include the schema for IdentityServer.</span></span>

<span data-ttu-id="0b9a7-145">Per ottenere il controllo completo dello schema del database, ereditare da una delle Identity `DbContext` classi disponibili e configurare il contesto per includere lo Identity schema chiamando `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` sul `OnModelCreating` metodo.</span><span class="sxs-lookup"><span data-stu-id="0b9a7-145">To gain full control of the database schema, inherit from one of the available Identity `DbContext` classes and configure the context to include the Identity schema by calling `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` on the `OnModelCreating` method.</span></span>

### <a name="oidcconfigurationcontroller"></a><span data-ttu-id="0b9a7-146">OidcConfigurationController</span><span class="sxs-lookup"><span data-stu-id="0b9a7-146">OidcConfigurationController</span></span>

<span data-ttu-id="0b9a7-147">Nel file *Controllers\OidcConfigurationController.cs* , si noti l'endpoint di cui è stato effettuato il provisioning per gestire i parametri di OIDC che il client deve usare.</span><span class="sxs-lookup"><span data-stu-id="0b9a7-147">In the *Controllers\OidcConfigurationController.cs* file, notice the endpoint that's provisioned to serve the OIDC parameters that the client needs to use.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="0b9a7-148">appsettings.json</span><span class="sxs-lookup"><span data-stu-id="0b9a7-148">appsettings.json</span></span>

<span data-ttu-id="0b9a7-149">Nella *appsettings.jssul* file della radice del progetto è presente una nuova `IdentityServer` sezione che descrive l'elenco dei client configurati.</span><span class="sxs-lookup"><span data-stu-id="0b9a7-149">In the *appsettings.json* file of the project root, there's a new `IdentityServer` section that describes the list of configured clients.</span></span> <span data-ttu-id="0b9a7-150">Nell'esempio seguente è presente un singolo client.</span><span class="sxs-lookup"><span data-stu-id="0b9a7-150">In the following example, there's a single client.</span></span> <span data-ttu-id="0b9a7-151">Il nome del client corrisponde al nome dell'app e viene mappato per convenzione al `ClientId` parametro OAuth.</span><span class="sxs-lookup"><span data-stu-id="0b9a7-151">The client name corresponds to the app name and is mapped by convention to the OAuth `ClientId` parameter.</span></span> <span data-ttu-id="0b9a7-152">Il profilo indica il tipo di app da configurare.</span><span class="sxs-lookup"><span data-stu-id="0b9a7-152">The profile indicates the app type being configured.</span></span> <span data-ttu-id="0b9a7-153">Viene usato internamente per guidare le convenzioni che semplificano il processo di configurazione per il server.</span><span class="sxs-lookup"><span data-stu-id="0b9a7-153">It's used internally to drive conventions that simplify the configuration process for the server.</span></span> <span data-ttu-id="0b9a7-154">Sono disponibili diversi profili, come illustrato nella sezione [profili applicazione](#application-profiles) .</span><span class="sxs-lookup"><span data-stu-id="0b9a7-154">There are several profiles available, as explained in the [Application profiles](#application-profiles) section.</span></span>

```json
"IdentityServer": {
  "Clients": {
    "angularindividualpreview3final": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

### <a name="appsettingsdevelopmentjson"></a><span data-ttu-id="0b9a7-155">appsettings.Development.js</span><span class="sxs-lookup"><span data-stu-id="0b9a7-155">appsettings.Development.json</span></span>

<span data-ttu-id="0b9a7-156">Nella *appsettings.Development.jssul* file della radice del progetto è presente una `IdentityServer` sezione che descrive la chiave usata per firmare i token.</span><span class="sxs-lookup"><span data-stu-id="0b9a7-156">In the *appsettings.Development.json* file of the project root, there's an `IdentityServer` section that describes the key used to sign tokens.</span></span> <span data-ttu-id="0b9a7-157">Quando si esegue la distribuzione nell'ambiente di produzione, è necessario eseguire il provisioning di una chiave e distribuirla insieme all'app, come illustrato nella sezione [Deploy to Production](#deploy-to-production) .</span><span class="sxs-lookup"><span data-stu-id="0b9a7-157">When deploying to production, a key needs to be provisioned and deployed alongside the app, as explained in the [Deploy to production](#deploy-to-production) section.</span></span>

```json
"IdentityServer": {
  "Key": {
    "Type": "Development"
  }
}
```

## <a name="general-description-of-the-angular-app"></a><span data-ttu-id="0b9a7-158">Descrizione generale dell'app angolare</span><span class="sxs-lookup"><span data-stu-id="0b9a7-158">General description of the Angular app</span></span>

<span data-ttu-id="0b9a7-159">Il supporto per l'autenticazione e l'autorizzazione API nel modello angolare si trova nel proprio modulo angolare nella directory *ClientApp\src\api-Authorization*</span><span class="sxs-lookup"><span data-stu-id="0b9a7-159">The authentication and API authorization support in the Angular template resides in its own Angular module in the *ClientApp\src\api-authorization* directory.</span></span> <span data-ttu-id="0b9a7-160">Il modulo è costituito dagli elementi seguenti:</span><span class="sxs-lookup"><span data-stu-id="0b9a7-160">The module is composed of the following elements:</span></span>

* <span data-ttu-id="0b9a7-161">3 componenti:</span><span class="sxs-lookup"><span data-stu-id="0b9a7-161">3 components:</span></span>
  * <span data-ttu-id="0b9a7-162">*login. Component. TS*: gestisce il flusso di accesso dell'app.</span><span class="sxs-lookup"><span data-stu-id="0b9a7-162">*login.component.ts*: Handles the app's login flow.</span></span>
  * <span data-ttu-id="0b9a7-163">*Logout. Component. TS*: gestisce il flusso di disconnessione dell'app.</span><span class="sxs-lookup"><span data-stu-id="0b9a7-163">*logout.component.ts*: Handles the app's logout flow.</span></span>
  * <span data-ttu-id="0b9a7-164">*login-menu. Component. TS*: widget che visualizza uno dei seguenti set di collegamenti:</span><span class="sxs-lookup"><span data-stu-id="0b9a7-164">*login-menu.component.ts*: A widget that displays one of the following sets of links:</span></span>
    * <span data-ttu-id="0b9a7-165">Collegamenti di gestione dei profili utente e disconnessione quando l'utente viene autenticato.</span><span class="sxs-lookup"><span data-stu-id="0b9a7-165">User profile management and log out links when the user is authenticated.</span></span>
    * <span data-ttu-id="0b9a7-166">Collegamenti per la registrazione e l'accesso quando l'utente non è autenticato.</span><span class="sxs-lookup"><span data-stu-id="0b9a7-166">Registration and log in links when the user isn't authenticated.</span></span>
* <span data-ttu-id="0b9a7-167">Una protezione `AuthorizeGuard` della route che può essere aggiunta alle route e richiede che un utente venga autenticato prima di visitare la route.</span><span class="sxs-lookup"><span data-stu-id="0b9a7-167">A route guard `AuthorizeGuard` that can be added to routes and requires a user to be authenticated before visiting the route.</span></span>
* <span data-ttu-id="0b9a7-168">Un intercettore HTTP `AuthorizeInterceptor` che connette il token di accesso alle richieste HTTP in uscita destinate all'API quando l'utente viene autenticato.</span><span class="sxs-lookup"><span data-stu-id="0b9a7-168">An HTTP interceptor `AuthorizeInterceptor` that attaches the access token to outgoing HTTP requests targeting the API when the user is authenticated.</span></span>
* <span data-ttu-id="0b9a7-169">Un servizio `AuthorizeService` che gestisce i dettagli di basso livello del processo di autenticazione ed espone le informazioni relative all'utente autenticato al resto dell'app per l'utilizzo.</span><span class="sxs-lookup"><span data-stu-id="0b9a7-169">A service `AuthorizeService` that handles the lower-level details of the authentication process and exposes information about the authenticated user to the rest of the app for consumption.</span></span>
* <span data-ttu-id="0b9a7-170">Un modulo angolare che definisce le route associate alle parti di autenticazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="0b9a7-170">An Angular module that defines routes associated with the authentication parts of the app.</span></span> <span data-ttu-id="0b9a7-171">Espone il componente del menu di accesso, l'intercettore, il Guard e il servizio per l'utilizzo dal resto dell'app.</span><span class="sxs-lookup"><span data-stu-id="0b9a7-171">It exposes the login menu component, the interceptor, the guard, and the service for consumption from the rest of the app.</span></span>

## <a name="general-description-of-the-react-app"></a><span data-ttu-id="0b9a7-172">Descrizione generale dell'app React</span><span class="sxs-lookup"><span data-stu-id="0b9a7-172">General description of the React app</span></span>

<span data-ttu-id="0b9a7-173">Il supporto per l'autenticazione e l'autorizzazione API nel modello React risiede nella directory *ClientApp\src\components\api-Authorization*</span><span class="sxs-lookup"><span data-stu-id="0b9a7-173">The support for authentication and API authorization in the React template resides in the *ClientApp\src\components\api-authorization* directory.</span></span> <span data-ttu-id="0b9a7-174">È costituito dagli elementi seguenti:</span><span class="sxs-lookup"><span data-stu-id="0b9a7-174">It's composed of the following elements:</span></span>

* <span data-ttu-id="0b9a7-175">4 componenti:</span><span class="sxs-lookup"><span data-stu-id="0b9a7-175">4 components:</span></span>
  * <span data-ttu-id="0b9a7-176">*Login.js*: gestisce il flusso di accesso dell'app.</span><span class="sxs-lookup"><span data-stu-id="0b9a7-176">*Login.js*: Handles the app's login flow.</span></span>
  * <span data-ttu-id="0b9a7-177">*Logout.js*: gestisce il flusso di disconnessione dell'app.</span><span class="sxs-lookup"><span data-stu-id="0b9a7-177">*Logout.js*: Handles the app's logout flow.</span></span>
  * <span data-ttu-id="0b9a7-178">*LoginMenu.js*: widget che visualizza uno dei seguenti set di collegamenti:</span><span class="sxs-lookup"><span data-stu-id="0b9a7-178">*LoginMenu.js*: A widget that displays one of the following sets of links:</span></span>
    * <span data-ttu-id="0b9a7-179">Collegamenti di gestione dei profili utente e disconnessione quando l'utente viene autenticato.</span><span class="sxs-lookup"><span data-stu-id="0b9a7-179">User profile management and log out links when the user is authenticated.</span></span>
    * <span data-ttu-id="0b9a7-180">Collegamenti per la registrazione e l'accesso quando l'utente non è autenticato.</span><span class="sxs-lookup"><span data-stu-id="0b9a7-180">Registration and log in links when the user isn't authenticated.</span></span>
  * <span data-ttu-id="0b9a7-181">*AuthorizeRoute.js*: componente di route che richiede l'autenticazione di un utente prima di eseguire il rendering del componente indicato nel `Component` parametro.</span><span class="sxs-lookup"><span data-stu-id="0b9a7-181">*AuthorizeRoute.js*: A route component that requires a user to be authenticated before rendering the component indicated in the `Component` parameter.</span></span>
* <span data-ttu-id="0b9a7-182">Istanza esportata `authService` della classe `AuthorizeService` che gestisce i dettagli di basso livello del processo di autenticazione ed espone le informazioni relative all'utente autenticato al resto dell'app per l'utilizzo.</span><span class="sxs-lookup"><span data-stu-id="0b9a7-182">An exported `authService` instance of class `AuthorizeService` that handles the lower-level details of the authentication process and exposes information about the authenticated user to the rest of the app for consumption.</span></span>

<span data-ttu-id="0b9a7-183">Ora che sono stati esaminati i componenti principali della soluzione, è possibile approfondire i singoli scenari per l'app.</span><span class="sxs-lookup"><span data-stu-id="0b9a7-183">Now that you've seen the main components of the solution, you can take a deeper look at individual scenarios for the app.</span></span>

## <a name="require-authorization-on-a-new-api"></a><span data-ttu-id="0b9a7-184">Richiedi autorizzazione per una nuova API</span><span class="sxs-lookup"><span data-stu-id="0b9a7-184">Require authorization on a new API</span></span>

<span data-ttu-id="0b9a7-185">Per impostazione predefinita, il sistema è configurato in modo da richiedere facilmente l'autorizzazione per le nuove API.</span><span class="sxs-lookup"><span data-stu-id="0b9a7-185">By default, the system is configured to easily require authorization for new APIs.</span></span> <span data-ttu-id="0b9a7-186">A tale scopo, creare un nuovo controller e aggiungere l' `[Authorize]` attributo alla classe controller o a qualsiasi azione all'interno del controller.</span><span class="sxs-lookup"><span data-stu-id="0b9a7-186">To do so, create a new controller and add the `[Authorize]` attribute to the controller class or to any action within the controller.</span></span>

## <a name="customize-the-api-authentication-handler"></a><span data-ttu-id="0b9a7-187">Personalizzare il gestore di autenticazione API</span><span class="sxs-lookup"><span data-stu-id="0b9a7-187">Customize the API authentication handler</span></span>

<span data-ttu-id="0b9a7-188">Per personalizzare la configurazione del gestore JWT dell'API, configurare la relativa <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> istanza:</span><span class="sxs-lookup"><span data-stu-id="0b9a7-188">To customize the configuration of the API's JWT handler, configure its <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> instance:</span></span>

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

<span data-ttu-id="0b9a7-189">Il gestore JWT dell'API genera eventi che consentono di controllare il processo di autenticazione tramite `JwtBearerEvents` .</span><span class="sxs-lookup"><span data-stu-id="0b9a7-189">The API's JWT handler raises events that enable control over the authentication process using `JwtBearerEvents`.</span></span> <span data-ttu-id="0b9a7-190">Per fornire il supporto per l'autorizzazione dell'API, `AddIdentityServerJwt` registra i relativi gestori eventi.</span><span class="sxs-lookup"><span data-stu-id="0b9a7-190">To provide support for API authorization, `AddIdentityServerJwt` registers its own event handlers.</span></span>

<span data-ttu-id="0b9a7-191">Per personalizzare la gestione di un evento, eseguire il wrapping del gestore eventi esistente con logica aggiuntiva, se necessario.</span><span class="sxs-lookup"><span data-stu-id="0b9a7-191">To customize the handling of an event, wrap the existing event handler with additional logic as required.</span></span> <span data-ttu-id="0b9a7-192">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="0b9a7-192">For example:</span></span>

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

<span data-ttu-id="0b9a7-193">Nel codice precedente, il `OnTokenValidated` gestore eventi viene sostituito con un'implementazione personalizzata.</span><span class="sxs-lookup"><span data-stu-id="0b9a7-193">In the preceding code, the `OnTokenValidated` event handler is replaced with a custom implementation.</span></span> <span data-ttu-id="0b9a7-194">Questa implementazione:</span><span class="sxs-lookup"><span data-stu-id="0b9a7-194">This implementation:</span></span>

1. <span data-ttu-id="0b9a7-195">Chiama l'implementazione originale fornita dal supporto dell'autorizzazione dell'API.</span><span class="sxs-lookup"><span data-stu-id="0b9a7-195">Calls the original implementation provided by the API authorization support.</span></span>
1. <span data-ttu-id="0b9a7-196">Eseguire la propria logica personalizzata.</span><span class="sxs-lookup"><span data-stu-id="0b9a7-196">Run its own custom logic.</span></span>

## <a name="protect-a-client-side-route-angular"></a><span data-ttu-id="0b9a7-197">Proteggere una route lato client (angolare)</span><span class="sxs-lookup"><span data-stu-id="0b9a7-197">Protect a client-side route (Angular)</span></span>

<span data-ttu-id="0b9a7-198">Per la protezione di una route sul lato client, è necessario aggiungere l'autorizzazione Guard all'elenco delle protezioni da eseguire durante la configurazione di una route.</span><span class="sxs-lookup"><span data-stu-id="0b9a7-198">Protecting a client-side route is done by adding the authorize guard to the list of guards to run when configuring a route.</span></span> <span data-ttu-id="0b9a7-199">Come esempio, è possibile vedere come la `fetch-data` Route è configurata nel modulo principale dell'app:</span><span class="sxs-lookup"><span data-stu-id="0b9a7-199">As an example, you can see how the `fetch-data` route is configured within the main app Angular module:</span></span>

```typescript
RouterModule.forRoot([
  // ...
  { path: 'fetch-data', component: FetchDataComponent, canActivate: [AuthorizeGuard] },
])
```

<span data-ttu-id="0b9a7-200">È importante ricordare che la protezione di una route non protegge l'endpoint effettivo (a cui è ancora necessario `[Authorize]` applicare un attributo) ma impedisce solo all'utente di passare alla route specificata sul lato client quando non è autenticato.</span><span class="sxs-lookup"><span data-stu-id="0b9a7-200">It's important to mention that protecting a route doesn't protect the actual endpoint (which still requires an `[Authorize]` attribute applied to it) but that it only prevents the user from navigating to the given client-side route when it isn't authenticated.</span></span>

## <a name="authenticate-api-requests-angular"></a><span data-ttu-id="0b9a7-201">Autenticare le richieste API (angolare)</span><span class="sxs-lookup"><span data-stu-id="0b9a7-201">Authenticate API requests (Angular)</span></span>

<span data-ttu-id="0b9a7-202">L'autenticazione delle richieste alle API ospitate insieme all'app viene eseguita automaticamente tramite l'uso dell'intercettore client HTTP definito dall'app.</span><span class="sxs-lookup"><span data-stu-id="0b9a7-202">Authenticating requests to APIs hosted alongside the app is done automatically through the use of the HTTP client interceptor defined by the app.</span></span>

## <a name="protect-a-client-side-route-react"></a><span data-ttu-id="0b9a7-203">Proteggere una route lato client (React)</span><span class="sxs-lookup"><span data-stu-id="0b9a7-203">Protect a client-side route (React)</span></span>

<span data-ttu-id="0b9a7-204">Proteggere una route sul lato client usando il `AuthorizeRoute` componente anziché il `Route` componente normale.</span><span class="sxs-lookup"><span data-stu-id="0b9a7-204">Protect a client-side route by using the `AuthorizeRoute` component instead of the plain `Route` component.</span></span> <span data-ttu-id="0b9a7-205">Si noti, ad esempio, il modo in cui la `fetch-data` Route è configurata all'interno del `App` componente:</span><span class="sxs-lookup"><span data-stu-id="0b9a7-205">For example, notice how the `fetch-data` route is configured within the `App` component:</span></span>

```jsx
<AuthorizeRoute path='/fetch-data' component={FetchData} />
```

<span data-ttu-id="0b9a7-206">Protezione di una route:</span><span class="sxs-lookup"><span data-stu-id="0b9a7-206">Protecting a route:</span></span>

* <span data-ttu-id="0b9a7-207">Non protegge l'endpoint effettivo (a cui è ancora necessario `[Authorize]` applicare un attributo).</span><span class="sxs-lookup"><span data-stu-id="0b9a7-207">Doesn't protect the actual endpoint (which still requires an `[Authorize]` attribute applied to it).</span></span>
* <span data-ttu-id="0b9a7-208">Impedisce solo all'utente di passare alla route specificata sul lato client quando non viene autenticata.</span><span class="sxs-lookup"><span data-stu-id="0b9a7-208">Only prevents the user from navigating to the given client-side route when it isn't authenticated.</span></span>

## <a name="authenticate-api-requests-react"></a><span data-ttu-id="0b9a7-209">Autenticare le richieste API (React)</span><span class="sxs-lookup"><span data-stu-id="0b9a7-209">Authenticate API requests (React)</span></span>

<span data-ttu-id="0b9a7-210">L'autenticazione delle richieste con React viene eseguita importando innanzitutto l' `authService` istanza da `AuthorizeService` .</span><span class="sxs-lookup"><span data-stu-id="0b9a7-210">Authenticating requests with React is done by first importing the `authService` instance from the `AuthorizeService`.</span></span> <span data-ttu-id="0b9a7-211">Il token di accesso viene recuperato da `authService` ed è associato alla richiesta, come illustrato di seguito.</span><span class="sxs-lookup"><span data-stu-id="0b9a7-211">The access token is retrieved from the `authService` and is attached to the request as shown below.</span></span> <span data-ttu-id="0b9a7-212">Nei componenti React questa operazione viene in genere eseguita nel `componentDidMount` Metodo Lifecycle o come risultato di un'interazione dell'utente.</span><span class="sxs-lookup"><span data-stu-id="0b9a7-212">In React components, this work is typically done in the `componentDidMount` lifecycle method or as the result from some user interaction.</span></span>

### <a name="import-the-authservice-into-your-component"></a><span data-ttu-id="0b9a7-213">Importare authService nel componente</span><span class="sxs-lookup"><span data-stu-id="0b9a7-213">Import the authService into your component</span></span>

```javascript
import authService from './api-authorization/AuthorizeService'
```

### <a name="retrieve-and-attach-the-access-token-to-the-response"></a><span data-ttu-id="0b9a7-214">Recupera e alleghi il token di accesso alla risposta</span><span class="sxs-lookup"><span data-stu-id="0b9a7-214">Retrieve and attach the access token to the response</span></span>

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

## <a name="deploy-to-production"></a><span data-ttu-id="0b9a7-215">Distribuire nell'ambiente di produzione</span><span class="sxs-lookup"><span data-stu-id="0b9a7-215">Deploy to production</span></span>

<span data-ttu-id="0b9a7-216">Per distribuire l'app nell'ambiente di produzione, è necessario eseguire il provisioning delle risorse seguenti:</span><span class="sxs-lookup"><span data-stu-id="0b9a7-216">To deploy the app to production, the following resources need to be provisioned:</span></span>

* <span data-ttu-id="0b9a7-217">Un database in cui archiviare gli Identity account utente e le concessioni IdentityServer.</span><span class="sxs-lookup"><span data-stu-id="0b9a7-217">A database to store the Identity user accounts and the IdentityServer grants.</span></span>
* <span data-ttu-id="0b9a7-218">Un certificato di produzione da usare per firmare i token.</span><span class="sxs-lookup"><span data-stu-id="0b9a7-218">A production certificate to use for signing tokens.</span></span>
  * <span data-ttu-id="0b9a7-219">Non sono previsti requisiti specifici per il certificato. può essere un certificato autofirmato o un certificato sottoposti a provisioning tramite un'autorità di certificazione.</span><span class="sxs-lookup"><span data-stu-id="0b9a7-219">There are no specific requirements for this certificate; it can be a self-signed certificate or a certificate provisioned through a CA authority.</span></span>
  * <span data-ttu-id="0b9a7-220">Può essere generato tramite strumenti standard come PowerShell o OpenSSL.</span><span class="sxs-lookup"><span data-stu-id="0b9a7-220">It can be generated through standard tools like PowerShell or OpenSSL.</span></span>
  * <span data-ttu-id="0b9a7-221">Può essere installata nell'archivio certificati nei computer di destinazione o distribuita come file con *estensione pfx* con una password complessa.</span><span class="sxs-lookup"><span data-stu-id="0b9a7-221">It can be installed into the certificate store on the target machines or deployed as a *.pfx* file with a strong password.</span></span>

### <a name="example-deploy-to-azure-app-service"></a><span data-ttu-id="0b9a7-222">Esempio: eseguire la distribuzione nel servizio app Azure</span><span class="sxs-lookup"><span data-stu-id="0b9a7-222">Example: Deploy to Azure App Service</span></span>

<span data-ttu-id="0b9a7-223">Questa sezione descrive come distribuire l'app nel servizio app Azure usando un certificato archiviato nell'archivio certificati.</span><span class="sxs-lookup"><span data-stu-id="0b9a7-223">This section describes deploying the app to Azure App Service using a certificate stored in the certificate store.</span></span> <span data-ttu-id="0b9a7-224">Per modificare l'app per caricare un certificato dall'archivio certificati, è necessario un piano di servizio di livello standard o superiore quando si configura l'app nel portale di Azure in un passaggio successivo.</span><span class="sxs-lookup"><span data-stu-id="0b9a7-224">To modify the app to load a certificate from the certificate store, a Standard tier service plan or better is required when you configure the app in the Azure portal in a later step.</span></span>

<span data-ttu-id="0b9a7-225">Nel file *appsettings.js* dell'app, modificare la sezione in `IdentityServer` modo da includere i dettagli della chiave:</span><span class="sxs-lookup"><span data-stu-id="0b9a7-225">In the app's *appsettings.json* file, modify the `IdentityServer` section to include the key details:</span></span>

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

* <span data-ttu-id="0b9a7-226">Il nome dell'archivio rappresenta il nome dell'archivio certificati in cui è archiviato il certificato.</span><span class="sxs-lookup"><span data-stu-id="0b9a7-226">The store name represents the name of the certificate store where the certificate is stored.</span></span> <span data-ttu-id="0b9a7-227">In questo caso, fa riferimento all'archivio utenti personali.</span><span class="sxs-lookup"><span data-stu-id="0b9a7-227">In this case, it points to the personal user store.</span></span>
* <span data-ttu-id="0b9a7-228">Il percorso dell'archivio rappresenta il punto da cui caricare il certificato ( `CurrentUser` o `LocalMachine` ).</span><span class="sxs-lookup"><span data-stu-id="0b9a7-228">The store location represents where to load the certificate from (`CurrentUser` or `LocalMachine`).</span></span>
* <span data-ttu-id="0b9a7-229">La proprietà Name del certificato corrisponde all'oggetto distinto per il certificato.</span><span class="sxs-lookup"><span data-stu-id="0b9a7-229">The name property on certificate corresponds with the distinguished subject for the certificate.</span></span>

<span data-ttu-id="0b9a7-230">Per eseguire la distribuzione nel servizio app Azure, seguire la procedura descritta in [distribuire l'app in Azure](xref:tutorials/publish-to-azure-webapp-using-vs#deploy-the-app-to-azure), che spiega come creare le risorse di Azure necessarie e distribuire l'app nell'ambiente di produzione.</span><span class="sxs-lookup"><span data-stu-id="0b9a7-230">To deploy to Azure App Service, follow the steps in [Deploy the app to Azure](xref:tutorials/publish-to-azure-webapp-using-vs#deploy-the-app-to-azure), which explains how to create the necessary Azure resources and deploy the app to production.</span></span>

<span data-ttu-id="0b9a7-231">Dopo aver seguito le istruzioni precedenti, l'app viene distribuita in Azure, ma non è ancora funzionante.</span><span class="sxs-lookup"><span data-stu-id="0b9a7-231">After following the preceding instructions, the app is deployed to Azure but isn't yet functional.</span></span> <span data-ttu-id="0b9a7-232">Il certificato usato dall'app deve essere configurato nel portale di Azure.</span><span class="sxs-lookup"><span data-stu-id="0b9a7-232">The certificate used by the app must be configured in the Azure portal.</span></span> <span data-ttu-id="0b9a7-233">Individuare l'identificazione personale per il certificato e seguire i passaggi descritti in [caricare i certificati](/azure/app-service/app-service-web-ssl-cert-load#load-the-certificate-in-code).</span><span class="sxs-lookup"><span data-stu-id="0b9a7-233">Locate the thumbprint for the certificate and follow the steps described in [Load your certificates](/azure/app-service/app-service-web-ssl-cert-load#load-the-certificate-in-code).</span></span>

<span data-ttu-id="0b9a7-234">Sebbene questi passaggi menzionino SSL, nella portale di Azure è possibile caricare il certificato di cui è stato **effettuato il** provisioning da usare con l'app.</span><span class="sxs-lookup"><span data-stu-id="0b9a7-234">While these steps mention SSL, there's a **Private certificates** section in the Azure portal where you can upload the provisioned certificate to use with the app.</span></span>

<span data-ttu-id="0b9a7-235">Dopo aver configurato l'app e le impostazioni dell'app nel portale di Azure, riavviare l'app nel portale.</span><span class="sxs-lookup"><span data-stu-id="0b9a7-235">After configuring the app and the app's settings in the Azure portal, restart the app in the portal.</span></span>

## <a name="other-configuration-options"></a><span data-ttu-id="0b9a7-236">Altre opzioni di configurazione</span><span class="sxs-lookup"><span data-stu-id="0b9a7-236">Other configuration options</span></span>

<span data-ttu-id="0b9a7-237">Il supporto per l'autorizzazione API si basa su IdentityServer con un set di convenzioni, valori predefiniti e miglioramenti per semplificare l'esperienza per le Spa.</span><span class="sxs-lookup"><span data-stu-id="0b9a7-237">The support for API authorization builds on top of IdentityServer with a set of conventions, default values, and enhancements to simplify the experience for SPAs.</span></span> <span data-ttu-id="0b9a7-238">Inutile dire che la potenza completa di IdentityServer è disponibile dietro le quinte se le integrazioni ASP.NET Core non coprono lo scenario.</span><span class="sxs-lookup"><span data-stu-id="0b9a7-238">Needless to say, the full power of IdentityServer is available behind the scenes if the ASP.NET Core integrations don't cover your scenario.</span></span> <span data-ttu-id="0b9a7-239">Il supporto ASP.NET Core si concentra sulle app "First-Party", in cui tutte le app vengono create e distribuite dall'organizzazione.</span><span class="sxs-lookup"><span data-stu-id="0b9a7-239">The ASP.NET Core support is focused on "first-party" apps, where all the apps are created and deployed by our organization.</span></span> <span data-ttu-id="0b9a7-240">Di conseguenza, il supporto non viene offerto per elementi come il consenso o la Federazione.</span><span class="sxs-lookup"><span data-stu-id="0b9a7-240">As such, support isn't offered for things like consent or federation.</span></span> <span data-ttu-id="0b9a7-241">Per questi scenari, usare IdentityServer e seguire la relativa documentazione.</span><span class="sxs-lookup"><span data-stu-id="0b9a7-241">For those scenarios, use IdentityServer and follow their documentation.</span></span>

### <a name="application-profiles"></a><span data-ttu-id="0b9a7-242">Profili applicazione</span><span class="sxs-lookup"><span data-stu-id="0b9a7-242">Application profiles</span></span>

<span data-ttu-id="0b9a7-243">I profili dell'applicazione sono configurazioni predefinite per le app che definiscono ulteriormente i parametri.</span><span class="sxs-lookup"><span data-stu-id="0b9a7-243">Application profiles are predefined configurations for apps that further define their parameters.</span></span> <span data-ttu-id="0b9a7-244">A questo punto sono supportati i profili seguenti:</span><span class="sxs-lookup"><span data-stu-id="0b9a7-244">At this time, the following profiles are supported:</span></span>

* <span data-ttu-id="0b9a7-245">`IdentityServerSPA`: Rappresenta una SPA ospitata insieme a IdentityServer come singola unità.</span><span class="sxs-lookup"><span data-stu-id="0b9a7-245">`IdentityServerSPA`: Represents a SPA hosted alongside IdentityServer as a single unit.</span></span>
  * <span data-ttu-id="0b9a7-246">Il `redirect_uri` valore predefinito è `/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="0b9a7-246">The `redirect_uri` defaults to `/authentication/login-callback`.</span></span>
  * <span data-ttu-id="0b9a7-247">Il `post_logout_redirect_uri` valore predefinito è `/authentication/logout-callback` .</span><span class="sxs-lookup"><span data-stu-id="0b9a7-247">The `post_logout_redirect_uri` defaults to `/authentication/logout-callback`.</span></span>
  * <span data-ttu-id="0b9a7-248">Il set di ambiti include `openid` , `profile` e ogni ambito definito per le API nell'app.</span><span class="sxs-lookup"><span data-stu-id="0b9a7-248">The set of scopes includes the `openid`, `profile`, and every scope defined for the APIs in the app.</span></span>
  * <span data-ttu-id="0b9a7-249">Il set di tipi di risposta OIDC consentiti è `id_token token` o singolarmente ( `id_token` , `token` ).</span><span class="sxs-lookup"><span data-stu-id="0b9a7-249">The set of allowed OIDC response types is `id_token token` or each of them individually (`id_token`, `token`).</span></span>
  * <span data-ttu-id="0b9a7-250">La modalità di risposta consentita è `fragment` .</span><span class="sxs-lookup"><span data-stu-id="0b9a7-250">The allowed response mode is `fragment`.</span></span>
* <span data-ttu-id="0b9a7-251">`SPA`: Rappresenta una SPA non ospitata con IdentityServer.</span><span class="sxs-lookup"><span data-stu-id="0b9a7-251">`SPA`: Represents a SPA that isn't hosted with IdentityServer.</span></span>
  * <span data-ttu-id="0b9a7-252">Il set di ambiti include `openid` , `profile` e ogni ambito definito per le API nell'app.</span><span class="sxs-lookup"><span data-stu-id="0b9a7-252">The set of scopes includes the `openid`, `profile`, and every scope defined for the APIs in the app.</span></span>
  * <span data-ttu-id="0b9a7-253">Il set di tipi di risposta OIDC consentiti è `id_token token` o singolarmente ( `id_token` , `token` ).</span><span class="sxs-lookup"><span data-stu-id="0b9a7-253">The set of allowed OIDC response types is `id_token token` or each of them individually (`id_token`, `token`).</span></span>
  * <span data-ttu-id="0b9a7-254">La modalità di risposta consentita è `fragment` .</span><span class="sxs-lookup"><span data-stu-id="0b9a7-254">The allowed response mode is `fragment`.</span></span>
* <span data-ttu-id="0b9a7-255">`IdentityServerJwt`: Rappresenta un'API ospitata insieme a IdentityServer.</span><span class="sxs-lookup"><span data-stu-id="0b9a7-255">`IdentityServerJwt`: Represents an API that is hosted alongside with IdentityServer.</span></span>
  * <span data-ttu-id="0b9a7-256">L'app è configurata in modo da avere un singolo ambito per impostazione predefinita sul nome dell'app.</span><span class="sxs-lookup"><span data-stu-id="0b9a7-256">The app is configured to have a single scope that defaults to the app name.</span></span>
* <span data-ttu-id="0b9a7-257">`API`: Rappresenta un'API non ospitata con IdentityServer.</span><span class="sxs-lookup"><span data-stu-id="0b9a7-257">`API`: Represents an API that isn't hosted with IdentityServer.</span></span>
  * <span data-ttu-id="0b9a7-258">L'app è configurata in modo da avere un singolo ambito per impostazione predefinita sul nome dell'app.</span><span class="sxs-lookup"><span data-stu-id="0b9a7-258">The app is configured to have a single scope that defaults to the app name.</span></span>

### <a name="configuration-through-appsettings"></a><span data-ttu-id="0b9a7-259">Configurazione tramite AppSettings</span><span class="sxs-lookup"><span data-stu-id="0b9a7-259">Configuration through AppSettings</span></span>

<span data-ttu-id="0b9a7-260">Configurare le app tramite il sistema di configurazione aggiungendole all'elenco di `Clients` o `Resources` .</span><span class="sxs-lookup"><span data-stu-id="0b9a7-260">Configure the apps through the configuration system by adding them to the list of `Clients` or `Resources`.</span></span>

<span data-ttu-id="0b9a7-261">Configurare `redirect_uri` la proprietà e di ogni client `post_logout_redirect_uri` , come illustrato nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="0b9a7-261">Configure each client's `redirect_uri` and `post_logout_redirect_uri` property, as shown in the following example:</span></span>

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

<span data-ttu-id="0b9a7-262">Quando si configurano le risorse, è possibile configurare gli ambiti per la risorsa come illustrato di seguito:</span><span class="sxs-lookup"><span data-stu-id="0b9a7-262">When configuring resources, you can configure the scopes for the resource as shown below:</span></span>

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

### <a name="configuration-through-code"></a><span data-ttu-id="0b9a7-263">Configurazione tramite codice</span><span class="sxs-lookup"><span data-stu-id="0b9a7-263">Configuration through code</span></span>

<span data-ttu-id="0b9a7-264">È anche possibile configurare i client e le risorse tramite codice usando un overload di `AddApiAuthorization` che esegue un'azione per configurare le opzioni.</span><span class="sxs-lookup"><span data-stu-id="0b9a7-264">You can also configure the clients and resources through code using an overload of `AddApiAuthorization` that takes an action to configure options.</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="0b9a7-265">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="0b9a7-265">Additional resources</span></span>

* <xref:spa/angular>
* <xref:spa/react>
* <xref:security/authentication/scaffold-identity>
