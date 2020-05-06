---
title: Eseguire la migrazione Identity dell'autenticazione e al ASP.NET Core 2,0
author: scottaddie
description: Questo articolo illustra i passaggi più comuni per la migrazione di ASP.NET Core l'autenticazione 1. x Identity e ASP.NET Core 2,0.
ms.author: scaddie
ms.date: 06/21/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: migration/1x-to-2x/identity-2x
ms.openlocfilehash: e828446716d88d92aeb587874421a5751dcb6de0
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82769501"
---
# <a name="migrate-authentication-and-identity-to-aspnet-core-20"></a>Eseguire la migrazione Identity dell'autenticazione e al ASP.NET Core 2,0

Di [Scott Addie](https://github.com/scottaddie) e [Hao Kung](https://github.com/HaoK)

ASP.NET Core 2,0 dispone di un nuovo modello di autenticazione [Identity](xref:security/authentication/identity) che semplifica la configurazione tramite i servizi. ASP.NET Core le applicazioni 1. x che usano l' Identity autenticazione o possono essere aggiornate per usare il nuovo modello, come descritto di seguito.

## <a name="update-namespaces"></a>Aggiornare gli spazi dei nomi

In 1. x, le classi `IdentityRole` come `IdentityUser` e sono state trovate `Microsoft.AspNetCore.Identity.EntityFrameworkCore` nello spazio dei nomi.

In 2,0 lo <xref:Microsoft.AspNetCore.Identity> spazio dei nomi è diventato la nuova Home page per diverse classi di questo tipo. Con il codice Identity predefinito, le classi interessate `ApplicationUser` includono `Startup`e. Modificare le `using` istruzioni per risolvere i riferimenti interessati.

<a name="auth-middleware"></a>

## <a name="authentication-middleware-and-services"></a>Middleware e servizi di autenticazione

Nei progetti di 1. x, l'autenticazione viene configurata tramite middleware. Viene richiamato un metodo middleware per ogni schema di autenticazione che si desidera supportare.

Nell'esempio 1. x seguente viene configurata l' Identity autenticazione di Facebook con in *Startup.cs*:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddIdentity<ApplicationUser, IdentityRole>()
            .AddEntityFrameworkStores<ApplicationDbContext>();
}

public void Configure(IApplicationBuilder app, ILoggerFactory loggerfactory)
{
    app.UseIdentity();
    app.UseFacebookAuthentication(new FacebookOptions {
        AppId = Configuration["auth:facebook:appid"],
        AppSecret = Configuration["auth:facebook:appsecret"]
    });
}
```

Nei progetti 2,0, l'autenticazione viene configurata tramite i servizi. Ogni schema di autenticazione viene registrato nel `ConfigureServices` metodo di *Startup.cs*. Il `UseIdentity` metodo viene sostituito con `UseAuthentication`.

L'esempio 2,0 seguente configura l'autenticazione Facebook con Identity in *Startup.cs*:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddIdentity<ApplicationUser, IdentityRole>()
            .AddEntityFrameworkStores<ApplicationDbContext>();

    // If you want to tweak Identity cookies, they're no longer part of IdentityOptions.
    services.ConfigureApplicationCookie(options => options.LoginPath = "/Account/LogIn");
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

Il `UseAuthentication` metodo aggiunge un singolo componente middleware di autenticazione, responsabile dell'autenticazione automatica e della gestione delle richieste di autenticazione remota. Sostituisce tutti i singoli componenti middleware con un singolo componente middleware comune.

Di seguito sono riportate le istruzioni di migrazione 2,0 per ogni schema di autenticazione principale.

### <a name="cookie-based-authentication"></a>Autenticazione basata su cookie

Selezionare una delle due opzioni seguenti e apportare le modifiche necessarie in *Startup.cs*:

1. Usare i cookie conIdentity
    - Sostituire `UseIdentity` con `UseAuthentication` nel `Configure` metodo:

        ```csharp
        app.UseAuthentication();
        ```

    - Richiamare il `AddIdentity` metodo nel `ConfigureServices` metodo per aggiungere i servizi di autenticazione dei cookie.
    - Facoltativamente, richiamare il `ConfigureApplicationCookie` metodo `ConfigureExternalCookie` o nel `ConfigureServices` metodo per modificare le impostazioni Identity del cookie.

        ```csharp
        services.AddIdentity<ApplicationUser, IdentityRole>()
                .AddEntityFrameworkStores<ApplicationDbContext>()
                .AddDefaultTokenProviders();

        services.ConfigureApplicationCookie(options => options.LoginPath = "/Account/LogIn");
        ```

2. Usa cookie senzaIdentity
    - Sostituire la `UseCookieAuthentication` chiamata al metodo nel `Configure` metodo con `UseAuthentication`:

        ```csharp
        app.UseAuthentication();
        ```

    - Richiamare i `AddAuthentication` metodi `AddCookie` e nel `ConfigureServices` metodo:

        ```csharp
        // If you don't want the cookie to be automatically authenticated and assigned to HttpContext.User,
        // remove the CookieAuthenticationDefaults.AuthenticationScheme parameter passed to AddAuthentication.
        services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
                .AddCookie(options =>
                {
                    options.LoginPath = "/Account/LogIn";
                    options.LogoutPath = "/Account/LogOff";
                });
        ```

### <a name="jwt-bearer-authentication"></a>Autenticazione di JWT Bearer

Apportare le modifiche seguenti in *Startup.cs*:
- Sostituire la `UseJwtBearerAuthentication` chiamata al metodo nel `Configure` metodo con `UseAuthentication`:

    ```csharp
    app.UseAuthentication();
    ```

- Richiamare il `AddJwtBearer` metodo nel `ConfigureServices` metodo:

    ```csharp
    services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
            .AddJwtBearer(options =>
            {
                options.Audience = "http://localhost:5001/";
                options.Authority = "http://localhost:5000/";
            });
    ```

    Questo frammento di codice Identitynon USA, quindi è necessario impostare lo schema predefinito `JwtBearerDefaults.AuthenticationScheme` passando al `AddAuthentication` metodo.

### <a name="openid-connect-oidc-authentication"></a>Autenticazione OpenID Connect (OIDC)

Apportare le modifiche seguenti in *Startup.cs*:

- Sostituire la `UseOpenIdConnectAuthentication` chiamata al metodo nel `Configure` metodo con `UseAuthentication`:

    ```csharp
    app.UseAuthentication();
    ```

- Richiamare il `AddOpenIdConnect` metodo nel `ConfigureServices` metodo:

    ```csharp
    services.AddAuthentication(options =>
    {
        options.DefaultScheme = CookieAuthenticationDefaults.AuthenticationScheme;
        options.DefaultChallengeScheme = OpenIdConnectDefaults.AuthenticationScheme;
    })
    .AddCookie()
    .AddOpenIdConnect(options =>
    {
        options.Authority = Configuration["auth:oidc:authority"];
        options.ClientId = Configuration["auth:oidc:clientid"];
    });
    ```

- Sostituire la `PostLogoutRedirectUri` proprietà nell' `OpenIdConnectOptions` azione con `SignedOutRedirectUri`:

    ```csharp
    .AddOpenIdConnect(options =>
    {
        options.SignedOutRedirectUri = "https://contoso.com";
    });
    ```
    
### <a name="facebook-authentication"></a>Autenticazione Facebook

Apportare le modifiche seguenti in *Startup.cs*:
- Sostituire la `UseFacebookAuthentication` chiamata al metodo nel `Configure` metodo con `UseAuthentication`:

    ```csharp
    app.UseAuthentication();
    ```

- Richiamare il `AddFacebook` metodo nel `ConfigureServices` metodo:

    ```csharp
    services.AddAuthentication()
            .AddFacebook(options =>
            {
                options.AppId = Configuration["auth:facebook:appid"];
                options.AppSecret = Configuration["auth:facebook:appsecret"];
            });
    ```

### <a name="google-authentication"></a>Autenticazione Google

Apportare le modifiche seguenti in *Startup.cs*:
- Sostituire la `UseGoogleAuthentication` chiamata al metodo nel `Configure` metodo con `UseAuthentication`:

    ```csharp
    app.UseAuthentication();
    ```

- Richiamare il `AddGoogle` metodo nel `ConfigureServices` metodo:

    ```csharp
    services.AddAuthentication()
            .AddGoogle(options =>
            {
                options.ClientId = Configuration["auth:google:clientid"];
                options.ClientSecret = Configuration["auth:google:clientsecret"];
            });
    ```

### <a name="microsoft-account-authentication"></a>Autenticazione di account Microsoft

Per ulteriori informazioni sull'autenticazione account Microsoft, vedere [questo problema di GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/14455).

Apportare le modifiche seguenti in *Startup.cs*:
- Sostituire la `UseMicrosoftAccountAuthentication` chiamata al metodo nel `Configure` metodo con `UseAuthentication`:

    ```csharp
    app.UseAuthentication();
    ```

- Richiamare il `AddMicrosoftAccount` metodo nel `ConfigureServices` metodo:

    ```csharp
    services.AddAuthentication()
            .AddMicrosoftAccount(options =>
            {
                options.ClientId = Configuration["auth:microsoft:clientid"];
                options.ClientSecret = Configuration["auth:microsoft:clientsecret"];
            });
    ```

### <a name="twitter-authentication"></a>Autenticazione Twitter

Apportare le modifiche seguenti in *Startup.cs*:
- Sostituire la `UseTwitterAuthentication` chiamata al metodo nel `Configure` metodo con `UseAuthentication`:

    ```csharp
    app.UseAuthentication();
    ```

- Richiamare il `AddTwitter` metodo nel `ConfigureServices` metodo:

    ```csharp
    services.AddAuthentication()
            .AddTwitter(options =>
            {
                options.ConsumerKey = Configuration["auth:twitter:consumerkey"];
                options.ConsumerSecret = Configuration["auth:twitter:consumersecret"];
            });
    ```

### <a name="setting-default-authentication-schemes"></a>Impostazione degli schemi di autenticazione predefiniti

In 1. x, le `AutomaticAuthenticate` proprietà `AutomaticChallenge` e della classe di base [AuthenticationOptions](/dotnet/api/Microsoft.AspNetCore.Builder.AuthenticationOptions?view=aspnetcore-1.1) sono state progettate per essere impostate su un solo schema di autenticazione. Non esiste un modo efficace per applicare questa operazione.

In 2,0 queste due proprietà sono state rimosse come proprietà nella singola `AuthenticationOptions` istanza. Possono essere configurate nella `AddAuthentication` chiamata al metodo all' `ConfigureServices` interno del metodo di *Startup.cs*:

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme);
```

Nel frammento di codice precedente, lo schema predefinito è impostato `CookieAuthenticationDefaults.AuthenticationScheme` su ("cookie").

In alternativa, usare una versione di overload del `AddAuthentication` metodo per impostare più di una proprietà. Nell'esempio di metodo di overload seguente, lo schema predefinito è impostato su `CookieAuthenticationDefaults.AuthenticationScheme`. In alternativa, lo schema di autenticazione può essere specificato all' `[Authorize]` interno dei singoli attributi o dei criteri di autorizzazione.

```csharp
services.AddAuthentication(options =>
{
    options.DefaultScheme = CookieAuthenticationDefaults.AuthenticationScheme;
    options.DefaultChallengeScheme = OpenIdConnectDefaults.AuthenticationScheme;
});
```

Definire uno schema predefinito in 2,0 se si verifica una delle condizioni seguenti:
- Si vuole che l'utente sia connesso automaticamente
- Usare l'attributo `[Authorize]` o i criteri di autorizzazione senza specificare gli schemi

Un'eccezione a questa regola è rappresentata dal `AddIdentity` metodo. Questo metodo aggiunge cookie per l'utente e imposta gli schemi di autenticazione e di verifica predefiniti per il `IdentityConstants.ApplicationScheme`cookie dell'applicazione. Inoltre, imposta lo schema di accesso predefinito sul cookie `IdentityConstants.ExternalScheme`esterno.

<a name="obsolete-interface"></a>

## <a name="use-httpcontext-authentication-extensions"></a>Usare le estensioni di autenticazione HttpContext

L' `IAuthenticationManager` interfaccia è il punto di ingresso principale nel sistema di autenticazione 1. x. È stata sostituita con un nuovo set di `HttpContext` metodi di estensione nello `Microsoft.AspNetCore.Authentication` spazio dei nomi.

I progetti 1. x, ad esempio, `Authentication` fanno riferimento a una proprietà:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

Nei progetti 2,0 importare lo `Microsoft.AspNetCore.Authentication` spazio dei nomi ed eliminare i `Authentication` riferimenti alle proprietà:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

<a name="windows-auth-changes"></a>

## <a name="windows-authentication-httpsys--iisintegration"></a>Autenticazione di Windows (HTTP. sys/IISIntegration)

Esistono due varianti di autenticazione di Windows:

* L'host consente solo gli utenti autenticati. Questa variante non è interessata dalle modifiche apportate al 2,0.
* L'host consente utenti anonimi e autenticati. Questa variazione è interessata dalle modifiche apportate a 2,0. Ad esempio, l'app deve consentire utenti anonimi a livello di [IIS](xref:host-and-deploy/iis/index) o [http. sys](xref:fundamentals/servers/httpsys) , ma autorizzare gli utenti a livello di controller. In questo scenario, impostare lo schema predefinito nel `Startup.ConfigureServices` metodo.

  Per [Microsoft. AspNetCore. Server. IISIntegration](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IISIntegration/), impostare lo schema predefinito su `IISDefaults.AuthenticationScheme`:

  ```csharp
  using Microsoft.AspNetCore.Server.IISIntegration;

  services.AddAuthentication(IISDefaults.AuthenticationScheme);
  ```

  Per [Microsoft. AspNetCore. Server. HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/), impostare lo schema predefinito su `HttpSysDefaults.AuthenticationScheme`:

  ```csharp
  using Microsoft.AspNetCore.Server.HttpSys;

  services.AddAuthentication(HttpSysDefaults.AuthenticationScheme);
  ```

  Se non si imposta lo schema predefinito, la richiesta autorizza (Challenge) non funziona con l'eccezione seguente:

  > `System.InvalidOperationException`: Non è stato specificato alcun authenticationScheme e non è stato trovato alcun DefaultChallengeScheme.

Per altre informazioni, vedere <xref:security/authentication/windowsauth>.

<a name="identity-cookie-options"></a>

## <a name="identitycookieoptions-instances"></a>Istanze di IdentityCookieOptions

Un effetto collaterale delle modifiche 2,0 è il passaggio all'uso delle opzioni denominate anziché delle istanze di opzioni dei cookie. La possibilità di personalizzare i Identity nomi degli schemi dei cookie viene rimossa.

Ad esempio, i progetti 1. x usano il [Costruttore Injection](xref:mvc/controllers/dependency-injection#constructor-injection) per `IdentityCookieOptions` passare un parametro in *AccountController.cs* e *ManageController.cs*. È possibile accedere allo schema di autenticazione del cookie esterno dall'istanza di specificata:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/AccountController.cs?name=snippet_AccountControllerConstructor&highlight=4,11)]

L'inserimento del costruttore precedente diventa superfluo nei progetti 2,0 e `_externalCookieScheme` il campo può essere eliminato:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AccountControllerConstructor)]

1. x i progetti usavano `_externalCookieScheme` il campo come indicato di seguito:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

Nei progetti 2,0 sostituire il codice precedente con quello riportato di seguito. La `IdentityConstants.ExternalScheme` costante può essere utilizzata direttamente.

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

Risolvere la chiamata appena `SignOutAsync` aggiunta importando lo spazio dei nomi seguente:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AuthenticationImport)]

<a name="navigation-properties"></a>

## <a name="add-identityuser-poco-navigation-properties"></a>Aggiungere le proprietà di navigazione POCO IdentityUser

Sono state rimosse le proprietà di navigazione di base `IdentityUser` Entity Framework (EF) dell'oggetto poco (Plain Old CLR Object). Se nel progetto 1. x sono state usate queste proprietà, aggiungerle manualmente al progetto 2,0:

```csharp
/// <summary>
/// Navigation property for the roles this user belongs to.
/// </summary>
public virtual ICollection<IdentityUserRole<int>> Roles { get; } = new List<IdentityUserRole<int>>();

/// <summary>
/// Navigation property for the claims this user possesses.
/// </summary>
public virtual ICollection<IdentityUserClaim<int>> Claims { get; } = new List<IdentityUserClaim<int>>();

/// <summary>
/// Navigation property for this users login accounts.
/// </summary>
public virtual ICollection<IdentityUserLogin<int>> Logins { get; } = new List<IdentityUserLogin<int>>();
```

Per evitare chiavi esterne duplicate durante l'esecuzione di EF Core migrazioni, aggiungere quanto segue `IdentityDbContext` al `OnModelCreating` metodo della classe (dopo `base.OnModelCreating();` la chiamata):

```csharp
protected override void OnModelCreating(ModelBuilder builder)
{
    base.OnModelCreating(builder);
    // Customize the ASP.NET Core Identity model and override the defaults if needed.
    // For example, you can rename the ASP.NET Core Identity table names and more.
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

## <a name="replace-getexternalauthenticationschemes"></a>Sostituisci GetExternalAuthenticationSchemes

Il metodo `GetExternalAuthenticationSchemes` sincrono è stato rimosso a favore di una versione asincrona. i progetti 1. x hanno il seguente codice in *Controllers/ManageController. cs*:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/ManageController.cs?name=snippet_GetExternalAuthenticationSchemes)]

Questo metodo viene visualizzato in *views/account/login. cshtml* :

[!code-cshtml[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Views/Account/Login.cshtml?name=snippet_GetExtAuthNSchemes&highlight=2)]

Nei progetti 2,0 usare il <xref:Microsoft.AspNetCore.Identity.SignInManager`1.GetExternalAuthenticationSchemesAsync*> metodo. La modifica in *ManageController.cs* è simile al codice seguente:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/ManageController.cs?name=snippet_GetExternalAuthenticationSchemesAsync)]

In *login. cshtml*la `AuthenticationScheme` proprietà a cui si accede `foreach` nel ciclo viene `Name`modificata in:

[!code-cshtml[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Views/Account/Login.cshtml?name=snippet_GetExtAuthNSchemesAsync&highlight=2,19)]

<a name="property-change"></a>

## <a name="manageloginsviewmodel-property-change"></a>Modifica della proprietà ManageLoginsViewModel

Un `ManageLoginsViewModel` oggetto viene utilizzato nell' `ManageLogins` azione di *ManageController.cs*. Nei progetti di 1. x, il tipo `OtherLogins` restituito della proprietà dell' `IList<AuthenticationDescription>`oggetto è. Questo tipo restituito richiede l'importazione di `Microsoft.AspNetCore.Http.Authentication`:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Models/ManageViewModels/ManageLoginsViewModel.cs?name=snippet_ManageLoginsViewModel&highlight=2,11)]

Nei progetti 2,0, il tipo restituito viene modificato `IList<AuthenticationScheme>`in. Questo nuovo tipo restituito richiede la sostituzione `Microsoft.AspNetCore.Http.Authentication` dell'importazione con `Microsoft.AspNetCore.Authentication` un'importazione.

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Models/ManageViewModels/ManageLoginsViewModel.cs?name=snippet_ManageLoginsViewModel&highlight=2,11)]

<a name="additional-resources"></a>

## <a name="additional-resources"></a>Risorse aggiuntive

Per ulteriori informazioni, vedere la [discussione relativa al problema Auth 2,0](https://github.com/aspnet/Security/issues/1338) su GitHub.
