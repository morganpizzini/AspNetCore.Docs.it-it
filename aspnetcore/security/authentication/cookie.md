---
title: USA cookie autenticazione senza ASP.NET CoreIdentity
author: rick-anderson
description: Informazioni su come usare cookie l'autenticazione senza ASP.NET Core Identity .
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 02/11/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/cookie
ms.openlocfilehash: 325bc3dcd48517d93d5f6f2d56e92651c780d759
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/08/2020
ms.locfileid: "88021887"
---
# <a name="use-no-loccookie-authentication-without-aspnet-core-no-locidentity"></a><span data-ttu-id="18687-103">USA cookie autenticazione senza ASP.NET CoreIdentity</span><span class="sxs-lookup"><span data-stu-id="18687-103">Use cookie authentication without ASP.NET Core Identity</span></span>

<span data-ttu-id="18687-104">Autore: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="18687-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="18687-105">ASP.NET Core Identity è un provider di autenticazione completo con funzionalità complete per la creazione e la gestione degli account di accesso.</span><span class="sxs-lookup"><span data-stu-id="18687-105">ASP.NET Core Identity is a complete, full-featured authentication provider for creating and maintaining logins.</span></span> <span data-ttu-id="18687-106">Tuttavia, cookie è possibile usare un provider di autenticazione basato su senza ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="18687-106">However, a cookie-based authentication provider without ASP.NET Core Identity can be used.</span></span> <span data-ttu-id="18687-107">Per altre informazioni, vedere <xref:security/authentication/identity>.</span><span class="sxs-lookup"><span data-stu-id="18687-107">For more information, see <xref:security/authentication/identity>.</span></span>

<span data-ttu-id="18687-108">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="18687-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="18687-109">A scopo dimostrativo nell'app di esempio, l'account utente per l'utente ipotetico, Maria Rodriguez, è hardcoded nell'app.</span><span class="sxs-lookup"><span data-stu-id="18687-109">For demonstration purposes in the sample app, the user account for the hypothetical user, Maria Rodriguez, is hardcoded into the app.</span></span> <span data-ttu-id="18687-110">Usare l'indirizzo di **posta elettronica** `maria.rodriguez@contoso.com` e qualsiasi password per accedere all'utente.</span><span class="sxs-lookup"><span data-stu-id="18687-110">Use the **Email** address `maria.rodriguez@contoso.com` and any password to sign in the user.</span></span> <span data-ttu-id="18687-111">L'utente viene autenticato nel `AuthenticateUser` metodo nel file *pages/account/login. cshtml. cs* .</span><span class="sxs-lookup"><span data-stu-id="18687-111">The user is authenticated in the `AuthenticateUser` method in the *Pages/Account/Login.cshtml.cs* file.</span></span> <span data-ttu-id="18687-112">In un esempio reale, l'utente viene autenticato a fronte di un database.</span><span class="sxs-lookup"><span data-stu-id="18687-112">In a real-world example, the user would be authenticated against a database.</span></span>

## <a name="configuration"></a><span data-ttu-id="18687-113">Configurazione</span><span class="sxs-lookup"><span data-stu-id="18687-113">Configuration</span></span>

<span data-ttu-id="18687-114">Se l'app non usa il [metapacchetto Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app), creare un riferimento al pacchetto nel file di progetto per [Microsoft. AspNetCore. Authentication. Cookie pacchetto s](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) .</span><span class="sxs-lookup"><span data-stu-id="18687-114">If the app doesn't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference in the project file for the [Microsoft.AspNetCore.Authentication.Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) package.</span></span>

<span data-ttu-id="18687-115">Nel `Startup.ConfigureServices` metodo creare i servizi del middleware di autenticazione con i <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> metodi e:</span><span class="sxs-lookup"><span data-stu-id="18687-115">In the `Startup.ConfigureServices` method, create the Authentication Middleware services with the <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> and <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> methods:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Startup.cs?name=snippet1)]

<span data-ttu-id="18687-116"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme>passato a `AddAuthentication` imposta lo schema di autenticazione predefinito per l'app.</span><span class="sxs-lookup"><span data-stu-id="18687-116"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> passed to `AddAuthentication` sets the default authentication scheme for the app.</span></span> <span data-ttu-id="18687-117">`AuthenticationScheme`è utile quando sono presenti più istanze di cookie autenticazione e si desidera [autorizzare con uno schema specifico](xref:security/authorization/limitingidentitybyscheme).</span><span class="sxs-lookup"><span data-stu-id="18687-117">`AuthenticationScheme` is useful when there are multiple instances of cookie authentication and you want to [authorize with a specific scheme](xref:security/authorization/limitingidentitybyscheme).</span></span> <span data-ttu-id="18687-118">L'impostazione `AuthenticationScheme` di su [ Cookie AuthenticationDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) fornisce il valore " Cookie s" per lo schema.</span><span class="sxs-lookup"><span data-stu-id="18687-118">Setting the `AuthenticationScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) provides a value of "Cookies" for the scheme.</span></span> <span data-ttu-id="18687-119">È possibile specificare qualsiasi valore stringa che distingua lo schema.</span><span class="sxs-lookup"><span data-stu-id="18687-119">You can supply any string value that distinguishes the scheme.</span></span>

<span data-ttu-id="18687-120">Lo schema di autenticazione dell'app è diverso dallo schema di cookie autenticazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="18687-120">The app's authentication scheme is different from the app's cookie authentication scheme.</span></span> <span data-ttu-id="18687-121">Quando uno cookie schema di autenticazione non viene fornito a <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> , USA `CookieAuthenticationDefaults.AuthenticationScheme` (" Cookie s").</span><span class="sxs-lookup"><span data-stu-id="18687-121">When a cookie authentication scheme isn't provided to <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, it uses `CookieAuthenticationDefaults.AuthenticationScheme` ("Cookies").</span></span>

<span data-ttu-id="18687-122">La cookie proprietà Authentication <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> è impostata su per `true` impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="18687-122">The authentication cookie's <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> property is set to `true` by default.</span></span> <span data-ttu-id="18687-123">cookieSono consentite le autenticazioni quando un visitatore del sito non ha acconsentito alla raccolta dei dati.</span><span class="sxs-lookup"><span data-stu-id="18687-123">Authentication cookies are allowed when a site visitor hasn't consented to data collection.</span></span> <span data-ttu-id="18687-124">Per altre informazioni, vedere <xref:security/gdpr#essential-cookies>.</span><span class="sxs-lookup"><span data-stu-id="18687-124">For more information, see <xref:security/gdpr#essential-cookies>.</span></span>

<span data-ttu-id="18687-125">In `Startup.Configure` , chiamare `UseAuthentication` e `UseAuthorization` per impostare la `HttpContext.User` proprietà ed eseguire il middleware di autorizzazione per le richieste.</span><span class="sxs-lookup"><span data-stu-id="18687-125">In `Startup.Configure`, call `UseAuthentication` and `UseAuthorization` to set the `HttpContext.User` property and run Authorization Middleware for requests.</span></span> <span data-ttu-id="18687-126">Chiamare i `UseAuthentication` `UseAuthorization` metodi e prima di chiamare `UseEndpoints` :</span><span class="sxs-lookup"><span data-stu-id="18687-126">Call the `UseAuthentication` and `UseAuthorization` methods before calling `UseEndpoints`:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Startup.cs?name=snippet2)]

<span data-ttu-id="18687-127">La <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> classe viene utilizzata per configurare le opzioni del provider di autenticazione.</span><span class="sxs-lookup"><span data-stu-id="18687-127">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> class is used to configure the authentication provider options.</span></span>

<span data-ttu-id="18687-128">Impostare `CookieAuthenticationOptions` nella configurazione del servizio per l'autenticazione nel `Startup.ConfigureServices` Metodo:</span><span class="sxs-lookup"><span data-stu-id="18687-128">Set `CookieAuthenticationOptions` in the service configuration for authentication in the `Startup.ConfigureServices` method:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        ...
    });
```

## <a name="no-loccookie-policy-middleware"></a><span data-ttu-id="18687-129">CookieMiddleware dei criteri</span><span class="sxs-lookup"><span data-stu-id="18687-129">Cookie Policy Middleware</span></span>

<span data-ttu-id="18687-130">Il [ Cookie middleware dei criteri](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) Abilita le cookie funzionalità dei criteri.</span><span class="sxs-lookup"><span data-stu-id="18687-130">[Cookie Policy Middleware](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) enables cookie policy capabilities.</span></span> <span data-ttu-id="18687-131">L'aggiunta del middleware alla pipeline di elaborazione delle app è sensibile agli ordini &mdash; , ma interessa solo i componenti downstream registrati nella pipeline.</span><span class="sxs-lookup"><span data-stu-id="18687-131">Adding the middleware to the app processing pipeline is order sensitive&mdash;it only affects downstream components registered in the pipeline.</span></span>

```csharp
app.UseCookiePolicy(cookiePolicyOptions);
```

<span data-ttu-id="18687-132">Utilizzare <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> fornito al Cookie middleware dei criteri per controllare le caratteristiche globali dell' cookie elaborazione e agganciare i cookie gestori di elaborazione quando cookie vengono aggiunti o eliminati.</span><span class="sxs-lookup"><span data-stu-id="18687-132">Use <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> provided to the Cookie Policy Middleware to control global characteristics of cookie processing and hook into cookie processing handlers when cookies are appended or deleted.</span></span>

<span data-ttu-id="18687-133">Il <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> valore predefinito è `SameSiteMode.Lax` consentire l'autenticazione OAuth2.</span><span class="sxs-lookup"><span data-stu-id="18687-133">The default <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> value is `SameSiteMode.Lax` to permit OAuth2 authentication.</span></span> <span data-ttu-id="18687-134">Per applicare rigorosamente un criterio dello stesso sito di `SameSiteMode.Strict` , impostare il `MinimumSameSitePolicy` .</span><span class="sxs-lookup"><span data-stu-id="18687-134">To strictly enforce a same-site policy of `SameSiteMode.Strict`, set the `MinimumSameSitePolicy`.</span></span> <span data-ttu-id="18687-135">Sebbene questa impostazione interrompa OAuth2 e altri schemi di autenticazione tra le origini, eleva il livello di cookie sicurezza per altri tipi di app che non si basano sull'elaborazione di richieste tra origini.</span><span class="sxs-lookup"><span data-stu-id="18687-135">Although this setting breaks OAuth2 and other cross-origin authentication schemes, it elevates the level of cookie security for other types of apps that don't rely on cross-origin request processing.</span></span>

```csharp
var cookiePolicyOptions = new CookiePolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

<span data-ttu-id="18687-136">L' Cookie impostazione del middleware per i criteri di `MinimumSameSitePolicy` può influire sull'impostazione di `Cookie.SameSite` in `CookieAuthenticationOptions` impostazioni in base alla matrice riportata di seguito.</span><span class="sxs-lookup"><span data-stu-id="18687-136">The Cookie Policy Middleware setting for `MinimumSameSitePolicy` can affect the setting of `Cookie.SameSite` in `CookieAuthenticationOptions` settings according to the matrix below.</span></span>

| <span data-ttu-id="18687-137">MinimumSameSitePolicy</span><span class="sxs-lookup"><span data-stu-id="18687-137">MinimumSameSitePolicy</span></span> | <span data-ttu-id="18687-138">Cookie. Navigava sullostesso sito</span><span class="sxs-lookup"><span data-stu-id="18687-138">Cookie.SameSite</span></span> | <span data-ttu-id="18687-139">Risultato Cookie . Impostazione navigava sullostesso sito</span><span class="sxs-lookup"><span data-stu-id="18687-139">Resultant Cookie.SameSite setting</span></span> |
| --------------------- | --------------- | --------------------------------- |
| <span data-ttu-id="18687-140">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="18687-140">SameSiteMode.None</span></span>     | <span data-ttu-id="18687-141">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="18687-141">SameSiteMode.None</span></span><br><span data-ttu-id="18687-142">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="18687-142">SameSiteMode.Lax</span></span><br><span data-ttu-id="18687-143">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="18687-143">SameSiteMode.Strict</span></span> | <span data-ttu-id="18687-144">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="18687-144">SameSiteMode.None</span></span><br><span data-ttu-id="18687-145">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="18687-145">SameSiteMode.Lax</span></span><br><span data-ttu-id="18687-146">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="18687-146">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="18687-147">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="18687-147">SameSiteMode.Lax</span></span>      | <span data-ttu-id="18687-148">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="18687-148">SameSiteMode.None</span></span><br><span data-ttu-id="18687-149">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="18687-149">SameSiteMode.Lax</span></span><br><span data-ttu-id="18687-150">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="18687-150">SameSiteMode.Strict</span></span> | <span data-ttu-id="18687-151">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="18687-151">SameSiteMode.Lax</span></span><br><span data-ttu-id="18687-152">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="18687-152">SameSiteMode.Lax</span></span><br><span data-ttu-id="18687-153">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="18687-153">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="18687-154">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="18687-154">SameSiteMode.Strict</span></span>   | <span data-ttu-id="18687-155">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="18687-155">SameSiteMode.None</span></span><br><span data-ttu-id="18687-156">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="18687-156">SameSiteMode.Lax</span></span><br><span data-ttu-id="18687-157">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="18687-157">SameSiteMode.Strict</span></span> | <span data-ttu-id="18687-158">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="18687-158">SameSiteMode.Strict</span></span><br><span data-ttu-id="18687-159">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="18687-159">SameSiteMode.Strict</span></span><br><span data-ttu-id="18687-160">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="18687-160">SameSiteMode.Strict</span></span> |

## <a name="create-an-authentication-no-loccookie"></a><span data-ttu-id="18687-161">Creare un'autenticazionecookie</span><span class="sxs-lookup"><span data-stu-id="18687-161">Create an authentication cookie</span></span>

<span data-ttu-id="18687-162">Per creare un oggetto che cookie contiene informazioni sull'utente, costruire un oggetto <xref:System.Security.Claims.ClaimsPrincipal> .</span><span class="sxs-lookup"><span data-stu-id="18687-162">To create a cookie holding user information, construct a <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="18687-163">Le informazioni utente vengono serializzate e archiviate in cookie .</span><span class="sxs-lookup"><span data-stu-id="18687-163">The user information is serialized and stored in the cookie.</span></span> 

<span data-ttu-id="18687-164">Creare una <xref:System.Security.Claims.ClaimsIdentity> con qualsiasi richiesta <xref:System.Security.Claims.Claim> e chiamare <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> per accedere all'utente:</span><span class="sxs-lookup"><span data-stu-id="18687-164">Create a <xref:System.Security.Claims.ClaimsIdentity> with any required <xref:System.Security.Claims.Claim>s and call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> to sign in the user:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet1)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]

<span data-ttu-id="18687-165">`SignInAsync`Crea un oggetto crittografato cookie e lo aggiunge alla risposta corrente.</span><span class="sxs-lookup"><span data-stu-id="18687-165">`SignInAsync` creates an encrypted cookie and adds it to the current response.</span></span> <span data-ttu-id="18687-166">Se `AuthenticationScheme` non è specificato, viene usato lo schema predefinito.</span><span class="sxs-lookup"><span data-stu-id="18687-166">If `AuthenticationScheme` isn't specified, the default scheme is used.</span></span>

<span data-ttu-id="18687-167"><xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.RedirectUri>viene utilizzato solo in alcuni percorsi specifici per impostazione predefinita, ad esempio il percorso di accesso e i percorsi di disconnessione.</span><span class="sxs-lookup"><span data-stu-id="18687-167"><xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.RedirectUri> is only used on a few specific paths by default, for example, the login path and logout paths.</span></span> <span data-ttu-id="18687-168">Per altre informazioni, vedere l' [ Cookie origine AuthenticationHandler](https://github.com/dotnet/aspnetcore/blob/f2e6e6ff334176540ef0b3291122e359c2106d1a/src/Security/Authentication/Cookies/src/CookieAuthenticationHandler.cs#L334).</span><span class="sxs-lookup"><span data-stu-id="18687-168">For more information see the [CookieAuthenticationHandler source](https://github.com/dotnet/aspnetcore/blob/f2e6e6ff334176540ef0b3291122e359c2106d1a/src/Security/Authentication/Cookies/src/CookieAuthenticationHandler.cs#L334).</span></span>

<span data-ttu-id="18687-169">Per la crittografia viene usato il sistema di [protezione dei dati](xref:security/data-protection/using-data-protection) di ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="18687-169">ASP.NET Core's [Data Protection](xref:security/data-protection/using-data-protection) system is used for encryption.</span></span> <span data-ttu-id="18687-170">Per un'app ospitata in più computer, bilanciamento del carico tra app o uso di un Web farm, [configurare la protezione dei dati](xref:security/data-protection/configuration/overview) in modo da usare lo stesso anello chiave e l'identificatore dell'app.</span><span class="sxs-lookup"><span data-stu-id="18687-170">For an app hosted on multiple machines, load balancing across apps, or using a web farm, [configure data protection](xref:security/data-protection/configuration/overview) to use the same key ring and app identifier.</span></span>

## <a name="sign-out"></a><span data-ttu-id="18687-171">Disconnetti</span><span class="sxs-lookup"><span data-stu-id="18687-171">Sign out</span></span>

<span data-ttu-id="18687-172">Per disconnettersi dall'utente corrente ed eliminare il relativo cookie , chiamare <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*> :</span><span class="sxs-lookup"><span data-stu-id="18687-172">To sign out the current user and delete their cookie, call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet2)]

<span data-ttu-id="18687-173">Se `CookieAuthenticationDefaults.AuthenticationScheme` (o " Cookie s") non viene usato come schema (ad esempio, "Contoso Cookie "), fornire lo schema usato durante la configurazione del provider di autenticazione.</span><span class="sxs-lookup"><span data-stu-id="18687-173">If `CookieAuthenticationDefaults.AuthenticationScheme` (or "Cookies") isn't used as the scheme (for example, "ContosoCookie"), supply the scheme used when configuring the authentication provider.</span></span> <span data-ttu-id="18687-174">In caso contrario, verrà utilizzato lo schema predefinito.</span><span class="sxs-lookup"><span data-stu-id="18687-174">Otherwise, the default scheme is used.</span></span>

## <a name="react-to-back-end-changes"></a><span data-ttu-id="18687-175">Reagire alle modifiche del back-end</span><span class="sxs-lookup"><span data-stu-id="18687-175">React to back-end changes</span></span>

<span data-ttu-id="18687-176">Una volta cookie creato un oggetto, cookie è l'unica origine di identità.</span><span class="sxs-lookup"><span data-stu-id="18687-176">Once a cookie is created, the cookie is the single source of identity.</span></span> <span data-ttu-id="18687-177">Se un account utente è disabilitato nei sistemi back-end:</span><span class="sxs-lookup"><span data-stu-id="18687-177">If a user account is disabled in back-end systems:</span></span>

* <span data-ttu-id="18687-178">Il sistema di cookie autenticazione dell'app continua a elaborare le richieste in base all'autenticazione cookie .</span><span class="sxs-lookup"><span data-stu-id="18687-178">The app's cookie authentication system continues to process requests based on the authentication cookie.</span></span>
* <span data-ttu-id="18687-179">L'utente rimane connesso all'app fino a quando l'autenticazione cookie è valida.</span><span class="sxs-lookup"><span data-stu-id="18687-179">The user remains signed into the app as long as the authentication cookie is valid.</span></span>

<span data-ttu-id="18687-180">L' <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> evento può essere usato per intercettare ed eseguire l'override della convalida dell' cookie identità.</span><span class="sxs-lookup"><span data-stu-id="18687-180">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> event can be used to intercept and override validation of the cookie identity.</span></span> <span data-ttu-id="18687-181">La convalida cookie di a ogni richiesta attenua il rischio di revoca degli utenti che accedono all'app.</span><span class="sxs-lookup"><span data-stu-id="18687-181">Validating the cookie on every request mitigates the risk of revoked users accessing the app.</span></span>

<span data-ttu-id="18687-182">Un approccio alla cookie convalida si basa sul tenere traccia del momento in cui il database utente viene modificato.</span><span class="sxs-lookup"><span data-stu-id="18687-182">One approach to cookie validation is based on keeping track of when the user database changes.</span></span> <span data-ttu-id="18687-183">Se il database non è stato modificato dopo l'emissione dell'utente cookie , non è necessario autenticare di nuovo l'utente se cookie è ancora valido.</span><span class="sxs-lookup"><span data-stu-id="18687-183">If the database hasn't been changed since the user's cookie was issued, there's no need to re-authenticate the user if their cookie is still valid.</span></span> <span data-ttu-id="18687-184">Nell'app di esempio, il database viene implementato in `IUserRepository` e archivia un `LastChanged` valore.</span><span class="sxs-lookup"><span data-stu-id="18687-184">In the sample app, the database is implemented in `IUserRepository` and stores a `LastChanged` value.</span></span> <span data-ttu-id="18687-185">Quando un utente viene aggiornato nel database, il `LastChanged` valore viene impostato sull'ora corrente.</span><span class="sxs-lookup"><span data-stu-id="18687-185">When a user is updated in the database, the `LastChanged` value is set to the current time.</span></span>

<span data-ttu-id="18687-186">Per invalidare un oggetto cookie quando il database viene modificato in base al `LastChanged` valore, creare cookie con un' `LastChanged` attestazione contenente il `LastChanged` valore corrente dal database:</span><span class="sxs-lookup"><span data-stu-id="18687-186">In order to invalidate a cookie when the database changes based on the `LastChanged` value, create the cookie with a `LastChanged` claim containing the current `LastChanged` value from the database:</span></span>

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

<span data-ttu-id="18687-187">Per implementare una sostituzione per l' `ValidatePrincipal` evento, scrivere un metodo con la firma seguente in una classe che deriva da <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents> :</span><span class="sxs-lookup"><span data-stu-id="18687-187">To implement an override for the `ValidatePrincipal` event, write a method with the following signature in a class that derives from <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>:</span></span>

```csharp
ValidatePrincipal(CookieValidatePrincipalContext)
```

<span data-ttu-id="18687-188">Di seguito è riportato un esempio di implementazione di `CookieAuthenticationEvents` :</span><span class="sxs-lookup"><span data-stu-id="18687-188">The following is an example implementation of `CookieAuthenticationEvents`:</span></span>

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

<span data-ttu-id="18687-189">Registrare l'istanza degli eventi durante cookie la registrazione del servizio nel `Startup.ConfigureServices` metodo.</span><span class="sxs-lookup"><span data-stu-id="18687-189">Register the events instance during cookie service registration in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="18687-190">Fornire una [registrazione del servizio con ambito](xref:fundamentals/dependency-injection#service-lifetimes) per la `CustomCookieAuthenticationEvents` classe:</span><span class="sxs-lookup"><span data-stu-id="18687-190">Provide a [scoped service registration](xref:fundamentals/dependency-injection#service-lifetimes) for your `CustomCookieAuthenticationEvents` class:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.EventsType = typeof(CustomCookieAuthenticationEvents);
    });

services.AddScoped<CustomCookieAuthenticationEvents>();
```

<span data-ttu-id="18687-191">Si consideri una situazione in cui il nome dell'utente viene aggiornato &mdash; una decisione che non influisce sulla sicurezza in alcun modo.</span><span class="sxs-lookup"><span data-stu-id="18687-191">Consider a situation in which the user's name is updated&mdash;a decision that doesn't affect security in any way.</span></span> <span data-ttu-id="18687-192">Se si desidera aggiornare l'entità utente in modo non distruttivo, chiamare `context.ReplacePrincipal` e impostare la `context.ShouldRenew` proprietà su `true` .</span><span class="sxs-lookup"><span data-stu-id="18687-192">If you want to non-destructively update the user principal, call `context.ReplacePrincipal` and set the `context.ShouldRenew` property to `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="18687-193">L'approccio descritto qui viene attivato a ogni richiesta.</span><span class="sxs-lookup"><span data-stu-id="18687-193">The approach described here is triggered on every request.</span></span> <span data-ttu-id="18687-194">La convalida cookie dell'autenticazione per tutti gli utenti in ogni richiesta può comportare un notevole calo delle prestazioni per l'app.</span><span class="sxs-lookup"><span data-stu-id="18687-194">Validating authentication cookies for all users on every request can result in a large performance penalty for the app.</span></span>

## <a name="persistent-no-loccookies"></a><span data-ttu-id="18687-195">S persistente cookie</span><span class="sxs-lookup"><span data-stu-id="18687-195">Persistent cookies</span></span>

<span data-ttu-id="18687-196">È possibile che il venga cookie mantenuto tra le sessioni del browser.</span><span class="sxs-lookup"><span data-stu-id="18687-196">You may want the cookie to persist across browser sessions.</span></span> <span data-ttu-id="18687-197">Questa persistenza deve essere abilitata solo con il consenso esplicito dell'utente con una casella di controllo "Remember me" all'accesso o un meccanismo simile.</span><span class="sxs-lookup"><span data-stu-id="18687-197">This persistence should only be enabled with explicit user consent with a "Remember Me" check box on sign in or a similar mechanism.</span></span> 

<span data-ttu-id="18687-198">Il frammento di codice seguente crea un'identità e corrispondente che rimane inalterata cookie attraverso le chiusure del browser.</span><span class="sxs-lookup"><span data-stu-id="18687-198">The following code snippet creates an identity and corresponding cookie that survives through browser closures.</span></span> <span data-ttu-id="18687-199">Vengono rispettate tutte le impostazioni di scadenza scorrevoli configurate in precedenza.</span><span class="sxs-lookup"><span data-stu-id="18687-199">Any sliding expiration settings previously configured are honored.</span></span> <span data-ttu-id="18687-200">Se l'oggetto cookie scade quando il browser viene chiuso, il browser cancella il oggetto cookie dopo il riavvio.</span><span class="sxs-lookup"><span data-stu-id="18687-200">If the cookie expires while the browser is closed, the browser clears the cookie once it's restarted.</span></span>

<span data-ttu-id="18687-201">Imposta <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> su `true` in <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties> :</span><span class="sxs-lookup"><span data-stu-id="18687-201">Set <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> to `true` in <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>:</span></span>

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

## <a name="absolute-no-loccookie-expiration"></a><span data-ttu-id="18687-202">cookieScadenza assoluta</span><span class="sxs-lookup"><span data-stu-id="18687-202">Absolute cookie expiration</span></span>

<span data-ttu-id="18687-203">È possibile impostare un'ora di scadenza assoluta con <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc> .</span><span class="sxs-lookup"><span data-stu-id="18687-203">An absolute expiration time can be set with <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>.</span></span> <span data-ttu-id="18687-204">Per creare un oggetto persistente cookie , `IsPersistent` è necessario impostare anche.</span><span class="sxs-lookup"><span data-stu-id="18687-204">To create a persistent cookie, `IsPersistent` must also be set.</span></span> <span data-ttu-id="18687-205">In caso contrario, cookie viene creato con una durata basata sulla sessione e potrebbe scadere prima o dopo il ticket di autenticazione che possiede.</span><span class="sxs-lookup"><span data-stu-id="18687-205">Otherwise, the cookie is created with a session-based lifetime and could expire either before or after the authentication ticket that it holds.</span></span> <span data-ttu-id="18687-206">Quando `ExpiresUtc` è impostato, esegue l'override del valore dell' <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.ExpireTimeSpan> opzione di <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> , se impostata.</span><span class="sxs-lookup"><span data-stu-id="18687-206">When `ExpiresUtc` is set, it overrides the value of the <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.ExpireTimeSpan> option of <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions>, if set.</span></span>

<span data-ttu-id="18687-207">Il frammento di codice seguente crea un'identità e corrisponde alla cookie durata di 20 minuti.</span><span class="sxs-lookup"><span data-stu-id="18687-207">The following code snippet creates an identity and corresponding cookie that lasts for 20 minutes.</span></span> <span data-ttu-id="18687-208">Questa operazione ignora le impostazioni di scadenza scorrevoli configurate in precedenza.</span><span class="sxs-lookup"><span data-stu-id="18687-208">This ignores any sliding expiration settings previously configured.</span></span>

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

<span data-ttu-id="18687-209">ASP.NET Core Identity è un provider di autenticazione completo con funzionalità complete per la creazione e la gestione degli account di accesso.</span><span class="sxs-lookup"><span data-stu-id="18687-209">ASP.NET Core Identity is a complete, full-featured authentication provider for creating and maintaining logins.</span></span> <span data-ttu-id="18687-210">Tuttavia, cookie è possibile usare un provider di autenticazione di autenticazione basato su senza ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="18687-210">However, a cookie-based authentication authentication provider without ASP.NET Core Identity can be used.</span></span> <span data-ttu-id="18687-211">Per altre informazioni, vedere <xref:security/authentication/identity>.</span><span class="sxs-lookup"><span data-stu-id="18687-211">For more information, see <xref:security/authentication/identity>.</span></span>

<span data-ttu-id="18687-212">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="18687-212">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="18687-213">A scopo dimostrativo nell'app di esempio, l'account utente per l'utente ipotetico, Maria Rodriguez, è hardcoded nell'app.</span><span class="sxs-lookup"><span data-stu-id="18687-213">For demonstration purposes in the sample app, the user account for the hypothetical user, Maria Rodriguez, is hardcoded into the app.</span></span> <span data-ttu-id="18687-214">Usare l'indirizzo di **posta elettronica** `maria.rodriguez@contoso.com` e qualsiasi password per accedere all'utente.</span><span class="sxs-lookup"><span data-stu-id="18687-214">Use the **Email** address `maria.rodriguez@contoso.com` and any password to sign in the user.</span></span> <span data-ttu-id="18687-215">L'utente viene autenticato nel `AuthenticateUser` metodo nel file *pages/account/login. cshtml. cs* .</span><span class="sxs-lookup"><span data-stu-id="18687-215">The user is authenticated in the `AuthenticateUser` method in the *Pages/Account/Login.cshtml.cs* file.</span></span> <span data-ttu-id="18687-216">In un esempio reale, l'utente viene autenticato a fronte di un database.</span><span class="sxs-lookup"><span data-stu-id="18687-216">In a real-world example, the user would be authenticated against a database.</span></span>

## <a name="configuration"></a><span data-ttu-id="18687-217">Configurazione</span><span class="sxs-lookup"><span data-stu-id="18687-217">Configuration</span></span>

<span data-ttu-id="18687-218">Se l'app non usa il [metapacchetto Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app), creare un riferimento al pacchetto nel file di progetto per [Microsoft. AspNetCore. Authentication. Cookie pacchetto s](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) .</span><span class="sxs-lookup"><span data-stu-id="18687-218">If the app doesn't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference in the project file for the [Microsoft.AspNetCore.Authentication.Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) package.</span></span>

<span data-ttu-id="18687-219">Nel `Startup.ConfigureServices` metodo creare il servizio middleware di autenticazione con i <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> metodi e <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> :</span><span class="sxs-lookup"><span data-stu-id="18687-219">In the `Startup.ConfigureServices` method, create the Authentication Middleware service with the <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> and <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> methods:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet1)]

<span data-ttu-id="18687-220"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme>passato a `AddAuthentication` imposta lo schema di autenticazione predefinito per l'app.</span><span class="sxs-lookup"><span data-stu-id="18687-220"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> passed to `AddAuthentication` sets the default authentication scheme for the app.</span></span> <span data-ttu-id="18687-221">`AuthenticationScheme`è utile quando sono presenti più istanze di cookie autenticazione e si desidera [autorizzare con uno schema specifico](xref:security/authorization/limitingidentitybyscheme).</span><span class="sxs-lookup"><span data-stu-id="18687-221">`AuthenticationScheme` is useful when there are multiple instances of cookie authentication and you want to [authorize with a specific scheme](xref:security/authorization/limitingidentitybyscheme).</span></span> <span data-ttu-id="18687-222">L'impostazione `AuthenticationScheme` di su [ Cookie AuthenticationDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) fornisce il valore " Cookie s" per lo schema.</span><span class="sxs-lookup"><span data-stu-id="18687-222">Setting the `AuthenticationScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) provides a value of "Cookies" for the scheme.</span></span> <span data-ttu-id="18687-223">È possibile specificare qualsiasi valore stringa che distingua lo schema.</span><span class="sxs-lookup"><span data-stu-id="18687-223">You can supply any string value that distinguishes the scheme.</span></span>

<span data-ttu-id="18687-224">Lo schema di autenticazione dell'app è diverso dallo schema di cookie autenticazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="18687-224">The app's authentication scheme is different from the app's cookie authentication scheme.</span></span> <span data-ttu-id="18687-225">Quando uno cookie schema di autenticazione non viene fornito a <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> , USA `CookieAuthenticationDefaults.AuthenticationScheme` (" Cookie s").</span><span class="sxs-lookup"><span data-stu-id="18687-225">When a cookie authentication scheme isn't provided to <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, it uses `CookieAuthenticationDefaults.AuthenticationScheme` ("Cookies").</span></span>

<span data-ttu-id="18687-226">La cookie proprietà Authentication <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> è impostata su per `true` impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="18687-226">The authentication cookie's <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> property is set to `true` by default.</span></span> <span data-ttu-id="18687-227">cookieSono consentite le autenticazioni quando un visitatore del sito non ha acconsentito alla raccolta dei dati.</span><span class="sxs-lookup"><span data-stu-id="18687-227">Authentication cookies are allowed when a site visitor hasn't consented to data collection.</span></span> <span data-ttu-id="18687-228">Per altre informazioni, vedere <xref:security/gdpr#essential-cookies>.</span><span class="sxs-lookup"><span data-stu-id="18687-228">For more information, see <xref:security/gdpr#essential-cookies>.</span></span>

<span data-ttu-id="18687-229">Nel `Startup.Configure` metodo chiamare il `UseAuthentication` metodo per richiamare il middleware di autenticazione che imposta la `HttpContext.User` Proprietà.</span><span class="sxs-lookup"><span data-stu-id="18687-229">In the `Startup.Configure` method, call the `UseAuthentication` method to invoke the Authentication Middleware that sets the `HttpContext.User` property.</span></span> <span data-ttu-id="18687-230">Chiamare il `UseAuthentication` metodo prima di chiamare `UseMvcWithDefaultRoute` o `UseMvc` :</span><span class="sxs-lookup"><span data-stu-id="18687-230">Call the `UseAuthentication` method before calling `UseMvcWithDefaultRoute` or `UseMvc`:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet2)]

<span data-ttu-id="18687-231">La <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> classe viene utilizzata per configurare le opzioni del provider di autenticazione.</span><span class="sxs-lookup"><span data-stu-id="18687-231">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> class is used to configure the authentication provider options.</span></span>

<span data-ttu-id="18687-232">Impostare `CookieAuthenticationOptions` nella configurazione del servizio per l'autenticazione nel `Startup.ConfigureServices` Metodo:</span><span class="sxs-lookup"><span data-stu-id="18687-232">Set `CookieAuthenticationOptions` in the service configuration for authentication in the `Startup.ConfigureServices` method:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        ...
    });
```

## <a name="no-loccookie-policy-middleware"></a><span data-ttu-id="18687-233">CookieMiddleware dei criteri</span><span class="sxs-lookup"><span data-stu-id="18687-233">Cookie Policy Middleware</span></span>

<span data-ttu-id="18687-234">Il [ Cookie middleware dei criteri](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) Abilita le cookie funzionalità dei criteri.</span><span class="sxs-lookup"><span data-stu-id="18687-234">[Cookie Policy Middleware](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) enables cookie policy capabilities.</span></span> <span data-ttu-id="18687-235">L'aggiunta del middleware alla pipeline di elaborazione delle app è sensibile agli ordini &mdash; , ma interessa solo i componenti downstream registrati nella pipeline.</span><span class="sxs-lookup"><span data-stu-id="18687-235">Adding the middleware to the app processing pipeline is order sensitive&mdash;it only affects downstream components registered in the pipeline.</span></span>

```csharp
app.UseCookiePolicy(cookiePolicyOptions);
```

<span data-ttu-id="18687-236">Utilizzare <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> fornito al Cookie middleware dei criteri per controllare le caratteristiche globali dell' cookie elaborazione e agganciare i cookie gestori di elaborazione quando cookie vengono aggiunti o eliminati.</span><span class="sxs-lookup"><span data-stu-id="18687-236">Use <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> provided to the Cookie Policy Middleware to control global characteristics of cookie processing and hook into cookie processing handlers when cookies are appended or deleted.</span></span>

<span data-ttu-id="18687-237">Il <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> valore predefinito è `SameSiteMode.Lax` consentire l'autenticazione OAuth2.</span><span class="sxs-lookup"><span data-stu-id="18687-237">The default <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> value is `SameSiteMode.Lax` to permit OAuth2 authentication.</span></span> <span data-ttu-id="18687-238">Per applicare rigorosamente un criterio dello stesso sito di `SameSiteMode.Strict` , impostare il `MinimumSameSitePolicy` .</span><span class="sxs-lookup"><span data-stu-id="18687-238">To strictly enforce a same-site policy of `SameSiteMode.Strict`, set the `MinimumSameSitePolicy`.</span></span> <span data-ttu-id="18687-239">Sebbene questa impostazione interrompa OAuth2 e altri schemi di autenticazione tra le origini, eleva il livello di cookie sicurezza per altri tipi di app che non si basano sull'elaborazione di richieste tra origini.</span><span class="sxs-lookup"><span data-stu-id="18687-239">Although this setting breaks OAuth2 and other cross-origin authentication schemes, it elevates the level of cookie security for other types of apps that don't rely on cross-origin request processing.</span></span>

```csharp
var cookiePolicyOptions = new CookiePolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

<span data-ttu-id="18687-240">L' Cookie impostazione del middleware per i criteri di `MinimumSameSitePolicy` può influire sull'impostazione di `Cookie.SameSite` in `CookieAuthenticationOptions` impostazioni in base alla matrice riportata di seguito.</span><span class="sxs-lookup"><span data-stu-id="18687-240">The Cookie Policy Middleware setting for `MinimumSameSitePolicy` can affect the setting of `Cookie.SameSite` in `CookieAuthenticationOptions` settings according to the matrix below.</span></span>

| <span data-ttu-id="18687-241">MinimumSameSitePolicy</span><span class="sxs-lookup"><span data-stu-id="18687-241">MinimumSameSitePolicy</span></span> | <span data-ttu-id="18687-242">Cookie. Navigava sullostesso sito</span><span class="sxs-lookup"><span data-stu-id="18687-242">Cookie.SameSite</span></span> | <span data-ttu-id="18687-243">Risultato Cookie . Impostazione navigava sullostesso sito</span><span class="sxs-lookup"><span data-stu-id="18687-243">Resultant Cookie.SameSite setting</span></span> |
| --------------------- | --------------- | --------------------------------- |
| <span data-ttu-id="18687-244">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="18687-244">SameSiteMode.None</span></span>     | <span data-ttu-id="18687-245">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="18687-245">SameSiteMode.None</span></span><br><span data-ttu-id="18687-246">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="18687-246">SameSiteMode.Lax</span></span><br><span data-ttu-id="18687-247">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="18687-247">SameSiteMode.Strict</span></span> | <span data-ttu-id="18687-248">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="18687-248">SameSiteMode.None</span></span><br><span data-ttu-id="18687-249">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="18687-249">SameSiteMode.Lax</span></span><br><span data-ttu-id="18687-250">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="18687-250">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="18687-251">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="18687-251">SameSiteMode.Lax</span></span>      | <span data-ttu-id="18687-252">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="18687-252">SameSiteMode.None</span></span><br><span data-ttu-id="18687-253">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="18687-253">SameSiteMode.Lax</span></span><br><span data-ttu-id="18687-254">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="18687-254">SameSiteMode.Strict</span></span> | <span data-ttu-id="18687-255">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="18687-255">SameSiteMode.Lax</span></span><br><span data-ttu-id="18687-256">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="18687-256">SameSiteMode.Lax</span></span><br><span data-ttu-id="18687-257">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="18687-257">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="18687-258">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="18687-258">SameSiteMode.Strict</span></span>   | <span data-ttu-id="18687-259">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="18687-259">SameSiteMode.None</span></span><br><span data-ttu-id="18687-260">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="18687-260">SameSiteMode.Lax</span></span><br><span data-ttu-id="18687-261">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="18687-261">SameSiteMode.Strict</span></span> | <span data-ttu-id="18687-262">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="18687-262">SameSiteMode.Strict</span></span><br><span data-ttu-id="18687-263">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="18687-263">SameSiteMode.Strict</span></span><br><span data-ttu-id="18687-264">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="18687-264">SameSiteMode.Strict</span></span> |

## <a name="create-an-authentication-no-loccookie"></a><span data-ttu-id="18687-265">Creare un'autenticazionecookie</span><span class="sxs-lookup"><span data-stu-id="18687-265">Create an authentication cookie</span></span>

<span data-ttu-id="18687-266">Per creare un oggetto che cookie contiene informazioni sull'utente, costruire un oggetto <xref:System.Security.Claims.ClaimsPrincipal> .</span><span class="sxs-lookup"><span data-stu-id="18687-266">To create a cookie holding user information, construct a <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="18687-267">Le informazioni utente vengono serializzate e archiviate in cookie .</span><span class="sxs-lookup"><span data-stu-id="18687-267">The user information is serialized and stored in the cookie.</span></span> 

<span data-ttu-id="18687-268">Creare una <xref:System.Security.Claims.ClaimsIdentity> con qualsiasi richiesta <xref:System.Security.Claims.Claim> e chiamare <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> per accedere all'utente:</span><span class="sxs-lookup"><span data-stu-id="18687-268">Create a <xref:System.Security.Claims.ClaimsIdentity> with any required <xref:System.Security.Claims.Claim>s and call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> to sign in the user:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet1)]

<span data-ttu-id="18687-269">`SignInAsync`Crea un oggetto crittografato cookie e lo aggiunge alla risposta corrente.</span><span class="sxs-lookup"><span data-stu-id="18687-269">`SignInAsync` creates an encrypted cookie and adds it to the current response.</span></span> <span data-ttu-id="18687-270">Se `AuthenticationScheme` non è specificato, viene usato lo schema predefinito.</span><span class="sxs-lookup"><span data-stu-id="18687-270">If `AuthenticationScheme` isn't specified, the default scheme is used.</span></span>

<span data-ttu-id="18687-271">Per la crittografia viene usato il sistema di [protezione dei dati](xref:security/data-protection/using-data-protection) di ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="18687-271">ASP.NET Core's [Data Protection](xref:security/data-protection/using-data-protection) system is used for encryption.</span></span> <span data-ttu-id="18687-272">Per un'app ospitata in più computer, bilanciamento del carico tra app o uso di un Web farm, [configurare la protezione dei dati](xref:security/data-protection/configuration/overview) in modo da usare lo stesso anello chiave e l'identificatore dell'app.</span><span class="sxs-lookup"><span data-stu-id="18687-272">For an app hosted on multiple machines, load balancing across apps, or using a web farm, [configure data protection](xref:security/data-protection/configuration/overview) to use the same key ring and app identifier.</span></span>

## <a name="sign-out"></a><span data-ttu-id="18687-273">Disconnetti</span><span class="sxs-lookup"><span data-stu-id="18687-273">Sign out</span></span>

<span data-ttu-id="18687-274">Per disconnettersi dall'utente corrente ed eliminare il relativo cookie , chiamare <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*> :</span><span class="sxs-lookup"><span data-stu-id="18687-274">To sign out the current user and delete their cookie, call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet2)]

<span data-ttu-id="18687-275">Se `CookieAuthenticationDefaults.AuthenticationScheme` (o " Cookie s") non viene usato come schema (ad esempio, "Contoso Cookie "), fornire lo schema usato durante la configurazione del provider di autenticazione.</span><span class="sxs-lookup"><span data-stu-id="18687-275">If `CookieAuthenticationDefaults.AuthenticationScheme` (or "Cookies") isn't used as the scheme (for example, "ContosoCookie"), supply the scheme used when configuring the authentication provider.</span></span> <span data-ttu-id="18687-276">In caso contrario, verrà utilizzato lo schema predefinito.</span><span class="sxs-lookup"><span data-stu-id="18687-276">Otherwise, the default scheme is used.</span></span>

## <a name="react-to-back-end-changes"></a><span data-ttu-id="18687-277">Reagire alle modifiche del back-end</span><span class="sxs-lookup"><span data-stu-id="18687-277">React to back-end changes</span></span>

<span data-ttu-id="18687-278">Una volta cookie creato un oggetto, cookie è l'unica origine di identità.</span><span class="sxs-lookup"><span data-stu-id="18687-278">Once a cookie is created, the cookie is the single source of identity.</span></span> <span data-ttu-id="18687-279">Se un account utente è disabilitato nei sistemi back-end:</span><span class="sxs-lookup"><span data-stu-id="18687-279">If a user account is disabled in back-end systems:</span></span>

* <span data-ttu-id="18687-280">Il sistema di cookie autenticazione dell'app continua a elaborare le richieste in base all'autenticazione cookie .</span><span class="sxs-lookup"><span data-stu-id="18687-280">The app's cookie authentication system continues to process requests based on the authentication cookie.</span></span>
* <span data-ttu-id="18687-281">L'utente rimane connesso all'app fino a quando l'autenticazione cookie è valida.</span><span class="sxs-lookup"><span data-stu-id="18687-281">The user remains signed into the app as long as the authentication cookie is valid.</span></span>

<span data-ttu-id="18687-282">L' <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> evento può essere usato per intercettare ed eseguire l'override della convalida dell' cookie identità.</span><span class="sxs-lookup"><span data-stu-id="18687-282">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> event can be used to intercept and override validation of the cookie identity.</span></span> <span data-ttu-id="18687-283">La convalida cookie di a ogni richiesta attenua il rischio di revoca degli utenti che accedono all'app.</span><span class="sxs-lookup"><span data-stu-id="18687-283">Validating the cookie on every request mitigates the risk of revoked users accessing the app.</span></span>

<span data-ttu-id="18687-284">Un approccio alla cookie convalida si basa sul tenere traccia del momento in cui il database utente viene modificato.</span><span class="sxs-lookup"><span data-stu-id="18687-284">One approach to cookie validation is based on keeping track of when the user database changes.</span></span> <span data-ttu-id="18687-285">Se il database non è stato modificato dopo l'emissione dell'utente cookie , non è necessario autenticare di nuovo l'utente se cookie è ancora valido.</span><span class="sxs-lookup"><span data-stu-id="18687-285">If the database hasn't been changed since the user's cookie was issued, there's no need to re-authenticate the user if their cookie is still valid.</span></span> <span data-ttu-id="18687-286">Nell'app di esempio, il database viene implementato in `IUserRepository` e archivia un `LastChanged` valore.</span><span class="sxs-lookup"><span data-stu-id="18687-286">In the sample app, the database is implemented in `IUserRepository` and stores a `LastChanged` value.</span></span> <span data-ttu-id="18687-287">Quando un utente viene aggiornato nel database, il `LastChanged` valore viene impostato sull'ora corrente.</span><span class="sxs-lookup"><span data-stu-id="18687-287">When a user is updated in the database, the `LastChanged` value is set to the current time.</span></span>

<span data-ttu-id="18687-288">Per invalidare un oggetto cookie quando il database viene modificato in base al `LastChanged` valore, creare cookie con un' `LastChanged` attestazione contenente il `LastChanged` valore corrente dal database:</span><span class="sxs-lookup"><span data-stu-id="18687-288">In order to invalidate a cookie when the database changes based on the `LastChanged` value, create the cookie with a `LastChanged` claim containing the current `LastChanged` value from the database:</span></span>

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

<span data-ttu-id="18687-289">Per implementare una sostituzione per l' `ValidatePrincipal` evento, scrivere un metodo con la firma seguente in una classe che deriva da <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents> :</span><span class="sxs-lookup"><span data-stu-id="18687-289">To implement an override for the `ValidatePrincipal` event, write a method with the following signature in a class that derives from <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>:</span></span>

```csharp
ValidatePrincipal(CookieValidatePrincipalContext)
```

<span data-ttu-id="18687-290">Di seguito è riportato un esempio di implementazione di `CookieAuthenticationEvents` :</span><span class="sxs-lookup"><span data-stu-id="18687-290">The following is an example implementation of `CookieAuthenticationEvents`:</span></span>

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

<span data-ttu-id="18687-291">Registrare l'istanza degli eventi durante cookie la registrazione del servizio nel `Startup.ConfigureServices` metodo.</span><span class="sxs-lookup"><span data-stu-id="18687-291">Register the events instance during cookie service registration in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="18687-292">Fornire una [registrazione del servizio con ambito](xref:fundamentals/dependency-injection#service-lifetimes) per la `CustomCookieAuthenticationEvents` classe:</span><span class="sxs-lookup"><span data-stu-id="18687-292">Provide a [scoped service registration](xref:fundamentals/dependency-injection#service-lifetimes) for your `CustomCookieAuthenticationEvents` class:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.EventsType = typeof(CustomCookieAuthenticationEvents);
    });

services.AddScoped<CustomCookieAuthenticationEvents>();
```

<span data-ttu-id="18687-293">Si consideri una situazione in cui il nome dell'utente viene aggiornato &mdash; una decisione che non influisce sulla sicurezza in alcun modo.</span><span class="sxs-lookup"><span data-stu-id="18687-293">Consider a situation in which the user's name is updated&mdash;a decision that doesn't affect security in any way.</span></span> <span data-ttu-id="18687-294">Se si desidera aggiornare l'entità utente in modo non distruttivo, chiamare `context.ReplacePrincipal` e impostare la `context.ShouldRenew` proprietà su `true` .</span><span class="sxs-lookup"><span data-stu-id="18687-294">If you want to non-destructively update the user principal, call `context.ReplacePrincipal` and set the `context.ShouldRenew` property to `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="18687-295">L'approccio descritto qui viene attivato a ogni richiesta.</span><span class="sxs-lookup"><span data-stu-id="18687-295">The approach described here is triggered on every request.</span></span> <span data-ttu-id="18687-296">La convalida cookie dell'autenticazione per tutti gli utenti in ogni richiesta può comportare un notevole calo delle prestazioni per l'app.</span><span class="sxs-lookup"><span data-stu-id="18687-296">Validating authentication cookies for all users on every request can result in a large performance penalty for the app.</span></span>

## <a name="persistent-no-loccookies"></a><span data-ttu-id="18687-297">S persistente cookie</span><span class="sxs-lookup"><span data-stu-id="18687-297">Persistent cookies</span></span>

<span data-ttu-id="18687-298">È possibile che il venga cookie mantenuto tra le sessioni del browser.</span><span class="sxs-lookup"><span data-stu-id="18687-298">You may want the cookie to persist across browser sessions.</span></span> <span data-ttu-id="18687-299">Questa persistenza deve essere abilitata solo con il consenso esplicito dell'utente con una casella di controllo "Remember me" all'accesso o un meccanismo simile.</span><span class="sxs-lookup"><span data-stu-id="18687-299">This persistence should only be enabled with explicit user consent with a "Remember Me" check box on sign in or a similar mechanism.</span></span> 

<span data-ttu-id="18687-300">Il frammento di codice seguente crea un'identità e corrispondente che rimane inalterata cookie attraverso le chiusure del browser.</span><span class="sxs-lookup"><span data-stu-id="18687-300">The following code snippet creates an identity and corresponding cookie that survives through browser closures.</span></span> <span data-ttu-id="18687-301">Vengono rispettate tutte le impostazioni di scadenza scorrevoli configurate in precedenza.</span><span class="sxs-lookup"><span data-stu-id="18687-301">Any sliding expiration settings previously configured are honored.</span></span> <span data-ttu-id="18687-302">Se l'oggetto cookie scade quando il browser viene chiuso, il browser cancella il oggetto cookie dopo il riavvio.</span><span class="sxs-lookup"><span data-stu-id="18687-302">If the cookie expires while the browser is closed, the browser clears the cookie once it's restarted.</span></span>

<span data-ttu-id="18687-303">Imposta <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> su `true` in <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties> :</span><span class="sxs-lookup"><span data-stu-id="18687-303">Set <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> to `true` in <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>:</span></span>

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

## <a name="absolute-no-loccookie-expiration"></a><span data-ttu-id="18687-304">cookieScadenza assoluta</span><span class="sxs-lookup"><span data-stu-id="18687-304">Absolute cookie expiration</span></span>

<span data-ttu-id="18687-305">È possibile impostare un'ora di scadenza assoluta con <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc> .</span><span class="sxs-lookup"><span data-stu-id="18687-305">An absolute expiration time can be set with <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>.</span></span> <span data-ttu-id="18687-306">Per creare un oggetto persistente cookie , `IsPersistent` è necessario impostare anche.</span><span class="sxs-lookup"><span data-stu-id="18687-306">To create a persistent cookie, `IsPersistent` must also be set.</span></span> <span data-ttu-id="18687-307">In caso contrario, cookie viene creato con una durata basata sulla sessione e potrebbe scadere prima o dopo il ticket di autenticazione che possiede.</span><span class="sxs-lookup"><span data-stu-id="18687-307">Otherwise, the cookie is created with a session-based lifetime and could expire either before or after the authentication ticket that it holds.</span></span> <span data-ttu-id="18687-308">Quando `ExpiresUtc` è impostato, esegue l'override del valore dell' <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.ExpireTimeSpan> opzione di <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions> , se impostata.</span><span class="sxs-lookup"><span data-stu-id="18687-308">When `ExpiresUtc` is set, it overrides the value of the <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.ExpireTimeSpan> option of <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions>, if set.</span></span>

<span data-ttu-id="18687-309">Il frammento di codice seguente crea un'identità e corrisponde alla cookie durata di 20 minuti.</span><span class="sxs-lookup"><span data-stu-id="18687-309">The following code snippet creates an identity and corresponding cookie that lasts for 20 minutes.</span></span> <span data-ttu-id="18687-310">Questa operazione ignora le impostazioni di scadenza scorrevoli configurate in precedenza.</span><span class="sxs-lookup"><span data-stu-id="18687-310">This ignores any sliding expiration settings previously configured.</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="18687-311">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="18687-311">Additional resources</span></span>

* <xref:security/authorization/limitingidentitybyscheme>
* <xref:security/authorization/claims>
* [<span data-ttu-id="18687-312">Controlli dei ruoli basati su criteri</span><span class="sxs-lookup"><span data-stu-id="18687-312">Policy-based role checks</span></span>](xref:security/authorization/roles#policy-based-role-checks)
* <xref:host-and-deploy/web-farm>
