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
# <a name="use-social-sign-in-provider-authentication-without-aspnet-core-identity"></a><span data-ttu-id="94215-103">Usa l'autenticazione del provider di accesso di social networking senza ASP.NET CoreIdentity</span><span class="sxs-lookup"><span data-stu-id="94215-103">Use social sign-in provider authentication without ASP.NET Core Identity</span></span>

<span data-ttu-id="94215-104">Di [Kirk Larkin](https://twitter.com/serpent5) e [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="94215-104">By [Kirk Larkin](https://twitter.com/serpent5) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="94215-105"><xref:security/authentication/social/index>viene descritto come consentire agli utenti di eseguire l'accesso utilizzando OAuth 2,0 con le credenziali dei provider di autenticazione esterni.</span><span class="sxs-lookup"><span data-stu-id="94215-105"><xref:security/authentication/social/index> describes how to enable users to sign in using OAuth 2.0 with credentials from external authentication providers.</span></span> <span data-ttu-id="94215-106">L'approccio descritto in questo argomento include ASP.NET Core Identity come provider di autenticazione.</span><span class="sxs-lookup"><span data-stu-id="94215-106">The approach described in that topic includes ASP.NET Core Identity as an authentication provider.</span></span>

<span data-ttu-id="94215-107">In questo esempio viene illustrato come utilizzare un provider di **without** autenticazione esterno Identitysenza ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="94215-107">This sample demonstrates how to use an external authentication provider **without** ASP.NET Core Identity.</span></span> <span data-ttu-id="94215-108">Questa operazione è utile per le app che non richiedono tutte le funzionalità di IdentityASP.NET Core, ma richiedono comunque l'integrazione con un provider di autenticazione esterno attendibile.</span><span class="sxs-lookup"><span data-stu-id="94215-108">This is useful for apps that don't require all of the features of ASP.NET Core Identity, but still require integration with a trusted external authentication provider.</span></span>

<span data-ttu-id="94215-109">Questo esempio usa [l'autenticazione di Google per l'](xref:security/authentication/google-logins) autenticazione degli utenti.</span><span class="sxs-lookup"><span data-stu-id="94215-109">This sample uses [Google authentication](xref:security/authentication/google-logins) for authenticating users.</span></span> <span data-ttu-id="94215-110">L'uso dell'autenticazione Google sposta molte delle complessità della gestione del processo di accesso a Google.</span><span class="sxs-lookup"><span data-stu-id="94215-110">Using Google authentication shifts many of the complexities of managing the sign-in process to Google.</span></span> <span data-ttu-id="94215-111">Per l'integrazione con un provider di autenticazione esterno diverso, vedere gli argomenti seguenti:</span><span class="sxs-lookup"><span data-stu-id="94215-111">To integrate with a different external authentication provider, see the following topics:</span></span>

* [<span data-ttu-id="94215-112">Autenticazione Facebook</span><span class="sxs-lookup"><span data-stu-id="94215-112">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="94215-113">Autenticazione Microsoft</span><span class="sxs-lookup"><span data-stu-id="94215-113">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="94215-114">Autenticazione Twitter</span><span class="sxs-lookup"><span data-stu-id="94215-114">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="94215-115">Altri provider di servizi</span><span class="sxs-lookup"><span data-stu-id="94215-115">Other providers</span></span>](xref:security/authentication/otherlogins)

## <a name="configuration"></a><span data-ttu-id="94215-116">Configurazione</span><span class="sxs-lookup"><span data-stu-id="94215-116">Configuration</span></span>

<span data-ttu-id="94215-117">Nel `ConfigureServices` metodo configurare gli schemi di autenticazione dell'app con i <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*>metodi, <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>e: <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*></span><span class="sxs-lookup"><span data-stu-id="94215-117">In the `ConfigureServices` method, configure the app's authentication schemes with the <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*>, <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, and <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> methods:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Startup.cs?name=snippet1)]

<span data-ttu-id="94215-118">La chiamata a <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> imposta l'oggetto dell' <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme>app.</span><span class="sxs-lookup"><span data-stu-id="94215-118">The call to <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> sets the app's <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme>.</span></span> <span data-ttu-id="94215-119">`DefaultScheme` È lo schema predefinito utilizzato dai metodi di estensione `HttpContext` di autenticazione seguenti:</span><span class="sxs-lookup"><span data-stu-id="94215-119">The `DefaultScheme` is the default scheme used by the following `HttpContext` authentication extension methods:</span></span>

* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>

<span data-ttu-id="94215-120">Impostando l'app `DefaultScheme` su [CookieAuthenticationDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("cookie"), l'app viene configurata in modo da usare i cookie come schema predefinito per questi metodi di estensione.</span><span class="sxs-lookup"><span data-stu-id="94215-120">Setting the app's `DefaultScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("Cookies") configures the app to use Cookies as the default scheme for these extension methods.</span></span> <span data-ttu-id="94215-121">Impostando l'app <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> su [GoogleDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google"), l'app viene configurata per l'uso di Google come schema `ChallengeAsync`predefinito per le chiamate a.</span><span class="sxs-lookup"><span data-stu-id="94215-121">Setting the app's <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> to [GoogleDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") configures the app to use Google as the default scheme for calls to `ChallengeAsync`.</span></span> <span data-ttu-id="94215-122">`DefaultChallengeScheme`esegue `DefaultScheme`l'override di.</span><span class="sxs-lookup"><span data-stu-id="94215-122">`DefaultChallengeScheme` overrides `DefaultScheme`.</span></span> <span data-ttu-id="94215-123">Per <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions> altre proprietà che eseguono l' `DefaultScheme` override di, vedere se impostate.</span><span class="sxs-lookup"><span data-stu-id="94215-123">See <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions> for additional properties that override `DefaultScheme` when set.</span></span>

<span data-ttu-id="94215-124">In `Startup.Configure`, chiamare `UseAuthentication` e `UseAuthorization` tra la `UseRouting` chiamata `UseEndpoints`a e.</span><span class="sxs-lookup"><span data-stu-id="94215-124">In `Startup.Configure`, call `UseAuthentication` and `UseAuthorization` between calling `UseRouting` and `UseEndpoints`.</span></span> <span data-ttu-id="94215-125">Viene impostata la `HttpContext.User` proprietà ed eseguito il middleware di autorizzazione per le richieste:</span><span class="sxs-lookup"><span data-stu-id="94215-125">This sets the `HttpContext.User` property and runs the Authorization Middleware for requests:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Startup.cs?name=snippet2&highlight=3-4)]

<span data-ttu-id="94215-126">Per altre informazioni sugli schemi di autenticazione, vedere [concetti relativi all'autenticazione](xref:security/authentication/index#authentication-concepts).</span><span class="sxs-lookup"><span data-stu-id="94215-126">To learn more about authentication schemes, see [Authentication Concepts](xref:security/authentication/index#authentication-concepts).</span></span> <span data-ttu-id="94215-127">Per ulteriori informazioni sull'autenticazione dei cookie, <xref:security/authentication/cookie>vedere.</span><span class="sxs-lookup"><span data-stu-id="94215-127">To learn more about cookie authentication, see <xref:security/authentication/cookie>.</span></span>

## <a name="apply-authorization"></a><span data-ttu-id="94215-128">Applica autorizzazione</span><span class="sxs-lookup"><span data-stu-id="94215-128">Apply authorization</span></span>

<span data-ttu-id="94215-129">Testare la configurazione di autenticazione dell'app applicando `AuthorizeAttribute` l'attributo a un controller, un'azione o una pagina.</span><span class="sxs-lookup"><span data-stu-id="94215-129">Test the app's authentication configuration by applying the `AuthorizeAttribute` attribute to a controller, action, or page.</span></span> <span data-ttu-id="94215-130">Il codice seguente limita l'accesso alla pagina *privacy* agli utenti che sono stati autenticati:</span><span class="sxs-lookup"><span data-stu-id="94215-130">The following code limits access to the *Privacy* page to users that have been authenticated:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Pages/Privacy.cshtml.cs?name=snippet&highlight=1)]

## <a name="sign-out"></a><span data-ttu-id="94215-131">Disconnessione</span><span class="sxs-lookup"><span data-stu-id="94215-131">Sign out</span></span>

<span data-ttu-id="94215-132">Per disconnettersi dall'utente corrente ed eliminare il cookie, chiamare [SignOutAsync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*).</span><span class="sxs-lookup"><span data-stu-id="94215-132">To sign out the current user and delete their cookie, call [SignOutAsync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*).</span></span> <span data-ttu-id="94215-133">Il codice seguente aggiunge un `Logout` gestore di pagina alla pagina di *Indice* :</span><span class="sxs-lookup"><span data-stu-id="94215-133">The following code adds a `Logout` page handler to the *Index* page:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Pages/Index.cshtml.cs?name=snippet&highlight=3-7)]

<span data-ttu-id="94215-134">Si noti che la chiamata `SignOutAsync` a non specifica uno schema di autenticazione.</span><span class="sxs-lookup"><span data-stu-id="94215-134">Notice that the call to `SignOutAsync` does not specify an authentication scheme.</span></span> <span data-ttu-id="94215-135">L'app `DefaultScheme` di `CookieAuthenticationDefaults.AuthenticationScheme` viene usata come fallback.</span><span class="sxs-lookup"><span data-stu-id="94215-135">The app's `DefaultScheme` of `CookieAuthenticationDefaults.AuthenticationScheme` is used as a fall back.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="94215-136">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="94215-136">Additional resources</span></span>

* <xref:security/authorization/simple>
* <xref:security/authentication/social/additional-claims>

::: moniker-end
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="94215-137"><xref:security/authentication/social/index>viene descritto come consentire agli utenti di eseguire l'accesso utilizzando OAuth 2,0 con le credenziali dei provider di autenticazione esterni.</span><span class="sxs-lookup"><span data-stu-id="94215-137"><xref:security/authentication/social/index> describes how to enable users to sign in using OAuth 2.0 with credentials from external authentication providers.</span></span> <span data-ttu-id="94215-138">L'approccio descritto in questo argomento include ASP.NET Core Identity come provider di autenticazione.</span><span class="sxs-lookup"><span data-stu-id="94215-138">The approach described in that topic includes ASP.NET Core Identity as an authentication provider.</span></span>

<span data-ttu-id="94215-139">In questo esempio viene illustrato come utilizzare un provider di **without** autenticazione esterno Identitysenza ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="94215-139">This sample demonstrates how to use an external authentication provider **without** ASP.NET Core Identity.</span></span> <span data-ttu-id="94215-140">Questa operazione è utile per le app che non richiedono tutte le funzionalità di IdentityASP.NET Core, ma richiedono comunque l'integrazione con un provider di autenticazione esterno attendibile.</span><span class="sxs-lookup"><span data-stu-id="94215-140">This is useful for apps that don't require all of the features of ASP.NET Core Identity, but still require integration with a trusted external authentication provider.</span></span>

<span data-ttu-id="94215-141">Questo esempio usa [l'autenticazione di Google per l'](xref:security/authentication/google-logins) autenticazione degli utenti.</span><span class="sxs-lookup"><span data-stu-id="94215-141">This sample uses [Google authentication](xref:security/authentication/google-logins) for authenticating users.</span></span> <span data-ttu-id="94215-142">L'uso dell'autenticazione Google sposta molte delle complessità della gestione del processo di accesso a Google.</span><span class="sxs-lookup"><span data-stu-id="94215-142">Using Google authentication shifts many of the complexities of managing the sign-in process to Google.</span></span> <span data-ttu-id="94215-143">Per l'integrazione con un provider di autenticazione esterno diverso, vedere gli argomenti seguenti:</span><span class="sxs-lookup"><span data-stu-id="94215-143">To integrate with a different external authentication provider, see the following topics:</span></span>

* [<span data-ttu-id="94215-144">Autenticazione Facebook</span><span class="sxs-lookup"><span data-stu-id="94215-144">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="94215-145">Autenticazione Microsoft</span><span class="sxs-lookup"><span data-stu-id="94215-145">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="94215-146">Autenticazione Twitter</span><span class="sxs-lookup"><span data-stu-id="94215-146">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="94215-147">Altri provider di servizi</span><span class="sxs-lookup"><span data-stu-id="94215-147">Other providers</span></span>](xref:security/authentication/otherlogins)

## <a name="configuration"></a><span data-ttu-id="94215-148">Configurazione</span><span class="sxs-lookup"><span data-stu-id="94215-148">Configuration</span></span>

<span data-ttu-id="94215-149">Nel `ConfigureServices` metodo configurare gli schemi di autenticazione dell'app con i `AddAuthentication`metodi, `AddCookie`e: `AddGoogle`</span><span class="sxs-lookup"><span data-stu-id="94215-149">In the `ConfigureServices` method, configure the app's authentication schemes with the `AddAuthentication`, `AddCookie`, and `AddGoogle` methods:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Startup.cs?name=snippet1)]

<span data-ttu-id="94215-150">La chiamata a [AddAuthentication](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication#Microsoft_Extensions_DependencyInjection_AuthenticationServiceCollectionExtensions_AddAuthentication_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_AuthenticationOptions__) imposta l' [DefaultScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme)dell'app.</span><span class="sxs-lookup"><span data-stu-id="94215-150">The call to [AddAuthentication](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication#Microsoft_Extensions_DependencyInjection_AuthenticationServiceCollectionExtensions_AddAuthentication_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_AuthenticationOptions__) sets the app's [DefaultScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme).</span></span> <span data-ttu-id="94215-151">`DefaultScheme` È lo schema predefinito utilizzato dai metodi di estensione `HttpContext` di autenticazione seguenti:</span><span class="sxs-lookup"><span data-stu-id="94215-151">The `DefaultScheme` is the default scheme used by the following `HttpContext` authentication extension methods:</span></span>

* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>

<span data-ttu-id="94215-152">Impostando l'app `DefaultScheme` su [CookieAuthenticationDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("cookie"), l'app viene configurata in modo da usare i cookie come schema predefinito per questi metodi di estensione.</span><span class="sxs-lookup"><span data-stu-id="94215-152">Setting the app's `DefaultScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("Cookies") configures the app to use Cookies as the default scheme for these extension methods.</span></span> <span data-ttu-id="94215-153">Impostando l'app <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> su [GoogleDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google"), l'app viene configurata per l'uso di Google come schema `ChallengeAsync`predefinito per le chiamate a.</span><span class="sxs-lookup"><span data-stu-id="94215-153">Setting the app's <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> to [GoogleDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") configures the app to use Google as the default scheme for calls to `ChallengeAsync`.</span></span> <span data-ttu-id="94215-154">`DefaultChallengeScheme`esegue `DefaultScheme`l'override di.</span><span class="sxs-lookup"><span data-stu-id="94215-154">`DefaultChallengeScheme` overrides `DefaultScheme`.</span></span> <span data-ttu-id="94215-155">Per <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions> altre proprietà che eseguono l' `DefaultScheme` override di, vedere se impostate.</span><span class="sxs-lookup"><span data-stu-id="94215-155">See <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions> for additional properties that override `DefaultScheme` when set.</span></span>

<span data-ttu-id="94215-156">Nel `Configure` metodo chiamare il `UseAuthentication` metodo per richiamare il middleware di autenticazione che imposta la `HttpContext.User` proprietà.</span><span class="sxs-lookup"><span data-stu-id="94215-156">In the `Configure` method, call the `UseAuthentication` method to invoke the Authentication Middleware that sets the `HttpContext.User` property.</span></span> <span data-ttu-id="94215-157">Chiamare il `UseAuthentication` metodo prima di `UseMvcWithDefaultRoute` chiamare `UseMvc`o:</span><span class="sxs-lookup"><span data-stu-id="94215-157">Call the `UseAuthentication` method before calling `UseMvcWithDefaultRoute` or `UseMvc`:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Startup.cs?name=snippet2)]

<span data-ttu-id="94215-158">Per altre informazioni sugli schemi di autenticazione, vedere [concetti relativi all'autenticazione](xref:security/authentication/index#authentication-concepts).</span><span class="sxs-lookup"><span data-stu-id="94215-158">To learn more about authentication schemes, see [Authentication Concepts](xref:security/authentication/index#authentication-concepts).</span></span> <span data-ttu-id="94215-159">Per ulteriori informazioni sull'autenticazione dei cookie, <xref:security/authentication/cookie>vedere.</span><span class="sxs-lookup"><span data-stu-id="94215-159">To learn more about cookie authentication, see <xref:security/authentication/cookie>.</span></span>

## <a name="apply-authorization"></a><span data-ttu-id="94215-160">Applica autorizzazione</span><span class="sxs-lookup"><span data-stu-id="94215-160">Apply authorization</span></span>

<span data-ttu-id="94215-161">Testare la configurazione di autenticazione dell'app applicando `AuthorizeAttribute` l'attributo a un controller, un'azione o una pagina.</span><span class="sxs-lookup"><span data-stu-id="94215-161">Test the app's authentication configuration by applying the `AuthorizeAttribute` attribute to a controller, action, or page.</span></span> <span data-ttu-id="94215-162">Il codice seguente limita l'accesso alla pagina *privacy* agli utenti che sono stati autenticati:</span><span class="sxs-lookup"><span data-stu-id="94215-162">The following code limits access to the *Privacy* page to users that have been authenticated:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Pages/Privacy.cshtml.cs?name=snippet&highlight=1)]

## <a name="sign-out"></a><span data-ttu-id="94215-163">Disconnessione</span><span class="sxs-lookup"><span data-stu-id="94215-163">Sign out</span></span>

<span data-ttu-id="94215-164">Per disconnettersi dall'utente corrente ed eliminare il cookie, chiamare [SignOutAsync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*).</span><span class="sxs-lookup"><span data-stu-id="94215-164">To sign out the current user and delete their cookie, call [SignOutAsync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*).</span></span> <span data-ttu-id="94215-165">Il codice seguente aggiunge un `Logout` gestore di pagina alla pagina di *Indice* :</span><span class="sxs-lookup"><span data-stu-id="94215-165">The following code adds a `Logout` page handler to the *Index* page:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Pages/Index.cshtml.cs?name=snippet&highlight=3-7)]

<span data-ttu-id="94215-166">Si noti che la chiamata `SignOutAsync` a non specifica uno schema di autenticazione.</span><span class="sxs-lookup"><span data-stu-id="94215-166">Notice that the call to `SignOutAsync` does not specify an authentication scheme.</span></span> <span data-ttu-id="94215-167">L'app `DefaultScheme` di `CookieAuthenticationDefaults.AuthenticationScheme` viene usata come fallback.</span><span class="sxs-lookup"><span data-stu-id="94215-167">The app's `DefaultScheme` of `CookieAuthenticationDefaults.AuthenticationScheme` is used as a fall back.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="94215-168">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="94215-168">Additional resources</span></span>

* <xref:security/authorization/simple>
* <xref:security/authentication/social/additional-claims>

::: moniker-end
