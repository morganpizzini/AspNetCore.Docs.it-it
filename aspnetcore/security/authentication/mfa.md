---
title: Autenticazione a più fattori in ASP.NET Core
author: damienbod
description: Informazioni su come configurare multi-factor authentication in un'app ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/17/2020
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
uid: security/authentication/mfa
ms.openlocfilehash: e224f947335ea8ea6ed8887dfadb52202bfd7866
ms.sourcegitcommit: 8fcb08312a59c37e3542e7a67dad25faf5bb8e76
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/11/2020
ms.locfileid: "90009505"
---
# <a name="multi-factor-authentication-in-aspnet-core"></a>Autenticazione a più fattori in ASP.NET Core

Di [Damien Bowden](https://github.com/damienbod)

Multi-factor authentication è un processo in cui un utente viene richiesto durante un evento di accesso per ulteriori forme di identificazione. Questa richiesta può essere l'immissione di un codice da un cellulare, l'uso di una chiave FIDO2 o l'analisi di un'impronta digitale. Quando è necessaria una seconda forma di autenticazione, la sicurezza viene migliorata. Il fattore aggiuntivo non è facilmente ottenibile o duplicato da un utente malintenzionato.

In questo articolo vengono illustrate le aree seguenti:

* Che cos'è l'autenticazione a più fattori e i flussi di autenticazione a più fattori
* Configurare l'autenticazione a più fattori per le pagine di amministrazione usando ASP.NET Core Identity
* Inviare il requisito di accesso a multi-factor authentication al server OpenID Connect
* Forza ASP.NET Core client OpenID Connect a richiedere l'autenticazione a più fattori

## <a name="mfa-2fa"></a>MULTI-FACTOR AUTHENTICATION, 2FA

Multi-factor authentication richiede almeno due o più tipi di prova per un'identità come un elemento noto, un elemento posseduto o una convalida biometrica per l'autenticazione dell'utente.

L'autenticazione a due fattori (2FA) è come un subset di autenticazione a più fattori, ma la differenza consiste nel fatto che l'autenticazione a più fattori può richiedere due o più fattori per dimostrare l'identità.

### <a name="mfa-totp-time-based-one-time-password-algorithm"></a>TOTP di autenticazione a più fattori (algoritmo monouso basato sul tempo)

L'autenticazione a più fattori con TOTP è un'implementazione supportata tramite ASP.NET Core Identity . Questo può essere usato insieme a qualsiasi app di autenticazione conforme, tra cui:

* App Microsoft Authenticator
* App Google Authenticator

Per informazioni dettagliate sull'implementazione, vedere il collegamento seguente:

[Abilitare la generazione di codice a matrice per le app TOTP Authenticator in ASP.NET Core](xref:security/authentication/identity-enable-qrcodes)

### <a name="mfa-fido2-or-passwordless"></a>FIDO2 di autenticazione a più fattori o password

FIDO2 è attualmente:

* Il modo più sicuro per ottenere l'autenticazione a più fattori.
* Unico flusso di autenticazione a più fattori che protegge da attacchi di phishing.

Attualmente, ASP.NET Core non supporta direttamente FIDO2. FIDO2 può essere usato per i flussi con autenticazione a più fattori o con password.

Azure Active Directory fornisce il supporto per FIDO2 e i flussi con password. Per ulteriori informazioni, vedere [Opzioni di autenticazione con password per Azure Active Directory](/azure/active-directory/authentication/concept-authentication-passwordless).

### <a name="mfa-sms"></a>SMS MULTI-FACTOR AUTHENTICATION

L'autenticazione a più fattori con SMS aumenta notevolmente la sicurezza rispetto all'autenticazione delle password (fattore singolo). Tuttavia, non è più consigliabile usare SMS come secondo fattore. Sono presenti troppi vettori di attacco noti per questo tipo di implementazione.

[Linee guida del NIST](https://pages.nist.gov/800-63-3/sp800-63b.html)

## <a name="configure-mfa-for-administration-pages-using-no-locaspnet-core-identity"></a>Configurare l'autenticazione a più fattori per le pagine di amministrazione usando ASP.NET Core Identity

L'autenticazione a più fattori può essere forzata sugli utenti per accedere alle pagine sensibili all'interno di un' ASP.NET Core Identity app. Questa operazione può essere utile per le app in cui esistono diversi livelli di accesso per le diverse identità. Ad esempio, gli utenti potrebbero essere in grado di visualizzare i dati del profilo utilizzando un account di accesso con password, ma per accedere alle pagine amministrative è necessario un amministratore per utilizzare l'autenticazione a più fattori.

### <a name="extend-the-login-with-an-mfa-claim"></a>Estendere l'accesso con un'attestazione di autenticazione a più fattori

Il codice demo viene configurato usando ASP.NET Core con Identity le Razor pagine e. `AddIdentity`Viene utilizzato il metodo anziché `AddDefaultIdentity` uno, pertanto è `IUserClaimsPrincipalFactory` possibile utilizzare un'implementazione per aggiungere attestazioni all'identità dopo un accesso riuscito.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(
            Configuration.GetConnectionString("DefaultConnection")));
    
    services.AddIdentity<IdentityUser, IdentityRole>(
            options => options.SignIn.RequireConfirmedAccount = false)
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

    services.AddSingleton<IEmailSender, EmailSender>();
    services.AddScoped<IUserClaimsPrincipalFactory<IdentityUser>, 
        AdditionalUserClaimsPrincipalFactory>();

    services.AddAuthorization(options =>
        options.AddPolicy("TwoFactorEnabled",
            x => x.RequireClaim("amr", "mfa")));

    services.AddRazorPages();
}
```

La `AdditionalUserClaimsPrincipalFactory` classe aggiunge l' `amr` attestazione alle attestazioni utente solo dopo un accesso riuscito. Il valore dell'attestazione viene letto dal database. L'attestazione viene aggiunta qui perché l'utente deve accedere solo a una visualizzazione protetta superiore se l'identità si è connessa con l'autenticazione a più fattori. Se la vista di database viene letta direttamente dal database anziché usare l'attestazione, è possibile accedere alla visualizzazione senza autenticazione a più fattori direttamente dopo l'attivazione dell'autenticazione a più fattori.

```csharp
using Microsoft.AspNetCore.Identity;
using Microsoft.Extensions.Options;
using System.Collections.Generic;
using System.Security.Claims;
using System.Threading.Tasks;

namespace IdentityStandaloneMfa
{
    public class AdditionalUserClaimsPrincipalFactory : 
        UserClaimsPrincipalFactory<IdentityUser, IdentityRole>
    {
        public AdditionalUserClaimsPrincipalFactory( 
            UserManager<IdentityUser> userManager,
            RoleManager<IdentityRole> roleManager, 
            IOptions<IdentityOptions> optionsAccessor) 
            : base(userManager, roleManager, optionsAccessor)
        {
        }

        public async override Task<ClaimsPrincipal> CreateAsync(IdentityUser user)
        {
            var principal = await base.CreateAsync(user);
            var identity = (ClaimsIdentity)principal.Identity;

            var claims = new List<Claim>();

            if (user.TwoFactorEnabled)
            {
                claims.Add(new Claim("amr", "mfa"));
            }
            else
            {
                claims.Add(new Claim("amr", "pwd"));
            }

            identity.AddClaims(claims);
            return principal;
        }
    }
}
```

Poiché l' Identity installazione del servizio è cambiata nella `Startup` classe, Identity è necessario aggiornare i layout di. Impalcature delle Identity pagine nell'app. Definire il layout nel file * Identity /account/Manage/_Layout. cshtml* .

```cshtml
@{
    Layout = "/Pages/Shared/_Layout.cshtml";
}
```

Assegnare anche il layout per tutte le pagine Gestisci dalle Identity pagine:

```cshtml
@{
    Layout = "_Layout.cshtml";
}
```

### <a name="validate-the-mfa-requirement-in-the-administration-page"></a>Convalidare il requisito di autenticazione a più fattori nella pagina di amministrazione

La pagina di amministrazione verifica Razor che l'utente abbia eseguito l'accesso con l'autenticazione a più fattori. Nel `OnGet` metodo, l'identità viene usata per accedere alle attestazioni utente. L' `amr` attestazione viene verificata per il valore `mfa` . Se nell'identità manca questa attestazione o se è `false` , la pagina viene reindirizzata alla pagina Abilita autenticazione a più fattori. Questa operazione è possibile perché l'utente ha già eseguito l'accesso, ma senza autenticazione a più fattori.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.AspNetCore.Mvc.RazorPages;

namespace IdentityStandaloneMfa
{
    public class AdminModel : PageModel
    {
        public IActionResult OnGet()
        {
            var claimTwoFactorEnabled = 
                User.Claims.FirstOrDefault(t => t.Type == "amr");

            if (claimTwoFactorEnabled != null && 
                "mfa".Equals(claimTwoFactorEnabled.Value))
            {
                // You logged in with MFA, do the administrative stuff
            }
            else
            {
                return Redirect(
                    "/Identity/Account/Manage/TwoFactorAuthentication");
            }

            return Page();
        }
    }
}
```

### <a name="ui-logic-to-toggle-user-login-information"></a>Logica dell'interfaccia utente per abilitare o disabilitare le informazioni di accesso degli utenti

Un criterio di autorizzazione è stato aggiunto all'avvio. Il criterio richiede l' `amr` attestazione con il valore `mfa` .

```csharp
services.AddAuthorization(options =>
    options.AddPolicy("TwoFactorEnabled",
        x => x.RequireClaim("amr", "mfa")));
```

Questo criterio può quindi essere usato nella `_Layout` visualizzazione per mostrare o nascondere il menu di **Amministrazione** con l'avviso:

```cshtml
@using Microsoft.AspNetCore.Authorization
@using Microsoft.AspNetCore.Identity
@inject SignInManager<IdentityUser> SignInManager
@inject UserManager<IdentityUser> UserManager
@inject IAuthorizationService AuthorizationService
```

Se l'identità ha eseguito l'accesso con l'autenticazione a più fattori, il menu di **Amministrazione** viene visualizzato senza l'avviso della descrizione comando. Quando l'utente ha effettuato l'accesso senza autenticazione a più fattori, viene visualizzato il menu **amministratore (non abilitato)** insieme alla descrizione comando che informa l'utente (spiegando l'avviso).

```cshtml
@if (SignInManager.IsSignedIn(User))
{
    @if ((AuthorizationService.AuthorizeAsync(User, "TwoFactorEnabled")).Result.Succeeded)
    {
        <li class="nav-item">
            <a class="nav-link text-dark" asp-area="" asp-page="/Admin">Admin</a>
        </li>
    }
    else
    {
        <li class="nav-item">
            <a class="nav-link text-dark" asp-area="" asp-page="/Admin" 
               id="tooltip-demo"  
               data-toggle="tooltip" 
               data-placement="bottom" 
               title="MFA is NOT enabled. This is required for the Admin Page. If you have activated MFA, then logout, login again.">
                Admin (Not Enabled)
            </a>
        </li>
    }
}
```

Se l'utente esegue l'accesso senza autenticazione a più fattori, viene visualizzato l'avviso seguente:

![Autenticazione a più fattori dell'amministratore](mfa/_static/identitystandalonemfa_01.png)

L'utente viene reindirizzato alla visualizzazione Abilita autenticazione a più fattori quando si fa clic sul collegamento **amministratore** :

![L'amministratore attiva l'autenticazione a più fattori](mfa/_static/identitystandalonemfa_02.png)

## <a name="send-mfa-sign-in-requirement-to-openid-connect-server"></a>Inviare il requisito di accesso a multi-factor authentication al server OpenID Connect 

Il `acr_values` parametro può essere usato per passare il `mfa` valore richiesto dal client al server in una richiesta di autenticazione.

> [!NOTE]
> Il `acr_values` parametro deve essere gestito nel server OpenID Connect affinché funzioni correttamente.

### <a name="openid-connect-aspnet-core-client"></a>Client ASP.NET Core OpenID Connect

L' Razor app client OpenID Connect per le pagine ASP.NET Core usa il `AddOpenIdConnect` metodo per accedere al server OpenID Connect. Il `acr_values` parametro viene impostato con il `mfa` valore e inviato con la richiesta di autenticazione. `OpenIdConnectEvents`Viene usato per aggiungere questo oggetto.

Per `acr_values` i valori dei parametri consigliati, vedere [valori di riferimento del metodo di autenticazione](https://tools.ietf.org/html/draft-ietf-oauth-amr-values-08).

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(options =>
    {
        options.DefaultScheme =
            CookieAuthenticationDefaults.AuthenticationScheme;
        options.DefaultChallengeScheme =
            OpenIdConnectDefaults.AuthenticationScheme;
    })
    .AddCookie()
    .AddOpenIdConnect(options =>
    {
        options.SignInScheme =
            CookieAuthenticationDefaults.AuthenticationScheme;
        options.Authority = "<OpenID Connect server URL>";
        options.RequireHttpsMetadata = true;
        options.ClientId = "<OpenID Connect client ID>";
        options.ClientSecret = "<>";
        // Code with PKCE can also be used here
        options.ResponseType = "code id_token";
        options.Scope.Add("profile");
        options.Scope.Add("offline_access");
        options.SaveTokens = true;
        options.Events = new OpenIdConnectEvents
        {
            OnRedirectToIdentityProvider = context =>
            {
                context.ProtocolMessage.SetParameter("acr_values", "mfa");
                return Task.FromResult(0);
            }
        };
    });
```

### <a name="example-openid-connect-no-locidentityserver-4-server-with-no-locaspnet-core-identity"></a>Esempio di OpenID Connect Identity Server 4 server con ASP.NET Core Identity

Nel server OpenID Connect, implementato usando ASP.NET Core Identity con le visualizzazioni MVC, viene creata una nuova vista denominata *ErrorEnable2FA. cshtml* . Visualizzazione:

* Visualizza se il Identity deriva da un'app che richiede l'autenticazione a più fattori, ma l'utente non ha attivato questa operazione in Identity .
* Informa l'utente e aggiunge un collegamento per attivarlo.

```cshtml
@{
    ViewData["Title"] = "ErrorEnable2FA";
}

<h1>The client application requires you to have MFA enabled. Enable this, try login again.</h1>

<br />

You can enable MFA to login here:

<br />

<a asp-controller="Manage" asp-action="TwoFactorAuthentication">Enable MFA</a>
```

Nel `Login` metodo, l' `IIdentityServerInteractionService` implementazione dell'interfaccia `_interaction` viene usata per accedere ai parametri della richiesta OpenID Connect. `acr_values`È possibile accedere al parametro utilizzando la `AcrValues` Proprietà. Il client ha inviato questo oggetto con `mfa` set, quindi è possibile verificarlo.

Se l'autenticazione a più fattori è obbligatoria e l'utente in ha l'autenticazione a più fattori ASP.NET Core Identity abilitata, l'accesso continua. Quando l'utente non dispone di autenticazione a più fattori abilitata, l'utente viene reindirizzato alla visualizzazione personalizzata *ErrorEnable2FA. cshtml*. Quindi ASP.NET Core Identity firma l'utente in.

```csharp
//
// POST: /Account/Login
[HttpPost]
[AllowAnonymous]
[ValidateAntiForgeryToken]
public async Task<IActionResult> Login(LoginInputModel model)
{
    var returnUrl = model.ReturnUrl;
    var context = 
        await _interaction.GetAuthorizationContextAsync(returnUrl);
    var requires2Fa = 
        context?.AcrValues.Count(t => t.Contains("mfa")) >= 1;

    var user = await _userManager.FindByNameAsync(model.Email);
    if (user != null && !user.TwoFactorEnabled && requires2Fa)
    {
        return RedirectToAction(nameof(ErrorEnable2FA));
    }

    // code omitted for brevity
```

Il `ExternalLoginCallback` metodo funziona come l' Identity account di accesso locale. `AcrValues`Viene verificata la proprietà per il `mfa` valore. Se il `mfa` valore è presente, l'autenticazione a più fattori viene forzata prima del completamento dell'accesso, ad esempio reindirizzato alla `ErrorEnable2FA` visualizzazione.

```csharp
//
// GET: /Account/ExternalLoginCallback
[HttpGet]
[AllowAnonymous]
public async Task<IActionResult> ExternalLoginCallback(
    string returnUrl = null,
    string remoteError = null)
{
    var context =
        await _interaction.GetAuthorizationContextAsync(returnUrl);
    var requires2Fa =
        context?.AcrValues.Count(t => t.Contains("mfa")) >= 1;

    if (remoteError != null)
    {
        ModelState.AddModelError(
            string.Empty,
            _sharedLocalizer["EXTERNAL_PROVIDER_ERROR", 
            remoteError]);
        return View(nameof(Login));
    }
    var info = await _signInManager.GetExternalLoginInfoAsync();

    if (info == null)
    {
        return RedirectToAction(nameof(Login));
    }

    var email = info.Principal.FindFirstValue(ClaimTypes.Email);

    if (!string.IsNullOrEmpty(email))
    {
        var user = await _userManager.FindByNameAsync(email);
        if (user != null && !user.TwoFactorEnabled && requires2Fa)
        {
            return RedirectToAction(nameof(ErrorEnable2FA));
        }
    }

    // Sign in the user with this external login provider if the user already has a login.
    var result = await _signInManager
        .ExternalLoginSignInAsync(
            info.LoginProvider, 
            info.ProviderKey, 
            isPersistent: 
            false);

    // code omitted for brevity
```

Se l'utente ha già eseguito l'accesso, l'app client:

* Convalida comunque l' `amr` attestazione.
* Consente di configurare l'autenticazione a più fattori con un collegamento alla ASP.NET Core Identity visualizzazione.

![acr_values-1](mfa/_static/acr_values-1.png)

## <a name="force-aspnet-core-openid-connect-client-to-require-mfa"></a>Forza ASP.NET Core client OpenID Connect a richiedere l'autenticazione a più fattori

Questo esempio Mostra come un' Razor app ASP.NET Core pagina, che usa OpenID Connect per accedere, può richiedere l'autenticazione degli utenti con l'autenticazione a più fattori.

Per convalidare il requisito di autenticazione a più fattori, `IAuthorizationRequirement` viene creato un requisito. Questa operazione verrà aggiunta alle pagine usando criteri che richiedono l'autenticazione a più fattori.

```csharp
using Microsoft.AspNetCore.Authorization;
 
namespace AspNetCoreRequireMfaOidc
{
    public class RequireMfa : IAuthorizationRequirement{}
}
```

Viene implementato un oggetto che utilizzerà `AuthorizationHandler` l' `amr` attestazione e verificherà il valore `mfa` . `amr`Viene restituito nell'oggetto `id_token` di un'autenticazione riuscita e può avere molti valori diversi come definito nella specifica dei [valori di riferimento del metodo di autenticazione](https://tools.ietf.org/html/draft-ietf-oauth-amr-values-08) .

Il valore restituito dipende dal modo in cui l'identità è stata autenticata e dall'implementazione del server OpenID Connect.

`AuthorizationHandler`Usa il `RequireMfa` requisito e convalida l' `amr` attestazione. Il server OpenID Connect può essere implementato usando Identity Server4 con ASP.NET Core Identity . Quando un utente esegue l'accesso con TOTP, l' `amr` attestazione viene restituita con un valore di autenticazione a più fattori. Se si usa un'implementazione del server OpenID Connect diversa o un tipo di autenticazione a più fattori differente, l' `amr` attestazione sarà o può avere un valore diverso. Il codice deve essere esteso per accettare anche questa operazione.

```csharp
using Microsoft.AspNetCore.Authorization;
using System;
using System.Linq;
using System.Threading.Tasks;

namespace AspNetCoreRequireMfaOidc
{
    public class RequireMfaHandler : AuthorizationHandler<RequireMfa>
    {
        protected override Task HandleRequirementAsync(
            AuthorizationHandlerContext context, 
            RequireMfa requirement)
        {
            if (context == null)
                throw new ArgumentNullException(nameof(context));
            if (requirement == null)
                throw new ArgumentNullException(nameof(requirement));

            var amrClaim =
                context.User.Claims.FirstOrDefault(t => t.Type == "amr");

            if (amrClaim != null && amrClaim.Value == Amr.Mfa)
            {
                context.Succeed(requirement);
            }

            return Task.CompletedTask;
        }
    }
}
```

Nel `Startup.ConfigureServices` metodo, il `AddOpenIdConnect` metodo viene usato come schema di richiesta predefinito. Il gestore di autorizzazione, usato per controllare l' `amr` attestazione, viene aggiunto all'inversione del contenitore del controllo. Viene quindi creato un criterio che aggiunge il `RequireMfa` requisito.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.ConfigureApplicationCookie(options =>
        options.Cookie.SecurePolicy =
            CookieSecurePolicy.Always);

    services.AddSingleton<IAuthorizationHandler, RequireMfaHandler>();

    services.AddAuthentication(options =>
    {
        options.DefaultScheme =
            CookieAuthenticationDefaults.AuthenticationScheme;
        options.DefaultChallengeScheme =
            OpenIdConnectDefaults.AuthenticationScheme;
    })
    .AddCookie()
    .AddOpenIdConnect(options =>
    {
        options.SignInScheme =
            CookieAuthenticationDefaults.AuthenticationScheme;
        options.Authority = "https://localhost:44352";
        options.RequireHttpsMetadata = true;
        options.ClientId = "AspNetCoreRequireMfaOidc";
        options.ClientSecret = "AspNetCoreRequireMfaOidcSecret";
        options.ResponseType = "code id_token";
        options.Scope.Add("profile");
        options.Scope.Add("offline_access");
        options.SaveTokens = true;
    });

    services.AddAuthorization(options =>
    {
        options.AddPolicy("RequireMfa", policyIsAdminRequirement =>
        {
            policyIsAdminRequirement.Requirements.Add(new RequireMfa());
        });
    });

    services.AddRazorPages();
}
```

Questi criteri vengono quindi usati nella Razor pagina in modo obbligatorio. I criteri possono essere aggiunti a livello globale anche per l'intera app.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Mvc;
using Microsoft.AspNetCore.Mvc.RazorPages;
using Microsoft.Extensions.Logging;

namespace AspNetCoreRequireMfaOidc.Pages
{
    [Authorize(Policy= "RequireMfa")]
    public class IndexModel : PageModel
    {
        private readonly ILogger<IndexModel> _logger;

        public IndexModel(ILogger<IndexModel> logger)
        {
            _logger = logger;
        }

        public void OnGet()
        {
        }
    }
}
```

Se l'utente esegue l'autenticazione senza autenticazione a più fattori, l' `amr` attestazione avrà probabilmente un `pwd` valore. La richiesta non sarà autorizzata ad accedere alla pagina. Utilizzando i valori predefiniti, l'utente verrà reindirizzato alla pagina *account/AccessDenied* . Questo comportamento può essere modificato oppure è possibile implementare qui la logica personalizzata. In questo esempio viene aggiunto un collegamento in modo che l'utente valido possa configurare l'autenticazione a più fattori per il proprio account.

```cshtml
@page
@model AspNetCoreRequireMfaOidc.AccessDeniedModel
@{
    ViewData["Title"] = "AccessDenied";
    Layout = "~/Pages/Shared/_Layout.cshtml";
}

<h1>AccessDenied</h1>

You require MFA to login here

<a href="https://localhost:44352/Manage/TwoFactorAuthentication">Enable MFA</a>
```

Ora solo gli utenti che eseguono l'autenticazione con l'autenticazione a più fattori possono accedere alla pagina o al sito Web. Se vengono usati diversi tipi di autenticazione a più fattori o se 2FA è corretto, l' `amr` attestazione avrà valori diversi e deve essere elaborata correttamente. Diversi server OpenID Connect restituiscono anche valori diversi per questa attestazione e potrebbero non seguire la specifica dei [valori di riferimento del metodo di autenticazione](https://tools.ietf.org/html/draft-ietf-oauth-amr-values-08) .

Quando si esegue l'accesso senza autenticazione a più fattori, ad esempio usando solo una password:

* Il `amr` valore di è `pwd` :

    ![require_mfa_oidc_02.png](mfa/_static/require_mfa_oidc_02.png)

* Accesso negato:

    ![require_mfa_oidc_03.png](mfa/_static/require_mfa_oidc_03.png)

In alternativa, accedere usando OTP con Identity :

![require_mfa_oidc_01.png](mfa/_static/require_mfa_oidc_01.png)

## <a name="additional-resources"></a>Risorse aggiuntive

* [Abilitare la generazione di codice a matrice per le app TOTP Authenticator in ASP.NET Core](xref:security/authentication/identity-enable-qrcodes)
* [Opzioni di autenticazione con password per Azure Active Directory](/azure/active-directory/authentication/concept-authentication-passwordless)
* [Libreria .NET FIDO2 per l'attestazione e l'attestazione FIDO2/WebAuth con .NET](https://github.com/abergs/fido2-net-lib)
* [Webauthn awesome](https://github.com/herrjemand/awesome-webauthn)
