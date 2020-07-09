---
title: Proteggere un' Blazor WebAssembly app ospitata ASP.NET Core Identity con server
author: guardrex
description: Per creare una nuova Blazor app ospitata con autenticazione da Visual Studio che usa un back-end del [ Identity server](https://identityserver.io/)
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
uid: blazor/security/webassembly/hosted-with-identity-server
ms.openlocfilehash: 001fa0885c4ef4f365d9849278d3aa36e7657c54
ms.sourcegitcommit: f7873c02c1505c99106cbc708f37e18fc0a496d1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86147733"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-hosted-app-with-identity-server"></a><span data-ttu-id="44075-103">Proteggere un' Blazor WebAssembly app ospitata ASP.NET Core Identity con server</span><span class="sxs-lookup"><span data-stu-id="44075-103">Secure an ASP.NET Core Blazor WebAssembly hosted app with Identity Server</span></span>

<span data-ttu-id="44075-104">Di [Javier Calvarro Nelson](https://github.com/javiercn) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="44075-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="44075-105">Questo articolo illustra come creare una nuova Blazor app ospitata che usa il [ Identity server](https://identityserver.io/) per autenticare gli utenti e le chiamate API.</span><span class="sxs-lookup"><span data-stu-id="44075-105">This article explains how to create a new Blazor hosted app that uses [IdentityServer](https://identityserver.io/) to authenticate users and API calls.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="44075-106">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="44075-106">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="44075-107">Per creare un nuovo Blazor WebAssembly progetto con un meccanismo di autenticazione:</span><span class="sxs-lookup"><span data-stu-id="44075-107">To create a new Blazor WebAssembly project with an authentication mechanism:</span></span>

1. <span data-ttu-id="44075-108">Dopo aver scelto il modello \*\* Blazor WebAssembly app\*\* nella finestra di dialogo **Crea un nuovo ASP.NET Core applicazione Web** , selezionare **Cambia** in **autenticazione**.</span><span class="sxs-lookup"><span data-stu-id="44075-108">After choosing the **Blazor WebAssembly App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication**.</span></span>

1. <span data-ttu-id="44075-109">Selezionare **singoli account utente** con l'opzione **Archivia account utente in-app** per archiviare gli utenti all'interno dell'app usando il sistema di ASP.NET Core [Identity](xref:security/authentication/identity) .</span><span class="sxs-lookup"><span data-stu-id="44075-109">Select **Individual User Accounts** with the **Store user accounts in-app** option to store users within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system.</span></span>

1. <span data-ttu-id="44075-110">Nella sezione **Avanzate** Selezionare la casella di controllo **ASP.NET Core Hosted** .</span><span class="sxs-lookup"><span data-stu-id="44075-110">Select the **ASP.NET Core hosted** check box in the **Advanced** section.</span></span>

# <a name="visual-studio-code--net-core-cli"></a>[<span data-ttu-id="44075-111">Visual Studio Code/Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="44075-111">Visual Studio Code / .NET Core CLI</span></span>](#tab/visual-studio-code+netcore-cli)

<span data-ttu-id="44075-112">Per creare un nuovo Blazor WebAssembly progetto con un meccanismo di autenticazione in una cartella vuota, specificare il `Individual` meccanismo di autenticazione con l' `-au|--auth` opzione per archiviare gli utenti all'interno dell'app usando il sistema di ASP.NET Core [Identity](xref:security/authentication/identity) :</span><span class="sxs-lookup"><span data-stu-id="44075-112">To create a new Blazor WebAssembly project with an authentication mechanism in an empty folder, specify the `Individual` authentication mechanism with the `-au|--auth` option to store users within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system:</span></span>

```dotnetcli
dotnet new blazorwasm -au Individual -ho -o {APP NAME}
```

| <span data-ttu-id="44075-113">Segnaposto</span><span class="sxs-lookup"><span data-stu-id="44075-113">Placeholder</span></span>  | <span data-ttu-id="44075-114">Esempio</span><span class="sxs-lookup"><span data-stu-id="44075-114">Example</span></span>        |
| ------------ | -------------- |
| `{APP NAME}` | `BlazorSample` |

<span data-ttu-id="44075-115">Il percorso di output specificato con l' `-o|--output` opzione Crea una cartella di progetto se non esiste e diventa parte del nome dell'app.</span><span class="sxs-lookup"><span data-stu-id="44075-115">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

<span data-ttu-id="44075-116">Per ulteriori informazioni, vedere il [`dotnet new`](/dotnet/core/tools/dotnet-new) comando nella Guida di .NET Core.</span><span class="sxs-lookup"><span data-stu-id="44075-116">For more information, see the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="44075-117">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="44075-117">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="44075-118">Per creare un nuovo Blazor WebAssembly progetto con un meccanismo di autenticazione:</span><span class="sxs-lookup"><span data-stu-id="44075-118">To create a new Blazor WebAssembly project with an authentication mechanism:</span></span>

1. <span data-ttu-id="44075-119">Nel passaggio **configurare la nuova Blazor WebAssembly app** selezionare **autenticazione singola (in-app) nell'elenco a** discesa **autenticazione** .</span><span class="sxs-lookup"><span data-stu-id="44075-119">On the **Configure your new Blazor WebAssembly App** step, select **Individual Authentication (in-app)** from the **Authentication** drop down.</span></span>

1. <span data-ttu-id="44075-120">L'app viene creata per i singoli utenti archiviati nell'app con ASP.NET Core [Identity](xref:security/authentication/identity) .</span><span class="sxs-lookup"><span data-stu-id="44075-120">The app is created for individual users stored in the app with ASP.NET Core [Identity](xref:security/authentication/identity).</span></span>

1. <span data-ttu-id="44075-121">Selezionare la casella di controllo **ASP.NET Core Hosted** .</span><span class="sxs-lookup"><span data-stu-id="44075-121">Select the **ASP.NET Core hosted** check box.</span></span>

---

## <a name="server-app-configuration"></a><span data-ttu-id="44075-122">Configurazione dell'app Server</span><span class="sxs-lookup"><span data-stu-id="44075-122">Server app configuration</span></span>

<span data-ttu-id="44075-123">Le sezioni seguenti descrivono le aggiunte al progetto quando è incluso il supporto per l'autenticazione.</span><span class="sxs-lookup"><span data-stu-id="44075-123">The following sections describe additions to the project when authentication support is included.</span></span>

### <a name="startup-class"></a><span data-ttu-id="44075-124">Classe di avvio</span><span class="sxs-lookup"><span data-stu-id="44075-124">Startup class</span></span>

<span data-ttu-id="44075-125">La `Startup` classe presenta le aggiunte seguenti.</span><span class="sxs-lookup"><span data-stu-id="44075-125">The `Startup` class has the following additions.</span></span>

* <span data-ttu-id="44075-126">In `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="44075-126">In `Startup.ConfigureServices`:</span></span>

  * <span data-ttu-id="44075-127">ASP.NET Core Identity :</span><span class="sxs-lookup"><span data-stu-id="44075-127">ASP.NET Core Identity:</span></span>

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(
            Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>(options => 
            options.SignIn.RequireConfirmedAccount = true)
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * Identity<span data-ttu-id="44075-128">Server con un <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> metodo helper aggiuntivo che imposta le convenzioni ASP.NET Core predefinite sul Identity Server:</span><span class="sxs-lookup"><span data-stu-id="44075-128">Server with an additional <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method that sets up default ASP.NET Core conventions on top of IdentityServer:</span></span>

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * <span data-ttu-id="44075-129">Autenticazione con un <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> metodo helper aggiuntivo che configura l'app per convalidare i token JWT prodotti dal Identity Server:</span><span class="sxs-lookup"><span data-stu-id="44075-129">Authentication with an additional <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper method that configures the app to validate JWT tokens produced by IdentityServer:</span></span>

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* <span data-ttu-id="44075-130">In `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="44075-130">In `Startup.Configure`:</span></span>

  * <span data-ttu-id="44075-131">Il Identity middleware server espone gli endpoint Open ID Connect (OIDC):</span><span class="sxs-lookup"><span data-stu-id="44075-131">The IdentityServer middleware exposes the Open ID Connect (OIDC) endpoints:</span></span>

    ```csharp
    app.UseIdentityServer();
    ```

  * <span data-ttu-id="44075-132">Il middleware di autenticazione è responsabile della convalida delle credenziali della richiesta e dell'impostazione dell'utente nel contesto della richiesta:</span><span class="sxs-lookup"><span data-stu-id="44075-132">The Authentication middleware is responsible for validating request credentials and setting the user on the request context:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

  * <span data-ttu-id="44075-133">Il middleware di autorizzazione consente le funzionalità di autorizzazione:</span><span class="sxs-lookup"><span data-stu-id="44075-133">Authorization Middleware enables authorization capabilities:</span></span>

    ```csharp
    app.UseAuthentication();
    app.UseAuthorization();
    ```

### <a name="addapiauthorization"></a><span data-ttu-id="44075-134">AddApiAuthorization</span><span class="sxs-lookup"><span data-stu-id="44075-134">AddApiAuthorization</span></span>

<span data-ttu-id="44075-135">Il <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> metodo helper configura il [ Identity server](https://identityserver.io/) per gli scenari di ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="44075-135">The <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method configures [IdentityServer](https://identityserver.io/) for ASP.NET Core scenarios.</span></span> Identity<span data-ttu-id="44075-136">Server è un Framework potente ed estendibile per la gestione dei problemi di sicurezza delle app.</span><span class="sxs-lookup"><span data-stu-id="44075-136">Server is a powerful and extensible framework for handling app security concerns.</span></span> Identity<span data-ttu-id="44075-137">Il server espone complessità non necessaria per gli scenari più comuni.</span><span class="sxs-lookup"><span data-stu-id="44075-137">Server exposes unnecessary complexity for the most common scenarios.</span></span> <span data-ttu-id="44075-138">Di conseguenza, viene fornito un set di convenzioni e opzioni di configurazione che consideriamo un valido punto di partenza.</span><span class="sxs-lookup"><span data-stu-id="44075-138">Consequently, a set of conventions and configuration options is provided that we consider a good starting point.</span></span> <span data-ttu-id="44075-139">Una volta modificate le esigenze di autenticazione, è disponibile tutta la potenza del Identity Server per personalizzare l'autenticazione per soddisfare i requisiti di un'app.</span><span class="sxs-lookup"><span data-stu-id="44075-139">Once your authentication needs change, the full power of IdentityServer is available to customize authentication to suit an app's requirements.</span></span>

### <a name="addidentityserverjwt"></a><span data-ttu-id="44075-140">Aggiungi Identity ServerJwt</span><span class="sxs-lookup"><span data-stu-id="44075-140">AddIdentityServerJwt</span></span>

<span data-ttu-id="44075-141">Il <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> metodo helper configura uno schema di criteri per l'app come gestore di autenticazione predefinito.</span><span class="sxs-lookup"><span data-stu-id="44075-141">The <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper method configures a policy scheme for the app as the default authentication handler.</span></span> <span data-ttu-id="44075-142">Il criterio è configurato in modo da consentire Identity a di gestire tutte le richieste indirizzate a qualsiasi sottopercorso nello Identity spazio URL `/Identity` .</span><span class="sxs-lookup"><span data-stu-id="44075-142">The policy is configured to allow Identity to handle all requests routed to any subpath in the Identity URL space `/Identity`.</span></span> <span data-ttu-id="44075-143"><xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler>Gestisce tutte le altre richieste.</span><span class="sxs-lookup"><span data-stu-id="44075-143">The <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> handles all other requests.</span></span> <span data-ttu-id="44075-144">Inoltre, questo metodo:</span><span class="sxs-lookup"><span data-stu-id="44075-144">Additionally, this method:</span></span>

* <span data-ttu-id="44075-145">Registra una `{APPLICATION NAME}API` risorsa API con Identity un server con un ambito predefinito `{APPLICATION NAME}API` .</span><span class="sxs-lookup"><span data-stu-id="44075-145">Registers an `{APPLICATION NAME}API` API resource with IdentityServer with a default scope of `{APPLICATION NAME}API`.</span></span>
* <span data-ttu-id="44075-146">Configura il middleware del token di porta JWT per convalidare i token emessi dal Identity Server per l'app.</span><span class="sxs-lookup"><span data-stu-id="44075-146">Configures the JWT Bearer Token Middleware to validate tokens issued by IdentityServer for the app.</span></span>

### <a name="weatherforecastcontroller"></a><span data-ttu-id="44075-147">WeatherForecastController</span><span class="sxs-lookup"><span data-stu-id="44075-147">WeatherForecastController</span></span>

<span data-ttu-id="44075-148">In `WeatherForecastController` ( `Controllers/WeatherForecastController.cs` ), l' [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attributo viene applicato alla classe.</span><span class="sxs-lookup"><span data-stu-id="44075-148">In the `WeatherForecastController` (`Controllers/WeatherForecastController.cs`), the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute is applied to the class.</span></span> <span data-ttu-id="44075-149">L'attributo indica che l'utente deve essere autorizzato in base ai criteri predefiniti per accedere alla risorsa.</span><span class="sxs-lookup"><span data-stu-id="44075-149">The attribute indicates that the user must be authorized based on the default policy to access the resource.</span></span> <span data-ttu-id="44075-150">I criteri di autorizzazione predefiniti sono configurati per l'utilizzo dello schema di autenticazione predefinito, impostato da <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> .</span><span class="sxs-lookup"><span data-stu-id="44075-150">The default authorization policy is configured to use the default authentication scheme, which is set up by <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A>.</span></span> <span data-ttu-id="44075-151">Il metodo helper viene configurato <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> come gestore predefinito per le richieste all'app.</span><span class="sxs-lookup"><span data-stu-id="44075-151">The helper method configures <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> as the default handler for requests to the app.</span></span>

### <a name="applicationdbcontext"></a><span data-ttu-id="44075-152">ApplicationDbContext</span><span class="sxs-lookup"><span data-stu-id="44075-152">ApplicationDbContext</span></span>

<span data-ttu-id="44075-153">In `ApplicationDbContext` ( `Data/ApplicationDbContext.cs` ), <xref:Microsoft.EntityFrameworkCore.DbContext> estende <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> per includere lo schema per il Identity Server.</span><span class="sxs-lookup"><span data-stu-id="44075-153">In the `ApplicationDbContext` (`Data/ApplicationDbContext.cs`), <xref:Microsoft.EntityFrameworkCore.DbContext> extends <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> to include the schema for IdentityServer.</span></span> <span data-ttu-id="44075-154">L'oggetto <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> è derivato da <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>.</span><span class="sxs-lookup"><span data-stu-id="44075-154"><xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> is derived from <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>.</span></span>

<span data-ttu-id="44075-155">Per ottenere il controllo completo dello schema del database, ereditare da una delle Identity <xref:Microsoft.EntityFrameworkCore.DbContext> classi disponibili e configurare il contesto per includere lo Identity schema chiamando `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` nel <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> metodo.</span><span class="sxs-lookup"><span data-stu-id="44075-155">To gain full control of the database schema, inherit from one of the available Identity <xref:Microsoft.EntityFrameworkCore.DbContext> classes and configure the context to include the Identity schema by calling `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` in the <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> method.</span></span>

### <a name="oidcconfigurationcontroller"></a><span data-ttu-id="44075-156">OidcConfigurationController</span><span class="sxs-lookup"><span data-stu-id="44075-156">OidcConfigurationController</span></span>

<span data-ttu-id="44075-157">In `OidcConfigurationController` ( `Controllers/OidcConfigurationController.cs` ), viene eseguito il provisioning dell'endpoint client per gestire i parametri OIDC.</span><span class="sxs-lookup"><span data-stu-id="44075-157">In the `OidcConfigurationController` (`Controllers/OidcConfigurationController.cs`), the client endpoint is provisioned to serve OIDC parameters.</span></span>

### <a name="app-settings-files"></a><span data-ttu-id="44075-158">File di impostazioni dell'app</span><span class="sxs-lookup"><span data-stu-id="44075-158">App settings files</span></span>

<span data-ttu-id="44075-159">Nel file di impostazioni dell'app ( `appsettings.json` ) nella radice del progetto, la `IdentityServer` sezione descrive l'elenco dei client configurati.</span><span class="sxs-lookup"><span data-stu-id="44075-159">In the app settings file (`appsettings.json`) at the project root, the `IdentityServer` section describes the list of configured clients.</span></span> <span data-ttu-id="44075-160">Nell'esempio seguente è presente un singolo client.</span><span class="sxs-lookup"><span data-stu-id="44075-160">In the following example, there's a single client.</span></span> <span data-ttu-id="44075-161">Il nome del client corrisponde al nome dell'app e viene mappato per convenzione al `ClientId` parametro OAuth.</span><span class="sxs-lookup"><span data-stu-id="44075-161">The client name corresponds to the app name and is mapped by convention to the OAuth `ClientId` parameter.</span></span> <span data-ttu-id="44075-162">Il profilo indica il tipo di app da configurare.</span><span class="sxs-lookup"><span data-stu-id="44075-162">The profile indicates the app type being configured.</span></span> <span data-ttu-id="44075-163">Il profilo viene utilizzato internamente per guidare le convenzioni che semplificano il processo di configurazione per il server.</span><span class="sxs-lookup"><span data-stu-id="44075-163">The profile is used internally to drive conventions that simplify the configuration process for the server.</span></span> <!-- There are several profiles available, as explained in the [Application profiles](#application-profiles) section. -->

```json
"IdentityServer": {
  "Clients": {
    "{APP ASSEMBLY}.Client": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

<span data-ttu-id="44075-164">Il segnaposto `{APP ASSEMBLY}` è il nome dell'assembly dell'app (ad esempio, `BlazorSample.Client` ).</span><span class="sxs-lookup"><span data-stu-id="44075-164">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `BlazorSample.Client`).</span></span>

## <a name="client-app-configuration"></a><span data-ttu-id="44075-165">Configurazione dell'app client</span><span class="sxs-lookup"><span data-stu-id="44075-165">Client app configuration</span></span>

### <a name="authentication-package"></a><span data-ttu-id="44075-166">Pacchetto di autenticazione</span><span class="sxs-lookup"><span data-stu-id="44075-166">Authentication package</span></span>

<span data-ttu-id="44075-167">Quando viene creata un'app per l'uso di singoli account utente ( `Individual` ), l'app riceve automaticamente un riferimento al pacchetto [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) nel file di progetto dell'app.</span><span class="sxs-lookup"><span data-stu-id="44075-167">When an app is created to use Individual User Accounts (`Individual`), the app automatically receives a package reference for the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package in the app's project file.</span></span> <span data-ttu-id="44075-168">Il pacchetto fornisce un set di primitive che consentono all'app di autenticare gli utenti e ottenere i token per chiamare le API protette.</span><span class="sxs-lookup"><span data-stu-id="44075-168">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="44075-169">Se si aggiunge l'autenticazione a un'app, aggiungere manualmente il pacchetto al file di progetto dell'app:</span><span class="sxs-lookup"><span data-stu-id="44075-169">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference 
  Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
  Version="3.2.0" />
```

### <a name="api-authorization-support"></a><span data-ttu-id="44075-170">Supporto dell'autorizzazione API</span><span class="sxs-lookup"><span data-stu-id="44075-170">API authorization support</span></span>

<span data-ttu-id="44075-171">Il supporto per l'autenticazione degli utenti è collegato al contenitore del servizio mediante il metodo di estensione fornito nel [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) pacchetto.</span><span class="sxs-lookup"><span data-stu-id="44075-171">The support for authenticating users is plugged into the service container by the extension method provided inside the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package.</span></span> <span data-ttu-id="44075-172">Questo metodo configura i servizi richiesti dall'app per interagire con il sistema di autorizzazione esistente.</span><span class="sxs-lookup"><span data-stu-id="44075-172">This method sets up the services required by the app to interact with the existing authorization system.</span></span>

```csharp
builder.Services.AddApiAuthorization();
```

<span data-ttu-id="44075-173">Per impostazione predefinita, la configurazione per l'app viene caricata per convenzione da `_configuration/{client-id}` .</span><span class="sxs-lookup"><span data-stu-id="44075-173">By default, configuration for the app is loaded by convention from `_configuration/{client-id}`.</span></span> <span data-ttu-id="44075-174">Per convenzione, l'ID client è impostato sul nome dell'assembly dell'app.</span><span class="sxs-lookup"><span data-stu-id="44075-174">By convention, the client ID is set to the app's assembly name.</span></span> <span data-ttu-id="44075-175">Questo URL può essere modificato in modo che punti a un endpoint separato chiamando l'overload con options.</span><span class="sxs-lookup"><span data-stu-id="44075-175">This URL can be changed to point to a separate endpoint by calling the overload with options.</span></span>

### <a name="imports-file"></a><span data-ttu-id="44075-176">Importa file</span><span class="sxs-lookup"><span data-stu-id="44075-176">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="44075-177">Pagina di indice</span><span class="sxs-lookup"><span data-stu-id="44075-177">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

### <a name="app-component"></a><span data-ttu-id="44075-178">Componente app</span><span class="sxs-lookup"><span data-stu-id="44075-178">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="44075-179">Componente RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="44075-179">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="44075-180">Componente LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="44075-180">LoginDisplay component</span></span>

<span data-ttu-id="44075-181">Il `LoginDisplay` componente ( `Shared/LoginDisplay.razor` ) viene sottoposto a rendering nel `MainLayout` componente ( `Shared/MainLayout.razor` ) e gestisce i comportamenti seguenti:</span><span class="sxs-lookup"><span data-stu-id="44075-181">The `LoginDisplay` component (`Shared/LoginDisplay.razor`) is rendered in the `MainLayout` component (`Shared/MainLayout.razor`) and manages the following behaviors:</span></span>

* <span data-ttu-id="44075-182">Per utenti autenticati:</span><span class="sxs-lookup"><span data-stu-id="44075-182">For authenticated users:</span></span>
  * <span data-ttu-id="44075-183">Visualizza il nome utente corrente.</span><span class="sxs-lookup"><span data-stu-id="44075-183">Displays the current user name.</span></span>
  * <span data-ttu-id="44075-184">Offre un collegamento alla pagina del profilo utente in ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="44075-184">Offers a link to the user profile page in ASP.NET Core Identity.</span></span>
  * <span data-ttu-id="44075-185">Offre un pulsante per disconnettersi dall'app.</span><span class="sxs-lookup"><span data-stu-id="44075-185">Offers a button to log out of the app.</span></span>
* <span data-ttu-id="44075-186">Per utenti anonimi:</span><span class="sxs-lookup"><span data-stu-id="44075-186">For anonymous users:</span></span>
  * <span data-ttu-id="44075-187">Offre la possibilità di effettuare la registrazione.</span><span class="sxs-lookup"><span data-stu-id="44075-187">Offers the option to register.</span></span>
  * <span data-ttu-id="44075-188">Offre la possibilità di effettuare l'accesso.</span><span class="sxs-lookup"><span data-stu-id="44075-188">Offers the option to log in.</span></span>

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

### <a name="authentication-component"></a><span data-ttu-id="44075-189">Componente di autenticazione</span><span class="sxs-lookup"><span data-stu-id="44075-189">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="44075-190">Componente FetchData</span><span class="sxs-lookup"><span data-stu-id="44075-190">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="44075-191">Eseguire l'app</span><span class="sxs-lookup"><span data-stu-id="44075-191">Run the app</span></span>

<span data-ttu-id="44075-192">Eseguire l'app dal progetto server.</span><span class="sxs-lookup"><span data-stu-id="44075-192">Run the app from the Server project.</span></span> <span data-ttu-id="44075-193">Quando si usa Visual Studio, eseguire una delle operazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="44075-193">When using Visual Studio, either:</span></span>

* <span data-ttu-id="44075-194">Impostare l'elenco a discesa **progetti di avvio** nella barra degli strumenti sull'app per le *API server* e selezionare il pulsante **Esegui** .</span><span class="sxs-lookup"><span data-stu-id="44075-194">Set the **Startup Projects** drop down list in the toolbar to the *Server API app* and select the **Run** button.</span></span>
* <span data-ttu-id="44075-195">Selezionare il progetto server in **Esplora soluzioni** , quindi fare clic sul pulsante **Esegui** nella barra degli strumenti o avviare l'app dal menu **debug** .</span><span class="sxs-lookup"><span data-stu-id="44075-195">Select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

## <a name="name-and-role-claim-with-api-authorization"></a><span data-ttu-id="44075-196">Nome e attestazione di ruolo con autorizzazione API</span><span class="sxs-lookup"><span data-stu-id="44075-196">Name and role claim with API authorization</span></span>

### <a name="custom-user-factory"></a><span data-ttu-id="44075-197">Factory utente personalizzata</span><span class="sxs-lookup"><span data-stu-id="44075-197">Custom user factory</span></span>

<span data-ttu-id="44075-198">Nell'app client creare una factory utente personalizzata.</span><span class="sxs-lookup"><span data-stu-id="44075-198">In the Client app, create a custom user factory.</span></span> Identity<span data-ttu-id="44075-199">Il server invia più ruoli come matrice JSON in un'unica `role` attestazione.</span><span class="sxs-lookup"><span data-stu-id="44075-199"> Server sends multiple roles as a JSON array in a single `role` claim.</span></span> <span data-ttu-id="44075-200">Un singolo ruolo viene inviato come valore stringa nell'attestazione.</span><span class="sxs-lookup"><span data-stu-id="44075-200">A single role is sent as a string value in the claim.</span></span> <span data-ttu-id="44075-201">La factory crea una singola `role` attestazione per ogni ruolo dell'utente.</span><span class="sxs-lookup"><span data-stu-id="44075-201">The factory creates an individual `role` claim for each of the user's roles.</span></span>

<span data-ttu-id="44075-202">`CustomUserFactory.cs`:</span><span class="sxs-lookup"><span data-stu-id="44075-202">`CustomUserFactory.cs`:</span></span>

```csharp
using System.Linq;
using System.Security.Claims;
using System.Text.Json;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication.Internal;

public class CustomUserFactory
    : AccountClaimsPrincipalFactory<RemoteUserAccount>
{
    public CustomUserFactory(IAccessTokenProviderAccessor accessor)
        : base(accessor)
    {
    }

    public async override ValueTask<ClaimsPrincipal> CreateUserAsync(
        RemoteUserAccount account,
        RemoteAuthenticationUserOptions options)
    {
        var user = await base.CreateUserAsync(account, options);

        if (user.Identity.IsAuthenticated)
        {
            var identity = (ClaimsIdentity)user.Identity;
            var roleClaims = identity.FindAll(identity.RoleClaimType);

            if (roleClaims != null && roleClaims.Any())
            {
                foreach (var existingClaim in roleClaims)
                {
                    identity.RemoveClaim(existingClaim);
                }

                var rolesElem = account.AdditionalProperties[identity.RoleClaimType];

                if (rolesElem is JsonElement roles)
                {
                    if (roles.ValueKind == JsonValueKind.Array)
                    {
                        foreach (var role in roles.EnumerateArray())
                        {
                            identity.AddClaim(new Claim(options.RoleClaim, role.GetString()));
                        }
                    }
                    else
                    {
                        identity.AddClaim(new Claim(options.RoleClaim, roles.GetString()));
                    }
                }
            }
        }

        return user;
    }
}
```

<span data-ttu-id="44075-203">Nell'app client registrare la factory in `Program.Main` ( `Program.cs` ):</span><span class="sxs-lookup"><span data-stu-id="44075-203">In the Client app, register the factory in `Program.Main` (`Program.cs`):</span></span>

```csharp
builder.Services.AddApiAuthorization()
    .AddAccountClaimsPrincipalFactory<CustomUserFactory>();
```

<span data-ttu-id="44075-204">Nell'app Server, chiamare <xref:Microsoft.AspNetCore.Identity.IdentityBuilder.AddRoles*> sul Identity Generatore, che aggiunge i servizi correlati ai ruoli:</span><span class="sxs-lookup"><span data-stu-id="44075-204">In the Server app, call <xref:Microsoft.AspNetCore.Identity.IdentityBuilder.AddRoles*> on the Identity builder, which adds role-related services:</span></span>

```csharp
using Microsoft.AspNetCore.Identity;

...

services.AddDefaultIdentity<ApplicationUser>(options => 
    options.SignIn.RequireConfirmedAccount = true)
    .AddRoles<IdentityRole>()
    .AddEntityFrameworkStores<ApplicationDbContext>();
```

### <a name="configure-identity-server"></a><span data-ttu-id="44075-205">Configura Identity Server</span><span class="sxs-lookup"><span data-stu-id="44075-205">Configure Identity Server</span></span>

<span data-ttu-id="44075-206">Usare **uno** degli approcci seguenti:</span><span class="sxs-lookup"><span data-stu-id="44075-206">Use **one** of the following approaches:</span></span>

* [<span data-ttu-id="44075-207">Opzioni di autorizzazione API</span><span class="sxs-lookup"><span data-stu-id="44075-207">API authorization options</span></span>](#api-authorization-options)
* [<span data-ttu-id="44075-208">Servizio profili</span><span class="sxs-lookup"><span data-stu-id="44075-208">Profile Service</span></span>](#profile-service)

#### <a name="api-authorization-options"></a><span data-ttu-id="44075-209">Opzioni di autorizzazione API</span><span class="sxs-lookup"><span data-stu-id="44075-209">API authorization options</span></span>

<span data-ttu-id="44075-210">Nell'app Server:</span><span class="sxs-lookup"><span data-stu-id="44075-210">In the Server app:</span></span>

* <span data-ttu-id="44075-211">Configurare Identity il server in modo che inserisca le `name` `role` attestazioni e nel token ID e nel token di accesso.</span><span class="sxs-lookup"><span data-stu-id="44075-211">Configure Identity Server to put the `name` and `role` claims into the ID token and access token.</span></span>
* <span data-ttu-id="44075-212">Impedire il mapping predefinito per i ruoli nel gestore del token JWT.</span><span class="sxs-lookup"><span data-stu-id="44075-212">Prevent the default mapping for roles in the JWT token handler.</span></span>

```csharp
using System.IdentityModel.Tokens.Jwt;
using System.Linq;

...

services.AddIdentityServer()
    .AddApiAuthorization<ApplicationUser, ApplicationDbContext>(options => {
        options.IdentityResources["openid"].UserClaims.Add("name");
        options.ApiResources.Single().UserClaims.Add("name");
        options.IdentityResources["openid"].UserClaims.Add("role");
        options.ApiResources.Single().UserClaims.Add("role");
    });

JwtSecurityTokenHandler.DefaultInboundClaimTypeMap.Remove("role");
```

#### <a name="profile-service"></a><span data-ttu-id="44075-213">Servizio profili</span><span class="sxs-lookup"><span data-stu-id="44075-213">Profile Service</span></span>

<span data-ttu-id="44075-214">Nell'app Server creare un' `ProfileService` implementazione.</span><span class="sxs-lookup"><span data-stu-id="44075-214">In the Server app, create a `ProfileService` implementation.</span></span>

<span data-ttu-id="44075-215">`ProfileService.cs`:</span><span class="sxs-lookup"><span data-stu-id="44075-215">`ProfileService.cs`:</span></span>

```csharp
using IdentityModel;
using IdentityServer4.Models;
using IdentityServer4.Services;
using System.Threading.Tasks;

public class ProfileService : IProfileService
{
    public ProfileService()
    {
    }

    public Task GetProfileDataAsync(ProfileDataRequestContext context)
    {
        var nameClaim = context.Subject.FindAll(JwtClaimTypes.Name);
        context.IssuedClaims.AddRange(nameClaim);

        var roleClaims = context.Subject.FindAll(JwtClaimTypes.Role);
        context.IssuedClaims.AddRange(roleClaims);

        return Task.CompletedTask;
    }

    public Task IsActiveAsync(IsActiveContext context)
    {
        return Task.CompletedTask;
    }
}
```

<span data-ttu-id="44075-216">Nell'app server registrare il servizio profili in `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="44075-216">In the Server app, register the Profile Service in `Startup.ConfigureServices`:</span></span>

```csharp
using IdentityServer4.Services;

...

services.AddTransient<IProfileService, ProfileService>();
```

### <a name="use-authorization-mechanisms"></a><span data-ttu-id="44075-217">Usare i meccanismi di autorizzazione</span><span class="sxs-lookup"><span data-stu-id="44075-217">Use authorization mechanisms</span></span>

<span data-ttu-id="44075-218">Nell'app client gli approcci di autorizzazione componenti sono funzionali a questo punto.</span><span class="sxs-lookup"><span data-stu-id="44075-218">In the Client app, component authorization approaches are functional at this point.</span></span> <span data-ttu-id="44075-219">Qualsiasi meccanismo di autorizzazione nei componenti può utilizzare un ruolo per autorizzare l'utente:</span><span class="sxs-lookup"><span data-stu-id="44075-219">Any of the authorization mechanisms in components can use a role to authorize the user:</span></span>

* <span data-ttu-id="44075-220">[ `AuthorizeView` Component](xref:blazor/security/index#authorizeview-component) (esempio: `<AuthorizeView Roles="admin">` )</span><span class="sxs-lookup"><span data-stu-id="44075-220">[`AuthorizeView` component](xref:blazor/security/index#authorizeview-component) (Example: `<AuthorizeView Roles="admin">`)</span></span>
* <span data-ttu-id="44075-221">[ `[Authorize]` direttiva attribute](xref:blazor/security/index#authorize-attribute) ( <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> ) (esempio: `@attribute [Authorize(Roles = "admin")]` )</span><span class="sxs-lookup"><span data-stu-id="44075-221">[`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) (Example: `@attribute [Authorize(Roles = "admin")]`)</span></span>
* <span data-ttu-id="44075-222">[Logica procedurale](xref:blazor/security/index#procedural-logic) (esempio: `if (user.IsInRole("admin")) { ... }` )</span><span class="sxs-lookup"><span data-stu-id="44075-222">[Procedural logic](xref:blazor/security/index#procedural-logic) (Example: `if (user.IsInRole("admin")) { ... }`)</span></span>

  <span data-ttu-id="44075-223">Sono supportati più test di ruolo:</span><span class="sxs-lookup"><span data-stu-id="44075-223">Multiple role tests are supported:</span></span>

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  {
      ...
  }
  ```

<span data-ttu-id="44075-224">`User.Identity.Name`viene popolato nell'app client con il nome utente dell'utente, che corrisponde in genere all'indirizzo di posta elettronica di accesso.</span><span class="sxs-lookup"><span data-stu-id="44075-224">`User.Identity.Name` is populated in the Client app with the user's user name, which is usually their sign-in email address.</span></span>

[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="44075-225">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="44075-225">Additional resources</span></span>

* [<span data-ttu-id="44075-226">Distribuzione nel servizio app Azure</span><span class="sxs-lookup"><span data-stu-id="44075-226">Deployment to Azure App Service</span></span>](xref:security/authentication/identity/spa#deploy-to-production)
* [<span data-ttu-id="44075-227">Importare un certificato da Key Vault (documentazione di Azure)</span><span class="sxs-lookup"><span data-stu-id="44075-227">Import a certificate from Key Vault (Azure documentation)</span></span>](/azure/app-service/configure-ssl-certificate#import-a-certificate-from-key-vault)
* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="44075-228">Richieste API Web non autenticate o non autorizzate in un'app con un client predefinito sicuro</span><span class="sxs-lookup"><span data-stu-id="44075-228">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
