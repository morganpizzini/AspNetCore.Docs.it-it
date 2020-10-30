---
title: "Eseguire la migrazione dell'autenticazione e :::no-loc(Identity)::: al ASP.NET Core 2,0"
author: scottaddie
description: "Questo articolo illustra i passaggi più comuni per la migrazione di ASP.NET Core l'autenticazione 1. x e :::no-loc(Identity)::: ASP.NET Core 2,0."
ms.author: scaddie
ms.date: 06/21/2019
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: migration/1x-to-2x/identity-2x
ms.openlocfilehash: cad7582670013661f5fcbfbebad923f0f092462e
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93057180"
---
# <a name="migrate-authentication-and-no-locidentity-to-aspnet-core-20"></a><span data-ttu-id="36c01-103">Eseguire la migrazione dell'autenticazione e :::no-loc(Identity)::: al ASP.NET Core 2,0</span><span class="sxs-lookup"><span data-stu-id="36c01-103">Migrate authentication and :::no-loc(Identity)::: to ASP.NET Core 2.0</span></span>

<span data-ttu-id="36c01-104">Di [Scott Addie](https://github.com/scottaddie) e [Hao Kung](https://github.com/HaoK)</span><span class="sxs-lookup"><span data-stu-id="36c01-104">By [Scott Addie](https://github.com/scottaddie) and [Hao Kung](https://github.com/HaoK)</span></span>

<span data-ttu-id="36c01-105">ASP.NET Core 2,0 dispone di un nuovo modello di autenticazione [:::no-loc(Identity):::](xref:security/authentication/identity) che semplifica la configurazione tramite i servizi.</span><span class="sxs-lookup"><span data-stu-id="36c01-105">ASP.NET Core 2.0 has a new model for authentication and [:::no-loc(Identity):::](xref:security/authentication/identity) that simplifies configuration by using services.</span></span> <span data-ttu-id="36c01-106">ASP.NET Core le applicazioni 1. x che usano l'autenticazione o :::no-loc(Identity)::: possono essere aggiornate per usare il nuovo modello, come descritto di seguito.</span><span class="sxs-lookup"><span data-stu-id="36c01-106">ASP.NET Core 1.x applications that use authentication or :::no-loc(Identity)::: can be updated to use the new model as outlined below.</span></span>

## <a name="update-namespaces"></a><span data-ttu-id="36c01-107">Aggiornare gli spazi dei nomi</span><span class="sxs-lookup"><span data-stu-id="36c01-107">Update namespaces</span></span>

<span data-ttu-id="36c01-108">In 1. x, le classi come `:::no-loc(Identity):::Role` e `:::no-loc(Identity):::User` sono state trovate nello `Microsoft.AspNetCore.:::no-loc(Identity):::.EntityFrameworkCore` spazio dei nomi.</span><span class="sxs-lookup"><span data-stu-id="36c01-108">In 1.x, classes such `:::no-loc(Identity):::Role` and `:::no-loc(Identity):::User` were found in the `Microsoft.AspNetCore.:::no-loc(Identity):::.EntityFrameworkCore` namespace.</span></span>

<span data-ttu-id="36c01-109">In 2,0 lo <xref:Microsoft.AspNetCore.:::no-loc(Identity):::> spazio dei nomi è diventato la nuova Home page per diverse classi di questo tipo.</span><span class="sxs-lookup"><span data-stu-id="36c01-109">In 2.0, the <xref:Microsoft.AspNetCore.:::no-loc(Identity):::> namespace became the new home for several of such classes.</span></span> <span data-ttu-id="36c01-110">Con il :::no-loc(Identity)::: codice predefinito, le classi interessate includono `ApplicationUser` e `Startup` .</span><span class="sxs-lookup"><span data-stu-id="36c01-110">With the default :::no-loc(Identity)::: code, affected classes include `ApplicationUser` and `Startup`.</span></span> <span data-ttu-id="36c01-111">Modificare le `using` istruzioni per risolvere i riferimenti interessati.</span><span class="sxs-lookup"><span data-stu-id="36c01-111">Adjust your `using` statements to resolve the affected references.</span></span>

<a name="auth-middleware"></a>

## <a name="authentication-middleware-and-services"></a><span data-ttu-id="36c01-112">Middleware e servizi di autenticazione</span><span class="sxs-lookup"><span data-stu-id="36c01-112">Authentication Middleware and services</span></span>

<span data-ttu-id="36c01-113">Nei progetti di 1. x, l'autenticazione viene configurata tramite middleware.</span><span class="sxs-lookup"><span data-stu-id="36c01-113">In 1.x projects, authentication is configured via middleware.</span></span> <span data-ttu-id="36c01-114">Viene richiamato un metodo middleware per ogni schema di autenticazione che si desidera supportare.</span><span class="sxs-lookup"><span data-stu-id="36c01-114">A middleware method is invoked for each authentication scheme you want to support.</span></span>

<span data-ttu-id="36c01-115">Nell'esempio 1. x seguente viene configurata l'autenticazione di Facebook con :::no-loc(Identity)::: in *Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="36c01-115">The following 1.x example configures Facebook authentication with :::no-loc(Identity)::: in *Startup.cs* :</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Add:::no-loc(Identity):::<ApplicationUser, :::no-loc(Identity):::Role>()
            .AddEntityFrameworkStores<ApplicationDbContext>();
}

public void Configure(IApplicationBuilder app, ILoggerFactory loggerfactory)
{
    app.Use:::no-loc(Identity):::();
    app.UseFacebookAuthentication(new FacebookOptions {
        AppId = Configuration["auth:facebook:appid"],
        AppSecret = Configuration["auth:facebook:appsecret"]
    });
}
```

<span data-ttu-id="36c01-116">Nei progetti 2,0, l'autenticazione viene configurata tramite i servizi.</span><span class="sxs-lookup"><span data-stu-id="36c01-116">In 2.0 projects, authentication is configured via services.</span></span> <span data-ttu-id="36c01-117">Ogni schema di autenticazione viene registrato nel `ConfigureServices` metodo di *Startup.cs* .</span><span class="sxs-lookup"><span data-stu-id="36c01-117">Each authentication scheme is registered in the `ConfigureServices` method of *Startup.cs* .</span></span> <span data-ttu-id="36c01-118">Il `Use:::no-loc(Identity):::` metodo viene sostituito con `UseAuthentication` .</span><span class="sxs-lookup"><span data-stu-id="36c01-118">The `Use:::no-loc(Identity):::` method is replaced with `UseAuthentication`.</span></span>

<span data-ttu-id="36c01-119">L'esempio 2,0 seguente configura l'autenticazione Facebook con :::no-loc(Identity)::: in *Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="36c01-119">The following 2.0 example configures Facebook authentication with :::no-loc(Identity)::: in *Startup.cs* :</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Add:::no-loc(Identity):::<ApplicationUser, :::no-loc(Identity):::Role>()
            .AddEntityFrameworkStores<ApplicationDbContext>();

    // If you want to tweak :::no-loc(Identity)::: :::no-loc(cookie):::s, they're no longer part of :::no-loc(Identity):::Options.
    services.ConfigureApplication:::no-loc(Cookie):::(options => options.LoginPath = "/Account/LogIn");
    services.AddAuthentication()
            .AddFacebook(options =>
            {
                options.AppId = Configuration["auth:facebook:appid"];
                options.AppSecret = Configuration["auth:facebook:appsecret"];
            });
}

public void Configure(IApplicationBuilder app, ILoggerFactory loggerfactory) {
    app.UseAuthentication();
}
```

<span data-ttu-id="36c01-120">Il `UseAuthentication` metodo aggiunge un singolo componente middleware di autenticazione, responsabile dell'autenticazione automatica e della gestione delle richieste di autenticazione remota.</span><span class="sxs-lookup"><span data-stu-id="36c01-120">The `UseAuthentication` method adds a single authentication middleware component, which is responsible for automatic authentication and the handling of remote authentication requests.</span></span> <span data-ttu-id="36c01-121">Sostituisce tutti i singoli componenti middleware con un singolo componente middleware comune.</span><span class="sxs-lookup"><span data-stu-id="36c01-121">It replaces all of the individual middleware components with a single, common middleware component.</span></span>

<span data-ttu-id="36c01-122">Di seguito sono riportate le istruzioni di migrazione 2,0 per ogni schema di autenticazione principale.</span><span class="sxs-lookup"><span data-stu-id="36c01-122">Below are 2.0 migration instructions for each major authentication scheme.</span></span>

### <a name="no-loccookie-based-authentication"></a><span data-ttu-id="36c01-123">:::no-loc(Cookie):::autenticazione basata su</span><span class="sxs-lookup"><span data-stu-id="36c01-123">:::no-loc(Cookie):::-based authentication</span></span>

<span data-ttu-id="36c01-124">Selezionare una delle due opzioni seguenti e apportare le modifiche necessarie in *Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="36c01-124">Select one of the two options below, and make the necessary changes in *Startup.cs* :</span></span>

1. <span data-ttu-id="36c01-125">USA :::no-loc(cookie)::: con :::no-loc(Identity):::</span><span class="sxs-lookup"><span data-stu-id="36c01-125">Use :::no-loc(cookie):::s with :::no-loc(Identity):::</span></span>
    - <span data-ttu-id="36c01-126">Sostituire `Use:::no-loc(Identity):::` con `UseAuthentication` nel `Configure` Metodo:</span><span class="sxs-lookup"><span data-stu-id="36c01-126">Replace `Use:::no-loc(Identity):::` with `UseAuthentication` in the `Configure` method:</span></span>

        ```csharp
        app.UseAuthentication();
        ```

    - <span data-ttu-id="36c01-127">Richiamare il `Add:::no-loc(Identity):::` metodo nel `ConfigureServices` metodo per aggiungere i :::no-loc(cookie)::: servizi di autenticazione.</span><span class="sxs-lookup"><span data-stu-id="36c01-127">Invoke the `Add:::no-loc(Identity):::` method in the `ConfigureServices` method to add the :::no-loc(cookie)::: authentication services.</span></span>
    - <span data-ttu-id="36c01-128">Facoltativamente, richiamare il `ConfigureApplication:::no-loc(Cookie):::` `ConfigureExternal:::no-loc(Cookie):::` metodo o nel `ConfigureServices` metodo per modificare le :::no-loc(Identity)::: :::no-loc(cookie)::: impostazioni.</span><span class="sxs-lookup"><span data-stu-id="36c01-128">Optionally, invoke the `ConfigureApplication:::no-loc(Cookie):::` or `ConfigureExternal:::no-loc(Cookie):::` method in the `ConfigureServices` method to tweak the :::no-loc(Identity)::: :::no-loc(cookie)::: settings.</span></span>

        ```csharp
        services.Add:::no-loc(Identity):::<ApplicationUser, :::no-loc(Identity):::Role>()
                .AddEntityFrameworkStores<ApplicationDbContext>()
                .AddDefaultTokenProviders();

        services.ConfigureApplication:::no-loc(Cookie):::(options => options.LoginPath = "/Account/LogIn");
        ```

2. <span data-ttu-id="36c01-129">USA :::no-loc(cookie)::: s senza :::no-loc(Identity):::</span><span class="sxs-lookup"><span data-stu-id="36c01-129">Use :::no-loc(cookie):::s without :::no-loc(Identity):::</span></span>
    - <span data-ttu-id="36c01-130">Sostituire la `Use:::no-loc(Cookie):::Authentication` chiamata al metodo nel `Configure` metodo con `UseAuthentication` :</span><span class="sxs-lookup"><span data-stu-id="36c01-130">Replace the `Use:::no-loc(Cookie):::Authentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

        ```csharp
        app.UseAuthentication();
        ```

    - <span data-ttu-id="36c01-131">Richiamare i `AddAuthentication` `Add:::no-loc(Cookie):::` metodi e nel `ConfigureServices` Metodo:</span><span class="sxs-lookup"><span data-stu-id="36c01-131">Invoke the `AddAuthentication` and `Add:::no-loc(Cookie):::` methods in the `ConfigureServices` method:</span></span>

        ```csharp
        // If you don't want the :::no-loc(cookie)::: to be automatically authenticated and assigned to HttpContext.User,
        // remove the :::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme parameter passed to AddAuthentication.
        services.AddAuthentication(:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme)
                .Add:::no-loc(Cookie):::(options =>
                {
                    options.LoginPath = "/Account/LogIn";
                    options.LogoutPath = "/Account/LogOff";
                });
        ```

### <a name="jwt-bearer-authentication"></a><span data-ttu-id="36c01-132">Autenticazione di JWT Bearer</span><span class="sxs-lookup"><span data-stu-id="36c01-132">JWT Bearer Authentication</span></span>

<span data-ttu-id="36c01-133">Apportare le modifiche seguenti in *Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="36c01-133">Make the following changes in *Startup.cs* :</span></span>
- <span data-ttu-id="36c01-134">Sostituire la `UseJwtBearerAuthentication` chiamata al metodo nel `Configure` metodo con `UseAuthentication` :</span><span class="sxs-lookup"><span data-stu-id="36c01-134">Replace the `UseJwtBearerAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="36c01-135">Richiamare il `AddJwtBearer` metodo nel `ConfigureServices` Metodo:</span><span class="sxs-lookup"><span data-stu-id="36c01-135">Invoke the `AddJwtBearer` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
            .AddJwtBearer(options =>
            {
                options.Audience = "http://localhost:5001/";
                options.Authority = "http://localhost:5000/";
            });
    ```

    <span data-ttu-id="36c01-136">Questo frammento di codice non usa :::no-loc(Identity)::: , quindi è necessario impostare lo schema predefinito passando `JwtBearerDefaults.AuthenticationScheme` al `AddAuthentication` metodo.</span><span class="sxs-lookup"><span data-stu-id="36c01-136">This code snippet doesn't use :::no-loc(Identity):::, so the default scheme should be set by passing `JwtBearerDefaults.AuthenticationScheme` to the `AddAuthentication` method.</span></span>

### <a name="openid-connect-oidc-authentication"></a><span data-ttu-id="36c01-137">Autenticazione OpenID Connect (OIDC)</span><span class="sxs-lookup"><span data-stu-id="36c01-137">OpenID Connect (OIDC) authentication</span></span>

<span data-ttu-id="36c01-138">Apportare le modifiche seguenti in *Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="36c01-138">Make the following changes in *Startup.cs* :</span></span>

- <span data-ttu-id="36c01-139">Sostituire la `UseOpenIdConnectAuthentication` chiamata al metodo nel `Configure` metodo con `UseAuthentication` :</span><span class="sxs-lookup"><span data-stu-id="36c01-139">Replace the `UseOpenIdConnectAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="36c01-140">Richiamare il `AddOpenIdConnect` metodo nel `ConfigureServices` Metodo:</span><span class="sxs-lookup"><span data-stu-id="36c01-140">Invoke the `AddOpenIdConnect` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication(options =>
    {
        options.DefaultScheme = :::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme;
        options.DefaultChallengeScheme = OpenIdConnectDefaults.AuthenticationScheme;
    })
    .Add:::no-loc(Cookie):::()
    .AddOpenIdConnect(options =>
    {
        options.Authority = Configuration["auth:oidc:authority"];
        options.ClientId = Configuration["auth:oidc:clientid"];
    });
    ```

- <span data-ttu-id="36c01-141">Sostituire la `PostLogoutRedirectUri` proprietà nell' `OpenIdConnectOptions` azione con `SignedOutRedirectUri` :</span><span class="sxs-lookup"><span data-stu-id="36c01-141">Replace the `PostLogoutRedirectUri` property in the `OpenIdConnectOptions` action with `SignedOutRedirectUri`:</span></span>

    ```csharp
    .AddOpenIdConnect(options =>
    {
        options.SignedOutRedirectUri = "https://contoso.com";
    });
    ```
    
### <a name="facebook-authentication"></a><span data-ttu-id="36c01-142">Autenticazione Facebook</span><span class="sxs-lookup"><span data-stu-id="36c01-142">Facebook authentication</span></span>

<span data-ttu-id="36c01-143">Apportare le modifiche seguenti in *Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="36c01-143">Make the following changes in *Startup.cs* :</span></span>
- <span data-ttu-id="36c01-144">Sostituire la `UseFacebookAuthentication` chiamata al metodo nel `Configure` metodo con `UseAuthentication` :</span><span class="sxs-lookup"><span data-stu-id="36c01-144">Replace the `UseFacebookAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="36c01-145">Richiamare il `AddFacebook` metodo nel `ConfigureServices` Metodo:</span><span class="sxs-lookup"><span data-stu-id="36c01-145">Invoke the `AddFacebook` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication()
            .AddFacebook(options =>
            {
                options.AppId = Configuration["auth:facebook:appid"];
                options.AppSecret = Configuration["auth:facebook:appsecret"];
            });
    ```

### <a name="google-authentication"></a><span data-ttu-id="36c01-146">Autenticazione Google</span><span class="sxs-lookup"><span data-stu-id="36c01-146">Google authentication</span></span>

<span data-ttu-id="36c01-147">Apportare le modifiche seguenti in *Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="36c01-147">Make the following changes in *Startup.cs* :</span></span>
- <span data-ttu-id="36c01-148">Sostituire la `UseGoogleAuthentication` chiamata al metodo nel `Configure` metodo con `UseAuthentication` :</span><span class="sxs-lookup"><span data-stu-id="36c01-148">Replace the `UseGoogleAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="36c01-149">Richiamare il `AddGoogle` metodo nel `ConfigureServices` Metodo:</span><span class="sxs-lookup"><span data-stu-id="36c01-149">Invoke the `AddGoogle` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication()
            .AddGoogle(options =>
            {
                options.ClientId = Configuration["auth:google:clientid"];
                options.ClientSecret = Configuration["auth:google:clientsecret"];
            });
    ```

### <a name="microsoft-account-authentication"></a><span data-ttu-id="36c01-150">Autenticazione di account Microsoft</span><span class="sxs-lookup"><span data-stu-id="36c01-150">Microsoft Account authentication</span></span>

<span data-ttu-id="36c01-151">Per ulteriori informazioni sull'autenticazione account Microsoft, vedere [questo problema di GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/14455).</span><span class="sxs-lookup"><span data-stu-id="36c01-151">For more information on Microsoft account authentication, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/14455).</span></span>

<span data-ttu-id="36c01-152">Apportare le modifiche seguenti in *Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="36c01-152">Make the following changes in *Startup.cs* :</span></span>
- <span data-ttu-id="36c01-153">Sostituire la `UseMicrosoftAccountAuthentication` chiamata al metodo nel `Configure` metodo con `UseAuthentication` :</span><span class="sxs-lookup"><span data-stu-id="36c01-153">Replace the `UseMicrosoftAccountAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="36c01-154">Richiamare il `AddMicrosoftAccount` metodo nel `ConfigureServices` Metodo:</span><span class="sxs-lookup"><span data-stu-id="36c01-154">Invoke the `AddMicrosoftAccount` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication()
            .AddMicrosoftAccount(options =>
            {
                options.ClientId = Configuration["auth:microsoft:clientid"];
                options.ClientSecret = Configuration["auth:microsoft:clientsecret"];
            });
    ```

### <a name="twitter-authentication"></a><span data-ttu-id="36c01-155">Autenticazione Twitter</span><span class="sxs-lookup"><span data-stu-id="36c01-155">Twitter authentication</span></span>

<span data-ttu-id="36c01-156">Apportare le modifiche seguenti in *Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="36c01-156">Make the following changes in *Startup.cs* :</span></span>
- <span data-ttu-id="36c01-157">Sostituire la `UseTwitterAuthentication` chiamata al metodo nel `Configure` metodo con `UseAuthentication` :</span><span class="sxs-lookup"><span data-stu-id="36c01-157">Replace the `UseTwitterAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="36c01-158">Richiamare il `AddTwitter` metodo nel `ConfigureServices` Metodo:</span><span class="sxs-lookup"><span data-stu-id="36c01-158">Invoke the `AddTwitter` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication()
            .AddTwitter(options =>
            {
                options.ConsumerKey = Configuration["auth:twitter:consumerkey"];
                options.ConsumerSecret = Configuration["auth:twitter:consumersecret"];
            });
    ```

### <a name="setting-default-authentication-schemes"></a><span data-ttu-id="36c01-159">Impostazione degli schemi di autenticazione predefiniti</span><span class="sxs-lookup"><span data-stu-id="36c01-159">Setting default authentication schemes</span></span>

<span data-ttu-id="36c01-160">In 1. x, le `AutomaticAuthenticate` `AutomaticChallenge` proprietà e della classe di base [AuthenticationOptions](/dotnet/api/Microsoft.AspNetCore.Builder.AuthenticationOptions?view=aspnetcore-1.1) sono state progettate per essere impostate su un solo schema di autenticazione.</span><span class="sxs-lookup"><span data-stu-id="36c01-160">In 1.x, the `AutomaticAuthenticate` and `AutomaticChallenge` properties of the [AuthenticationOptions](/dotnet/api/Microsoft.AspNetCore.Builder.AuthenticationOptions?view=aspnetcore-1.1) base class were intended to be set on a single authentication scheme.</span></span> <span data-ttu-id="36c01-161">Non esiste un modo efficace per applicare questa operazione.</span><span class="sxs-lookup"><span data-stu-id="36c01-161">There was no good way to enforce this.</span></span>

<span data-ttu-id="36c01-162">In 2,0 queste due proprietà sono state rimosse come proprietà nella singola `AuthenticationOptions` istanza.</span><span class="sxs-lookup"><span data-stu-id="36c01-162">In 2.0, these two properties have been removed as properties on the individual `AuthenticationOptions` instance.</span></span> <span data-ttu-id="36c01-163">Possono essere configurate nella `AddAuthentication` chiamata al metodo all'interno del `ConfigureServices` metodo di *Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="36c01-163">They can be configured in the `AddAuthentication` method call within the `ConfigureServices` method of *Startup.cs* :</span></span>

```csharp
services.AddAuthentication(:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme);
```

<span data-ttu-id="36c01-164">Nel frammento di codice precedente, lo schema predefinito è impostato su `:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme` (" :::no-loc(Cookie)::: s").</span><span class="sxs-lookup"><span data-stu-id="36c01-164">In the preceding code snippet, the default scheme is set to `:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme` (":::no-loc(Cookie):::s").</span></span>

<span data-ttu-id="36c01-165">In alternativa, usare una versione di overload del `AddAuthentication` metodo per impostare più di una proprietà.</span><span class="sxs-lookup"><span data-stu-id="36c01-165">Alternatively, use an overloaded version of the `AddAuthentication` method to set more than one property.</span></span> <span data-ttu-id="36c01-166">Nell'esempio di metodo di overload seguente, lo schema predefinito è impostato su `:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme` .</span><span class="sxs-lookup"><span data-stu-id="36c01-166">In the following overloaded method example, the default scheme is set to `:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme`.</span></span> <span data-ttu-id="36c01-167">In alternativa, lo schema di autenticazione può essere specificato all'interno dei singoli `[Authorize]` attributi o dei criteri di autorizzazione.</span><span class="sxs-lookup"><span data-stu-id="36c01-167">The authentication scheme may alternatively be specified within your individual `[Authorize]` attributes or authorization policies.</span></span>

```csharp
services.AddAuthentication(options =>
{
    options.DefaultScheme = :::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme;
    options.DefaultChallengeScheme = OpenIdConnectDefaults.AuthenticationScheme;
});
```

<span data-ttu-id="36c01-168">Definire uno schema predefinito in 2,0 se si verifica una delle condizioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="36c01-168">Define a default scheme in 2.0 if one of the following conditions is true:</span></span>
- <span data-ttu-id="36c01-169">Si vuole che l'utente sia connesso automaticamente</span><span class="sxs-lookup"><span data-stu-id="36c01-169">You want the user to be automatically signed in</span></span>
- <span data-ttu-id="36c01-170">Usare l' `[Authorize]` attributo o i criteri di autorizzazione senza specificare gli schemi</span><span class="sxs-lookup"><span data-stu-id="36c01-170">You use the `[Authorize]` attribute or authorization policies without specifying schemes</span></span>

<span data-ttu-id="36c01-171">Un'eccezione a questa regola è rappresentata dal `Add:::no-loc(Identity):::` metodo.</span><span class="sxs-lookup"><span data-stu-id="36c01-171">An exception to this rule is the `Add:::no-loc(Identity):::` method.</span></span> <span data-ttu-id="36c01-172">Questo metodo aggiunge :::no-loc(cookie)::: per l'utente e imposta gli schemi di autenticazione e di verifica predefiniti per l'applicazione :::no-loc(cookie)::: `:::no-loc(Identity):::Constants.ApplicationScheme` .</span><span class="sxs-lookup"><span data-stu-id="36c01-172">This method adds :::no-loc(cookie):::s for you and sets the default authenticate and challenge schemes to the application :::no-loc(cookie)::: `:::no-loc(Identity):::Constants.ApplicationScheme`.</span></span> <span data-ttu-id="36c01-173">Inoltre, imposta lo schema di accesso predefinito sull'esterno :::no-loc(cookie)::: `:::no-loc(Identity):::Constants.ExternalScheme` .</span><span class="sxs-lookup"><span data-stu-id="36c01-173">Additionally, it sets the default sign-in scheme to the external :::no-loc(cookie)::: `:::no-loc(Identity):::Constants.ExternalScheme`.</span></span>

<a name="obsolete-interface"></a>

## <a name="use-httpcontext-authentication-extensions"></a><span data-ttu-id="36c01-174">Usare le estensioni di autenticazione HttpContext</span><span class="sxs-lookup"><span data-stu-id="36c01-174">Use HttpContext authentication extensions</span></span>

<span data-ttu-id="36c01-175">L' `IAuthenticationManager` interfaccia è il punto di ingresso principale nel sistema di autenticazione 1. x.</span><span class="sxs-lookup"><span data-stu-id="36c01-175">The `IAuthenticationManager` interface is the main entry point into the 1.x authentication system.</span></span> <span data-ttu-id="36c01-176">È stata sostituita con un nuovo set di `HttpContext` metodi di estensione nello `Microsoft.AspNetCore.Authentication` spazio dei nomi.</span><span class="sxs-lookup"><span data-stu-id="36c01-176">It has been replaced with a new set of `HttpContext` extension methods in the `Microsoft.AspNetCore.Authentication` namespace.</span></span>

<span data-ttu-id="36c01-177">I progetti 1. x, ad esempio, fanno riferimento a una `Authentication` proprietà:</span><span class="sxs-lookup"><span data-stu-id="36c01-177">For example, 1.x projects reference an `Authentication` property:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

<span data-ttu-id="36c01-178">Nei progetti 2,0 importare lo `Microsoft.AspNetCore.Authentication` spazio dei nomi ed eliminare i `Authentication` riferimenti alle proprietà:</span><span class="sxs-lookup"><span data-stu-id="36c01-178">In 2.0 projects, import the `Microsoft.AspNetCore.Authentication` namespace, and delete the `Authentication` property references:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

<a name="windows-auth-changes"></a>

## <a name="windows-authentication-httpsys--iisintegration"></a><span data-ttu-id="36c01-179">Autenticazione di Windows (HTTP.sys/IISIntegration)</span><span class="sxs-lookup"><span data-stu-id="36c01-179">Windows Authentication (HTTP.sys / IISIntegration)</span></span>

<span data-ttu-id="36c01-180">Esistono due varianti di autenticazione di Windows:</span><span class="sxs-lookup"><span data-stu-id="36c01-180">There are two variations of Windows authentication:</span></span>

* <span data-ttu-id="36c01-181">L'host consente solo gli utenti autenticati.</span><span class="sxs-lookup"><span data-stu-id="36c01-181">The host only allows authenticated users.</span></span> <span data-ttu-id="36c01-182">Questa variante non è interessata dalle modifiche apportate al 2,0.</span><span class="sxs-lookup"><span data-stu-id="36c01-182">This variation isn't affected by the 2.0 changes.</span></span>
* <span data-ttu-id="36c01-183">L'host consente utenti anonimi e autenticati.</span><span class="sxs-lookup"><span data-stu-id="36c01-183">The host allows both anonymous and authenticated users.</span></span> <span data-ttu-id="36c01-184">Questa variazione è interessata dalle modifiche apportate a 2,0.</span><span class="sxs-lookup"><span data-stu-id="36c01-184">This variation is affected by the 2.0 changes.</span></span> <span data-ttu-id="36c01-185">Ad esempio, l'app deve consentire agli utenti anonimi a livello di [IIS](xref:host-and-deploy/iis/index) o [HTTP.sys](xref:fundamentals/servers/httpsys) ma autorizzare gli utenti a livello di controller.</span><span class="sxs-lookup"><span data-stu-id="36c01-185">For example, the app should allow anonymous users at the [IIS](xref:host-and-deploy/iis/index) or [HTTP.sys](xref:fundamentals/servers/httpsys) layer but authorize users at the controller level.</span></span> <span data-ttu-id="36c01-186">In questo scenario, impostare lo schema predefinito nel `Startup.ConfigureServices` metodo.</span><span class="sxs-lookup"><span data-stu-id="36c01-186">In this scenario, set the default scheme in the `Startup.ConfigureServices` method.</span></span>

  <span data-ttu-id="36c01-187">Per [Microsoft. AspNetCore. Server. IISIntegration](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IISIntegration/), impostare lo schema predefinito su `IISDefaults.AuthenticationScheme` :</span><span class="sxs-lookup"><span data-stu-id="36c01-187">For [Microsoft.AspNetCore.Server.IISIntegration](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IISIntegration/), set the default scheme to `IISDefaults.AuthenticationScheme`:</span></span>

  ```csharp
  using Microsoft.AspNetCore.Server.IISIntegration;

  services.AddAuthentication(IISDefaults.AuthenticationScheme);
  ```

  <span data-ttu-id="36c01-188">Per [Microsoft. AspNetCore. Server. HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/), impostare lo schema predefinito su `HttpSysDefaults.AuthenticationScheme` :</span><span class="sxs-lookup"><span data-stu-id="36c01-188">For [Microsoft.AspNetCore.Server.HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/), set the default scheme to `HttpSysDefaults.AuthenticationScheme`:</span></span>

  ```csharp
  using Microsoft.AspNetCore.Server.HttpSys;

  services.AddAuthentication(HttpSysDefaults.AuthenticationScheme);
  ```

  <span data-ttu-id="36c01-189">Se non si imposta lo schema predefinito, la richiesta autorizza (Challenge) non funziona con l'eccezione seguente:</span><span class="sxs-lookup"><span data-stu-id="36c01-189">Failure to set the default scheme prevents the authorize (challenge) request from working with the following exception:</span></span>

  > <span data-ttu-id="36c01-190">`System.InvalidOperationException`: Non è stato specificato alcun authenticationScheme e non è stato trovato alcun DefaultChallengeScheme.</span><span class="sxs-lookup"><span data-stu-id="36c01-190">`System.InvalidOperationException`: No authenticationScheme was specified, and there was no DefaultChallengeScheme found.</span></span>

<span data-ttu-id="36c01-191">Per altre informazioni, vedere <xref:security/authentication/windowsauth>.</span><span class="sxs-lookup"><span data-stu-id="36c01-191">For more information, see <xref:security/authentication/windowsauth>.</span></span>

<a name="identity-:::no-loc(cookie):::-options"></a>

## <a name="no-locidentityno-loccookieoptions-instances"></a><span data-ttu-id="36c01-192">:::no-loc(Identity)::::::no-loc(Cookie):::Istanze di opzioni</span><span class="sxs-lookup"><span data-stu-id="36c01-192">:::no-loc(Identity)::::::no-loc(Cookie):::Options instances</span></span>

<span data-ttu-id="36c01-193">Un effetto collaterale delle modifiche 2,0 è il passaggio all'uso delle opzioni denominate anziché delle :::no-loc(cookie)::: istanze di Options.</span><span class="sxs-lookup"><span data-stu-id="36c01-193">A side effect of the 2.0 changes is the switch to using named options instead of :::no-loc(cookie)::: options instances.</span></span> <span data-ttu-id="36c01-194">La possibilità di personalizzare i :::no-loc(Identity)::: :::no-loc(cookie)::: nomi degli schemi viene rimossa.</span><span class="sxs-lookup"><span data-stu-id="36c01-194">The ability to customize the :::no-loc(Identity)::: :::no-loc(cookie)::: scheme names is removed.</span></span>

<span data-ttu-id="36c01-195">Ad esempio, i progetti 1. x usano il [Costruttore Injection](xref:mvc/controllers/dependency-injection#constructor-injection) per passare un `:::no-loc(Identity)::::::no-loc(Cookie):::Options` parametro in *AccountController.cs* e *ManageController.cs* .</span><span class="sxs-lookup"><span data-stu-id="36c01-195">For example, 1.x projects use [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) to pass an `:::no-loc(Identity)::::::no-loc(Cookie):::Options` parameter into *AccountController.cs* and *ManageController.cs* .</span></span> <span data-ttu-id="36c01-196">:::no-loc(cookie):::È possibile accedere allo schema di autenticazione esterno dall'istanza di specificata:</span><span class="sxs-lookup"><span data-stu-id="36c01-196">The external :::no-loc(cookie)::: authentication scheme is accessed from the provided instance:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/AccountController.cs?name=snippet_AccountControllerConstructor&highlight=4,11)]

<span data-ttu-id="36c01-197">L'inserimento del costruttore precedente diventa superfluo nei progetti 2,0 e il `_external:::no-loc(Cookie):::Scheme` campo può essere eliminato:</span><span class="sxs-lookup"><span data-stu-id="36c01-197">The aforementioned constructor injection becomes unnecessary in 2.0 projects, and the `_external:::no-loc(Cookie):::Scheme` field can be deleted:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AccountControllerConstructor)]

<span data-ttu-id="36c01-198">1. x i progetti usavano il `_external:::no-loc(Cookie):::Scheme` campo come indicato di seguito:</span><span class="sxs-lookup"><span data-stu-id="36c01-198">1.x projects used the `_external:::no-loc(Cookie):::Scheme` field as follows:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

<span data-ttu-id="36c01-199">Nei progetti 2,0 sostituire il codice precedente con quello riportato di seguito.</span><span class="sxs-lookup"><span data-stu-id="36c01-199">In 2.0 projects, replace the preceding code with the following.</span></span> <span data-ttu-id="36c01-200">La `:::no-loc(Identity):::Constants.ExternalScheme` costante può essere utilizzata direttamente.</span><span class="sxs-lookup"><span data-stu-id="36c01-200">The `:::no-loc(Identity):::Constants.ExternalScheme` constant can be used directly.</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

<span data-ttu-id="36c01-201">Risolvere la chiamata appena aggiunta `SignOutAsync` importando lo spazio dei nomi seguente:</span><span class="sxs-lookup"><span data-stu-id="36c01-201">Resolve the newly added `SignOutAsync` call by importing the following namespace:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AuthenticationImport)]

<a name="navigation-properties"></a>

## <a name="add-no-locidentityuser-poco-navigation-properties"></a><span data-ttu-id="36c01-202">Aggiungere le :::no-loc(Identity)::: proprietà di navigazione poco utente</span><span class="sxs-lookup"><span data-stu-id="36c01-202">Add :::no-loc(Identity):::User POCO navigation properties</span></span>

<span data-ttu-id="36c01-203">Sono state rimosse le proprietà di navigazione di base Entity Framework (EF) dell' `:::no-loc(Identity):::User` oggetto poco (Plain Old CLR Object).</span><span class="sxs-lookup"><span data-stu-id="36c01-203">The Entity Framework (EF) Core navigation properties of the base `:::no-loc(Identity):::User` POCO (Plain Old CLR Object) have been removed.</span></span> <span data-ttu-id="36c01-204">Se nel progetto 1. x sono state usate queste proprietà, aggiungerle manualmente al progetto 2,0:</span><span class="sxs-lookup"><span data-stu-id="36c01-204">If your 1.x project used these properties, manually add them back to the 2.0 project:</span></span>

```csharp
/// <summary>
/// Navigation property for the roles this user belongs to.
/// </summary>
public virtual ICollection<:::no-loc(Identity):::UserRole<int>> Roles { get; } = new List<:::no-loc(Identity):::UserRole<int>>();

/// <summary>
/// Navigation property for the claims this user possesses.
/// </summary>
public virtual ICollection<:::no-loc(Identity):::UserClaim<int>> Claims { get; } = new List<:::no-loc(Identity):::UserClaim<int>>();

/// <summary>
/// Navigation property for this users login accounts.
/// </summary>
public virtual ICollection<:::no-loc(Identity):::UserLogin<int>> Logins { get; } = new List<:::no-loc(Identity):::UserLogin<int>>();
```

<span data-ttu-id="36c01-205">Per evitare chiavi esterne duplicate durante l'esecuzione di EF Core migrazioni, aggiungere quanto segue al `:::no-loc(Identity):::DbContext` metodo della classe `OnModelCreating` (dopo la `base.OnModelCreating();` chiamata):</span><span class="sxs-lookup"><span data-stu-id="36c01-205">To prevent duplicate foreign keys when running EF Core Migrations, add the following to your `:::no-loc(Identity):::DbContext` class' `OnModelCreating` method (after the `base.OnModelCreating();` call):</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder builder)
{
    base.OnModelCreating(builder);
    // Customize the :::no-loc(ASP.NET Core Identity)::: model and override the defaults if needed.
    // For example, you can rename the :::no-loc(ASP.NET Core Identity)::: table names and more.
    // Add your customizations after calling base.OnModelCreating(builder);

    builder.Entity<ApplicationUser>()
        .HasMany(e => e.Claims)
        .WithOne()
        .HasForeignKey(e => e.UserId)
        .IsRequired()
        .OnDelete(DeleteBehavior.Cascade);

    builder.Entity<ApplicationUser>()
        .HasMany(e => e.Logins)
        .WithOne()
        .HasForeignKey(e => e.UserId)
        .IsRequired()
        .OnDelete(DeleteBehavior.Cascade);

    builder.Entity<ApplicationUser>()
        .HasMany(e => e.Roles)
        .WithOne()
        .HasForeignKey(e => e.UserId)
        .IsRequired()
        .OnDelete(DeleteBehavior.Cascade);
}
```

<a name="synchronous-method-removal"></a>

## <a name="replace-getexternalauthenticationschemes"></a><span data-ttu-id="36c01-206">Sostituisci GetExternalAuthenticationSchemes</span><span class="sxs-lookup"><span data-stu-id="36c01-206">Replace GetExternalAuthenticationSchemes</span></span>

<span data-ttu-id="36c01-207">Il metodo sincrono `GetExternalAuthenticationSchemes` è stato rimosso a favore di una versione asincrona.</span><span class="sxs-lookup"><span data-stu-id="36c01-207">The synchronous method `GetExternalAuthenticationSchemes` was removed in favor of an asynchronous version.</span></span> <span data-ttu-id="36c01-208">i progetti 1. x hanno il seguente codice in *Controllers/ManageController. cs* :</span><span class="sxs-lookup"><span data-stu-id="36c01-208">1.x projects have the following code in *Controllers/ManageController.cs* :</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/ManageController.cs?name=snippet_GetExternalAuthenticationSchemes)]

<span data-ttu-id="36c01-209">Questo metodo viene visualizzato in *views/account/login. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="36c01-209">This method appears in *Views/Account/Login.cshtml* too:</span></span>

[!code-cshtml[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Views/Account/Login.cshtml?name=snippet_GetExtAuthNSchemes&highlight=2)]

<span data-ttu-id="36c01-210">Nei progetti 2,0 usare il <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.SignInManager`1.GetExternalAuthenticationSchemesAsync*> metodo.</span><span class="sxs-lookup"><span data-stu-id="36c01-210">In 2.0 projects, use the <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.SignInManager`1.GetExternalAuthenticationSchemesAsync*> method.</span></span> <span data-ttu-id="36c01-211">La modifica in *ManageController.cs* è simile al codice seguente:</span><span class="sxs-lookup"><span data-stu-id="36c01-211">The change in *ManageController.cs* resembles the following code:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/ManageController.cs?name=snippet_GetExternalAuthenticationSchemesAsync)]

<span data-ttu-id="36c01-212">In *login. cshtml* la `AuthenticationScheme` proprietà a cui si accede nel `foreach` ciclo viene modificata in `Name` :</span><span class="sxs-lookup"><span data-stu-id="36c01-212">In *Login.cshtml* , the `AuthenticationScheme` property accessed in the `foreach` loop changes to `Name`:</span></span>

[!code-cshtml[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Views/Account/Login.cshtml?name=snippet_GetExtAuthNSchemesAsync&highlight=2,19)]

<a name="property-change"></a>

## <a name="manageloginsviewmodel-property-change"></a><span data-ttu-id="36c01-213">Modifica della proprietà ManageLoginsViewModel</span><span class="sxs-lookup"><span data-stu-id="36c01-213">ManageLoginsViewModel property change</span></span>

<span data-ttu-id="36c01-214">Un `ManageLoginsViewModel` oggetto viene utilizzato nell' `ManageLogins` azione di *ManageController.cs* .</span><span class="sxs-lookup"><span data-stu-id="36c01-214">A `ManageLoginsViewModel` object is used in the `ManageLogins` action of *ManageController.cs* .</span></span> <span data-ttu-id="36c01-215">Nei progetti di 1. x, il `OtherLogins` tipo restituito della proprietà dell'oggetto è `IList<AuthenticationDescription>` .</span><span class="sxs-lookup"><span data-stu-id="36c01-215">In 1.x projects, the object's `OtherLogins` property return type is `IList<AuthenticationDescription>`.</span></span> <span data-ttu-id="36c01-216">Questo tipo restituito richiede l'importazione di `Microsoft.AspNetCore.Http.Authentication` :</span><span class="sxs-lookup"><span data-stu-id="36c01-216">This return type requires an import of `Microsoft.AspNetCore.Http.Authentication`:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Models/ManageViewModels/ManageLoginsViewModel.cs?name=snippet_ManageLoginsViewModel&highlight=2,11)]

<span data-ttu-id="36c01-217">Nei progetti 2,0, il tipo restituito viene modificato in `IList<AuthenticationScheme>` .</span><span class="sxs-lookup"><span data-stu-id="36c01-217">In 2.0 projects, the return type changes to `IList<AuthenticationScheme>`.</span></span> <span data-ttu-id="36c01-218">Questo nuovo tipo restituito richiede la sostituzione dell' `Microsoft.AspNetCore.Http.Authentication` importazione con un' `Microsoft.AspNetCore.Authentication` importazione.</span><span class="sxs-lookup"><span data-stu-id="36c01-218">This new return type requires replacing the `Microsoft.AspNetCore.Http.Authentication` import with a `Microsoft.AspNetCore.Authentication` import.</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Models/ManageViewModels/ManageLoginsViewModel.cs?name=snippet_ManageLoginsViewModel&highlight=2,11)]

<a name="additional-resources"></a>

## <a name="additional-resources"></a><span data-ttu-id="36c01-219">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="36c01-219">Additional resources</span></span>

<span data-ttu-id="36c01-220">Per ulteriori informazioni, vedere la [discussione relativa al problema Auth 2,0](https://github.com/aspnet/Security/issues/1338) su GitHub.</span><span class="sxs-lookup"><span data-stu-id="36c01-220">For more information, see the [Discussion for Auth 2.0](https://github.com/aspnet/Security/issues/1338) issue on GitHub.</span></span>
