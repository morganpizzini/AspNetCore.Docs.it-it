---
title: Configurare ASP.NET CoreIdentity
author: AdrienTorris
description: Comprendere ASP.NET Core Identity valori predefiniti e informazioni su come configurare le Identity proprietà per l'utilizzo di valori personalizzati.
ms.author: riande
ms.date: 02/11/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/identity-configuration
ms.openlocfilehash: 95c19b671602b45ba217dcb551110854cbbee359
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/26/2020
ms.locfileid: "85408968"
---
# <a name="configure-aspnet-core-identity"></a><span data-ttu-id="2966c-103">Configurare ASP.NET CoreIdentity</span><span class="sxs-lookup"><span data-stu-id="2966c-103">Configure ASP.NET Core Identity</span></span>

<span data-ttu-id="2966c-104">ASP.NET Core Identity utilizza i valori predefiniti per le impostazioni, ad esempio i criteri password, il blocco e la configurazione dei cookie.</span><span class="sxs-lookup"><span data-stu-id="2966c-104">ASP.NET Core Identity uses default values for settings such as password policy, lockout, and cookie configuration.</span></span> <span data-ttu-id="2966c-105">È possibile eseguire l'override di queste impostazioni nella `Startup` classe.</span><span class="sxs-lookup"><span data-stu-id="2966c-105">These settings can be overridden in the `Startup` class.</span></span>

## <a name="identity-options"></a>Identity<span data-ttu-id="2966c-106">Opzioni</span><span class="sxs-lookup"><span data-stu-id="2966c-106"> options</span></span>

<span data-ttu-id="2966c-107">La classe [IdentityOptions](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) rappresenta le opzioni che possono essere utilizzate per configurare il Identity sistema.</span><span class="sxs-lookup"><span data-stu-id="2966c-107">The [IdentityOptions](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) class represents the options that can be used to configure the Identity system.</span></span> <span data-ttu-id="2966c-108">`IdentityOptions`deve essere impostato **dopo** la chiamata di `AddIdentity` o `AddDefaultIdentity` .</span><span class="sxs-lookup"><span data-stu-id="2966c-108">`IdentityOptions` must be set **after** calling `AddIdentity` or `AddDefaultIdentity`.</span></span>

### <a name="claims-identity"></a><span data-ttu-id="2966c-109">SostieneIdentity</span><span class="sxs-lookup"><span data-stu-id="2966c-109">Claims Identity</span></span>

<span data-ttu-id="2966c-110">[IdentityOptions. ClaimsIdentity](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.claimsidentity) specifica il [ClaimsIdentityOptions](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions) con le proprietà mostrate nella tabella seguente.</span><span class="sxs-lookup"><span data-stu-id="2966c-110">[IdentityOptions.ClaimsIdentity](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.claimsidentity) specifies the [ClaimsIdentityOptions](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions) with the properties shown in the following table.</span></span>

| <span data-ttu-id="2966c-111">Proprietà</span><span class="sxs-lookup"><span data-stu-id="2966c-111">Property</span></span> | <span data-ttu-id="2966c-112">Descrizione</span><span class="sxs-lookup"><span data-stu-id="2966c-112">Description</span></span> | <span data-ttu-id="2966c-113">Predefinito</span><span class="sxs-lookup"><span data-stu-id="2966c-113">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="2966c-114">RoleClaimType</span><span class="sxs-lookup"><span data-stu-id="2966c-114">RoleClaimType</span></span>](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions.roleclaimtype) | <span data-ttu-id="2966c-115">Ottiene o imposta il tipo di attestazione utilizzato per un'attestazione di ruolo.</span><span class="sxs-lookup"><span data-stu-id="2966c-115">Gets or sets the claim type used for a role claim.</span></span> | [<span data-ttu-id="2966c-116">ClaimTypes. Role</span><span class="sxs-lookup"><span data-stu-id="2966c-116">ClaimTypes.Role</span></span>](/dotnet/api/system.security.claims.claimtypes.role) |
| [<span data-ttu-id="2966c-117">SecurityStampClaimType</span><span class="sxs-lookup"><span data-stu-id="2966c-117">SecurityStampClaimType</span></span>](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions.securitystampclaimtype) | <span data-ttu-id="2966c-118">Ottiene o imposta il tipo di attestazione utilizzato per l'attestazione del timbro di sicurezza.</span><span class="sxs-lookup"><span data-stu-id="2966c-118">Gets or sets the claim type used for the security stamp claim.</span></span> | `AspNet.Identity.SecurityStamp` |
| [<span data-ttu-id="2966c-119">UserIdClaimType</span><span class="sxs-lookup"><span data-stu-id="2966c-119">UserIdClaimType</span></span>](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions.useridclaimtype) | <span data-ttu-id="2966c-120">Ottiene o imposta il tipo di attestazione utilizzato per l'attestazione dell'identificatore utente.</span><span class="sxs-lookup"><span data-stu-id="2966c-120">Gets or sets the claim type used for the user identifier claim.</span></span> | [<span data-ttu-id="2966c-121">ClaimTypes. NameIdentifier</span><span class="sxs-lookup"><span data-stu-id="2966c-121">ClaimTypes.NameIdentifier</span></span>](/dotnet/api/system.security.claims.claimtypes.nameidentifier) |
| [<span data-ttu-id="2966c-122">UserNameClaimType</span><span class="sxs-lookup"><span data-stu-id="2966c-122">UserNameClaimType</span></span>](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions.usernameclaimtype) | <span data-ttu-id="2966c-123">Ottiene o imposta il tipo di attestazione utilizzato per l'attestazione del nome utente.</span><span class="sxs-lookup"><span data-stu-id="2966c-123">Gets or sets the claim type used for the user name claim.</span></span> | [<span data-ttu-id="2966c-124">ClaimTypes.Name</span><span class="sxs-lookup"><span data-stu-id="2966c-124">ClaimTypes.Name</span></span>](/dotnet/api/system.security.claims.claimtypes.name) |

### <a name="lockout"></a><span data-ttu-id="2966c-125">Blocco</span><span class="sxs-lookup"><span data-stu-id="2966c-125">Lockout</span></span>

<span data-ttu-id="2966c-126">Il blocco viene impostato nel metodo [PasswordSignInAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.passwordsigninasync#Microsoft_AspNetCore_Identity_SignInManager_1_PasswordSignInAsync_System_String_System_String_System_Boolean_System_Boolean_) :</span><span class="sxs-lookup"><span data-stu-id="2966c-126">Lockout is set in the [PasswordSignInAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.passwordsigninasync#Microsoft_AspNetCore_Identity_SignInManager_1_PasswordSignInAsync_System_String_System_String_System_Boolean_System_Boolean_) method:</span></span>

[!code-csharp[](identity-configuration/sample/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=9)]

<span data-ttu-id="2966c-127">Il codice precedente è basato sul `Login` Identity modello.</span><span class="sxs-lookup"><span data-stu-id="2966c-127">The preceding code is based on the `Login` Identity template.</span></span> 

<span data-ttu-id="2966c-128">Le opzioni di blocco sono impostate in `StartUp.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="2966c-128">Lockout options are set in `StartUp.ConfigureServices`:</span></span>

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_lock)]

<span data-ttu-id="2966c-129">Il codice precedente imposta il [IdentityOptions](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) [LockoutOptions](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions) IdentityOptions con i valori predefiniti.</span><span class="sxs-lookup"><span data-stu-id="2966c-129">The preceding code sets the [IdentityOptions](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) [LockoutOptions](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions) with default values.</span></span>

<span data-ttu-id="2966c-130">Una corretta autenticazione Reimposta il numero di tentativi di accesso non riusciti e reimposta il clock.</span><span class="sxs-lookup"><span data-stu-id="2966c-130">A successful authentication resets the failed access attempts count and resets the clock.</span></span>

<span data-ttu-id="2966c-131">[IdentityOptions. lockout](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.lockout) specifica il [LockoutOptions](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions) con le proprietà visualizzate nella tabella.</span><span class="sxs-lookup"><span data-stu-id="2966c-131">[IdentityOptions.Lockout](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.lockout) specifies the [LockoutOptions](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions) with the properties shown in the table.</span></span>

| <span data-ttu-id="2966c-132">Proprietà</span><span class="sxs-lookup"><span data-stu-id="2966c-132">Property</span></span> | <span data-ttu-id="2966c-133">Descrizione</span><span class="sxs-lookup"><span data-stu-id="2966c-133">Description</span></span> | <span data-ttu-id="2966c-134">Predefinito</span><span class="sxs-lookup"><span data-stu-id="2966c-134">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="2966c-135">AllowedForNewUsers</span><span class="sxs-lookup"><span data-stu-id="2966c-135">AllowedForNewUsers</span></span>](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.allowedfornewusers) | <span data-ttu-id="2966c-136">Determina se un nuovo utente può essere bloccato.</span><span class="sxs-lookup"><span data-stu-id="2966c-136">Determines if a new user can be locked out.</span></span> | `true` |
| [<span data-ttu-id="2966c-137">DefaultLockoutTimeSpan</span><span class="sxs-lookup"><span data-stu-id="2966c-137">DefaultLockoutTimeSpan</span></span>](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.defaultlockouttimespan) | <span data-ttu-id="2966c-138">La quantità di tempo in cui un utente viene bloccato quando si verifica un blocco.</span><span class="sxs-lookup"><span data-stu-id="2966c-138">The amount of time a user is locked out when a lockout occurs.</span></span> | <span data-ttu-id="2966c-139">5 minuti</span><span class="sxs-lookup"><span data-stu-id="2966c-139">5 minutes</span></span> |
| [<span data-ttu-id="2966c-140">MaxFailedAccessAttempts</span><span class="sxs-lookup"><span data-stu-id="2966c-140">MaxFailedAccessAttempts</span></span>](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.maxfailedaccessattempts) | <span data-ttu-id="2966c-141">Il numero di tentativi di accesso non riusciti finché un utente non viene bloccato, se il blocco è abilitato.</span><span class="sxs-lookup"><span data-stu-id="2966c-141">The number of failed access attempts until a user is locked out, if lockout is enabled.</span></span> | <span data-ttu-id="2966c-142">5</span><span class="sxs-lookup"><span data-stu-id="2966c-142">5</span></span> |

### <a name="password"></a><span data-ttu-id="2966c-143">Password</span><span class="sxs-lookup"><span data-stu-id="2966c-143">Password</span></span>

<span data-ttu-id="2966c-144">Per impostazione predefinita, Identity richiede che le password contengano un carattere maiuscolo, un carattere minuscolo, una cifra e un carattere non alfanumerico.</span><span class="sxs-lookup"><span data-stu-id="2966c-144">By default, Identity requires that passwords contain an uppercase character, lowercase character, a digit, and a non-alphanumeric character.</span></span> <span data-ttu-id="2966c-145">La lunghezza delle password deve essere di almeno sei caratteri.</span><span class="sxs-lookup"><span data-stu-id="2966c-145">Passwords must be at least six characters long.</span></span> <span data-ttu-id="2966c-146">È possibile impostare [PasswordOptions](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions) in `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="2966c-146">[PasswordOptions](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions) can be set in `Startup.ConfigureServices`.</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_pw)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](identity/sample/src/ASPNETv2-IdentityDemo-Configuration/Startup.cs?range=29-37,50-52)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](identity/sample/src/ASPNET-IdentityDemo-PrimaryKeysConfig/Startup.cs?range=58-65,84)]

::: moniker-end

<span data-ttu-id="2966c-147">[IdentityOptions. password](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.password) specifica il [PasswordOptions](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions) con le proprietà visualizzate nella tabella.</span><span class="sxs-lookup"><span data-stu-id="2966c-147">[IdentityOptions.Password](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.password) specifies the [PasswordOptions](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions) with the properties shown in the table.</span></span>

::: moniker range=">= aspnetcore-2.0"

| <span data-ttu-id="2966c-148">Proprietà</span><span class="sxs-lookup"><span data-stu-id="2966c-148">Property</span></span> | <span data-ttu-id="2966c-149">Descrizione</span><span class="sxs-lookup"><span data-stu-id="2966c-149">Description</span></span> | <span data-ttu-id="2966c-150">Predefinito</span><span class="sxs-lookup"><span data-stu-id="2966c-150">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="2966c-151">RequireDigit</span><span class="sxs-lookup"><span data-stu-id="2966c-151">RequireDigit</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requiredigit) | <span data-ttu-id="2966c-152">Richiede un numero compreso tra 0-9 nella password.</span><span class="sxs-lookup"><span data-stu-id="2966c-152">Requires a number between 0-9 in the password.</span></span> | `true` |
| [<span data-ttu-id="2966c-153">RequiredLength</span><span class="sxs-lookup"><span data-stu-id="2966c-153">RequiredLength</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requiredlength) | <span data-ttu-id="2966c-154">Lunghezza minima della password.</span><span class="sxs-lookup"><span data-stu-id="2966c-154">The minimum length of the password.</span></span> | <span data-ttu-id="2966c-155">6</span><span class="sxs-lookup"><span data-stu-id="2966c-155">6</span></span> |
| [<span data-ttu-id="2966c-156">RequireLowercase</span><span class="sxs-lookup"><span data-stu-id="2966c-156">RequireLowercase</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requirelowercase) | <span data-ttu-id="2966c-157">Richiede un carattere minuscolo nella password.</span><span class="sxs-lookup"><span data-stu-id="2966c-157">Requires a lowercase character in the password.</span></span> | `true` |
| [<span data-ttu-id="2966c-158">RequireNonAlphanumeric</span><span class="sxs-lookup"><span data-stu-id="2966c-158">RequireNonAlphanumeric</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requirenonalphanumeric) | <span data-ttu-id="2966c-159">Richiede un carattere non alfanumerico nella password.</span><span class="sxs-lookup"><span data-stu-id="2966c-159">Requires a non-alphanumeric character in the password.</span></span> | `true` |
| [<span data-ttu-id="2966c-160">RequiredUniqueChars</span><span class="sxs-lookup"><span data-stu-id="2966c-160">RequiredUniqueChars</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requireduniquechars) | <span data-ttu-id="2966c-161">Si applica solo a ASP.NET Core 2,0 o versione successiva.</span><span class="sxs-lookup"><span data-stu-id="2966c-161">Only applies to ASP.NET Core 2.0 or later.</span></span><br><br> <span data-ttu-id="2966c-162">Richiede il numero di caratteri distinti nella password.</span><span class="sxs-lookup"><span data-stu-id="2966c-162">Requires the number of distinct characters in the password.</span></span> | <span data-ttu-id="2966c-163">1</span><span class="sxs-lookup"><span data-stu-id="2966c-163">1</span></span> |
| [<span data-ttu-id="2966c-164">RequireUppercase</span><span class="sxs-lookup"><span data-stu-id="2966c-164">RequireUppercase</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requireuppercase) | <span data-ttu-id="2966c-165">Richiede un carattere maiuscolo nella password.</span><span class="sxs-lookup"><span data-stu-id="2966c-165">Requires an uppercase character in the password.</span></span> | `true` |

::: moniker-end

::: moniker range="< aspnetcore-2.0"

| <span data-ttu-id="2966c-166">Proprietà</span><span class="sxs-lookup"><span data-stu-id="2966c-166">Property</span></span> | <span data-ttu-id="2966c-167">Descrizione</span><span class="sxs-lookup"><span data-stu-id="2966c-167">Description</span></span> | <span data-ttu-id="2966c-168">Predefinito</span><span class="sxs-lookup"><span data-stu-id="2966c-168">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="2966c-169">RequireDigit</span><span class="sxs-lookup"><span data-stu-id="2966c-169">RequireDigit</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requiredigit) | <span data-ttu-id="2966c-170">Richiede un numero compreso tra 0-9 nella password.</span><span class="sxs-lookup"><span data-stu-id="2966c-170">Requires a number between 0-9 in the password.</span></span> | `true` |
| [<span data-ttu-id="2966c-171">RequiredLength</span><span class="sxs-lookup"><span data-stu-id="2966c-171">RequiredLength</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requiredlength) | <span data-ttu-id="2966c-172">Lunghezza minima della password.</span><span class="sxs-lookup"><span data-stu-id="2966c-172">The minimum length of the password.</span></span> | <span data-ttu-id="2966c-173">6</span><span class="sxs-lookup"><span data-stu-id="2966c-173">6</span></span> |
| [<span data-ttu-id="2966c-174">RequireLowercase</span><span class="sxs-lookup"><span data-stu-id="2966c-174">RequireLowercase</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requirelowercase) | <span data-ttu-id="2966c-175">Richiede un carattere minuscolo nella password.</span><span class="sxs-lookup"><span data-stu-id="2966c-175">Requires a lowercase character in the password.</span></span> | `true` |
| [<span data-ttu-id="2966c-176">RequireNonAlphanumeric</span><span class="sxs-lookup"><span data-stu-id="2966c-176">RequireNonAlphanumeric</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requirenonalphanumeric) | <span data-ttu-id="2966c-177">Richiede un carattere non alfanumerico nella password.</span><span class="sxs-lookup"><span data-stu-id="2966c-177">Requires a non-alphanumeric character in the password.</span></span> | `true` |
| [<span data-ttu-id="2966c-178">RequireUppercase</span><span class="sxs-lookup"><span data-stu-id="2966c-178">RequireUppercase</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requireuppercase) | <span data-ttu-id="2966c-179">Richiede un carattere maiuscolo nella password.</span><span class="sxs-lookup"><span data-stu-id="2966c-179">Requires an uppercase character in the password.</span></span> | `true` |

::: moniker-end

### <a name="sign-in"></a><span data-ttu-id="2966c-180">Accesso</span><span class="sxs-lookup"><span data-stu-id="2966c-180">Sign-in</span></span>

<span data-ttu-id="2966c-181">Il codice seguente imposta `SignIn` le impostazioni (sui valori predefiniti):</span><span class="sxs-lookup"><span data-stu-id="2966c-181">The following code sets `SignIn` settings (to default values):</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_si)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](identity/sample/src/ASPNETv2-IdentityDemo-Configuration/Startup.cs?range=29-30,44-46,50-52)] 

::: moniker-end

<span data-ttu-id="2966c-182">[IdentityOptions. SignIn](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.signin) specifica il [SignInOptions](/dotnet/api/microsoft.aspnetcore.identity.signinoptions) con le proprietà visualizzate nella tabella.</span><span class="sxs-lookup"><span data-stu-id="2966c-182">[IdentityOptions.SignIn](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.signin) specifies the [SignInOptions](/dotnet/api/microsoft.aspnetcore.identity.signinoptions) with the properties shown in the table.</span></span>

| <span data-ttu-id="2966c-183">Proprietà</span><span class="sxs-lookup"><span data-stu-id="2966c-183">Property</span></span> | <span data-ttu-id="2966c-184">Descrizione</span><span class="sxs-lookup"><span data-stu-id="2966c-184">Description</span></span> | <span data-ttu-id="2966c-185">Predefinito</span><span class="sxs-lookup"><span data-stu-id="2966c-185">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="2966c-186">RequireConfirmedEmail</span><span class="sxs-lookup"><span data-stu-id="2966c-186">RequireConfirmedEmail</span></span>](/dotnet/api/microsoft.aspnetcore.identity.signinoptions.requireconfirmedemail) | <span data-ttu-id="2966c-187">Richiede un messaggio di posta elettronica confermato per l'accesso.</span><span class="sxs-lookup"><span data-stu-id="2966c-187">Requires a confirmed email to sign in.</span></span> | `false` |
| [<span data-ttu-id="2966c-188">RequireConfirmedPhoneNumber</span><span class="sxs-lookup"><span data-stu-id="2966c-188">RequireConfirmedPhoneNumber</span></span>](/dotnet/api/microsoft.aspnetcore.identity.signinoptions.requireconfirmedphonenumber) | <span data-ttu-id="2966c-189">Richiede un numero di telefono confermato per l'accesso.</span><span class="sxs-lookup"><span data-stu-id="2966c-189">Requires a confirmed phone number to sign in.</span></span> | `false` |

### <a name="tokens"></a><span data-ttu-id="2966c-190">Tokens</span><span class="sxs-lookup"><span data-stu-id="2966c-190">Tokens</span></span>

<span data-ttu-id="2966c-191">[IdentityOptions. Tokens](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.tokens) specifica il [TokenOptions](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions) con le proprietà visualizzate nella tabella.</span><span class="sxs-lookup"><span data-stu-id="2966c-191">[IdentityOptions.Tokens](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.tokens) specifies the [TokenOptions](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions) with the properties shown in the table.</span></span>

|                                                        <span data-ttu-id="2966c-192">Proprietà</span><span class="sxs-lookup"><span data-stu-id="2966c-192">Property</span></span>                                                         |                                                                                      <span data-ttu-id="2966c-193">Descrizione</span><span class="sxs-lookup"><span data-stu-id="2966c-193">Description</span></span>                                                                                      |
|-------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|     [<span data-ttu-id="2966c-194">AuthenticatorTokenProvider</span><span class="sxs-lookup"><span data-stu-id="2966c-194">AuthenticatorTokenProvider</span></span>](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.authenticatortokenprovider)     |                                       <span data-ttu-id="2966c-195">Ottiene o imposta l'oggetto `AuthenticatorTokenProvider` usato per convalidare gli accessi a due fattori con un autenticatore.</span><span class="sxs-lookup"><span data-stu-id="2966c-195">Gets or sets the `AuthenticatorTokenProvider` used to validate two-factor sign-ins with an authenticator.</span></span>                                       |
|       [<span data-ttu-id="2966c-196">ChangeEmailTokenProvider</span><span class="sxs-lookup"><span data-stu-id="2966c-196">ChangeEmailTokenProvider</span></span>](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.changeemailtokenprovider)       |                                     <span data-ttu-id="2966c-197">Ottiene o imposta l'oggetto `ChangeEmailTokenProvider` usato per generare token usati nei messaggi di posta elettronica di conferma della modifica della posta elettronica.</span><span class="sxs-lookup"><span data-stu-id="2966c-197">Gets or sets the `ChangeEmailTokenProvider` used to generate tokens used in email change confirmation emails.</span></span>                                     |
| [<span data-ttu-id="2966c-198">ChangePhoneNumberTokenProvider</span><span class="sxs-lookup"><span data-stu-id="2966c-198">ChangePhoneNumberTokenProvider</span></span>](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.changephonenumbertokenprovider) |                                      <span data-ttu-id="2966c-199">Ottiene o imposta l'oggetto `ChangePhoneNumberTokenProvider` utilizzato per generare i token utilizzati per la modifica dei numeri di telefono.</span><span class="sxs-lookup"><span data-stu-id="2966c-199">Gets or sets the `ChangePhoneNumberTokenProvider` used to generate tokens used when changing phone numbers.</span></span>                                      |
| [<span data-ttu-id="2966c-200">EmailConfirmationTokenProvider</span><span class="sxs-lookup"><span data-stu-id="2966c-200">EmailConfirmationTokenProvider</span></span>](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.emailconfirmationtokenprovider) |                                             <span data-ttu-id="2966c-201">Ottiene o imposta il provider di token utilizzato per generare i token utilizzati nei messaggi di posta elettronica di conferma dell'account.</span><span class="sxs-lookup"><span data-stu-id="2966c-201">Gets or sets the token provider used to generate tokens used in account confirmation emails.</span></span>                                              |
|     [<span data-ttu-id="2966c-202">PasswordResetTokenProvider</span><span class="sxs-lookup"><span data-stu-id="2966c-202">PasswordResetTokenProvider</span></span>](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.passwordresettokenprovider)     | <span data-ttu-id="2966c-203">Ottiene o imposta l' [oggetto \<TUser> IUserTwoFactorTokenProvider](/dotnet/api/microsoft.aspnetcore.identity.iusertwofactortokenprovider-1) utilizzato per generare i token utilizzati nei messaggi di posta elettronica di reimpostazione della password.</span><span class="sxs-lookup"><span data-stu-id="2966c-203">Gets or sets the [IUserTwoFactorTokenProvider\<TUser>](/dotnet/api/microsoft.aspnetcore.identity.iusertwofactortokenprovider-1) used to generate tokens used in password reset emails.</span></span> |
|                    [<span data-ttu-id="2966c-204">ProviderMap</span><span class="sxs-lookup"><span data-stu-id="2966c-204">ProviderMap</span></span>](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.providermap)                    |                <span data-ttu-id="2966c-205">Utilizzato per costruire un [provider di token utente](/dotnet/api/microsoft.aspnetcore.identity.tokenproviderdescriptor) con la chiave utilizzata come nome del provider.</span><span class="sxs-lookup"><span data-stu-id="2966c-205">Used to construct a [User Token Provider](/dotnet/api/microsoft.aspnetcore.identity.tokenproviderdescriptor) with the key used as the provider's name.</span></span>                 |

### <a name="user"></a><span data-ttu-id="2966c-206">Utente</span><span class="sxs-lookup"><span data-stu-id="2966c-206">User</span></span>

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_user)]

<span data-ttu-id="2966c-207">[IdentityOptions. User](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.user) specifica il [UserOptions](/dotnet/api/microsoft.aspnetcore.identity.useroptions) con le proprietà visualizzate nella tabella.</span><span class="sxs-lookup"><span data-stu-id="2966c-207">[IdentityOptions.User](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.user) specifies the [UserOptions](/dotnet/api/microsoft.aspnetcore.identity.useroptions) with the properties shown in the table.</span></span>

| <span data-ttu-id="2966c-208">Proprietà</span><span class="sxs-lookup"><span data-stu-id="2966c-208">Property</span></span> | <span data-ttu-id="2966c-209">Descrizione</span><span class="sxs-lookup"><span data-stu-id="2966c-209">Description</span></span> | <span data-ttu-id="2966c-210">Predefinito</span><span class="sxs-lookup"><span data-stu-id="2966c-210">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="2966c-211">AllowedUserNameCharacters</span><span class="sxs-lookup"><span data-stu-id="2966c-211">AllowedUserNameCharacters</span></span>](/dotnet/api/microsoft.aspnetcore.identity.useroptions.allowedusernamecharacters) | <span data-ttu-id="2966c-212">Caratteri consentiti nel nome utente.</span><span class="sxs-lookup"><span data-stu-id="2966c-212">Allowed characters in the username.</span></span> | <span data-ttu-id="2966c-213">abcdefghijklmnopqrstuvwxyz</span><span class="sxs-lookup"><span data-stu-id="2966c-213">abcdefghijklmnopqrstuvwxyz</span></span><br><span data-ttu-id="2966c-214">ABCDEFGHIJKLMNOPQRSTUVWXYZ</span><span class="sxs-lookup"><span data-stu-id="2966c-214">ABCDEFGHIJKLMNOPQRSTUVWXYZ</span></span><br><span data-ttu-id="2966c-215">0123456789</span><span class="sxs-lookup"><span data-stu-id="2966c-215">0123456789</span></span><br><span data-ttu-id="2966c-216">-.\_@+</span><span class="sxs-lookup"><span data-stu-id="2966c-216">-.\_@+</span></span> |
| [<span data-ttu-id="2966c-217">RequireUniqueEmail</span><span class="sxs-lookup"><span data-stu-id="2966c-217">RequireUniqueEmail</span></span>](/dotnet/api/microsoft.aspnetcore.identity.useroptions.requireuniqueemail) | <span data-ttu-id="2966c-218">Richiede che ogni utente disponga di un messaggio di posta elettronica univoco.</span><span class="sxs-lookup"><span data-stu-id="2966c-218">Requires each user to have a unique email.</span></span> | `false` |

### <a name="cookie-settings"></a><span data-ttu-id="2966c-219">Impostazioni cookie</span><span class="sxs-lookup"><span data-stu-id="2966c-219">Cookie settings</span></span>

<span data-ttu-id="2966c-220">Configurare il cookie dell'app in `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="2966c-220">Configure the app's cookie in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="2966c-221">[ConfigureApplicationCookie](/dotnet/api/microsoft.extensions.dependencyinjection.identityservicecollectionextensions.configureapplicationcookie#Microsoft_Extensions_DependencyInjection_IdentityServiceCollectionExtensions_ConfigureApplicationCookie_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_Cookies_CookieAuthenticationOptions__) deve essere chiamato **dopo aver** chiamato `AddIdentity` o `AddDefaultIdentity` .</span><span class="sxs-lookup"><span data-stu-id="2966c-221">[ConfigureApplicationCookie](/dotnet/api/microsoft.extensions.dependencyinjection.identityservicecollectionextensions.configureapplicationcookie#Microsoft_Extensions_DependencyInjection_IdentityServiceCollectionExtensions_ConfigureApplicationCookie_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_Cookies_CookieAuthenticationOptions__) must be called **after** calling `AddIdentity` or `AddDefaultIdentity`.</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_cookie)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](identity/sample/src/ASPNETv2-IdentityDemo-Configuration/Startup.cs?name=snippet_configurecookie)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](identity/sample/src/ASPNET-IdentityDemo-PrimaryKeysConfig/Startup.cs?range=58-59,72-80,84)]

::: moniker-end

<span data-ttu-id="2966c-222">Per ulteriori informazioni, vedere [CookieAuthenticationOptions](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions).</span><span class="sxs-lookup"><span data-stu-id="2966c-222">For more information, see [CookieAuthenticationOptions](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions).</span></span>

## <a name="password-hasher-options"></a><span data-ttu-id="2966c-223">Opzioni dell'hash delle password</span><span class="sxs-lookup"><span data-stu-id="2966c-223">Password Hasher options</span></span>

<span data-ttu-id="2966c-224"><xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions>Ottiene e imposta le opzioni per l'hashing delle password.</span><span class="sxs-lookup"><span data-stu-id="2966c-224"><xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions> gets and sets options for password hashing.</span></span>

| <span data-ttu-id="2966c-225">Opzione</span><span class="sxs-lookup"><span data-stu-id="2966c-225">Option</span></span> | <span data-ttu-id="2966c-226">Description</span><span class="sxs-lookup"><span data-stu-id="2966c-226">Description</span></span> |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.CompatibilityMode> | <span data-ttu-id="2966c-227">Modalità di compatibilità utilizzata per l'hashing di nuove password.</span><span class="sxs-lookup"><span data-stu-id="2966c-227">The compatibility mode used when hashing new passwords.</span></span> <span data-ttu-id="2966c-228">Il valore predefinito è <xref:Microsoft.AspNetCore.Identity.PasswordHasherCompatibilityMode.IdentityV3>.</span><span class="sxs-lookup"><span data-stu-id="2966c-228">Defaults to <xref:Microsoft.AspNetCore.Identity.PasswordHasherCompatibilityMode.IdentityV3>.</span></span> <span data-ttu-id="2966c-229">Il primo byte di una password con hash, denominato *marcatore di formato*, specifica la versione dell'algoritmo hash utilizzato per l'hash della password.</span><span class="sxs-lookup"><span data-stu-id="2966c-229">The first byte of a hashed password, called a *format marker*, specifies the version of the hashing algorithm used to hash the password.</span></span> <span data-ttu-id="2966c-230">Quando si verifica una password rispetto a un hash, il <xref:Microsoft.AspNetCore.Identity.PasswordHasher`1.VerifyHashedPassword*> metodo seleziona l'algoritmo corretto in base al primo byte.</span><span class="sxs-lookup"><span data-stu-id="2966c-230">When verifying a password against a hash, the <xref:Microsoft.AspNetCore.Identity.PasswordHasher`1.VerifyHashedPassword*> method selects the correct algorithm based on the first byte.</span></span> <span data-ttu-id="2966c-231">Un client è in grado di eseguire l'autenticazione indipendentemente dalla versione dell'algoritmo utilizzata per eseguire l'hashing della password.</span><span class="sxs-lookup"><span data-stu-id="2966c-231">A client is able to authenticate regardless of which version of the algorithm was used to hash the password.</span></span> <span data-ttu-id="2966c-232">L'impostazione della modalità di compatibilità influiscono sull'hashing delle *nuove password*.</span><span class="sxs-lookup"><span data-stu-id="2966c-232">Setting the compatibility mode affects the hashing of *new passwords*.</span></span> |
| <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.IterationCount> | <span data-ttu-id="2966c-233">Numero di iterazioni utilizzate quando si esegue l'hashing delle password utilizzando PBKDF2.</span><span class="sxs-lookup"><span data-stu-id="2966c-233">The number of iterations used when hashing passwords using PBKDF2.</span></span> <span data-ttu-id="2966c-234">Questo valore viene utilizzato solo quando <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.CompatibilityMode> è impostato su <xref:Microsoft.AspNetCore.Identity.PasswordHasherCompatibilityMode.IdentityV3> .</span><span class="sxs-lookup"><span data-stu-id="2966c-234">This value is only used when the <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.CompatibilityMode> is set to <xref:Microsoft.AspNetCore.Identity.PasswordHasherCompatibilityMode.IdentityV3>.</span></span> <span data-ttu-id="2966c-235">Il valore deve essere un numero intero positivo e il valore predefinito è `10000` .</span><span class="sxs-lookup"><span data-stu-id="2966c-235">The value must be a positive integer and defaults to `10000`.</span></span> |

<span data-ttu-id="2966c-236">Nell'esempio seguente, l'oggetto <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.IterationCount> è impostato su `12000` in `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="2966c-236">In the following example, the <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.IterationCount> is set to `12000` in `Startup.ConfigureServices`:</span></span>

```csharp
// using Microsoft.AspNetCore.Identity;

services.Configure<PasswordHasherOptions>(option =>
{
    option.IterationCount = 12000;
});
```
