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
ms.openlocfilehash: 832109530c4aac372fd75aa1a1d2edbe3768f55f
ms.sourcegitcommit: 1d8f1396ccc66a0c3fcb5e5f36ea29b50db6d92a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/01/2020
ms.locfileid: "80501275"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-hosted-app-with-identity-server"></a><span data-ttu-id="d0cfb-103">Proteggere un'app ospitata ASP.NET Core WebAssembly con Identity ServerSecure an ASP.NET Core Blazor WebAssembly hosted app with Identity Server</span><span class="sxs-lookup"><span data-stu-id="d0cfb-103">Secure an ASP.NET Core Blazor WebAssembly hosted app with Identity Server</span></span>

<span data-ttu-id="d0cfb-104">Di [Javier Calvarro Nelson](https://github.com/javiercn) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="d0cfb-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="d0cfb-105">Per creare Blazor una nuova app ospitata in Visual Studio che usa IdentityServer per autenticare utenti e chiamate API:To create a new hosted app in Visual Studio that uses [IdentityServer](https://identityserver.io/) to authenticate users and API calls:</span><span class="sxs-lookup"><span data-stu-id="d0cfb-105">To create a new Blazor hosted app in Visual Studio that uses [IdentityServer](https://identityserver.io/) to authenticate users and API calls:</span></span>

1. <span data-ttu-id="d0cfb-106">Usare Visual Studio per \*\* Blazor \*\* creare una nuova app WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="d0cfb-106">Use Visual Studio to create a new **Blazor WebAssembly** app.</span></span> <span data-ttu-id="d0cfb-107">Per altre informazioni, vedere <xref:blazor/get-started>.</span><span class="sxs-lookup"><span data-stu-id="d0cfb-107">For more information, see <xref:blazor/get-started>.</span></span>
1. <span data-ttu-id="d0cfb-108">Nella finestra di dialogo \*\*Crea una nuova app Blazor \*\* selezionare Modifica nella sezione **Autenticazione.In** the Create a new app dialog, select **Change** in the Authentication section.</span><span class="sxs-lookup"><span data-stu-id="d0cfb-108">In the **Create a new Blazor app** dialog, select **Change** in the **Authentication** section.</span></span>
1. <span data-ttu-id="d0cfb-109">Selezionare **Singoli account utente** seguiti da **OK**.</span><span class="sxs-lookup"><span data-stu-id="d0cfb-109">Select **Individual User Accounts** followed by **OK**.</span></span>
1. <span data-ttu-id="d0cfb-110">Selezionare la casella di **controllo ASP.NET Core ospitato** nella sezione **Avanzate.Select** the Core hosted checkbox in the Advanced section.</span><span class="sxs-lookup"><span data-stu-id="d0cfb-110">Select the **ASP.NET Core hosted** checkbox in the **Advanced** section.</span></span>
1. <span data-ttu-id="d0cfb-111">Selezionare il pulsante **Crea**.</span><span class="sxs-lookup"><span data-stu-id="d0cfb-111">Select the **Create** button.</span></span>

<span data-ttu-id="d0cfb-112">Per creare l'app in una shell dei comandi, eseguire il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="d0cfb-112">To create the app in a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorwasm -au Individual -ho
```

<span data-ttu-id="d0cfb-113">Per specificare il percorso di output, che crea una cartella di progetto se non esiste, `-o BlazorSample`includere l'opzione di output nel comando con un percorso, ad esempio ).</span><span class="sxs-lookup"><span data-stu-id="d0cfb-113">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="d0cfb-114">Il nome della cartella diventa anche parte del nome del progetto.</span><span class="sxs-lookup"><span data-stu-id="d0cfb-114">The folder name also becomes part of the project's name.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="d0cfb-115">Configurazione dell'app server</span><span class="sxs-lookup"><span data-stu-id="d0cfb-115">Server app configuration</span></span>

<span data-ttu-id="d0cfb-116">Nelle sezioni seguenti vengono descritte le aggiunte al progetto quando è incluso il supporto dell'autenticazione.</span><span class="sxs-lookup"><span data-stu-id="d0cfb-116">The following sections describe additions to the project when authentication support is included.</span></span>

### <a name="startup-class"></a><span data-ttu-id="d0cfb-117">Classe di avvio</span><span class="sxs-lookup"><span data-stu-id="d0cfb-117">Startup class</span></span>

<span data-ttu-id="d0cfb-118">La `Startup` classe ha le seguenti aggiunte:</span><span class="sxs-lookup"><span data-stu-id="d0cfb-118">The `Startup` class has the following additions:</span></span>

* <span data-ttu-id="d0cfb-119">In `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="d0cfb-119">In `Startup.ConfigureServices`:</span></span>

  * <span data-ttu-id="d0cfb-120">Identità con l'interfaccia utente predefinita:Identity with the default UI:</span><span class="sxs-lookup"><span data-stu-id="d0cfb-120">Identity with the default UI:</span></span>

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>()
        .AddDefaultUI(UIFramework.Bootstrap4)
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * <span data-ttu-id="d0cfb-121">IdentityServer con <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> un metodo helper aggiuntivo che imposta alcune convenzioni predefinite ASP.NET Core sopra IdentityServer:</span><span class="sxs-lookup"><span data-stu-id="d0cfb-121">IdentityServer with an additional <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method that sets up some default ASP.NET Core conventions on top of IdentityServer:</span></span>

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * <span data-ttu-id="d0cfb-122">Autenticazione con <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> un metodo helper aggiuntivo che configura l'app per convalidare i token JWT prodotti da IdentityServer:Authentication with an additional helper method that configures the app to validate JWT tokens produced by IdentityServer:</span><span class="sxs-lookup"><span data-stu-id="d0cfb-122">Authentication with an additional <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper method that configures the app to validate JWT tokens produced by IdentityServer:</span></span>

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* <span data-ttu-id="d0cfb-123">In `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="d0cfb-123">In `Startup.Configure`:</span></span>

  * <span data-ttu-id="d0cfb-124">Middleware di autenticazione responsabile della convalida delle credenziali della richiesta e dell'impostazione dell'utente nel contesto della richiesta:</span><span class="sxs-lookup"><span data-stu-id="d0cfb-124">The authentication middleware that is responsible for validating the request credentials and setting the user on the request context:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

  * <span data-ttu-id="d0cfb-125">Middleware di IdentityServer che espone gli endpoint Open ID Connect (OIDC):</span><span class="sxs-lookup"><span data-stu-id="d0cfb-125">The IdentityServer middleware that exposes the Open ID Connect (OIDC) endpoints:</span></span>

    ```csharp
    app.UseIdentityServer();
    ```

### <a name="addapiauthorization"></a><span data-ttu-id="d0cfb-126">AddApiAuthorization</span><span class="sxs-lookup"><span data-stu-id="d0cfb-126">AddApiAuthorization</span></span>

<span data-ttu-id="d0cfb-127">Il <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> metodo helper configura [IdentityServer](https://identityserver.io/) per gli scenari di ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d0cfb-127">The <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method configures [IdentityServer](https://identityserver.io/) for ASP.NET Core scenarios.</span></span> <span data-ttu-id="d0cfb-128">IdentityServer è un framework potente ed estensibile per la gestione dei problemi di sicurezza delle app.</span><span class="sxs-lookup"><span data-stu-id="d0cfb-128">IdentityServer is a powerful and extensible framework for handling app security concerns.</span></span> <span data-ttu-id="d0cfb-129">IdentityServer espone la complessità non necessaria per gli scenari più comuni.</span><span class="sxs-lookup"><span data-stu-id="d0cfb-129">IdentityServer exposes unnecessary complexity for the most common scenarios.</span></span> <span data-ttu-id="d0cfb-130">Di conseguenza, viene fornito un insieme di convenzioni e opzioni di configurazione che consideriamo un buon punto di partenza.</span><span class="sxs-lookup"><span data-stu-id="d0cfb-130">Consequently, a set of conventions and configuration options is provided that we consider a good starting point.</span></span> <span data-ttu-id="d0cfb-131">Una volta modificate le esigenze di autenticazione, la potenza completa di IdentityServer è ancora disponibile per personalizzare l'autenticazione in base ai requisiti di un'app.</span><span class="sxs-lookup"><span data-stu-id="d0cfb-131">Once your authentication needs change, the full power of IdentityServer is still available to customize authentication to suit an app's requirements.</span></span>

### <a name="addidentityserverjwt"></a><span data-ttu-id="d0cfb-132">AddIdentityServerJwt</span><span class="sxs-lookup"><span data-stu-id="d0cfb-132">AddIdentityServerJwt</span></span>

<span data-ttu-id="d0cfb-133">Il <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> metodo helper configura uno schema di criteri per l'app come gestore di autenticazione predefinito.</span><span class="sxs-lookup"><span data-stu-id="d0cfb-133">The <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper method configures a policy scheme for the app as the default authentication handler.</span></span> <span data-ttu-id="d0cfb-134">Il criterio è configurato per consentire a Identity di gestire tutte `/Identity`le richieste indirizzate a qualsiasi sottopercorso nello spazio URL identità.</span><span class="sxs-lookup"><span data-stu-id="d0cfb-134">The policy is configured to allow Identity to handle all requests routed to any subpath in the Identity URL space `/Identity`.</span></span> <span data-ttu-id="d0cfb-135">L'oggetto <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> gestisce tutte le altre richieste.</span><span class="sxs-lookup"><span data-stu-id="d0cfb-135">The <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> handles all other requests.</span></span> <span data-ttu-id="d0cfb-136">Inoltre, questo metodo:</span><span class="sxs-lookup"><span data-stu-id="d0cfb-136">Additionally, this method:</span></span>

* <span data-ttu-id="d0cfb-137">Registra una `{APPLICATION NAME}API` risorsa API con IdentityServer `{APPLICATION NAME}API`con un ambito predefinito di .</span><span class="sxs-lookup"><span data-stu-id="d0cfb-137">Registers an `{APPLICATION NAME}API` API resource with IdentityServer with a default scope of `{APPLICATION NAME}API`.</span></span>
* <span data-ttu-id="d0cfb-138">Configura il middleware del token di bearer JWT per convalidare i token emessi da IdentityServer per l'app.</span><span class="sxs-lookup"><span data-stu-id="d0cfb-138">Configures the JWT Bearer Token Middleware to validate tokens issued by IdentityServer for the app.</span></span>

### <a name="weatherforecastcontroller"></a><span data-ttu-id="d0cfb-139">WeatherForecastController</span><span class="sxs-lookup"><span data-stu-id="d0cfb-139">WeatherForecastController</span></span>

<span data-ttu-id="d0cfb-140">In `WeatherForecastController` (*Controllers/WeatherForecastController.cs* [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) ), l'attributo viene applicato alla classe .</span><span class="sxs-lookup"><span data-stu-id="d0cfb-140">In the `WeatherForecastController` (*Controllers/WeatherForecastController.cs*), the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute is applied to the class.</span></span> <span data-ttu-id="d0cfb-141">L'attributo indica che l'utente deve essere autorizzato in base ai criteri predefiniti per accedere alla risorsa.</span><span class="sxs-lookup"><span data-stu-id="d0cfb-141">The attribute indicates that the user must be authorized based on the default policy to access the resource.</span></span> <span data-ttu-id="d0cfb-142">Il criterio di autorizzazione predefinito è configurato per utilizzare <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> lo schema di autenticazione predefinito, che viene impostato dallo schema di criteri menzionato in precedenza.</span><span class="sxs-lookup"><span data-stu-id="d0cfb-142">The default authorization policy is configured to use the default authentication scheme, which is set up by <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> to the policy scheme that was mentioned earlier.</span></span> <span data-ttu-id="d0cfb-143">Il metodo helper <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> viene configurato come gestore predefinito per le richieste all'app.</span><span class="sxs-lookup"><span data-stu-id="d0cfb-143">The helper method configures <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> as the default handler for requests to the app.</span></span>

### <a name="applicationdbcontext"></a><span data-ttu-id="d0cfb-144">ApplicationDbContext</span><span class="sxs-lookup"><span data-stu-id="d0cfb-144">ApplicationDbContext</span></span>

<span data-ttu-id="d0cfb-145">In `ApplicationDbContext` (*Data/ApplicationDbContext.cs*), <xref:Microsoft.EntityFrameworkCore.DbContext> lo stesso viene utilizzato in <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> Identity con l'eccezione che estende per includere lo schema per IdentityServer.</span><span class="sxs-lookup"><span data-stu-id="d0cfb-145">In the `ApplicationDbContext` (*Data/ApplicationDbContext.cs*), the same <xref:Microsoft.EntityFrameworkCore.DbContext> is used in Identity with the exception that it extends <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> to include the schema for IdentityServer.</span></span> <span data-ttu-id="d0cfb-146">L'oggetto <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> è derivato da <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>.</span><span class="sxs-lookup"><span data-stu-id="d0cfb-146"><xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> is derived from <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>.</span></span>

<span data-ttu-id="d0cfb-147">Per ottenere il controllo completo dello schema del <xref:Microsoft.EntityFrameworkCore.DbContext> database, ereditare da una delle `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` classi `OnModelCreating` Identity disponibili e configurare il contesto in modo da includere lo schema Identity chiamando nel metodo .</span><span class="sxs-lookup"><span data-stu-id="d0cfb-147">To gain full control of the database schema, inherit from one of the available Identity <xref:Microsoft.EntityFrameworkCore.DbContext> classes and configure the context to include the Identity schema by calling `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` in the `OnModelCreating` method.</span></span>

### <a name="oidcconfigurationcontroller"></a><span data-ttu-id="d0cfb-148">OidcConfigurationController</span><span class="sxs-lookup"><span data-stu-id="d0cfb-148">OidcConfigurationController</span></span>

<span data-ttu-id="d0cfb-149">In `OidcConfigurationController` (*Controllers/OidcConfigurationController.cs*) viene eseguito il provisioning dell'endpoint client per la gestione dei parametri OIDC.</span><span class="sxs-lookup"><span data-stu-id="d0cfb-149">In the `OidcConfigurationController` (*Controllers/OidcConfigurationController.cs*), the client endpoint is provisioned to serve OIDC parameters.</span></span>

### <a name="app-settings-files"></a><span data-ttu-id="d0cfb-150">File di impostazioni dell'app</span><span class="sxs-lookup"><span data-stu-id="d0cfb-150">App settings files</span></span>

<span data-ttu-id="d0cfb-151">Nel file di impostazioni dell'app (*appsettings.json*) nella radice del progetto, la `IdentityServer` sezione descrive l'elenco dei client configurati.</span><span class="sxs-lookup"><span data-stu-id="d0cfb-151">In the app settings file (*appsettings.json*) at the project root, the `IdentityServer` section describes the list of configured clients.</span></span> <span data-ttu-id="d0cfb-152">Nell'esempio seguente è presente un singolo client.</span><span class="sxs-lookup"><span data-stu-id="d0cfb-152">In the following example, there's a single client.</span></span> <span data-ttu-id="d0cfb-153">Il nome del client corrisponde al nome dell'app `ClientId` ed è mappato per convenzione al parametro OAuth.</span><span class="sxs-lookup"><span data-stu-id="d0cfb-153">The client name corresponds to the app name and is mapped by convention to the OAuth `ClientId` parameter.</span></span> <span data-ttu-id="d0cfb-154">Il profilo indica il tipo di app da configurare.</span><span class="sxs-lookup"><span data-stu-id="d0cfb-154">The profile indicates the app type being configured.</span></span> <span data-ttu-id="d0cfb-155">Il profilo viene utilizzato internamente per guidare le convenzioni che semplificano il processo di configurazione per il server.</span><span class="sxs-lookup"><span data-stu-id="d0cfb-155">The profile is used internally to drive conventions that simplify the configuration process for the server.</span></span> <!-- There are several profiles available, as explained in the [Application profiles](#application-profiles) section. -->

```json
"IdentityServer": {
  "Clients": {
    "BlazorApplicationWithAuthentication.Client": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

<span data-ttu-id="d0cfb-156">Nel file di impostazioni dell'app Ambiente di sviluppo *(impostazioni app. Development.json*) nella radice `IdentityServer` del progetto, la sezione descrive la chiave utilizzata per firmare i token.</span><span class="sxs-lookup"><span data-stu-id="d0cfb-156">In the Development environment app settings file (*appsettings.Development.json*) at the project root, the `IdentityServer` section describes the key used to sign tokens.</span></span> <!-- When deploying to production, a key needs to be provisioned and deployed alongside the app, as explained in the [Deploy to production](#deploy-to-production) section. -->

```json
"IdentityServer": {
  "Key": {
    "Type": "Development"
  }
}
```

## <a name="client-app-configuration"></a><span data-ttu-id="d0cfb-157">Configurazione dell'app client</span><span class="sxs-lookup"><span data-stu-id="d0cfb-157">Client app configuration</span></span>

### <a name="authentication-package"></a><span data-ttu-id="d0cfb-158">Pacchetto di autenticazione</span><span class="sxs-lookup"><span data-stu-id="d0cfb-158">Authentication package</span></span>

<span data-ttu-id="d0cfb-159">Quando viene creata un'app per`Individual`usare singoli account utente ( `Microsoft.AspNetCore.Components.WebAssembly.Authentication` ), l'app riceve automaticamente un riferimento al pacchetto per il pacchetto nel file di progetto dell'app.</span><span class="sxs-lookup"><span data-stu-id="d0cfb-159">When an app is created to use Individual User Accounts (`Individual`), the app automatically receives a package reference for the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package in the app's project file.</span></span> <span data-ttu-id="d0cfb-160">Il pacchetto fornisce un set di primitive che consentono all'app di autenticare gli utenti e ottenere token per chiamare API protette.</span><span class="sxs-lookup"><span data-stu-id="d0cfb-160">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="d0cfb-161">Se si aggiunge l'autenticazione a un'app, aggiungi manualmente il pacchetto al file di progetto dell'app:</span><span class="sxs-lookup"><span data-stu-id="d0cfb-161">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference 
    Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
    Version="{VERSION}" />
```

<span data-ttu-id="d0cfb-162">Sostituire `{VERSION}` nel riferimento al pacchetto precedente `Microsoft.AspNetCore.Blazor.Templates` con la <xref:blazor/get-started> versione del pacchetto illustrata nell'articolo.</span><span class="sxs-lookup"><span data-stu-id="d0cfb-162">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

### <a name="api-authorization-support"></a><span data-ttu-id="d0cfb-163">Supporto per l'autorizzazione API</span><span class="sxs-lookup"><span data-stu-id="d0cfb-163">API authorization support</span></span>

<span data-ttu-id="d0cfb-164">Il supporto per l'autenticazione degli utenti viene inserito nel `Microsoft.AspNetCore.Components.WebAssembly.Authentication` contenitore dei servizi dal metodo di estensione fornito all'interno del pacchetto.</span><span class="sxs-lookup"><span data-stu-id="d0cfb-164">The support for authenticating users is plugged into the service container by the extension method provided inside the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package.</span></span> <span data-ttu-id="d0cfb-165">Questo metodo imposta tutti i servizi necessari per l'interazione dell'app con il sistema di autorizzazione esistente.</span><span class="sxs-lookup"><span data-stu-id="d0cfb-165">This method sets up all the services needed for the app to interact with the existing authorization system.</span></span>

```csharp
builder.Services.AddApiAuthorization();
```

<span data-ttu-id="d0cfb-166">Per impostazione predefinita, carica la configurazione `_configuration/{client-id}`per l'app per convenzione da .</span><span class="sxs-lookup"><span data-stu-id="d0cfb-166">By default, it loads the configuration for the app by convention from `_configuration/{client-id}`.</span></span> <span data-ttu-id="d0cfb-167">Per convenzione, l'ID client è impostato sul nome dell'assembly dell'app.</span><span class="sxs-lookup"><span data-stu-id="d0cfb-167">By convention, the client ID is set to the app's assembly name.</span></span> <span data-ttu-id="d0cfb-168">Questo URL può essere modificato in modo da puntare a un endpoint separato chiamando l'overload con le opzioni.</span><span class="sxs-lookup"><span data-stu-id="d0cfb-168">This URL can be changed to point to a separate endpoint by calling the overload with options.</span></span>

### <a name="imports-file"></a><span data-ttu-id="d0cfb-169">File di importazione</span><span class="sxs-lookup"><span data-stu-id="d0cfb-169">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="d0cfb-170">Pagina di indice</span><span class="sxs-lookup"><span data-stu-id="d0cfb-170">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

### <a name="app-component"></a><span data-ttu-id="d0cfb-171">Componente dell'app</span><span class="sxs-lookup"><span data-stu-id="d0cfb-171">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="d0cfb-172">Componente RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="d0cfb-172">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="d0cfb-173">Componente LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="d0cfb-173">LoginDisplay component</span></span>

<span data-ttu-id="d0cfb-174">Il `LoginDisplay` rendering del `MainLayout` componente (*Shared/LoginDisplay.razor*) viene eseguito nel componente (*Shared/MainLayout.razor*) e gestisce i comportamenti seguenti:</span><span class="sxs-lookup"><span data-stu-id="d0cfb-174">The `LoginDisplay` component (*Shared/LoginDisplay.razor*) is rendered in the `MainLayout` component (*Shared/MainLayout.razor*) and manages the following behaviors:</span></span>

* <span data-ttu-id="d0cfb-175">Per gli utenti autenticati:</span><span class="sxs-lookup"><span data-stu-id="d0cfb-175">For authenticated users:</span></span>
  * <span data-ttu-id="d0cfb-176">Visualizza il nome utente corrente.</span><span class="sxs-lookup"><span data-stu-id="d0cfb-176">Displays the current user name.</span></span>
  * <span data-ttu-id="d0cfb-177">Offre un collegamento alla pagina del profilo utente in ASP.NET Core Identity.</span><span class="sxs-lookup"><span data-stu-id="d0cfb-177">Offers a link to the user profile page in ASP.NET Core Identity.</span></span>
  * <span data-ttu-id="d0cfb-178">Offre un pulsante per uscire dall'app.</span><span class="sxs-lookup"><span data-stu-id="d0cfb-178">Offers a button to log out of the app.</span></span>
* <span data-ttu-id="d0cfb-179">Per gli utenti anonimi:</span><span class="sxs-lookup"><span data-stu-id="d0cfb-179">For anonymous users:</span></span>
  * <span data-ttu-id="d0cfb-180">Offre la possibilità di registrarsi.</span><span class="sxs-lookup"><span data-stu-id="d0cfb-180">Offers the option to register.</span></span>
  * <span data-ttu-id="d0cfb-181">Offre la possibilità di effettuare il login.</span><span class="sxs-lookup"><span data-stu-id="d0cfb-181">Offers the option to log in.</span></span>

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

### <a name="authentication-component"></a><span data-ttu-id="d0cfb-182">Componente di autenticazione</span><span class="sxs-lookup"><span data-stu-id="d0cfb-182">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="d0cfb-183">Componente FetchData</span><span class="sxs-lookup"><span data-stu-id="d0cfb-183">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="d0cfb-184">Eseguire l'app</span><span class="sxs-lookup"><span data-stu-id="d0cfb-184">Run the app</span></span>

<span data-ttu-id="d0cfb-185">Eseguire l'app dal progetto Server.</span><span class="sxs-lookup"><span data-stu-id="d0cfb-185">Run the app from the Server project.</span></span> <span data-ttu-id="d0cfb-186">Quando si usa Visual Studio, selezionare il progetto Server in **Esplora soluzioni** e selezionare il pulsante **Esegui** nella barra degli strumenti o avviare l'app dal menu **Debug.**</span><span class="sxs-lookup"><span data-stu-id="d0cfb-186">When using Visual Studio, select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="d0cfb-187">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="d0cfb-187">Additional resources</span></span>

* [<span data-ttu-id="d0cfb-188">Richiedere token di accesso aggiuntiviRequest additional access tokens</span><span class="sxs-lookup"><span data-stu-id="d0cfb-188">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
