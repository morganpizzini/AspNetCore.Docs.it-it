---
title: Autenticazione di Facebook, Google e del provider esterno senza ASP.NET CoreIdentity
author: rick-anderson
description: Spiegazione dell'uso dell'autenticazione utente per l'account Facebook, Google, Twitter e così via Identity, senza ASP.NET Core.
ms.author: riande
ms.date: 12/10/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/social/social-without-identity
ms.openlocfilehash: cc44eb83947540ca9a5a04ffad4fdb8522fab26a
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775739"
---
# <a name="use-social-sign-in-provider-authentication-without-aspnet-core-identity"></a>Usa l'autenticazione del provider di accesso di social networking senza ASP.NET CoreIdentity

Di [Kirk Larkin](https://twitter.com/serpent5) e [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

<xref:security/authentication/social/index>viene descritto come consentire agli utenti di eseguire l'accesso utilizzando OAuth 2,0 con le credenziali dei provider di autenticazione esterni. L'approccio descritto in questo argomento include ASP.NET Core Identity come provider di autenticazione.

In questo esempio viene illustrato come utilizzare un provider di **without** autenticazione esterno Identitysenza ASP.NET Core. Questa operazione è utile per le app che non richiedono tutte le funzionalità di IdentityASP.NET Core, ma richiedono comunque l'integrazione con un provider di autenticazione esterno attendibile.

Questo esempio usa [l'autenticazione di Google per l'](xref:security/authentication/google-logins) autenticazione degli utenti. L'uso dell'autenticazione Google sposta molte delle complessità della gestione del processo di accesso a Google. Per l'integrazione con un provider di autenticazione esterno diverso, vedere gli argomenti seguenti:

* [Autenticazione Facebook](xref:security/authentication/facebook-logins)
* [Autenticazione Microsoft](xref:security/authentication/microsoft-logins)
* [Autenticazione Twitter](xref:security/authentication/twitter-logins)
* [Altri provider di servizi](xref:security/authentication/otherlogins)

## <a name="configuration"></a>Configurazione

Nel `ConfigureServices` metodo configurare gli schemi di autenticazione dell'app con i <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*>metodi, <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>e: <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*>

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Startup.cs?name=snippet1)]

La chiamata a <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> imposta l'oggetto dell' <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme>app. `DefaultScheme` È lo schema predefinito utilizzato dai metodi di estensione `HttpContext` di autenticazione seguenti:

* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>

Impostando l'app `DefaultScheme` su [CookieAuthenticationDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("cookie"), l'app viene configurata in modo da usare i cookie come schema predefinito per questi metodi di estensione. Impostando l'app <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> su [GoogleDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google"), l'app viene configurata per l'uso di Google come schema `ChallengeAsync`predefinito per le chiamate a. `DefaultChallengeScheme`esegue `DefaultScheme`l'override di. Per <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions> altre proprietà che eseguono l' `DefaultScheme` override di, vedere se impostate.

In `Startup.Configure`, chiamare `UseAuthentication` e `UseAuthorization` tra la `UseRouting` chiamata `UseEndpoints`a e. Viene impostata la `HttpContext.User` proprietà ed eseguito il middleware di autorizzazione per le richieste:

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Startup.cs?name=snippet2&highlight=3-4)]

Per altre informazioni sugli schemi di autenticazione, vedere [concetti relativi all'autenticazione](xref:security/authentication/index#authentication-concepts). Per ulteriori informazioni sull'autenticazione dei cookie, <xref:security/authentication/cookie>vedere.

## <a name="apply-authorization"></a>Applica autorizzazione

Testare la configurazione di autenticazione dell'app applicando `AuthorizeAttribute` l'attributo a un controller, un'azione o una pagina. Il codice seguente limita l'accesso alla pagina *privacy* agli utenti che sono stati autenticati:

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Pages/Privacy.cshtml.cs?name=snippet&highlight=1)]

## <a name="sign-out"></a>Disconnessione

Per disconnettersi dall'utente corrente ed eliminare il cookie, chiamare [SignOutAsync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*). Il codice seguente aggiunge un `Logout` gestore di pagina alla pagina di *Indice* :

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Pages/Index.cshtml.cs?name=snippet&highlight=3-7)]

Si noti che la chiamata `SignOutAsync` a non specifica uno schema di autenticazione. L'app `DefaultScheme` di `CookieAuthenticationDefaults.AuthenticationScheme` viene usata come fallback.

## <a name="additional-resources"></a>Risorse aggiuntive

* <xref:security/authorization/simple>
* <xref:security/authentication/social/additional-claims>

::: moniker-end
::: moniker range="< aspnetcore-3.0"

<xref:security/authentication/social/index>viene descritto come consentire agli utenti di eseguire l'accesso utilizzando OAuth 2,0 con le credenziali dei provider di autenticazione esterni. L'approccio descritto in questo argomento include ASP.NET Core Identity come provider di autenticazione.

In questo esempio viene illustrato come utilizzare un provider di **without** autenticazione esterno Identitysenza ASP.NET Core. Questa operazione è utile per le app che non richiedono tutte le funzionalità di IdentityASP.NET Core, ma richiedono comunque l'integrazione con un provider di autenticazione esterno attendibile.

Questo esempio usa [l'autenticazione di Google per l'](xref:security/authentication/google-logins) autenticazione degli utenti. L'uso dell'autenticazione Google sposta molte delle complessità della gestione del processo di accesso a Google. Per l'integrazione con un provider di autenticazione esterno diverso, vedere gli argomenti seguenti:

* [Autenticazione Facebook](xref:security/authentication/facebook-logins)
* [Autenticazione Microsoft](xref:security/authentication/microsoft-logins)
* [Autenticazione Twitter](xref:security/authentication/twitter-logins)
* [Altri provider di servizi](xref:security/authentication/otherlogins)

## <a name="configuration"></a>Configurazione

Nel `ConfigureServices` metodo configurare gli schemi di autenticazione dell'app con i `AddAuthentication`metodi, `AddCookie`e: `AddGoogle`

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Startup.cs?name=snippet1)]

La chiamata a [AddAuthentication](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication#Microsoft_Extensions_DependencyInjection_AuthenticationServiceCollectionExtensions_AddAuthentication_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_AuthenticationOptions__) imposta l' [DefaultScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme)dell'app. `DefaultScheme` È lo schema predefinito utilizzato dai metodi di estensione `HttpContext` di autenticazione seguenti:

* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>

Impostando l'app `DefaultScheme` su [CookieAuthenticationDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("cookie"), l'app viene configurata in modo da usare i cookie come schema predefinito per questi metodi di estensione. Impostando l'app <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> su [GoogleDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google"), l'app viene configurata per l'uso di Google come schema `ChallengeAsync`predefinito per le chiamate a. `DefaultChallengeScheme`esegue `DefaultScheme`l'override di. Per <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions> altre proprietà che eseguono l' `DefaultScheme` override di, vedere se impostate.

Nel `Configure` metodo chiamare il `UseAuthentication` metodo per richiamare il middleware di autenticazione che imposta la `HttpContext.User` proprietà. Chiamare il `UseAuthentication` metodo prima di `UseMvcWithDefaultRoute` chiamare `UseMvc`o:

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Startup.cs?name=snippet2)]

Per altre informazioni sugli schemi di autenticazione, vedere [concetti relativi all'autenticazione](xref:security/authentication/index#authentication-concepts). Per ulteriori informazioni sull'autenticazione dei cookie, <xref:security/authentication/cookie>vedere.

## <a name="apply-authorization"></a>Applica autorizzazione

Testare la configurazione di autenticazione dell'app applicando `AuthorizeAttribute` l'attributo a un controller, un'azione o una pagina. Il codice seguente limita l'accesso alla pagina *privacy* agli utenti che sono stati autenticati:

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Pages/Privacy.cshtml.cs?name=snippet&highlight=1)]

## <a name="sign-out"></a>Disconnessione

Per disconnettersi dall'utente corrente ed eliminare il cookie, chiamare [SignOutAsync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*). Il codice seguente aggiunge un `Logout` gestore di pagina alla pagina di *Indice* :

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Pages/Index.cshtml.cs?name=snippet&highlight=3-7)]

Si noti che la chiamata `SignOutAsync` a non specifica uno schema di autenticazione. L'app `DefaultScheme` di `CookieAuthenticationDefaults.AuthenticationScheme` viene usata come fallback.

## <a name="additional-resources"></a>Risorse aggiuntive

* <xref:security/authorization/simple>
* <xref:security/authentication/social/additional-claims>

::: moniker-end
