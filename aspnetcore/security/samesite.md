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
ms.openlocfilehash: 68766591ec86e12e5602d741de74e20aec67cf49
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/26/2020
ms.locfileid: "85399504"
---
# <a name="work-with-samesite-cookies-in-aspnet-core"></a><span data-ttu-id="39a16-103">Usare i cookie navigava sullostesso sito in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="39a16-103">Work with SameSite cookies in ASP.NET Core</span></span>

<span data-ttu-id="39a16-104">Autore: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="39a16-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="39a16-105">Navigava sullostesso sito è uno standard [IETF](https://ietf.org/about/) Draft progettato per garantire una protezione contro gli attacchi di richiesta intersito falsificazione (CSRF).</span><span class="sxs-lookup"><span data-stu-id="39a16-105">SameSite is an [IETF](https://ietf.org/about/) draft standard designed to provide some protection against cross-site request forgery (CSRF) attacks.</span></span> <span data-ttu-id="39a16-106">Originariamente redatto in [2016](https://tools.ietf.org/html/draft-west-first-party-cookies-07), il Draft standard è stato aggiornato in [2019](https://tools.ietf.org/html/draft-west-cookie-incrementalism-00).</span><span class="sxs-lookup"><span data-stu-id="39a16-106">Originally drafted in [2016](https://tools.ietf.org/html/draft-west-first-party-cookies-07), the draft standard was updated in [2019](https://tools.ietf.org/html/draft-west-cookie-incrementalism-00).</span></span> <span data-ttu-id="39a16-107">Lo standard aggiornato non è compatibile con le versioni precedenti dello standard precedente, con le differenze più evidenti tra quelle riportate di seguito:</span><span class="sxs-lookup"><span data-stu-id="39a16-107">The updated standard is not backward compatible with the previous standard, with the following being the most noticeable differences:</span></span>

* <span data-ttu-id="39a16-108">I cookie senza intestazione navigava sullostesso sito vengono considerati come `SameSite=Lax` per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="39a16-108">Cookies without SameSite header are treated as `SameSite=Lax` by default.</span></span>
* <span data-ttu-id="39a16-109">`SameSite=None`deve essere usato per consentire l'uso di cookie tra siti.</span><span class="sxs-lookup"><span data-stu-id="39a16-109">`SameSite=None` must be used to allow cross-site cookie use.</span></span>
* <span data-ttu-id="39a16-110">I cookie che asseriscono `SameSite=None` anche devono essere contrassegnati come `Secure` .</span><span class="sxs-lookup"><span data-stu-id="39a16-110">Cookies that assert `SameSite=None` must also be marked as `Secure`.</span></span>
* <span data-ttu-id="39a16-111">Le applicazioni che utilizzano [`<iframe>`](https://developer.mozilla.org/docs/Web/HTML/Element/iframe) potrebbero riscontrare problemi con i `sameSite=Lax` cookie di o `sameSite=Strict` perché `<iframe>` vengono considerati scenari tra siti.</span><span class="sxs-lookup"><span data-stu-id="39a16-111">Applications that use [`<iframe>`](https://developer.mozilla.org/docs/Web/HTML/Element/iframe) may experience issues with `sameSite=Lax` or `sameSite=Strict` cookies because `<iframe>` is treated as cross-site scenarios.</span></span>
* <span data-ttu-id="39a16-112">Il valore `SameSite=None` non è consentito dallo [standard 2016](https://tools.ietf.org/html/draft-west-first-party-cookies-07) e fa in modo che alcune implementazioni considerino tali cookie come `SameSite=Strict` .</span><span class="sxs-lookup"><span data-stu-id="39a16-112">The value `SameSite=None` is not allowed by the [2016 standard](https://tools.ietf.org/html/draft-west-first-party-cookies-07) and causes some implementations to treat such cookies as `SameSite=Strict`.</span></span> <span data-ttu-id="39a16-113">Vedere [supporto di browser meno recenti](#sob) in questo documento.</span><span class="sxs-lookup"><span data-stu-id="39a16-113">See [Supporting older browsers](#sob) in this document.</span></span>

<span data-ttu-id="39a16-114">L' `SameSite=Lax` impostazione funziona per la maggior parte dei cookie dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="39a16-114">The `SameSite=Lax` setting works for most application cookies.</span></span> <span data-ttu-id="39a16-115">Alcune forme di autenticazione come [OpenID Connect](https://openid.net/connect/) (OIDC) e l'impostazione predefinita di [WS-Federation](https://auth0.com/docs/protocols/ws-fed) sono i reindirizzamenti basati su post.</span><span class="sxs-lookup"><span data-stu-id="39a16-115">Some forms of authentication like [OpenID Connect](https://openid.net/connect/) (OIDC) and [WS-Federation](https://auth0.com/docs/protocols/ws-fed) default to POST based redirects.</span></span> <span data-ttu-id="39a16-116">I reindirizzamenti basati su POST attivano le protezioni del browser navigava sullostesso sito, pertanto navigava sullostesso sito è disabilitato per questi componenti.</span><span class="sxs-lookup"><span data-stu-id="39a16-116">The POST based redirects trigger the SameSite browser protections, so SameSite is disabled for these components.</span></span> <span data-ttu-id="39a16-117">La maggior parte degli accessi [OAuth](https://oauth.net/) non è interessata a causa di differenze nel modo in cui la richiesta fluisce.</span><span class="sxs-lookup"><span data-stu-id="39a16-117">Most [OAuth](https://oauth.net/) logins are not affected due to differences in how the request flows.</span></span>

<span data-ttu-id="39a16-118">Ogni componente ASP.NET Core che emette cookie deve decidere se navigava sullostesso sito è appropriato.</span><span class="sxs-lookup"><span data-stu-id="39a16-118">Each ASP.NET Core component that emits cookies needs to decide if SameSite is appropriate.</span></span>

## <a name="samesite-test-sample-code"></a><span data-ttu-id="39a16-119">Codice di esempio di test di navigava sullostesso sito</span><span class="sxs-lookup"><span data-stu-id="39a16-119">SameSite test sample code</span></span>

 ::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"

<span data-ttu-id="39a16-120">È possibile scaricare e testare gli esempi seguenti:</span><span class="sxs-lookup"><span data-stu-id="39a16-120">The following samples can be downloaded and tested:</span></span>

| <span data-ttu-id="39a16-121">Esempio</span><span class="sxs-lookup"><span data-stu-id="39a16-121">Sample</span></span>               | <span data-ttu-id="39a16-122">Document</span><span class="sxs-lookup"><span data-stu-id="39a16-122">Document</span></span> |
| ----------------- | ------------ |
| [<span data-ttu-id="39a16-123">MVC .NET Core</span><span class="sxs-lookup"><span data-stu-id="39a16-123">.NET Core MVC</span></span>](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21MVC)  | <xref:security/samesite/mvc21> |
| <span data-ttu-id="39a16-124">[Pagine di .NET Core Razor](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21RazorPages)</span><span class="sxs-lookup"><span data-stu-id="39a16-124">[.NET Core Razor Pages](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21RazorPages)</span></span>  | <xref:security/samesite/rp21> |

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="39a16-125">È possibile scaricare e testare l'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="39a16-125">The following sample can be downloaded and tested:</span></span>


| <span data-ttu-id="39a16-126">Esempio</span><span class="sxs-lookup"><span data-stu-id="39a16-126">Sample</span></span>               | <span data-ttu-id="39a16-127">Document</span><span class="sxs-lookup"><span data-stu-id="39a16-127">Document</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="39a16-128">[Pagine di .NET Core Razor](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore31RazorPages)</span><span class="sxs-lookup"><span data-stu-id="39a16-128">[.NET Core Razor Pages](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore31RazorPages)</span></span>  | <xref:security/samesite/rp31> |

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

## <a name="net-core-support-for-the-samesite-attribute"></a><span data-ttu-id="39a16-129">Supporto di .NET Core per l'attributo navigava sullostesso sito</span><span class="sxs-lookup"><span data-stu-id="39a16-129">.NET Core support for the sameSite attribute</span></span>

<span data-ttu-id="39a16-130">.NET Core 2,2 supporta lo standard 2019 Draft per navigava sullostesso sito dal rilascio degli aggiornamenti nel 2019 dicembre.</span><span class="sxs-lookup"><span data-stu-id="39a16-130">.NET Core 2.2 supports the 2019 draft standard for SameSite since the release of updates in December 2019.</span></span> <span data-ttu-id="39a16-131">Gli sviluppatori sono in grado di controllare a livello di codice il valore dell'attributo navigava sullostesso sito usando la `HttpCookie.SameSite` Proprietà.</span><span class="sxs-lookup"><span data-stu-id="39a16-131">Developers are able to programmatically control the value of the sameSite attribute using the `HttpCookie.SameSite` property.</span></span> <span data-ttu-id="39a16-132">Impostando la `SameSite` proprietà su Strict, LAX o None, i valori vengono scritti in rete con il cookie.</span><span class="sxs-lookup"><span data-stu-id="39a16-132">Setting the `SameSite` property to Strict, Lax, or None results in those values being written on the network with the cookie.</span></span> <span data-ttu-id="39a16-133">L'impostazione di un valore uguale a (SameSiteMode) (-1) indica che non è necessario includere nella rete alcun attributo navigava sullostesso sito con il cookie</span><span class="sxs-lookup"><span data-stu-id="39a16-133">Setting it equal to (SameSiteMode)(-1) indicates that no sameSite attribute should be included on the network with the cookie</span></span>

[!code-csharp[](samesite/snippets/Privacy.cshtml.cs?name=snippet)]

<span data-ttu-id="39a16-134">.NET Core 3,0 supporta i valori navigava sullostesso sito aggiornati e aggiunge un valore enum aggiuntivo `SameSiteMode.Unspecified` all' `SameSiteMode` enumerazione.</span><span class="sxs-lookup"><span data-stu-id="39a16-134">.NET Core 3.0 supports the updated SameSite values and adds an extra enum value, `SameSiteMode.Unspecified` to the `SameSiteMode` enum.</span></span>
<span data-ttu-id="39a16-135">Questo nuovo valore indica che non è necessario inviare navigava sullostesso sito con il cookie.</span><span class="sxs-lookup"><span data-stu-id="39a16-135">This new value indicates no sameSite should be sent with the cookie.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

## <a name="december-patch-behavior-changes"></a><span data-ttu-id="39a16-136">Modifiche al comportamento della patch di dicembre</span><span class="sxs-lookup"><span data-stu-id="39a16-136">December patch behavior changes</span></span>

<span data-ttu-id="39a16-137">La modifica del comportamento specifico per .NET Framework e .NET Core 2,1 è il modo in cui la `SameSite` Proprietà interpreta il `None` valore.</span><span class="sxs-lookup"><span data-stu-id="39a16-137">The specific behavior change for .NET Framework and .NET Core 2.1 is how the `SameSite` property interprets the `None` value.</span></span> <span data-ttu-id="39a16-138">Prima della patch, il valore `None` significava "non emettere l'attributo", dopo la patch significa "creare l'attributo con un valore di `None` ".</span><span class="sxs-lookup"><span data-stu-id="39a16-138">Before the patch a value of `None` meant "Do not emit the attribute at all", after the patch it means "Emit the attribute with a value of `None`".</span></span> <span data-ttu-id="39a16-139">Dopo la patch `SameSite` , un valore di `(SameSiteMode)(-1)` causa la mancata creazione dell'attributo.</span><span class="sxs-lookup"><span data-stu-id="39a16-139">After the patch a `SameSite` value of `(SameSiteMode)(-1)` causes the attribute not to be emitted.</span></span>

<span data-ttu-id="39a16-140">Il valore predefinito di navigava sullostesso sito per l'autenticazione basata su form e i cookie di stato della sessione è stato modificato da `None` a `Lax` .</span><span class="sxs-lookup"><span data-stu-id="39a16-140">The default SameSite value for forms authentication and session state cookies was changed from `None` to `Lax`.</span></span>

::: moniker-end

## <a name="api-usage-with-samesite"></a><span data-ttu-id="39a16-141">Utilizzo delle API con navigava sullostesso sito</span><span class="sxs-lookup"><span data-stu-id="39a16-141">API usage with SameSite</span></span>

<span data-ttu-id="39a16-142">[HttpContext. Response. cookies. Append](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*) viene impostato sul valore predefinito `Unspecified` , ovvero nessun attributo navigava sullostesso sito aggiunto al cookie e il client utilizzerà il comportamento predefinito (LAX per i nuovi browser, None per quelli precedenti).</span><span class="sxs-lookup"><span data-stu-id="39a16-142">[HttpContext.Response.Cookies.Append](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*) defaults to `Unspecified`, meaning no SameSite attribute added to the cookie and the client will use its default behavior (Lax for new browsers, None for old ones).</span></span> <span data-ttu-id="39a16-143">Il codice seguente illustra come modificare il valore di navigava sullostesso sito del cookie in `SameSiteMode.Lax` :</span><span class="sxs-lookup"><span data-stu-id="39a16-143">The following code shows how to change the cookie SameSite value to `SameSiteMode.Lax`:</span></span>

[!code-csharp[](samesite/sample/Pages/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="39a16-144">Tutti i componenti ASP.NET Core che emettono cookie sostituiscono i valori predefiniti precedenti con le impostazioni appropriate per gli scenari.</span><span class="sxs-lookup"><span data-stu-id="39a16-144">All ASP.NET Core components that emit cookies override the preceding defaults with settings appropriate for their scenarios.</span></span> <span data-ttu-id="39a16-145">L'override dei valori predefiniti precedenti non è stato modificato.</span><span class="sxs-lookup"><span data-stu-id="39a16-145">The overridden preceding default values haven't changed.</span></span>

| <span data-ttu-id="39a16-146">Componente</span><span class="sxs-lookup"><span data-stu-id="39a16-146">Component</span></span> | <span data-ttu-id="39a16-147">cookie</span><span class="sxs-lookup"><span data-stu-id="39a16-147">cookie</span></span> | <span data-ttu-id="39a16-148">Predefinito</span><span class="sxs-lookup"><span data-stu-id="39a16-148">Default</span></span> |
| ------------- | ------------- |
| <xref:Microsoft.AspNetCore.Http.CookieBuilder> | <xref:Microsoft.AspNetCore.Http.CookieBuilder.SameSite> | `Unspecified` |
| <xref:Microsoft.AspNetCore.Http.HttpContext.Session>  | [<span data-ttu-id="39a16-149">SessionOptions. cookie</span><span class="sxs-lookup"><span data-stu-id="39a16-149">SessionOptions.Cookie</span></span>](xref:Microsoft.AspNetCore.Builder.SessionOptions.Cookie) |`Lax` |
| <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.CookieTempDataProvider>  | [<span data-ttu-id="39a16-150">CookieTempDataProviderOptions. cookie</span><span class="sxs-lookup"><span data-stu-id="39a16-150">CookieTempDataProviderOptions.Cookie</span></span>](xref:Microsoft.AspNetCore.Mvc.CookieTempDataProviderOptions.Cookie) | `Lax` |
| <xref:Microsoft.AspNetCore.Antiforgery.IAntiforgery> | [<span data-ttu-id="39a16-151">AntiforgeryOptions. cookie</span><span class="sxs-lookup"><span data-stu-id="39a16-151">AntiforgeryOptions.Cookie</span></span>](xref:Microsoft.AspNetCore.Antiforgery.AntiforgeryOptions.Cookie)| `Strict` |
| [<span data-ttu-id="39a16-152">Autenticazione cookie</span><span class="sxs-lookup"><span data-stu-id="39a16-152">Cookie Authentication</span></span>](xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*) | [<span data-ttu-id="39a16-153">CookieAuthenticationOptions. cookie</span><span class="sxs-lookup"><span data-stu-id="39a16-153">CookieAuthenticationOptions.Cookie</span></span>](xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.CookieName) | `Lax` |
| <xref:Microsoft.Extensions.DependencyInjection.TwitterExtensions.AddTwitter*> | [<span data-ttu-id="39a16-154">TwitterOptions.StateCookie</span><span class="sxs-lookup"><span data-stu-id="39a16-154">TwitterOptions.StateCookie </span></span>](xref:Microsoft.AspNetCore.Authentication.Twitter.TwitterOptions.StateCookie) | `Lax`  |
| <span data-ttu-id="39a16-155"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationHandler`1> | [RemoteAuthenticationOptions.CorrelationCookie](xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.CorrelationCookie)  | `None`</span><span class="sxs-lookup"><span data-stu-id="39a16-155"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationHandler`1> | [RemoteAuthenticationOptions.CorrelationCookie](xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.CorrelationCookie)  | `None`</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.OpenIdConnectExtensions.AddOpenIdConnect*> | [<span data-ttu-id="39a16-156">OpenIdConnectOptions.NonceCookie</span><span class="sxs-lookup"><span data-stu-id="39a16-156">OpenIdConnectOptions.NonceCookie</span></span>](xref:Microsoft.AspNetCore.Authentication.OpenIdConnect.OpenIdConnectOptions.NonceCookie)| `None` |
| [<span data-ttu-id="39a16-157">HttpContext. Response. cookies. Append</span><span class="sxs-lookup"><span data-stu-id="39a16-157">HttpContext.Response.Cookies.Append</span></span>](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*) | <xref:Microsoft.AspNetCore.Http.CookieOptions> | `Unspecified` |

::: moniker range=">= aspnetcore-3.1"

<span data-ttu-id="39a16-158">ASP.NET Core 3,1 e versioni successive fornisce il supporto navigava sullostesso sito seguente:</span><span class="sxs-lookup"><span data-stu-id="39a16-158">ASP.NET Core 3.1 and later provides the following SameSite support:</span></span>

* <span data-ttu-id="39a16-159">Ridefinisce il comportamento di `SameSiteMode.None` da emettere`SameSite=None`</span><span class="sxs-lookup"><span data-stu-id="39a16-159">Redefines the behavior of `SameSiteMode.None` to emit `SameSite=None`</span></span>
* <span data-ttu-id="39a16-160">Aggiunge un nuovo valore `SameSiteMode.Unspecified` per omettere l'attributo navigava sullostesso sito.</span><span class="sxs-lookup"><span data-stu-id="39a16-160">Adds a new value `SameSiteMode.Unspecified` to omit the SameSite attribute.</span></span>
* <span data-ttu-id="39a16-161">Per impostazione predefinita, tutte le API cookie `Unspecified` .</span><span class="sxs-lookup"><span data-stu-id="39a16-161">All cookies APIs default to `Unspecified`.</span></span> <span data-ttu-id="39a16-162">Alcuni componenti che usano cookie impostano valori più specifici per gli scenari.</span><span class="sxs-lookup"><span data-stu-id="39a16-162">Some components that use cookies set values more specific to their scenarios.</span></span> <span data-ttu-id="39a16-163">Vedere la tabella precedente per gli esempi.</span><span class="sxs-lookup"><span data-stu-id="39a16-163">See the table above for examples.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="39a16-164">In ASP.NET Core 3,0 e versioni successive i valori predefiniti di navigava sullostesso sito sono stati modificati per evitare conflitti con impostazioni predefinite client incoerenti.</span><span class="sxs-lookup"><span data-stu-id="39a16-164">In ASP.NET Core 3.0 and later the SameSite defaults were changed to avoid conflicting with inconsistent client defaults.</span></span> <span data-ttu-id="39a16-165">Le API seguenti hanno modificato il valore predefinito da `SameSiteMode.Lax ` a `-1` per evitare la creazione di un attributo navigava sullostesso sito per questi cookie:</span><span class="sxs-lookup"><span data-stu-id="39a16-165">The following APIs have changed the default from `SameSiteMode.Lax ` to `-1` to avoid emitting a SameSite attribute for these cookies:</span></span>

* <span data-ttu-id="39a16-166"><xref:Microsoft.AspNetCore.Http.CookieOptions>usato con [HttpContext. Response. cookies. Append](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*)</span><span class="sxs-lookup"><span data-stu-id="39a16-166"><xref:Microsoft.AspNetCore.Http.CookieOptions> used with [HttpContext.Response.Cookies.Append](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*)</span></span>
* <span data-ttu-id="39a16-167"><xref:Microsoft.AspNetCore.Http.CookieBuilder>usato come factory per`CookieOptions`</span><span class="sxs-lookup"><span data-stu-id="39a16-167"><xref:Microsoft.AspNetCore.Http.CookieBuilder>  used as a factory for `CookieOptions`</span></span>
* [<span data-ttu-id="39a16-168">CookiePolicyOptions.MinimumSameSitePolicy</span><span class="sxs-lookup"><span data-stu-id="39a16-168">CookiePolicyOptions.MinimumSameSitePolicy</span></span>](xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy)

::: moniker-end

## <a name="history-and-changes"></a><span data-ttu-id="39a16-169">Cronologia e modifiche</span><span class="sxs-lookup"><span data-stu-id="39a16-169">History and changes</span></span>

<span data-ttu-id="39a16-170">Il supporto di navigava sullostesso sito è stato implementato per la prima volta nel ASP.NET Core 2,0 usando lo [standard 2016 Draft](https://tools.ietf.org/html/draft-west-first-party-cookies-07#section-4.1).</span><span class="sxs-lookup"><span data-stu-id="39a16-170">SameSite support was first implemented in ASP.NET Core in 2.0 using the [2016 draft standard](https://tools.ietf.org/html/draft-west-first-party-cookies-07#section-4.1).</span></span> <span data-ttu-id="39a16-171">Lo standard 2016 è stato acconsentito esplicitamente.</span><span class="sxs-lookup"><span data-stu-id="39a16-171">The 2016 standard was opt-in.</span></span> <span data-ttu-id="39a16-172">Per impostazione predefinita, ASP.NET Core si è scelto di impostare diversi cookie `Lax` .</span><span class="sxs-lookup"><span data-stu-id="39a16-172">ASP.NET Core opted-in by setting several cookies to `Lax` by default.</span></span> <span data-ttu-id="39a16-173">Dopo avere riscontrato diversi [problemi](https://github.com/aspnet/Announcements/issues/318) di autenticazione, la maggior parte dell'utilizzo di navigava sullostesso sito è stata [disabilitata](https://github.com/aspnet/Announcements/issues/348).</span><span class="sxs-lookup"><span data-stu-id="39a16-173">After encountering several [issues](https://github.com/aspnet/Announcements/issues/318) with authentication, most SameSite usage was [disabled](https://github.com/aspnet/Announcements/issues/348).</span></span>

<span data-ttu-id="39a16-174">Le [patch](https://devblogs.microsoft.com/dotnet/net-core-November-2019/) sono state rilasciate nel 2019 novembre per l'aggiornamento dallo standard 2016 allo standard 2019.</span><span class="sxs-lookup"><span data-stu-id="39a16-174">[Patches](https://devblogs.microsoft.com/dotnet/net-core-November-2019/) were issued in November 2019 to update from the 2016 standard to the 2019 standard.</span></span> <span data-ttu-id="39a16-175">[Bozza 2019 della specifica navigava sullostesso sito](https://github.com/aspnet/Announcements/issues/390):</span><span class="sxs-lookup"><span data-stu-id="39a16-175">The [2019 draft of the SameSite specification](https://github.com/aspnet/Announcements/issues/390):</span></span>

* <span data-ttu-id="39a16-176">**Non** è compatibile con le versioni precedenti della bozza 2016.</span><span class="sxs-lookup"><span data-stu-id="39a16-176">Is **not** backwards compatible with the 2016 draft.</span></span> <span data-ttu-id="39a16-177">Per ulteriori informazioni, vedere [supporto dei browser meno recenti](#sob) in questo documento.</span><span class="sxs-lookup"><span data-stu-id="39a16-177">For more information, see [Supporting older browsers](#sob) in this document.</span></span>
* <span data-ttu-id="39a16-178">Specifica che i cookie vengono considerati come `SameSite=Lax` per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="39a16-178">Specifies cookies are treated as `SameSite=Lax` by default.</span></span>
* <span data-ttu-id="39a16-179">Specifica i cookie che asseriscono in modo esplicito `SameSite=None` per consentire il recapito tra siti deve essere contrassegnato come `Secure` .</span><span class="sxs-lookup"><span data-stu-id="39a16-179">Specifies cookies that explicitly assert `SameSite=None` in order to enable cross-site delivery should be marked as `Secure`.</span></span> <span data-ttu-id="39a16-180">`None`è una nuova voce da rifiutare esplicitamente.</span><span class="sxs-lookup"><span data-stu-id="39a16-180">`None` is a new entry to opt out.</span></span>
* <span data-ttu-id="39a16-181">È supportato dalle patch rilasciate per ASP.NET Core 2,1, 2,2 e 3,0.</span><span class="sxs-lookup"><span data-stu-id="39a16-181">Is supported by patches issued for ASP.NET Core 2.1, 2.2, and 3.0.</span></span> <span data-ttu-id="39a16-182">ASP.NET Core 3,1 dispone di supporto navigava sullostesso sito aggiuntivo.</span><span class="sxs-lookup"><span data-stu-id="39a16-182">ASP.NET Core 3.1 has additional SameSite support.</span></span>
* <span data-ttu-id="39a16-183">Viene pianificata per essere abilitata per impostazione predefinita da [Chrome](https://chromestatus.com/feature/5088147346030592) in [feb 2020](https://blog.chromium.org/2019/10/developers-get-ready-for-new.html).</span><span class="sxs-lookup"><span data-stu-id="39a16-183">Is scheduled to be enabled by [Chrome](https://chromestatus.com/feature/5088147346030592) by default in [Feb 2020](https://blog.chromium.org/2019/10/developers-get-ready-for-new.html).</span></span> <span data-ttu-id="39a16-184">Il passaggio a questo standard nei browser è stato avviato in 2019.</span><span class="sxs-lookup"><span data-stu-id="39a16-184">Browsers started moving to this standard in 2019.</span></span>

## <a name="apis-impacted-by-the-change-from-the-2016-samesite-draft-standard-to-the-2019-draft-standard"></a><span data-ttu-id="39a16-185">API interessate dal passaggio dallo standard Draft 2016 navigava sullostesso sito allo standard 2019 Draft</span><span class="sxs-lookup"><span data-stu-id="39a16-185">APIs impacted by the change from the 2016 SameSite draft standard to the 2019 draft standard</span></span>

* [<span data-ttu-id="39a16-186">Http. SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="39a16-186">Http.SameSiteMode</span></span>](xref:Microsoft.AspNetCore.Http.SameSiteMode)
* [<span data-ttu-id="39a16-187">CookieOptions. navigava sullostesso sito</span><span class="sxs-lookup"><span data-stu-id="39a16-187">CookieOptions.SameSite</span></span>](xref:Microsoft.AspNetCore.Http.CookieOptions.SameSite)
* [<span data-ttu-id="39a16-188">CookieBuilder. navigava sullostesso sito</span><span class="sxs-lookup"><span data-stu-id="39a16-188">CookieBuilder.SameSite</span></span>](xref:Microsoft.AspNetCore.Http.CookieBuilder.SameSite)
* [<span data-ttu-id="39a16-189">CookiePolicyOptions.MinimumSameSitePolicy</span><span class="sxs-lookup"><span data-stu-id="39a16-189">CookiePolicyOptions.MinimumSameSitePolicy</span></span>](xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy)
* <xref:Microsoft.Net.Http.Headers.SameSiteMode?displayProperty=fullName>
* <xref:Microsoft.Net.Http.Headers.SetCookieHeaderValue.SameSite?displayProperty=fullName>

<a name="sob"></a>

## <a name="supporting-older-browsers"></a><span data-ttu-id="39a16-190">Supporto di browser meno recenti</span><span class="sxs-lookup"><span data-stu-id="39a16-190">Supporting older browsers</span></span>

<span data-ttu-id="39a16-191">Lo standard 2016 navigava sullostesso sito ha richiesto che i valori sconosciuti debbano essere considerati `SameSite=Strict` valori.</span><span class="sxs-lookup"><span data-stu-id="39a16-191">The 2016 SameSite standard mandated that unknown values must be treated as `SameSite=Strict` values.</span></span> <span data-ttu-id="39a16-192">Le app a cui è stato eseguito l'accesso da browser meno recenti che supportano lo standard navigava sullostesso sito 2016 possono interrompersi quando ottengono una proprietà navigava sullostesso sito con un valore `None` .</span><span class="sxs-lookup"><span data-stu-id="39a16-192">Apps accessed from older browsers which support the 2016 SameSite standard may break when they get a SameSite property with a value of `None`.</span></span> <span data-ttu-id="39a16-193">Le app Web devono implementare il rilevamento del browser se intendono supportare browser meno recenti.</span><span class="sxs-lookup"><span data-stu-id="39a16-193">Web apps must implement browser detection if they intend to support older browsers.</span></span> <span data-ttu-id="39a16-194">ASP.NET Core non implementa il rilevamento del browser perché i valori degli agenti utente sono altamente volatili e cambiano di frequente.</span><span class="sxs-lookup"><span data-stu-id="39a16-194">ASP.NET Core doesn't implement browser detection because User-Agents values are highly volatile and change frequently.</span></span> <span data-ttu-id="39a16-195">Un punto di estensione in <xref:Microsoft.AspNetCore.CookiePolicy> consente di collegare la logica specifica dell'agente utente.</span><span class="sxs-lookup"><span data-stu-id="39a16-195">An extension point in <xref:Microsoft.AspNetCore.CookiePolicy> allows plugging in User-Agent specific logic.</span></span>

<span data-ttu-id="39a16-196">In `Startup.Configure` aggiungere il codice che chiama <xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy*> prima di chiamare <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> o *qualsiasi* metodo che scrive cookie:</span><span class="sxs-lookup"><span data-stu-id="39a16-196">In `Startup.Configure`, add code that calls <xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy*> before calling <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> or *any* method that writes cookies:</span></span>

[!code-csharp[](samesite/sample/Startup.cs?name=snippet5&highlight=18-19)]

<span data-ttu-id="39a16-197">In `Startup.ConfigureServices` aggiungere codice simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="39a16-197">In `Startup.ConfigureServices`, add code similar to the following:</span></span>

::: moniker range="= aspnetcore-3.1"

[!code-csharp[](samesite/sample/Startup31.cs?name=snippet)]

::: moniker-end

::: moniker range="< aspnetcore-3.1"

[!code-csharp[](samesite/sample/Startup.cs?name=snippet)]

::: moniker-end

<span data-ttu-id="39a16-198">Nell'esempio precedente, `MyUserAgentDetectionLib.DisallowsSameSiteNone` è una libreria fornita dall'utente che rileva se l'agente utente non supporta navigava sullostesso sito `None` :</span><span class="sxs-lookup"><span data-stu-id="39a16-198">In the preceding sample, `MyUserAgentDetectionLib.DisallowsSameSiteNone` is a user supplied library that detects if the user agent doesn't support SameSite `None`:</span></span>

[!code-csharp[](samesite/sample/Startup31.cs?name=snippet2)]

<span data-ttu-id="39a16-199">Il codice seguente illustra un metodo di esempio `DisallowsSameSiteNone` :</span><span class="sxs-lookup"><span data-stu-id="39a16-199">The following code shows a sample `DisallowsSameSiteNone` method:</span></span>

> [!WARNING]
> <span data-ttu-id="39a16-200">Il codice seguente è solo a scopo dimostrativo:</span><span class="sxs-lookup"><span data-stu-id="39a16-200">The following code is for demonstration only:</span></span>
> * <span data-ttu-id="39a16-201">Non deve essere considerato completo.</span><span class="sxs-lookup"><span data-stu-id="39a16-201">It should not be considered complete.</span></span>
> * <span data-ttu-id="39a16-202">Non è gestita né supportata.</span><span class="sxs-lookup"><span data-stu-id="39a16-202">It is not maintained or supported.</span></span>

[!code-csharp[](samesite/sample/Startup31.cs?name=snippetX)]

## <a name="test-apps-for-samesite-problems"></a><span data-ttu-id="39a16-203">Testare le app per i problemi di navigava sullostesso sito</span><span class="sxs-lookup"><span data-stu-id="39a16-203">Test apps for SameSite problems</span></span>

<span data-ttu-id="39a16-204">Le app che interagiscono con i siti remoti, ad esempio tramite l'accesso di terze parti, devono:</span><span class="sxs-lookup"><span data-stu-id="39a16-204">Apps that interact with remote sites such as through third-party login need to:</span></span>

* <span data-ttu-id="39a16-205">Testare l'interazione su più browser.</span><span class="sxs-lookup"><span data-stu-id="39a16-205">Test the interaction on multiple browsers.</span></span>
* <span data-ttu-id="39a16-206">Applicare il [rilevamento e la mitigazione del browser CookiePolicy](#sob) descritti in questo documento.</span><span class="sxs-lookup"><span data-stu-id="39a16-206">Apply the [CookiePolicy browser detection and mitigation](#sob) discussed in this document.</span></span>

<span data-ttu-id="39a16-207">Testare le app Web usando una versione client che può acconsentire esplicitamente al nuovo comportamento del navigava sullostesso sito.</span><span class="sxs-lookup"><span data-stu-id="39a16-207">Test web apps using a client version that can opt-in to the new SameSite behavior.</span></span> <span data-ttu-id="39a16-208">Chrome, Firefox e Chromium Edge hanno tutti nuovi flag di funzionalità di consenso esplicito che possono essere usati per i test.</span><span class="sxs-lookup"><span data-stu-id="39a16-208">Chrome, Firefox, and Chromium Edge all have new opt-in feature flags that can be used for testing.</span></span> <span data-ttu-id="39a16-209">Quando l'app applica le patch di navigava sullostesso sito, testarla con versioni client precedenti, in particolare Safari.</span><span class="sxs-lookup"><span data-stu-id="39a16-209">After your app applies the SameSite patches, test it with older client versions, especially Safari.</span></span> <span data-ttu-id="39a16-210">Per ulteriori informazioni, vedere [supporto dei browser meno recenti](#sob) in questo documento.</span><span class="sxs-lookup"><span data-stu-id="39a16-210">For more information, see [Supporting older browsers](#sob) in this document.</span></span>

### <a name="test-with-chrome"></a><span data-ttu-id="39a16-211">Eseguire test con Chrome</span><span class="sxs-lookup"><span data-stu-id="39a16-211">Test with Chrome</span></span>

<span data-ttu-id="39a16-212">Chrome 78 + fornisce risultati fuorvianti perché prevede una mitigazione temporanea.</span><span class="sxs-lookup"><span data-stu-id="39a16-212">Chrome 78+ gives misleading results because it has a temporary mitigation in place.</span></span> <span data-ttu-id="39a16-213">La mitigazione temporanea di Chrome 78 + consente ai cookie meno di due minuti di età.</span><span class="sxs-lookup"><span data-stu-id="39a16-213">The Chrome 78+ temporary mitigation allows cookies less than two minutes old.</span></span> <span data-ttu-id="39a16-214">Chrome 76 o 77 con i flag di test appropriati abilitati fornisce risultati più accurati.</span><span class="sxs-lookup"><span data-stu-id="39a16-214">Chrome 76 or 77 with the appropriate test flags enabled provides more accurate results.</span></span> <span data-ttu-id="39a16-215">Per testare il nuovo comportamento `chrome://flags/#same-site-by-default-cookies` di navigava sullostesso sito, impostare su **Enabled**.</span><span class="sxs-lookup"><span data-stu-id="39a16-215">To test the new SameSite behavior toggle `chrome://flags/#same-site-by-default-cookies` to **Enabled**.</span></span> <span data-ttu-id="39a16-216">Le versioni precedenti di Chrome (75 e versioni precedenti) vengono segnalate per avere esito negativo con la nuova `None` impostazione.</span><span class="sxs-lookup"><span data-stu-id="39a16-216">Older versions of Chrome (75 and below) are reported to fail with the new `None` setting.</span></span> <span data-ttu-id="39a16-217">Vedere [supporto di browser meno recenti](#sob) in questo documento.</span><span class="sxs-lookup"><span data-stu-id="39a16-217">See [Supporting older browsers](#sob) in this document.</span></span>

<span data-ttu-id="39a16-218">Google non rende disponibili versioni precedenti di Chrome.</span><span class="sxs-lookup"><span data-stu-id="39a16-218">Google does not make older chrome versions available.</span></span> <span data-ttu-id="39a16-219">Seguire le istruzioni riportate in [scaricare Chromium](https://www.chromium.org/getting-involved/download-chromium) per testare le versioni precedenti di Chrome.</span><span class="sxs-lookup"><span data-stu-id="39a16-219">Follow the instructions at [Download Chromium](https://www.chromium.org/getting-involved/download-chromium) to test older versions of Chrome.</span></span> <span data-ttu-id="39a16-220">Non **scaricare Chrome** dai collegamenti forniti cercando le versioni precedenti di Chrome.</span><span class="sxs-lookup"><span data-stu-id="39a16-220">Do **not** download Chrome from links provided by searching for older versions of chrome.</span></span>

* [<span data-ttu-id="39a16-221">Cromo 76 Win64</span><span class="sxs-lookup"><span data-stu-id="39a16-221">Chromium 76 Win64</span></span>](https://commondatastorage.googleapis.com/chromium-browser-snapshots/index.html?prefix=Win_x64/664998/)
* [<span data-ttu-id="39a16-222">Cromo 74 Win64</span><span class="sxs-lookup"><span data-stu-id="39a16-222">Chromium 74 Win64</span></span>](https://commondatastorage.googleapis.com/chromium-browser-snapshots/index.html?prefix=Win_x64/638880/)

<span data-ttu-id="39a16-223">A partire dalla versione Canary `80.0.3975.0` , è possibile disabilitare la mitigazione temporanea + post temporanea a scopo di test usando il nuovo flag `--enable-features=SameSiteDefaultChecksMethodRigorously` per consentire il test di siti e servizi nello stato finale finale della funzionalità in cui è stata rimossa la mitigazione.</span><span class="sxs-lookup"><span data-stu-id="39a16-223">Starting in Canary version `80.0.3975.0`, the Lax+POST temporary mitigation can be disabled for testing purposes using the new flag `--enable-features=SameSiteDefaultChecksMethodRigorously` to allow testing of sites and services in the eventual end state of the feature where the mitigation has been removed.</span></span> <span data-ttu-id="39a16-224">Per ulteriori informazioni, vedere la pagina relativa agli [aggiornamenti di navigava sullostesso sito](https://www.chromium.org/updates/same-site) per progetti Chromium</span><span class="sxs-lookup"><span data-stu-id="39a16-224">For more information, see The Chromium Projects [SameSite Updates](https://www.chromium.org/updates/same-site)</span></span>

### <a name="test-with-safari"></a><span data-ttu-id="39a16-225">Eseguire test con Safari</span><span class="sxs-lookup"><span data-stu-id="39a16-225">Test with Safari</span></span>

<span data-ttu-id="39a16-226">Safari 12 ha implementato rigorosamente la bozza precedente e ha esito negativo quando il nuovo `None` valore si trova in un cookie.</span><span class="sxs-lookup"><span data-stu-id="39a16-226">Safari 12 strictly implemented the prior draft and fails when the new `None` value is in a cookie.</span></span> <span data-ttu-id="39a16-227">`None`viene evitato tramite il codice di rilevamento del browser che [supporta i browser meno recenti](#sob) in questo documento.</span><span class="sxs-lookup"><span data-stu-id="39a16-227">`None` is avoided via the browser detection code [Supporting older browsers](#sob) in this document.</span></span> <span data-ttu-id="39a16-228">Testare gli accessi basati su stile sistema operativo Safari 12, Safari 13 e WebKit usando MSAL, ADAL o qualsiasi libreria in uso.</span><span class="sxs-lookup"><span data-stu-id="39a16-228">Test Safari 12, Safari 13, and WebKit based OS style logins using MSAL, ADAL or whatever library you are using.</span></span> <span data-ttu-id="39a16-229">Il problema dipende dalla versione del sistema operativo sottostante.</span><span class="sxs-lookup"><span data-stu-id="39a16-229">The problem is dependent on the underlying OS version.</span></span> <span data-ttu-id="39a16-230">I problemi di compatibilità con il nuovo comportamento navigava sullostesso sito sono noti per OSX Mojave (10,14) e iOS 12.</span><span class="sxs-lookup"><span data-stu-id="39a16-230">OSX Mojave (10.14) and iOS 12 are known to have compatibility problems with the new SameSite behavior.</span></span> <span data-ttu-id="39a16-231">L'aggiornamento del sistema operativo a OSX Catalina (10,15) o iOS 13 corregge il problema.</span><span class="sxs-lookup"><span data-stu-id="39a16-231">Upgrading the OS to OSX Catalina (10.15) or iOS 13 fixes the problem.</span></span> <span data-ttu-id="39a16-232">Safari attualmente non dispone di un flag di consenso esplicito per il test del nuovo comportamento delle specifiche.</span><span class="sxs-lookup"><span data-stu-id="39a16-232">Safari does not currently have an opt-in flag for testing the new spec behavior.</span></span>

### <a name="test-with-firefox"></a><span data-ttu-id="39a16-233">Eseguire test con Firefox</span><span class="sxs-lookup"><span data-stu-id="39a16-233">Test with Firefox</span></span>

<span data-ttu-id="39a16-234">Il supporto di Firefox per il nuovo standard può essere testato nella versione 68 + scegliendo nella `about:config` pagina con il flag funzionalità `network.cookie.sameSite.laxByDefault` .</span><span class="sxs-lookup"><span data-stu-id="39a16-234">Firefox support for the new standard can be tested on version 68+ by opting in on the `about:config` page with the feature flag `network.cookie.sameSite.laxByDefault`.</span></span> <span data-ttu-id="39a16-235">Non sono stati segnalati problemi di compatibilità con le versioni precedenti di Firefox.</span><span class="sxs-lookup"><span data-stu-id="39a16-235">There haven't been reports of compatibility issues with older versions of Firefox.</span></span>

### <a name="test-with-edge-browser"></a><span data-ttu-id="39a16-236">Eseguire test con il browser Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="39a16-236">Test with Edge browser</span></span>

<span data-ttu-id="39a16-237">Edge supporta lo standard navigava sullostesso sito precedente.</span><span class="sxs-lookup"><span data-stu-id="39a16-237">Edge supports the old SameSite standard.</span></span> <span data-ttu-id="39a16-238">La versione perimetrale 44 non presenta problemi di compatibilità noti con il nuovo standard.</span><span class="sxs-lookup"><span data-stu-id="39a16-238">Edge version 44 doesn't have any known compatibility problems with the new standard.</span></span>

### <a name="test-with-edge-chromium"></a><span data-ttu-id="39a16-239">Test con Edge (cromo)</span><span class="sxs-lookup"><span data-stu-id="39a16-239">Test with Edge (Chromium)</span></span>

<span data-ttu-id="39a16-240">I flag navigava sullostesso sito sono impostati nella `edge://flags/#same-site-by-default-cookies` pagina.</span><span class="sxs-lookup"><span data-stu-id="39a16-240">SameSite flags are set on the `edge://flags/#same-site-by-default-cookies` page.</span></span> <span data-ttu-id="39a16-241">Nessun problema di compatibilità rilevato con cromo perimetrale.</span><span class="sxs-lookup"><span data-stu-id="39a16-241">No compatibility issues were discovered with Edge Chromium.</span></span>

### <a name="test-with-electron"></a><span data-ttu-id="39a16-242">Esegui test conElectron</span><span class="sxs-lookup"><span data-stu-id="39a16-242">Test with Electron</span></span>

<span data-ttu-id="39a16-243">Le versioni di Electron includono versioni precedenti di cromo.</span><span class="sxs-lookup"><span data-stu-id="39a16-243">Versions of Electron include older versions of Chromium.</span></span> <span data-ttu-id="39a16-244">Ad esempio, la versione di Electron usata dai team è Chromium 66, che presenta il comportamento precedente.</span><span class="sxs-lookup"><span data-stu-id="39a16-244">For example, the version of Electron used by Teams is Chromium 66, which exhibits the older behavior.</span></span> <span data-ttu-id="39a16-245">È necessario eseguire un test di compatibilità personalizzato con la versione del Electron prodotto usato.</span><span class="sxs-lookup"><span data-stu-id="39a16-245">You must perform your own compatibility testing with the version of Electron your product uses.</span></span> <span data-ttu-id="39a16-246">Vedere [supporto dei browser precedenti](#sob) nella sezione seguente.</span><span class="sxs-lookup"><span data-stu-id="39a16-246">See [Supporting older browsers](#sob) in the following section.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="39a16-247">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="39a16-247">Additional resources</span></span>

* [<span data-ttu-id="39a16-248">Blog di Chromium: sviluppatori: prepararsi per la nuova navigava sullostesso sito = None; Impostazioni sicure del cookie</span><span class="sxs-lookup"><span data-stu-id="39a16-248">Chromium Blog:Developers: Get Ready for New SameSite=None; Secure Cookie Settings</span></span>](https://blog.chromium.org/2019/10/developers-get-ready-for-new.html)
* [<span data-ttu-id="39a16-249">Spiegazione dei cookie navigava sullostesso sito</span><span class="sxs-lookup"><span data-stu-id="39a16-249">SameSite cookies explained</span></span>](https://web.dev/samesite-cookies-explained/)
* [<span data-ttu-id="39a16-250">Patch di novembre 2019</span><span class="sxs-lookup"><span data-stu-id="39a16-250">November 2019 Patches</span></span>](https://devblogs.microsoft.com/dotnet/net-core-November-2019/)

 ::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"

| <span data-ttu-id="39a16-251">Esempio</span><span class="sxs-lookup"><span data-stu-id="39a16-251">Sample</span></span>               | <span data-ttu-id="39a16-252">Document</span><span class="sxs-lookup"><span data-stu-id="39a16-252">Document</span></span> |
| ----------------- | ------------ |
| [<span data-ttu-id="39a16-253">MVC .NET Core</span><span class="sxs-lookup"><span data-stu-id="39a16-253">.NET Core MVC</span></span>](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21MVC)  | <xref:security/samesite/mvc21> |
| <span data-ttu-id="39a16-254">[Pagine di .NET Core Razor](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21RazorPages)</span><span class="sxs-lookup"><span data-stu-id="39a16-254">[.NET Core Razor Pages](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21RazorPages)</span></span>  | <xref:security/samesite/rp21> |

::: moniker-end

 ::: moniker range=">= aspnetcore-3.0"

| <span data-ttu-id="39a16-255">Esempio</span><span class="sxs-lookup"><span data-stu-id="39a16-255">Sample</span></span>               | <span data-ttu-id="39a16-256">Document</span><span class="sxs-lookup"><span data-stu-id="39a16-256">Document</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="39a16-257">[Pagine di .NET Core Razor](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore31RazorPages)</span><span class="sxs-lookup"><span data-stu-id="39a16-257">[.NET Core Razor Pages](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore31RazorPages)</span></span>  | <xref:security/samesite/rp31> |

::: moniker-end
