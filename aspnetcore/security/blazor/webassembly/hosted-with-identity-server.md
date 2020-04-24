---
title: Proteggere un'app Blazor ospitata in un ASP.NET Core webassembly con Identity Server
author: guardrex
description: Per creare una nuova Blazor app ospitata con autenticazione da Visual Studio che usa un back-end [IdentityServer](https://identityserver.io/)
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/22/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/hosted-with-identity-server
ms.openlocfilehash: f8de07e2e21ca19b5c4e95839e7b7e621c335ad0
ms.sourcegitcommit: 7bb14d005155a5044c7902a08694ee8ccb20c113
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82110947"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-hosted-app-with-identity-server"></a><span data-ttu-id="c1d22-103">Proteggere un'app Blazor ospitata in un ASP.NET Core webassembly con Identity Server</span><span class="sxs-lookup"><span data-stu-id="c1d22-103">Secure an ASP.NET Core Blazor WebAssembly hosted app with Identity Server</span></span>

<span data-ttu-id="c1d22-104">Di [Javier Calvarro Nelson](https://github.com/javiercn) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="c1d22-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

> [!NOTE]
> <span data-ttu-id="c1d22-105">Le indicazioni fornite in questo articolo si applicano a ASP.NET Core 3,2 Preview 4.</span><span class="sxs-lookup"><span data-stu-id="c1d22-105">The guidance in this article applies to ASP.NET Core 3.2 Preview 4.</span></span> <span data-ttu-id="c1d22-106">Questo argomento verrà aggiornato per coprire l'anteprima 5 il venerdì 24 aprile.</span><span class="sxs-lookup"><span data-stu-id="c1d22-106">This topic will be updated to cover Preview 5 on Friday, April 24.</span></span>

<span data-ttu-id="c1d22-107">Per creare una nuova Blazor app ospitata in Visual Studio che usa [IdentityServer](https://identityserver.io/) per autenticare gli utenti e le chiamate API:</span><span class="sxs-lookup"><span data-stu-id="c1d22-107">To create a new Blazor hosted app in Visual Studio that uses [IdentityServer](https://identityserver.io/) to authenticate users and API calls:</span></span>

1. <span data-ttu-id="c1d22-108">Usare Visual Studio per creare una nuova \*\* Blazor app webassembly\*\* .</span><span class="sxs-lookup"><span data-stu-id="c1d22-108">Use Visual Studio to create a new **Blazor WebAssembly** app.</span></span> <span data-ttu-id="c1d22-109">Per altre informazioni, vedere <xref:blazor/get-started>.</span><span class="sxs-lookup"><span data-stu-id="c1d22-109">For more information, see <xref:blazor/get-started>.</span></span>
1. <span data-ttu-id="c1d22-110">Nella finestra di dialogo **Crea Blazor una nuova app** Selezionare **modifica** nella sezione **autenticazione** .</span><span class="sxs-lookup"><span data-stu-id="c1d22-110">In the **Create a new Blazor app** dialog, select **Change** in the **Authentication** section.</span></span>
1. <span data-ttu-id="c1d22-111">Selezionare **account utente singoli** e quindi **OK**.</span><span class="sxs-lookup"><span data-stu-id="c1d22-111">Select **Individual User Accounts** followed by **OK**.</span></span>
1. <span data-ttu-id="c1d22-112">Nella sezione **Avanzate** Selezionare la casella di controllo **ASP.NET Core Hosted** .</span><span class="sxs-lookup"><span data-stu-id="c1d22-112">Select the **ASP.NET Core hosted** checkbox in the **Advanced** section.</span></span>
1. <span data-ttu-id="c1d22-113">Selezionare il pulsante **Crea**.</span><span class="sxs-lookup"><span data-stu-id="c1d22-113">Select the **Create** button.</span></span>

<span data-ttu-id="c1d22-114">Per creare l'app in una shell dei comandi, eseguire il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="c1d22-114">To create the app in a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorwasm -au Individual -ho
```

<span data-ttu-id="c1d22-115">Per specificare il percorso di output, che crea una cartella di progetto, se non esiste, includere l'opzione di output nel comando con un percorso (ad `-o BlazorSample`esempio,).</span><span class="sxs-lookup"><span data-stu-id="c1d22-115">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="c1d22-116">Il nome della cartella diventa anche parte del nome del progetto.</span><span class="sxs-lookup"><span data-stu-id="c1d22-116">The folder name also becomes part of the project's name.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="c1d22-117">Configurazione dell'app Server</span><span class="sxs-lookup"><span data-stu-id="c1d22-117">Server app configuration</span></span>

<span data-ttu-id="c1d22-118">Le sezioni seguenti descrivono le aggiunte al progetto quando è incluso il supporto per l'autenticazione.</span><span class="sxs-lookup"><span data-stu-id="c1d22-118">The following sections describe additions to the project when authentication support is included.</span></span>

### <a name="startup-class"></a><span data-ttu-id="c1d22-119">Classe di avvio</span><span class="sxs-lookup"><span data-stu-id="c1d22-119">Startup class</span></span>

<span data-ttu-id="c1d22-120">La `Startup` classe presenta le aggiunte seguenti:</span><span class="sxs-lookup"><span data-stu-id="c1d22-120">The `Startup` class has the following additions:</span></span>

* <span data-ttu-id="c1d22-121">In `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="c1d22-121">In `Startup.ConfigureServices`:</span></span>

  * <span data-ttu-id="c1d22-122">Identità:</span><span class="sxs-lookup"><span data-stu-id="c1d22-122">Identity:</span></span>

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>()
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * <span data-ttu-id="c1d22-123">IdentityServer con un metodo <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> Helper aggiuntivo che configura alcune convenzioni ASP.NET Core predefinite in IdentityServer:</span><span class="sxs-lookup"><span data-stu-id="c1d22-123">IdentityServer with an additional <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method that sets up some default ASP.NET Core conventions on top of IdentityServer:</span></span>

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * <span data-ttu-id="c1d22-124">Autenticazione con un metodo <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> Helper aggiuntivo che configura l'app per convalidare i token JWT prodotti da IdentityServer:</span><span class="sxs-lookup"><span data-stu-id="c1d22-124">Authentication with an additional <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper method that configures the app to validate JWT tokens produced by IdentityServer:</span></span>

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* <span data-ttu-id="c1d22-125">In `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="c1d22-125">In `Startup.Configure`:</span></span>

  * <span data-ttu-id="c1d22-126">Il middleware di autenticazione responsabile della convalida delle credenziali della richiesta e dell'impostazione dell'utente nel contesto della richiesta:</span><span class="sxs-lookup"><span data-stu-id="c1d22-126">The authentication middleware that is responsible for validating the request credentials and setting the user on the request context:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

  * <span data-ttu-id="c1d22-127">Il middleware IdentityServer che espone gli endpoint Open ID Connect (OIDC):</span><span class="sxs-lookup"><span data-stu-id="c1d22-127">The IdentityServer middleware that exposes the Open ID Connect (OIDC) endpoints:</span></span>

    ```csharp
    app.UseIdentityServer();
    ```

### <a name="addapiauthorization"></a><span data-ttu-id="c1d22-128">AddApiAuthorization</span><span class="sxs-lookup"><span data-stu-id="c1d22-128">AddApiAuthorization</span></span>

<span data-ttu-id="c1d22-129">Il <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> metodo helper Configura [IdentityServer](https://identityserver.io/) per scenari ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c1d22-129">The <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method configures [IdentityServer](https://identityserver.io/) for ASP.NET Core scenarios.</span></span> <span data-ttu-id="c1d22-130">IdentityServer è un Framework potente ed estendibile per la gestione dei problemi di sicurezza delle app.</span><span class="sxs-lookup"><span data-stu-id="c1d22-130">IdentityServer is a powerful and extensible framework for handling app security concerns.</span></span> <span data-ttu-id="c1d22-131">IdentityServer espone una complessità superflua per gli scenari più comuni.</span><span class="sxs-lookup"><span data-stu-id="c1d22-131">IdentityServer exposes unnecessary complexity for the most common scenarios.</span></span> <span data-ttu-id="c1d22-132">Di conseguenza, viene fornito un set di convenzioni e opzioni di configurazione che consideriamo un valido punto di partenza.</span><span class="sxs-lookup"><span data-stu-id="c1d22-132">Consequently, a set of conventions and configuration options is provided that we consider a good starting point.</span></span> <span data-ttu-id="c1d22-133">Una volta modificate le esigenze di autenticazione, è ancora disponibile la potenza completa di IdentityServer per personalizzare l'autenticazione per soddisfare i requisiti di un'app.</span><span class="sxs-lookup"><span data-stu-id="c1d22-133">Once your authentication needs change, the full power of IdentityServer is still available to customize authentication to suit an app's requirements.</span></span>

### <a name="addidentityserverjwt"></a><span data-ttu-id="c1d22-134">AddIdentityServerJwt</span><span class="sxs-lookup"><span data-stu-id="c1d22-134">AddIdentityServerJwt</span></span>

<span data-ttu-id="c1d22-135">Il <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> metodo helper configura uno schema di criteri per l'app come gestore di autenticazione predefinito.</span><span class="sxs-lookup"><span data-stu-id="c1d22-135">The <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper method configures a policy scheme for the app as the default authentication handler.</span></span> <span data-ttu-id="c1d22-136">Il criterio è configurato in modo da consentire all'identità di gestire tutte le richieste indirizzate a qualsiasi sottopercorso nello `/Identity`spazio URL identità.</span><span class="sxs-lookup"><span data-stu-id="c1d22-136">The policy is configured to allow Identity to handle all requests routed to any subpath in the Identity URL space `/Identity`.</span></span> <span data-ttu-id="c1d22-137"><xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> Gestisce tutte le altre richieste.</span><span class="sxs-lookup"><span data-stu-id="c1d22-137">The <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> handles all other requests.</span></span> <span data-ttu-id="c1d22-138">Inoltre, questo metodo:</span><span class="sxs-lookup"><span data-stu-id="c1d22-138">Additionally, this method:</span></span>

* <span data-ttu-id="c1d22-139">Registra una `{APPLICATION NAME}API` risorsa API con IdentityServer con un ambito predefinito `{APPLICATION NAME}API`.</span><span class="sxs-lookup"><span data-stu-id="c1d22-139">Registers an `{APPLICATION NAME}API` API resource with IdentityServer with a default scope of `{APPLICATION NAME}API`.</span></span>
* <span data-ttu-id="c1d22-140">Configura il middleware del token di porta JWT per convalidare i token emessi da IdentityServer per l'app.</span><span class="sxs-lookup"><span data-stu-id="c1d22-140">Configures the JWT Bearer Token Middleware to validate tokens issued by IdentityServer for the app.</span></span>

### <a name="weatherforecastcontroller"></a><span data-ttu-id="c1d22-141">WeatherForecastController</span><span class="sxs-lookup"><span data-stu-id="c1d22-141">WeatherForecastController</span></span>

<span data-ttu-id="c1d22-142">In `WeatherForecastController` (*Controllers/WeatherForecastController. cs*) l' [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attributo viene applicato alla classe.</span><span class="sxs-lookup"><span data-stu-id="c1d22-142">In the `WeatherForecastController` (*Controllers/WeatherForecastController.cs*), the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute is applied to the class.</span></span> <span data-ttu-id="c1d22-143">L'attributo indica che l'utente deve essere autorizzato in base ai criteri predefiniti per accedere alla risorsa.</span><span class="sxs-lookup"><span data-stu-id="c1d22-143">The attribute indicates that the user must be authorized based on the default policy to access the resource.</span></span> <span data-ttu-id="c1d22-144">I criteri di autorizzazione predefiniti sono configurati per l'utilizzo dello schema di autenticazione predefinito, <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> configurato in base allo schema dei criteri indicato in precedenza.</span><span class="sxs-lookup"><span data-stu-id="c1d22-144">The default authorization policy is configured to use the default authentication scheme, which is set up by <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> to the policy scheme that was mentioned earlier.</span></span> <span data-ttu-id="c1d22-145">Il metodo helper viene configurato <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> come gestore predefinito per le richieste all'app.</span><span class="sxs-lookup"><span data-stu-id="c1d22-145">The helper method configures <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> as the default handler for requests to the app.</span></span>

### <a name="applicationdbcontext"></a><span data-ttu-id="c1d22-146">ApplicationDbContext</span><span class="sxs-lookup"><span data-stu-id="c1d22-146">ApplicationDbContext</span></span>

<span data-ttu-id="c1d22-147">In `ApplicationDbContext` (*Data/ApplicationDbContext. cs*) lo stesso <xref:Microsoft.EntityFrameworkCore.DbContext> viene usato nell'identità con l'eccezione che estende <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> per includere lo schema per IdentityServer.</span><span class="sxs-lookup"><span data-stu-id="c1d22-147">In the `ApplicationDbContext` (*Data/ApplicationDbContext.cs*), the same <xref:Microsoft.EntityFrameworkCore.DbContext> is used in Identity with the exception that it extends <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> to include the schema for IdentityServer.</span></span> <span data-ttu-id="c1d22-148">L'oggetto <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> è derivato da <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>.</span><span class="sxs-lookup"><span data-stu-id="c1d22-148"><xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> is derived from <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>.</span></span>

<span data-ttu-id="c1d22-149">Per ottenere il controllo completo dello schema del database, ereditare da una delle classi <xref:Microsoft.EntityFrameworkCore.DbContext> di identità disponibili e configurare il contesto per includere lo schema di `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` identità chiamando `OnModelCreating` nel metodo.</span><span class="sxs-lookup"><span data-stu-id="c1d22-149">To gain full control of the database schema, inherit from one of the available Identity <xref:Microsoft.EntityFrameworkCore.DbContext> classes and configure the context to include the Identity schema by calling `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` in the `OnModelCreating` method.</span></span>

### <a name="oidcconfigurationcontroller"></a><span data-ttu-id="c1d22-150">OidcConfigurationController</span><span class="sxs-lookup"><span data-stu-id="c1d22-150">OidcConfigurationController</span></span>

<span data-ttu-id="c1d22-151">In `OidcConfigurationController` (*Controllers/OidcConfigurationController. cs*), viene eseguito il provisioning dell'endpoint client per gestire i parametri di OIDC.</span><span class="sxs-lookup"><span data-stu-id="c1d22-151">In the `OidcConfigurationController` (*Controllers/OidcConfigurationController.cs*), the client endpoint is provisioned to serve OIDC parameters.</span></span>

### <a name="app-settings-files"></a><span data-ttu-id="c1d22-152">File di impostazioni dell'app</span><span class="sxs-lookup"><span data-stu-id="c1d22-152">App settings files</span></span>

<span data-ttu-id="c1d22-153">Nel file di impostazioni dell'app (*appSettings. JSON*) nella radice del progetto, `IdentityServer` la sezione descrive l'elenco dei client configurati.</span><span class="sxs-lookup"><span data-stu-id="c1d22-153">In the app settings file (*appsettings.json*) at the project root, the `IdentityServer` section describes the list of configured clients.</span></span> <span data-ttu-id="c1d22-154">Nell'esempio seguente è presente un singolo client.</span><span class="sxs-lookup"><span data-stu-id="c1d22-154">In the following example, there's a single client.</span></span> <span data-ttu-id="c1d22-155">Il nome del client corrisponde al nome dell'app e viene mappato per convenzione al `ClientId` parametro OAuth.</span><span class="sxs-lookup"><span data-stu-id="c1d22-155">The client name corresponds to the app name and is mapped by convention to the OAuth `ClientId` parameter.</span></span> <span data-ttu-id="c1d22-156">Il profilo indica il tipo di app da configurare.</span><span class="sxs-lookup"><span data-stu-id="c1d22-156">The profile indicates the app type being configured.</span></span> <span data-ttu-id="c1d22-157">Il profilo viene utilizzato internamente per guidare le convenzioni che semplificano il processo di configurazione per il server.</span><span class="sxs-lookup"><span data-stu-id="c1d22-157">The profile is used internally to drive conventions that simplify the configuration process for the server.</span></span> <!-- There are several profiles available, as explained in the [Application profiles](#application-profiles) section. -->

```json
"IdentityServer": {
  "Clients": {
    "BlazorApplicationWithAuthentication.Client": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

<span data-ttu-id="c1d22-158">Nel file di impostazioni dell'app dell'ambiente di sviluppo (*appSettings. Development. JSON*) nella radice del progetto, la `IdentityServer` sezione descrive la chiave usata per firmare i token.</span><span class="sxs-lookup"><span data-stu-id="c1d22-158">In the Development environment app settings file (*appsettings.Development.json*) at the project root, the `IdentityServer` section describes the key used to sign tokens.</span></span> <!-- When deploying to production, a key needs to be provisioned and deployed alongside the app, as explained in the [Deploy to production](#deploy-to-production) section. -->

```json
"IdentityServer": {
  "Key": {
    "Type": "Development"
  }
}
```

## <a name="client-app-configuration"></a><span data-ttu-id="c1d22-159">Configurazione dell'app client</span><span class="sxs-lookup"><span data-stu-id="c1d22-159">Client app configuration</span></span>

### <a name="authentication-package"></a><span data-ttu-id="c1d22-160">Pacchetto di autenticazione</span><span class="sxs-lookup"><span data-stu-id="c1d22-160">Authentication package</span></span>

<span data-ttu-id="c1d22-161">Quando viene creata un'app per l'`Individual` `Microsoft.AspNetCore.Components.WebAssembly.Authentication` uso di singoli account utente (), l'app riceve automaticamente un riferimento al pacchetto nel file di progetto dell'app.</span><span class="sxs-lookup"><span data-stu-id="c1d22-161">When an app is created to use Individual User Accounts (`Individual`), the app automatically receives a package reference for the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package in the app's project file.</span></span> <span data-ttu-id="c1d22-162">Il pacchetto fornisce un set di primitive che consentono all'app di autenticare gli utenti e ottenere i token per chiamare le API protette.</span><span class="sxs-lookup"><span data-stu-id="c1d22-162">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="c1d22-163">Se si aggiunge l'autenticazione a un'app, aggiungere manualmente il pacchetto al file di progetto dell'app:</span><span class="sxs-lookup"><span data-stu-id="c1d22-163">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference 
    Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
    Version="{VERSION}" />
```

<span data-ttu-id="c1d22-164">Sostituire `{VERSION}` nel riferimento al pacchetto precedente con la versione del `Microsoft.AspNetCore.Blazor.Templates` pacchetto illustrata nell' <xref:blazor/get-started> articolo.</span><span class="sxs-lookup"><span data-stu-id="c1d22-164">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

### <a name="api-authorization-support"></a><span data-ttu-id="c1d22-165">Supporto dell'autorizzazione API</span><span class="sxs-lookup"><span data-stu-id="c1d22-165">API authorization support</span></span>

<span data-ttu-id="c1d22-166">Il supporto per l'autenticazione degli utenti è collegato al contenitore del servizio mediante il metodo di estensione fornito nel `Microsoft.AspNetCore.Components.WebAssembly.Authentication` pacchetto.</span><span class="sxs-lookup"><span data-stu-id="c1d22-166">The support for authenticating users is plugged into the service container by the extension method provided inside the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package.</span></span> <span data-ttu-id="c1d22-167">Questo metodo configura tutti i servizi necessari per l'interazione dell'app con il sistema di autorizzazione esistente.</span><span class="sxs-lookup"><span data-stu-id="c1d22-167">This method sets up all the services needed for the app to interact with the existing authorization system.</span></span>

```csharp
builder.Services.AddApiAuthorization();
```

<span data-ttu-id="c1d22-168">Per impostazione predefinita, carica la configurazione per l'app per convenzione da `_configuration/{client-id}`.</span><span class="sxs-lookup"><span data-stu-id="c1d22-168">By default, it loads the configuration for the app by convention from `_configuration/{client-id}`.</span></span> <span data-ttu-id="c1d22-169">Per convenzione, l'ID client è impostato sul nome dell'assembly dell'app.</span><span class="sxs-lookup"><span data-stu-id="c1d22-169">By convention, the client ID is set to the app's assembly name.</span></span> <span data-ttu-id="c1d22-170">Questo URL può essere modificato in modo che punti a un endpoint separato chiamando l'overload con options.</span><span class="sxs-lookup"><span data-stu-id="c1d22-170">This URL can be changed to point to a separate endpoint by calling the overload with options.</span></span>

### <a name="imports-file"></a><span data-ttu-id="c1d22-171">Importa file</span><span class="sxs-lookup"><span data-stu-id="c1d22-171">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="c1d22-172">Pagina di indice</span><span class="sxs-lookup"><span data-stu-id="c1d22-172">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

### <a name="app-component"></a><span data-ttu-id="c1d22-173">Componente app</span><span class="sxs-lookup"><span data-stu-id="c1d22-173">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="c1d22-174">Componente RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="c1d22-174">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="c1d22-175">Componente LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="c1d22-175">LoginDisplay component</span></span>

<span data-ttu-id="c1d22-176">Il `LoginDisplay` componente (*Shared/LoginDisplay. Razor*) viene sottoposto a `MainLayout` rendering nel componente (*Shared/MainLayout. Razor*) e gestisce i comportamenti seguenti:</span><span class="sxs-lookup"><span data-stu-id="c1d22-176">The `LoginDisplay` component (*Shared/LoginDisplay.razor*) is rendered in the `MainLayout` component (*Shared/MainLayout.razor*) and manages the following behaviors:</span></span>

* <span data-ttu-id="c1d22-177">Per utenti autenticati:</span><span class="sxs-lookup"><span data-stu-id="c1d22-177">For authenticated users:</span></span>
  * <span data-ttu-id="c1d22-178">Visualizza il nome utente corrente.</span><span class="sxs-lookup"><span data-stu-id="c1d22-178">Displays the current user name.</span></span>
  * <span data-ttu-id="c1d22-179">Offre un collegamento alla pagina del profilo utente nell'identità del ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c1d22-179">Offers a link to the user profile page in ASP.NET Core Identity.</span></span>
  * <span data-ttu-id="c1d22-180">Offre un pulsante per disconnettersi dall'app.</span><span class="sxs-lookup"><span data-stu-id="c1d22-180">Offers a button to log out of the app.</span></span>
* <span data-ttu-id="c1d22-181">Per utenti anonimi:</span><span class="sxs-lookup"><span data-stu-id="c1d22-181">For anonymous users:</span></span>
  * <span data-ttu-id="c1d22-182">Offre la possibilità di effettuare la registrazione.</span><span class="sxs-lookup"><span data-stu-id="c1d22-182">Offers the option to register.</span></span>
  * <span data-ttu-id="c1d22-183">Offre la possibilità di effettuare l'accesso.</span><span class="sxs-lookup"><span data-stu-id="c1d22-183">Offers the option to log in.</span></span>

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

### <a name="authentication-component"></a><span data-ttu-id="c1d22-184">Componente di autenticazione</span><span class="sxs-lookup"><span data-stu-id="c1d22-184">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="c1d22-185">Componente FetchData</span><span class="sxs-lookup"><span data-stu-id="c1d22-185">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="c1d22-186">Eseguire l'app</span><span class="sxs-lookup"><span data-stu-id="c1d22-186">Run the app</span></span>

<span data-ttu-id="c1d22-187">Eseguire l'app dal progetto server.</span><span class="sxs-lookup"><span data-stu-id="c1d22-187">Run the app from the Server project.</span></span> <span data-ttu-id="c1d22-188">Quando si usa Visual Studio, selezionare il progetto server in **Esplora soluzioni** e selezionare il pulsante **Esegui** sulla barra degli strumenti o avviare l'app dal menu **debug** .</span><span class="sxs-lookup"><span data-stu-id="c1d22-188">When using Visual Studio, select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="c1d22-189">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="c1d22-189">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
