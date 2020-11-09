---
title: Autenticazione a più fattori in ASP.NET Core
author: damienbod
description: Informazioni su come configurare multi-factor authentication in un'app ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/17/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: security/authentication/mfa
ms.openlocfilehash: 873f7d113df84c931ad7fbf2c72aa292e4e87c48
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060391"
---
# <a name="multi-factor-authentication-in-aspnet-core"></a><span data-ttu-id="7ad2a-103">Autenticazione a più fattori in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7ad2a-103">Multi-factor authentication in ASP.NET Core</span></span>

<span data-ttu-id="7ad2a-104">Di [Damien Bowden](https://github.com/damienbod)</span><span class="sxs-lookup"><span data-stu-id="7ad2a-104">By [Damien Bowden](https://github.com/damienbod)</span></span>

[<span data-ttu-id="7ad2a-105">Visualizzare o scaricare il codice di esempio (repository GitHub damienbod/AspNetCoreHybridFlowWithApi)</span><span class="sxs-lookup"><span data-stu-id="7ad2a-105">View or download sample code (damienbod/AspNetCoreHybridFlowWithApi GitHub repository)</span></span>](https://github.com/damienbod/AspNetCoreHybridFlowWithApi)

<span data-ttu-id="7ad2a-106">Multi-factor authentication è un processo in cui un utente viene richiesto durante un evento di accesso per ulteriori forme di identificazione.</span><span class="sxs-lookup"><span data-stu-id="7ad2a-106">Multi-factor authentication (MFA) is a process in which a user is requested during a sign-in event for additional forms of identification.</span></span> <span data-ttu-id="7ad2a-107">Questa richiesta può essere l'immissione di un codice da un cellulare, l'uso di una chiave FIDO2 o l'analisi di un'impronta digitale.</span><span class="sxs-lookup"><span data-stu-id="7ad2a-107">This prompt could be to enter a code from a cellphone, use a FIDO2 key, or to provide a fingerprint scan.</span></span> <span data-ttu-id="7ad2a-108">Quando è necessaria una seconda forma di autenticazione, la sicurezza viene migliorata.</span><span class="sxs-lookup"><span data-stu-id="7ad2a-108">When you require a second form of authentication, security is enhanced.</span></span> <span data-ttu-id="7ad2a-109">Il fattore aggiuntivo non è facilmente ottenibile o duplicato da un utente malintenzionato.</span><span class="sxs-lookup"><span data-stu-id="7ad2a-109">The additional factor isn't easily obtained or duplicated by an attacker.</span></span>

<span data-ttu-id="7ad2a-110">In questo articolo vengono illustrate le aree seguenti:</span><span class="sxs-lookup"><span data-stu-id="7ad2a-110">This article covers the following areas:</span></span>

* <span data-ttu-id="7ad2a-111">Che cos'è l'autenticazione a più fattori e i flussi di autenticazione a più fattori</span><span class="sxs-lookup"><span data-stu-id="7ad2a-111">What is MFA and what MFA flows are recommended</span></span>
* <span data-ttu-id="7ad2a-112">Configurare l'autenticazione a più fattori per le pagine di amministrazione usando ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="7ad2a-112">Configure MFA for administration pages using ASP.NET Core Identity</span></span>
* <span data-ttu-id="7ad2a-113">Inviare il requisito di accesso a multi-factor authentication al server OpenID Connect</span><span class="sxs-lookup"><span data-stu-id="7ad2a-113">Send MFA sign-in requirement to OpenID Connect server</span></span>
* <span data-ttu-id="7ad2a-114">Forza ASP.NET Core client OpenID Connect a richiedere l'autenticazione a più fattori</span><span class="sxs-lookup"><span data-stu-id="7ad2a-114">Force ASP.NET Core OpenID Connect client to require MFA</span></span>

## <a name="mfa-2fa"></a><span data-ttu-id="7ad2a-115">MULTI-FACTOR AUTHENTICATION, 2FA</span><span class="sxs-lookup"><span data-stu-id="7ad2a-115">MFA, 2FA</span></span>

<span data-ttu-id="7ad2a-116">Multi-factor authentication richiede almeno due o più tipi di prova per un'identità come un elemento noto, un elemento posseduto o una convalida biometrica per l'autenticazione dell'utente.</span><span class="sxs-lookup"><span data-stu-id="7ad2a-116">MFA requires at least two or more types of proof for an identity like something you know, something you possess, or biometric validation for the user to authenticate.</span></span>

<span data-ttu-id="7ad2a-117">L'autenticazione a due fattori (2FA) è come un subset di autenticazione a più fattori, ma la differenza consiste nel fatto che l'autenticazione a più fattori può richiedere due o più fattori per dimostrare l'identità.</span><span class="sxs-lookup"><span data-stu-id="7ad2a-117">Two-factor authentication (2FA) is like a subset of MFA, but the difference being that MFA can require two or more factors to prove the identity.</span></span>

### <a name="mfa-totp-time-based-one-time-password-algorithm"></a><span data-ttu-id="7ad2a-118">TOTP di autenticazione a più fattori (algoritmo monouso basato sul tempo)</span><span class="sxs-lookup"><span data-stu-id="7ad2a-118">MFA TOTP (Time-based One-time Password Algorithm)</span></span>

<span data-ttu-id="7ad2a-119">L'autenticazione a più fattori con TOTP è un'implementazione supportata tramite ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="7ad2a-119">MFA using TOTP is a supported implementation using ASP.NET Core Identity.</span></span> <span data-ttu-id="7ad2a-120">Questo può essere usato insieme a qualsiasi app di autenticazione conforme, tra cui:</span><span class="sxs-lookup"><span data-stu-id="7ad2a-120">This can be used together with any compliant authenticator app, including:</span></span>

* <span data-ttu-id="7ad2a-121">App Microsoft Authenticator</span><span class="sxs-lookup"><span data-stu-id="7ad2a-121">Microsoft Authenticator App</span></span>
* <span data-ttu-id="7ad2a-122">App Google Authenticator</span><span class="sxs-lookup"><span data-stu-id="7ad2a-122">Google Authenticator App</span></span>

<span data-ttu-id="7ad2a-123">Per informazioni dettagliate sull'implementazione, vedere il collegamento seguente:</span><span class="sxs-lookup"><span data-stu-id="7ad2a-123">See the following link for implementation details:</span></span>

[<span data-ttu-id="7ad2a-124">Abilitare la generazione di codice a matrice per le app TOTP Authenticator in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7ad2a-124">Enable QR Code generation for TOTP authenticator apps in ASP.NET Core</span></span>](xref:security/authentication/identity-enable-qrcodes)

### <a name="mfa-fido2-or-passwordless"></a><span data-ttu-id="7ad2a-125">FIDO2 di autenticazione a più fattori o password</span><span class="sxs-lookup"><span data-stu-id="7ad2a-125">MFA FIDO2 or passwordless</span></span>

<span data-ttu-id="7ad2a-126">FIDO2 è attualmente:</span><span class="sxs-lookup"><span data-stu-id="7ad2a-126">FIDO2 is currently:</span></span>

* <span data-ttu-id="7ad2a-127">Il modo più sicuro per ottenere l'autenticazione a più fattori.</span><span class="sxs-lookup"><span data-stu-id="7ad2a-127">The most secure way of achieving MFA.</span></span>
* <span data-ttu-id="7ad2a-128">Unico flusso di autenticazione a più fattori che protegge da attacchi di phishing.</span><span class="sxs-lookup"><span data-stu-id="7ad2a-128">The only MFA flow that protects against phishing attacks.</span></span>

<span data-ttu-id="7ad2a-129">Attualmente, ASP.NET Core non supporta direttamente FIDO2.</span><span class="sxs-lookup"><span data-stu-id="7ad2a-129">At present, ASP.NET Core doesn't support FIDO2 directly.</span></span> <span data-ttu-id="7ad2a-130">FIDO2 può essere usato per i flussi con autenticazione a più fattori o con password.</span><span class="sxs-lookup"><span data-stu-id="7ad2a-130">FIDO2 can be used for MFA or passwordless flows.</span></span>

<span data-ttu-id="7ad2a-131">Azure Active Directory fornisce il supporto per FIDO2 e i flussi con password.</span><span class="sxs-lookup"><span data-stu-id="7ad2a-131">Azure Active Directory provides support for FIDO2 and passwordless flows.</span></span> <span data-ttu-id="7ad2a-132">Per ulteriori informazioni, vedere [Opzioni di autenticazione con password per Azure Active Directory](/azure/active-directory/authentication/concept-authentication-passwordless).</span><span class="sxs-lookup"><span data-stu-id="7ad2a-132">For more information, see [Passwordless authentication options for Azure Active Directory](/azure/active-directory/authentication/concept-authentication-passwordless).</span></span>

### <a name="mfa-sms"></a><span data-ttu-id="7ad2a-133">SMS MULTI-FACTOR AUTHENTICATION</span><span class="sxs-lookup"><span data-stu-id="7ad2a-133">MFA SMS</span></span>

<span data-ttu-id="7ad2a-134">L'autenticazione a più fattori con SMS aumenta notevolmente la sicurezza rispetto all'autenticazione delle password (fattore singolo).</span><span class="sxs-lookup"><span data-stu-id="7ad2a-134">MFA with SMS increases security massively compared with password authentication (single factor).</span></span> <span data-ttu-id="7ad2a-135">Tuttavia, non è più consigliabile usare SMS come secondo fattore.</span><span class="sxs-lookup"><span data-stu-id="7ad2a-135">However, using SMS as a second factor is no longer recommended.</span></span> <span data-ttu-id="7ad2a-136">Sono presenti troppi vettori di attacco noti per questo tipo di implementazione.</span><span class="sxs-lookup"><span data-stu-id="7ad2a-136">Too many known attack vectors exist for this type of implementation.</span></span>

[<span data-ttu-id="7ad2a-137">Linee guida del NIST</span><span class="sxs-lookup"><span data-stu-id="7ad2a-137">NIST guidelines</span></span>](https://pages.nist.gov/800-63-3/sp800-63b.html)

## <a name="configure-mfa-for-administration-pages-using-no-locaspnet-core-identity"></a><span data-ttu-id="7ad2a-138">Configurare l'autenticazione a più fattori per le pagine di amministrazione usando ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="7ad2a-138">Configure MFA for administration pages using ASP.NET Core Identity</span></span>

<span data-ttu-id="7ad2a-139">L'autenticazione a più fattori può essere forzata sugli utenti per accedere alle pagine sensibili all'interno di un' ASP.NET Core Identity app.</span><span class="sxs-lookup"><span data-stu-id="7ad2a-139">MFA could be forced on users to access sensitive pages within an ASP.NET Core Identity app.</span></span> <span data-ttu-id="7ad2a-140">Questa operazione può essere utile per le app in cui esistono diversi livelli di accesso per le diverse identità.</span><span class="sxs-lookup"><span data-stu-id="7ad2a-140">This could be useful for apps where different levels of access exist for the different identities.</span></span> <span data-ttu-id="7ad2a-141">Ad esempio, gli utenti potrebbero essere in grado di visualizzare i dati del profilo utilizzando un account di accesso con password, ma per accedere alle pagine amministrative è necessario un amministratore per utilizzare l'autenticazione a più fattori.</span><span class="sxs-lookup"><span data-stu-id="7ad2a-141">For example, users might be able to view the profile data using a password login, but an administrator would be required to use MFA to access the administrative pages.</span></span>

### <a name="extend-the-login-with-an-mfa-claim"></a><span data-ttu-id="7ad2a-142">Estendere l'accesso con un'attestazione di autenticazione a più fattori</span><span class="sxs-lookup"><span data-stu-id="7ad2a-142">Extend the login with an MFA claim</span></span>

<span data-ttu-id="7ad2a-143">Il codice demo viene configurato usando ASP.NET Core con Identity le Razor pagine e.</span><span class="sxs-lookup"><span data-stu-id="7ad2a-143">The demo code is setup using ASP.NET Core with Identity and Razor Pages.</span></span> <span data-ttu-id="7ad2a-144">`AddIdentity`Viene utilizzato il metodo anziché `AddDefaultIdentity` uno, pertanto è `IUserClaimsPrincipalFactory` possibile utilizzare un'implementazione per aggiungere attestazioni all'identità dopo un accesso riuscito.</span><span class="sxs-lookup"><span data-stu-id="7ad2a-144">The `AddIdentity` method is used instead of `AddDefaultIdentity` one, so an `IUserClaimsPrincipalFactory` implementation can be used to add claims to the identity after a successful login.</span></span>

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

<span data-ttu-id="7ad2a-145">La `AdditionalUserClaimsPrincipalFactory` classe aggiunge l' `amr` attestazione alle attestazioni utente solo dopo un accesso riuscito.</span><span class="sxs-lookup"><span data-stu-id="7ad2a-145">The `AdditionalUserClaimsPrincipalFactory` class adds the `amr` claim to the user claims only after a successful login.</span></span> <span data-ttu-id="7ad2a-146">Il valore dell'attestazione viene letto dal database.</span><span class="sxs-lookup"><span data-stu-id="7ad2a-146">The claim's value is read from the database.</span></span> <span data-ttu-id="7ad2a-147">L'attestazione viene aggiunta qui perché l'utente deve accedere solo a una visualizzazione protetta superiore se l'identità si è connessa con l'autenticazione a più fattori.</span><span class="sxs-lookup"><span data-stu-id="7ad2a-147">The claim is added here because the user should only access the higher protected view if the identity has logged in with MFA.</span></span> <span data-ttu-id="7ad2a-148">Se la vista di database viene letta direttamente dal database anziché usare l'attestazione, è possibile accedere alla visualizzazione senza autenticazione a più fattori direttamente dopo l'attivazione dell'autenticazione a più fattori.</span><span class="sxs-lookup"><span data-stu-id="7ad2a-148">If the database view is read from the database directly instead of using the claim, it's possible to access the view without MFA directly after activating the MFA.</span></span>

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

<span data-ttu-id="7ad2a-149">Poiché l' Identity installazione del servizio è cambiata nella `Startup` classe, Identity è necessario aggiornare i layout di.</span><span class="sxs-lookup"><span data-stu-id="7ad2a-149">Because the Identity service setup changed in the `Startup` class, the layouts of the Identity need to be updated.</span></span> <span data-ttu-id="7ad2a-150">Impalcature delle Identity pagine nell'app.</span><span class="sxs-lookup"><span data-stu-id="7ad2a-150">Scaffold the Identity pages into the app.</span></span> <span data-ttu-id="7ad2a-151">Definire il layout nel file *Identity /account/Manage/_Layout. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="7ad2a-151">Define the layout in the *Identity/Account/Manage/_Layout.cshtml* file.</span></span>

```cshtml
@{
    Layout = "/Pages/Shared/_Layout.cshtml";
}
```

<span data-ttu-id="7ad2a-152">Assegnare anche il layout per tutte le pagine Gestisci dalle Identity pagine:</span><span class="sxs-lookup"><span data-stu-id="7ad2a-152">Also assign the layout for all the manage pages from the Identity pages:</span></span>

```cshtml
@{
    Layout = "_Layout.cshtml";
}
```

### <a name="validate-the-mfa-requirement-in-the-administration-page"></a><span data-ttu-id="7ad2a-153">Convalidare il requisito di autenticazione a più fattori nella pagina di amministrazione</span><span class="sxs-lookup"><span data-stu-id="7ad2a-153">Validate the MFA requirement in the administration page</span></span>

<span data-ttu-id="7ad2a-154">La pagina di amministrazione verifica Razor che l'utente abbia eseguito l'accesso con l'autenticazione a più fattori.</span><span class="sxs-lookup"><span data-stu-id="7ad2a-154">The administration Razor Page validates that the user has logged in using MFA.</span></span> <span data-ttu-id="7ad2a-155">Nel `OnGet` metodo, l'identità viene usata per accedere alle attestazioni utente.</span><span class="sxs-lookup"><span data-stu-id="7ad2a-155">In the `OnGet` method, the identity is used to access the user claims.</span></span> <span data-ttu-id="7ad2a-156">L' `amr` attestazione viene verificata per il valore `mfa` .</span><span class="sxs-lookup"><span data-stu-id="7ad2a-156">The `amr` claim is checked for the value `mfa`.</span></span> <span data-ttu-id="7ad2a-157">Se nell'identità manca questa attestazione o se è `false` , la pagina viene reindirizzata alla pagina Abilita autenticazione a più fattori.</span><span class="sxs-lookup"><span data-stu-id="7ad2a-157">If the identity is missing this claim or is `false`, the page redirects to the Enable MFA page.</span></span> <span data-ttu-id="7ad2a-158">Questa operazione è possibile perché l'utente ha già eseguito l'accesso, ma senza autenticazione a più fattori.</span><span class="sxs-lookup"><span data-stu-id="7ad2a-158">This is possible because the user has logged in already, but without MFA.</span></span>

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

### <a name="ui-logic-to-toggle-user-login-information"></a><span data-ttu-id="7ad2a-159">Logica dell'interfaccia utente per abilitare o disabilitare le informazioni di accesso degli utenti</span><span class="sxs-lookup"><span data-stu-id="7ad2a-159">UI logic to toggle user login information</span></span>

<span data-ttu-id="7ad2a-160">Un criterio di autorizzazione è stato aggiunto all'avvio.</span><span class="sxs-lookup"><span data-stu-id="7ad2a-160">An authorization policy was added at startup.</span></span> <span data-ttu-id="7ad2a-161">Il criterio richiede l' `amr` attestazione con il valore `mfa` .</span><span class="sxs-lookup"><span data-stu-id="7ad2a-161">The policy requires the `amr` claim with the value `mfa`.</span></span>

```csharp
services.AddAuthorization(options =>
    options.AddPolicy("TwoFactorEnabled",
        x => x.RequireClaim("amr", "mfa")));
```

<span data-ttu-id="7ad2a-162">Questo criterio può quindi essere usato nella `_Layout` visualizzazione per mostrare o nascondere il menu di **Amministrazione** con l'avviso:</span><span class="sxs-lookup"><span data-stu-id="7ad2a-162">This policy can then be used in the `_Layout` view to show or hide the **Admin** menu with the warning:</span></span>

```cshtml
@using Microsoft.AspNetCore.Authorization
@using Microsoft.AspNetCore.Identity
@inject SignInManager<IdentityUser> SignInManager
@inject UserManager<IdentityUser> UserManager
@inject IAuthorizationService AuthorizationService
```

<span data-ttu-id="7ad2a-163">Se l'identità ha eseguito l'accesso con l'autenticazione a più fattori, il menu di **Amministrazione** viene visualizzato senza l'avviso della descrizione comando.</span><span class="sxs-lookup"><span data-stu-id="7ad2a-163">If the identity has logged in using MFA, the **Admin** menu is displayed without the tooltip warning.</span></span> <span data-ttu-id="7ad2a-164">Quando l'utente ha effettuato l'accesso senza autenticazione a più fattori, viene visualizzato il menu **amministratore (non abilitato)** insieme alla descrizione comando che informa l'utente (spiegando l'avviso).</span><span class="sxs-lookup"><span data-stu-id="7ad2a-164">When the user has logged in without MFA, the **Admin (Not Enabled)** menu is displayed along with the tooltip that informs the user (explaining the warning).</span></span>

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

<span data-ttu-id="7ad2a-165">Se l'utente esegue l'accesso senza autenticazione a più fattori, viene visualizzato l'avviso seguente:</span><span class="sxs-lookup"><span data-stu-id="7ad2a-165">If the user logs in without MFA, the warning is displayed:</span></span>

![Autenticazione a più fattori dell'amministratore](mfa/_static/identitystandalonemfa_01.png)

<span data-ttu-id="7ad2a-167">L'utente viene reindirizzato alla visualizzazione Abilita autenticazione a più fattori quando si fa clic sul collegamento **amministratore** :</span><span class="sxs-lookup"><span data-stu-id="7ad2a-167">The user is redirected to the MFA enable view when clicking the **Admin** link:</span></span>

![L'amministratore attiva l'autenticazione a più fattori](mfa/_static/identitystandalonemfa_02.png)

## <a name="send-mfa-sign-in-requirement-to-openid-connect-server"></a><span data-ttu-id="7ad2a-169">Inviare il requisito di accesso a multi-factor authentication al server OpenID Connect</span><span class="sxs-lookup"><span data-stu-id="7ad2a-169">Send MFA sign-in requirement to OpenID Connect server</span></span> 

<span data-ttu-id="7ad2a-170">Il `acr_values` parametro può essere usato per passare il `mfa` valore richiesto dal client al server in una richiesta di autenticazione.</span><span class="sxs-lookup"><span data-stu-id="7ad2a-170">The `acr_values` parameter can be used to pass the `mfa` required value from the client to the server in an authentication request.</span></span>

> [!NOTE]
> <span data-ttu-id="7ad2a-171">Il `acr_values` parametro deve essere gestito nel server OpenID Connect affinché funzioni correttamente.</span><span class="sxs-lookup"><span data-stu-id="7ad2a-171">The `acr_values` parameter needs to be handled on the OpenID Connect server for this to work.</span></span>

### <a name="openid-connect-aspnet-core-client"></a><span data-ttu-id="7ad2a-172">Client ASP.NET Core OpenID Connect</span><span class="sxs-lookup"><span data-stu-id="7ad2a-172">OpenID Connect ASP.NET Core client</span></span>

<span data-ttu-id="7ad2a-173">L' Razor app client OpenID Connect per le pagine ASP.NET Core usa il `AddOpenIdConnect` metodo per accedere al server OpenID Connect.</span><span class="sxs-lookup"><span data-stu-id="7ad2a-173">The ASP.NET Core Razor Pages OpenID Connect client app uses the `AddOpenIdConnect` method to login to the OpenID Connect server.</span></span> <span data-ttu-id="7ad2a-174">Il `acr_values` parametro viene impostato con il `mfa` valore e inviato con la richiesta di autenticazione.</span><span class="sxs-lookup"><span data-stu-id="7ad2a-174">The `acr_values` parameter is set with the `mfa` value and sent with the authentication request.</span></span> <span data-ttu-id="7ad2a-175">`OpenIdConnectEvents`Viene usato per aggiungere questo oggetto.</span><span class="sxs-lookup"><span data-stu-id="7ad2a-175">The `OpenIdConnectEvents` is used to add this.</span></span>

<span data-ttu-id="7ad2a-176">Per `acr_values` i valori dei parametri consigliati, vedere [valori di riferimento del metodo di autenticazione](https://tools.ietf.org/html/draft-ietf-oauth-amr-values-08).</span><span class="sxs-lookup"><span data-stu-id="7ad2a-176">For recommended `acr_values` parameter values, see [Authentication Method Reference Values](https://tools.ietf.org/html/draft-ietf-oauth-amr-values-08).</span></span>

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

### <a name="example-openid-connect-no-locidentityserver-4-server-with-no-locaspnet-core-identity"></a><span data-ttu-id="7ad2a-177">Esempio di OpenID Connect Identity Server 4 server con ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="7ad2a-177">Example OpenID Connect IdentityServer 4 server with ASP.NET Core Identity</span></span>

<span data-ttu-id="7ad2a-178">Nel server OpenID Connect, implementato usando ASP.NET Core Identity con le visualizzazioni MVC, viene creata una nuova vista denominata *ErrorEnable2FA. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="7ad2a-178">On the OpenID Connect server, which is implemented using ASP.NET Core Identity with MVC views, a new view named *ErrorEnable2FA.cshtml* is created.</span></span> <span data-ttu-id="7ad2a-179">Visualizzazione:</span><span class="sxs-lookup"><span data-stu-id="7ad2a-179">The view:</span></span>

* <span data-ttu-id="7ad2a-180">Visualizza se il Identity deriva da un'app che richiede l'autenticazione a più fattori, ma l'utente non ha attivato questa operazione in Identity .</span><span class="sxs-lookup"><span data-stu-id="7ad2a-180">Displays if the Identity comes from an app that requires MFA but the user hasn't activated this in Identity.</span></span>
* <span data-ttu-id="7ad2a-181">Informa l'utente e aggiunge un collegamento per attivarlo.</span><span class="sxs-lookup"><span data-stu-id="7ad2a-181">Informs the user and adds a link to activate this.</span></span>

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

<span data-ttu-id="7ad2a-182">Nel `Login` metodo, l' `IIdentityServerInteractionService` implementazione dell'interfaccia `_interaction` viene usata per accedere ai parametri della richiesta OpenID Connect.</span><span class="sxs-lookup"><span data-stu-id="7ad2a-182">In the `Login` method, the `IIdentityServerInteractionService` interface implementation `_interaction` is used to access the OpenID Connect request parameters.</span></span> <span data-ttu-id="7ad2a-183">`acr_values`È possibile accedere al parametro utilizzando la `AcrValues` Proprietà.</span><span class="sxs-lookup"><span data-stu-id="7ad2a-183">The `acr_values` parameter is accessed using the `AcrValues` property.</span></span> <span data-ttu-id="7ad2a-184">Il client ha inviato questo oggetto con `mfa` set, quindi è possibile verificarlo.</span><span class="sxs-lookup"><span data-stu-id="7ad2a-184">As the client sent this with `mfa` set, this can then be checked.</span></span>

<span data-ttu-id="7ad2a-185">Se l'autenticazione a più fattori è obbligatoria e l'utente in ha l'autenticazione a più fattori ASP.NET Core Identity abilitata, l'accesso continua.</span><span class="sxs-lookup"><span data-stu-id="7ad2a-185">If MFA is required, and the user in ASP.NET Core Identity has MFA enabled, then the login continues.</span></span> <span data-ttu-id="7ad2a-186">Quando l'utente non dispone di autenticazione a più fattori abilitata, l'utente viene reindirizzato alla visualizzazione personalizzata *ErrorEnable2FA. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="7ad2a-186">When the user has no MFA enabled, the user is redirected to the custom view *ErrorEnable2FA.cshtml* .</span></span> <span data-ttu-id="7ad2a-187">Quindi ASP.NET Core Identity firma l'utente in.</span><span class="sxs-lookup"><span data-stu-id="7ad2a-187">Then ASP.NET Core Identity signs the user in.</span></span>

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

<span data-ttu-id="7ad2a-188">Il `ExternalLoginCallback` metodo funziona come l' Identity account di accesso locale.</span><span class="sxs-lookup"><span data-stu-id="7ad2a-188">The `ExternalLoginCallback` method works like the local Identity login.</span></span> <span data-ttu-id="7ad2a-189">`AcrValues`Viene verificata la proprietà per il `mfa` valore.</span><span class="sxs-lookup"><span data-stu-id="7ad2a-189">The `AcrValues` property is checked for the `mfa` value.</span></span> <span data-ttu-id="7ad2a-190">Se il `mfa` valore è presente, l'autenticazione a più fattori viene forzata prima del completamento dell'accesso, ad esempio reindirizzato alla `ErrorEnable2FA` visualizzazione.</span><span class="sxs-lookup"><span data-stu-id="7ad2a-190">If the `mfa` value is present, MFA is forced before the login completes (for example, redirected to the `ErrorEnable2FA` view).</span></span>

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

<span data-ttu-id="7ad2a-191">Se l'utente ha già eseguito l'accesso, l'app client:</span><span class="sxs-lookup"><span data-stu-id="7ad2a-191">If the user is already logged in, the client app:</span></span>

* <span data-ttu-id="7ad2a-192">Convalida comunque l' `amr` attestazione.</span><span class="sxs-lookup"><span data-stu-id="7ad2a-192">Still validates the `amr` claim.</span></span>
* <span data-ttu-id="7ad2a-193">Consente di configurare l'autenticazione a più fattori con un collegamento alla ASP.NET Core Identity visualizzazione.</span><span class="sxs-lookup"><span data-stu-id="7ad2a-193">Can set up the MFA with a link to the ASP.NET Core Identity view.</span></span>

![acr_values-1](mfa/_static/acr_values-1.png)

## <a name="force-aspnet-core-openid-connect-client-to-require-mfa"></a><span data-ttu-id="7ad2a-195">Forza ASP.NET Core client OpenID Connect a richiedere l'autenticazione a più fattori</span><span class="sxs-lookup"><span data-stu-id="7ad2a-195">Force ASP.NET Core OpenID Connect client to require MFA</span></span>

<span data-ttu-id="7ad2a-196">Questo esempio Mostra come un' Razor app ASP.NET Core pagina, che usa OpenID Connect per accedere, può richiedere l'autenticazione degli utenti con l'autenticazione a più fattori.</span><span class="sxs-lookup"><span data-stu-id="7ad2a-196">This example shows how an ASP.NET Core Razor Page app, which uses OpenID Connect to sign in, can require that users have authenticated using MFA.</span></span>

<span data-ttu-id="7ad2a-197">Per convalidare il requisito di autenticazione a più fattori, `IAuthorizationRequirement` viene creato un requisito.</span><span class="sxs-lookup"><span data-stu-id="7ad2a-197">To validate the MFA requirement, an `IAuthorizationRequirement` requirement is created.</span></span> <span data-ttu-id="7ad2a-198">Questa operazione verrà aggiunta alle pagine usando criteri che richiedono l'autenticazione a più fattori.</span><span class="sxs-lookup"><span data-stu-id="7ad2a-198">This will be added to the pages using a policy that requires MFA.</span></span>

```csharp
using Microsoft.AspNetCore.Authorization;
 
namespace AspNetCoreRequireMfaOidc
{
    public class RequireMfa : IAuthorizationRequirement{}
}
```

<span data-ttu-id="7ad2a-199">Viene implementato un oggetto che utilizzerà `AuthorizationHandler` l' `amr` attestazione e verificherà il valore `mfa` .</span><span class="sxs-lookup"><span data-stu-id="7ad2a-199">An `AuthorizationHandler` is implemented that will use the `amr` claim and check for the value `mfa`.</span></span> <span data-ttu-id="7ad2a-200">`amr`Viene restituito nell'oggetto `id_token` di un'autenticazione riuscita e può avere molti valori diversi come definito nella specifica dei [valori di riferimento del metodo di autenticazione](https://tools.ietf.org/html/draft-ietf-oauth-amr-values-08) .</span><span class="sxs-lookup"><span data-stu-id="7ad2a-200">The `amr` is returned in the `id_token` of a successful authentication and can have many different values as defined in the [Authentication Method Reference Values](https://tools.ietf.org/html/draft-ietf-oauth-amr-values-08) specification.</span></span>

<span data-ttu-id="7ad2a-201">Il valore restituito dipende dal modo in cui l'identità è stata autenticata e dall'implementazione del server OpenID Connect.</span><span class="sxs-lookup"><span data-stu-id="7ad2a-201">The returned value depends on how the identity authenticated and on the OpenID Connect server implementation.</span></span>

<span data-ttu-id="7ad2a-202">`AuthorizationHandler`Usa il `RequireMfa` requisito e convalida l' `amr` attestazione.</span><span class="sxs-lookup"><span data-stu-id="7ad2a-202">The `AuthorizationHandler` uses the `RequireMfa` requirement and validates the `amr` claim.</span></span> <span data-ttu-id="7ad2a-203">Il server OpenID Connect può essere implementato usando Identity Server4 con ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="7ad2a-203">The OpenID Connect server can be implemented using IdentityServer4 with ASP.NET Core Identity.</span></span> <span data-ttu-id="7ad2a-204">Quando un utente esegue l'accesso con TOTP, l' `amr` attestazione viene restituita con un valore di autenticazione a più fattori.</span><span class="sxs-lookup"><span data-stu-id="7ad2a-204">When a user logs in using TOTP, the `amr` claim is returned with an MFA value.</span></span> <span data-ttu-id="7ad2a-205">Se si usa un'implementazione del server OpenID Connect diversa o un tipo di autenticazione a più fattori differente, l' `amr` attestazione sarà o può avere un valore diverso.</span><span class="sxs-lookup"><span data-stu-id="7ad2a-205">If using a different OpenID Connect server implementation or a different MFA type, the `amr` claim will, or can, have a different value.</span></span> <span data-ttu-id="7ad2a-206">Il codice deve essere esteso per accettare anche questa operazione.</span><span class="sxs-lookup"><span data-stu-id="7ad2a-206">The code must be extended to accept this as well.</span></span>

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

<span data-ttu-id="7ad2a-207">Nel `Startup.ConfigureServices` metodo, il `AddOpenIdConnect` metodo viene usato come schema di richiesta predefinito.</span><span class="sxs-lookup"><span data-stu-id="7ad2a-207">In the `Startup.ConfigureServices` method, the `AddOpenIdConnect` method is used as the default challenge scheme.</span></span> <span data-ttu-id="7ad2a-208">Il gestore di autorizzazione, usato per controllare l' `amr` attestazione, viene aggiunto all'inversione del contenitore del controllo.</span><span class="sxs-lookup"><span data-stu-id="7ad2a-208">The authorization handler, which is used to check the `amr` claim, is added to the Inversion of Control container.</span></span> <span data-ttu-id="7ad2a-209">Viene quindi creato un criterio che aggiunge il `RequireMfa` requisito.</span><span class="sxs-lookup"><span data-stu-id="7ad2a-209">A policy is then created which adds the `RequireMfa` requirement.</span></span>

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

<span data-ttu-id="7ad2a-210">Questi criteri vengono quindi usati nella Razor pagina in modo obbligatorio.</span><span class="sxs-lookup"><span data-stu-id="7ad2a-210">This policy is then used in the Razor page as required.</span></span> <span data-ttu-id="7ad2a-211">I criteri possono essere aggiunti a livello globale anche per l'intera app.</span><span class="sxs-lookup"><span data-stu-id="7ad2a-211">The policy could be added globally for the entire app as well.</span></span>

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

<span data-ttu-id="7ad2a-212">Se l'utente esegue l'autenticazione senza autenticazione a più fattori, l' `amr` attestazione avrà probabilmente un `pwd` valore.</span><span class="sxs-lookup"><span data-stu-id="7ad2a-212">If the user authenticates without MFA, the `amr` claim will probably have a `pwd` value.</span></span> <span data-ttu-id="7ad2a-213">La richiesta non sarà autorizzata ad accedere alla pagina.</span><span class="sxs-lookup"><span data-stu-id="7ad2a-213">The request won't be authorized to access the page.</span></span> <span data-ttu-id="7ad2a-214">Utilizzando i valori predefiniti, l'utente verrà reindirizzato alla pagina *account/AccessDenied* .</span><span class="sxs-lookup"><span data-stu-id="7ad2a-214">Using the default values, the user will be redirected to the *Account/AccessDenied* page.</span></span> <span data-ttu-id="7ad2a-215">Questo comportamento può essere modificato oppure è possibile implementare qui la logica personalizzata.</span><span class="sxs-lookup"><span data-stu-id="7ad2a-215">This behavior can be changed or you can implement your own custom logic here.</span></span> <span data-ttu-id="7ad2a-216">In questo esempio viene aggiunto un collegamento in modo che l'utente valido possa configurare l'autenticazione a più fattori per il proprio account.</span><span class="sxs-lookup"><span data-stu-id="7ad2a-216">In this example, a link is added so that the valid user can set up MFA for their account.</span></span>

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

<span data-ttu-id="7ad2a-217">Ora solo gli utenti che eseguono l'autenticazione con l'autenticazione a più fattori possono accedere alla pagina o al sito Web.</span><span class="sxs-lookup"><span data-stu-id="7ad2a-217">Now only users that authenticate with MFA can access the page or website.</span></span> <span data-ttu-id="7ad2a-218">Se vengono usati diversi tipi di autenticazione a più fattori o se 2FA è corretto, l' `amr` attestazione avrà valori diversi e deve essere elaborata correttamente.</span><span class="sxs-lookup"><span data-stu-id="7ad2a-218">If different MFA types are used or if 2FA is okay, the `amr` claim will have different values and needs to be processed correctly.</span></span> <span data-ttu-id="7ad2a-219">Diversi server OpenID Connect restituiscono anche valori diversi per questa attestazione e potrebbero non seguire la specifica dei [valori di riferimento del metodo di autenticazione](https://tools.ietf.org/html/draft-ietf-oauth-amr-values-08) .</span><span class="sxs-lookup"><span data-stu-id="7ad2a-219">Different OpenID Connect servers also return different values for this claim and might not follow the [Authentication Method Reference Values](https://tools.ietf.org/html/draft-ietf-oauth-amr-values-08) specification.</span></span>

<span data-ttu-id="7ad2a-220">Quando si esegue l'accesso senza autenticazione a più fattori, ad esempio usando solo una password:</span><span class="sxs-lookup"><span data-stu-id="7ad2a-220">When logging in without MFA (for example, using just a password):</span></span>

* <span data-ttu-id="7ad2a-221">Il `amr` valore di è `pwd` :</span><span class="sxs-lookup"><span data-stu-id="7ad2a-221">The `amr` has the `pwd` value:</span></span>

    ![require_mfa_oidc_02.png](mfa/_static/require_mfa_oidc_02.png)

* <span data-ttu-id="7ad2a-223">Accesso negato:</span><span class="sxs-lookup"><span data-stu-id="7ad2a-223">Access is denied:</span></span>

    ![require_mfa_oidc_03.png](mfa/_static/require_mfa_oidc_03.png)

<span data-ttu-id="7ad2a-225">In alternativa, accedere usando OTP con Identity :</span><span class="sxs-lookup"><span data-stu-id="7ad2a-225">Alternatively, logging in using OTP with Identity:</span></span>

![require_mfa_oidc_01.png](mfa/_static/require_mfa_oidc_01.png)

## <a name="additional-resources"></a><span data-ttu-id="7ad2a-227">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="7ad2a-227">Additional resources</span></span>

* [<span data-ttu-id="7ad2a-228">Abilitare la generazione di codice a matrice per le app TOTP Authenticator in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7ad2a-228">Enable QR Code generation for TOTP authenticator apps in ASP.NET Core</span></span>](xref:security/authentication/identity-enable-qrcodes)
* [<span data-ttu-id="7ad2a-229">Opzioni di autenticazione con password per Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="7ad2a-229">Passwordless authentication options for Azure Active Directory</span></span>](/azure/active-directory/authentication/concept-authentication-passwordless)
* [<span data-ttu-id="7ad2a-230">Libreria .NET FIDO2 per l'attestazione e l'attestazione FIDO2/WebAuth con .NET</span><span class="sxs-lookup"><span data-stu-id="7ad2a-230">FIDO2 .NET library for FIDO2 / WebAuthn Attestation and Assertion using .NET</span></span>](https://github.com/abergs/fido2-net-lib)
* [<span data-ttu-id="7ad2a-231">Webauthn awesome</span><span class="sxs-lookup"><span data-stu-id="7ad2a-231">WebAuthn Awesome</span></span>](https://github.com/herrjemand/awesome-webauthn)
