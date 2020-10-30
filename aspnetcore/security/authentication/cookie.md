---
title: 'USA :::no-loc(cookie)::: autenticazione senza :::no-loc(ASP.NET Core Identity):::'
author: rick-anderson
description: "Informazioni su come usare :::no-loc(cookie)::: l'autenticazione senza :::no-loc(ASP.NET Core Identity)::: ."
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 02/11/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: 'security/authentication/:::no-loc(cookie):::'
ms.openlocfilehash: 04469e0e75c433b40b364873a7e72e30421936f4
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061353"
---
# <a name="use-no-loccookie-authentication-without-no-locaspnet-core-identity"></a><span data-ttu-id="4b204-103">USA :::no-loc(cookie)::: autenticazione senza :::no-loc(ASP.NET Core Identity):::</span><span class="sxs-lookup"><span data-stu-id="4b204-103">Use :::no-loc(cookie)::: authentication without :::no-loc(ASP.NET Core Identity):::</span></span>

<span data-ttu-id="4b204-104">Autore: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="4b204-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="4b204-105">:::no-loc(ASP.NET Core Identity)::: è un provider di autenticazione completo e completo per la creazione e la gestione degli account di accesso.</span><span class="sxs-lookup"><span data-stu-id="4b204-105">:::no-loc(ASP.NET Core Identity)::: is a complete, full-featured authentication provider for creating and maintaining logins.</span></span> <span data-ttu-id="4b204-106">Tuttavia, un :::no-loc(cookie)::: provider di autenticazione basato su senza :::no-loc(ASP.NET Core Identity)::: può essere utilizzato.</span><span class="sxs-lookup"><span data-stu-id="4b204-106">However, a :::no-loc(cookie):::-based authentication provider without :::no-loc(ASP.NET Core Identity)::: can be used.</span></span> <span data-ttu-id="4b204-107">Per altre informazioni, vedere <xref:security/authentication/identity>.</span><span class="sxs-lookup"><span data-stu-id="4b204-107">For more information, see <xref:security/authentication/identity>.</span></span>

<span data-ttu-id="4b204-108">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/:::no-loc(cookie):::/samples) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="4b204-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/:::no-loc(cookie):::/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="4b204-109">A scopo dimostrativo nell'app di esempio, l'account utente per l'utente ipotetico, Maria Rodriguez, è hardcoded nell'app.</span><span class="sxs-lookup"><span data-stu-id="4b204-109">For demonstration purposes in the sample app, the user account for the hypothetical user, Maria Rodriguez, is hardcoded into the app.</span></span> <span data-ttu-id="4b204-110">Usare l'indirizzo di **posta elettronica** `maria.rodriguez@contoso.com` e qualsiasi password per accedere all'utente.</span><span class="sxs-lookup"><span data-stu-id="4b204-110">Use the **Email** address `maria.rodriguez@contoso.com` and any password to sign in the user.</span></span> <span data-ttu-id="4b204-111">L'utente viene autenticato nel `AuthenticateUser` metodo nel file *pages/account/login. cshtml. cs* .</span><span class="sxs-lookup"><span data-stu-id="4b204-111">The user is authenticated in the `AuthenticateUser` method in the *Pages/Account/Login.cshtml.cs* file.</span></span> <span data-ttu-id="4b204-112">In un esempio reale, l'utente viene autenticato a fronte di un database.</span><span class="sxs-lookup"><span data-stu-id="4b204-112">In a real-world example, the user would be authenticated against a database.</span></span>

## <a name="configuration"></a><span data-ttu-id="4b204-113">Configurazione</span><span class="sxs-lookup"><span data-stu-id="4b204-113">Configuration</span></span>

<span data-ttu-id="4b204-114">Nel `Startup.ConfigureServices` metodo creare i servizi del middleware di autenticazione con i <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Cookie):::Extensions.Add:::no-loc(Cookie):::*> metodi e:</span><span class="sxs-lookup"><span data-stu-id="4b204-114">In the `Startup.ConfigureServices` method, create the Authentication Middleware services with the <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> and <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Cookie):::Extensions.Add:::no-loc(Cookie):::*> methods:</span></span>

[!code-csharp[](:::no-loc(cookie):::/samples/3.x/:::no-loc(Cookie):::Sample/Startup.cs?name=snippet1)]

<span data-ttu-id="4b204-115"><xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme> passato a `AddAuthentication` imposta lo schema di autenticazione predefinito per l'app.</span><span class="sxs-lookup"><span data-stu-id="4b204-115"><xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme> passed to `AddAuthentication` sets the default authentication scheme for the app.</span></span> <span data-ttu-id="4b204-116">`AuthenticationScheme` è utile quando sono presenti più istanze di :::no-loc(cookie)::: autenticazione e si desidera [autorizzare con uno schema specifico](xref:security/authorization/limitingidentitybyscheme).</span><span class="sxs-lookup"><span data-stu-id="4b204-116">`AuthenticationScheme` is useful when there are multiple instances of :::no-loc(cookie)::: authentication and you want to [authorize with a specific scheme](xref:security/authorization/limitingidentitybyscheme).</span></span> <span data-ttu-id="4b204-117">L'impostazione `AuthenticationScheme` di su [ :::no-loc(Cookie)::: AuthenticationDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme) fornisce il valore " :::no-loc(Cookie)::: s" per lo schema.</span><span class="sxs-lookup"><span data-stu-id="4b204-117">Setting the `AuthenticationScheme` to [:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme) provides a value of ":::no-loc(Cookie):::s" for the scheme.</span></span> <span data-ttu-id="4b204-118">È possibile specificare qualsiasi valore stringa che distingua lo schema.</span><span class="sxs-lookup"><span data-stu-id="4b204-118">You can supply any string value that distinguishes the scheme.</span></span>

<span data-ttu-id="4b204-119">Lo schema di autenticazione dell'app è diverso dallo schema di :::no-loc(cookie)::: autenticazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="4b204-119">The app's authentication scheme is different from the app's :::no-loc(cookie)::: authentication scheme.</span></span> <span data-ttu-id="4b204-120">Quando uno :::no-loc(cookie)::: schema di autenticazione non viene fornito a <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Cookie):::Extensions.Add:::no-loc(Cookie):::*> , USA `:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme` (" :::no-loc(Cookie)::: s").</span><span class="sxs-lookup"><span data-stu-id="4b204-120">When a :::no-loc(cookie)::: authentication scheme isn't provided to <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Cookie):::Extensions.Add:::no-loc(Cookie):::*>, it uses `:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme` (":::no-loc(Cookie):::s").</span></span>

<span data-ttu-id="4b204-121">La :::no-loc(cookie)::: proprietà Authentication <xref:Microsoft.AspNetCore.Http.:::no-loc(Cookie):::Builder.IsEssential> è impostata su per `true` impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="4b204-121">The authentication :::no-loc(cookie):::'s <xref:Microsoft.AspNetCore.Http.:::no-loc(Cookie):::Builder.IsEssential> property is set to `true` by default.</span></span> <span data-ttu-id="4b204-122">:::no-loc(cookie):::Sono consentite le autenticazioni quando un visitatore del sito non ha acconsentito alla raccolta dei dati.</span><span class="sxs-lookup"><span data-stu-id="4b204-122">Authentication :::no-loc(cookie):::s are allowed when a site visitor hasn't consented to data collection.</span></span> <span data-ttu-id="4b204-123">Per altre informazioni, vedere <xref:security/gdpr#essential-:::no-loc(cookie):::s>.</span><span class="sxs-lookup"><span data-stu-id="4b204-123">For more information, see <xref:security/gdpr#essential-:::no-loc(cookie):::s>.</span></span>

<span data-ttu-id="4b204-124">In `Startup.Configure` , chiamare `UseAuthentication` e `UseAuthorization` per impostare la `HttpContext.User` proprietà ed eseguire il middleware di autorizzazione per le richieste.</span><span class="sxs-lookup"><span data-stu-id="4b204-124">In `Startup.Configure`, call `UseAuthentication` and `UseAuthorization` to set the `HttpContext.User` property and run Authorization Middleware for requests.</span></span> <span data-ttu-id="4b204-125">Chiamare i `UseAuthentication` `UseAuthorization` metodi e prima di chiamare `UseEndpoints` :</span><span class="sxs-lookup"><span data-stu-id="4b204-125">Call the `UseAuthentication` and `UseAuthorization` methods before calling `UseEndpoints`:</span></span>

[!code-csharp[](:::no-loc(cookie):::/samples/3.x/:::no-loc(Cookie):::Sample/Startup.cs?name=snippet2)]

<span data-ttu-id="4b204-126">La <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationOptions> classe viene utilizzata per configurare le opzioni del provider di autenticazione.</span><span class="sxs-lookup"><span data-stu-id="4b204-126">The <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationOptions> class is used to configure the authentication provider options.</span></span>

<span data-ttu-id="4b204-127">Impostare `:::no-loc(Cookie):::AuthenticationOptions` nella configurazione del servizio per l'autenticazione nel `Startup.ConfigureServices` Metodo:</span><span class="sxs-lookup"><span data-stu-id="4b204-127">Set `:::no-loc(Cookie):::AuthenticationOptions` in the service configuration for authentication in the `Startup.ConfigureServices` method:</span></span>

```csharp
services.AddAuthentication(:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme)
    .Add:::no-loc(Cookie):::(options =>
    {
        ...
    });
```

## <a name="no-loccookie-policy-middleware"></a><span data-ttu-id="4b204-128">:::no-loc(Cookie)::: Middleware dei criteri</span><span class="sxs-lookup"><span data-stu-id="4b204-128">:::no-loc(Cookie)::: Policy Middleware</span></span>

<span data-ttu-id="4b204-129">Il [ :::no-loc(Cookie)::: middleware dei criteri](xref:Microsoft.AspNetCore.:::no-loc(Cookie):::Policy.:::no-loc(Cookie):::PolicyMiddleware) Abilita le :::no-loc(cookie)::: funzionalità dei criteri.</span><span class="sxs-lookup"><span data-stu-id="4b204-129">[:::no-loc(Cookie)::: Policy Middleware](xref:Microsoft.AspNetCore.:::no-loc(Cookie):::Policy.:::no-loc(Cookie):::PolicyMiddleware) enables :::no-loc(cookie)::: policy capabilities.</span></span> <span data-ttu-id="4b204-130">L'aggiunta del middleware alla pipeline di elaborazione delle app è sensibile agli ordini &mdash; , ma interessa solo i componenti downstream registrati nella pipeline.</span><span class="sxs-lookup"><span data-stu-id="4b204-130">Adding the middleware to the app processing pipeline is order sensitive&mdash;it only affects downstream components registered in the pipeline.</span></span>

```csharp
app.Use:::no-loc(Cookie):::Policy(:::no-loc(cookie):::PolicyOptions);
```

<span data-ttu-id="4b204-131">Utilizzare <xref:Microsoft.AspNetCore.Builder.:::no-loc(Cookie):::PolicyOptions> fornito al :::no-loc(Cookie)::: middleware dei criteri per controllare le caratteristiche globali dell' :::no-loc(cookie)::: elaborazione e agganciare i :::no-loc(cookie)::: gestori di elaborazione quando :::no-loc(cookie)::: vengono aggiunti o eliminati.</span><span class="sxs-lookup"><span data-stu-id="4b204-131">Use <xref:Microsoft.AspNetCore.Builder.:::no-loc(Cookie):::PolicyOptions> provided to the :::no-loc(Cookie)::: Policy Middleware to control global characteristics of :::no-loc(cookie)::: processing and hook into :::no-loc(cookie)::: processing handlers when :::no-loc(cookie):::s are appended or deleted.</span></span>

<span data-ttu-id="4b204-132">Il <xref:Microsoft.AspNetCore.Builder.:::no-loc(Cookie):::PolicyOptions.MinimumSameSitePolicy> valore predefinito è `SameSiteMode.Lax` consentire l'autenticazione OAuth2.</span><span class="sxs-lookup"><span data-stu-id="4b204-132">The default <xref:Microsoft.AspNetCore.Builder.:::no-loc(Cookie):::PolicyOptions.MinimumSameSitePolicy> value is `SameSiteMode.Lax` to permit OAuth2 authentication.</span></span> <span data-ttu-id="4b204-133">Per applicare rigorosamente un criterio dello stesso sito di `SameSiteMode.Strict` , impostare il `MinimumSameSitePolicy` .</span><span class="sxs-lookup"><span data-stu-id="4b204-133">To strictly enforce a same-site policy of `SameSiteMode.Strict`, set the `MinimumSameSitePolicy`.</span></span> <span data-ttu-id="4b204-134">Sebbene questa impostazione interrompa OAuth2 e altri schemi di autenticazione tra le origini, eleva il livello di :::no-loc(cookie)::: sicurezza per altri tipi di app che non si basano sull'elaborazione di richieste tra origini.</span><span class="sxs-lookup"><span data-stu-id="4b204-134">Although this setting breaks OAuth2 and other cross-origin authentication schemes, it elevates the level of :::no-loc(cookie)::: security for other types of apps that don't rely on cross-origin request processing.</span></span>

```csharp
var :::no-loc(cookie):::PolicyOptions = new :::no-loc(Cookie):::PolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

<span data-ttu-id="4b204-135">L' :::no-loc(Cookie)::: impostazione del middleware per i criteri di `MinimumSameSitePolicy` può influire sull'impostazione di `:::no-loc(Cookie):::.SameSite` in `:::no-loc(Cookie):::AuthenticationOptions` impostazioni in base alla matrice riportata di seguito.</span><span class="sxs-lookup"><span data-stu-id="4b204-135">The :::no-loc(Cookie)::: Policy Middleware setting for `MinimumSameSitePolicy` can affect the setting of `:::no-loc(Cookie):::.SameSite` in `:::no-loc(Cookie):::AuthenticationOptions` settings according to the matrix below.</span></span>

| <span data-ttu-id="4b204-136">MinimumSameSitePolicy</span><span class="sxs-lookup"><span data-stu-id="4b204-136">MinimumSameSitePolicy</span></span> | <span data-ttu-id="4b204-137">:::no-loc(Cookie):::. Navigava sullostesso sito</span><span class="sxs-lookup"><span data-stu-id="4b204-137">:::no-loc(Cookie):::.SameSite</span></span> | <span data-ttu-id="4b204-138">Risultato :::no-loc(Cookie)::: . Impostazione navigava sullostesso sito</span><span class="sxs-lookup"><span data-stu-id="4b204-138">Resultant :::no-loc(Cookie):::.SameSite setting</span></span> |
| --------------------- | --------------- | --------------------------------- |
| <span data-ttu-id="4b204-139">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="4b204-139">SameSiteMode.None</span></span>     | <span data-ttu-id="4b204-140">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="4b204-140">SameSiteMode.None</span></span><br><span data-ttu-id="4b204-141">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="4b204-141">SameSiteMode.Lax</span></span><br><span data-ttu-id="4b204-142">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="4b204-142">SameSiteMode.Strict</span></span> | <span data-ttu-id="4b204-143">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="4b204-143">SameSiteMode.None</span></span><br><span data-ttu-id="4b204-144">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="4b204-144">SameSiteMode.Lax</span></span><br><span data-ttu-id="4b204-145">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="4b204-145">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="4b204-146">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="4b204-146">SameSiteMode.Lax</span></span>      | <span data-ttu-id="4b204-147">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="4b204-147">SameSiteMode.None</span></span><br><span data-ttu-id="4b204-148">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="4b204-148">SameSiteMode.Lax</span></span><br><span data-ttu-id="4b204-149">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="4b204-149">SameSiteMode.Strict</span></span> | <span data-ttu-id="4b204-150">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="4b204-150">SameSiteMode.Lax</span></span><br><span data-ttu-id="4b204-151">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="4b204-151">SameSiteMode.Lax</span></span><br><span data-ttu-id="4b204-152">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="4b204-152">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="4b204-153">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="4b204-153">SameSiteMode.Strict</span></span>   | <span data-ttu-id="4b204-154">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="4b204-154">SameSiteMode.None</span></span><br><span data-ttu-id="4b204-155">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="4b204-155">SameSiteMode.Lax</span></span><br><span data-ttu-id="4b204-156">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="4b204-156">SameSiteMode.Strict</span></span> | <span data-ttu-id="4b204-157">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="4b204-157">SameSiteMode.Strict</span></span><br><span data-ttu-id="4b204-158">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="4b204-158">SameSiteMode.Strict</span></span><br><span data-ttu-id="4b204-159">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="4b204-159">SameSiteMode.Strict</span></span> |

## <a name="create-an-authentication-no-loccookie"></a><span data-ttu-id="4b204-160">Creare un'autenticazione :::no-loc(cookie):::</span><span class="sxs-lookup"><span data-stu-id="4b204-160">Create an authentication :::no-loc(cookie):::</span></span>

<span data-ttu-id="4b204-161">Per creare un oggetto che :::no-loc(cookie)::: contiene informazioni sull'utente, costruire un oggetto <xref:System.Security.Claims.ClaimsPrincipal> .</span><span class="sxs-lookup"><span data-stu-id="4b204-161">To create a :::no-loc(cookie)::: holding user information, construct a <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="4b204-162">Le informazioni utente vengono serializzate e archiviate in :::no-loc(cookie)::: .</span><span class="sxs-lookup"><span data-stu-id="4b204-162">The user information is serialized and stored in the :::no-loc(cookie):::.</span></span> 

<span data-ttu-id="4b204-163">Creare una <xref:System.Security.Claims.Claims:::no-loc(Identity):::> con qualsiasi richiesta <xref:System.Security.Claims.Claim> e chiamare <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> per accedere all'utente:</span><span class="sxs-lookup"><span data-stu-id="4b204-163">Create a <xref:System.Security.Claims.Claims:::no-loc(Identity):::> with any required <xref:System.Security.Claims.Claim>s and call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> to sign in the user:</span></span>

[!code-csharp[](:::no-loc(cookie):::/samples/3.x/:::no-loc(Cookie):::Sample/Pages/Account/Login.cshtml.cs?name=snippet1)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]

<span data-ttu-id="4b204-164">`SignInAsync` Crea un oggetto crittografato :::no-loc(cookie)::: e lo aggiunge alla risposta corrente.</span><span class="sxs-lookup"><span data-stu-id="4b204-164">`SignInAsync` creates an encrypted :::no-loc(cookie)::: and adds it to the current response.</span></span> <span data-ttu-id="4b204-165">Se `AuthenticationScheme` non è specificato, viene usato lo schema predefinito.</span><span class="sxs-lookup"><span data-stu-id="4b204-165">If `AuthenticationScheme` isn't specified, the default scheme is used.</span></span>

<span data-ttu-id="4b204-166"><xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.RedirectUri> viene utilizzato solo in alcuni percorsi specifici per impostazione predefinita, ad esempio il percorso di accesso e i percorsi di disconnessione.</span><span class="sxs-lookup"><span data-stu-id="4b204-166"><xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.RedirectUri> is only used on a few specific paths by default, for example, the login path and logout paths.</span></span> <span data-ttu-id="4b204-167">Per altre informazioni, vedere l' [ :::no-loc(Cookie)::: origine AuthenticationHandler](https://github.com/dotnet/aspnetcore/blob/f2e6e6ff334176540ef0b3291122e359c2106d1a/src/Security/Authentication/:::no-loc(Cookie):::s/src/:::no-loc(Cookie):::AuthenticationHandler.cs#L334).</span><span class="sxs-lookup"><span data-stu-id="4b204-167">For more information see the [:::no-loc(Cookie):::AuthenticationHandler source](https://github.com/dotnet/aspnetcore/blob/f2e6e6ff334176540ef0b3291122e359c2106d1a/src/Security/Authentication/:::no-loc(Cookie):::s/src/:::no-loc(Cookie):::AuthenticationHandler.cs#L334).</span></span>

<span data-ttu-id="4b204-168">Per la crittografia viene usato il sistema di [protezione dei dati](xref:security/data-protection/using-data-protection) di ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="4b204-168">ASP.NET Core's [Data Protection](xref:security/data-protection/using-data-protection) system is used for encryption.</span></span> <span data-ttu-id="4b204-169">Per un'app ospitata in più computer, bilanciamento del carico tra app o uso di un Web farm, [configurare la protezione dei dati](xref:security/data-protection/configuration/overview) in modo da usare lo stesso anello chiave e l'identificatore dell'app.</span><span class="sxs-lookup"><span data-stu-id="4b204-169">For an app hosted on multiple machines, load balancing across apps, or using a web farm, [configure data protection](xref:security/data-protection/configuration/overview) to use the same key ring and app identifier.</span></span>

## <a name="sign-out"></a><span data-ttu-id="4b204-170">Disconnetti</span><span class="sxs-lookup"><span data-stu-id="4b204-170">Sign out</span></span>

<span data-ttu-id="4b204-171">Per disconnettersi dall'utente corrente ed eliminare il relativo :::no-loc(cookie)::: , chiamare <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*> :</span><span class="sxs-lookup"><span data-stu-id="4b204-171">To sign out the current user and delete their :::no-loc(cookie):::, call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>:</span></span>

[!code-csharp[](:::no-loc(cookie):::/samples/3.x/:::no-loc(Cookie):::Sample/Pages/Account/Login.cshtml.cs?name=snippet2)]

<span data-ttu-id="4b204-172">Se `:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme` (o " :::no-loc(Cookie)::: s") non viene usato come schema (ad esempio, "Contoso :::no-loc(Cookie)::: "), fornire lo schema usato durante la configurazione del provider di autenticazione.</span><span class="sxs-lookup"><span data-stu-id="4b204-172">If `:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme` (or ":::no-loc(Cookie):::s") isn't used as the scheme (for example, "Contoso:::no-loc(Cookie):::"), supply the scheme used when configuring the authentication provider.</span></span> <span data-ttu-id="4b204-173">In caso contrario, verrà utilizzato lo schema predefinito.</span><span class="sxs-lookup"><span data-stu-id="4b204-173">Otherwise, the default scheme is used.</span></span>

<span data-ttu-id="4b204-174">Quando il browser viene chiuso, Elimina automaticamente le istanze di basate su sessione :::no-loc(cookie)::: (non persistenti :::no-loc(cookie)::: ), ma nessuna viene :::no-loc(cookie)::: cancellata quando viene chiusa una singola scheda.</span><span class="sxs-lookup"><span data-stu-id="4b204-174">When the browser closes it automatically deletes session based :::no-loc(cookie):::s (non-persistent :::no-loc(cookie):::s), but no :::no-loc(cookie):::s are cleared when an individual tab is closed.</span></span> <span data-ttu-id="4b204-175">Il server non riceve notifiche sugli eventi di chiusura di schede o browser.</span><span class="sxs-lookup"><span data-stu-id="4b204-175">The server is not notified of tab or browser close events.</span></span>

## <a name="react-to-back-end-changes"></a><span data-ttu-id="4b204-176">Reagire alle modifiche del back-end</span><span class="sxs-lookup"><span data-stu-id="4b204-176">React to back-end changes</span></span>

<span data-ttu-id="4b204-177">Una volta :::no-loc(cookie)::: creato un oggetto, :::no-loc(cookie)::: è l'unica origine di identità.</span><span class="sxs-lookup"><span data-stu-id="4b204-177">Once a :::no-loc(cookie)::: is created, the :::no-loc(cookie)::: is the single source of identity.</span></span> <span data-ttu-id="4b204-178">Se un account utente è disabilitato nei sistemi back-end:</span><span class="sxs-lookup"><span data-stu-id="4b204-178">If a user account is disabled in back-end systems:</span></span>

* <span data-ttu-id="4b204-179">Il sistema di :::no-loc(cookie)::: autenticazione dell'app continua a elaborare le richieste in base all'autenticazione :::no-loc(cookie)::: .</span><span class="sxs-lookup"><span data-stu-id="4b204-179">The app's :::no-loc(cookie)::: authentication system continues to process requests based on the authentication :::no-loc(cookie):::.</span></span>
* <span data-ttu-id="4b204-180">L'utente rimane connesso all'app fino a quando l'autenticazione :::no-loc(cookie)::: è valida.</span><span class="sxs-lookup"><span data-stu-id="4b204-180">The user remains signed into the app as long as the authentication :::no-loc(cookie)::: is valid.</span></span>

<span data-ttu-id="4b204-181">L' <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationEvents.ValidatePrincipal*> evento può essere usato per intercettare ed eseguire l'override della convalida dell' :::no-loc(cookie)::: identità.</span><span class="sxs-lookup"><span data-stu-id="4b204-181">The <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationEvents.ValidatePrincipal*> event can be used to intercept and override validation of the :::no-loc(cookie)::: identity.</span></span> <span data-ttu-id="4b204-182">La convalida :::no-loc(cookie)::: di a ogni richiesta attenua il rischio di revoca degli utenti che accedono all'app.</span><span class="sxs-lookup"><span data-stu-id="4b204-182">Validating the :::no-loc(cookie)::: on every request mitigates the risk of revoked users accessing the app.</span></span>

<span data-ttu-id="4b204-183">Un approccio alla :::no-loc(cookie)::: convalida si basa sul tenere traccia del momento in cui il database utente viene modificato.</span><span class="sxs-lookup"><span data-stu-id="4b204-183">One approach to :::no-loc(cookie)::: validation is based on keeping track of when the user database changes.</span></span> <span data-ttu-id="4b204-184">Se il database non è stato modificato dopo l'emissione dell'utente :::no-loc(cookie)::: , non è necessario autenticare di nuovo l'utente se :::no-loc(cookie)::: è ancora valido.</span><span class="sxs-lookup"><span data-stu-id="4b204-184">If the database hasn't been changed since the user's :::no-loc(cookie)::: was issued, there's no need to re-authenticate the user if their :::no-loc(cookie)::: is still valid.</span></span> <span data-ttu-id="4b204-185">Nell'app di esempio, il database viene implementato in `IUserRepository` e archivia un `LastChanged` valore.</span><span class="sxs-lookup"><span data-stu-id="4b204-185">In the sample app, the database is implemented in `IUserRepository` and stores a `LastChanged` value.</span></span> <span data-ttu-id="4b204-186">Quando un utente viene aggiornato nel database, il `LastChanged` valore viene impostato sull'ora corrente.</span><span class="sxs-lookup"><span data-stu-id="4b204-186">When a user is updated in the database, the `LastChanged` value is set to the current time.</span></span>

<span data-ttu-id="4b204-187">Per invalidare un oggetto :::no-loc(cookie)::: quando il database viene modificato in base al `LastChanged` valore, creare :::no-loc(cookie)::: con un' `LastChanged` attestazione contenente il `LastChanged` valore corrente dal database:</span><span class="sxs-lookup"><span data-stu-id="4b204-187">In order to invalidate a :::no-loc(cookie)::: when the database changes based on the `LastChanged` value, create the :::no-loc(cookie)::: with a `LastChanged` claim containing the current `LastChanged` value from the database:</span></span>

```csharp
var claims = new List<Claim>
{
    new Claim(ClaimTypes.Name, user.Email),
    new Claim("LastChanged", {Database Value})
};

var claims:::no-loc(Identity)::: = new Claims:::no-loc(Identity):::(
    claims, 
    :::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme);

await HttpContext.SignInAsync(
    :::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme, 
    new ClaimsPrincipal(claims:::no-loc(Identity):::));
```

<span data-ttu-id="4b204-188">Per implementare una sostituzione per l' `ValidatePrincipal` evento, scrivere un metodo con la firma seguente in una classe che deriva da <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationEvents> :</span><span class="sxs-lookup"><span data-stu-id="4b204-188">To implement an override for the `ValidatePrincipal` event, write a method with the following signature in a class that derives from <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationEvents>:</span></span>

```csharp
ValidatePrincipal(:::no-loc(Cookie):::ValidatePrincipalContext)
```

<span data-ttu-id="4b204-189">Di seguito è riportato un esempio di implementazione di `:::no-loc(Cookie):::AuthenticationEvents` :</span><span class="sxs-lookup"><span data-stu-id="4b204-189">The following is an example implementation of `:::no-loc(Cookie):::AuthenticationEvents`:</span></span>

```csharp
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Authentication;
using Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s;

public class Custom:::no-loc(Cookie):::AuthenticationEvents : :::no-loc(Cookie):::AuthenticationEvents
{
    private readonly IUserRepository _userRepository;

    public Custom:::no-loc(Cookie):::AuthenticationEvents(IUserRepository userRepository)
    {
        // Get the database from registered DI services.
        _userRepository = userRepository;
    }

    public override async Task ValidatePrincipal(:::no-loc(Cookie):::ValidatePrincipalContext context)
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
                :::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme);
        }
    }
}
```

<span data-ttu-id="4b204-190">Registrare l'istanza degli eventi durante :::no-loc(cookie)::: la registrazione del servizio nel `Startup.ConfigureServices` metodo.</span><span class="sxs-lookup"><span data-stu-id="4b204-190">Register the events instance during :::no-loc(cookie)::: service registration in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="4b204-191">Fornire una [registrazione del servizio con ambito](xref:fundamentals/dependency-injection#service-lifetimes) per la `Custom:::no-loc(Cookie):::AuthenticationEvents` classe:</span><span class="sxs-lookup"><span data-stu-id="4b204-191">Provide a [scoped service registration](xref:fundamentals/dependency-injection#service-lifetimes) for your `Custom:::no-loc(Cookie):::AuthenticationEvents` class:</span></span>

```csharp
services.AddAuthentication(:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme)
    .Add:::no-loc(Cookie):::(options =>
    {
        options.EventsType = typeof(Custom:::no-loc(Cookie):::AuthenticationEvents);
    });

services.AddScoped<Custom:::no-loc(Cookie):::AuthenticationEvents>();
```

<span data-ttu-id="4b204-192">Si consideri una situazione in cui il nome dell'utente viene aggiornato &mdash; una decisione che non influisce sulla sicurezza in alcun modo.</span><span class="sxs-lookup"><span data-stu-id="4b204-192">Consider a situation in which the user's name is updated&mdash;a decision that doesn't affect security in any way.</span></span> <span data-ttu-id="4b204-193">Se si desidera aggiornare l'entità utente in modo non distruttivo, chiamare `context.ReplacePrincipal` e impostare la `context.ShouldRenew` proprietà su `true` .</span><span class="sxs-lookup"><span data-stu-id="4b204-193">If you want to non-destructively update the user principal, call `context.ReplacePrincipal` and set the `context.ShouldRenew` property to `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="4b204-194">L'approccio descritto qui viene attivato a ogni richiesta.</span><span class="sxs-lookup"><span data-stu-id="4b204-194">The approach described here is triggered on every request.</span></span> <span data-ttu-id="4b204-195">La convalida :::no-loc(cookie)::: dell'autenticazione per tutti gli utenti in ogni richiesta può comportare un notevole calo delle prestazioni per l'app.</span><span class="sxs-lookup"><span data-stu-id="4b204-195">Validating authentication :::no-loc(cookie):::s for all users on every request can result in a large performance penalty for the app.</span></span>

## <a name="persistent-no-loccookies"></a><span data-ttu-id="4b204-196">S persistente :::no-loc(cookie):::</span><span class="sxs-lookup"><span data-stu-id="4b204-196">Persistent :::no-loc(cookie):::s</span></span>

<span data-ttu-id="4b204-197">È possibile che il venga :::no-loc(cookie)::: mantenuto tra le sessioni del browser.</span><span class="sxs-lookup"><span data-stu-id="4b204-197">You may want the :::no-loc(cookie)::: to persist across browser sessions.</span></span> <span data-ttu-id="4b204-198">Questa persistenza deve essere abilitata solo con il consenso esplicito dell'utente con una casella di controllo "Remember me" all'accesso o un meccanismo simile.</span><span class="sxs-lookup"><span data-stu-id="4b204-198">This persistence should only be enabled with explicit user consent with a "Remember Me" check box on sign in or a similar mechanism.</span></span> 

<span data-ttu-id="4b204-199">Il frammento di codice seguente crea un'identità e corrispondente che rimane inalterata :::no-loc(cookie)::: attraverso le chiusure del browser.</span><span class="sxs-lookup"><span data-stu-id="4b204-199">The following code snippet creates an identity and corresponding :::no-loc(cookie)::: that survives through browser closures.</span></span> <span data-ttu-id="4b204-200">Vengono rispettate tutte le impostazioni di scadenza scorrevoli configurate in precedenza.</span><span class="sxs-lookup"><span data-stu-id="4b204-200">Any sliding expiration settings previously configured are honored.</span></span> <span data-ttu-id="4b204-201">Se l'oggetto :::no-loc(cookie)::: scade quando il browser viene chiuso, il browser cancella il oggetto :::no-loc(cookie)::: dopo il riavvio.</span><span class="sxs-lookup"><span data-stu-id="4b204-201">If the :::no-loc(cookie)::: expires while the browser is closed, the browser clears the :::no-loc(cookie)::: once it's restarted.</span></span>

<span data-ttu-id="4b204-202">Imposta <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> su `true` in <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties> :</span><span class="sxs-lookup"><span data-stu-id="4b204-202">Set <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> to `true` in <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>:</span></span>

```csharp
// using Microsoft.AspNetCore.Authentication;

await HttpContext.SignInAsync(
    :::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claims:::no-loc(Identity):::),
    new AuthenticationProperties
    {
        IsPersistent = true
    });
```

## <a name="absolute-no-loccookie-expiration"></a><span data-ttu-id="4b204-203">:::no-loc(cookie):::Scadenza assoluta</span><span class="sxs-lookup"><span data-stu-id="4b204-203">Absolute :::no-loc(cookie)::: expiration</span></span>

<span data-ttu-id="4b204-204">È possibile impostare un'ora di scadenza assoluta con <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc> .</span><span class="sxs-lookup"><span data-stu-id="4b204-204">An absolute expiration time can be set with <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>.</span></span> <span data-ttu-id="4b204-205">Per creare un oggetto persistente :::no-loc(cookie)::: , `IsPersistent` è necessario impostare anche.</span><span class="sxs-lookup"><span data-stu-id="4b204-205">To create a persistent :::no-loc(cookie):::, `IsPersistent` must also be set.</span></span> <span data-ttu-id="4b204-206">In caso contrario, :::no-loc(cookie)::: viene creato con una durata basata sulla sessione e potrebbe scadere prima o dopo il ticket di autenticazione che possiede.</span><span class="sxs-lookup"><span data-stu-id="4b204-206">Otherwise, the :::no-loc(cookie)::: is created with a session-based lifetime and could expire either before or after the authentication ticket that it holds.</span></span> <span data-ttu-id="4b204-207">Quando `ExpiresUtc` è impostato, esegue l'override del valore dell' <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationOptions.ExpireTimeSpan> opzione di <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationOptions> , se impostata.</span><span class="sxs-lookup"><span data-stu-id="4b204-207">When `ExpiresUtc` is set, it overrides the value of the <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationOptions.ExpireTimeSpan> option of <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationOptions>, if set.</span></span>

<span data-ttu-id="4b204-208">Il frammento di codice seguente crea un'identità e corrisponde alla :::no-loc(cookie)::: durata di 20 minuti.</span><span class="sxs-lookup"><span data-stu-id="4b204-208">The following code snippet creates an identity and corresponding :::no-loc(cookie)::: that lasts for 20 minutes.</span></span> <span data-ttu-id="4b204-209">Questa operazione ignora le impostazioni di scadenza scorrevoli configurate in precedenza.</span><span class="sxs-lookup"><span data-stu-id="4b204-209">This ignores any sliding expiration settings previously configured.</span></span>

```csharp
// using Microsoft.AspNetCore.Authentication;

await HttpContext.SignInAsync(
    :::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claims:::no-loc(Identity):::),
    new AuthenticationProperties
    {
        IsPersistent = true,
        ExpiresUtc = DateTime.UtcNow.AddMinutes(20)
    });
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="4b204-210">:::no-loc(ASP.NET Core Identity)::: è un provider di autenticazione completo e completo per la creazione e la gestione degli account di accesso.</span><span class="sxs-lookup"><span data-stu-id="4b204-210">:::no-loc(ASP.NET Core Identity)::: is a complete, full-featured authentication provider for creating and maintaining logins.</span></span> <span data-ttu-id="4b204-211">Tuttavia, un :::no-loc(cookie)::: provider di autenticazione basato su senza :::no-loc(ASP.NET Core Identity)::: può essere utilizzato.</span><span class="sxs-lookup"><span data-stu-id="4b204-211">However, a :::no-loc(cookie):::-based authentication provider without :::no-loc(ASP.NET Core Identity)::: can be used.</span></span> <span data-ttu-id="4b204-212">Per altre informazioni, vedere <xref:security/authentication/identity>.</span><span class="sxs-lookup"><span data-stu-id="4b204-212">For more information, see <xref:security/authentication/identity>.</span></span>

<span data-ttu-id="4b204-213">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/:::no-loc(cookie):::/samples) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="4b204-213">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/:::no-loc(cookie):::/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="4b204-214">A scopo dimostrativo nell'app di esempio, l'account utente per l'utente ipotetico, Maria Rodriguez, è hardcoded nell'app.</span><span class="sxs-lookup"><span data-stu-id="4b204-214">For demonstration purposes in the sample app, the user account for the hypothetical user, Maria Rodriguez, is hardcoded into the app.</span></span> <span data-ttu-id="4b204-215">Usare l'indirizzo di **posta elettronica** `maria.rodriguez@contoso.com` e qualsiasi password per accedere all'utente.</span><span class="sxs-lookup"><span data-stu-id="4b204-215">Use the **Email** address `maria.rodriguez@contoso.com` and any password to sign in the user.</span></span> <span data-ttu-id="4b204-216">L'utente viene autenticato nel `AuthenticateUser` metodo nel file *pages/account/login. cshtml. cs* .</span><span class="sxs-lookup"><span data-stu-id="4b204-216">The user is authenticated in the `AuthenticateUser` method in the *Pages/Account/Login.cshtml.cs* file.</span></span> <span data-ttu-id="4b204-217">In un esempio reale, l'utente viene autenticato a fronte di un database.</span><span class="sxs-lookup"><span data-stu-id="4b204-217">In a real-world example, the user would be authenticated against a database.</span></span>

## <a name="configuration"></a><span data-ttu-id="4b204-218">Configurazione</span><span class="sxs-lookup"><span data-stu-id="4b204-218">Configuration</span></span>

<span data-ttu-id="4b204-219">Se l'app non usa il [metapacchetto Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app), creare un riferimento al pacchetto nel file di progetto per [Microsoft. AspNetCore. Authentication. :::no-loc(Cookie)::: pacchetto s](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s/) .</span><span class="sxs-lookup"><span data-stu-id="4b204-219">If the app doesn't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference in the project file for the [Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s/) package.</span></span>

<span data-ttu-id="4b204-220">Nel `Startup.ConfigureServices` metodo creare il servizio middleware di autenticazione con i <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> metodi e <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Cookie):::Extensions.Add:::no-loc(Cookie):::*> :</span><span class="sxs-lookup"><span data-stu-id="4b204-220">In the `Startup.ConfigureServices` method, create the Authentication Middleware service with the <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> and <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Cookie):::Extensions.Add:::no-loc(Cookie):::*> methods:</span></span>

[!code-csharp[](:::no-loc(cookie):::/samples/2.x/:::no-loc(Cookie):::Sample/Startup.cs?name=snippet1)]

<span data-ttu-id="4b204-221"><xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme> passato a `AddAuthentication` imposta lo schema di autenticazione predefinito per l'app.</span><span class="sxs-lookup"><span data-stu-id="4b204-221"><xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme> passed to `AddAuthentication` sets the default authentication scheme for the app.</span></span> <span data-ttu-id="4b204-222">`AuthenticationScheme` è utile quando sono presenti più istanze di :::no-loc(cookie)::: autenticazione e si desidera [autorizzare con uno schema specifico](xref:security/authorization/limitingidentitybyscheme).</span><span class="sxs-lookup"><span data-stu-id="4b204-222">`AuthenticationScheme` is useful when there are multiple instances of :::no-loc(cookie)::: authentication and you want to [authorize with a specific scheme](xref:security/authorization/limitingidentitybyscheme).</span></span> <span data-ttu-id="4b204-223">L'impostazione `AuthenticationScheme` di su [ :::no-loc(Cookie)::: AuthenticationDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme) fornisce il valore " :::no-loc(Cookie)::: s" per lo schema.</span><span class="sxs-lookup"><span data-stu-id="4b204-223">Setting the `AuthenticationScheme` to [:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme) provides a value of ":::no-loc(Cookie):::s" for the scheme.</span></span> <span data-ttu-id="4b204-224">È possibile specificare qualsiasi valore stringa che distingua lo schema.</span><span class="sxs-lookup"><span data-stu-id="4b204-224">You can supply any string value that distinguishes the scheme.</span></span>

<span data-ttu-id="4b204-225">Lo schema di autenticazione dell'app è diverso dallo schema di :::no-loc(cookie)::: autenticazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="4b204-225">The app's authentication scheme is different from the app's :::no-loc(cookie)::: authentication scheme.</span></span> <span data-ttu-id="4b204-226">Quando uno :::no-loc(cookie)::: schema di autenticazione non viene fornito a <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Cookie):::Extensions.Add:::no-loc(Cookie):::*> , USA `:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme` (" :::no-loc(Cookie)::: s").</span><span class="sxs-lookup"><span data-stu-id="4b204-226">When a :::no-loc(cookie)::: authentication scheme isn't provided to <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Cookie):::Extensions.Add:::no-loc(Cookie):::*>, it uses `:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme` (":::no-loc(Cookie):::s").</span></span>

<span data-ttu-id="4b204-227">La :::no-loc(cookie)::: proprietà Authentication <xref:Microsoft.AspNetCore.Http.:::no-loc(Cookie):::Builder.IsEssential> è impostata su per `true` impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="4b204-227">The authentication :::no-loc(cookie):::'s <xref:Microsoft.AspNetCore.Http.:::no-loc(Cookie):::Builder.IsEssential> property is set to `true` by default.</span></span> <span data-ttu-id="4b204-228">:::no-loc(cookie):::Sono consentite le autenticazioni quando un visitatore del sito non ha acconsentito alla raccolta dei dati.</span><span class="sxs-lookup"><span data-stu-id="4b204-228">Authentication :::no-loc(cookie):::s are allowed when a site visitor hasn't consented to data collection.</span></span> <span data-ttu-id="4b204-229">Per altre informazioni, vedere <xref:security/gdpr#essential-:::no-loc(cookie):::s>.</span><span class="sxs-lookup"><span data-stu-id="4b204-229">For more information, see <xref:security/gdpr#essential-:::no-loc(cookie):::s>.</span></span>

<span data-ttu-id="4b204-230">Nel `Startup.Configure` metodo chiamare il `UseAuthentication` metodo per richiamare il middleware di autenticazione che imposta la `HttpContext.User` Proprietà.</span><span class="sxs-lookup"><span data-stu-id="4b204-230">In the `Startup.Configure` method, call the `UseAuthentication` method to invoke the Authentication Middleware that sets the `HttpContext.User` property.</span></span> <span data-ttu-id="4b204-231">Chiamare il `UseAuthentication` metodo prima di chiamare `UseMvcWithDefaultRoute` o `UseMvc` :</span><span class="sxs-lookup"><span data-stu-id="4b204-231">Call the `UseAuthentication` method before calling `UseMvcWithDefaultRoute` or `UseMvc`:</span></span>

[!code-csharp[](:::no-loc(cookie):::/samples/2.x/:::no-loc(Cookie):::Sample/Startup.cs?name=snippet2)]

<span data-ttu-id="4b204-232">La <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationOptions> classe viene utilizzata per configurare le opzioni del provider di autenticazione.</span><span class="sxs-lookup"><span data-stu-id="4b204-232">The <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationOptions> class is used to configure the authentication provider options.</span></span>

<span data-ttu-id="4b204-233">Impostare `:::no-loc(Cookie):::AuthenticationOptions` nella configurazione del servizio per l'autenticazione nel `Startup.ConfigureServices` Metodo:</span><span class="sxs-lookup"><span data-stu-id="4b204-233">Set `:::no-loc(Cookie):::AuthenticationOptions` in the service configuration for authentication in the `Startup.ConfigureServices` method:</span></span>

```csharp
services.AddAuthentication(:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme)
    .Add:::no-loc(Cookie):::(options =>
    {
        ...
    });
```

## <a name="no-loccookie-policy-middleware"></a><span data-ttu-id="4b204-234">:::no-loc(Cookie)::: Middleware dei criteri</span><span class="sxs-lookup"><span data-stu-id="4b204-234">:::no-loc(Cookie)::: Policy Middleware</span></span>

<span data-ttu-id="4b204-235">Il [ :::no-loc(Cookie)::: middleware dei criteri](xref:Microsoft.AspNetCore.:::no-loc(Cookie):::Policy.:::no-loc(Cookie):::PolicyMiddleware) Abilita le :::no-loc(cookie)::: funzionalità dei criteri.</span><span class="sxs-lookup"><span data-stu-id="4b204-235">[:::no-loc(Cookie)::: Policy Middleware](xref:Microsoft.AspNetCore.:::no-loc(Cookie):::Policy.:::no-loc(Cookie):::PolicyMiddleware) enables :::no-loc(cookie)::: policy capabilities.</span></span> <span data-ttu-id="4b204-236">L'aggiunta del middleware alla pipeline di elaborazione delle app è sensibile agli ordini &mdash; , ma interessa solo i componenti downstream registrati nella pipeline.</span><span class="sxs-lookup"><span data-stu-id="4b204-236">Adding the middleware to the app processing pipeline is order sensitive&mdash;it only affects downstream components registered in the pipeline.</span></span>

```csharp
app.Use:::no-loc(Cookie):::Policy(:::no-loc(cookie):::PolicyOptions);
```

<span data-ttu-id="4b204-237">Utilizzare <xref:Microsoft.AspNetCore.Builder.:::no-loc(Cookie):::PolicyOptions> fornito al :::no-loc(Cookie)::: middleware dei criteri per controllare le caratteristiche globali dell' :::no-loc(cookie)::: elaborazione e agganciare i :::no-loc(cookie)::: gestori di elaborazione quando :::no-loc(cookie)::: vengono aggiunti o eliminati.</span><span class="sxs-lookup"><span data-stu-id="4b204-237">Use <xref:Microsoft.AspNetCore.Builder.:::no-loc(Cookie):::PolicyOptions> provided to the :::no-loc(Cookie)::: Policy Middleware to control global characteristics of :::no-loc(cookie)::: processing and hook into :::no-loc(cookie)::: processing handlers when :::no-loc(cookie):::s are appended or deleted.</span></span>

<span data-ttu-id="4b204-238">Il <xref:Microsoft.AspNetCore.Builder.:::no-loc(Cookie):::PolicyOptions.MinimumSameSitePolicy> valore predefinito è `SameSiteMode.Lax` consentire l'autenticazione OAuth2.</span><span class="sxs-lookup"><span data-stu-id="4b204-238">The default <xref:Microsoft.AspNetCore.Builder.:::no-loc(Cookie):::PolicyOptions.MinimumSameSitePolicy> value is `SameSiteMode.Lax` to permit OAuth2 authentication.</span></span> <span data-ttu-id="4b204-239">Per applicare rigorosamente un criterio dello stesso sito di `SameSiteMode.Strict` , impostare il `MinimumSameSitePolicy` .</span><span class="sxs-lookup"><span data-stu-id="4b204-239">To strictly enforce a same-site policy of `SameSiteMode.Strict`, set the `MinimumSameSitePolicy`.</span></span> <span data-ttu-id="4b204-240">Sebbene questa impostazione interrompa OAuth2 e altri schemi di autenticazione tra le origini, eleva il livello di :::no-loc(cookie)::: sicurezza per altri tipi di app che non si basano sull'elaborazione di richieste tra origini.</span><span class="sxs-lookup"><span data-stu-id="4b204-240">Although this setting breaks OAuth2 and other cross-origin authentication schemes, it elevates the level of :::no-loc(cookie)::: security for other types of apps that don't rely on cross-origin request processing.</span></span>

```csharp
var :::no-loc(cookie):::PolicyOptions = new :::no-loc(Cookie):::PolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

<span data-ttu-id="4b204-241">L' :::no-loc(Cookie)::: impostazione del middleware per i criteri di `MinimumSameSitePolicy` può influire sull'impostazione di `:::no-loc(Cookie):::.SameSite` in `:::no-loc(Cookie):::AuthenticationOptions` impostazioni in base alla matrice riportata di seguito.</span><span class="sxs-lookup"><span data-stu-id="4b204-241">The :::no-loc(Cookie)::: Policy Middleware setting for `MinimumSameSitePolicy` can affect the setting of `:::no-loc(Cookie):::.SameSite` in `:::no-loc(Cookie):::AuthenticationOptions` settings according to the matrix below.</span></span>

| <span data-ttu-id="4b204-242">MinimumSameSitePolicy</span><span class="sxs-lookup"><span data-stu-id="4b204-242">MinimumSameSitePolicy</span></span> | <span data-ttu-id="4b204-243">:::no-loc(Cookie):::. Navigava sullostesso sito</span><span class="sxs-lookup"><span data-stu-id="4b204-243">:::no-loc(Cookie):::.SameSite</span></span> | <span data-ttu-id="4b204-244">Risultato :::no-loc(Cookie)::: . Impostazione navigava sullostesso sito</span><span class="sxs-lookup"><span data-stu-id="4b204-244">Resultant :::no-loc(Cookie):::.SameSite setting</span></span> |
| --------------------- | --------------- | --------------------------------- |
| <span data-ttu-id="4b204-245">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="4b204-245">SameSiteMode.None</span></span>     | <span data-ttu-id="4b204-246">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="4b204-246">SameSiteMode.None</span></span><br><span data-ttu-id="4b204-247">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="4b204-247">SameSiteMode.Lax</span></span><br><span data-ttu-id="4b204-248">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="4b204-248">SameSiteMode.Strict</span></span> | <span data-ttu-id="4b204-249">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="4b204-249">SameSiteMode.None</span></span><br><span data-ttu-id="4b204-250">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="4b204-250">SameSiteMode.Lax</span></span><br><span data-ttu-id="4b204-251">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="4b204-251">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="4b204-252">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="4b204-252">SameSiteMode.Lax</span></span>      | <span data-ttu-id="4b204-253">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="4b204-253">SameSiteMode.None</span></span><br><span data-ttu-id="4b204-254">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="4b204-254">SameSiteMode.Lax</span></span><br><span data-ttu-id="4b204-255">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="4b204-255">SameSiteMode.Strict</span></span> | <span data-ttu-id="4b204-256">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="4b204-256">SameSiteMode.Lax</span></span><br><span data-ttu-id="4b204-257">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="4b204-257">SameSiteMode.Lax</span></span><br><span data-ttu-id="4b204-258">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="4b204-258">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="4b204-259">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="4b204-259">SameSiteMode.Strict</span></span>   | <span data-ttu-id="4b204-260">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="4b204-260">SameSiteMode.None</span></span><br><span data-ttu-id="4b204-261">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="4b204-261">SameSiteMode.Lax</span></span><br><span data-ttu-id="4b204-262">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="4b204-262">SameSiteMode.Strict</span></span> | <span data-ttu-id="4b204-263">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="4b204-263">SameSiteMode.Strict</span></span><br><span data-ttu-id="4b204-264">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="4b204-264">SameSiteMode.Strict</span></span><br><span data-ttu-id="4b204-265">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="4b204-265">SameSiteMode.Strict</span></span> |

## <a name="create-an-authentication-no-loccookie"></a><span data-ttu-id="4b204-266">Creare un'autenticazione :::no-loc(cookie):::</span><span class="sxs-lookup"><span data-stu-id="4b204-266">Create an authentication :::no-loc(cookie):::</span></span>

<span data-ttu-id="4b204-267">Per creare un oggetto che :::no-loc(cookie)::: contiene informazioni sull'utente, costruire un oggetto <xref:System.Security.Claims.ClaimsPrincipal> .</span><span class="sxs-lookup"><span data-stu-id="4b204-267">To create a :::no-loc(cookie)::: holding user information, construct a <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="4b204-268">Le informazioni utente vengono serializzate e archiviate in :::no-loc(cookie)::: .</span><span class="sxs-lookup"><span data-stu-id="4b204-268">The user information is serialized and stored in the :::no-loc(cookie):::.</span></span> 

<span data-ttu-id="4b204-269">Creare una <xref:System.Security.Claims.Claims:::no-loc(Identity):::> con qualsiasi richiesta <xref:System.Security.Claims.Claim> e chiamare <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> per accedere all'utente:</span><span class="sxs-lookup"><span data-stu-id="4b204-269">Create a <xref:System.Security.Claims.Claims:::no-loc(Identity):::> with any required <xref:System.Security.Claims.Claim>s and call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> to sign in the user:</span></span>

[!code-csharp[](:::no-loc(cookie):::/samples/2.x/:::no-loc(Cookie):::Sample/Pages/Account/Login.cshtml.cs?name=snippet1)]

<span data-ttu-id="4b204-270">`SignInAsync` Crea un oggetto crittografato :::no-loc(cookie)::: e lo aggiunge alla risposta corrente.</span><span class="sxs-lookup"><span data-stu-id="4b204-270">`SignInAsync` creates an encrypted :::no-loc(cookie)::: and adds it to the current response.</span></span> <span data-ttu-id="4b204-271">Se `AuthenticationScheme` non è specificato, viene usato lo schema predefinito.</span><span class="sxs-lookup"><span data-stu-id="4b204-271">If `AuthenticationScheme` isn't specified, the default scheme is used.</span></span>

<span data-ttu-id="4b204-272">Per la crittografia viene usato il sistema di [protezione dei dati](xref:security/data-protection/using-data-protection) di ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="4b204-272">ASP.NET Core's [Data Protection](xref:security/data-protection/using-data-protection) system is used for encryption.</span></span> <span data-ttu-id="4b204-273">Per un'app ospitata in più computer, bilanciamento del carico tra app o uso di un Web farm, [configurare la protezione dei dati](xref:security/data-protection/configuration/overview) in modo da usare lo stesso anello chiave e l'identificatore dell'app.</span><span class="sxs-lookup"><span data-stu-id="4b204-273">For an app hosted on multiple machines, load balancing across apps, or using a web farm, [configure data protection](xref:security/data-protection/configuration/overview) to use the same key ring and app identifier.</span></span>

## <a name="sign-out"></a><span data-ttu-id="4b204-274">Disconnetti</span><span class="sxs-lookup"><span data-stu-id="4b204-274">Sign out</span></span>

<span data-ttu-id="4b204-275">Per disconnettersi dall'utente corrente ed eliminare il relativo :::no-loc(cookie)::: , chiamare <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*> :</span><span class="sxs-lookup"><span data-stu-id="4b204-275">To sign out the current user and delete their :::no-loc(cookie):::, call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>:</span></span>

[!code-csharp[](:::no-loc(cookie):::/samples/2.x/:::no-loc(Cookie):::Sample/Pages/Account/Login.cshtml.cs?name=snippet2)]

<span data-ttu-id="4b204-276">Se `:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme` (o " :::no-loc(Cookie)::: s") non viene usato come schema (ad esempio, "Contoso :::no-loc(Cookie)::: "), fornire lo schema usato durante la configurazione del provider di autenticazione.</span><span class="sxs-lookup"><span data-stu-id="4b204-276">If `:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme` (or ":::no-loc(Cookie):::s") isn't used as the scheme (for example, "Contoso:::no-loc(Cookie):::"), supply the scheme used when configuring the authentication provider.</span></span> <span data-ttu-id="4b204-277">In caso contrario, verrà utilizzato lo schema predefinito.</span><span class="sxs-lookup"><span data-stu-id="4b204-277">Otherwise, the default scheme is used.</span></span>

## <a name="react-to-back-end-changes"></a><span data-ttu-id="4b204-278">Reagire alle modifiche del back-end</span><span class="sxs-lookup"><span data-stu-id="4b204-278">React to back-end changes</span></span>

<span data-ttu-id="4b204-279">Una volta :::no-loc(cookie)::: creato un oggetto, :::no-loc(cookie)::: è l'unica origine di identità.</span><span class="sxs-lookup"><span data-stu-id="4b204-279">Once a :::no-loc(cookie)::: is created, the :::no-loc(cookie)::: is the single source of identity.</span></span> <span data-ttu-id="4b204-280">Se un account utente è disabilitato nei sistemi back-end:</span><span class="sxs-lookup"><span data-stu-id="4b204-280">If a user account is disabled in back-end systems:</span></span>

* <span data-ttu-id="4b204-281">Il sistema di :::no-loc(cookie)::: autenticazione dell'app continua a elaborare le richieste in base all'autenticazione :::no-loc(cookie)::: .</span><span class="sxs-lookup"><span data-stu-id="4b204-281">The app's :::no-loc(cookie)::: authentication system continues to process requests based on the authentication :::no-loc(cookie):::.</span></span>
* <span data-ttu-id="4b204-282">L'utente rimane connesso all'app fino a quando l'autenticazione :::no-loc(cookie)::: è valida.</span><span class="sxs-lookup"><span data-stu-id="4b204-282">The user remains signed into the app as long as the authentication :::no-loc(cookie)::: is valid.</span></span>

<span data-ttu-id="4b204-283">L' <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationEvents.ValidatePrincipal*> evento può essere usato per intercettare ed eseguire l'override della convalida dell' :::no-loc(cookie)::: identità.</span><span class="sxs-lookup"><span data-stu-id="4b204-283">The <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationEvents.ValidatePrincipal*> event can be used to intercept and override validation of the :::no-loc(cookie)::: identity.</span></span> <span data-ttu-id="4b204-284">La convalida :::no-loc(cookie)::: di a ogni richiesta attenua il rischio di revoca degli utenti che accedono all'app.</span><span class="sxs-lookup"><span data-stu-id="4b204-284">Validating the :::no-loc(cookie)::: on every request mitigates the risk of revoked users accessing the app.</span></span>

<span data-ttu-id="4b204-285">Un approccio alla :::no-loc(cookie)::: convalida si basa sul tenere traccia del momento in cui il database utente viene modificato.</span><span class="sxs-lookup"><span data-stu-id="4b204-285">One approach to :::no-loc(cookie)::: validation is based on keeping track of when the user database changes.</span></span> <span data-ttu-id="4b204-286">Se il database non è stato modificato dopo l'emissione dell'utente :::no-loc(cookie)::: , non è necessario autenticare di nuovo l'utente se :::no-loc(cookie)::: è ancora valido.</span><span class="sxs-lookup"><span data-stu-id="4b204-286">If the database hasn't been changed since the user's :::no-loc(cookie)::: was issued, there's no need to re-authenticate the user if their :::no-loc(cookie)::: is still valid.</span></span> <span data-ttu-id="4b204-287">Nell'app di esempio, il database viene implementato in `IUserRepository` e archivia un `LastChanged` valore.</span><span class="sxs-lookup"><span data-stu-id="4b204-287">In the sample app, the database is implemented in `IUserRepository` and stores a `LastChanged` value.</span></span> <span data-ttu-id="4b204-288">Quando un utente viene aggiornato nel database, il `LastChanged` valore viene impostato sull'ora corrente.</span><span class="sxs-lookup"><span data-stu-id="4b204-288">When a user is updated in the database, the `LastChanged` value is set to the current time.</span></span>

<span data-ttu-id="4b204-289">Per invalidare un oggetto :::no-loc(cookie)::: quando il database viene modificato in base al `LastChanged` valore, creare :::no-loc(cookie)::: con un' `LastChanged` attestazione contenente il `LastChanged` valore corrente dal database:</span><span class="sxs-lookup"><span data-stu-id="4b204-289">In order to invalidate a :::no-loc(cookie)::: when the database changes based on the `LastChanged` value, create the :::no-loc(cookie)::: with a `LastChanged` claim containing the current `LastChanged` value from the database:</span></span>

```csharp
var claims = new List<Claim>
{
    new Claim(ClaimTypes.Name, user.Email),
    new Claim("LastChanged", {Database Value})
};

var claims:::no-loc(Identity)::: = new Claims:::no-loc(Identity):::(
    claims, 
    :::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme);

await HttpContext.SignInAsync(
    :::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme, 
    new ClaimsPrincipal(claims:::no-loc(Identity):::));
```

<span data-ttu-id="4b204-290">Per implementare una sostituzione per l' `ValidatePrincipal` evento, scrivere un metodo con la firma seguente in una classe che deriva da <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationEvents> :</span><span class="sxs-lookup"><span data-stu-id="4b204-290">To implement an override for the `ValidatePrincipal` event, write a method with the following signature in a class that derives from <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationEvents>:</span></span>

```csharp
ValidatePrincipal(:::no-loc(Cookie):::ValidatePrincipalContext)
```

<span data-ttu-id="4b204-291">Di seguito è riportato un esempio di implementazione di `:::no-loc(Cookie):::AuthenticationEvents` :</span><span class="sxs-lookup"><span data-stu-id="4b204-291">The following is an example implementation of `:::no-loc(Cookie):::AuthenticationEvents`:</span></span>

```csharp
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Authentication;
using Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s;

public class Custom:::no-loc(Cookie):::AuthenticationEvents : :::no-loc(Cookie):::AuthenticationEvents
{
    private readonly IUserRepository _userRepository;

    public Custom:::no-loc(Cookie):::AuthenticationEvents(IUserRepository userRepository)
    {
        // Get the database from registered DI services.
        _userRepository = userRepository;
    }

    public override async Task ValidatePrincipal(:::no-loc(Cookie):::ValidatePrincipalContext context)
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
                :::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme);
        }
    }
}
```

<span data-ttu-id="4b204-292">Registrare l'istanza degli eventi durante :::no-loc(cookie)::: la registrazione del servizio nel `Startup.ConfigureServices` metodo.</span><span class="sxs-lookup"><span data-stu-id="4b204-292">Register the events instance during :::no-loc(cookie)::: service registration in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="4b204-293">Fornire una [registrazione del servizio con ambito](xref:fundamentals/dependency-injection#service-lifetimes) per la `Custom:::no-loc(Cookie):::AuthenticationEvents` classe:</span><span class="sxs-lookup"><span data-stu-id="4b204-293">Provide a [scoped service registration](xref:fundamentals/dependency-injection#service-lifetimes) for your `Custom:::no-loc(Cookie):::AuthenticationEvents` class:</span></span>

```csharp
services.AddAuthentication(:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme)
    .Add:::no-loc(Cookie):::(options =>
    {
        options.EventsType = typeof(Custom:::no-loc(Cookie):::AuthenticationEvents);
    });

services.AddScoped<Custom:::no-loc(Cookie):::AuthenticationEvents>();
```

<span data-ttu-id="4b204-294">Si consideri una situazione in cui il nome dell'utente viene aggiornato &mdash; una decisione che non influisce sulla sicurezza in alcun modo.</span><span class="sxs-lookup"><span data-stu-id="4b204-294">Consider a situation in which the user's name is updated&mdash;a decision that doesn't affect security in any way.</span></span> <span data-ttu-id="4b204-295">Se si desidera aggiornare l'entità utente in modo non distruttivo, chiamare `context.ReplacePrincipal` e impostare la `context.ShouldRenew` proprietà su `true` .</span><span class="sxs-lookup"><span data-stu-id="4b204-295">If you want to non-destructively update the user principal, call `context.ReplacePrincipal` and set the `context.ShouldRenew` property to `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="4b204-296">L'approccio descritto qui viene attivato a ogni richiesta.</span><span class="sxs-lookup"><span data-stu-id="4b204-296">The approach described here is triggered on every request.</span></span> <span data-ttu-id="4b204-297">La convalida :::no-loc(cookie)::: dell'autenticazione per tutti gli utenti in ogni richiesta può comportare un notevole calo delle prestazioni per l'app.</span><span class="sxs-lookup"><span data-stu-id="4b204-297">Validating authentication :::no-loc(cookie):::s for all users on every request can result in a large performance penalty for the app.</span></span>

## <a name="persistent-no-loccookies"></a><span data-ttu-id="4b204-298">S persistente :::no-loc(cookie):::</span><span class="sxs-lookup"><span data-stu-id="4b204-298">Persistent :::no-loc(cookie):::s</span></span>

<span data-ttu-id="4b204-299">È possibile che il venga :::no-loc(cookie)::: mantenuto tra le sessioni del browser.</span><span class="sxs-lookup"><span data-stu-id="4b204-299">You may want the :::no-loc(cookie)::: to persist across browser sessions.</span></span> <span data-ttu-id="4b204-300">Questa persistenza deve essere abilitata solo con il consenso esplicito dell'utente con una casella di controllo "Remember me" all'accesso o un meccanismo simile.</span><span class="sxs-lookup"><span data-stu-id="4b204-300">This persistence should only be enabled with explicit user consent with a "Remember Me" check box on sign in or a similar mechanism.</span></span> 

<span data-ttu-id="4b204-301">Il frammento di codice seguente crea un'identità e corrispondente che rimane inalterata :::no-loc(cookie)::: attraverso le chiusure del browser.</span><span class="sxs-lookup"><span data-stu-id="4b204-301">The following code snippet creates an identity and corresponding :::no-loc(cookie)::: that survives through browser closures.</span></span> <span data-ttu-id="4b204-302">Vengono rispettate tutte le impostazioni di scadenza scorrevoli configurate in precedenza.</span><span class="sxs-lookup"><span data-stu-id="4b204-302">Any sliding expiration settings previously configured are honored.</span></span> <span data-ttu-id="4b204-303">Se l'oggetto :::no-loc(cookie)::: scade quando il browser viene chiuso, il browser cancella il oggetto :::no-loc(cookie)::: dopo il riavvio.</span><span class="sxs-lookup"><span data-stu-id="4b204-303">If the :::no-loc(cookie)::: expires while the browser is closed, the browser clears the :::no-loc(cookie)::: once it's restarted.</span></span>

<span data-ttu-id="4b204-304">Imposta <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> su `true` in <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties> :</span><span class="sxs-lookup"><span data-stu-id="4b204-304">Set <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> to `true` in <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>:</span></span>

```csharp
// using Microsoft.AspNetCore.Authentication;

await HttpContext.SignInAsync(
    :::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claims:::no-loc(Identity):::),
    new AuthenticationProperties
    {
        IsPersistent = true
    });
```

## <a name="absolute-no-loccookie-expiration"></a><span data-ttu-id="4b204-305">:::no-loc(cookie):::Scadenza assoluta</span><span class="sxs-lookup"><span data-stu-id="4b204-305">Absolute :::no-loc(cookie)::: expiration</span></span>

<span data-ttu-id="4b204-306">È possibile impostare un'ora di scadenza assoluta con <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc> .</span><span class="sxs-lookup"><span data-stu-id="4b204-306">An absolute expiration time can be set with <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>.</span></span> <span data-ttu-id="4b204-307">Per creare un oggetto persistente :::no-loc(cookie)::: , `IsPersistent` è necessario impostare anche.</span><span class="sxs-lookup"><span data-stu-id="4b204-307">To create a persistent :::no-loc(cookie):::, `IsPersistent` must also be set.</span></span> <span data-ttu-id="4b204-308">In caso contrario, :::no-loc(cookie)::: viene creato con una durata basata sulla sessione e potrebbe scadere prima o dopo il ticket di autenticazione che possiede.</span><span class="sxs-lookup"><span data-stu-id="4b204-308">Otherwise, the :::no-loc(cookie)::: is created with a session-based lifetime and could expire either before or after the authentication ticket that it holds.</span></span> <span data-ttu-id="4b204-309">Quando `ExpiresUtc` è impostato, esegue l'override del valore dell' <xref:Microsoft.AspNetCore.Builder.:::no-loc(Cookie):::AuthenticationOptions.ExpireTimeSpan> opzione di <xref:Microsoft.AspNetCore.Builder.:::no-loc(Cookie):::AuthenticationOptions> , se impostata.</span><span class="sxs-lookup"><span data-stu-id="4b204-309">When `ExpiresUtc` is set, it overrides the value of the <xref:Microsoft.AspNetCore.Builder.:::no-loc(Cookie):::AuthenticationOptions.ExpireTimeSpan> option of <xref:Microsoft.AspNetCore.Builder.:::no-loc(Cookie):::AuthenticationOptions>, if set.</span></span>

<span data-ttu-id="4b204-310">Il frammento di codice seguente crea un'identità e corrisponde alla :::no-loc(cookie)::: durata di 20 minuti.</span><span class="sxs-lookup"><span data-stu-id="4b204-310">The following code snippet creates an identity and corresponding :::no-loc(cookie)::: that lasts for 20 minutes.</span></span> <span data-ttu-id="4b204-311">Questa operazione ignora le impostazioni di scadenza scorrevoli configurate in precedenza.</span><span class="sxs-lookup"><span data-stu-id="4b204-311">This ignores any sliding expiration settings previously configured.</span></span>

```csharp
// using Microsoft.AspNetCore.Authentication;

await HttpContext.SignInAsync(
    :::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claims:::no-loc(Identity):::),
    new AuthenticationProperties
    {
        IsPersistent = true,
        ExpiresUtc = DateTime.UtcNow.AddMinutes(20)
    });
```

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="4b204-312">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="4b204-312">Additional resources</span></span>

* <xref:security/authorization/limitingidentitybyscheme>
* <xref:security/authorization/claims>
* [<span data-ttu-id="4b204-313">Controlli dei ruoli basati su criteri</span><span class="sxs-lookup"><span data-stu-id="4b204-313">Policy-based role checks</span></span>](xref:security/authorization/roles#policy-based-role-checks)
* <xref:host-and-deploy/web-farm>
