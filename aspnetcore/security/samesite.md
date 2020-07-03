---
title: Usare i cookie navigava sullostesso sito in ASP.NET Core
author: rick-anderson
description: Informazioni su come usare per navigava sullostesso sito i cookie in ASP.NET Core
ms.author: riande
ms.custom: mvc
ms.date: 12/03/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
- Electron
uid: security/samesite
ms.openlocfilehash: 3e3c12e17de3e12ead15c405e9339761a3f2f711
ms.sourcegitcommit: 66fca14611eba141d455fe0bd2c37803062e439c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/03/2020
ms.locfileid: "85944276"
---
# <a name="work-with-samesite-cookies-in-aspnet-core"></a><span data-ttu-id="cc2ec-103">Usare i cookie navigava sullostesso sito in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="cc2ec-103">Work with SameSite cookies in ASP.NET Core</span></span>

<span data-ttu-id="cc2ec-104">Autore: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="cc2ec-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="cc2ec-105">Navigava sullostesso sito è uno standard [IETF](https://ietf.org/about/) Draft progettato per garantire una protezione contro gli attacchi di richiesta intersito falsificazione (CSRF).</span><span class="sxs-lookup"><span data-stu-id="cc2ec-105">SameSite is an [IETF](https://ietf.org/about/) draft standard designed to provide some protection against cross-site request forgery (CSRF) attacks.</span></span> <span data-ttu-id="cc2ec-106">Originariamente redatto in [2016](https://tools.ietf.org/html/draft-west-first-party-cookies-07), il Draft standard è stato aggiornato in [2019](https://tools.ietf.org/html/draft-west-cookie-incrementalism-00).</span><span class="sxs-lookup"><span data-stu-id="cc2ec-106">Originally drafted in [2016](https://tools.ietf.org/html/draft-west-first-party-cookies-07), the draft standard was updated in [2019](https://tools.ietf.org/html/draft-west-cookie-incrementalism-00).</span></span> <span data-ttu-id="cc2ec-107">Lo standard aggiornato non è compatibile con le versioni precedenti dello standard precedente, con le differenze più evidenti tra quelle riportate di seguito:</span><span class="sxs-lookup"><span data-stu-id="cc2ec-107">The updated standard is not backward compatible with the previous standard, with the following being the most noticeable differences:</span></span>

* <span data-ttu-id="cc2ec-108">I cookie senza intestazione navigava sullostesso sito vengono considerati come `SameSite=Lax` per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="cc2ec-108">Cookies without SameSite header are treated as `SameSite=Lax` by default.</span></span>
* <span data-ttu-id="cc2ec-109">`SameSite=None`deve essere usato per consentire l'uso di cookie tra siti.</span><span class="sxs-lookup"><span data-stu-id="cc2ec-109">`SameSite=None` must be used to allow cross-site cookie use.</span></span>
* <span data-ttu-id="cc2ec-110">I cookie che asseriscono `SameSite=None` anche devono essere contrassegnati come `Secure` .</span><span class="sxs-lookup"><span data-stu-id="cc2ec-110">Cookies that assert `SameSite=None` must also be marked as `Secure`.</span></span>
* <span data-ttu-id="cc2ec-111">Le applicazioni che utilizzano [`<iframe>`](https://developer.mozilla.org/docs/Web/HTML/Element/iframe) potrebbero riscontrare problemi con i `sameSite=Lax` cookie di o `sameSite=Strict` perché `<iframe>` vengono considerati scenari tra siti.</span><span class="sxs-lookup"><span data-stu-id="cc2ec-111">Applications that use [`<iframe>`](https://developer.mozilla.org/docs/Web/HTML/Element/iframe) may experience issues with `sameSite=Lax` or `sameSite=Strict` cookies because `<iframe>` is treated as cross-site scenarios.</span></span>
* <span data-ttu-id="cc2ec-112">Il valore `SameSite=None` non è consentito dallo [standard 2016](https://tools.ietf.org/html/draft-west-first-party-cookies-07) e fa in modo che alcune implementazioni considerino tali cookie come `SameSite=Strict` .</span><span class="sxs-lookup"><span data-stu-id="cc2ec-112">The value `SameSite=None` is not allowed by the [2016 standard](https://tools.ietf.org/html/draft-west-first-party-cookies-07) and causes some implementations to treat such cookies as `SameSite=Strict`.</span></span> <span data-ttu-id="cc2ec-113">Vedere [supporto di browser meno recenti](#sob) in questo documento.</span><span class="sxs-lookup"><span data-stu-id="cc2ec-113">See [Supporting older browsers](#sob) in this document.</span></span>

<span data-ttu-id="cc2ec-114">L' `SameSite=Lax` impostazione funziona per la maggior parte dei cookie dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="cc2ec-114">The `SameSite=Lax` setting works for most application cookies.</span></span> <span data-ttu-id="cc2ec-115">Alcune forme di autenticazione come [OpenID Connect](https://openid.net/connect/) (OIDC) e l'impostazione predefinita di [WS-Federation](https://auth0.com/docs/protocols/ws-fed) sono i reindirizzamenti basati su post.</span><span class="sxs-lookup"><span data-stu-id="cc2ec-115">Some forms of authentication like [OpenID Connect](https://openid.net/connect/) (OIDC) and [WS-Federation](https://auth0.com/docs/protocols/ws-fed) default to POST based redirects.</span></span> <span data-ttu-id="cc2ec-116">I reindirizzamenti basati su POST attivano le protezioni del browser navigava sullostesso sito, pertanto navigava sullostesso sito è disabilitato per questi componenti.</span><span class="sxs-lookup"><span data-stu-id="cc2ec-116">The POST based redirects trigger the SameSite browser protections, so SameSite is disabled for these components.</span></span> <span data-ttu-id="cc2ec-117">La maggior parte degli accessi [OAuth](https://oauth.net/) non è interessata a causa di differenze nel modo in cui la richiesta fluisce.</span><span class="sxs-lookup"><span data-stu-id="cc2ec-117">Most [OAuth](https://oauth.net/) logins are not affected due to differences in how the request flows.</span></span>

<span data-ttu-id="cc2ec-118">Ogni componente ASP.NET Core che emette cookie deve decidere se navigava sullostesso sito è appropriato.</span><span class="sxs-lookup"><span data-stu-id="cc2ec-118">Each ASP.NET Core component that emits cookies needs to decide if SameSite is appropriate.</span></span>

## <a name="samesite-and-identity"></a><span data-ttu-id="cc2ec-119">Navigava sullostesso sito eIdentity</span><span class="sxs-lookup"><span data-stu-id="cc2ec-119">SameSite and Identity</span></span>

[!INCLUDE[](~/includes/SameSiteIdentity.md)]

## <a name="samesite-test-sample-code"></a><span data-ttu-id="cc2ec-120">Codice di esempio di test di navigava sullostesso sito</span><span class="sxs-lookup"><span data-stu-id="cc2ec-120">SameSite test sample code</span></span>

 ::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"

<span data-ttu-id="cc2ec-121">È possibile scaricare e testare gli esempi seguenti:</span><span class="sxs-lookup"><span data-stu-id="cc2ec-121">The following samples can be downloaded and tested:</span></span>

| <span data-ttu-id="cc2ec-122">Esempio</span><span class="sxs-lookup"><span data-stu-id="cc2ec-122">Sample</span></span>               | <span data-ttu-id="cc2ec-123">Document</span><span class="sxs-lookup"><span data-stu-id="cc2ec-123">Document</span></span> |
| ----------------- | ------------ |
| [<span data-ttu-id="cc2ec-124">MVC .NET Core</span><span class="sxs-lookup"><span data-stu-id="cc2ec-124">.NET Core MVC</span></span>](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21MVC)  | <xref:security/samesite/mvc21> |
| <span data-ttu-id="cc2ec-125">[Pagine di .NET Core Razor](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21RazorPages)</span><span class="sxs-lookup"><span data-stu-id="cc2ec-125">[.NET Core Razor Pages](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21RazorPages)</span></span>  | <xref:security/samesite/rp21> |

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="cc2ec-126">È possibile scaricare e testare l'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="cc2ec-126">The following sample can be downloaded and tested:</span></span>


| <span data-ttu-id="cc2ec-127">Esempio</span><span class="sxs-lookup"><span data-stu-id="cc2ec-127">Sample</span></span>               | <span data-ttu-id="cc2ec-128">Document</span><span class="sxs-lookup"><span data-stu-id="cc2ec-128">Document</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="cc2ec-129">[Pagine di .NET Core Razor](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore31RazorPages)</span><span class="sxs-lookup"><span data-stu-id="cc2ec-129">[.NET Core Razor Pages](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore31RazorPages)</span></span>  | <xref:security/samesite/rp31> |

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

## <a name="net-core-support-for-the-samesite-attribute"></a><span data-ttu-id="cc2ec-130">Supporto di .NET Core per l'attributo navigava sullostesso sito</span><span class="sxs-lookup"><span data-stu-id="cc2ec-130">.NET Core support for the sameSite attribute</span></span>

<span data-ttu-id="cc2ec-131">.NET Core 2,2 supporta lo standard 2019 Draft per navigava sullostesso sito dal rilascio degli aggiornamenti nel 2019 dicembre.</span><span class="sxs-lookup"><span data-stu-id="cc2ec-131">.NET Core 2.2 supports the 2019 draft standard for SameSite since the release of updates in December 2019.</span></span> <span data-ttu-id="cc2ec-132">Gli sviluppatori sono in grado di controllare a livello di codice il valore dell'attributo navigava sullostesso sito usando la `HttpCookie.SameSite` Proprietà.</span><span class="sxs-lookup"><span data-stu-id="cc2ec-132">Developers are able to programmatically control the value of the sameSite attribute using the `HttpCookie.SameSite` property.</span></span> <span data-ttu-id="cc2ec-133">Impostando la `SameSite` proprietà su Strict, LAX o None, i valori vengono scritti in rete con il cookie.</span><span class="sxs-lookup"><span data-stu-id="cc2ec-133">Setting the `SameSite` property to Strict, Lax, or None results in those values being written on the network with the cookie.</span></span> <span data-ttu-id="cc2ec-134">L'impostazione di un valore uguale a (SameSiteMode) (-1) indica che non è necessario includere nella rete alcun attributo navigava sullostesso sito con il cookie</span><span class="sxs-lookup"><span data-stu-id="cc2ec-134">Setting it equal to (SameSiteMode)(-1) indicates that no sameSite attribute should be included on the network with the cookie</span></span>

[!code-csharp[](samesite/snippets/Privacy.cshtml.cs?name=snippet)]

<span data-ttu-id="cc2ec-135">.NET Core 3,0 supporta i valori navigava sullostesso sito aggiornati e aggiunge un valore enum aggiuntivo `SameSiteMode.Unspecified` all' `SameSiteMode` enumerazione.</span><span class="sxs-lookup"><span data-stu-id="cc2ec-135">.NET Core 3.0 supports the updated SameSite values and adds an extra enum value, `SameSiteMode.Unspecified` to the `SameSiteMode` enum.</span></span>
<span data-ttu-id="cc2ec-136">Questo nuovo valore indica che non è necessario inviare navigava sullostesso sito con il cookie.</span><span class="sxs-lookup"><span data-stu-id="cc2ec-136">This new value indicates no sameSite should be sent with the cookie.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

## <a name="december-patch-behavior-changes"></a><span data-ttu-id="cc2ec-137">Modifiche al comportamento della patch di dicembre</span><span class="sxs-lookup"><span data-stu-id="cc2ec-137">December patch behavior changes</span></span>

<span data-ttu-id="cc2ec-138">La modifica del comportamento specifico per .NET Framework e .NET Core 2,1 è il modo in cui la `SameSite` Proprietà interpreta il `None` valore.</span><span class="sxs-lookup"><span data-stu-id="cc2ec-138">The specific behavior change for .NET Framework and .NET Core 2.1 is how the `SameSite` property interprets the `None` value.</span></span> <span data-ttu-id="cc2ec-139">Prima della patch, il valore `None` significava "non emettere l'attributo", dopo la patch significa "creare l'attributo con un valore di `None` ".</span><span class="sxs-lookup"><span data-stu-id="cc2ec-139">Before the patch a value of `None` meant "Do not emit the attribute at all", after the patch it means "Emit the attribute with a value of `None`".</span></span> <span data-ttu-id="cc2ec-140">Dopo la patch `SameSite` , un valore di `(SameSiteMode)(-1)` causa la mancata creazione dell'attributo.</span><span class="sxs-lookup"><span data-stu-id="cc2ec-140">After the patch a `SameSite` value of `(SameSiteMode)(-1)` causes the attribute not to be emitted.</span></span>

<span data-ttu-id="cc2ec-141">Il valore predefinito di navigava sullostesso sito per l'autenticazione basata su form e i cookie di stato della sessione è stato modificato da `None` a `Lax` .</span><span class="sxs-lookup"><span data-stu-id="cc2ec-141">The default SameSite value for forms authentication and session state cookies was changed from `None` to `Lax`.</span></span>

::: moniker-end

## <a name="api-usage-with-samesite"></a><span data-ttu-id="cc2ec-142">Utilizzo delle API con navigava sullostesso sito</span><span class="sxs-lookup"><span data-stu-id="cc2ec-142">API usage with SameSite</span></span>

<span data-ttu-id="cc2ec-143">[HttpContext. Response. cookies. Append](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*) viene impostato sul valore predefinito `Unspecified` , ovvero nessun attributo navigava sullostesso sito aggiunto al cookie e il client utilizzerà il comportamento predefinito (LAX per i nuovi browser, None per quelli precedenti).</span><span class="sxs-lookup"><span data-stu-id="cc2ec-143">[HttpContext.Response.Cookies.Append](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*) defaults to `Unspecified`, meaning no SameSite attribute added to the cookie and the client will use its default behavior (Lax for new browsers, None for old ones).</span></span> <span data-ttu-id="cc2ec-144">Il codice seguente illustra come modificare il valore di navigava sullostesso sito del cookie in `SameSiteMode.Lax` :</span><span class="sxs-lookup"><span data-stu-id="cc2ec-144">The following code shows how to change the cookie SameSite value to `SameSiteMode.Lax`:</span></span>

[!code-csharp[](samesite/sample/Pages/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="cc2ec-145">Tutti i componenti ASP.NET Core che emettono cookie sostituiscono i valori predefiniti precedenti con le impostazioni appropriate per gli scenari.</span><span class="sxs-lookup"><span data-stu-id="cc2ec-145">All ASP.NET Core components that emit cookies override the preceding defaults with settings appropriate for their scenarios.</span></span> <span data-ttu-id="cc2ec-146">L'override dei valori predefiniti precedenti non è stato modificato.</span><span class="sxs-lookup"><span data-stu-id="cc2ec-146">The overridden preceding default values haven't changed.</span></span>

| <span data-ttu-id="cc2ec-147">Componente</span><span class="sxs-lookup"><span data-stu-id="cc2ec-147">Component</span></span> | <span data-ttu-id="cc2ec-148">cookie</span><span class="sxs-lookup"><span data-stu-id="cc2ec-148">cookie</span></span> | <span data-ttu-id="cc2ec-149">Predefinito</span><span class="sxs-lookup"><span data-stu-id="cc2ec-149">Default</span></span> |
| ------------- | ------------- |
| <xref:Microsoft.AspNetCore.Http.CookieBuilder> | <xref:Microsoft.AspNetCore.Http.CookieBuilder.SameSite> | `Unspecified` |
| <xref:Microsoft.AspNetCore.Http.HttpContext.Session>  | [<span data-ttu-id="cc2ec-150">SessionOptions. cookie</span><span class="sxs-lookup"><span data-stu-id="cc2ec-150">SessionOptions.Cookie</span></span>](xref:Microsoft.AspNetCore.Builder.SessionOptions.Cookie) |`Lax` |
| <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.CookieTempDataProvider>  | [<span data-ttu-id="cc2ec-151">CookieTempDataProviderOptions. cookie</span><span class="sxs-lookup"><span data-stu-id="cc2ec-151">CookieTempDataProviderOptions.Cookie</span></span>](xref:Microsoft.AspNetCore.Mvc.CookieTempDataProviderOptions.Cookie) | `Lax` |
| <xref:Microsoft.AspNetCore.Antiforgery.IAntiforgery> | [<span data-ttu-id="cc2ec-152">AntiforgeryOptions. cookie</span><span class="sxs-lookup"><span data-stu-id="cc2ec-152">AntiforgeryOptions.Cookie</span></span>](xref:Microsoft.AspNetCore.Antiforgery.AntiforgeryOptions.Cookie)| `Strict` |
| [<span data-ttu-id="cc2ec-153">Autenticazione cookie</span><span class="sxs-lookup"><span data-stu-id="cc2ec-153">Cookie Authentication</span></span>](xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*) | [<span data-ttu-id="cc2ec-154">CookieAuthenticationOptions. cookie</span><span class="sxs-lookup"><span data-stu-id="cc2ec-154">CookieAuthenticationOptions.Cookie</span></span>](xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.CookieName) | `Lax` |
| <xref:Microsoft.Extensions.DependencyInjection.TwitterExtensions.AddTwitter*> | [<span data-ttu-id="cc2ec-155">TwitterOptions.StateCookie</span><span class="sxs-lookup"><span data-stu-id="cc2ec-155">TwitterOptions.StateCookie </span></span>](xref:Microsoft.AspNetCore.Authentication.Twitter.TwitterOptions.StateCookie) | `Lax`  |
| <span data-ttu-id="cc2ec-156"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationHandler`1> | [RemoteAuthenticationOptions.CorrelationCookie](xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.CorrelationCookie)  | `None`</span><span class="sxs-lookup"><span data-stu-id="cc2ec-156"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationHandler`1> | [RemoteAuthenticationOptions.CorrelationCookie](xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.CorrelationCookie)  | `None`</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.OpenIdConnectExtensions.AddOpenIdConnect*> | [<span data-ttu-id="cc2ec-157">OpenIdConnectOptions.NonceCookie</span><span class="sxs-lookup"><span data-stu-id="cc2ec-157">OpenIdConnectOptions.NonceCookie</span></span>](xref:Microsoft.AspNetCore.Authentication.OpenIdConnect.OpenIdConnectOptions.NonceCookie)| `None` |
| [<span data-ttu-id="cc2ec-158">HttpContext. Response. cookies. Append</span><span class="sxs-lookup"><span data-stu-id="cc2ec-158">HttpContext.Response.Cookies.Append</span></span>](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*) | <xref:Microsoft.AspNetCore.Http.CookieOptions> | `Unspecified` |

::: moniker range=">= aspnetcore-3.1"

<span data-ttu-id="cc2ec-159">ASP.NET Core 3,1 e versioni successive fornisce il supporto navigava sullostesso sito seguente:</span><span class="sxs-lookup"><span data-stu-id="cc2ec-159">ASP.NET Core 3.1 and later provides the following SameSite support:</span></span>

* <span data-ttu-id="cc2ec-160">Ridefinisce il comportamento di `SameSiteMode.None` da emettere`SameSite=None`</span><span class="sxs-lookup"><span data-stu-id="cc2ec-160">Redefines the behavior of `SameSiteMode.None` to emit `SameSite=None`</span></span>
* <span data-ttu-id="cc2ec-161">Aggiunge un nuovo valore `SameSiteMode.Unspecified` per omettere l'attributo navigava sullostesso sito.</span><span class="sxs-lookup"><span data-stu-id="cc2ec-161">Adds a new value `SameSiteMode.Unspecified` to omit the SameSite attribute.</span></span>
* <span data-ttu-id="cc2ec-162">Per impostazione predefinita, tutte le API cookie `Unspecified` .</span><span class="sxs-lookup"><span data-stu-id="cc2ec-162">All cookies APIs default to `Unspecified`.</span></span> <span data-ttu-id="cc2ec-163">Alcuni componenti che usano cookie impostano valori più specifici per gli scenari.</span><span class="sxs-lookup"><span data-stu-id="cc2ec-163">Some components that use cookies set values more specific to their scenarios.</span></span> <span data-ttu-id="cc2ec-164">Vedere la tabella precedente per gli esempi.</span><span class="sxs-lookup"><span data-stu-id="cc2ec-164">See the table above for examples.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="cc2ec-165">In ASP.NET Core 3,0 e versioni successive i valori predefiniti di navigava sullostesso sito sono stati modificati per evitare conflitti con impostazioni predefinite client incoerenti.</span><span class="sxs-lookup"><span data-stu-id="cc2ec-165">In ASP.NET Core 3.0 and later the SameSite defaults were changed to avoid conflicting with inconsistent client defaults.</span></span> <span data-ttu-id="cc2ec-166">Le API seguenti hanno modificato il valore predefinito da `SameSiteMode.Lax ` a `-1` per evitare la creazione di un attributo navigava sullostesso sito per questi cookie:</span><span class="sxs-lookup"><span data-stu-id="cc2ec-166">The following APIs have changed the default from `SameSiteMode.Lax ` to `-1` to avoid emitting a SameSite attribute for these cookies:</span></span>

* <span data-ttu-id="cc2ec-167"><xref:Microsoft.AspNetCore.Http.CookieOptions>usato con [HttpContext. Response. cookies. Append](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*)</span><span class="sxs-lookup"><span data-stu-id="cc2ec-167"><xref:Microsoft.AspNetCore.Http.CookieOptions> used with [HttpContext.Response.Cookies.Append](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*)</span></span>
* <span data-ttu-id="cc2ec-168"><xref:Microsoft.AspNetCore.Http.CookieBuilder>usato come factory per`CookieOptions`</span><span class="sxs-lookup"><span data-stu-id="cc2ec-168"><xref:Microsoft.AspNetCore.Http.CookieBuilder>  used as a factory for `CookieOptions`</span></span>
* [<span data-ttu-id="cc2ec-169">CookiePolicyOptions.MinimumSameSitePolicy</span><span class="sxs-lookup"><span data-stu-id="cc2ec-169">CookiePolicyOptions.MinimumSameSitePolicy</span></span>](xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy)

::: moniker-end

## <a name="history-and-changes"></a><span data-ttu-id="cc2ec-170">Cronologia e modifiche</span><span class="sxs-lookup"><span data-stu-id="cc2ec-170">History and changes</span></span>

<span data-ttu-id="cc2ec-171">Il supporto di navigava sullostesso sito è stato implementato per la prima volta nel ASP.NET Core 2,0 usando lo [standard 2016 Draft](https://tools.ietf.org/html/draft-west-first-party-cookies-07#section-4.1).</span><span class="sxs-lookup"><span data-stu-id="cc2ec-171">SameSite support was first implemented in ASP.NET Core in 2.0 using the [2016 draft standard](https://tools.ietf.org/html/draft-west-first-party-cookies-07#section-4.1).</span></span> <span data-ttu-id="cc2ec-172">Lo standard 2016 è stato acconsentito esplicitamente.</span><span class="sxs-lookup"><span data-stu-id="cc2ec-172">The 2016 standard was opt-in.</span></span> <span data-ttu-id="cc2ec-173">Per impostazione predefinita, ASP.NET Core si è scelto di impostare diversi cookie `Lax` .</span><span class="sxs-lookup"><span data-stu-id="cc2ec-173">ASP.NET Core opted-in by setting several cookies to `Lax` by default.</span></span> <span data-ttu-id="cc2ec-174">Dopo avere riscontrato diversi [problemi](https://github.com/aspnet/Announcements/issues/318) di autenticazione, la maggior parte dell'utilizzo di navigava sullostesso sito è stata [disabilitata](https://github.com/aspnet/Announcements/issues/348).</span><span class="sxs-lookup"><span data-stu-id="cc2ec-174">After encountering several [issues](https://github.com/aspnet/Announcements/issues/318) with authentication, most SameSite usage was [disabled](https://github.com/aspnet/Announcements/issues/348).</span></span>

<span data-ttu-id="cc2ec-175">Le [patch](https://devblogs.microsoft.com/dotnet/net-core-November-2019/) sono state rilasciate nel 2019 novembre per l'aggiornamento dallo standard 2016 allo standard 2019.</span><span class="sxs-lookup"><span data-stu-id="cc2ec-175">[Patches](https://devblogs.microsoft.com/dotnet/net-core-November-2019/) were issued in November 2019 to update from the 2016 standard to the 2019 standard.</span></span> <span data-ttu-id="cc2ec-176">[Bozza 2019 della specifica navigava sullostesso sito](https://github.com/aspnet/Announcements/issues/390):</span><span class="sxs-lookup"><span data-stu-id="cc2ec-176">The [2019 draft of the SameSite specification](https://github.com/aspnet/Announcements/issues/390):</span></span>

* <span data-ttu-id="cc2ec-177">**Non** è compatibile con le versioni precedenti della bozza 2016.</span><span class="sxs-lookup"><span data-stu-id="cc2ec-177">Is **not** backwards compatible with the 2016 draft.</span></span> <span data-ttu-id="cc2ec-178">Per ulteriori informazioni, vedere [supporto dei browser meno recenti](#sob) in questo documento.</span><span class="sxs-lookup"><span data-stu-id="cc2ec-178">For more information, see [Supporting older browsers](#sob) in this document.</span></span>
* <span data-ttu-id="cc2ec-179">Specifica che i cookie vengono considerati come `SameSite=Lax` per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="cc2ec-179">Specifies cookies are treated as `SameSite=Lax` by default.</span></span>
* <span data-ttu-id="cc2ec-180">Specifica i cookie che asseriscono in modo esplicito `SameSite=None` per consentire il recapito tra siti deve essere contrassegnato come `Secure` .</span><span class="sxs-lookup"><span data-stu-id="cc2ec-180">Specifies cookies that explicitly assert `SameSite=None` in order to enable cross-site delivery should be marked as `Secure`.</span></span> <span data-ttu-id="cc2ec-181">`None`è una nuova voce da rifiutare esplicitamente.</span><span class="sxs-lookup"><span data-stu-id="cc2ec-181">`None` is a new entry to opt out.</span></span>
* <span data-ttu-id="cc2ec-182">È supportato dalle patch rilasciate per ASP.NET Core 2,1, 2,2 e 3,0.</span><span class="sxs-lookup"><span data-stu-id="cc2ec-182">Is supported by patches issued for ASP.NET Core 2.1, 2.2, and 3.0.</span></span> <span data-ttu-id="cc2ec-183">ASP.NET Core 3,1 dispone di supporto navigava sullostesso sito aggiuntivo.</span><span class="sxs-lookup"><span data-stu-id="cc2ec-183">ASP.NET Core 3.1 has additional SameSite support.</span></span>
* <span data-ttu-id="cc2ec-184">Viene pianificata per essere abilitata per impostazione predefinita da [Chrome](https://chromestatus.com/feature/5088147346030592) in [feb 2020](https://blog.chromium.org/2019/10/developers-get-ready-for-new.html).</span><span class="sxs-lookup"><span data-stu-id="cc2ec-184">Is scheduled to be enabled by [Chrome](https://chromestatus.com/feature/5088147346030592) by default in [Feb 2020](https://blog.chromium.org/2019/10/developers-get-ready-for-new.html).</span></span> <span data-ttu-id="cc2ec-185">Il passaggio a questo standard nei browser è stato avviato in 2019.</span><span class="sxs-lookup"><span data-stu-id="cc2ec-185">Browsers started moving to this standard in 2019.</span></span>

## <a name="apis-impacted-by-the-change-from-the-2016-samesite-draft-standard-to-the-2019-draft-standard"></a><span data-ttu-id="cc2ec-186">API interessate dal passaggio dallo standard Draft 2016 navigava sullostesso sito allo standard 2019 Draft</span><span class="sxs-lookup"><span data-stu-id="cc2ec-186">APIs impacted by the change from the 2016 SameSite draft standard to the 2019 draft standard</span></span>

* [<span data-ttu-id="cc2ec-187">Http. SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="cc2ec-187">Http.SameSiteMode</span></span>](xref:Microsoft.AspNetCore.Http.SameSiteMode)
* [<span data-ttu-id="cc2ec-188">CookieOptions. navigava sullostesso sito</span><span class="sxs-lookup"><span data-stu-id="cc2ec-188">CookieOptions.SameSite</span></span>](xref:Microsoft.AspNetCore.Http.CookieOptions.SameSite)
* [<span data-ttu-id="cc2ec-189">CookieBuilder. navigava sullostesso sito</span><span class="sxs-lookup"><span data-stu-id="cc2ec-189">CookieBuilder.SameSite</span></span>](xref:Microsoft.AspNetCore.Http.CookieBuilder.SameSite)
* [<span data-ttu-id="cc2ec-190">CookiePolicyOptions.MinimumSameSitePolicy</span><span class="sxs-lookup"><span data-stu-id="cc2ec-190">CookiePolicyOptions.MinimumSameSitePolicy</span></span>](xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy)
* <xref:Microsoft.Net.Http.Headers.SameSiteMode?displayProperty=fullName>
* <xref:Microsoft.Net.Http.Headers.SetCookieHeaderValue.SameSite?displayProperty=fullName>

<a name="sob"></a>

## <a name="supporting-older-browsers"></a><span data-ttu-id="cc2ec-191">Supporto di browser meno recenti</span><span class="sxs-lookup"><span data-stu-id="cc2ec-191">Supporting older browsers</span></span>

<span data-ttu-id="cc2ec-192">Lo standard 2016 navigava sullostesso sito ha richiesto che i valori sconosciuti debbano essere considerati `SameSite=Strict` valori.</span><span class="sxs-lookup"><span data-stu-id="cc2ec-192">The 2016 SameSite standard mandated that unknown values must be treated as `SameSite=Strict` values.</span></span> <span data-ttu-id="cc2ec-193">Le app a cui è stato eseguito l'accesso da browser meno recenti che supportano lo standard navigava sullostesso sito 2016 possono interrompersi quando ottengono una proprietà navigava sullostesso sito con un valore `None` .</span><span class="sxs-lookup"><span data-stu-id="cc2ec-193">Apps accessed from older browsers which support the 2016 SameSite standard may break when they get a SameSite property with a value of `None`.</span></span> <span data-ttu-id="cc2ec-194">Le app Web devono implementare il rilevamento del browser se intendono supportare browser meno recenti.</span><span class="sxs-lookup"><span data-stu-id="cc2ec-194">Web apps must implement browser detection if they intend to support older browsers.</span></span> <span data-ttu-id="cc2ec-195">ASP.NET Core non implementa il rilevamento del browser perché i valori degli agenti utente sono altamente volatili e cambiano di frequente.</span><span class="sxs-lookup"><span data-stu-id="cc2ec-195">ASP.NET Core doesn't implement browser detection because User-Agents values are highly volatile and change frequently.</span></span> <span data-ttu-id="cc2ec-196">Un punto di estensione in <xref:Microsoft.AspNetCore.CookiePolicy> consente di collegare la logica specifica dell'agente utente.</span><span class="sxs-lookup"><span data-stu-id="cc2ec-196">An extension point in <xref:Microsoft.AspNetCore.CookiePolicy> allows plugging in User-Agent specific logic.</span></span>

<span data-ttu-id="cc2ec-197">In `Startup.Configure` aggiungere il codice che chiama <xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy*> prima di chiamare <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> o *qualsiasi* metodo che scrive cookie:</span><span class="sxs-lookup"><span data-stu-id="cc2ec-197">In `Startup.Configure`, add code that calls <xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy*> before calling <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> or *any* method that writes cookies:</span></span>

[!code-csharp[](samesite/sample/Startup.cs?name=snippet5&highlight=18-19)]

<span data-ttu-id="cc2ec-198">In `Startup.ConfigureServices` aggiungere codice simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="cc2ec-198">In `Startup.ConfigureServices`, add code similar to the following:</span></span>

::: moniker range="= aspnetcore-3.1"

[!code-csharp[](samesite/sample/Startup31.cs?name=snippet)]

::: moniker-end

::: moniker range="< aspnetcore-3.1"

[!code-csharp[](samesite/sample/Startup.cs?name=snippet)]

::: moniker-end

<span data-ttu-id="cc2ec-199">Nell'esempio precedente, `MyUserAgentDetectionLib.DisallowsSameSiteNone` è una libreria fornita dall'utente che rileva se l'agente utente non supporta navigava sullostesso sito `None` :</span><span class="sxs-lookup"><span data-stu-id="cc2ec-199">In the preceding sample, `MyUserAgentDetectionLib.DisallowsSameSiteNone` is a user supplied library that detects if the user agent doesn't support SameSite `None`:</span></span>

[!code-csharp[](samesite/sample/Startup31.cs?name=snippet2)]

<span data-ttu-id="cc2ec-200">Il codice seguente illustra un metodo di esempio `DisallowsSameSiteNone` :</span><span class="sxs-lookup"><span data-stu-id="cc2ec-200">The following code shows a sample `DisallowsSameSiteNone` method:</span></span>

> [!WARNING]
> <span data-ttu-id="cc2ec-201">Il codice seguente è solo a scopo dimostrativo:</span><span class="sxs-lookup"><span data-stu-id="cc2ec-201">The following code is for demonstration only:</span></span>
> * <span data-ttu-id="cc2ec-202">Non deve essere considerato completo.</span><span class="sxs-lookup"><span data-stu-id="cc2ec-202">It should not be considered complete.</span></span>
> * <span data-ttu-id="cc2ec-203">Non è gestita né supportata.</span><span class="sxs-lookup"><span data-stu-id="cc2ec-203">It is not maintained or supported.</span></span>

[!code-csharp[](samesite/sample/Startup31.cs?name=snippetX)]

## <a name="test-apps-for-samesite-problems"></a><span data-ttu-id="cc2ec-204">Testare le app per i problemi di navigava sullostesso sito</span><span class="sxs-lookup"><span data-stu-id="cc2ec-204">Test apps for SameSite problems</span></span>

<span data-ttu-id="cc2ec-205">Le app che interagiscono con i siti remoti, ad esempio tramite l'accesso di terze parti, devono:</span><span class="sxs-lookup"><span data-stu-id="cc2ec-205">Apps that interact with remote sites such as through third-party login need to:</span></span>

* <span data-ttu-id="cc2ec-206">Testare l'interazione su più browser.</span><span class="sxs-lookup"><span data-stu-id="cc2ec-206">Test the interaction on multiple browsers.</span></span>
* <span data-ttu-id="cc2ec-207">Applicare il [rilevamento e la mitigazione del browser CookiePolicy](#sob) descritti in questo documento.</span><span class="sxs-lookup"><span data-stu-id="cc2ec-207">Apply the [CookiePolicy browser detection and mitigation](#sob) discussed in this document.</span></span>

<span data-ttu-id="cc2ec-208">Testare le app Web usando una versione client che può acconsentire esplicitamente al nuovo comportamento del navigava sullostesso sito.</span><span class="sxs-lookup"><span data-stu-id="cc2ec-208">Test web apps using a client version that can opt-in to the new SameSite behavior.</span></span> <span data-ttu-id="cc2ec-209">Chrome, Firefox e Chromium Edge hanno tutti nuovi flag di funzionalità di consenso esplicito che possono essere usati per i test.</span><span class="sxs-lookup"><span data-stu-id="cc2ec-209">Chrome, Firefox, and Chromium Edge all have new opt-in feature flags that can be used for testing.</span></span> <span data-ttu-id="cc2ec-210">Quando l'app applica le patch di navigava sullostesso sito, testarla con versioni client precedenti, in particolare Safari.</span><span class="sxs-lookup"><span data-stu-id="cc2ec-210">After your app applies the SameSite patches, test it with older client versions, especially Safari.</span></span> <span data-ttu-id="cc2ec-211">Per ulteriori informazioni, vedere [supporto dei browser meno recenti](#sob) in questo documento.</span><span class="sxs-lookup"><span data-stu-id="cc2ec-211">For more information, see [Supporting older browsers](#sob) in this document.</span></span>

### <a name="test-with-chrome"></a><span data-ttu-id="cc2ec-212">Eseguire test con Chrome</span><span class="sxs-lookup"><span data-stu-id="cc2ec-212">Test with Chrome</span></span>

<span data-ttu-id="cc2ec-213">Chrome 78 + fornisce risultati fuorvianti perché prevede una mitigazione temporanea.</span><span class="sxs-lookup"><span data-stu-id="cc2ec-213">Chrome 78+ gives misleading results because it has a temporary mitigation in place.</span></span> <span data-ttu-id="cc2ec-214">La mitigazione temporanea di Chrome 78 + consente ai cookie meno di due minuti di età.</span><span class="sxs-lookup"><span data-stu-id="cc2ec-214">The Chrome 78+ temporary mitigation allows cookies less than two minutes old.</span></span> <span data-ttu-id="cc2ec-215">Chrome 76 o 77 con i flag di test appropriati abilitati fornisce risultati più accurati.</span><span class="sxs-lookup"><span data-stu-id="cc2ec-215">Chrome 76 or 77 with the appropriate test flags enabled provides more accurate results.</span></span> <span data-ttu-id="cc2ec-216">Per testare il nuovo comportamento `chrome://flags/#same-site-by-default-cookies` di navigava sullostesso sito, impostare su **Enabled**.</span><span class="sxs-lookup"><span data-stu-id="cc2ec-216">To test the new SameSite behavior toggle `chrome://flags/#same-site-by-default-cookies` to **Enabled**.</span></span> <span data-ttu-id="cc2ec-217">Le versioni precedenti di Chrome (75 e versioni precedenti) vengono segnalate per avere esito negativo con la nuova `None` impostazione.</span><span class="sxs-lookup"><span data-stu-id="cc2ec-217">Older versions of Chrome (75 and below) are reported to fail with the new `None` setting.</span></span> <span data-ttu-id="cc2ec-218">Vedere [supporto di browser meno recenti](#sob) in questo documento.</span><span class="sxs-lookup"><span data-stu-id="cc2ec-218">See [Supporting older browsers](#sob) in this document.</span></span>

<span data-ttu-id="cc2ec-219">Google non rende disponibili versioni precedenti di Chrome.</span><span class="sxs-lookup"><span data-stu-id="cc2ec-219">Google does not make older chrome versions available.</span></span> <span data-ttu-id="cc2ec-220">Seguire le istruzioni riportate in [scaricare Chromium](https://www.chromium.org/getting-involved/download-chromium) per testare le versioni precedenti di Chrome.</span><span class="sxs-lookup"><span data-stu-id="cc2ec-220">Follow the instructions at [Download Chromium](https://www.chromium.org/getting-involved/download-chromium) to test older versions of Chrome.</span></span> <span data-ttu-id="cc2ec-221">Non **scaricare Chrome** dai collegamenti forniti cercando le versioni precedenti di Chrome.</span><span class="sxs-lookup"><span data-stu-id="cc2ec-221">Do **not** download Chrome from links provided by searching for older versions of chrome.</span></span>

* [<span data-ttu-id="cc2ec-222">Cromo 76 Win64</span><span class="sxs-lookup"><span data-stu-id="cc2ec-222">Chromium 76 Win64</span></span>](https://commondatastorage.googleapis.com/chromium-browser-snapshots/index.html?prefix=Win_x64/664998/)
* [<span data-ttu-id="cc2ec-223">Cromo 74 Win64</span><span class="sxs-lookup"><span data-stu-id="cc2ec-223">Chromium 74 Win64</span></span>](https://commondatastorage.googleapis.com/chromium-browser-snapshots/index.html?prefix=Win_x64/638880/)

<span data-ttu-id="cc2ec-224">A partire dalla versione Canary `80.0.3975.0` , è possibile disabilitare la mitigazione temporanea + post temporanea a scopo di test usando il nuovo flag `--enable-features=SameSiteDefaultChecksMethodRigorously` per consentire il test di siti e servizi nello stato finale finale della funzionalità in cui è stata rimossa la mitigazione.</span><span class="sxs-lookup"><span data-stu-id="cc2ec-224">Starting in Canary version `80.0.3975.0`, the Lax+POST temporary mitigation can be disabled for testing purposes using the new flag `--enable-features=SameSiteDefaultChecksMethodRigorously` to allow testing of sites and services in the eventual end state of the feature where the mitigation has been removed.</span></span> <span data-ttu-id="cc2ec-225">Per ulteriori informazioni, vedere la pagina relativa agli [aggiornamenti di navigava sullostesso sito](https://www.chromium.org/updates/same-site) per progetti Chromium</span><span class="sxs-lookup"><span data-stu-id="cc2ec-225">For more information, see The Chromium Projects [SameSite Updates](https://www.chromium.org/updates/same-site)</span></span>

### <a name="test-with-safari"></a><span data-ttu-id="cc2ec-226">Eseguire test con Safari</span><span class="sxs-lookup"><span data-stu-id="cc2ec-226">Test with Safari</span></span>

<span data-ttu-id="cc2ec-227">Safari 12 ha implementato rigorosamente la bozza precedente e ha esito negativo quando il nuovo `None` valore si trova in un cookie.</span><span class="sxs-lookup"><span data-stu-id="cc2ec-227">Safari 12 strictly implemented the prior draft and fails when the new `None` value is in a cookie.</span></span> <span data-ttu-id="cc2ec-228">`None`viene evitato tramite il codice di rilevamento del browser che [supporta i browser meno recenti](#sob) in questo documento.</span><span class="sxs-lookup"><span data-stu-id="cc2ec-228">`None` is avoided via the browser detection code [Supporting older browsers](#sob) in this document.</span></span> <span data-ttu-id="cc2ec-229">Testare gli accessi basati su stile sistema operativo Safari 12, Safari 13 e WebKit usando MSAL, ADAL o qualsiasi libreria in uso.</span><span class="sxs-lookup"><span data-stu-id="cc2ec-229">Test Safari 12, Safari 13, and WebKit based OS style logins using MSAL, ADAL or whatever library you are using.</span></span> <span data-ttu-id="cc2ec-230">Il problema dipende dalla versione del sistema operativo sottostante.</span><span class="sxs-lookup"><span data-stu-id="cc2ec-230">The problem is dependent on the underlying OS version.</span></span> <span data-ttu-id="cc2ec-231">I problemi di compatibilità con il nuovo comportamento navigava sullostesso sito sono noti per OSX Mojave (10,14) e iOS 12.</span><span class="sxs-lookup"><span data-stu-id="cc2ec-231">OSX Mojave (10.14) and iOS 12 are known to have compatibility problems with the new SameSite behavior.</span></span> <span data-ttu-id="cc2ec-232">L'aggiornamento del sistema operativo a OSX Catalina (10,15) o iOS 13 corregge il problema.</span><span class="sxs-lookup"><span data-stu-id="cc2ec-232">Upgrading the OS to OSX Catalina (10.15) or iOS 13 fixes the problem.</span></span> <span data-ttu-id="cc2ec-233">Safari attualmente non dispone di un flag di consenso esplicito per il test del nuovo comportamento delle specifiche.</span><span class="sxs-lookup"><span data-stu-id="cc2ec-233">Safari does not currently have an opt-in flag for testing the new spec behavior.</span></span>

### <a name="test-with-firefox"></a><span data-ttu-id="cc2ec-234">Eseguire test con Firefox</span><span class="sxs-lookup"><span data-stu-id="cc2ec-234">Test with Firefox</span></span>

<span data-ttu-id="cc2ec-235">Il supporto di Firefox per il nuovo standard può essere testato nella versione 68 + scegliendo nella `about:config` pagina con il flag funzionalità `network.cookie.sameSite.laxByDefault` .</span><span class="sxs-lookup"><span data-stu-id="cc2ec-235">Firefox support for the new standard can be tested on version 68+ by opting in on the `about:config` page with the feature flag `network.cookie.sameSite.laxByDefault`.</span></span> <span data-ttu-id="cc2ec-236">Non sono stati segnalati problemi di compatibilità con le versioni precedenti di Firefox.</span><span class="sxs-lookup"><span data-stu-id="cc2ec-236">There haven't been reports of compatibility issues with older versions of Firefox.</span></span>

### <a name="test-with-edge-browser"></a><span data-ttu-id="cc2ec-237">Eseguire test con il browser Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="cc2ec-237">Test with Edge browser</span></span>

<span data-ttu-id="cc2ec-238">Edge supporta lo standard navigava sullostesso sito precedente.</span><span class="sxs-lookup"><span data-stu-id="cc2ec-238">Edge supports the old SameSite standard.</span></span> <span data-ttu-id="cc2ec-239">La versione perimetrale 44 non presenta problemi di compatibilità noti con il nuovo standard.</span><span class="sxs-lookup"><span data-stu-id="cc2ec-239">Edge version 44 doesn't have any known compatibility problems with the new standard.</span></span>

### <a name="test-with-edge-chromium"></a><span data-ttu-id="cc2ec-240">Test con Edge (cromo)</span><span class="sxs-lookup"><span data-stu-id="cc2ec-240">Test with Edge (Chromium)</span></span>

<span data-ttu-id="cc2ec-241">I flag navigava sullostesso sito sono impostati nella `edge://flags/#same-site-by-default-cookies` pagina.</span><span class="sxs-lookup"><span data-stu-id="cc2ec-241">SameSite flags are set on the `edge://flags/#same-site-by-default-cookies` page.</span></span> <span data-ttu-id="cc2ec-242">Nessun problema di compatibilità rilevato con cromo perimetrale.</span><span class="sxs-lookup"><span data-stu-id="cc2ec-242">No compatibility issues were discovered with Edge Chromium.</span></span>

### <a name="test-with-electron"></a><span data-ttu-id="cc2ec-243">Esegui test conElectron</span><span class="sxs-lookup"><span data-stu-id="cc2ec-243">Test with Electron</span></span>

<span data-ttu-id="cc2ec-244">Le versioni di Electron includono versioni precedenti di cromo.</span><span class="sxs-lookup"><span data-stu-id="cc2ec-244">Versions of Electron include older versions of Chromium.</span></span> <span data-ttu-id="cc2ec-245">Ad esempio, la versione di Electron usata dai team è Chromium 66, che presenta il comportamento precedente.</span><span class="sxs-lookup"><span data-stu-id="cc2ec-245">For example, the version of Electron used by Teams is Chromium 66, which exhibits the older behavior.</span></span> <span data-ttu-id="cc2ec-246">È necessario eseguire un test di compatibilità personalizzato con la versione del Electron prodotto usato.</span><span class="sxs-lookup"><span data-stu-id="cc2ec-246">You must perform your own compatibility testing with the version of Electron your product uses.</span></span> <span data-ttu-id="cc2ec-247">Vedere [supporto dei browser precedenti](#sob) nella sezione seguente.</span><span class="sxs-lookup"><span data-stu-id="cc2ec-247">See [Supporting older browsers](#sob) in the following section.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="cc2ec-248">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="cc2ec-248">Additional resources</span></span>

* [<span data-ttu-id="cc2ec-249">Blog di Chromium: sviluppatori: prepararsi per la nuova navigava sullostesso sito = None; Impostazioni sicure del cookie</span><span class="sxs-lookup"><span data-stu-id="cc2ec-249">Chromium Blog:Developers: Get Ready for New SameSite=None; Secure Cookie Settings</span></span>](https://blog.chromium.org/2019/10/developers-get-ready-for-new.html)
* [<span data-ttu-id="cc2ec-250">Spiegazione dei cookie navigava sullostesso sito</span><span class="sxs-lookup"><span data-stu-id="cc2ec-250">SameSite cookies explained</span></span>](https://web.dev/samesite-cookies-explained/)
* [<span data-ttu-id="cc2ec-251">Patch di novembre 2019</span><span class="sxs-lookup"><span data-stu-id="cc2ec-251">November 2019 Patches</span></span>](https://devblogs.microsoft.com/dotnet/net-core-November-2019/)

 ::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"

| <span data-ttu-id="cc2ec-252">Esempio</span><span class="sxs-lookup"><span data-stu-id="cc2ec-252">Sample</span></span>               | <span data-ttu-id="cc2ec-253">Document</span><span class="sxs-lookup"><span data-stu-id="cc2ec-253">Document</span></span> |
| ----------------- | ------------ |
| [<span data-ttu-id="cc2ec-254">MVC .NET Core</span><span class="sxs-lookup"><span data-stu-id="cc2ec-254">.NET Core MVC</span></span>](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21MVC)  | <xref:security/samesite/mvc21> |
| <span data-ttu-id="cc2ec-255">[Pagine di .NET Core Razor](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21RazorPages)</span><span class="sxs-lookup"><span data-stu-id="cc2ec-255">[.NET Core Razor Pages](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21RazorPages)</span></span>  | <xref:security/samesite/rp21> |

::: moniker-end

 ::: moniker range=">= aspnetcore-3.0"

| <span data-ttu-id="cc2ec-256">Esempio</span><span class="sxs-lookup"><span data-stu-id="cc2ec-256">Sample</span></span>               | <span data-ttu-id="cc2ec-257">Document</span><span class="sxs-lookup"><span data-stu-id="cc2ec-257">Document</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="cc2ec-258">[Pagine di .NET Core Razor](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore31RazorPages)</span><span class="sxs-lookup"><span data-stu-id="cc2ec-258">[.NET Core Razor Pages](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore31RazorPages)</span></span>  | <xref:security/samesite/rp31> |

::: moniker-end
