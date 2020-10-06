---
title: Proteggere un'app ASP.NET Core ospitata Blazor WebAssembly con il Identity Server
author: guardrex
description: Informazioni su come proteggere un'app ASP.NET Core ospitata Blazor WebAssembly con il Identity Server.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/02/2020
no-loc:
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
uid: blazor/security/webassembly/hosted-with-identity-server
ms.openlocfilehash: 91cc7ffc46f5f1f68efd7e481479b19938476cb0
ms.sourcegitcommit: d7991068bc6b04063f4bd836fc5b9591d614d448
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/06/2020
ms.locfileid: "91762243"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-hosted-app-with-no-locidentity-server"></a><span data-ttu-id="15659-103">Proteggere un' Blazor WebAssembly app ospitata ASP.NET Core Identity con server</span><span class="sxs-lookup"><span data-stu-id="15659-103">Secure an ASP.NET Core Blazor WebAssembly hosted app with Identity Server</span></span>

<span data-ttu-id="15659-104">Di [Javier Calvarro Nelson](https://github.com/javiercn) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="15659-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="15659-105">Questo articolo illustra come creare un' [ Blazor WebAssembly app ospitata](xref:blazor/hosting-models#blazor-webassembly) che usa il [ Identity Server](https://identityserver.io/) per autenticare gli utenti e le chiamate API.</span><span class="sxs-lookup"><span data-stu-id="15659-105">This article explains how to create a [hosted Blazor WebAssembly app](xref:blazor/hosting-models#blazor-webassembly) that uses [IdentityServer](https://identityserver.io/) to authenticate users and API calls.</span></span>

> [!NOTE]
> <span data-ttu-id="15659-106">Per configurare un'app autonoma o ospitata Blazor WebAssembly per l'uso di un' Identity istanza del server esterno esistente, seguire le istruzioni riportate in <xref:blazor/security/webassembly/standalone-with-authentication-library> .</span><span class="sxs-lookup"><span data-stu-id="15659-106">To configure a standalone or hosted Blazor WebAssembly app to use an existing, external Identity Server instance, follow the guidance in <xref:blazor/security/webassembly/standalone-with-authentication-library>.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="15659-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="15659-107">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="15659-108">Per creare un nuovo Blazor WebAssembly progetto con un meccanismo di autenticazione:</span><span class="sxs-lookup"><span data-stu-id="15659-108">To create a new Blazor WebAssembly project with an authentication mechanism:</span></span>

1. <span data-ttu-id="15659-109">Dopo aver scelto il modello \*\* Blazor WebAssembly app\*\* nella finestra di dialogo **Crea un nuovo ASP.NET Core applicazione Web** , selezionare **Cambia** in **autenticazione**.</span><span class="sxs-lookup"><span data-stu-id="15659-109">After choosing the **Blazor WebAssembly App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication**.</span></span>

1. <span data-ttu-id="15659-110">Selezionare **singoli account utente** con l'opzione **Archivia account utente in-app** per archiviare gli utenti all'interno dell'app usando il sistema di ASP.NET Core [Identity](xref:security/authentication/identity) .</span><span class="sxs-lookup"><span data-stu-id="15659-110">Select **Individual User Accounts** with the **Store user accounts in-app** option to store users within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system.</span></span>

1. <span data-ttu-id="15659-111">Nella sezione **Avanzate** Selezionare la casella di controllo **ASP.NET Core Hosted** .</span><span class="sxs-lookup"><span data-stu-id="15659-111">Select the **ASP.NET Core hosted** check box in the **Advanced** section.</span></span>

# <a name="visual-studio-code--net-core-cli"></a>[<span data-ttu-id="15659-112">Visual Studio Code/Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="15659-112">Visual Studio Code / .NET Core CLI</span></span>](#tab/visual-studio-code+netcore-cli)

<span data-ttu-id="15659-113">Per creare un nuovo Blazor WebAssembly progetto con un meccanismo di autenticazione in una cartella vuota, specificare il `Individual` meccanismo di autenticazione con l' `-au|--auth` opzione per archiviare gli utenti all'interno dell'app usando il sistema di ASP.NET Core [Identity](xref:security/authentication/identity) :</span><span class="sxs-lookup"><span data-stu-id="15659-113">To create a new Blazor WebAssembly project with an authentication mechanism in an empty folder, specify the `Individual` authentication mechanism with the `-au|--auth` option to store users within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system:</span></span>

```dotnetcli
dotnet new blazorwasm -au Individual -ho -o {APP NAME}
```

| <span data-ttu-id="15659-114">Segnaposto</span><span class="sxs-lookup"><span data-stu-id="15659-114">Placeholder</span></span>  | <span data-ttu-id="15659-115">Esempio</span><span class="sxs-lookup"><span data-stu-id="15659-115">Example</span></span>        |
| ------------ | -------------- |
| `{APP NAME}` | `BlazorSample` |

<span data-ttu-id="15659-116">Il percorso di output specificato con l' `-o|--output` opzione Crea una cartella di progetto se non esiste e diventa parte del nome dell'app.</span><span class="sxs-lookup"><span data-stu-id="15659-116">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

<span data-ttu-id="15659-117">Per ulteriori informazioni, vedere il [`dotnet new`](/dotnet/core/tools/dotnet-new) comando nella Guida di .NET Core.</span><span class="sxs-lookup"><span data-stu-id="15659-117">For more information, see the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="15659-118">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="15659-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="15659-119">Per creare un nuovo Blazor WebAssembly progetto con un meccanismo di autenticazione:</span><span class="sxs-lookup"><span data-stu-id="15659-119">To create a new Blazor WebAssembly project with an authentication mechanism:</span></span>

1. <span data-ttu-id="15659-120">Nel passaggio **configurare la nuova Blazor WebAssembly app** selezionare **autenticazione singola (in-app) nell'elenco a** discesa **autenticazione** .</span><span class="sxs-lookup"><span data-stu-id="15659-120">On the **Configure your new Blazor WebAssembly App** step, select **Individual Authentication (in-app)** from the **Authentication** drop down.</span></span>

1. <span data-ttu-id="15659-121">L'app viene creata per i singoli utenti archiviati nell'app con ASP.NET Core [Identity](xref:security/authentication/identity) .</span><span class="sxs-lookup"><span data-stu-id="15659-121">The app is created for individual users stored in the app with ASP.NET Core [Identity](xref:security/authentication/identity).</span></span>

1. <span data-ttu-id="15659-122">Selezionare la casella di controllo **ASP.NET Core Hosted** .</span><span class="sxs-lookup"><span data-stu-id="15659-122">Select the **ASP.NET Core hosted** check box.</span></span>

---

## <a name="server-app-configuration"></a><span data-ttu-id="15659-123">Configurazione dell'app Server</span><span class="sxs-lookup"><span data-stu-id="15659-123">Server app configuration</span></span>

<span data-ttu-id="15659-124">Le sezioni seguenti descrivono le aggiunte al progetto quando è incluso il supporto per l'autenticazione.</span><span class="sxs-lookup"><span data-stu-id="15659-124">The following sections describe additions to the project when authentication support is included.</span></span>

### <a name="startup-class"></a><span data-ttu-id="15659-125">Classe di avvio</span><span class="sxs-lookup"><span data-stu-id="15659-125">Startup class</span></span>

<span data-ttu-id="15659-126">La `Startup` classe presenta le aggiunte seguenti.</span><span class="sxs-lookup"><span data-stu-id="15659-126">The `Startup` class has the following additions.</span></span>

* <span data-ttu-id="15659-127">In `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="15659-127">In `Startup.ConfigureServices`:</span></span>

  * <span data-ttu-id="15659-128">ASP.NET Core Identity:</span><span class="sxs-lookup"><span data-stu-id="15659-128">ASP.NET Core Identity:</span></span>

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(
            Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>(options => 
            options.SignIn.RequireConfirmedAccount = true)
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * <span data-ttu-id="15659-129">IdentityServer con un <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> metodo helper aggiuntivo che imposta le convenzioni ASP.NET Core predefinite sul Identity Server:</span><span class="sxs-lookup"><span data-stu-id="15659-129">IdentityServer with an additional <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method that sets up default ASP.NET Core conventions on top of IdentityServer:</span></span>

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * <span data-ttu-id="15659-130">Autenticazione con un <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> metodo helper aggiuntivo che configura l'app per convalidare i token JWT prodotti dal Identity Server:</span><span class="sxs-lookup"><span data-stu-id="15659-130">Authentication with an additional <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper method that configures the app to validate JWT tokens produced by IdentityServer:</span></span>

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* <span data-ttu-id="15659-131">In `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="15659-131">In `Startup.Configure`:</span></span>

  * <span data-ttu-id="15659-132">Il Identity middleware server espone gli endpoint OpenID Connect (OIDC):</span><span class="sxs-lookup"><span data-stu-id="15659-132">The IdentityServer middleware exposes the OpenID Connect (OIDC) endpoints:</span></span>

    ```csharp
    app.UseIdentityServer();
    ```

  * <span data-ttu-id="15659-133">Il middleware di autenticazione è responsabile della convalida delle credenziali della richiesta e dell'impostazione dell'utente nel contesto della richiesta:</span><span class="sxs-lookup"><span data-stu-id="15659-133">The Authentication middleware is responsible for validating request credentials and setting the user on the request context:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

  * <span data-ttu-id="15659-134">Il middleware di autorizzazione consente le funzionalità di autorizzazione:</span><span class="sxs-lookup"><span data-stu-id="15659-134">Authorization Middleware enables authorization capabilities:</span></span>

    ```csharp
    app.UseAuthentication();
    app.UseAuthorization();
    ```

### <a name="addapiauthorization"></a><span data-ttu-id="15659-135">AddApiAuthorization</span><span class="sxs-lookup"><span data-stu-id="15659-135">AddApiAuthorization</span></span>

<span data-ttu-id="15659-136">Il <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> metodo helper configura il [ Identity server](https://identityserver.io/) per gli scenari di ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="15659-136">The <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method configures [IdentityServer](https://identityserver.io/) for ASP.NET Core scenarios.</span></span> <span data-ttu-id="15659-137">IdentityServer è un Framework potente ed estendibile per la gestione dei problemi di sicurezza delle app.</span><span class="sxs-lookup"><span data-stu-id="15659-137">IdentityServer is a powerful and extensible framework for handling app security concerns.</span></span> <span data-ttu-id="15659-138">IdentityIl server espone complessità non necessaria per gli scenari più comuni.</span><span class="sxs-lookup"><span data-stu-id="15659-138">IdentityServer exposes unnecessary complexity for the most common scenarios.</span></span> <span data-ttu-id="15659-139">Di conseguenza, viene fornito un set di convenzioni e opzioni di configurazione che consideriamo un valido punto di partenza.</span><span class="sxs-lookup"><span data-stu-id="15659-139">Consequently, a set of conventions and configuration options is provided that we consider a good starting point.</span></span> <span data-ttu-id="15659-140">Una volta modificate le esigenze di autenticazione, è disponibile tutta la potenza del Identity Server per personalizzare l'autenticazione per soddisfare i requisiti di un'app.</span><span class="sxs-lookup"><span data-stu-id="15659-140">Once your authentication needs change, the full power of IdentityServer is available to customize authentication to suit an app's requirements.</span></span>

### <a name="addno-locidentityserverjwt"></a><span data-ttu-id="15659-141">Aggiungi Identity ServerJwt</span><span class="sxs-lookup"><span data-stu-id="15659-141">AddIdentityServerJwt</span></span>

<span data-ttu-id="15659-142">Il <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> metodo helper configura uno schema di criteri per l'app come gestore di autenticazione predefinito.</span><span class="sxs-lookup"><span data-stu-id="15659-142">The <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper method configures a policy scheme for the app as the default authentication handler.</span></span> <span data-ttu-id="15659-143">Il criterio è configurato in modo da consentire Identity a di gestire tutte le richieste indirizzate a qualsiasi sottopercorso nello Identity spazio URL `/Identity` .</span><span class="sxs-lookup"><span data-stu-id="15659-143">The policy is configured to allow Identity to handle all requests routed to any subpath in the Identity URL space `/Identity`.</span></span> <span data-ttu-id="15659-144"><xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler>Gestisce tutte le altre richieste.</span><span class="sxs-lookup"><span data-stu-id="15659-144">The <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> handles all other requests.</span></span> <span data-ttu-id="15659-145">Inoltre, questo metodo:</span><span class="sxs-lookup"><span data-stu-id="15659-145">Additionally, this method:</span></span>

* <span data-ttu-id="15659-146">Registra una `{APPLICATION NAME}API` risorsa API con Identity un server con un ambito predefinito `{APPLICATION NAME}API` .</span><span class="sxs-lookup"><span data-stu-id="15659-146">Registers an `{APPLICATION NAME}API` API resource with IdentityServer with a default scope of `{APPLICATION NAME}API`.</span></span>
* <span data-ttu-id="15659-147">Configura il middleware del token di porta JWT per convalidare i token emessi dal Identity Server per l'app.</span><span class="sxs-lookup"><span data-stu-id="15659-147">Configures the JWT Bearer Token Middleware to validate tokens issued by IdentityServer for the app.</span></span>

### <a name="weatherforecastcontroller"></a><span data-ttu-id="15659-148">WeatherForecastController</span><span class="sxs-lookup"><span data-stu-id="15659-148">WeatherForecastController</span></span>

<span data-ttu-id="15659-149">In `WeatherForecastController` ( `Controllers/WeatherForecastController.cs` ), l' [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attributo viene applicato alla classe.</span><span class="sxs-lookup"><span data-stu-id="15659-149">In the `WeatherForecastController` (`Controllers/WeatherForecastController.cs`), the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute is applied to the class.</span></span> <span data-ttu-id="15659-150">L'attributo indica che l'utente deve essere autorizzato in base ai criteri predefiniti per accedere alla risorsa.</span><span class="sxs-lookup"><span data-stu-id="15659-150">The attribute indicates that the user must be authorized based on the default policy to access the resource.</span></span> <span data-ttu-id="15659-151">I criteri di autorizzazione predefiniti sono configurati per l'utilizzo dello schema di autenticazione predefinito, impostato da <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> .</span><span class="sxs-lookup"><span data-stu-id="15659-151">The default authorization policy is configured to use the default authentication scheme, which is set up by <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A>.</span></span> <span data-ttu-id="15659-152">Il metodo helper viene configurato <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> come gestore predefinito per le richieste all'app.</span><span class="sxs-lookup"><span data-stu-id="15659-152">The helper method configures <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> as the default handler for requests to the app.</span></span>

### <a name="applicationdbcontext"></a><span data-ttu-id="15659-153">ApplicationDbContext</span><span class="sxs-lookup"><span data-stu-id="15659-153">ApplicationDbContext</span></span>

<span data-ttu-id="15659-154">In `ApplicationDbContext` ( `Data/ApplicationDbContext.cs` ), <xref:Microsoft.EntityFrameworkCore.DbContext> estende <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> per includere lo schema per il Identity Server.</span><span class="sxs-lookup"><span data-stu-id="15659-154">In the `ApplicationDbContext` (`Data/ApplicationDbContext.cs`), <xref:Microsoft.EntityFrameworkCore.DbContext> extends <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> to include the schema for IdentityServer.</span></span> <span data-ttu-id="15659-155">L'oggetto <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> è derivato da <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>.</span><span class="sxs-lookup"><span data-stu-id="15659-155"><xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> is derived from <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>.</span></span>

<span data-ttu-id="15659-156">Per ottenere il controllo completo dello schema del database, ereditare da una delle Identity <xref:Microsoft.EntityFrameworkCore.DbContext> classi disponibili e configurare il contesto per includere lo Identity schema chiamando `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` nel <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> metodo.</span><span class="sxs-lookup"><span data-stu-id="15659-156">To gain full control of the database schema, inherit from one of the available Identity <xref:Microsoft.EntityFrameworkCore.DbContext> classes and configure the context to include the Identity schema by calling `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` in the <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> method.</span></span>

### <a name="oidcconfigurationcontroller"></a><span data-ttu-id="15659-157">OidcConfigurationController</span><span class="sxs-lookup"><span data-stu-id="15659-157">OidcConfigurationController</span></span>

<span data-ttu-id="15659-158">In `OidcConfigurationController` ( `Controllers/OidcConfigurationController.cs` ), viene eseguito il provisioning dell'endpoint client per gestire i parametri OIDC.</span><span class="sxs-lookup"><span data-stu-id="15659-158">In the `OidcConfigurationController` (`Controllers/OidcConfigurationController.cs`), the client endpoint is provisioned to serve OIDC parameters.</span></span>

### <a name="app-settings"></a><span data-ttu-id="15659-159">Impostazioni dell'app</span><span class="sxs-lookup"><span data-stu-id="15659-159">App settings</span></span>

<span data-ttu-id="15659-160">Nel file di impostazioni dell'app ( `appsettings.json` ) nella radice del progetto, la `IdentityServer` sezione descrive l'elenco dei client configurati.</span><span class="sxs-lookup"><span data-stu-id="15659-160">In the app settings file (`appsettings.json`) at the project root, the `IdentityServer` section describes the list of configured clients.</span></span> <span data-ttu-id="15659-161">Nell'esempio seguente è presente un singolo client.</span><span class="sxs-lookup"><span data-stu-id="15659-161">In the following example, there's a single client.</span></span> <span data-ttu-id="15659-162">Il nome del client corrisponde al nome dell'app e viene mappato per convenzione al `ClientId` parametro OAuth.</span><span class="sxs-lookup"><span data-stu-id="15659-162">The client name corresponds to the app name and is mapped by convention to the OAuth `ClientId` parameter.</span></span> <span data-ttu-id="15659-163">Il profilo indica il tipo di app da configurare.</span><span class="sxs-lookup"><span data-stu-id="15659-163">The profile indicates the app type being configured.</span></span> <span data-ttu-id="15659-164">Il profilo viene utilizzato internamente per guidare le convenzioni che semplificano il processo di configurazione per il server.</span><span class="sxs-lookup"><span data-stu-id="15659-164">The profile is used internally to drive conventions that simplify the configuration process for the server.</span></span> <!-- There are several profiles available, as explained in the [Application profiles](#application-profiles) section. -->

```json
"IdentityServer": {
  "Clients": {
    "{APP ASSEMBLY}.Client": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

<span data-ttu-id="15659-165">Il segnaposto `{APP ASSEMBLY}` è il nome dell'assembly dell'app (ad esempio, `BlazorSample.Client` ).</span><span class="sxs-lookup"><span data-stu-id="15659-165">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `BlazorSample.Client`).</span></span>

## <a name="client-app-configuration"></a><span data-ttu-id="15659-166">Configurazione dell'app client</span><span class="sxs-lookup"><span data-stu-id="15659-166">Client app configuration</span></span>

### <a name="authentication-package"></a><span data-ttu-id="15659-167">Pacchetto di autenticazione</span><span class="sxs-lookup"><span data-stu-id="15659-167">Authentication package</span></span>

<span data-ttu-id="15659-168">Quando viene creata un'app per l'uso di singoli account utente ( `Individual` ), l'app riceve automaticamente un riferimento al pacchetto [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) nel file di progetto dell'app.</span><span class="sxs-lookup"><span data-stu-id="15659-168">When an app is created to use Individual User Accounts (`Individual`), the app automatically receives a package reference for the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package in the app's project file.</span></span> <span data-ttu-id="15659-169">Il pacchetto fornisce un set di primitive che consentono all'app di autenticare gli utenti e ottenere i token per chiamare le API protette.</span><span class="sxs-lookup"><span data-stu-id="15659-169">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="15659-170">Se si aggiunge l'autenticazione a un'app, aggiungere manualmente il pacchetto al file di progetto dell'app:</span><span class="sxs-lookup"><span data-stu-id="15659-170">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference 
  Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
  Version="{VERSION}" />
```

<span data-ttu-id="15659-171">Per il segnaposto `{VERSION}` , la versione stabile più recente del pacchetto che corrisponde alla versione del Framework condiviso dell'app è disponibile nella cronologia delle **versioni** del pacchetto in [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication).</span><span class="sxs-lookup"><span data-stu-id="15659-171">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication).</span></span>

### <a name="httpclient-configuration"></a><span data-ttu-id="15659-172">`HttpClient` configurazione</span><span class="sxs-lookup"><span data-stu-id="15659-172">`HttpClient` configuration</span></span>

<span data-ttu-id="15659-173">In `Program.Main` ( `Program.cs` ), un denominato <xref:System.Net.Http.HttpClient> ( `HostIS.ServerAPI` ) viene configurato per fornire <xref:System.Net.Http.HttpClient> istanze che includono token di accesso quando si effettuano richieste all'API del server:</span><span class="sxs-lookup"><span data-stu-id="15659-173">In `Program.Main` (`Program.cs`), a named <xref:System.Net.Http.HttpClient> (`HostIS.ServerAPI`) is configured to supply <xref:System.Net.Http.HttpClient> instances that include access tokens when making requests to the server API:</span></span>

```csharp
builder.Services.AddHttpClient("HostIS.ServerAPI", 
        client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddScoped(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("HostIS.ServerAPI"));
```

> [!NOTE]
> <span data-ttu-id="15659-174">Se si sta configurando un' Blazor WebAssembly app per l'uso di un' Identity istanza del server esistente che non fa parte di una soluzione ospitata Blazor , modificare la <xref:System.Net.Http.HttpClient> registrazione dell'indirizzo di base da <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> () nell'URL dell' `builder.HostEnvironment.BaseAddress` endpoint di autorizzazione dell'API dell'app Server.</span><span class="sxs-lookup"><span data-stu-id="15659-174">If you're configuring a Blazor WebAssembly app to use an existing Identity Server instance that isn't part of a hosted Blazor solution, change the <xref:System.Net.Http.HttpClient> base address registration from <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`builder.HostEnvironment.BaseAddress`) to the server app's API authorization endpoint URL.</span></span>

### <a name="api-authorization-support"></a><span data-ttu-id="15659-175">Supporto dell'autorizzazione API</span><span class="sxs-lookup"><span data-stu-id="15659-175">API authorization support</span></span>

<span data-ttu-id="15659-176">Il supporto per l'autenticazione degli utenti è collegato al contenitore del servizio mediante il metodo di estensione fornito nel [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) pacchetto.</span><span class="sxs-lookup"><span data-stu-id="15659-176">The support for authenticating users is plugged into the service container by the extension method provided inside the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package.</span></span> <span data-ttu-id="15659-177">Questo metodo configura i servizi richiesti dall'app per interagire con il sistema di autorizzazione esistente.</span><span class="sxs-lookup"><span data-stu-id="15659-177">This method sets up the services required by the app to interact with the existing authorization system.</span></span>

```csharp
builder.Services.AddApiAuthorization();
```

<span data-ttu-id="15659-178">Per impostazione predefinita, la configurazione per l'app viene caricata per convenzione da `_configuration/{client-id}` .</span><span class="sxs-lookup"><span data-stu-id="15659-178">By default, configuration for the app is loaded by convention from `_configuration/{client-id}`.</span></span> <span data-ttu-id="15659-179">Per convenzione, l'ID client è impostato sul nome dell'assembly dell'app.</span><span class="sxs-lookup"><span data-stu-id="15659-179">By convention, the client ID is set to the app's assembly name.</span></span> <span data-ttu-id="15659-180">Questo URL può essere modificato in modo che punti a un endpoint separato chiamando l'overload con options.</span><span class="sxs-lookup"><span data-stu-id="15659-180">This URL can be changed to point to a separate endpoint by calling the overload with options.</span></span>

### <a name="imports-file"></a><span data-ttu-id="15659-181">Importa file</span><span class="sxs-lookup"><span data-stu-id="15659-181">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="15659-182">Pagina di indice</span><span class="sxs-lookup"><span data-stu-id="15659-182">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

### <a name="app-component"></a><span data-ttu-id="15659-183">Componente app</span><span class="sxs-lookup"><span data-stu-id="15659-183">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="15659-184">Componente RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="15659-184">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="15659-185">Componente LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="15659-185">LoginDisplay component</span></span>

<span data-ttu-id="15659-186">Il `LoginDisplay` componente ( `Shared/LoginDisplay.razor` ) viene sottoposto a rendering nel `MainLayout` componente ( `Shared/MainLayout.razor` ) e gestisce i comportamenti seguenti:</span><span class="sxs-lookup"><span data-stu-id="15659-186">The `LoginDisplay` component (`Shared/LoginDisplay.razor`) is rendered in the `MainLayout` component (`Shared/MainLayout.razor`) and manages the following behaviors:</span></span>

* <span data-ttu-id="15659-187">Per utenti autenticati:</span><span class="sxs-lookup"><span data-stu-id="15659-187">For authenticated users:</span></span>
  * <span data-ttu-id="15659-188">Visualizza il nome utente corrente.</span><span class="sxs-lookup"><span data-stu-id="15659-188">Displays the current user name.</span></span>
  * <span data-ttu-id="15659-189">Offre un collegamento alla pagina del profilo utente in ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="15659-189">Offers a link to the user profile page in ASP.NET Core Identity.</span></span>
  * <span data-ttu-id="15659-190">Offre un pulsante per disconnettersi dall'app.</span><span class="sxs-lookup"><span data-stu-id="15659-190">Offers a button to log out of the app.</span></span>
* <span data-ttu-id="15659-191">Per utenti anonimi:</span><span class="sxs-lookup"><span data-stu-id="15659-191">For anonymous users:</span></span>
  * <span data-ttu-id="15659-192">Offre la possibilità di effettuare la registrazione.</span><span class="sxs-lookup"><span data-stu-id="15659-192">Offers the option to register.</span></span>
  * <span data-ttu-id="15659-193">Offre la possibilità di effettuare l'accesso.</span><span class="sxs-lookup"><span data-stu-id="15659-193">Offers the option to log in.</span></span>

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

### <a name="authentication-component"></a><span data-ttu-id="15659-194">Componente di autenticazione</span><span class="sxs-lookup"><span data-stu-id="15659-194">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="15659-195">Componente FetchData</span><span class="sxs-lookup"><span data-stu-id="15659-195">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="15659-196">Eseguire l'app</span><span class="sxs-lookup"><span data-stu-id="15659-196">Run the app</span></span>

<span data-ttu-id="15659-197">Eseguire l'app dal progetto server.</span><span class="sxs-lookup"><span data-stu-id="15659-197">Run the app from the Server project.</span></span> <span data-ttu-id="15659-198">Quando si usa Visual Studio, eseguire una delle operazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="15659-198">When using Visual Studio, either:</span></span>

* <span data-ttu-id="15659-199">Impostare l'elenco a discesa **progetti di avvio** nella barra degli strumenti sull'app per le *API server* e selezionare il pulsante **Esegui** .</span><span class="sxs-lookup"><span data-stu-id="15659-199">Set the **Startup Projects** drop down list in the toolbar to the *Server API app* and select the **Run** button.</span></span>
* <span data-ttu-id="15659-200">Selezionare il progetto server in **Esplora soluzioni** , quindi fare clic sul pulsante **Esegui** nella barra degli strumenti o avviare l'app dal menu **debug** .</span><span class="sxs-lookup"><span data-stu-id="15659-200">Select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

## <a name="name-and-role-claim-with-api-authorization"></a><span data-ttu-id="15659-201">Nome e attestazione di ruolo con autorizzazione API</span><span class="sxs-lookup"><span data-stu-id="15659-201">Name and role claim with API authorization</span></span>

### <a name="custom-user-factory"></a><span data-ttu-id="15659-202">Factory utente personalizzata</span><span class="sxs-lookup"><span data-stu-id="15659-202">Custom user factory</span></span>

<span data-ttu-id="15659-203">Nell'app client creare una factory utente personalizzata.</span><span class="sxs-lookup"><span data-stu-id="15659-203">In the Client app, create a custom user factory.</span></span> <span data-ttu-id="15659-204">Identity Il server invia più ruoli come matrice JSON in un'unica `role` attestazione.</span><span class="sxs-lookup"><span data-stu-id="15659-204">Identity Server sends multiple roles as a JSON array in a single `role` claim.</span></span> <span data-ttu-id="15659-205">Un singolo ruolo viene inviato come valore stringa nell'attestazione.</span><span class="sxs-lookup"><span data-stu-id="15659-205">A single role is sent as a string value in the claim.</span></span> <span data-ttu-id="15659-206">La factory crea una singola `role` attestazione per ogni ruolo dell'utente.</span><span class="sxs-lookup"><span data-stu-id="15659-206">The factory creates an individual `role` claim for each of the user's roles.</span></span>

<span data-ttu-id="15659-207">`CustomUserFactory.cs`:</span><span class="sxs-lookup"><span data-stu-id="15659-207">`CustomUserFactory.cs`:</span></span>

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
            var roleClaims = identity.FindAll(identity.RoleClaimType).ToArray();

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

<span data-ttu-id="15659-208">Nell'app client registrare la factory in `Program.Main` ( `Program.cs` ):</span><span class="sxs-lookup"><span data-stu-id="15659-208">In the Client app, register the factory in `Program.Main` (`Program.cs`):</span></span>

```csharp
builder.Services.AddApiAuthorization()
    .AddAccountClaimsPrincipalFactory<CustomUserFactory>();
```

<span data-ttu-id="15659-209">Nell'app Server, chiamare <xref:Microsoft.AspNetCore.Identity.IdentityBuilder.AddRoles*> sul Identity Generatore, che aggiunge i servizi correlati ai ruoli:</span><span class="sxs-lookup"><span data-stu-id="15659-209">In the Server app, call <xref:Microsoft.AspNetCore.Identity.IdentityBuilder.AddRoles*> on the Identity builder, which adds role-related services:</span></span>

```csharp
using Microsoft.AspNetCore.Identity;

...

services.AddDefaultIdentity<ApplicationUser>(options => 
    options.SignIn.RequireConfirmedAccount = true)
    .AddRoles<IdentityRole>()
    .AddEntityFrameworkStores<ApplicationDbContext>();
```

### <a name="configure-no-locidentity-server"></a><span data-ttu-id="15659-210">Configura Identity Server</span><span class="sxs-lookup"><span data-stu-id="15659-210">Configure Identity Server</span></span>

<span data-ttu-id="15659-211">Usare **uno** degli approcci seguenti:</span><span class="sxs-lookup"><span data-stu-id="15659-211">Use **one** of the following approaches:</span></span>

* [<span data-ttu-id="15659-212">Opzioni di autorizzazione API</span><span class="sxs-lookup"><span data-stu-id="15659-212">API authorization options</span></span>](#api-authorization-options)
* [<span data-ttu-id="15659-213">Servizio profili</span><span class="sxs-lookup"><span data-stu-id="15659-213">Profile Service</span></span>](#profile-service)

#### <a name="api-authorization-options"></a><span data-ttu-id="15659-214">Opzioni di autorizzazione API</span><span class="sxs-lookup"><span data-stu-id="15659-214">API authorization options</span></span>

<span data-ttu-id="15659-215">Nell'app Server:</span><span class="sxs-lookup"><span data-stu-id="15659-215">In the Server app:</span></span>

* <span data-ttu-id="15659-216">Configurare Identity il server in modo che inserisca le `name` `role` attestazioni e nel token ID e nel token di accesso.</span><span class="sxs-lookup"><span data-stu-id="15659-216">Configure Identity Server to put the `name` and `role` claims into the ID token and access token.</span></span>
* <span data-ttu-id="15659-217">Impedire il mapping predefinito per i ruoli nel gestore del token JWT.</span><span class="sxs-lookup"><span data-stu-id="15659-217">Prevent the default mapping for roles in the JWT token handler.</span></span>

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

#### <a name="profile-service"></a><span data-ttu-id="15659-218">Servizio profili</span><span class="sxs-lookup"><span data-stu-id="15659-218">Profile Service</span></span>

<span data-ttu-id="15659-219">Nell'app Server creare un' `ProfileService` implementazione.</span><span class="sxs-lookup"><span data-stu-id="15659-219">In the Server app, create a `ProfileService` implementation.</span></span>

<span data-ttu-id="15659-220">`ProfileService.cs`:</span><span class="sxs-lookup"><span data-stu-id="15659-220">`ProfileService.cs`:</span></span>

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

<span data-ttu-id="15659-221">Nell'app server registrare il servizio profili in `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="15659-221">In the Server app, register the Profile Service in `Startup.ConfigureServices`:</span></span>

```csharp
using IdentityServer4.Services;

...

services.AddTransient<IProfileService, ProfileService>();
```

### <a name="use-authorization-mechanisms"></a><span data-ttu-id="15659-222">Usare i meccanismi di autorizzazione</span><span class="sxs-lookup"><span data-stu-id="15659-222">Use authorization mechanisms</span></span>

<span data-ttu-id="15659-223">Nell'app client gli approcci di autorizzazione componenti sono funzionali a questo punto.</span><span class="sxs-lookup"><span data-stu-id="15659-223">In the Client app, component authorization approaches are functional at this point.</span></span> <span data-ttu-id="15659-224">Qualsiasi meccanismo di autorizzazione nei componenti può utilizzare un ruolo per autorizzare l'utente:</span><span class="sxs-lookup"><span data-stu-id="15659-224">Any of the authorization mechanisms in components can use a role to authorize the user:</span></span>

* <span data-ttu-id="15659-225">[ `AuthorizeView` Component](xref:blazor/security/index#authorizeview-component) (esempio: `<AuthorizeView Roles="admin">` )</span><span class="sxs-lookup"><span data-stu-id="15659-225">[`AuthorizeView` component](xref:blazor/security/index#authorizeview-component) (Example: `<AuthorizeView Roles="admin">`)</span></span>
* <span data-ttu-id="15659-226">[ `[Authorize]` direttiva attribute](xref:blazor/security/index#authorize-attribute) ( <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> ) (esempio: `@attribute [Authorize(Roles = "admin")]` )</span><span class="sxs-lookup"><span data-stu-id="15659-226">[`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) (Example: `@attribute [Authorize(Roles = "admin")]`)</span></span>
* <span data-ttu-id="15659-227">[Logica procedurale](xref:blazor/security/index#procedural-logic) (esempio: `if (user.IsInRole("admin")) { ... }` )</span><span class="sxs-lookup"><span data-stu-id="15659-227">[Procedural logic](xref:blazor/security/index#procedural-logic) (Example: `if (user.IsInRole("admin")) { ... }`)</span></span>

  <span data-ttu-id="15659-228">Sono supportati più test di ruolo:</span><span class="sxs-lookup"><span data-stu-id="15659-228">Multiple role tests are supported:</span></span>

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  {
      ...
  }
  ```

<span data-ttu-id="15659-229">`User.Identity.Name` viene popolato nell'app client con il nome utente dell'utente, che corrisponde in genere all'indirizzo di posta elettronica di accesso.</span><span class="sxs-lookup"><span data-stu-id="15659-229">`User.Identity.Name` is populated in the Client app with the user's user name, which is usually their sign-in email address.</span></span>

[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]

## <a name="host-in-azure-app-service-with-a-custom-domain"></a><span data-ttu-id="15659-230">Host in app Azure servizio con un dominio personalizzato</span><span class="sxs-lookup"><span data-stu-id="15659-230">Host in Azure App Service with a custom domain</span></span>

<span data-ttu-id="15659-231">Le linee guida seguenti illustrano come distribuire un' Blazor WebAssembly app ospitata con Identity Server per [app Azure servizio](https://azure.microsoft.com/services/app-service/) con un dominio personalizzato.</span><span class="sxs-lookup"><span data-stu-id="15659-231">The following guidance explains how to deploy a hosted Blazor WebAssembly app with Identity Server to [Azure App Service](https://azure.microsoft.com/services/app-service/) with a custom domain.</span></span>

<span data-ttu-id="15659-232">Per questo scenario di hosting, **non** usare lo stesso certificato per la [ Identity chiave di firma del token del server](https://docs.identityserver.io/en/latest/topics/crypto.html#token-signing-and-validation) e la comunicazione protetta HTTPS del sito con i browser:</span><span class="sxs-lookup"><span data-stu-id="15659-232">For this hosting scenario, do **not** use the same certificate for [Identity Server's token signing key](https://docs.identityserver.io/en/latest/topics/crypto.html#token-signing-and-validation) and the site's HTTPS secure communication with browsers:</span></span>

* <span data-ttu-id="15659-233">L'uso di certificati diversi per questi due requisiti è una procedura di sicurezza efficace, in quanto isola le chiavi private per ogni scopo.</span><span class="sxs-lookup"><span data-stu-id="15659-233">Using different certificates for these two requirements is a good security practice because it isolates private keys for each purpose.</span></span>
* <span data-ttu-id="15659-234">I certificati TLS per la comunicazione con i browser vengono gestiti in modo indipendente senza influire sulla Identity firma dei token del server.</span><span class="sxs-lookup"><span data-stu-id="15659-234">TLS certificates for communication with browsers is managed independently without affecting Identity Server's token signing.</span></span>
* <span data-ttu-id="15659-235">Quando [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) fornisce un certificato a un'app del servizio app per l'associazione di dominio personalizzata, Identity il server non può ottenere lo stesso certificato da Azure Key Vault per la firma di token.</span><span class="sxs-lookup"><span data-stu-id="15659-235">When [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) supplies a certificate to an App Service app for custom domain binding, Identity Server can't obtain the same certificate from Azure Key Vault for token signing.</span></span> <span data-ttu-id="15659-236">Sebbene Identity sia possibile configurare il server per l'utilizzo dello stesso certificato TLS da un percorso fisico, l'inserimento dei certificati di sicurezza nel controllo del codice sorgente non è una **procedura consigliata e deve essere evitato nella maggior parte degli scenari**.</span><span class="sxs-lookup"><span data-stu-id="15659-236">Although configuring Identity Server to use the same TLS certificate from a physical path is possible, placing security certificates into source control is a **poor practice and should be avoided in most scenarios**.</span></span>

<span data-ttu-id="15659-237">Nelle linee guida seguenti viene creato un certificato autofirmato in Azure Key Vault esclusivamente per la Identity firma di token del server.</span><span class="sxs-lookup"><span data-stu-id="15659-237">In the following guidance, a self-signed certificate is created in Azure Key Vault solely for Identity Server token signing.</span></span> <span data-ttu-id="15659-238">La Identity configurazione del server usa il certificato dell'insieme di credenziali delle chiavi tramite l' `My`  >  `CurrentUser` archivio certificati dell'app.</span><span class="sxs-lookup"><span data-stu-id="15659-238">The Identity Server configuration uses the key vault certificate via the app's `My` > `CurrentUser` certificate store.</span></span> <span data-ttu-id="15659-239">Altri certificati utilizzati per il traffico HTTPS con domini personalizzati vengono creati e configurati separatamente dal Identity certificato di firma del server.</span><span class="sxs-lookup"><span data-stu-id="15659-239">Other certificates used for HTTPS traffic with custom domains are created and configured separately from the Identity Server signing certificate.</span></span>

<span data-ttu-id="15659-240">Per configurare un'app, app Azure servizio e Azure Key Vault per ospitare con un dominio personalizzato e HTTPS:</span><span class="sxs-lookup"><span data-stu-id="15659-240">To configure an app, Azure App Service, and Azure Key Vault to host with a custom domain and HTTPS:</span></span>

1. <span data-ttu-id="15659-241">Creare un [piano di servizio app](/azure/app-service/overview-hosting-plans) con un livello di piano `Basic B1` o superiore.</span><span class="sxs-lookup"><span data-stu-id="15659-241">Create an [App Service plan](/azure/app-service/overview-hosting-plans) with an plan level of `Basic B1` or higher.</span></span> <span data-ttu-id="15659-242">Il servizio app richiede un `Basic B1` livello di servizio o superiore per l'uso di domini personalizzati.</span><span class="sxs-lookup"><span data-stu-id="15659-242">App Service requires a `Basic B1` or higher service tier to use custom domains.</span></span>
1. <span data-ttu-id="15659-243">Creare un certificato PFX per il protocollo HTTPS (Secure Browser Communication) del sito con un nome comune del nome di dominio completo (FQDN) del sito controllato dall'organizzazione (ad esempio, `www.contoso.com` ).</span><span class="sxs-lookup"><span data-stu-id="15659-243">Create a PFX certificate for the site's secure browser communication (HTTPS protocol) with a common name of the site's fully qualified domain name (FQDN) that your organization controls (for example, `www.contoso.com`).</span></span> <span data-ttu-id="15659-244">Creare il certificato con:</span><span class="sxs-lookup"><span data-stu-id="15659-244">Create the certificate with:</span></span>
   * <span data-ttu-id="15659-245">Usi chiave</span><span class="sxs-lookup"><span data-stu-id="15659-245">Key uses</span></span>
     * <span data-ttu-id="15659-246">Convalida della firma digitale ( `digitalSignature` )</span><span class="sxs-lookup"><span data-stu-id="15659-246">Digital signature validation (`digitalSignature`)</span></span>
     * <span data-ttu-id="15659-247">Crittografia chiave ( `keyEncipherment` )</span><span class="sxs-lookup"><span data-stu-id="15659-247">Key encipherment (`keyEncipherment`)</span></span>
   * <span data-ttu-id="15659-248">Usi chiave avanzata/estesa</span><span class="sxs-lookup"><span data-stu-id="15659-248">Enhanced/extended key uses</span></span>
     * <span data-ttu-id="15659-249">Autenticazione client (1.3.6.1.5.5.7.3.2)</span><span class="sxs-lookup"><span data-stu-id="15659-249">Client Authentication (1.3.6.1.5.5.7.3.2)</span></span>
     * <span data-ttu-id="15659-250">Autenticazione server (1.3.6.1.5.5.7.3.1)</span><span class="sxs-lookup"><span data-stu-id="15659-250">Server Authentication (1.3.6.1.5.5.7.3.1)</span></span>

   <span data-ttu-id="15659-251">Per creare il certificato, usare uno degli approcci seguenti o qualsiasi altro strumento o servizio online appropriato:</span><span class="sxs-lookup"><span data-stu-id="15659-251">To create the certificate, use one of the following approaches or any other suitable tool or online service:</span></span>

   * [<span data-ttu-id="15659-252">Insieme di credenziali chiave Azure</span><span class="sxs-lookup"><span data-stu-id="15659-252">Azure Key Vault</span></span>](/azure/key-vault/certificates/quick-create-portal#add-a-certificate-to-key-vault)
   * [<span data-ttu-id="15659-253">MakeCert per Windows</span><span class="sxs-lookup"><span data-stu-id="15659-253">MakeCert on Windows</span></span>](/windows/desktop/seccrypto/makecert)
   * [<span data-ttu-id="15659-254">OpenSSL</span><span class="sxs-lookup"><span data-stu-id="15659-254">OpenSSL</span></span>](https://www.openssl.org)

   <span data-ttu-id="15659-255">Prendere nota della password, che verrà usata in seguito per importare il certificato in Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="15659-255">Make note of the password, which is used later to import the certificate into Azure Key Vault.</span></span>

   <span data-ttu-id="15659-256">Per ulteriori informazioni sui certificati Azure Key Vault, vedere [Azure Key Vault: Certificates](/azure/key-vault/certificates/).</span><span class="sxs-lookup"><span data-stu-id="15659-256">For more information on Azure Key Vault certificates, see [Azure Key Vault: Certificates](/azure/key-vault/certificates/).</span></span>
1. <span data-ttu-id="15659-257">Creare una nuova Azure Key Vault o usare un insieme di credenziali delle chiavi esistente nella sottoscrizione di Azure.</span><span class="sxs-lookup"><span data-stu-id="15659-257">Create a new Azure Key Vault or use an existing key vault in your Azure subscription.</span></span>
1. <span data-ttu-id="15659-258">Nell'area **certificati** dell'insieme di credenziali delle chiavi importare il certificato del sito PFX.</span><span class="sxs-lookup"><span data-stu-id="15659-258">In the key vault's **Certificates** area, import the PFX site certificate.</span></span> <span data-ttu-id="15659-259">Registrare l'identificazione personale del certificato, che viene usato nella configurazione dell'app in un secondo momento.</span><span class="sxs-lookup"><span data-stu-id="15659-259">Record the certificate's thumbprint, which is used in the app's configuration later.</span></span>
1. <span data-ttu-id="15659-260">In Azure Key Vault generare un nuovo certificato autofirmato per la Identity firma del token del server.</span><span class="sxs-lookup"><span data-stu-id="15659-260">In Azure Key Vault, generate a new self-signed certificate for Identity Server token signing.</span></span> <span data-ttu-id="15659-261">Assegnare al certificato un **nome** e un **oggetto**del certificato.</span><span class="sxs-lookup"><span data-stu-id="15659-261">Give the certificate a **Certificate Name** and **Subject**.</span></span> <span data-ttu-id="15659-262">Il **soggetto** viene specificato come `CN={COMMON NAME}` , dove il `{COMMON NAME}` segnaposto è il nome comune del certificato.</span><span class="sxs-lookup"><span data-stu-id="15659-262">The **Subject** is specified as `CN={COMMON NAME}`, where the `{COMMON NAME}` placeholder is the certificate's common name.</span></span> <span data-ttu-id="15659-263">Il nome comune può essere qualsiasi stringa alfanumerica.</span><span class="sxs-lookup"><span data-stu-id="15659-263">The common name can be any alphanumeric string.</span></span> <span data-ttu-id="15659-264">Ad esempio, `CN=IdentityServerSigning` è un **oggetto**certificato valido.</span><span class="sxs-lookup"><span data-stu-id="15659-264">For example, `CN=IdentityServerSigning` is a valid certificate **Subject**.</span></span> <span data-ttu-id="15659-265">Usare le impostazioni di **configurazione avanzata dei criteri** predefiniti.</span><span class="sxs-lookup"><span data-stu-id="15659-265">Use the default **Advanced Policy Configuration** settings.</span></span> <span data-ttu-id="15659-266">Registrare l'identificazione personale del certificato, che viene usato nella configurazione dell'app in un secondo momento.</span><span class="sxs-lookup"><span data-stu-id="15659-266">Record the certificate's thumbprint, which is used in the app's configuration later.</span></span>
1. <span data-ttu-id="15659-267">Passare a app Azure servizio nel portale di Azure e creare un nuovo servizio app con la configurazione seguente:</span><span class="sxs-lookup"><span data-stu-id="15659-267">Navigate to Azure App Service in the Azure portal and create a new App Service with the following configuration:</span></span>
   * <span data-ttu-id="15659-268">**Pubblica** impostare su `Code` .</span><span class="sxs-lookup"><span data-stu-id="15659-268">**Publish** set to `Code`.</span></span>
   * <span data-ttu-id="15659-269">**Stack di runtime** impostato sul runtime dell'app.</span><span class="sxs-lookup"><span data-stu-id="15659-269">**Runtime stack** set to the app's runtime.</span></span>
   * <span data-ttu-id="15659-270">Per **SKU e dimensioni**, verificare che il livello di servizio app sia `Basic B1` o superiore.</span><span class="sxs-lookup"><span data-stu-id="15659-270">For **Sku and size**, confirm that the App Service tier is `Basic B1` or higher.</span></span>  <span data-ttu-id="15659-271">Il servizio app richiede un `Basic B1` livello di servizio o superiore per l'uso di domini personalizzati.</span><span class="sxs-lookup"><span data-stu-id="15659-271">App Service requires a `Basic B1` or higher service tier to use custom domains.</span></span>
1. <span data-ttu-id="15659-272">Dopo che Azure ha creato il servizio app, aprire la **configurazione** dell'app e aggiungere una nuova impostazione dell'applicazione specificando le identificazioni personali del certificato registrate in precedenza.</span><span class="sxs-lookup"><span data-stu-id="15659-272">After Azure creates the App Service, open the app's **Configuration** and add a new application setting specifying the certificate thumbprints recorded earlier.</span></span> <span data-ttu-id="15659-273">La chiave dell'impostazione dell'app è `WEBSITE_LOAD_CERTIFICATES` .</span><span class="sxs-lookup"><span data-stu-id="15659-273">The app setting key is `WEBSITE_LOAD_CERTIFICATES`.</span></span> <span data-ttu-id="15659-274">Separare le identificazioni personali del certificato nel valore dell'impostazione dell'app con una virgola, come illustrato nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="15659-274">Separate the certificate thumbprints in the app setting value with a comma, as the following example shows:</span></span>
   * <span data-ttu-id="15659-275">Chiave: `WEBSITE_LOAD_CERTIFICATES`</span><span class="sxs-lookup"><span data-stu-id="15659-275">Key: `WEBSITE_LOAD_CERTIFICATES`</span></span>
   * <span data-ttu-id="15659-276">Valore: `57443A552A46DB...D55E28D412B943565,29F43A772CB6AF...1D04F0C67F85FB0B1`</span><span class="sxs-lookup"><span data-stu-id="15659-276">Value: `57443A552A46DB...D55E28D412B943565,29F43A772CB6AF...1D04F0C67F85FB0B1`</span></span>

   <span data-ttu-id="15659-277">Nel portale di Azure il salvataggio delle impostazioni dell'app è un processo in due passaggi: salvare l' `WEBSITE_LOAD_CERTIFICATES` impostazione chiave-valore, quindi selezionare il pulsante **Salva** nella parte superiore del pannello.</span><span class="sxs-lookup"><span data-stu-id="15659-277">In the Azure portal, saving app settings is a two-step process: Save the `WEBSITE_LOAD_CERTIFICATES` key-value setting, then select the **Save** button at the top of the blade.</span></span>
1. <span data-ttu-id="15659-278">Selezionare le **Impostazioni TLS/SSL**dell'app.</span><span class="sxs-lookup"><span data-stu-id="15659-278">Select the app's **TLS/SSL settings**.</span></span> <span data-ttu-id="15659-279">Selezionare **certificati di chiave privata (con estensione pfx)**.</span><span class="sxs-lookup"><span data-stu-id="15659-279">Select **Private Key Certificates (.pfx)**.</span></span> <span data-ttu-id="15659-280">Usare il processo **Import Key Vault certificate** due volte per importare il certificato del sito per la comunicazione HTTPS e il certificato di firma del token del server autofirmato del sito Identity .</span><span class="sxs-lookup"><span data-stu-id="15659-280">Use the **Import Key Vault Certificate** process twice to import both the site's certificate for HTTPS communication and the site's self-signed Identity Server token signing certificate.</span></span>
1. <span data-ttu-id="15659-281">Passare al pannello **domini personalizzati** .</span><span class="sxs-lookup"><span data-stu-id="15659-281">Navigate to the **Custom domains** blade.</span></span> <span data-ttu-id="15659-282">Nel sito Web del registrar di dominio usare l' **indirizzo IP** e l' **ID di verifica del dominio personalizzato** per configurare il dominio.</span><span class="sxs-lookup"><span data-stu-id="15659-282">At your domain registrar's website, use the **IP address** and **Custom Domain Verification ID** to configure the domain.</span></span> <span data-ttu-id="15659-283">Una configurazione di dominio tipica include:</span><span class="sxs-lookup"><span data-stu-id="15659-283">A typical domain configuration includes:</span></span>
   * <span data-ttu-id="15659-284">Un **record a** con un **host** di `@` e un valore dell'indirizzo IP del portale di Azure.</span><span class="sxs-lookup"><span data-stu-id="15659-284">An **A Record** with a **Host** of `@` and a value of the IP address from the Azure portal.</span></span>
   * <span data-ttu-id="15659-285">Un **record TXT** con un **host** di `asuid` e il valore dell'ID di verifica generato da Azure e fornito dal portale di Azure.</span><span class="sxs-lookup"><span data-stu-id="15659-285">A **TXT Record** with a **Host** of `asuid` and the value of the verification ID generated by Azure and provided by the Azure portal.</span></span>

   <span data-ttu-id="15659-286">Assicurarsi di salvare correttamente le modifiche nel sito Web del registrar di dominio.</span><span class="sxs-lookup"><span data-stu-id="15659-286">Make sure that you save the changes at your domain registrar's website correctly.</span></span> <span data-ttu-id="15659-287">Per alcuni siti Web del registrar è necessario un processo in due passaggi per salvare i record di dominio: uno o più record vengono salvati singolarmente seguito dall'aggiornamento della registrazione del dominio con un pulsante separato.</span><span class="sxs-lookup"><span data-stu-id="15659-287">Some registrar websites require a two-step process to save domain records: One or more records are saved individually followed by updating the domain's registration with a separate button.</span></span>
1. <span data-ttu-id="15659-288">Tornare al pannello **domini personalizzati** nel portale di Azure.</span><span class="sxs-lookup"><span data-stu-id="15659-288">Return to the **Custom domains** blade in the Azure portal.</span></span> <span data-ttu-id="15659-289">Selezionare **Aggiungi dominio personalizzato**.</span><span class="sxs-lookup"><span data-stu-id="15659-289">Select **Add custom domain**.</span></span> <span data-ttu-id="15659-290">Selezionare l'opzione **A record** .</span><span class="sxs-lookup"><span data-stu-id="15659-290">Select the **A Record** option.</span></span> <span data-ttu-id="15659-291">Specificare il dominio e selezionare **convalida**.</span><span class="sxs-lookup"><span data-stu-id="15659-291">Provide the domain and select **Validate**.</span></span> <span data-ttu-id="15659-292">Se i record di dominio sono corretti e propagati in Internet, il portale consente di selezionare il pulsante **Aggiungi dominio personalizzato** .</span><span class="sxs-lookup"><span data-stu-id="15659-292">If the domain records are correct and propagated across the Internet, the portal allows you to select the **Add custom domain** button.</span></span>

   <span data-ttu-id="15659-293">Potrebbero essere necessari alcuni giorni per la propagazione delle modifiche di registrazione del dominio tra i server dei nomi di dominio Internet (DNS) dopo che sono state elaborate dal registrar.</span><span class="sxs-lookup"><span data-stu-id="15659-293">It can take a few days for domain registration changes to propagate across Internet domain name servers (DNS) after they're processed by your domain registrar.</span></span> <span data-ttu-id="15659-294">Se i record di dominio non vengono aggiornati entro tre giorni lavorativi, verificare che i record siano impostati correttamente con il registrar e contattare il supporto tecnico.</span><span class="sxs-lookup"><span data-stu-id="15659-294">If domain records aren't updated within three business days, confirm the records are correctly set with the domain registrar and contact their customer support.</span></span>
1. <span data-ttu-id="15659-295">Nel pannello **domini personalizzati** lo **stato SSL** per il dominio è contrassegnato `Not Secure` .</span><span class="sxs-lookup"><span data-stu-id="15659-295">In the **Custom domains** blade, the **SSL STATE** for the domain is marked `Not Secure`.</span></span> <span data-ttu-id="15659-296">Selezionare il collegamento **Aggiungi binding** .</span><span class="sxs-lookup"><span data-stu-id="15659-296">Select the **Add binding** link.</span></span> <span data-ttu-id="15659-297">Selezionare il certificato HTTPS del sito dall'insieme di credenziali delle chiavi per l'associazione del dominio personalizzato.</span><span class="sxs-lookup"><span data-stu-id="15659-297">Select the site HTTPS certificate from the key vault for the custom domain binding.</span></span>
1. <span data-ttu-id="15659-298">In Visual Studio aprire il file di impostazioni dell'app del progetto *Server* ( `appsettings.json` o `appsettings.Production.json` ).</span><span class="sxs-lookup"><span data-stu-id="15659-298">In Visual Studio, open the *Server* project's app settings file (`appsettings.json` or `appsettings.Production.json`).</span></span> <span data-ttu-id="15659-299">Nella Identity configurazione del server aggiungere la sezione seguente `Key` .</span><span class="sxs-lookup"><span data-stu-id="15659-299">In the Identity Server configuration, add the following `Key` section.</span></span> <span data-ttu-id="15659-300">Specificare il certificato autofirmato **soggetto** alla `Name` chiave.</span><span class="sxs-lookup"><span data-stu-id="15659-300">Specify the self-signed certificate **Subject** for the `Name` key.</span></span> <span data-ttu-id="15659-301">Nell'esempio seguente il nome comune del certificato assegnato nell'insieme di credenziali delle chiavi è `IdentityServerSigning` , che restituisce un **oggetto** `CN=IdentityServerSigning` :</span><span class="sxs-lookup"><span data-stu-id="15659-301">In the following example, the certificate's common name assigned in the key vault is `IdentityServerSigning`, which yields a **Subject** of `CN=IdentityServerSigning`:</span></span>

   ```json
   "IdentityServer": {

     ...

     "Key": {
       "Type": "Store",
       "StoreName": "My",
       "StoreLocation": "CurrentUser",
       "Name": "CN=IdentityServerSigning"
     }
   },
   ```

1. <span data-ttu-id="15659-302">In Visual Studio creare un [profilo di pubblicazione](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) del servizio app Azure per il progetto *Server* .</span><span class="sxs-lookup"><span data-stu-id="15659-302">In Visual Studio, create an Azure App Service [publish profile](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) for the *Server* project.</span></span> <span data-ttu-id="15659-303">Dalla barra dei menu selezionare: **Compila**  >  **pubblica**  >  **nuovo**  >  **Azure**  >  **servizio app Azure** di Azure (Windows o Linux).</span><span class="sxs-lookup"><span data-stu-id="15659-303">From the menu bar, select: **Build** > **Publish** > **New** > **Azure** > **Azure App Service** (Windows or Linux).</span></span> <span data-ttu-id="15659-304">Quando Visual Studio è connesso a una sottoscrizione di Azure, è possibile impostare la **visualizzazione** delle risorse di Azure in base al **tipo di risorsa**.</span><span class="sxs-lookup"><span data-stu-id="15659-304">When Visual Studio is connected to an Azure subscription, you can set the **View** of Azure resources by **Resource type**.</span></span> <span data-ttu-id="15659-305">Spostarsi all'interno dell'elenco di **app Web** per trovare il servizio app per l'app e selezionarlo.</span><span class="sxs-lookup"><span data-stu-id="15659-305">Navigate within the **Web App** list to find the App Service for the app and select it.</span></span> <span data-ttu-id="15659-306">Selezionare **Fine**.</span><span class="sxs-lookup"><span data-stu-id="15659-306">Select **Finish**.</span></span>
1. <span data-ttu-id="15659-307">Quando Visual Studio torna alla finestra di **pubblicazione** , le dipendenze del servizio Key vault e SQL Server database vengono rilevate automaticamente.</span><span class="sxs-lookup"><span data-stu-id="15659-307">When Visual Studio returns to the **Publish** window, the key vault and SQL Server database service dependencies are automatically detected.</span></span>

   <span data-ttu-id="15659-308">Non sono necessarie modifiche di configurazione alle impostazioni predefinite per il servizio Key Vault.</span><span class="sxs-lookup"><span data-stu-id="15659-308">No configuration changes to the default settings are required for the key vault service.</span></span>

   <span data-ttu-id="15659-309">A scopo di test, il database [SQLite](https://www.sqlite.org/index.html) locale di un'app, configurato per impostazione predefinita dal Blazor modello, può essere distribuito con l'app senza alcuna configurazione aggiuntiva.</span><span class="sxs-lookup"><span data-stu-id="15659-309">For testing purposes, an app's local [SQLite](https://www.sqlite.org/index.html) database, which is configured by default by the Blazor template, can be deployed with the app without additional configuration.</span></span> <span data-ttu-id="15659-310">La configurazione di un database diverso per Identity il server in produzione esula dall'ambito di questo articolo.</span><span class="sxs-lookup"><span data-stu-id="15659-310">Configuring a different database for Identity Server in production is beyond the scope of this article.</span></span> <span data-ttu-id="15659-311">Per ulteriori informazioni, vedere le risorse di database nei set di documentazione seguenti:</span><span class="sxs-lookup"><span data-stu-id="15659-311">For more information, see the database resources in the following documentation sets:</span></span>
   * [<span data-ttu-id="15659-312">Servizio app</span><span class="sxs-lookup"><span data-stu-id="15659-312">App Service</span></span>](/azure/app-service/)
   * [<span data-ttu-id="15659-313">Identity Server</span><span class="sxs-lookup"><span data-stu-id="15659-313">Identity Server</span></span>](https://identityserver4.readthedocs.io/en/latest/)

1. <span data-ttu-id="15659-314">Selezionare il collegamento **modifica** sotto il nome del profilo di distribuzione nella parte superiore della finestra.</span><span class="sxs-lookup"><span data-stu-id="15659-314">Select the **Edit** link under the deployment profile name at the top of the window.</span></span> <span data-ttu-id="15659-315">Modificare l'URL di destinazione sull'URL del dominio personalizzato del sito, ad esempio `https://www.contoso.com` .</span><span class="sxs-lookup"><span data-stu-id="15659-315">Change the destination URL to the site's custom domain URL (for example, `https://www.contoso.com`).</span></span> <span data-ttu-id="15659-316">Salvare le impostazioni.</span><span class="sxs-lookup"><span data-stu-id="15659-316">Save the settings.</span></span>
1. <span data-ttu-id="15659-317">Pubblicare l'app.</span><span class="sxs-lookup"><span data-stu-id="15659-317">Publish the app.</span></span> <span data-ttu-id="15659-318">Visual Studio apre una finestra del browser e richiede il sito nel dominio personalizzato.</span><span class="sxs-lookup"><span data-stu-id="15659-318">Visual Studio opens a browser window and requests the site at its custom domain.</span></span>

<span data-ttu-id="15659-319">La documentazione di Azure contiene ulteriori dettagli sull'uso di servizi di Azure e domini personalizzati con binding TLS nel servizio app, incluse informazioni sull'uso di record CNAME invece di record.</span><span class="sxs-lookup"><span data-stu-id="15659-319">The Azure documentation contains additional detail on using Azure services and custom domains with TLS binding in App Service, including information on using CNAME records instead of A records.</span></span> <span data-ttu-id="15659-320">Per altre informazioni, vedere le seguenti risorse:</span><span class="sxs-lookup"><span data-stu-id="15659-320">For more information, see the following resources:</span></span>

* [<span data-ttu-id="15659-321">Documentazione del servizio app</span><span class="sxs-lookup"><span data-stu-id="15659-321">App Service documentation</span></span>](/azure/app-service/)
* [<span data-ttu-id="15659-322">Esercitazione: Eseguire il mapping di un nome DNS personalizzato esistente al Servizio app di Azure</span><span class="sxs-lookup"><span data-stu-id="15659-322">Tutorial: Map an existing custom DNS name to Azure App Service</span></span>](/azure/app-service/app-service-web-tutorial-custom-domain)
* [<span data-ttu-id="15659-323">Proteggere un nome DNS personalizzato con un'associazione TLS/SSL nel Servizio app di Azure</span><span class="sxs-lookup"><span data-stu-id="15659-323">Secure a custom DNS name with a TLS/SSL binding in Azure App Service</span></span>](/azure/app-service/configure-ssl-bindings)
* [<span data-ttu-id="15659-324">Insieme di credenziali chiave Azure</span><span class="sxs-lookup"><span data-stu-id="15659-324">Azure Key Vault</span></span>](/azure/key-vault/)

<span data-ttu-id="15659-325">È consigliabile usare una nuova finestra del browser privata o in incognito per ogni esecuzione dei test dell'app dopo una modifica all'app, alla configurazione dell'app o ai servizi di Azure nella portale di Azure.</span><span class="sxs-lookup"><span data-stu-id="15659-325">We recommend using a new in-private or incognito browser window for each app test run after a change to the app, app configuration, or Azure services in the Azure portal.</span></span> <span data-ttu-id="15659-326">I residui cookie di un'esecuzione dei test precedente possono causare l'autenticazione o l'autorizzazione non riuscita durante il test del sito anche quando la configurazione del sito è corretta.</span><span class="sxs-lookup"><span data-stu-id="15659-326">Lingering cookies from a previous test run can result in failed authentication or authorization when testing the site even when the site's configuration is correct.</span></span> <span data-ttu-id="15659-327">Per ulteriori informazioni su come configurare Visual Studio per aprire una nuova finestra del browser in modalità privata o in incognito per ogni esecuzione dei test, vedere la sezione relativa ai [ Cookie dati del sito e](#cookies-and-site-data) di.</span><span class="sxs-lookup"><span data-stu-id="15659-327">For more information on how to configure Visual Studio to open a new in-private or incognito browser window for each test run, see the [Cookies and site data](#cookies-and-site-data) section.</span></span>

<span data-ttu-id="15659-328">Quando la configurazione del servizio app viene modificata nella portale di Azure, gli aggiornamenti sono in genere effettivi, ma non sono istantanei.</span><span class="sxs-lookup"><span data-stu-id="15659-328">When App Service configuration is changed in the Azure portal, the updates generally take effect quickly but aren't instant.</span></span> <span data-ttu-id="15659-329">In alcuni casi, è necessario attendere un breve periodo di tempo per il riavvio di un servizio app, in modo da rendere effettiva una modifica della configurazione.</span><span class="sxs-lookup"><span data-stu-id="15659-329">Sometimes, you must wait a short period for an App Service to restart in order for a configuration change to take effect.</span></span>

<span data-ttu-id="15659-330">Per risolvere un problema di caricamento del certificato, eseguire il comando seguente in una shell dei comandi di PowerShell portale di Azure [Kudu](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service) .</span><span class="sxs-lookup"><span data-stu-id="15659-330">If troubleshooting a certificate loading problem, execute the following command in an Azure portal [Kudu](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service) PowerShell command shell.</span></span> <span data-ttu-id="15659-331">Il comando fornisce un elenco di certificati a cui l'app può accedere dall' `My`  >  `CurrentUser` archivio certificati.</span><span class="sxs-lookup"><span data-stu-id="15659-331">The command provides a list of certificates that the app can access from the `My` > `CurrentUser` certificate store.</span></span> <span data-ttu-id="15659-332">L'output include gli oggetti certificato e le identificazioni personali utili durante il debug di un'app:</span><span class="sxs-lookup"><span data-stu-id="15659-332">The output includes certificate subjects and thumbprints useful when debugging an app:</span></span>

```powershell
Get-ChildItem -path Cert:\CurrentUser\My -Recurse | Format-List DnsNameList, Subject, Thumbprint, EnhancedKeyUsageList
```

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="15659-333">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="15659-333">Additional resources</span></span>

* [<span data-ttu-id="15659-334">Distribuzione nel servizio app Azure</span><span class="sxs-lookup"><span data-stu-id="15659-334">Deployment to Azure App Service</span></span>](xref:security/authentication/identity/spa#deploy-to-production)
* [<span data-ttu-id="15659-335">Importare un certificato da Key Vault (documentazione di Azure)</span><span class="sxs-lookup"><span data-stu-id="15659-335">Import a certificate from Key Vault (Azure documentation)</span></span>](/azure/app-service/configure-ssl-certificate#import-a-certificate-from-key-vault)
* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="15659-336">Richieste API Web non autenticate o non autorizzate in un'app con un client predefinito sicuro</span><span class="sxs-lookup"><span data-stu-id="15659-336">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
