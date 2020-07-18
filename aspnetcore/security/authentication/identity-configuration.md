---
title: Configurare ASP.NET CoreIdentity
author: AdrienTorris
description: Comprendere ASP.NET Core Identity valori predefiniti e informazioni su come configurare le Identity proprietà per l'utilizzo di valori personalizzati.
ms.author: riande
ms.custom: mvc
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
ms.openlocfilehash: 262101594d57ad63bb0bac4da3cefa8d8d380908
ms.sourcegitcommit: 3544941682869734ea0113e24e02ed0ec9e1a9ec
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/18/2020
ms.locfileid: "86464527"
---
# <a name="configure-aspnet-core-identity"></a>Configurare ASP.NET CoreIdentity

ASP.NET Core Identity utilizza i valori predefiniti per le impostazioni, ad esempio i criteri password, il blocco e la configurazione dei cookie. È possibile eseguire l'override di queste impostazioni nella `Startup` classe.

## <a name="identity-options"></a>IdentityOpzioni

La classe [ Identity options](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) rappresenta le opzioni che possono essere utilizzate per configurare il Identity sistema. `IdentityOptions`deve essere impostato **dopo** la chiamata di `AddIdentity` o `AddDefaultIdentity` .

### <a name="claims-identity"></a>SostieneIdentity

[ Identity Options. Claims Identity ](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.claimsidentity) specifica [le Identity Opzioni delle attestazioni](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions) con le proprietà mostrate nella tabella seguente.

| Proprietà | Descrizione | Predefinito |
| -------- | ----------- | :-----: |
| [RoleClaimType](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions.roleclaimtype) | Ottiene o imposta il tipo di attestazione utilizzato per un'attestazione di ruolo. | [ClaimTypes. Role](/dotnet/api/system.security.claims.claimtypes.role) |
| [SecurityStampClaimType](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions.securitystampclaimtype) | Ottiene o imposta il tipo di attestazione utilizzato per l'attestazione del timbro di sicurezza. | `AspNet.Identity.SecurityStamp` |
| [UserIdClaimType](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions.useridclaimtype) | Ottiene o imposta il tipo di attestazione utilizzato per l'attestazione dell'identificatore utente. | [ClaimTypes. NameIdentifier](/dotnet/api/system.security.claims.claimtypes.nameidentifier) |
| [UserNameClaimType](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions.usernameclaimtype) | Ottiene o imposta il tipo di attestazione utilizzato per l'attestazione del nome utente. | [ClaimTypes.Name](/dotnet/api/system.security.claims.claimtypes.name) |

### <a name="lockout"></a>Blocco

Il blocco viene impostato nel metodo [PasswordSignInAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.passwordsigninasync#Microsoft_AspNetCore_Identity_SignInManager_1_PasswordSignInAsync_System_String_System_String_System_Boolean_System_Boolean_) :

[!code-csharp[](identity-configuration/sample/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=9)]

Il codice precedente è basato sul `Login` Identity modello. 

Le opzioni di blocco sono impostate in `StartUp.ConfigureServices` :

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_lock)]

Il codice precedente imposta le [ Identity Opzioni](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) [LockoutOptions](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions) con i valori predefiniti.

Una corretta autenticazione Reimposta il numero di tentativi di accesso non riusciti e reimposta il clock.

[ Identity Options. lockout](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.lockout) specifica [LockoutOptions](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions) con le proprietà visualizzate nella tabella.

| Proprietà | Descrizione | Predefinito |
| -------- | ----------- | :-----: |
| [AllowedForNewUsers](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.allowedfornewusers) | Determina se un nuovo utente può essere bloccato. | `true` |
| [DefaultLockoutTimeSpan](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.defaultlockouttimespan) | La quantità di tempo in cui un utente viene bloccato quando si verifica un blocco. | 5 minuti |
| [MaxFailedAccessAttempts](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.maxfailedaccessattempts) | Il numero di tentativi di accesso non riusciti finché un utente non viene bloccato, se il blocco è abilitato. | 5 |

### <a name="password"></a>Password

Per impostazione predefinita, Identity richiede che le password contengano un carattere maiuscolo, un carattere minuscolo, una cifra e un carattere non alfanumerico. La lunghezza delle password deve essere di almeno sei caratteri.

Le password sono configurate con:

* <xref:Microsoft.AspNetCore.Identity.PasswordOptions>in `Startup.ConfigureServices` .
* [ `[StringLength]` attributi](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute) delle `Password` proprietà se è costituito da Identity [impalcature nell'app](xref:security/authentication/scaffold-identity). `InputModel``Password`le proprietà si trovano nei file seguenti:
  * `Areas/Identity/Pages/Account/Register.cshtml.cs`
  * `Areas/Identity/Pages/Account/ResetPassword.cshtml.cs`

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_pw)]

[ Identity Options. password](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.password) specifica [PasswordOptions](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions) con le proprietà visualizzate nella tabella.

| Proprietà | Descrizione | Predefinito |
| -------- | ----------- | :-----: |
| [RequireDigit](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requiredigit) | Richiede un numero compreso tra 0-9 nella password. | `true` |
| [RequiredLength](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requiredlength) | Lunghezza minima della password. | 6 |
| [RequireLowercase](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requirelowercase) | Richiede un carattere minuscolo nella password. | `true` |
| [RequireNonAlphanumeric](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requirenonalphanumeric) | Richiede un carattere non alfanumerico nella password. | `true` |
| [RequiredUniqueChars](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requireduniquechars) | Si applica solo a ASP.NET Core 2,0 o versione successiva.<br><br> Richiede il numero di caratteri distinti nella password. | 1 |
| [RequireUppercase](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requireuppercase) | Richiede un carattere maiuscolo nella password. | `true` |

### <a name="sign-in"></a>Accesso

Il codice seguente imposta `SignIn` le impostazioni (sui valori predefiniti):

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_si)]

[ Identity Options. SignIn](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.signin) specifica [SignInOptions](/dotnet/api/microsoft.aspnetcore.identity.signinoptions) con le proprietà visualizzate nella tabella.

| Proprietà | Descrizione | Predefinito |
| -------- | ----------- | :-----: |
| [RequireConfirmedEmail](/dotnet/api/microsoft.aspnetcore.identity.signinoptions.requireconfirmedemail) | Richiede un messaggio di posta elettronica confermato per l'accesso. | `false` |
| [RequireConfirmedPhoneNumber](/dotnet/api/microsoft.aspnetcore.identity.signinoptions.requireconfirmedphonenumber) | Richiede un numero di telefono confermato per l'accesso. | `false` |

### <a name="tokens"></a>Tokens

[ Identity Options. Tokens](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.tokens) specifica [TokenOptions](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions) con le proprietà visualizzate nella tabella.

| Proprietà | Descrizione |
| -------- | ----------- |
| [AuthenticatorTokenProvider](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.authenticatortokenprovider) | Ottiene o imposta l'oggetto `AuthenticatorTokenProvider` usato per convalidare gli accessi a due fattori con un autenticatore. |
| [ChangeEmailTokenProvider](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.changeemailtokenprovider) | Ottiene o imposta l'oggetto `ChangeEmailTokenProvider` usato per generare token usati nei messaggi di posta elettronica di conferma della modifica della posta elettronica. |
| [ChangePhoneNumberTokenProvider](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.changephonenumbertokenprovider) | Ottiene o imposta l'oggetto `ChangePhoneNumberTokenProvider` utilizzato per generare i token utilizzati per la modifica dei numeri di telefono. |
| [EmailConfirmationTokenProvider](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.emailconfirmationtokenprovider) | Ottiene o imposta il provider di token utilizzato per generare i token utilizzati nei messaggi di posta elettronica di conferma dell'account. |
| [PasswordResetTokenProvider](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.passwordresettokenprovider) | Ottiene o imposta l' [oggetto \<TUser> IUserTwoFactorTokenProvider](/dotnet/api/microsoft.aspnetcore.identity.iusertwofactortokenprovider-1) utilizzato per generare i token utilizzati nei messaggi di posta elettronica di reimpostazione della password. |
| [ProviderMap](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.providermap) | Utilizzato per costruire un [provider di token utente](/dotnet/api/microsoft.aspnetcore.identity.tokenproviderdescriptor) con la chiave utilizzata come nome del provider. |

### <a name="user"></a>Utente

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_user)]

[ Identity Options. User](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.user) specifica [UserOptions](/dotnet/api/microsoft.aspnetcore.identity.useroptions) con le proprietà visualizzate nella tabella.

| Proprietà | Descrizione | Predefinito |
| -------- | ----------- | :-----: |
| [AllowedUserNameCharacters](/dotnet/api/microsoft.aspnetcore.identity.useroptions.allowedusernamecharacters) | Caratteri consentiti nel nome utente. | abcdefghijklmnopqrstuvwxyz<br>ABCDEFGHIJKLMNOPQRSTUVWXYZ<br>0123456789<br>-.\_@+ |
| [RequireUniqueEmail](/dotnet/api/microsoft.aspnetcore.identity.useroptions.requireuniqueemail) | Richiede che ogni utente disponga di un messaggio di posta elettronica univoco. | `false` |

### <a name="cookie-settings"></a>Impostazioni cookie

Configurare il cookie dell'app in `Startup.ConfigureServices` . [ConfigureApplicationCookie](/dotnet/api/microsoft.extensions.dependencyinjection.identityservicecollectionextensions.configureapplicationcookie#Microsoft_Extensions_DependencyInjection_IdentityServiceCollectionExtensions_ConfigureApplicationCookie_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_Cookies_CookieAuthenticationOptions__) deve essere chiamato **dopo aver** chiamato `AddIdentity` o `AddDefaultIdentity` .

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_cookie)]

Per ulteriori informazioni, vedere [CookieAuthenticationOptions](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions).

## <a name="password-hasher-options"></a>Opzioni dell'hash delle password

<xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions>Ottiene e imposta le opzioni per l'hashing delle password.

| Opzione | Descrizione |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.CompatibilityMode> | Modalità di compatibilità utilizzata per l'hashing di nuove password. Il valore predefinito è <xref:Microsoft.AspNetCore.Identity.PasswordHasherCompatibilityMode.IdentityV3>. Il primo byte di una password con hash, denominato *marcatore di formato*, specifica la versione dell'algoritmo hash utilizzato per l'hash della password. Quando si verifica una password rispetto a un hash, il <xref:Microsoft.AspNetCore.Identity.PasswordHasher`1.VerifyHashedPassword*> metodo seleziona l'algoritmo corretto in base al primo byte. Un client è in grado di eseguire l'autenticazione indipendentemente dalla versione dell'algoritmo utilizzata per eseguire l'hashing della password. L'impostazione della modalità di compatibilità influiscono sull'hashing delle *nuove password*. |
| <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.IterationCount> | Numero di iterazioni utilizzate quando si esegue l'hashing delle password utilizzando PBKDF2. Questo valore viene utilizzato solo quando <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.CompatibilityMode> è impostato su <xref:Microsoft.AspNetCore.Identity.PasswordHasherCompatibilityMode.IdentityV3> . Il valore deve essere un numero intero positivo e il valore predefinito è `10000` . |

Nell'esempio seguente, l'oggetto <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.IterationCount> è impostato su `12000` in `Startup.ConfigureServices` :

```csharp
// using Microsoft.AspNetCore.Identity;

services.Configure<PasswordHasherOptions>(option =>
{
    option.IterationCount = 12000;
});
```
