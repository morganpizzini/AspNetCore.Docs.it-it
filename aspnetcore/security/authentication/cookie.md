---
title: Usa autenticazione cookie senza ASP.NET CoreIdentity
author: rick-anderson
description: Informazioni su come usare l'autenticazione tramite cookie senza ASP.NET Core Identity .
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 02/11/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/cookie
ms.openlocfilehash: 401d03352b8c2c040202716bdddf484e3b778f52
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/26/2020
ms.locfileid: "85408825"
---
# <a name="use-cookie-authentication-without-aspnet-core-identity"></a><span data-ttu-id="3a478-103">Usa autenticazione cookie senza ASP.NET CoreIdentity</span><span class="sxs-lookup"><span data-stu-id="3a478-103">Use cookie authentication without ASP.NET Core Identity</span></span>

<span data-ttu-id="3a478-104">Autore: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="3a478-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="3a478-105">ASP.NET Core Identity è un provider di autenticazione completo con funzionalità complete per la creazione e la gestione degli account di accesso.</span><span class="sxs-lookup"><span data-stu-id="3a478-105">ASP.NET Core Identity is a complete, full-featured authentication provider for creating and maintaining logins.</span></span> <span data-ttu-id="3a478-106">Tuttavia, è possibile usare un provider di autenticazione basato su cookie senza ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="3a478-106">However, a cookie-based authentication provider without ASP.NET Core Identity can be used.</span></span> <span data-ttu-id="3a478-107">Per altre informazioni, vedere <xref:security/authentication/identity>.</span><span class="sxs-lookup"><span data-stu-id="3a478-107">For more information, see <xref:security/authentication/identity>.</span></span>

<span data-ttu-id="3a478-108">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="3a478-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="3a478-109">A scopo dimostrativo nell'app di esempio, l'account utente per l'utente ipotetico, Maria Rodriguez, è hardcoded nell'app.</span><span class="sxs-lookup"><span data-stu-id="3a478-109">For demonstration purposes in the sample app, the user account for the hypothetical user, Maria Rodriguez, is hardcoded into the app.</span></span> <span data-ttu-id="3a478-110">Usare l'indirizzo di **posta elettronica** `maria.rodriguez@contoso.com` e qualsiasi password per accedere all'utente.</span><span class="sxs-lookup"><span data-stu-id="3a478-110">Use the **Email** address `maria.rodriguez@contoso.com` and any password to sign in the user.</span></span> <span data-ttu-id="3a478-111">L'utente viene autenticato nel `AuthenticateUser` metodo nel file *pages/account/login. cshtml. cs* .</span><span class="sxs-lookup"><span data-stu-id="3a478-111">The user is authenticated in the `AuthenticateUser` method in the *Pages/Account/Login.cshtml.cs* file.</span></span> <span data-ttu-id="3a478-112">In un esempio reale, l'utente viene autenticato a fronte di un database.</span><span class="sxs-lookup"><span data-stu-id="3a478-112">In a real-world example, the user would be authenticated against a database.</span></span>

## <a name="configuration"></a><span data-ttu-id="3a478-113">Configurazione</span><span class="sxs-lookup"><span data-stu-id="3a478-113">Configuration</span></span>

<span data-ttu-id="3a478-114">Se l'app non usa il [metapacchetto Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app), creare un riferimento al pacchetto nel file di progetto per il pacchetto [Microsoft. AspNetCore. Authentication. cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) .</span><span class="sxs-lookup"><span data-stu-id="3a478-114">If the app doesn't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference in the project file for the [Microsoft.AspNetCore.Authentication.Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) package.</span></span>

<span data-ttu-id="3a478-115">Nel `Startup.ConfigureServices` metodo creare i servizi del middleware di autenticazione con i <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> metodi e:</span><span class="sxs-lookup"><span data-stu-id="3a478-115">In the `Startup.ConfigureServices` method, create the Authentication Middleware services with the <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> and <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> methods:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Startup.cs?name=snippet1)]

<span data-ttu-id="3a478-116"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme>passato a `AddAuthentication` imposta lo schema di autenticazione predefinito per l'app.</span><span class="sxs-lookup"><span data-stu-id="3a478-116"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> passed to `AddAuthentication` sets the default authentication scheme for the app.</span></span> <span data-ttu-id="3a478-117">`AuthenticationScheme`è utile quando sono presenti più istanze dell'autenticazione dei cookie e si desidera [autorizzare con uno schema specifico](xref:security/authorization/limitingidentitybyscheme).</span><span class="sxs-lookup"><span data-stu-id="3a478-117">`AuthenticationScheme` is useful when there are multiple instances of cookie authentication and you want to [authorize with a specific scheme](xref:security/authorization/limitingidentitybyscheme).</span></span> <span data-ttu-id="3a478-118">L'impostazione `AuthenticationScheme` di su [CookieAuthenticationDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) fornisce il valore "cookie" per lo schema.</span><span class="sxs-lookup"><span data-stu-id="3a478-118">Setting the `AuthenticationScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) provides a value of "Cookies" for the scheme.</span></span> <span data-ttu-id="3a478-119">È possibile specificare qualsiasi valore stringa che distingua lo schema.</span><span class="sxs-lookup"><span data-stu-id="3a478-119">You can supply any string value that distinguishes the scheme.</span></span>

<span data-ttu-id="3a478-120">Lo schema di autenticazione dell'app è diverso dallo schema di autenticazione dei cookie dell'app.</span><span class="sxs-lookup"><span data-stu-id="3a478-120">The app's authentication scheme is different from the app's cookie authentication scheme.</span></span> <span data-ttu-id="3a478-121">Quando non viene fornito uno schema di autenticazione dei cookie a <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> , viene usato `CookieAuthenticationDefaults.AuthenticationScheme` ("cookie").</span><span class="sxs-lookup"><span data-stu-id="3a478-121">When a cookie authentication scheme isn't provided to <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, it uses `CookieAuthenticationDefaults.AuthenticationScheme` ("Cookies").</span></span>

<span data-ttu-id="3a478-122">La proprietà del cookie di autenticazione <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> è impostata su per `true` impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="3a478-122">The authentication cookie's <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> property is set to `true` by default.</span></span> <span data-ttu-id="3a478-123">I cookie di autenticazione sono consentiti quando un visitatore del sito non ha acconsentito alla raccolta dei dati.</span><span class="sxs-lookup"><span data-stu-id="3a478-123">Authentication cookies are allowed when a site visitor hasn't consented to data collection.</span></span> <span data-ttu-id="3a478-124">Per altre informazioni, vedere <xref:security/gdpr#essential-cookies>.</span><span class="sxs-lookup"><span data-stu-id="3a478-124">For more information, see <xref:security/gdpr#essential-cookies>.</span></span>

<span data-ttu-id="3a478-125">In `Startup.Configure` , chiamare `UseAuthentication` e `UseAuthorization` per impostare la `HttpContext.User` proprietà ed eseguire il middleware di autorizzazione per le richieste.</span><span class="sxs-lookup"><span data-stu-id="3a478-125">In `Startup.Configure`, call `UseAuthentication` and `UseAuthorization` to set the `HttpContext.User` property and run Authorization Middleware for requests.</span></span> <span data-ttu-id="3a478-126">Chiamare i `UseAuthentication` `UseAuthorization` metodi e prima di chiamare `UseEndpoints` :</span><span class="sxs-lookup"><span data-stu-id="3a478-126">Call the `UseAuthentication` and `UseAuthorization` methods before calling `UseEndpoints`:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Startup.cs?name=snippet2)]

<span data-ttu-id="3a478-127">La <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> classe viene utilizzata per configurare le opzioni del provider di autenticazione.</span><span class="sxs-lookup"><span data-stu-id="3a478-127">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> class is used to configure the authentication provider options.</span></span>

<span data-ttu-id="3a478-128">Impostare `CookieAuthenticationOptions` nella configurazione del servizio per l'autenticazione nel `Startup.ConfigureServices` Metodo:</span><span class="sxs-lookup"><span data-stu-id="3a478-128">Set `CookieAuthenticationOptions` in the service configuration for authentication in the `Startup.ConfigureServices` method:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        ...
    });
```

## <a name="cookie-policy-middleware"></a><span data-ttu-id="3a478-129">Middleware dei criteri dei cookie</span><span class="sxs-lookup"><span data-stu-id="3a478-129">Cookie Policy Middleware</span></span>

<span data-ttu-id="3a478-130">Il [middleware dei criteri](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) dei cookie Abilita le funzionalità dei criteri dei cookie.</span><span class="sxs-lookup"><span data-stu-id="3a478-130">[Cookie Policy Middleware](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) enables cookie policy capabilities.</span></span> <span data-ttu-id="3a478-131">L'aggiunta del middleware alla pipeline di elaborazione delle app è sensibile agli ordini &mdash; , ma interessa solo i componenti downstream registrati nella pipeline.</span><span class="sxs-lookup"><span data-stu-id="3a478-131">Adding the middleware to the app processing pipeline is order sensitive&mdash;it only affects downstream components registered in the pipeline.</span></span>

```csharp
app.UseCookiePolicy(cookiePolicyOptions);
```

<span data-ttu-id="3a478-132">Usare <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> fornito al middleware dei criteri dei cookie per controllare le caratteristiche globali dell'elaborazione dei cookie e l'hook nei gestori di elaborazione dei cookie quando i cookie vengono aggiunti o eliminati.</span><span class="sxs-lookup"><span data-stu-id="3a478-132">Use <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> provided to the Cookie Policy Middleware to control global characteristics of cookie processing and hook into cookie processing handlers when cookies are appended or deleted.</span></span>

<span data-ttu-id="3a478-133">Il <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> valore predefinito è `SameSiteMode.Lax` consentire l'autenticazione OAuth2.</span><span class="sxs-lookup"><span data-stu-id="3a478-133">The default <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> value is `SameSiteMode.Lax` to permit OAuth2 authentication.</span></span> <span data-ttu-id="3a478-134">Per applicare rigorosamente un criterio dello stesso sito di `SameSiteMode.Strict` , impostare il `MinimumSameSitePolicy` .</span><span class="sxs-lookup"><span data-stu-id="3a478-134">To strictly enforce a same-site policy of `SameSiteMode.Strict`, set the `MinimumSameSitePolicy`.</span></span> <span data-ttu-id="3a478-135">Sebbene questa impostazione interrompa OAuth2 e altri schemi di autenticazione tra le origini, eleva il livello di sicurezza dei cookie per altri tipi di app che non si basano sull'elaborazione di richieste tra origini.</span><span class="sxs-lookup"><span data-stu-id="3a478-135">Although this setting breaks OAuth2 and other cross-origin authentication schemes, it elevates the level of cookie security for other types of apps that don't rely on cross-origin request processing.</span></span>

```csharp
var cookiePolicyOptions = new CookiePolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

<span data-ttu-id="3a478-136">L'impostazione del middleware per i criteri dei cookie per `MinimumSameSitePolicy` può influire sull'impostazione di `Cookie.SameSite` in `CookieAuthenticationOptions` impostazioni in base alla matrice riportata di seguito.</span><span class="sxs-lookup"><span data-stu-id="3a478-136">The Cookie Policy Middleware setting for `MinimumSameSitePolicy` can affect the setting of `Cookie.SameSite` in `CookieAuthenticationOptions` settings according to the matrix below.</span></span>

| <span data-ttu-id="3a478-137">MinimumSameSitePolicy</span><span class="sxs-lookup"><span data-stu-id="3a478-137">MinimumSameSitePolicy</span></span> | <span data-ttu-id="3a478-138">Cookie. navigava sullostesso sito</span><span class="sxs-lookup"><span data-stu-id="3a478-138">Cookie.SameSite</span></span> | <span data-ttu-id="3a478-139">Impostazione cookie. navigava sullostesso sito risultante</span><span class="sxs-lookup"><span data-stu-id="3a478-139">Resultant Cookie.SameSite setting</span></span> |
| --------------------- | --------------- | --------------------------------- |
| <span data-ttu-id="3a478-140">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="3a478-140">SameSiteMode.None</span></span>     | <span data-ttu-id="3a478-141">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="3a478-141">SameSiteMode.None</span></span><br><span data-ttu-id="3a478-142">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="3a478-142">SameSiteMode.Lax</span></span><br><span data-ttu-id="3a478-143">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="3a478-143">SameSiteMode.Strict</span></span> | <span data-ttu-id="3a478-144">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="3a478-144">SameSiteMode.None</span></span><br><span data-ttu-id="3a478-145">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="3a478-145">SameSiteMode.Lax</span></span><br><span data-ttu-id="3a478-146">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="3a478-146">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="3a478-147">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="3a478-147">SameSiteMode.Lax</span></span>      | <span data-ttu-id="3a478-148">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="3a478-148">SameSiteMode.None</span></span><br><span data-ttu-id="3a478-149">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="3a478-149">SameSiteMode.Lax</span></span><br><span data-ttu-id="3a478-150">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="3a478-150">SameSiteMode.Strict</span></span> | <span data-ttu-id="3a478-151">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="3a478-151">SameSiteMode.Lax</span></span><br><span data-ttu-id="3a478-152">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="3a478-152">SameSiteMode.Lax</span></span><br><span data-ttu-id="3a478-153">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="3a478-153">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="3a478-154">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="3a478-154">SameSiteMode.Strict</span></span>   | <span data-ttu-id="3a478-155">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="3a478-155">SameSiteMode.None</span></span><br><span data-ttu-id="3a478-156">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="3a478-156">SameSiteMode.Lax</span></span><br><span data-ttu-id="3a478-157">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="3a478-157">SameSiteMode.Strict</span></span> | <span data-ttu-id="3a478-158">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="3a478-158">SameSiteMode.Strict</span></span><br><span data-ttu-id="3a478-159">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="3a478-159">SameSiteMode.Strict</span></span><br><span data-ttu-id="3a478-160">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="3a478-160">SameSiteMode.Strict</span></span> |

## <a name="create-an-authentication-cookie"></a><span data-ttu-id="3a478-161">Creare un cookie di autenticazione</span><span class="sxs-lookup"><span data-stu-id="3a478-161">Create an authentication cookie</span></span>

<span data-ttu-id="3a478-162">Per creare un cookie che contiene informazioni sull'utente, costruire un oggetto <xref:System.Security.Claims.ClaimsPrincipal> .</span><span class="sxs-lookup"><span data-stu-id="3a478-162">To create a cookie holding user information, construct a <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="3a478-163">Le informazioni utente vengono serializzate e archiviate nel cookie.</span><span class="sxs-lookup"><span data-stu-id="3a478-163">The user information is serialized and stored in the cookie.</span></span> 

<span data-ttu-id="3a478-164">Creare una <xref:System.Security.Claims.ClaimsIdentity> con qualsiasi richiesta <xref:System.Security.Claims.Claim> e chiamare <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> per accedere all'utente:</span><span class="sxs-lookup"><span data-stu-id="3a478-164">Create a <xref:System.Security.Claims.ClaimsIdentity> with any required <xref:System.Security.Claims.Claim>s and call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> to sign in the user:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet1)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]

<span data-ttu-id="3a478-165">`SignInAsync`Crea un cookie crittografato e lo aggiunge alla risposta corrente.</span><span class="sxs-lookup"><span data-stu-id="3a478-165">`SignInAsync` creates an encrypted cookie and adds it to the current response.</span></span> <span data-ttu-id="3a478-166">Se `AuthenticationScheme` non è specificato, viene usato lo schema predefinito.</span><span class="sxs-lookup"><span data-stu-id="3a478-166">If `AuthenticationScheme` isn't specified, the default scheme is used.</span></span>

<span data-ttu-id="3a478-167"><xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.RedirectUri>viene utilizzato solo in alcuni percorsi specifici per impostazione predefinita, ad esempio il percorso di accesso e i percorsi di disconnessione.</span><span class="sxs-lookup"><span data-stu-id="3a478-167"><xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.RedirectUri> is only used on a few specific paths by default, for example, the login path and logout paths.</span></span> <span data-ttu-id="3a478-168">Per altre informazioni, vedere l' [origine CookieAuthenticationHandler](https://github.com/dotnet/aspnetcore/blob/f2e6e6ff334176540ef0b3291122e359c2106d1a/src/Security/Authentication/Cookies/src/CookieAuthenticationHandler.cs#L334).</span><span class="sxs-lookup"><span data-stu-id="3a478-168">For more information see the [CookieAuthenticationHandler source](https://github.com/dotnet/aspnetcore/blob/f2e6e6ff334176540ef0b3291122e359c2106d1a/src/Security/Authentication/Cookies/src/CookieAuthenticationHandler.cs#L334).</span></span>

<span data-ttu-id="3a478-169">Per la crittografia viene usato il sistema di [protezione dei dati](xref:security/data-protection/using-data-protection) di ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="3a478-169">ASP.NET Core's [Data Protection](xref:security/data-protection/using-data-protection) system is used for encryption.</span></span> <span data-ttu-id="3a478-170">Per un'app ospitata in più computer, bilanciamento del carico tra app o uso di un Web farm, [configurare la protezione dei dati](xref:security/data-protection/configuration/overview) in modo da usare lo stesso anello chiave e l'identificatore dell'app.</span><span class="sxs-lookup"><span data-stu-id="3a478-170">For an app hosted on multiple machines, load balancing across apps, or using a web farm, [configure data protection](xref:security/data-protection/configuration/overview) to use the same key ring and app identifier.</span></span>

## <a name="sign-out"></a><span data-ttu-id="3a478-171">Disconnetti</span><span class="sxs-lookup"><span data-stu-id="3a478-171">Sign out</span></span>

<span data-ttu-id="3a478-172">Per disconnettersi dall'utente corrente ed eliminare il cookie, chiamare <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*> :</span><span class="sxs-lookup"><span data-stu-id="3a478-172">To sign out the current user and delete their cookie, call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet2)]

<span data-ttu-id="3a478-173">Se `CookieAuthenticationDefaults.AuthenticationScheme` (o "cookie") non viene usato come schema (ad esempio, "ContosoCookie"), fornire lo schema usato durante la configurazione del provider di autenticazione.</span><span class="sxs-lookup"><span data-stu-id="3a478-173">If `CookieAuthenticationDefaults.AuthenticationScheme` (or "Cookies") isn't used as the scheme (for example, "ContosoCookie"), supply the scheme used when configuring the authentication provider.</span></span> <span data-ttu-id="3a478-174">In caso contrario, verrà utilizzato lo schema predefinito.</span><span class="sxs-lookup"><span data-stu-id="3a478-174">Otherwise, the default scheme is used.</span></span>

## <a name="react-to-back-end-changes"></a><span data-ttu-id="3a478-175">Reagire alle modifiche del back-end</span><span class="sxs-lookup"><span data-stu-id="3a478-175">React to back-end changes</span></span>

<span data-ttu-id="3a478-176">Una volta creato un cookie, il cookie è l'unica origine di identità.</span><span class="sxs-lookup"><span data-stu-id="3a478-176">Once a cookie is created, the cookie is the single source of identity.</span></span> <span data-ttu-id="3a478-177">Se un account utente è disabilitato nei sistemi back-end:</span><span class="sxs-lookup"><span data-stu-id="3a478-177">If a user account is disabled in back-end systems:</span></span>

* <span data-ttu-id="3a478-178">Il sistema di autenticazione dei cookie dell'app continua a elaborare le richieste in base al cookie di autenticazione.</span><span class="sxs-lookup"><span data-stu-id="3a478-178">The app's cookie authentication system continues to process requests based on the authentication cookie.</span></span>
* <span data-ttu-id="3a478-179">L'utente rimane connesso all'app fino a quando il cookie di autenticazione è valido.</span><span class="sxs-lookup"><span data-stu-id="3a478-179">The user remains signed into the app as long as the authentication cookie is valid.</span></span>

<span data-ttu-id="3a478-180">L' <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> evento può essere usato per intercettare ed eseguire l'override della convalida dell'identità del cookie.</span><span class="sxs-lookup"><span data-stu-id="3a478-180">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> event can be used to intercept and override validation of the cookie identity.</span></span> <span data-ttu-id="3a478-181">La convalida del cookie a ogni richiesta attenua il rischio di revocare gli utenti che accedono all'app.</span><span class="sxs-lookup"><span data-stu-id="3a478-181">Validating the cookie on every request mitigates the risk of revoked users accessing the app.</span></span>

<span data-ttu-id="3a478-182">Un approccio alla convalida dei cookie si basa sul tenere traccia del momento in cui il database utente viene modificato.</span><span class="sxs-lookup"><span data-stu-id="3a478-182">One approach to cookie validation is based on keeping track of when the user database changes.</span></span> <span data-ttu-id="3a478-183">Se il database non è stato modificato dopo l'emissione del cookie dell'utente, non è necessario autenticare di nuovo l'utente se il cookie è ancora valido.</span><span class="sxs-lookup"><span data-stu-id="3a478-183">If the database hasn't been changed since the user's cookie was issued, there's no need to re-authenticate the user if their cookie is still valid.</span></span> <span data-ttu-id="3a478-184">Nell'app di esempio, il database viene implementato in `IUserRepository` e archivia un `LastChanged` valore.</span><span class="sxs-lookup"><span data-stu-id="3a478-184">In the sample app, the database is implemented in `IUserRepository` and stores a `LastChanged` value.</span></span> <span data-ttu-id="3a478-185">Quando un utente viene aggiornato nel database, il `LastChanged` valore viene impostato sull'ora corrente.</span><span class="sxs-lookup"><span data-stu-id="3a478-185">When a user is updated in the database, the `LastChanged` value is set to the current time.</span></span>

<span data-ttu-id="3a478-186">Per invalidare un cookie quando il database viene modificato in base al `LastChanged` valore, creare il cookie con un' `LastChanged` attestazione contenente il `LastChanged` valore corrente dal database:</span><span class="sxs-lookup"><span data-stu-id="3a478-186">In order to invalidate a cookie when the database changes based on the `LastChanged` value, create the cookie with a `LastChanged` claim containing the current `LastChanged` value from the database:</span></span>

```csharp
var claims = new List<Claim>
{
    new Claim(ClaimTypes.Name, user.Email),
    new Claim("LastChanged", {Database Value})
};

var claimsIdentity = new ClaimsIdentity(
    claims, 
    CookieAuthenticationDefaults.AuthenticationScheme);

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme, 
    new ClaimsPrincipal(claimsIdentity));
```

<span data-ttu-id="3a478-187">Per implementare una sostituzione per l' `ValidatePrincipal` evento, scrivere un metodo con la firma seguente in una classe che deriva da <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents> :</span><span class="sxs-lookup"><span data-stu-id="3a478-187">To implement an override for the `ValidatePrincipal` event, write a method with the following signature in a class that derives from <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>:</span></span>

```csharp
ValidatePrincipal(CookieValidatePrincipalContext)
```

<span data-ttu-id="3a478-188">Di seguito è riportato un esempio di implementazione di `CookieAuthenticationEvents` :</span><span class="sxs-lookup"><span data-stu-id="3a478-188">The following is an example implementation of `CookieAuthenticationEvents`:</span></span>

```csharp
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Authentication;
using Microsoft.AspNetCore.Authentication.Cookies;

public class CustomCookieAuthenticationEvents : CookieAuthenticationEvents
{
    private readonly IUserRepository _userRepository;

    public CustomCookieAuthenticationEvents(IUserRepository userRepository)
    {
        // Get the database from registered DI services.
        _userRepository = userRepository;
    }

    public override async Task ValidatePrincipal(CookieValidatePrincipalContext context)
    {
        var userPrincipal = context.Principal;

        // Look for the LastChanged claim.
        var lastChanged = (from c in userPrincipal.Claims
                           where c.Type == "LastChanged"
                           select c.Value).FirstOrDefault();

        if (string.IsNullOrEmpty(lastChanged) ||
            !_userRepository.ValidateLastChanged(lastChanged))
        {
            context.RejectPrincipal();

            await context.HttpContext.SignOutAsync(
                CookieAuthenticationDefaults.AuthenticationScheme);
        }
    }
}
```

<span data-ttu-id="3a478-189">Registrare l'istanza degli eventi durante la registrazione del servizio cookie nel `Startup.ConfigureServices` metodo.</span><span class="sxs-lookup"><span data-stu-id="3a478-189">Register the events instance during cookie service registration in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="3a478-190">Fornire una [registrazione del servizio con ambito](xref:fundamentals/dependency-injection#service-lifetimes) per la `CustomCookieAuthenticationEvents` classe:</span><span class="sxs-lookup"><span data-stu-id="3a478-190">Provide a [scoped service registration](xref:fundamentals/dependency-injection#service-lifetimes) for your `CustomCookieAuthenticationEvents` class:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.EventsType = typeof(CustomCookieAuthenticationEvents);
    });

services.AddScoped<CustomCookieAuthenticationEvents>();
```

<span data-ttu-id="3a478-191">Si consideri una situazione in cui il nome dell'utente viene aggiornato &mdash; una decisione che non influisce sulla sicurezza in alcun modo.</span><span class="sxs-lookup"><span data-stu-id="3a478-191">Consider a situation in which the user's name is updated&mdash;a decision that doesn't affect security in any way.</span></span> <span data-ttu-id="3a478-192">Se si desidera aggiornare l'entità utente in modo non distruttivo, chiamare `context.ReplacePrincipal` e impostare la `context.ShouldRenew` proprietà su `true` .</span><span class="sxs-lookup"><span data-stu-id="3a478-192">If you want to non-destructively update the user principal, call `context.ReplacePrincipal` and set the `context.ShouldRenew` property to `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="3a478-193">L'approccio descritto qui viene attivato a ogni richiesta.</span><span class="sxs-lookup"><span data-stu-id="3a478-193">The approach described here is triggered on every request.</span></span> <span data-ttu-id="3a478-194">La convalida dei cookie di autenticazione per tutti gli utenti in ogni richiesta può comportare un notevole calo delle prestazioni per l'app.</span><span class="sxs-lookup"><span data-stu-id="3a478-194">Validating authentication cookies for all users on every request can result in a large performance penalty for the app.</span></span>

## <a name="persistent-cookies"></a><span data-ttu-id="3a478-195">Cookie permanenti</span><span class="sxs-lookup"><span data-stu-id="3a478-195">Persistent cookies</span></span>

<span data-ttu-id="3a478-196">È possibile che il cookie venga mantenuto tra le sessioni del browser.</span><span class="sxs-lookup"><span data-stu-id="3a478-196">You may want the cookie to persist across browser sessions.</span></span> <span data-ttu-id="3a478-197">Questa persistenza deve essere abilitata solo con il consenso esplicito dell'utente con una casella di controllo "Remember me" all'accesso o un meccanismo simile.</span><span class="sxs-lookup"><span data-stu-id="3a478-197">This persistence should only be enabled with explicit user consent with a "Remember Me" check box on sign in or a similar mechanism.</span></span> 

<span data-ttu-id="3a478-198">Il frammento di codice seguente crea un'identità e un cookie corrispondente che sopravvivono attraverso le chiusure del browser.</span><span class="sxs-lookup"><span data-stu-id="3a478-198">The following code snippet creates an identity and corresponding cookie that survives through browser closures.</span></span> <span data-ttu-id="3a478-199">Vengono rispettate tutte le impostazioni di scadenza scorrevoli configurate in precedenza.</span><span class="sxs-lookup"><span data-stu-id="3a478-199">Any sliding expiration settings previously configured are honored.</span></span> <span data-ttu-id="3a478-200">Se il cookie scade quando il browser viene chiuso, il cookie viene cancellato dopo il riavvio.</span><span class="sxs-lookup"><span data-stu-id="3a478-200">If the cookie expires while the browser is closed, the browser clears the cookie once it's restarted.</span></span>

<span data-ttu-id="3a478-201">Imposta <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> su `true` in <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties> :</span><span class="sxs-lookup"><span data-stu-id="3a478-201">Set <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> to `true` in <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>:</span></span>

```csharp
// using Microsoft.AspNetCore.Authentication;

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claimsIdentity),
    new AuthenticationProperties
    {
        IsPersistent = true
    });
```

## <a name="absolute-cookie-expiration"></a><span data-ttu-id="3a478-202">Scadenza assoluta del cookie</span><span class="sxs-lookup"><span data-stu-id="3a478-202">Absolute cookie expiration</span></span>

<span data-ttu-id="3a478-203">È possibile impostare un'ora di scadenza assoluta con <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc> .</span><span class="sxs-lookup"><span data-stu-id="3a478-203">An absolute expiration time can be set with <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>.</span></span> <span data-ttu-id="3a478-204">Per creare un cookie persistente, `IsPersistent` è necessario impostare anche.</span><span class="sxs-lookup"><span data-stu-id="3a478-204">To create a persistent cookie, `IsPersistent` must also be set.</span></span> <span data-ttu-id="3a478-205">In caso contrario, il cookie viene creato con una durata basata sulla sessione e potrebbe scadere prima o dopo il ticket di autenticazione che possiede.</span><span class="sxs-lookup"><span data-stu-id="3a478-205">Otherwise, the cookie is created with a session-based lifetime and could expire either before or after the authentication ticket that it holds.</span></span> <span data-ttu-id="3a478-206">Quando `ExpiresUtc` è impostato, esegue l'override del valore dell' <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.ExpireTimeSpan> opzione di <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> , se impostata.</span><span class="sxs-lookup"><span data-stu-id="3a478-206">When `ExpiresUtc` is set, it overrides the value of the <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.ExpireTimeSpan> option of <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions>, if set.</span></span>

<span data-ttu-id="3a478-207">Il frammento di codice seguente crea un'identità e un cookie corrispondente che dura 20 minuti.</span><span class="sxs-lookup"><span data-stu-id="3a478-207">The following code snippet creates an identity and corresponding cookie that lasts for 20 minutes.</span></span> <span data-ttu-id="3a478-208">Questa operazione ignora le impostazioni di scadenza scorrevoli configurate in precedenza.</span><span class="sxs-lookup"><span data-stu-id="3a478-208">This ignores any sliding expiration settings previously configured.</span></span>

```csharp
// using Microsoft.AspNetCore.Authentication;

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claimsIdentity),
    new AuthenticationProperties
    {
        IsPersistent = true,
        ExpiresUtc = DateTime.UtcNow.AddMinutes(20)
    });
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="3a478-209">ASP.NET Core Identity è un provider di autenticazione completo con funzionalità complete per la creazione e la gestione degli account di accesso.</span><span class="sxs-lookup"><span data-stu-id="3a478-209">ASP.NET Core Identity is a complete, full-featured authentication provider for creating and maintaining logins.</span></span> <span data-ttu-id="3a478-210">Tuttavia, è possibile usare un provider di autenticazione basata su cookie senza ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="3a478-210">However, a cookie-based authentication authentication provider without ASP.NET Core Identity can be used.</span></span> <span data-ttu-id="3a478-211">Per altre informazioni, vedere <xref:security/authentication/identity>.</span><span class="sxs-lookup"><span data-stu-id="3a478-211">For more information, see <xref:security/authentication/identity>.</span></span>

<span data-ttu-id="3a478-212">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="3a478-212">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="3a478-213">A scopo dimostrativo nell'app di esempio, l'account utente per l'utente ipotetico, Maria Rodriguez, è hardcoded nell'app.</span><span class="sxs-lookup"><span data-stu-id="3a478-213">For demonstration purposes in the sample app, the user account for the hypothetical user, Maria Rodriguez, is hardcoded into the app.</span></span> <span data-ttu-id="3a478-214">Usare l'indirizzo di **posta elettronica** `maria.rodriguez@contoso.com` e qualsiasi password per accedere all'utente.</span><span class="sxs-lookup"><span data-stu-id="3a478-214">Use the **Email** address `maria.rodriguez@contoso.com` and any password to sign in the user.</span></span> <span data-ttu-id="3a478-215">L'utente viene autenticato nel `AuthenticateUser` metodo nel file *pages/account/login. cshtml. cs* .</span><span class="sxs-lookup"><span data-stu-id="3a478-215">The user is authenticated in the `AuthenticateUser` method in the *Pages/Account/Login.cshtml.cs* file.</span></span> <span data-ttu-id="3a478-216">In un esempio reale, l'utente viene autenticato a fronte di un database.</span><span class="sxs-lookup"><span data-stu-id="3a478-216">In a real-world example, the user would be authenticated against a database.</span></span>

## <a name="configuration"></a><span data-ttu-id="3a478-217">Configurazione</span><span class="sxs-lookup"><span data-stu-id="3a478-217">Configuration</span></span>

<span data-ttu-id="3a478-218">Se l'app non usa il [metapacchetto Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app), creare un riferimento al pacchetto nel file di progetto per il pacchetto [Microsoft. AspNetCore. Authentication. cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) .</span><span class="sxs-lookup"><span data-stu-id="3a478-218">If the app doesn't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference in the project file for the [Microsoft.AspNetCore.Authentication.Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) package.</span></span>

<span data-ttu-id="3a478-219">Nel `Startup.ConfigureServices` metodo creare il servizio middleware di autenticazione con i <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> metodi e <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> :</span><span class="sxs-lookup"><span data-stu-id="3a478-219">In the `Startup.ConfigureServices` method, create the Authentication Middleware service with the <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> and <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> methods:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet1)]

<span data-ttu-id="3a478-220"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme>passato a `AddAuthentication` imposta lo schema di autenticazione predefinito per l'app.</span><span class="sxs-lookup"><span data-stu-id="3a478-220"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> passed to `AddAuthentication` sets the default authentication scheme for the app.</span></span> <span data-ttu-id="3a478-221">`AuthenticationScheme`è utile quando sono presenti più istanze dell'autenticazione dei cookie e si desidera [autorizzare con uno schema specifico](xref:security/authorization/limitingidentitybyscheme).</span><span class="sxs-lookup"><span data-stu-id="3a478-221">`AuthenticationScheme` is useful when there are multiple instances of cookie authentication and you want to [authorize with a specific scheme](xref:security/authorization/limitingidentitybyscheme).</span></span> <span data-ttu-id="3a478-222">L'impostazione `AuthenticationScheme` di su [CookieAuthenticationDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) fornisce il valore "cookie" per lo schema.</span><span class="sxs-lookup"><span data-stu-id="3a478-222">Setting the `AuthenticationScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) provides a value of "Cookies" for the scheme.</span></span> <span data-ttu-id="3a478-223">È possibile specificare qualsiasi valore stringa che distingua lo schema.</span><span class="sxs-lookup"><span data-stu-id="3a478-223">You can supply any string value that distinguishes the scheme.</span></span>

<span data-ttu-id="3a478-224">Lo schema di autenticazione dell'app è diverso dallo schema di autenticazione dei cookie dell'app.</span><span class="sxs-lookup"><span data-stu-id="3a478-224">The app's authentication scheme is different from the app's cookie authentication scheme.</span></span> <span data-ttu-id="3a478-225">Quando non viene fornito uno schema di autenticazione dei cookie a <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> , viene usato `CookieAuthenticationDefaults.AuthenticationScheme` ("cookie").</span><span class="sxs-lookup"><span data-stu-id="3a478-225">When a cookie authentication scheme isn't provided to <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, it uses `CookieAuthenticationDefaults.AuthenticationScheme` ("Cookies").</span></span>

<span data-ttu-id="3a478-226">La proprietà del cookie di autenticazione <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> è impostata su per `true` impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="3a478-226">The authentication cookie's <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> property is set to `true` by default.</span></span> <span data-ttu-id="3a478-227">I cookie di autenticazione sono consentiti quando un visitatore del sito non ha acconsentito alla raccolta dei dati.</span><span class="sxs-lookup"><span data-stu-id="3a478-227">Authentication cookies are allowed when a site visitor hasn't consented to data collection.</span></span> <span data-ttu-id="3a478-228">Per altre informazioni, vedere <xref:security/gdpr#essential-cookies>.</span><span class="sxs-lookup"><span data-stu-id="3a478-228">For more information, see <xref:security/gdpr#essential-cookies>.</span></span>

<span data-ttu-id="3a478-229">Nel `Startup.Configure` metodo chiamare il `UseAuthentication` metodo per richiamare il middleware di autenticazione che imposta la `HttpContext.User` Proprietà.</span><span class="sxs-lookup"><span data-stu-id="3a478-229">In the `Startup.Configure` method, call the `UseAuthentication` method to invoke the Authentication Middleware that sets the `HttpContext.User` property.</span></span> <span data-ttu-id="3a478-230">Chiamare il `UseAuthentication` metodo prima di chiamare `UseMvcWithDefaultRoute` o `UseMvc` :</span><span class="sxs-lookup"><span data-stu-id="3a478-230">Call the `UseAuthentication` method before calling `UseMvcWithDefaultRoute` or `UseMvc`:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet2)]

<span data-ttu-id="3a478-231">La <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> classe viene utilizzata per configurare le opzioni del provider di autenticazione.</span><span class="sxs-lookup"><span data-stu-id="3a478-231">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> class is used to configure the authentication provider options.</span></span>

<span data-ttu-id="3a478-232">Impostare `CookieAuthenticationOptions` nella configurazione del servizio per l'autenticazione nel `Startup.ConfigureServices` Metodo:</span><span class="sxs-lookup"><span data-stu-id="3a478-232">Set `CookieAuthenticationOptions` in the service configuration for authentication in the `Startup.ConfigureServices` method:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        ...
    });
```

## <a name="cookie-policy-middleware"></a><span data-ttu-id="3a478-233">Middleware dei criteri dei cookie</span><span class="sxs-lookup"><span data-stu-id="3a478-233">Cookie Policy Middleware</span></span>

<span data-ttu-id="3a478-234">Il [middleware dei criteri](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) dei cookie Abilita le funzionalità dei criteri dei cookie.</span><span class="sxs-lookup"><span data-stu-id="3a478-234">[Cookie Policy Middleware](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) enables cookie policy capabilities.</span></span> <span data-ttu-id="3a478-235">L'aggiunta del middleware alla pipeline di elaborazione delle app è sensibile agli ordini &mdash; , ma interessa solo i componenti downstream registrati nella pipeline.</span><span class="sxs-lookup"><span data-stu-id="3a478-235">Adding the middleware to the app processing pipeline is order sensitive&mdash;it only affects downstream components registered in the pipeline.</span></span>

```csharp
app.UseCookiePolicy(cookiePolicyOptions);
```

<span data-ttu-id="3a478-236">Usare <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> fornito al middleware dei criteri dei cookie per controllare le caratteristiche globali dell'elaborazione dei cookie e l'hook nei gestori di elaborazione dei cookie quando i cookie vengono aggiunti o eliminati.</span><span class="sxs-lookup"><span data-stu-id="3a478-236">Use <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> provided to the Cookie Policy Middleware to control global characteristics of cookie processing and hook into cookie processing handlers when cookies are appended or deleted.</span></span>

<span data-ttu-id="3a478-237">Il <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> valore predefinito è `SameSiteMode.Lax` consentire l'autenticazione OAuth2.</span><span class="sxs-lookup"><span data-stu-id="3a478-237">The default <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> value is `SameSiteMode.Lax` to permit OAuth2 authentication.</span></span> <span data-ttu-id="3a478-238">Per applicare rigorosamente un criterio dello stesso sito di `SameSiteMode.Strict` , impostare il `MinimumSameSitePolicy` .</span><span class="sxs-lookup"><span data-stu-id="3a478-238">To strictly enforce a same-site policy of `SameSiteMode.Strict`, set the `MinimumSameSitePolicy`.</span></span> <span data-ttu-id="3a478-239">Sebbene questa impostazione interrompa OAuth2 e altri schemi di autenticazione tra le origini, eleva il livello di sicurezza dei cookie per altri tipi di app che non si basano sull'elaborazione di richieste tra origini.</span><span class="sxs-lookup"><span data-stu-id="3a478-239">Although this setting breaks OAuth2 and other cross-origin authentication schemes, it elevates the level of cookie security for other types of apps that don't rely on cross-origin request processing.</span></span>

```csharp
var cookiePolicyOptions = new CookiePolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

<span data-ttu-id="3a478-240">L'impostazione del middleware per i criteri dei cookie per `MinimumSameSitePolicy` può influire sull'impostazione di `Cookie.SameSite` in `CookieAuthenticationOptions` impostazioni in base alla matrice riportata di seguito.</span><span class="sxs-lookup"><span data-stu-id="3a478-240">The Cookie Policy Middleware setting for `MinimumSameSitePolicy` can affect the setting of `Cookie.SameSite` in `CookieAuthenticationOptions` settings according to the matrix below.</span></span>

| <span data-ttu-id="3a478-241">MinimumSameSitePolicy</span><span class="sxs-lookup"><span data-stu-id="3a478-241">MinimumSameSitePolicy</span></span> | <span data-ttu-id="3a478-242">Cookie. navigava sullostesso sito</span><span class="sxs-lookup"><span data-stu-id="3a478-242">Cookie.SameSite</span></span> | <span data-ttu-id="3a478-243">Impostazione cookie. navigava sullostesso sito risultante</span><span class="sxs-lookup"><span data-stu-id="3a478-243">Resultant Cookie.SameSite setting</span></span> |
| --------------------- | --------------- | --------------------------------- |
| <span data-ttu-id="3a478-244">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="3a478-244">SameSiteMode.None</span></span>     | <span data-ttu-id="3a478-245">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="3a478-245">SameSiteMode.None</span></span><br><span data-ttu-id="3a478-246">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="3a478-246">SameSiteMode.Lax</span></span><br><span data-ttu-id="3a478-247">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="3a478-247">SameSiteMode.Strict</span></span> | <span data-ttu-id="3a478-248">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="3a478-248">SameSiteMode.None</span></span><br><span data-ttu-id="3a478-249">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="3a478-249">SameSiteMode.Lax</span></span><br><span data-ttu-id="3a478-250">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="3a478-250">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="3a478-251">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="3a478-251">SameSiteMode.Lax</span></span>      | <span data-ttu-id="3a478-252">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="3a478-252">SameSiteMode.None</span></span><br><span data-ttu-id="3a478-253">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="3a478-253">SameSiteMode.Lax</span></span><br><span data-ttu-id="3a478-254">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="3a478-254">SameSiteMode.Strict</span></span> | <span data-ttu-id="3a478-255">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="3a478-255">SameSiteMode.Lax</span></span><br><span data-ttu-id="3a478-256">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="3a478-256">SameSiteMode.Lax</span></span><br><span data-ttu-id="3a478-257">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="3a478-257">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="3a478-258">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="3a478-258">SameSiteMode.Strict</span></span>   | <span data-ttu-id="3a478-259">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="3a478-259">SameSiteMode.None</span></span><br><span data-ttu-id="3a478-260">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="3a478-260">SameSiteMode.Lax</span></span><br><span data-ttu-id="3a478-261">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="3a478-261">SameSiteMode.Strict</span></span> | <span data-ttu-id="3a478-262">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="3a478-262">SameSiteMode.Strict</span></span><br><span data-ttu-id="3a478-263">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="3a478-263">SameSiteMode.Strict</span></span><br><span data-ttu-id="3a478-264">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="3a478-264">SameSiteMode.Strict</span></span> |

## <a name="create-an-authentication-cookie"></a><span data-ttu-id="3a478-265">Creare un cookie di autenticazione</span><span class="sxs-lookup"><span data-stu-id="3a478-265">Create an authentication cookie</span></span>

<span data-ttu-id="3a478-266">Per creare un cookie che contiene informazioni sull'utente, costruire un oggetto <xref:System.Security.Claims.ClaimsPrincipal> .</span><span class="sxs-lookup"><span data-stu-id="3a478-266">To create a cookie holding user information, construct a <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="3a478-267">Le informazioni utente vengono serializzate e archiviate nel cookie.</span><span class="sxs-lookup"><span data-stu-id="3a478-267">The user information is serialized and stored in the cookie.</span></span> 

<span data-ttu-id="3a478-268">Creare una <xref:System.Security.Claims.ClaimsIdentity> con qualsiasi richiesta <xref:System.Security.Claims.Claim> e chiamare <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> per accedere all'utente:</span><span class="sxs-lookup"><span data-stu-id="3a478-268">Create a <xref:System.Security.Claims.ClaimsIdentity> with any required <xref:System.Security.Claims.Claim>s and call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> to sign in the user:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet1)]

<span data-ttu-id="3a478-269">`SignInAsync`Crea un cookie crittografato e lo aggiunge alla risposta corrente.</span><span class="sxs-lookup"><span data-stu-id="3a478-269">`SignInAsync` creates an encrypted cookie and adds it to the current response.</span></span> <span data-ttu-id="3a478-270">Se `AuthenticationScheme` non è specificato, viene usato lo schema predefinito.</span><span class="sxs-lookup"><span data-stu-id="3a478-270">If `AuthenticationScheme` isn't specified, the default scheme is used.</span></span>

<span data-ttu-id="3a478-271">Per la crittografia viene usato il sistema di [protezione dei dati](xref:security/data-protection/using-data-protection) di ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="3a478-271">ASP.NET Core's [Data Protection](xref:security/data-protection/using-data-protection) system is used for encryption.</span></span> <span data-ttu-id="3a478-272">Per un'app ospitata in più computer, bilanciamento del carico tra app o uso di un Web farm, [configurare la protezione dei dati](xref:security/data-protection/configuration/overview) in modo da usare lo stesso anello chiave e l'identificatore dell'app.</span><span class="sxs-lookup"><span data-stu-id="3a478-272">For an app hosted on multiple machines, load balancing across apps, or using a web farm, [configure data protection](xref:security/data-protection/configuration/overview) to use the same key ring and app identifier.</span></span>

## <a name="sign-out"></a><span data-ttu-id="3a478-273">Disconnetti</span><span class="sxs-lookup"><span data-stu-id="3a478-273">Sign out</span></span>

<span data-ttu-id="3a478-274">Per disconnettersi dall'utente corrente ed eliminare il cookie, chiamare <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*> :</span><span class="sxs-lookup"><span data-stu-id="3a478-274">To sign out the current user and delete their cookie, call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet2)]

<span data-ttu-id="3a478-275">Se `CookieAuthenticationDefaults.AuthenticationScheme` (o "cookie") non viene usato come schema (ad esempio, "ContosoCookie"), fornire lo schema usato durante la configurazione del provider di autenticazione.</span><span class="sxs-lookup"><span data-stu-id="3a478-275">If `CookieAuthenticationDefaults.AuthenticationScheme` (or "Cookies") isn't used as the scheme (for example, "ContosoCookie"), supply the scheme used when configuring the authentication provider.</span></span> <span data-ttu-id="3a478-276">In caso contrario, verrà utilizzato lo schema predefinito.</span><span class="sxs-lookup"><span data-stu-id="3a478-276">Otherwise, the default scheme is used.</span></span>

## <a name="react-to-back-end-changes"></a><span data-ttu-id="3a478-277">Reagire alle modifiche del back-end</span><span class="sxs-lookup"><span data-stu-id="3a478-277">React to back-end changes</span></span>

<span data-ttu-id="3a478-278">Una volta creato un cookie, il cookie è l'unica origine di identità.</span><span class="sxs-lookup"><span data-stu-id="3a478-278">Once a cookie is created, the cookie is the single source of identity.</span></span> <span data-ttu-id="3a478-279">Se un account utente è disabilitato nei sistemi back-end:</span><span class="sxs-lookup"><span data-stu-id="3a478-279">If a user account is disabled in back-end systems:</span></span>

* <span data-ttu-id="3a478-280">Il sistema di autenticazione dei cookie dell'app continua a elaborare le richieste in base al cookie di autenticazione.</span><span class="sxs-lookup"><span data-stu-id="3a478-280">The app's cookie authentication system continues to process requests based on the authentication cookie.</span></span>
* <span data-ttu-id="3a478-281">L'utente rimane connesso all'app fino a quando il cookie di autenticazione è valido.</span><span class="sxs-lookup"><span data-stu-id="3a478-281">The user remains signed into the app as long as the authentication cookie is valid.</span></span>

<span data-ttu-id="3a478-282">L' <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> evento può essere usato per intercettare ed eseguire l'override della convalida dell'identità del cookie.</span><span class="sxs-lookup"><span data-stu-id="3a478-282">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> event can be used to intercept and override validation of the cookie identity.</span></span> <span data-ttu-id="3a478-283">La convalida del cookie a ogni richiesta attenua il rischio di revocare gli utenti che accedono all'app.</span><span class="sxs-lookup"><span data-stu-id="3a478-283">Validating the cookie on every request mitigates the risk of revoked users accessing the app.</span></span>

<span data-ttu-id="3a478-284">Un approccio alla convalida dei cookie si basa sul tenere traccia del momento in cui il database utente viene modificato.</span><span class="sxs-lookup"><span data-stu-id="3a478-284">One approach to cookie validation is based on keeping track of when the user database changes.</span></span> <span data-ttu-id="3a478-285">Se il database non è stato modificato dopo l'emissione del cookie dell'utente, non è necessario autenticare di nuovo l'utente se il cookie è ancora valido.</span><span class="sxs-lookup"><span data-stu-id="3a478-285">If the database hasn't been changed since the user's cookie was issued, there's no need to re-authenticate the user if their cookie is still valid.</span></span> <span data-ttu-id="3a478-286">Nell'app di esempio, il database viene implementato in `IUserRepository` e archivia un `LastChanged` valore.</span><span class="sxs-lookup"><span data-stu-id="3a478-286">In the sample app, the database is implemented in `IUserRepository` and stores a `LastChanged` value.</span></span> <span data-ttu-id="3a478-287">Quando un utente viene aggiornato nel database, il `LastChanged` valore viene impostato sull'ora corrente.</span><span class="sxs-lookup"><span data-stu-id="3a478-287">When a user is updated in the database, the `LastChanged` value is set to the current time.</span></span>

<span data-ttu-id="3a478-288">Per invalidare un cookie quando il database viene modificato in base al `LastChanged` valore, creare il cookie con un' `LastChanged` attestazione contenente il `LastChanged` valore corrente dal database:</span><span class="sxs-lookup"><span data-stu-id="3a478-288">In order to invalidate a cookie when the database changes based on the `LastChanged` value, create the cookie with a `LastChanged` claim containing the current `LastChanged` value from the database:</span></span>

```csharp
var claims = new List<Claim>
{
    new Claim(ClaimTypes.Name, user.Email),
    new Claim("LastChanged", {Database Value})
};

var claimsIdentity = new ClaimsIdentity(
    claims, 
    CookieAuthenticationDefaults.AuthenticationScheme);

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme, 
    new ClaimsPrincipal(claimsIdentity));
```

<span data-ttu-id="3a478-289">Per implementare una sostituzione per l' `ValidatePrincipal` evento, scrivere un metodo con la firma seguente in una classe che deriva da <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents> :</span><span class="sxs-lookup"><span data-stu-id="3a478-289">To implement an override for the `ValidatePrincipal` event, write a method with the following signature in a class that derives from <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>:</span></span>

```csharp
ValidatePrincipal(CookieValidatePrincipalContext)
```

<span data-ttu-id="3a478-290">Di seguito è riportato un esempio di implementazione di `CookieAuthenticationEvents` :</span><span class="sxs-lookup"><span data-stu-id="3a478-290">The following is an example implementation of `CookieAuthenticationEvents`:</span></span>

```csharp
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Authentication;
using Microsoft.AspNetCore.Authentication.Cookies;

public class CustomCookieAuthenticationEvents : CookieAuthenticationEvents
{
    private readonly IUserRepository _userRepository;

    public CustomCookieAuthenticationEvents(IUserRepository userRepository)
    {
        // Get the database from registered DI services.
        _userRepository = userRepository;
    }

    public override async Task ValidatePrincipal(CookieValidatePrincipalContext context)
    {
        var userPrincipal = context.Principal;

        // Look for the LastChanged claim.
        var lastChanged = (from c in userPrincipal.Claims
                           where c.Type == "LastChanged"
                           select c.Value).FirstOrDefault();

        if (string.IsNullOrEmpty(lastChanged) ||
            !_userRepository.ValidateLastChanged(lastChanged))
        {
            context.RejectPrincipal();

            await context.HttpContext.SignOutAsync(
                CookieAuthenticationDefaults.AuthenticationScheme);
        }
    }
}
```

<span data-ttu-id="3a478-291">Registrare l'istanza degli eventi durante la registrazione del servizio cookie nel `Startup.ConfigureServices` metodo.</span><span class="sxs-lookup"><span data-stu-id="3a478-291">Register the events instance during cookie service registration in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="3a478-292">Fornire una [registrazione del servizio con ambito](xref:fundamentals/dependency-injection#service-lifetimes) per la `CustomCookieAuthenticationEvents` classe:</span><span class="sxs-lookup"><span data-stu-id="3a478-292">Provide a [scoped service registration](xref:fundamentals/dependency-injection#service-lifetimes) for your `CustomCookieAuthenticationEvents` class:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.EventsType = typeof(CustomCookieAuthenticationEvents);
    });

services.AddScoped<CustomCookieAuthenticationEvents>();
```

<span data-ttu-id="3a478-293">Si consideri una situazione in cui il nome dell'utente viene aggiornato &mdash; una decisione che non influisce sulla sicurezza in alcun modo.</span><span class="sxs-lookup"><span data-stu-id="3a478-293">Consider a situation in which the user's name is updated&mdash;a decision that doesn't affect security in any way.</span></span> <span data-ttu-id="3a478-294">Se si desidera aggiornare l'entità utente in modo non distruttivo, chiamare `context.ReplacePrincipal` e impostare la `context.ShouldRenew` proprietà su `true` .</span><span class="sxs-lookup"><span data-stu-id="3a478-294">If you want to non-destructively update the user principal, call `context.ReplacePrincipal` and set the `context.ShouldRenew` property to `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="3a478-295">L'approccio descritto qui viene attivato a ogni richiesta.</span><span class="sxs-lookup"><span data-stu-id="3a478-295">The approach described here is triggered on every request.</span></span> <span data-ttu-id="3a478-296">La convalida dei cookie di autenticazione per tutti gli utenti in ogni richiesta può comportare un notevole calo delle prestazioni per l'app.</span><span class="sxs-lookup"><span data-stu-id="3a478-296">Validating authentication cookies for all users on every request can result in a large performance penalty for the app.</span></span>

## <a name="persistent-cookies"></a><span data-ttu-id="3a478-297">Cookie permanenti</span><span class="sxs-lookup"><span data-stu-id="3a478-297">Persistent cookies</span></span>

<span data-ttu-id="3a478-298">È possibile che il cookie venga mantenuto tra le sessioni del browser.</span><span class="sxs-lookup"><span data-stu-id="3a478-298">You may want the cookie to persist across browser sessions.</span></span> <span data-ttu-id="3a478-299">Questa persistenza deve essere abilitata solo con il consenso esplicito dell'utente con una casella di controllo "Remember me" all'accesso o un meccanismo simile.</span><span class="sxs-lookup"><span data-stu-id="3a478-299">This persistence should only be enabled with explicit user consent with a "Remember Me" check box on sign in or a similar mechanism.</span></span> 

<span data-ttu-id="3a478-300">Il frammento di codice seguente crea un'identità e un cookie corrispondente che sopravvivono attraverso le chiusure del browser.</span><span class="sxs-lookup"><span data-stu-id="3a478-300">The following code snippet creates an identity and corresponding cookie that survives through browser closures.</span></span> <span data-ttu-id="3a478-301">Vengono rispettate tutte le impostazioni di scadenza scorrevoli configurate in precedenza.</span><span class="sxs-lookup"><span data-stu-id="3a478-301">Any sliding expiration settings previously configured are honored.</span></span> <span data-ttu-id="3a478-302">Se il cookie scade quando il browser viene chiuso, il cookie viene cancellato dopo il riavvio.</span><span class="sxs-lookup"><span data-stu-id="3a478-302">If the cookie expires while the browser is closed, the browser clears the cookie once it's restarted.</span></span>

<span data-ttu-id="3a478-303">Imposta <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> su `true` in <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties> :</span><span class="sxs-lookup"><span data-stu-id="3a478-303">Set <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> to `true` in <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>:</span></span>

```csharp
// using Microsoft.AspNetCore.Authentication;

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claimsIdentity),
    new AuthenticationProperties
    {
        IsPersistent = true
    });
```

## <a name="absolute-cookie-expiration"></a><span data-ttu-id="3a478-304">Scadenza assoluta del cookie</span><span class="sxs-lookup"><span data-stu-id="3a478-304">Absolute cookie expiration</span></span>

<span data-ttu-id="3a478-305">È possibile impostare un'ora di scadenza assoluta con <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc> .</span><span class="sxs-lookup"><span data-stu-id="3a478-305">An absolute expiration time can be set with <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>.</span></span> <span data-ttu-id="3a478-306">Per creare un cookie persistente, `IsPersistent` è necessario impostare anche.</span><span class="sxs-lookup"><span data-stu-id="3a478-306">To create a persistent cookie, `IsPersistent` must also be set.</span></span> <span data-ttu-id="3a478-307">In caso contrario, il cookie viene creato con una durata basata sulla sessione e potrebbe scadere prima o dopo il ticket di autenticazione che possiede.</span><span class="sxs-lookup"><span data-stu-id="3a478-307">Otherwise, the cookie is created with a session-based lifetime and could expire either before or after the authentication ticket that it holds.</span></span> <span data-ttu-id="3a478-308">Quando `ExpiresUtc` è impostato, esegue l'override del valore dell' <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.ExpireTimeSpan> opzione di <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions> , se impostata.</span><span class="sxs-lookup"><span data-stu-id="3a478-308">When `ExpiresUtc` is set, it overrides the value of the <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.ExpireTimeSpan> option of <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions>, if set.</span></span>

<span data-ttu-id="3a478-309">Il frammento di codice seguente crea un'identità e un cookie corrispondente che dura 20 minuti.</span><span class="sxs-lookup"><span data-stu-id="3a478-309">The following code snippet creates an identity and corresponding cookie that lasts for 20 minutes.</span></span> <span data-ttu-id="3a478-310">Questa operazione ignora le impostazioni di scadenza scorrevoli configurate in precedenza.</span><span class="sxs-lookup"><span data-stu-id="3a478-310">This ignores any sliding expiration settings previously configured.</span></span>

```csharp
// using Microsoft.AspNetCore.Authentication;

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claimsIdentity),
    new AuthenticationProperties
    {
        IsPersistent = true,
        ExpiresUtc = DateTime.UtcNow.AddMinutes(20)
    });
```

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="3a478-311">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="3a478-311">Additional resources</span></span>

* <xref:security/authorization/limitingidentitybyscheme>
* <xref:security/authorization/claims>
* [<span data-ttu-id="3a478-312">Controlli dei ruoli basati su criteri</span><span class="sxs-lookup"><span data-stu-id="3a478-312">Policy-based role checks</span></span>](xref:security/authorization/roles#policy-based-role-checks)
* <xref:host-and-deploy/web-farm>
