---
title: Autenticazione di Facebook, Google e del provider esterno senza ASP.NET Core Identity
author: rick-anderson
description: Spiegazione dell'uso di Facebook, Google, Twitter e così via. autenticazione utente dell'account senza ASP.NET Core Identity .
ms.author: riande
ms.date: 12/10/2019
no-loc:
- appsettings.json
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
uid: security/authentication/social/social-without-identity
ms.openlocfilehash: cd7545a3ddaccedfa64ef5e9d5458c21c651257a
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060287"
---
# <a name="use-social-sign-in-provider-authentication-without-no-locaspnet-core-identity"></a>Usare l'autenticazione del provider di accesso sociale senza ASP.NET Core Identity

Di [Kirk Larkin](https://twitter.com/serpent5) e [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

<xref:security/authentication/social/index> viene descritto come consentire agli utenti di eseguire l'accesso utilizzando OAuth 2,0 con le credenziali dei provider di autenticazione esterni. L'approccio descritto in questo argomento include ASP.NET Core Identity come provider di autenticazione.

In questo esempio viene illustrato come utilizzare un provider di autenticazione esterno **senza** ASP.NET Core Identity . Questa operazione è utile per le app che non richiedono tutte le funzionalità di ASP.NET Core Identity , ma richiedono comunque l'integrazione con un provider di autenticazione esterno attendibile.

Questo esempio usa [l'autenticazione di Google per l'](xref:security/authentication/google-logins) autenticazione degli utenti. L'uso dell'autenticazione Google sposta molte delle complessità della gestione del processo di accesso a Google. Per l'integrazione con un provider di autenticazione esterno diverso, vedere gli argomenti seguenti:

* [Autenticazione Facebook](xref:security/authentication/facebook-logins)
* [Autenticazione Microsoft](xref:security/authentication/microsoft-logins)
* [Autenticazione Twitter](xref:security/authentication/twitter-logins)
* [Altri provider di servizi](xref:security/authentication/otherlogins)

## <a name="configuration"></a>Configurazione

Nel `ConfigureServices` Metodo configurare gli schemi di autenticazione dell'app con i <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> metodi, <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> e <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> :

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Startup.cs?name=snippet1)]

La chiamata a <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> imposta l'oggetto dell'app <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme> . `DefaultScheme`È lo schema predefinito utilizzato dai `HttpContext` metodi di estensione di autenticazione seguenti:

* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>

Impostando l'app `DefaultScheme` su [ Cookie AuthenticationDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) (" Cookie s"), l'app viene configurata in modo da usare Cookie s come schema predefinito per questi metodi di estensione. Impostando l'app <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> su [GoogleDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google"), l'app viene configurata per l'uso di Google come schema predefinito per le chiamate a `ChallengeAsync` . `DefaultChallengeScheme` esegue l'override di `DefaultScheme` . <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions>Per altre proprietà che eseguono l'override di `DefaultScheme` , vedere se impostate.

In `Startup.Configure` , chiamare `UseAuthentication` e `UseAuthorization` tra la chiamata a `UseRouting` e `UseEndpoints` . Viene impostata la `HttpContext.User` proprietà ed eseguito il middleware di autorizzazione per le richieste:

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Startup.cs?name=snippet2&highlight=3-4)]

Per altre informazioni sugli schemi di autenticazione, vedere [concetti relativi all'autenticazione](xref:security/authentication/index#authentication-concepts). Per ulteriori informazioni sull' cookie autenticazione di, vedere <xref:security/authentication/cookie> .

## <a name="apply-authorization"></a>Applica autorizzazione

Testare la configurazione di autenticazione dell'app applicando l' `AuthorizeAttribute` attributo a un controller, un'azione o una pagina. Il codice seguente limita l'accesso alla pagina *privacy* agli utenti che sono stati autenticati:

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Pages/Privacy.cshtml.cs?name=snippet&highlight=1)]

## <a name="sign-out"></a>Disconnetti

Per disconnettersi dall'utente corrente ed eliminarne la cookie chiamata, chiamare [SignOutAsync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*). Il codice seguente aggiunge un `Logout` gestore di pagina alla pagina di *Indice* :

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Pages/Index.cshtml.cs?name=snippet&highlight=3-7)]

Si noti che la chiamata a `SignOutAsync` non specifica uno schema di autenticazione. L'app `DefaultScheme` di `CookieAuthenticationDefaults.AuthenticationScheme` viene usata come fallback.

## <a name="additional-resources"></a>Risorse aggiuntive

* <xref:security/authorization/simple>
* <xref:security/authentication/social/additional-claims>

::: moniker-end
::: moniker range="< aspnetcore-3.0"

<xref:security/authentication/social/index> viene descritto come consentire agli utenti di eseguire l'accesso utilizzando OAuth 2,0 con le credenziali dei provider di autenticazione esterni. L'approccio descritto in questo argomento include ASP.NET Core Identity come provider di autenticazione.

In questo esempio viene illustrato come utilizzare un provider di autenticazione esterno **senza** ASP.NET Core Identity . Questa operazione è utile per le app che non richiedono tutte le funzionalità di ASP.NET Core Identity , ma richiedono comunque l'integrazione con un provider di autenticazione esterno attendibile.

Questo esempio usa [l'autenticazione di Google per l'](xref:security/authentication/google-logins) autenticazione degli utenti. L'uso dell'autenticazione Google sposta molte delle complessità della gestione del processo di accesso a Google. Per l'integrazione con un provider di autenticazione esterno diverso, vedere gli argomenti seguenti:

* [Autenticazione Facebook](xref:security/authentication/facebook-logins)
* [Autenticazione Microsoft](xref:security/authentication/microsoft-logins)
* [Autenticazione Twitter](xref:security/authentication/twitter-logins)
* [Altri provider di servizi](xref:security/authentication/otherlogins)

## <a name="configuration"></a>Configurazione

Nel `ConfigureServices` Metodo configurare gli schemi di autenticazione dell'app con i `AddAuthentication` metodi, `AddCookie` e `AddGoogle` :

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Startup.cs?name=snippet1)]

La chiamata a [AddAuthentication](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication#Microsoft_Extensions_DependencyInjection_AuthenticationServiceCollectionExtensions_AddAuthentication_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_AuthenticationOptions__) imposta l' [DefaultScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme)dell'app. `DefaultScheme`È lo schema predefinito utilizzato dai `HttpContext` metodi di estensione di autenticazione seguenti:

* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>

Impostando l'app `DefaultScheme` su [ Cookie AuthenticationDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) (" Cookie s"), l'app viene configurata in modo da usare Cookie s come schema predefinito per questi metodi di estensione. Impostando l'app <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> su [GoogleDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google"), l'app viene configurata per l'uso di Google come schema predefinito per le chiamate a `ChallengeAsync` . `DefaultChallengeScheme` esegue l'override di `DefaultScheme` . <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions>Per altre proprietà che eseguono l'override di `DefaultScheme` , vedere se impostate.

Nel `Configure` metodo chiamare il `UseAuthentication` metodo per richiamare il middleware di autenticazione che imposta la `HttpContext.User` Proprietà. Chiamare il `UseAuthentication` metodo prima di chiamare `UseMvcWithDefaultRoute` o `UseMvc` :

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Startup.cs?name=snippet2)]

Per altre informazioni sugli schemi di autenticazione, vedere [concetti relativi all'autenticazione](xref:security/authentication/index#authentication-concepts). Per ulteriori informazioni sull' cookie autenticazione di, vedere <xref:security/authentication/cookie> .

## <a name="apply-authorization"></a>Applica autorizzazione

Testare la configurazione di autenticazione dell'app applicando l' `AuthorizeAttribute` attributo a un controller, un'azione o una pagina. Il codice seguente limita l'accesso alla pagina *privacy* agli utenti che sono stati autenticati:

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Pages/Privacy.cshtml.cs?name=snippet&highlight=1)]

## <a name="sign-out"></a>Disconnetti

Per disconnettersi dall'utente corrente ed eliminarne la cookie chiamata, chiamare [SignOutAsync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*). Il codice seguente aggiunge un `Logout` gestore di pagina alla pagina di *Indice* :

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Pages/Index.cshtml.cs?name=snippet&highlight=3-7)]

Si noti che la chiamata a `SignOutAsync` non specifica uno schema di autenticazione. L'app `DefaultScheme` di `CookieAuthenticationDefaults.AuthenticationScheme` viene usata come fallback.

## <a name="additional-resources"></a>Risorse aggiuntive

* <xref:security/authorization/simple>
* <xref:security/authentication/social/additional-claims>

::: moniker-end
