---
title: Configurare ASP.NET Core per l'uso di server proxy e servizi di bilanciamento del carico
author: rick-anderson
description: Informazioni sulla configurazione per le app ospitate dietro server proxy e servizi di bilanciamento del carico, che spesso nascondono informazioni importanti sulle richieste.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
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
uid: host-and-deploy/proxy-load-balancer
ms.openlocfilehash: 461f6d2105d38c5dbea2f8cf479e027c2edede14
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93057622"
---
# <a name="configure-aspnet-core-to-work-with-proxy-servers-and-load-balancers"></a><span data-ttu-id="08729-103">Configurare ASP.NET Core per l'uso di server proxy e servizi di bilanciamento del carico</span><span class="sxs-lookup"><span data-stu-id="08729-103">Configure ASP.NET Core to work with proxy servers and load balancers</span></span>

<span data-ttu-id="08729-104">Di [Chris Ross](https://github.com/Tratcher)</span><span class="sxs-lookup"><span data-stu-id="08729-104">By [Chris Ross](https://github.com/Tratcher)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="08729-105">Nella configurazione consigliata per ASP.NET Core, l'app è ospitata mediante IIS e il modulo ASP.NET Core, Nginx o Apache.</span><span class="sxs-lookup"><span data-stu-id="08729-105">In the recommended configuration for ASP.NET Core, the app is hosted using IIS/ASP.NET Core Module, Nginx, or Apache.</span></span> <span data-ttu-id="08729-106">Server proxy, servizi di bilanciamento del carico e altre appliance di rete spesso nascondono informazioni sulla richiesta prima che questa raggiunga l'app:</span><span class="sxs-lookup"><span data-stu-id="08729-106">Proxy servers, load balancers, and other network appliances often obscure information about the request before it reaches the app:</span></span>

* <span data-ttu-id="08729-107">Quando le richieste HTTPS vengono trasmesse tramite proxy su HTTP, lo schema originale (HTTPS) viene perso e deve essere inoltrato in un'intestazione.</span><span class="sxs-lookup"><span data-stu-id="08729-107">When HTTPS requests are proxied over HTTP, the original scheme (HTTPS) is lost and must be forwarded in a header.</span></span>
* <span data-ttu-id="08729-108">Poiché un'app riceve una richiesta dal proxy e non dall'effettiva origine su Internet o nella rete aziendale, anche l'indirizzo IP di origine del client deve essere inoltrato in un'intestazione.</span><span class="sxs-lookup"><span data-stu-id="08729-108">Because an app receives a request from the proxy and not its true source on the Internet or corporate network, the originating client IP address must also be forwarded in a header.</span></span>

<span data-ttu-id="08729-109">Queste informazioni potrebbero essere importanti per l'elaborazione delle richieste, ad esempio per i reindirizzamenti, l'autenticazione, la generazione di collegamenti, la valutazione dei criteri e la georilevazione dei client.</span><span class="sxs-lookup"><span data-stu-id="08729-109">This information may be important in request processing, for example in redirects, authentication, link generation, policy evaluation, and client geolocation.</span></span>

## <a name="forwarded-headers"></a><span data-ttu-id="08729-110">Intestazioni inoltrate</span><span class="sxs-lookup"><span data-stu-id="08729-110">Forwarded headers</span></span>

<span data-ttu-id="08729-111">Per convenzione, i proxy inoltrano le informazioni nelle intestazioni HTTP.</span><span class="sxs-lookup"><span data-stu-id="08729-111">By convention, proxies forward information in HTTP headers.</span></span>

| <span data-ttu-id="08729-112">Intestazione</span><span class="sxs-lookup"><span data-stu-id="08729-112">Header</span></span> | <span data-ttu-id="08729-113">Descrizione</span><span class="sxs-lookup"><span data-stu-id="08729-113">Description</span></span> |
| ------ | ----------- |
| <span data-ttu-id="08729-114">X-Forwarded-For</span><span class="sxs-lookup"><span data-stu-id="08729-114">X-Forwarded-For</span></span> | <span data-ttu-id="08729-115">Contiene informazioni sul client che ha avviato la richiesta e sui proxy successivi in una catena di proxy.</span><span class="sxs-lookup"><span data-stu-id="08729-115">Holds information about the client that initiated the request and subsequent proxies in a chain of proxies.</span></span> <span data-ttu-id="08729-116">Questo parametro può contenere indirizzi IP (e, facoltativamente, numeri di porta).</span><span class="sxs-lookup"><span data-stu-id="08729-116">This parameter may contain IP addresses (and, optionally, port numbers).</span></span> <span data-ttu-id="08729-117">In una catena di server proxy, il primo parametro indica il client in cui è stata eseguita inizialmente la richiesta.</span><span class="sxs-lookup"><span data-stu-id="08729-117">In a chain of proxy servers, the first parameter indicates the client where the request was first made.</span></span> <span data-ttu-id="08729-118">Seguono gli identificatori dei proxy successivi.</span><span class="sxs-lookup"><span data-stu-id="08729-118">Subsequent proxy identifiers follow.</span></span> <span data-ttu-id="08729-119">L'ultimo proxy della catena non è incluso nell'elenco dei parametri.</span><span class="sxs-lookup"><span data-stu-id="08729-119">The last proxy in the chain isn't in the list of parameters.</span></span> <span data-ttu-id="08729-120">L'indirizzo IP dell'ultimo proxy e, facoltativamente, un numero di porta sono disponibili come indirizzo IP remoto a livello di trasporto.</span><span class="sxs-lookup"><span data-stu-id="08729-120">The last proxy's IP address, and optionally a port number, are available as the remote IP address at the transport layer.</span></span> |
| <span data-ttu-id="08729-121">X-Forwarded-Proto</span><span class="sxs-lookup"><span data-stu-id="08729-121">X-Forwarded-Proto</span></span> | <span data-ttu-id="08729-122">Il valore dello schema di origine (HTTP/HTTPS).</span><span class="sxs-lookup"><span data-stu-id="08729-122">The value of the originating scheme (HTTP/HTTPS).</span></span> <span data-ttu-id="08729-123">Il valore può essere anche un elenco di schemi, se la richiesta ha attraversato più proxy.</span><span class="sxs-lookup"><span data-stu-id="08729-123">The value may also be a list of schemes if the request has traversed multiple proxies.</span></span> |
| <span data-ttu-id="08729-124">X-Forwarded-Host</span><span class="sxs-lookup"><span data-stu-id="08729-124">X-Forwarded-Host</span></span> | <span data-ttu-id="08729-125">Il valore originale del campo dell'intestazione host.</span><span class="sxs-lookup"><span data-stu-id="08729-125">The original value of the Host header field.</span></span> <span data-ttu-id="08729-126">I proxy in genere non modificano l'intestazione host.</span><span class="sxs-lookup"><span data-stu-id="08729-126">Usually, proxies don't modify the Host header.</span></span> <span data-ttu-id="08729-127">Vedere l'[avviso di sicurezza Microsoft CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) per informazioni su una vulnerabilità di elevazione dei privilegi che interessa i sistemi in cui il proxy non convalida o limita le intestazioni host a valori di riferimento noti.</span><span class="sxs-lookup"><span data-stu-id="08729-127">See [Microsoft Security Advisory CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) for information on an elevation-of-privileges vulnerability that affects systems where the proxy doesn't validate or restrict Host headers to known good values.</span></span> |

<span data-ttu-id="08729-128">Il middleware delle intestazioni inoltri ( <xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersMiddleware> ) legge queste intestazioni e compila i campi associati in <xref:Microsoft.AspNetCore.Http.HttpContext> .</span><span class="sxs-lookup"><span data-stu-id="08729-128">The Forwarded Headers Middleware (<xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersMiddleware>), reads these headers and fills in the associated fields on <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span>

<span data-ttu-id="08729-129">Il middleware aggiorna:</span><span class="sxs-lookup"><span data-stu-id="08729-129">The middleware updates:</span></span>

* <span data-ttu-id="08729-130">[HttpContext. Connection. RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress): set usando il `X-Forwarded-For` valore dell'intestazione.</span><span class="sxs-lookup"><span data-stu-id="08729-130">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress): Set using the `X-Forwarded-For` header value.</span></span> <span data-ttu-id="08729-131">Impostazioni aggiuntive influiscono sul modo in cui il middleware imposta `RemoteIpAddress`.</span><span class="sxs-lookup"><span data-stu-id="08729-131">Additional settings influence how the middleware sets `RemoteIpAddress`.</span></span> <span data-ttu-id="08729-132">Per informazioni dettagliate, vedere [Opzioni del middleware delle intestazioni inoltrate](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="08729-132">For details, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>
* <span data-ttu-id="08729-133">[HttpContext. Request. Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme): impostare usando il `X-Forwarded-Proto` valore dell'intestazione.</span><span class="sxs-lookup"><span data-stu-id="08729-133">[HttpContext.Request.Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme): Set using the `X-Forwarded-Proto` header value.</span></span>
* <span data-ttu-id="08729-134">[HttpContext. Request. host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host): impostare usando il `X-Forwarded-Host` valore dell'intestazione.</span><span class="sxs-lookup"><span data-stu-id="08729-134">[HttpContext.Request.Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host): Set using the `X-Forwarded-Host` header value.</span></span>

<span data-ttu-id="08729-135">È possibile configurare le [impostazioni predefinite](#forwarded-headers-middleware-options) del middleware delle intestazioni inoltrate.</span><span class="sxs-lookup"><span data-stu-id="08729-135">Forwarded Headers Middleware [default settings](#forwarded-headers-middleware-options) can be configured.</span></span> <span data-ttu-id="08729-136">Le impostazioni predefinite sono le seguenti:</span><span class="sxs-lookup"><span data-stu-id="08729-136">The default settings are:</span></span>

* <span data-ttu-id="08729-137">È presente *un solo proxy* tra l'app e l'origine delle richieste.</span><span class="sxs-lookup"><span data-stu-id="08729-137">There is only *one proxy* between the app and the source of the requests.</span></span>
* <span data-ttu-id="08729-138">Sono configurati indirizzi di loopback solo per reti e proxy noti.</span><span class="sxs-lookup"><span data-stu-id="08729-138">Only loopback addresses are configured for known proxies and known networks.</span></span>
* <span data-ttu-id="08729-139">Le intestazioni inoltrate sono denominate `X-Forwarded-For` e `X-Forwarded-Proto`.</span><span class="sxs-lookup"><span data-stu-id="08729-139">The forwarded headers are named `X-Forwarded-For` and `X-Forwarded-Proto`.</span></span>

<span data-ttu-id="08729-140">Non tutte le appliance di rete aggiungono le intestazioni `X-Forwarded-For` e `X-Forwarded-Proto` senza alcuna configurazione aggiuntiva.</span><span class="sxs-lookup"><span data-stu-id="08729-140">Not all network appliances add the `X-Forwarded-For` and `X-Forwarded-Proto` headers without additional configuration.</span></span> <span data-ttu-id="08729-141">Se le richieste trasmesse tramite proxy non contengono queste intestazioni quando raggiungono l'app, fare riferimento alle indicazioni del produttore dell'appliance.</span><span class="sxs-lookup"><span data-stu-id="08729-141">Consult your appliance manufacturer's guidance if proxied requests don't contain these headers when they reach the app.</span></span> <span data-ttu-id="08729-142">Se l'appliance usa nomi di intestazione diversi da `X-Forwarded-For` e `X-Forwarded-Proto`, impostare le opzioni <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> e <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> in modo che corrispondano ai nomi di intestazione usati dall'appliance.</span><span class="sxs-lookup"><span data-stu-id="08729-142">If the appliance uses different header names than `X-Forwarded-For` and `X-Forwarded-Proto`, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the appliance.</span></span> <span data-ttu-id="08729-143">Per altre informazioni, vedere [Opzioni del middleware delle intestazioni inoltrate](#forwarded-headers-middleware-options) e [Configurazione per un proxy che usa nomi di intestazione diversi](#configuration-for-a-proxy-that-uses-different-header-names).</span><span class="sxs-lookup"><span data-stu-id="08729-143">For more information, see [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) and [Configuration for a proxy that uses different header names](#configuration-for-a-proxy-that-uses-different-header-names).</span></span>

## <a name="iisiis-express-and-aspnet-core-module"></a><span data-ttu-id="08729-144">IIS/IIS Express e modulo Core ASP.NET</span><span class="sxs-lookup"><span data-stu-id="08729-144">IIS/IIS Express and ASP.NET Core Module</span></span>

<span data-ttu-id="08729-145">Il middleware delle intestazioni inoltrate è abilitato per impostazione predefinita dal [middleware di integrazione IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) quando l'app è ospitata [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) dietro IIS e il modulo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="08729-145">Forwarded Headers Middleware is enabled by default by [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when the app is hosted [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind IIS and the ASP.NET Core Module.</span></span> <span data-ttu-id="08729-146">Il middleware delle intestazioni inoltrate viene attivato per l'esecuzione prima nella pipeline del middleware con una configurazione con restrizioni specifica per il modulo ASP.NET Core per motivi di attendibilità delle intestazioni inoltrate, ad esempio lo [spoofing degli indirizzi IP](https://www.iplocation.net/ip-spoofing).</span><span class="sxs-lookup"><span data-stu-id="08729-146">Forwarded Headers Middleware is activated to run first in the middleware pipeline with a restricted configuration specific to the ASP.NET Core Module due to trust concerns with forwarded headers (for example, [IP spoofing](https://www.iplocation.net/ip-spoofing)).</span></span> <span data-ttu-id="08729-147">Il middleware è configurato per inoltrare le intestazioni `X-Forwarded-For` e `X-Forwarded-Proto` ed è limitato a un singolo proxy localhost.</span><span class="sxs-lookup"><span data-stu-id="08729-147">The middleware is configured to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers and is restricted to a single localhost proxy.</span></span> <span data-ttu-id="08729-148">Se è richiesta una configurazione aggiuntiva, vedere [Opzioni del middleware delle intestazioni inoltrate](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="08729-148">If additional configuration is required, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>

## <a name="other-proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="08729-149">Altri scenari con server proxy e servizi di bilanciamento del carico</span><span class="sxs-lookup"><span data-stu-id="08729-149">Other proxy server and load balancer scenarios</span></span>

<span data-ttu-id="08729-150">Se non si usa il [middleware di integrazione IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) per l'hosting [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model), il middleware delle intestazioni inoltrate non è abilitato per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="08729-150">Outside of using [IIS Integration](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when hosting [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model), Forwarded Headers Middleware isn't enabled by default.</span></span> <span data-ttu-id="08729-151">Il middleware delle intestazioni inoltrate deve essere abilitato per consentire a un'app di inoltrare le intestazioni inoltrate del processo con <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>.</span><span class="sxs-lookup"><span data-stu-id="08729-151">Forwarded Headers Middleware must be enabled for an app to process forwarded headers with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>.</span></span> <span data-ttu-id="08729-152">Dopo aver abilitato il middleware, se non sono specificate opzioni <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> per il middleware, le intestazioni [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) predefinite sono [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="08729-152">After enabling the middleware if no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified to the middleware, the default [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span>

<span data-ttu-id="08729-153">Configurare il middleware con <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> per l'inoltro delle intestazioni `X-Forwarded-For` e `X-Forwarded-Proto` in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="08729-153">Configure the middleware with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers in `Startup.ConfigureServices`.</span></span>

<a name="fhmo"></a>

### <a name="forwarded-headers-middleware-order"></a><span data-ttu-id="08729-154">Ordine middleware intestazioni inoltri</span><span class="sxs-lookup"><span data-stu-id="08729-154">Forwarded Headers Middleware order</span></span>

<span data-ttu-id="08729-155">Il middleware delle intestazioni con inoltri deve essere eseguito prima di altri middleware.</span><span class="sxs-lookup"><span data-stu-id="08729-155">Forwarded Headers Middleware should run before other middleware.</span></span> <span data-ttu-id="08729-156">Questo ordine garantisce che il middleware basato sulle intestazioni inoltrate possa usare i valori di intestazione per l'elaborazione.</span><span class="sxs-lookup"><span data-stu-id="08729-156">This ordering ensures that the middleware relying on forwarded headers information can consume the header values for processing.</span></span> <span data-ttu-id="08729-157">Il middleware delle intestazioni con inoltri può essere eseguito dopo la diagnostica e la gestione degli errori, ma deve essere eseguito prima di chiamare `UseHsts` :</span><span class="sxs-lookup"><span data-stu-id="08729-157">Forwarded Headers Middleware can run after diagnostics and error handling, but it must be run before calling `UseHsts`:</span></span>

[!code-csharp[](~/host-and-deploy/proxy-load-balancer/3.1samples/Startup.cs?name=snippet&highlight=13-17,25,30)]

<span data-ttu-id="08729-158">In alternativa, chiamare `UseForwardedHeaders` prima di diagnostica:</span><span class="sxs-lookup"><span data-stu-id="08729-158">Alternatively, call `UseForwardedHeaders` before diagnostics:</span></span>

[!code-csharp[](~/host-and-deploy/proxy-load-balancer/3.1samples/Startup2.cs?name=snippet)]

> [!NOTE]
> <span data-ttu-id="08729-159">Se non vengono specificate <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> in `Startup.ConfigureServices` o direttamente nel metodo di estensione con <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>, le intestazioni predefinite da inoltrare sono [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="08729-159">If no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified in `Startup.ConfigureServices` or directly to the extension method with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>, the default headers to forward are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> <span data-ttu-id="08729-160">La proprietà <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> deve essere configurata con le intestazioni da inoltrare.</span><span class="sxs-lookup"><span data-stu-id="08729-160">The <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> property must be configured with the headers to forward.</span></span>

## <a name="nginx-configuration"></a><span data-ttu-id="08729-161">Configurazione Nginx</span><span class="sxs-lookup"><span data-stu-id="08729-161">Nginx configuration</span></span>

<span data-ttu-id="08729-162">Per inoltrare le intestazioni `X-Forwarded-For` e `X-Forwarded-Proto`, vedere <xref:host-and-deploy/linux-nginx#configure-nginx>.</span><span class="sxs-lookup"><span data-stu-id="08729-162">To forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers, see <xref:host-and-deploy/linux-nginx#configure-nginx>.</span></span> <span data-ttu-id="08729-163">Per altre informazioni, vedere [NGINX: Using the Forwarded header](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/) (NGINX: Uso dell'intestazione Forwarded).</span><span class="sxs-lookup"><span data-stu-id="08729-163">For more information, see [NGINX: Using the Forwarded header](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/).</span></span>

## <a name="apache-configuration"></a><span data-ttu-id="08729-164">Configurazione Apache</span><span class="sxs-lookup"><span data-stu-id="08729-164">Apache configuration</span></span>

<span data-ttu-id="08729-165">`X-Forwarded-For` viene aggiunta automaticamente. Vedere [Apache Module mod_proxy: Reverse Proxy Request Headers](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers) (Modulo Apache mod_proxy: Intestazioni delle richieste del proxy inverso).</span><span class="sxs-lookup"><span data-stu-id="08729-165">`X-Forwarded-For` is added automatically (see [Apache Module mod_proxy: Reverse Proxy Request Headers](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)).</span></span> <span data-ttu-id="08729-166">Per informazioni su come inoltrare l'intestazione `X-Forwarded-Proto`, vedere <xref:host-and-deploy/linux-apache#configure-apache>.</span><span class="sxs-lookup"><span data-stu-id="08729-166">For information on how to forward the `X-Forwarded-Proto` header, see <xref:host-and-deploy/linux-apache#configure-apache>.</span></span>

## <a name="forwarded-headers-middleware-options"></a><span data-ttu-id="08729-167">Opzioni del middleware delle intestazioni inoltrate</span><span class="sxs-lookup"><span data-stu-id="08729-167">Forwarded Headers Middleware options</span></span>

<span data-ttu-id="08729-168"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> controllano il comportamento del middleware delle intestazioni inoltrate.</span><span class="sxs-lookup"><span data-stu-id="08729-168"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> control the behavior of the Forwarded Headers Middleware.</span></span> <span data-ttu-id="08729-169">L'esempio seguente modifica i valori predefiniti:</span><span class="sxs-lookup"><span data-stu-id="08729-169">The following example changes the default values:</span></span>

* <span data-ttu-id="08729-170">Limitare il numero di voci nelle intestazioni inoltrate a `2`.</span><span class="sxs-lookup"><span data-stu-id="08729-170">Limit the number of entries in the forwarded headers to `2`.</span></span>
* <span data-ttu-id="08729-171">Aggiungere un indirizzo proxy noto di `127.0.10.1`.</span><span class="sxs-lookup"><span data-stu-id="08729-171">Add a known proxy address of `127.0.10.1`.</span></span>
* <span data-ttu-id="08729-172">Modificare il nome dell'intestazione inoltrata dal nome predefinito `X-Forwarded-For` a `X-Forwarded-For-My-Custom-Header-Name`.</span><span class="sxs-lookup"><span data-stu-id="08729-172">Change the forwarded header name from the default `X-Forwarded-For` to `X-Forwarded-For-My-Custom-Header-Name`.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardLimit = 2;
    options.KnownProxies.Add(IPAddress.Parse("127.0.10.1"));
    options.ForwardedForHeaderName = "X-Forwarded-For-My-Custom-Header-Name";
});
```

| <span data-ttu-id="08729-173">Opzione</span><span class="sxs-lookup"><span data-stu-id="08729-173">Option</span></span> | <span data-ttu-id="08729-174">Descrizione</span><span class="sxs-lookup"><span data-stu-id="08729-174">Description</span></span> |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> | <span data-ttu-id="08729-175">Limita gli host mediante l'intestazione `X-Forwarded-Host` ai valori specificati.</span><span class="sxs-lookup"><span data-stu-id="08729-175">Restricts hosts by the `X-Forwarded-Host` header to the values provided.</span></span><ul><li><span data-ttu-id="08729-176">I valori vengono confrontati tramite ordinal-ignore-case.</span><span class="sxs-lookup"><span data-stu-id="08729-176">Values are compared using ordinal-ignore-case.</span></span></li><li><span data-ttu-id="08729-177">I numeri di porta devono essere esclusi.</span><span class="sxs-lookup"><span data-stu-id="08729-177">Port numbers must be excluded.</span></span></li><li><span data-ttu-id="08729-178">Se l'elenco è vuoto, sono consentiti tutti gli host.</span><span class="sxs-lookup"><span data-stu-id="08729-178">If the list is empty, all hosts are allowed.</span></span></li><li><span data-ttu-id="08729-179">Un carattere jolly di primo livello `*` consente tutti gli host non vuoti.</span><span class="sxs-lookup"><span data-stu-id="08729-179">A top-level wildcard `*` allows all non-empty hosts.</span></span></li><li><span data-ttu-id="08729-180">I caratteri jolly per i sottodomini sono consentiti, ma non corrispondono al dominio radice.</span><span class="sxs-lookup"><span data-stu-id="08729-180">Subdomain wildcards are permitted but don't match the root domain.</span></span> <span data-ttu-id="08729-181">Ad esempio, `*.contoso.com` corrisponde al sottodominio `foo.contoso.com`, ma non al dominio radice `contoso.com`.</span><span class="sxs-lookup"><span data-stu-id="08729-181">For example, `*.contoso.com` matches the subdomain `foo.contoso.com` but not the root domain `contoso.com`.</span></span></li><li><span data-ttu-id="08729-182">I nomi host Unicode sono consentiti ma vengono convertiti in [Punycode](https://tools.ietf.org/html/rfc3492) per la corrispondenza.</span><span class="sxs-lookup"><span data-stu-id="08729-182">Unicode host names are allowed but are converted to [Punycode](https://tools.ietf.org/html/rfc3492) for matching.</span></span></li><li><span data-ttu-id="08729-183">Gli [indirizzi IPv6](https://tools.ietf.org/html/rfc4291) devono includere le parentesi quadre di delimitazione ed essere in [formato convenzionale](https://tools.ietf.org/html/rfc4291#section-2.2), ad esempio `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`.</span><span class="sxs-lookup"><span data-stu-id="08729-183">[IPv6 addresses](https://tools.ietf.org/html/rfc4291) must include bounding brackets and be in [conventional form](https://tools.ietf.org/html/rfc4291#section-2.2) (for example, `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`).</span></span> <span data-ttu-id="08729-184">Per gli indirizzi IPv6 non viene applicata la distinzione tra maiuscole e minuscole per la verifica dell'uguaglianza logica tra i diversi formati e non viene eseguita alcuna canonizzazione.</span><span class="sxs-lookup"><span data-stu-id="08729-184">IPv6 addresses aren't special-cased to check for logical equality between different formats, and no canonicalization is performed.</span></span></li><li><span data-ttu-id="08729-185">La mancata limitazione degli host consentiti potrebbe permettere a un utente malintenzionato di eseguire lo spoofing dei collegamenti generati dal servizio.</span><span class="sxs-lookup"><span data-stu-id="08729-185">Failure to restrict the allowed hosts may allow an attacker to spoof links generated by the service.</span></span></li></ul><span data-ttu-id="08729-186">Il valore predefinito è un insieme `IList<string>` vuoto.</span><span class="sxs-lookup"><span data-stu-id="08729-186">The default value is an empty `IList<string>`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> | <span data-ttu-id="08729-187">Usare l'intestazione specificata da questa proprietà anziché quella specificata da [ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName).</span><span class="sxs-lookup"><span data-stu-id="08729-187">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName).</span></span> <span data-ttu-id="08729-188">Questa opzione viene usata quando il proxy o il server d'inoltro non usa l'intestazione `X-Forwarded-For` ma usa un'altra intestazione per inoltrare le informazioni.</span><span class="sxs-lookup"><span data-stu-id="08729-188">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-For` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="08729-189">Il valore predefinito è `X-Forwarded-For`.</span><span class="sxs-lookup"><span data-stu-id="08729-189">The default is `X-Forwarded-For`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> | <span data-ttu-id="08729-190">Identifica i server d'inoltro che devono essere elaborati.</span><span class="sxs-lookup"><span data-stu-id="08729-190">Identifies which forwarders should be processed.</span></span> <span data-ttu-id="08729-191">Vedere [ForwardedHeaders Enum](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) (Enumerazione ForwardedHeaders) per l'elenco dei campi applicabili.</span><span class="sxs-lookup"><span data-stu-id="08729-191">See the [ForwardedHeaders Enum](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) for the list of fields that apply.</span></span> <span data-ttu-id="08729-192">I valori tipici assegnati a questa proprietà sono `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`.</span><span class="sxs-lookup"><span data-stu-id="08729-192">Typical values assigned to this property are `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`.</span></span><br><br><span data-ttu-id="08729-193">Il valore predefinito è [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="08729-193">The default value is [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHostHeaderName> | <span data-ttu-id="08729-194">Usare l'intestazione specificata da questa proprietà anziché quella specificata da [ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName).</span><span class="sxs-lookup"><span data-stu-id="08729-194">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName).</span></span> <span data-ttu-id="08729-195">Questa opzione viene usata quando il proxy o il server d'inoltro non usa l'intestazione `X-Forwarded-Host` ma usa un'altra intestazione per inoltrare le informazioni.</span><span class="sxs-lookup"><span data-stu-id="08729-195">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Host` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="08729-196">Il valore predefinito è `X-Forwarded-Host`.</span><span class="sxs-lookup"><span data-stu-id="08729-196">The default is `X-Forwarded-Host`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> | <span data-ttu-id="08729-197">Usare l'intestazione specificata da questa proprietà anziché quella specificata da [ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName).</span><span class="sxs-lookup"><span data-stu-id="08729-197">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName).</span></span> <span data-ttu-id="08729-198">Questa opzione viene usata quando il proxy o il server d'inoltro non usa l'intestazione `X-Forwarded-Proto` ma usa un'altra intestazione per inoltrare le informazioni.</span><span class="sxs-lookup"><span data-stu-id="08729-198">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Proto` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="08729-199">Il valore predefinito è `X-Forwarded-Proto`.</span><span class="sxs-lookup"><span data-stu-id="08729-199">The default is `X-Forwarded-Proto`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardLimit> | <span data-ttu-id="08729-200">Limita il numero di voci nelle intestazioni elaborate.</span><span class="sxs-lookup"><span data-stu-id="08729-200">Limits the number of entries in the headers that are processed.</span></span> <span data-ttu-id="08729-201">Impostare su `null` per disabilitare il limite, ma solo se è configurato `KnownProxies` o `KnownNetworks`.</span><span class="sxs-lookup"><span data-stu-id="08729-201">Set to `null` to disable the limit, but this should only be done if `KnownProxies` or `KnownNetworks` are configured.</span></span> <span data-ttu-id="08729-202">L'impostazione di un valore non `null` è una precauzione (ma non una garanzia) per proteggersi da proxy non configurati correttamente e richieste dannose provenienti da canali laterali in rete.</span><span class="sxs-lookup"><span data-stu-id="08729-202">Setting a non-`null` value is a precaution (but not a guarantee) to guard against misconfigured proxies and malicious requests arriving from side-channels on the network.</span></span><br><br><span data-ttu-id="08729-203">Il middleware delle intestazioni inoltrate elabora le intestazioni in ordine inverso da destra a sinistra.</span><span class="sxs-lookup"><span data-stu-id="08729-203">Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="08729-204">Se viene usato il valore predefinito (`1`), viene elaborato solo il valore più a destra delle intestazioni, a meno che non venga aumentato il valore di `ForwardLimit`.</span><span class="sxs-lookup"><span data-stu-id="08729-204">If the default value (`1`) is used, only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span><br><br><span data-ttu-id="08729-205">Il valore predefinito è `1`.</span><span class="sxs-lookup"><span data-stu-id="08729-205">The default is `1`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks> | <span data-ttu-id="08729-206">Intervalli di indirizzi delle reti note da cui accettare le intestazioni inoltrate.</span><span class="sxs-lookup"><span data-stu-id="08729-206">Address ranges of known networks to accept forwarded headers from.</span></span> <span data-ttu-id="08729-207">Specificare gli intervalli IP usando la notazione CIDR (Classless Interdomain Routing).</span><span class="sxs-lookup"><span data-stu-id="08729-207">Provide IP ranges using Classless Interdomain Routing (CIDR) notation.</span></span><br><br><span data-ttu-id="08729-208">Se il server usa socket dual mode, gli indirizzi IPv4 vengono forniti in un formato IPv6 (ad esempio, `10.0.0.1` in IPv4 rappresentato in IPv6 come `::ffff:10.0.0.1`).</span><span class="sxs-lookup"><span data-stu-id="08729-208">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="08729-209">Vedere [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="08729-209">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="08729-210">Determinare se questo formato è richiesto esaminando [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span><span class="sxs-lookup"><span data-stu-id="08729-210">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="08729-211">Per altre informazioni, vedere la sezione [Configurazione per un indirizzo IPv4 rappresentato come indirizzo IPv6](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address).</span><span class="sxs-lookup"><span data-stu-id="08729-211">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="08729-212">Il valore predefinito è un oggetto `IList`\<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> contenente una singola voce per `IPAddress.Loopback`.</span><span class="sxs-lookup"><span data-stu-id="08729-212">The default is an `IList`\<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> containing a single entry for `IPAddress.Loopback`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies> | <span data-ttu-id="08729-213">Indirizzi dei proxy noti da cui accettare le intestazioni inoltrate.</span><span class="sxs-lookup"><span data-stu-id="08729-213">Addresses of known proxies to accept forwarded headers from.</span></span> <span data-ttu-id="08729-214">Usare `KnownProxies` per specificare le corrispondenze esatte degli indirizzi IP.</span><span class="sxs-lookup"><span data-stu-id="08729-214">Use `KnownProxies` to specify exact IP address matches.</span></span><br><br><span data-ttu-id="08729-215">Se il server usa socket dual mode, gli indirizzi IPv4 vengono forniti in un formato IPv6 (ad esempio, `10.0.0.1` in IPv4 rappresentato in IPv6 come `::ffff:10.0.0.1`).</span><span class="sxs-lookup"><span data-stu-id="08729-215">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="08729-216">Vedere [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="08729-216">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="08729-217">Determinare se questo formato è richiesto esaminando [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span><span class="sxs-lookup"><span data-stu-id="08729-217">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="08729-218">Per altre informazioni, vedere la sezione [Configurazione per un indirizzo IPv4 rappresentato come indirizzo IPv6](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address).</span><span class="sxs-lookup"><span data-stu-id="08729-218">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="08729-219">Il valore predefinito è un oggetto `IList`\<<xref:System.Net.IPAddress>> contenente una singola voce per `IPAddress.IPv6Loopback`.</span><span class="sxs-lookup"><span data-stu-id="08729-219">The default is an `IList`\<<xref:System.Net.IPAddress>> containing a single entry for `IPAddress.IPv6Loopback`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalForHeaderName> | <span data-ttu-id="08729-220">Usare l'intestazione specificata da questa proprietà anziché quella specificata da [ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).</span><span class="sxs-lookup"><span data-stu-id="08729-220">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).</span></span><br><br><span data-ttu-id="08729-221">Il valore predefinito è `X-Original-For`.</span><span class="sxs-lookup"><span data-stu-id="08729-221">The default is `X-Original-For`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalHostHeaderName> | <span data-ttu-id="08729-222">Usare l'intestazione specificata da questa proprietà anziché quella specificata da [ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).</span><span class="sxs-lookup"><span data-stu-id="08729-222">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).</span></span><br><br><span data-ttu-id="08729-223">Il valore predefinito è `X-Original-Host`.</span><span class="sxs-lookup"><span data-stu-id="08729-223">The default is `X-Original-Host`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalProtoHeaderName> | <span data-ttu-id="08729-224">Usare l'intestazione specificata da questa proprietà anziché quella specificata da [ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).</span><span class="sxs-lookup"><span data-stu-id="08729-224">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).</span></span><br><br><span data-ttu-id="08729-225">Il valore predefinito è `X-Original-Proto`.</span><span class="sxs-lookup"><span data-stu-id="08729-225">The default is `X-Original-Proto`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.RequireHeaderSymmetry> | <span data-ttu-id="08729-226">Richiedere il numero di valori di intestazione da sincronizzare tra le intestazioni [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) in fase di elaborazione.</span><span class="sxs-lookup"><span data-stu-id="08729-226">Require the number of header values to be in sync between the [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) being processed.</span></span><br><br><span data-ttu-id="08729-227">Il valore predefinito in ASP.NET Core 1.x è `true`.</span><span class="sxs-lookup"><span data-stu-id="08729-227">The default in ASP.NET Core 1.x is `true`.</span></span> <span data-ttu-id="08729-228">Il valore predefinito in ASP.NET Core 2.0 o versione successiva è `false`.</span><span class="sxs-lookup"><span data-stu-id="08729-228">The default in ASP.NET Core 2.0 or later is `false`.</span></span> |

## <a name="scenarios-and-use-cases"></a><span data-ttu-id="08729-229">Scenari e casi d'uso</span><span class="sxs-lookup"><span data-stu-id="08729-229">Scenarios and use cases</span></span>

### <a name="when-it-isnt-possible-to-add-forwarded-headers-and-all-requests-are-secure"></a><span data-ttu-id="08729-230">Quando non è possibile aggiungere le intestazioni inoltrate e tutte le richieste sono sicure</span><span class="sxs-lookup"><span data-stu-id="08729-230">When it isn't possible to add forwarded headers and all requests are secure</span></span>

<span data-ttu-id="08729-231">In alcuni casi, potrebbe non essere possibile aggiungere le intestazioni inoltrate alle richieste trasmesse tramite proxy all'app.</span><span class="sxs-lookup"><span data-stu-id="08729-231">In some cases, it might not be possible to add forwarded headers to the requests proxied to the app.</span></span> <span data-ttu-id="08729-232">Se il proxy impone che tutte le richieste esterne pubbliche siano HTTPS, lo schema può essere impostato manualmente in `Startup.Configure` prima di usare qualsiasi tipo di middleware:</span><span class="sxs-lookup"><span data-stu-id="08729-232">If the proxy is enforcing that all public external requests are HTTPS, the scheme can be manually set in `Startup.Configure` before using any type of middleware:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.Scheme = "https";
    return next();
});
```

<span data-ttu-id="08729-233">Questo codice può essere disabilitato tramite una variabile di ambiente o un'altra impostazione di configurazione in un ambiente di sviluppo o di gestione temporanea.</span><span class="sxs-lookup"><span data-stu-id="08729-233">This code can be disabled with an environment variable or other configuration setting in a development or staging environment.</span></span>

### <a name="deal-with-path-base-and-proxies-that-change-the-request-path"></a><span data-ttu-id="08729-234">Gestire la base del percorso e i proxy che modificano il percorso della richiesta</span><span class="sxs-lookup"><span data-stu-id="08729-234">Deal with path base and proxies that change the request path</span></span>

<span data-ttu-id="08729-235">Alcuni proxy passano il percorso senza modifiche, ma con un percorso di base dell'app che deve essere rimosso per consentire il corretto funzionamento del routing.</span><span class="sxs-lookup"><span data-stu-id="08729-235">Some proxies pass the path intact but with an app base path that should be removed so that routing works properly.</span></span> <span data-ttu-id="08729-236">Il middleware [UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) suddivide il percorso in [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) e il percorso di base dell'app in [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).</span><span class="sxs-lookup"><span data-stu-id="08729-236">[UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) middleware splits the path into [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) and the app base path into [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).</span></span>

<span data-ttu-id="08729-237">Se `/foo` è il percorso di base dell'app per un percorso proxy passato come `/foo/api/1`, il middleware imposta `Request.PathBase` su `/foo` e `Request.Path` su `/api/1` con il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="08729-237">If `/foo` is the app base path for a proxy path passed as `/foo/api/1`, the middleware sets `Request.PathBase` to `/foo` and `Request.Path` to `/api/1` with the following command:</span></span>

```csharp
app.UsePathBase("/foo");
```

<span data-ttu-id="08729-238">Il percorso originale e la base del percorso vengono riapplicati quando il middleware viene chiamato nuovamente in direzione inversa.</span><span class="sxs-lookup"><span data-stu-id="08729-238">The original path and path base are reapplied when the middleware is called again in reverse.</span></span> <span data-ttu-id="08729-239">Per altre informazioni dell'elaborazione degli ordini del middleware, vedere <xref:fundamentals/middleware/index>.</span><span class="sxs-lookup"><span data-stu-id="08729-239">For more information on middleware order processing, see <xref:fundamentals/middleware/index>.</span></span>

<span data-ttu-id="08729-240">Se il proxy taglia il percorso (ad esempio, inoltrando `/foo/api/1` a `/api/1`), correggere i reindirizzamenti e i collegamenti impostando la proprietà [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) della richiesta:</span><span class="sxs-lookup"><span data-stu-id="08729-240">If the proxy trims the path (for example, forwarding `/foo/api/1` to `/api/1`), fix redirects and links by setting the request's [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) property:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.PathBase = new PathString("/foo");
    return next();
});
```

<span data-ttu-id="08729-241">Se il proxy aggiunge i dati del percorso, eliminare parte del percorso per correggere i reindirizzamenti e i collegamenti usando <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> ed eseguendo l'assegnazione alla proprietà <xref:Microsoft.AspNetCore.Http.HttpRequest.Path>:</span><span class="sxs-lookup"><span data-stu-id="08729-241">If the proxy is adding path data, discard part of the path to fix redirects and links by using <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> and assigning to the <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> property:</span></span>

```csharp
app.Use((context, next) =>
{
    if (context.Request.Path.StartsWithSegments("/foo", out var remainder))
    {
        context.Request.Path = remainder;
    }

    return next();
});
```

### <a name="configuration-for-a-proxy-that-uses-different-header-names"></a><span data-ttu-id="08729-242">Configurazione per un proxy che usa nomi di intestazione diversi</span><span class="sxs-lookup"><span data-stu-id="08729-242">Configuration for a proxy that uses different header names</span></span>

<span data-ttu-id="08729-243">Se il proxy non usa intestazioni denominate `X-Forwarded-For` e `X-Forwarded-Proto` per inoltrare l'indirizzo proxy o la porta e le informazioni dello scherma originali, impostare le opzioni <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> e <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> in modo che corrispondano ai nomi di intestazione usati dal proxy:</span><span class="sxs-lookup"><span data-stu-id="08729-243">If the proxy doesn't use headers named `X-Forwarded-For` and `X-Forwarded-Proto` to forward the proxy address/port and originating scheme information, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the proxy:</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedForHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-For_Header";
    options.ForwardedProtoHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-Proto_Header";
});
```

### <a name="configuration-for-an-ipv4-address-represented-as-an-ipv6-address"></a><span data-ttu-id="08729-244">Configurazione per un indirizzo IPv4 rappresentato come indirizzo IPv6</span><span class="sxs-lookup"><span data-stu-id="08729-244">Configuration for an IPv4 address represented as an IPv6 address</span></span>

<span data-ttu-id="08729-245">Se il server usa socket dual mode, gli indirizzi IPv4 vengono forniti in un formato IPv6 (ad esempio, `10.0.0.1` in IPv4 rappresentato in IPv6 come `::ffff:10.0.0.1` o `::ffff:a00:1`).</span><span class="sxs-lookup"><span data-stu-id="08729-245">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1` or `::ffff:a00:1`).</span></span> <span data-ttu-id="08729-246">Vedere [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="08729-246">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="08729-247">Determinare se questo formato è richiesto esaminando [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span><span class="sxs-lookup"><span data-stu-id="08729-247">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span>

<span data-ttu-id="08729-248">Nell'esempio seguente un indirizzo di rete che fornisce intestazioni inoltrate viene aggiunto all'elenco `KnownNetworks` in formato IPv6.</span><span class="sxs-lookup"><span data-stu-id="08729-248">In the following example, a network address that supplies forwarded headers is added to the `KnownNetworks` list in IPv6 format.</span></span>

<span data-ttu-id="08729-249">Indirizzo IPv4: `10.11.12.1/8`</span><span class="sxs-lookup"><span data-stu-id="08729-249">IPv4 address: `10.11.12.1/8`</span></span>

<span data-ttu-id="08729-250">Indirizzo IPv6 convertito: `::ffff:10.11.12.1`</span><span class="sxs-lookup"><span data-stu-id="08729-250">Converted IPv6 address: `::ffff:10.11.12.1`</span></span>  
<span data-ttu-id="08729-251">Lunghezza del prefisso convertito: 104</span><span class="sxs-lookup"><span data-stu-id="08729-251">Converted prefix length: 104</span></span>

<span data-ttu-id="08729-252">È anche possibile specificare l'indirizzo in formato esadecimale (`10.11.12.1` rappresentato in IPv6 come `::ffff:0a0b:0c01`).</span><span class="sxs-lookup"><span data-stu-id="08729-252">You can also supply the address in hexadecimal format (`10.11.12.1` represented in IPv6 as `::ffff:0a0b:0c01`).</span></span> <span data-ttu-id="08729-253">Durante la conversione di un indirizzo IPv4 in IPv6, aggiungere 96 alla lunghezza del prefisso CIDR (`8` nell'esempio) per tenere conto del prefisso IPv6 aggiuntivo `::ffff:` (8 + 96 = 104).</span><span class="sxs-lookup"><span data-stu-id="08729-253">When converting an IPv4 address to IPv6, add 96 to the CIDR Prefix Length (`8` in the example) to account for the additional `::ffff:` IPv6 prefix (8 + 96 = 104).</span></span> 

```csharp
// To access IPNetwork and IPAddress, add the following namespaces:
// using System.Net;
// using Microsoft.AspNetCore.HttpOverrides;
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedHeaders =
        ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto;
    options.KnownNetworks.Add(new IPNetwork(
        IPAddress.Parse("::ffff:10.11.12.1"), 104));
});
```

## <a name="forward-the-scheme-for-linux-and-non-iis-reverse-proxies"></a><span data-ttu-id="08729-254">Inoltrare lo schema per Linux e proxy inversi non IIS</span><span class="sxs-lookup"><span data-stu-id="08729-254">Forward the scheme for Linux and non-IIS reverse proxies</span></span>

<span data-ttu-id="08729-255">Con le app che chiamano <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> e <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> il sito si ritrova in un ciclo infinito se distribuito in un servizio app Linux di Azure, in una macchina virtuale Linux di Azure o dietro eventuali altri proxy inversi diversi da IIS.</span><span class="sxs-lookup"><span data-stu-id="08729-255">Apps that call <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> and <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> put a site into an infinite loop if deployed to an Azure Linux App Service, Azure Linux virtual machine (VM), or behind any other reverse proxy besides IIS.</span></span> <span data-ttu-id="08729-256">TLS viene terminato dal proxy inverso e Kestrel non viene a conoscenza dello schema di richiesta corretto.</span><span class="sxs-lookup"><span data-stu-id="08729-256">TLS is terminated by the reverse proxy, and Kestrel isn't made aware of the correct request scheme.</span></span> <span data-ttu-id="08729-257">Si verificano errori anche per OAuth e OIDC in questa configurazione perché generano reindirizzamenti non corretti.</span><span class="sxs-lookup"><span data-stu-id="08729-257">OAuth and OIDC also fail in this configuration because they generate incorrect redirects.</span></span> <span data-ttu-id="08729-258"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> aggiunge e configura il middleware delle intestazioni inoltrate in caso di esecuzione dietro IIS, ma non esiste alcuna configurazione automatica corrispondente per Linux (integrazione di Apache o Nginx).</span><span class="sxs-lookup"><span data-stu-id="08729-258"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> adds and configures Forwarded Headers Middleware when running behind IIS, but there's no matching automatic configuration for Linux (Apache or Nginx integration).</span></span>

<span data-ttu-id="08729-259">Per inoltrare lo schema dal proxy in scenari non IIS, aggiungere e configurare il middleware delle intestazioni inoltrate.</span><span class="sxs-lookup"><span data-stu-id="08729-259">To forward the scheme from the proxy in non-IIS scenarios, add and configure Forwarded Headers Middleware.</span></span> <span data-ttu-id="08729-260">In `Startup.ConfigureServices` usare il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="08729-260">In `Startup.ConfigureServices`, use the following code:</span></span>

```csharp
// using Microsoft.AspNetCore.HttpOverrides;

if (string.Equals(
    Environment.GetEnvironmentVariable("ASPNETCORE_FORWARDEDHEADERS_ENABLED"), 
    "true", StringComparison.OrdinalIgnoreCase))
{
    services.Configure<ForwardedHeadersOptions>(options =>
    {
        options.ForwardedHeaders = ForwardedHeaders.XForwardedFor | 
            ForwardedHeaders.XForwardedProto;
        // Only loopback proxies are allowed by default.
        // Clear that restriction because forwarders are enabled by explicit 
        // configuration.
        options.KnownNetworks.Clear();
        options.KnownProxies.Clear();
    });
}
```

## <a name="certificate-forwarding"></a><span data-ttu-id="08729-261">Inoltro di certificati</span><span class="sxs-lookup"><span data-stu-id="08729-261">Certificate forwarding</span></span> 

### <a name="azure"></a><span data-ttu-id="08729-262">Azure</span><span class="sxs-lookup"><span data-stu-id="08729-262">Azure</span></span>

<span data-ttu-id="08729-263">Per configurare app Azure servizio per l'invio di certificati, vedere [configurare l'autenticazione reciproca TLS per il servizio app Azure](/azure/app-service/app-service-web-configure-tls-mutual-auth).</span><span class="sxs-lookup"><span data-stu-id="08729-263">To configure Azure App Service for certificate forwarding, see [Configure TLS mutual authentication for Azure App Service](/azure/app-service/app-service-web-configure-tls-mutual-auth).</span></span> <span data-ttu-id="08729-264">Le linee guida seguenti riguardano la configurazione dell'app ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="08729-264">The following guidance pertains to configuring the ASP.NET Core app.</span></span>

<span data-ttu-id="08729-265">In `Startup.Configure` aggiungere il codice seguente prima della chiamata a `app.UseAuthentication();` :</span><span class="sxs-lookup"><span data-stu-id="08729-265">In `Startup.Configure`, add the following code before the call to `app.UseAuthentication();`:</span></span>

```csharp
app.UseCertificateForwarding();
```


<span data-ttu-id="08729-266">Configurare il middleware di invio del certificato per specificare il nome dell'intestazione usato da Azure.</span><span class="sxs-lookup"><span data-stu-id="08729-266">Configure Certificate Forwarding Middleware to specify the header name that Azure uses.</span></span> <span data-ttu-id="08729-267">In `Startup.ConfigureServices` aggiungere il codice seguente per configurare l'intestazione da cui il middleware compila un certificato:</span><span class="sxs-lookup"><span data-stu-id="08729-267">In `Startup.ConfigureServices`, add the following code to configure the header from which the middleware builds a certificate:</span></span>

```csharp
services.AddCertificateForwarding(options =>
    options.CertificateHeader = "X-ARR-ClientCert");
```

### <a name="other-web-proxies"></a><span data-ttu-id="08729-268">Altri proxy Web</span><span class="sxs-lookup"><span data-stu-id="08729-268">Other web proxies</span></span>

<span data-ttu-id="08729-269">Se viene usato un proxy che non è IIS o app Azure Application Request Routing (ARR) del servizio, configurare il proxy in modo che inoltri il certificato ricevuto in un'intestazione HTTP.</span><span class="sxs-lookup"><span data-stu-id="08729-269">If a proxy is used that isn't IIS or Azure App Service's Application Request Routing (ARR), configure the proxy to forward the certificate that it received in an HTTP header.</span></span> <span data-ttu-id="08729-270">In `Startup.Configure` aggiungere il codice seguente prima della chiamata a `app.UseAuthentication();` :</span><span class="sxs-lookup"><span data-stu-id="08729-270">In `Startup.Configure`, add the following code before the call to `app.UseAuthentication();`:</span></span>

```csharp
app.UseCertificateForwarding();
```

<span data-ttu-id="08729-271">Configurare il middleware di invio del certificato per specificare il nome dell'intestazione.</span><span class="sxs-lookup"><span data-stu-id="08729-271">Configure the Certificate Forwarding Middleware to specify the header name.</span></span> <span data-ttu-id="08729-272">In `Startup.ConfigureServices` aggiungere il codice seguente per configurare l'intestazione da cui il middleware compila un certificato:</span><span class="sxs-lookup"><span data-stu-id="08729-272">In `Startup.ConfigureServices`, add the following code to configure the header from which the middleware builds a certificate:</span></span>

```csharp
services.AddCertificateForwarding(options =>
    options.CertificateHeader = "YOUR_CERTIFICATE_HEADER_NAME");
```

<span data-ttu-id="08729-273">Se il proxy non prevede la codifica Base64 del certificato (come nel caso di nginx), impostare l' `HeaderConverter` opzione.</span><span class="sxs-lookup"><span data-stu-id="08729-273">If the proxy isn't base64-encoding the certificate (as is the case with Nginx), set the `HeaderConverter` option.</span></span> <span data-ttu-id="08729-274">Si consideri l'esempio seguente in `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="08729-274">Consider the following example in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddCertificateForwarding(options =>
{
    options.CertificateHeader = "YOUR_CUSTOM_HEADER_NAME";
    options.HeaderConverter = (headerValue) => 
    {
        var clientCertificate = 
           /* some conversion logic to create an X509Certificate2 */
        return clientCertificate;
    }
});
```

## <a name="troubleshoot"></a><span data-ttu-id="08729-275">Risolvere problemi</span><span class="sxs-lookup"><span data-stu-id="08729-275">Troubleshoot</span></span>

<span data-ttu-id="08729-276">Quando le intestazioni non vengono inoltrate come previsto, abilitare la [registrazione](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="08729-276">When headers aren't forwarded as expected, enable [logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="08729-277">Se i log non forniscono informazioni sufficienti per risolvere il problema, enumerare le intestazioni delle richieste ricevute dal server.</span><span class="sxs-lookup"><span data-stu-id="08729-277">If the logs don't provide sufficient information to troubleshoot the problem, enumerate the request headers received by the server.</span></span> <span data-ttu-id="08729-278">Usare il middleware inline per scrivere le intestazioni di richiesta in una risposta dell'app o per registrare le intestazioni.</span><span class="sxs-lookup"><span data-stu-id="08729-278">Use inline middleware to write request headers to an app response or log the headers.</span></span> 

<span data-ttu-id="08729-279">Per scrivere le intestazioni nella risposta dell'app, aggiungere il middleware inline di terminale seguente immediatamente dopo la chiamata a <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="08729-279">To write the headers to the app's response, place the following terminal inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`:</span></span>

```csharp
app.Run(async (context) =>
{
    context.Response.ContentType = "text/plain";

    // Request method, scheme, and path
    await context.Response.WriteAsync(
        $"Request Method: {context.Request.Method}{Environment.NewLine}");
    await context.Response.WriteAsync(
        $"Request Scheme: {context.Request.Scheme}{Environment.NewLine}");
    await context.Response.WriteAsync(
        $"Request Path: {context.Request.Path}{Environment.NewLine}");

    // Headers
    await context.Response.WriteAsync($"Request Headers:{Environment.NewLine}");

    foreach (var header in context.Request.Headers)
    {
        await context.Response.WriteAsync($"{header.Key}: " +
            $"{header.Value}{Environment.NewLine}");
    }

    await context.Response.WriteAsync(Environment.NewLine);

    // Connection: RemoteIp
    await context.Response.WriteAsync(
        $"Request RemoteIp: {context.Connection.RemoteIpAddress}");
});
```

<span data-ttu-id="08729-280">È possibile scrivere nei log invece che nel corpo della risposta.</span><span class="sxs-lookup"><span data-stu-id="08729-280">You can write to logs instead of the response body.</span></span> <span data-ttu-id="08729-281">La scrittura nei log permette il normale funzionamento del sito durante il debug.</span><span class="sxs-lookup"><span data-stu-id="08729-281">Writing to logs allows the site to function normally while debugging.</span></span>

<span data-ttu-id="08729-282">Per scrivere nei log invece che nel corpo della risposta:</span><span class="sxs-lookup"><span data-stu-id="08729-282">To write logs rather than to the response body:</span></span>

* <span data-ttu-id="08729-283">Inserire `ILogger<Startup>` nella classe `Startup` come descritto in [Creare log in Startup](xref:fundamentals/logging/index#create-logs-in-startup).</span><span class="sxs-lookup"><span data-stu-id="08729-283">Inject `ILogger<Startup>` into the `Startup` class as described in [Create logs in Startup](xref:fundamentals/logging/index#create-logs-in-startup).</span></span>
* <span data-ttu-id="08729-284">Aggiungere il middleware inline seguente immediatamente dopo la chiamata a <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="08729-284">Place the following inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`.</span></span>

```csharp
app.Use(async (context, next) =>
{
    // Request method, scheme, and path
    _logger.LogDebug("Request Method: {Method}", context.Request.Method);
    _logger.LogDebug("Request Scheme: {Scheme}", context.Request.Scheme);
    _logger.LogDebug("Request Path: {Path}", context.Request.Path);

    // Headers
    foreach (var header in context.Request.Headers)
    {
        _logger.LogDebug("Header: {Key}: {Value}", header.Key, header.Value);
    }

    // Connection: RemoteIp
    _logger.LogDebug("Request RemoteIp: {RemoteIpAddress}", 
        context.Connection.RemoteIpAddress);

    await next();
});
```

<span data-ttu-id="08729-285">Durante l'elaborazione, i valori di `X-Forwarded-{For|Proto|Host}` vengono spostati in `X-Original-{For|Proto|Host}`.</span><span class="sxs-lookup"><span data-stu-id="08729-285">When processed, `X-Forwarded-{For|Proto|Host}` values are moved to `X-Original-{For|Proto|Host}`.</span></span> <span data-ttu-id="08729-286">Se sono presenti più valori in una determinata intestazione, il middleware delle intestazioni inoltrate elabora le intestazioni in ordine inverso da destra a sinistra.</span><span class="sxs-lookup"><span data-stu-id="08729-286">If there are multiple values in a given header, Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="08729-287">Il valore predefinito di `ForwardLimit` è `1` (uno) e, pertanto, viene elaborato solo il valore più a destra delle intestazioni, a meno che non venga aumentato il valore di `ForwardLimit`.</span><span class="sxs-lookup"><span data-stu-id="08729-287">The default `ForwardLimit` is `1` (one), so only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span>

<span data-ttu-id="08729-288">L'indirizzo IP remoto originale della richiesta deve corrispondere a una voce negli elenchi `KnownProxies` o `KnownNetworks` prima dell'elaborazione delle intestazioni inoltrate.</span><span class="sxs-lookup"><span data-stu-id="08729-288">The request's original remote IP must match an entry in the `KnownProxies` or `KnownNetworks` lists before forwarded headers are processed.</span></span> <span data-ttu-id="08729-289">Questo riduce lo spoofing delle intestazioni, non accettando server di inoltro da proxy non attendibili.</span><span class="sxs-lookup"><span data-stu-id="08729-289">This limits header spoofing by not accepting forwarders from untrusted proxies.</span></span> <span data-ttu-id="08729-290">Quando viene rilevato un proxy sconosciuto, la registrazione indica l'indirizzo del proxy:</span><span class="sxs-lookup"><span data-stu-id="08729-290">When an unknown proxy is detected, logging indicates the address of the proxy:</span></span>

```console
September 20th 2018, 15:49:44.168 Unknown proxy: 10.0.0.100:54321
```

<span data-ttu-id="08729-291">Nell'esempio precedente, 10.0.0.100 è un server proxy.</span><span class="sxs-lookup"><span data-stu-id="08729-291">In the preceding example, 10.0.0.100 is a proxy server.</span></span> <span data-ttu-id="08729-292">Se il server è un proxy attendibile, aggiungere l'indirizzo IP del server a `KnownProxies` (o aggiungere una rete attendibile a `KnownNetworks`) in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="08729-292">If the server is a trusted proxy, add the server's IP address to `KnownProxies` (or add a trusted network to `KnownNetworks`) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="08729-293">Per altre informazioni, vedere la sezione [Opzioni del middleware delle intestazioni inoltrate](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="08729-293">For more information, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) section.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

> [!IMPORTANT]
> <span data-ttu-id="08729-294">Consentire solo a reti e proxy attendibili di inoltrare le intestazioni.</span><span class="sxs-lookup"><span data-stu-id="08729-294">Only allow trusted proxies and networks to forward headers.</span></span> <span data-ttu-id="08729-295">In caso contrario, possono verificarsi attacchi di [spoofing degli indirizzi IP](https://www.iplocation.net/ip-spoofing).</span><span class="sxs-lookup"><span data-stu-id="08729-295">Otherwise, [IP spoofing](https://www.iplocation.net/ip-spoofing) attacks are possible.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="08729-296">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="08729-296">Additional resources</span></span>

* <xref:host-and-deploy/web-farm>
* [<span data-ttu-id="08729-297">Avviso di sicurezza Microsoft CVE-2018-0787: vulnerabilità di elevazione dei privilegi di ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="08729-297">Microsoft Security Advisory CVE-2018-0787: ASP.NET Core Elevation Of Privilege Vulnerability</span></span>](https://github.com/aspnet/Announcements/issues/295)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="08729-298">Nella configurazione consigliata per ASP.NET Core, l'app è ospitata mediante IIS e il modulo ASP.NET Core, Nginx o Apache.</span><span class="sxs-lookup"><span data-stu-id="08729-298">In the recommended configuration for ASP.NET Core, the app is hosted using IIS/ASP.NET Core Module, Nginx, or Apache.</span></span> <span data-ttu-id="08729-299">Server proxy, servizi di bilanciamento del carico e altre appliance di rete spesso nascondono informazioni sulla richiesta prima che questa raggiunga l'app:</span><span class="sxs-lookup"><span data-stu-id="08729-299">Proxy servers, load balancers, and other network appliances often obscure information about the request before it reaches the app:</span></span>

* <span data-ttu-id="08729-300">Quando le richieste HTTPS vengono trasmesse tramite proxy su HTTP, lo schema originale (HTTPS) viene perso e deve essere inoltrato in un'intestazione.</span><span class="sxs-lookup"><span data-stu-id="08729-300">When HTTPS requests are proxied over HTTP, the original scheme (HTTPS) is lost and must be forwarded in a header.</span></span>
* <span data-ttu-id="08729-301">Poiché un'app riceve una richiesta dal proxy e non dall'effettiva origine su Internet o nella rete aziendale, anche l'indirizzo IP di origine del client deve essere inoltrato in un'intestazione.</span><span class="sxs-lookup"><span data-stu-id="08729-301">Because an app receives a request from the proxy and not its true source on the Internet or corporate network, the originating client IP address must also be forwarded in a header.</span></span>

<span data-ttu-id="08729-302">Queste informazioni potrebbero essere importanti per l'elaborazione delle richieste, ad esempio per i reindirizzamenti, l'autenticazione, la generazione di collegamenti, la valutazione dei criteri e la georilevazione dei client.</span><span class="sxs-lookup"><span data-stu-id="08729-302">This information may be important in request processing, for example in redirects, authentication, link generation, policy evaluation, and client geolocation.</span></span>

## <a name="forwarded-headers"></a><span data-ttu-id="08729-303">Intestazioni inoltrate</span><span class="sxs-lookup"><span data-stu-id="08729-303">Forwarded headers</span></span>

<span data-ttu-id="08729-304">Per convenzione, i proxy inoltrano le informazioni nelle intestazioni HTTP.</span><span class="sxs-lookup"><span data-stu-id="08729-304">By convention, proxies forward information in HTTP headers.</span></span>

| <span data-ttu-id="08729-305">Intestazione</span><span class="sxs-lookup"><span data-stu-id="08729-305">Header</span></span> | <span data-ttu-id="08729-306">Descrizione</span><span class="sxs-lookup"><span data-stu-id="08729-306">Description</span></span> |
| ------ | ----------- |
| <span data-ttu-id="08729-307">X-Forwarded-For</span><span class="sxs-lookup"><span data-stu-id="08729-307">X-Forwarded-For</span></span> | <span data-ttu-id="08729-308">Contiene informazioni sul client che ha avviato la richiesta e sui proxy successivi in una catena di proxy.</span><span class="sxs-lookup"><span data-stu-id="08729-308">Holds information about the client that initiated the request and subsequent proxies in a chain of proxies.</span></span> <span data-ttu-id="08729-309">Questo parametro può contenere indirizzi IP (e, facoltativamente, numeri di porta).</span><span class="sxs-lookup"><span data-stu-id="08729-309">This parameter may contain IP addresses (and, optionally, port numbers).</span></span> <span data-ttu-id="08729-310">In una catena di server proxy, il primo parametro indica il client in cui è stata eseguita inizialmente la richiesta.</span><span class="sxs-lookup"><span data-stu-id="08729-310">In a chain of proxy servers, the first parameter indicates the client where the request was first made.</span></span> <span data-ttu-id="08729-311">Seguono gli identificatori dei proxy successivi.</span><span class="sxs-lookup"><span data-stu-id="08729-311">Subsequent proxy identifiers follow.</span></span> <span data-ttu-id="08729-312">L'ultimo proxy della catena non è incluso nell'elenco dei parametri.</span><span class="sxs-lookup"><span data-stu-id="08729-312">The last proxy in the chain isn't in the list of parameters.</span></span> <span data-ttu-id="08729-313">L'indirizzo IP dell'ultimo proxy e, facoltativamente, un numero di porta sono disponibili come indirizzo IP remoto a livello di trasporto.</span><span class="sxs-lookup"><span data-stu-id="08729-313">The last proxy's IP address, and optionally a port number, are available as the remote IP address at the transport layer.</span></span> |
| <span data-ttu-id="08729-314">X-Forwarded-Proto</span><span class="sxs-lookup"><span data-stu-id="08729-314">X-Forwarded-Proto</span></span> | <span data-ttu-id="08729-315">Il valore dello schema di origine (HTTP/HTTPS).</span><span class="sxs-lookup"><span data-stu-id="08729-315">The value of the originating scheme (HTTP/HTTPS).</span></span> <span data-ttu-id="08729-316">Il valore può essere anche un elenco di schemi, se la richiesta ha attraversato più proxy.</span><span class="sxs-lookup"><span data-stu-id="08729-316">The value may also be a list of schemes if the request has traversed multiple proxies.</span></span> |
| <span data-ttu-id="08729-317">X-Forwarded-Host</span><span class="sxs-lookup"><span data-stu-id="08729-317">X-Forwarded-Host</span></span> | <span data-ttu-id="08729-318">Il valore originale del campo dell'intestazione host.</span><span class="sxs-lookup"><span data-stu-id="08729-318">The original value of the Host header field.</span></span> <span data-ttu-id="08729-319">I proxy in genere non modificano l'intestazione host.</span><span class="sxs-lookup"><span data-stu-id="08729-319">Usually, proxies don't modify the Host header.</span></span> <span data-ttu-id="08729-320">Vedere l'[avviso di sicurezza Microsoft CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) per informazioni su una vulnerabilità di elevazione dei privilegi che interessa i sistemi in cui il proxy non convalida o limita le intestazioni host a valori di riferimento noti.</span><span class="sxs-lookup"><span data-stu-id="08729-320">See [Microsoft Security Advisory CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) for information on an elevation-of-privileges vulnerability that affects systems where the proxy doesn't validate or restrict Host headers to known good values.</span></span> |

<span data-ttu-id="08729-321">Il middleware delle intestazioni inoltrate, dal pacchetto [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/), legge le intestazioni e compila i campi associati in <xref:Microsoft.AspNetCore.Http.HttpContext>.</span><span class="sxs-lookup"><span data-stu-id="08729-321">The Forwarded Headers Middleware, from the [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) package, reads these headers and fills in the associated fields on <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span>

<span data-ttu-id="08729-322">Il middleware aggiorna:</span><span class="sxs-lookup"><span data-stu-id="08729-322">The middleware updates:</span></span>

* <span data-ttu-id="08729-323">[HttpContext. Connection. RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress): set usando il `X-Forwarded-For` valore dell'intestazione.</span><span class="sxs-lookup"><span data-stu-id="08729-323">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress): Set using the `X-Forwarded-For` header value.</span></span> <span data-ttu-id="08729-324">Impostazioni aggiuntive influiscono sul modo in cui il middleware imposta `RemoteIpAddress`.</span><span class="sxs-lookup"><span data-stu-id="08729-324">Additional settings influence how the middleware sets `RemoteIpAddress`.</span></span> <span data-ttu-id="08729-325">Per informazioni dettagliate, vedere [Opzioni del middleware delle intestazioni inoltrate](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="08729-325">For details, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>
* <span data-ttu-id="08729-326">[HttpContext. Request. Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme): impostare usando il `X-Forwarded-Proto` valore dell'intestazione.</span><span class="sxs-lookup"><span data-stu-id="08729-326">[HttpContext.Request.Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme): Set using the `X-Forwarded-Proto` header value.</span></span>
* <span data-ttu-id="08729-327">[HttpContext. Request. host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host): impostare usando il `X-Forwarded-Host` valore dell'intestazione.</span><span class="sxs-lookup"><span data-stu-id="08729-327">[HttpContext.Request.Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host): Set using the `X-Forwarded-Host` header value.</span></span>

<span data-ttu-id="08729-328">È possibile configurare le [impostazioni predefinite](#forwarded-headers-middleware-options) del middleware delle intestazioni inoltrate.</span><span class="sxs-lookup"><span data-stu-id="08729-328">Forwarded Headers Middleware [default settings](#forwarded-headers-middleware-options) can be configured.</span></span> <span data-ttu-id="08729-329">Le impostazioni predefinite sono le seguenti:</span><span class="sxs-lookup"><span data-stu-id="08729-329">The default settings are:</span></span>

* <span data-ttu-id="08729-330">È presente *un solo proxy* tra l'app e l'origine delle richieste.</span><span class="sxs-lookup"><span data-stu-id="08729-330">There is only *one proxy* between the app and the source of the requests.</span></span>
* <span data-ttu-id="08729-331">Sono configurati indirizzi di loopback solo per reti e proxy noti.</span><span class="sxs-lookup"><span data-stu-id="08729-331">Only loopback addresses are configured for known proxies and known networks.</span></span>
* <span data-ttu-id="08729-332">Le intestazioni inoltrate sono denominate `X-Forwarded-For` e `X-Forwarded-Proto`.</span><span class="sxs-lookup"><span data-stu-id="08729-332">The forwarded headers are named `X-Forwarded-For` and `X-Forwarded-Proto`.</span></span>

<span data-ttu-id="08729-333">Non tutte le appliance di rete aggiungono le intestazioni `X-Forwarded-For` e `X-Forwarded-Proto` senza alcuna configurazione aggiuntiva.</span><span class="sxs-lookup"><span data-stu-id="08729-333">Not all network appliances add the `X-Forwarded-For` and `X-Forwarded-Proto` headers without additional configuration.</span></span> <span data-ttu-id="08729-334">Se le richieste trasmesse tramite proxy non contengono queste intestazioni quando raggiungono l'app, fare riferimento alle indicazioni del produttore dell'appliance.</span><span class="sxs-lookup"><span data-stu-id="08729-334">Consult your appliance manufacturer's guidance if proxied requests don't contain these headers when they reach the app.</span></span> <span data-ttu-id="08729-335">Se l'appliance usa nomi di intestazione diversi da `X-Forwarded-For` e `X-Forwarded-Proto`, impostare le opzioni <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> e <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> in modo che corrispondano ai nomi di intestazione usati dall'appliance.</span><span class="sxs-lookup"><span data-stu-id="08729-335">If the appliance uses different header names than `X-Forwarded-For` and `X-Forwarded-Proto`, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the appliance.</span></span> <span data-ttu-id="08729-336">Per altre informazioni, vedere [Opzioni del middleware delle intestazioni inoltrate](#forwarded-headers-middleware-options) e [Configurazione per un proxy che usa nomi di intestazione diversi](#configuration-for-a-proxy-that-uses-different-header-names).</span><span class="sxs-lookup"><span data-stu-id="08729-336">For more information, see [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) and [Configuration for a proxy that uses different header names](#configuration-for-a-proxy-that-uses-different-header-names).</span></span>

## <a name="iisiis-express-and-aspnet-core-module"></a><span data-ttu-id="08729-337">IIS/IIS Express e modulo Core ASP.NET</span><span class="sxs-lookup"><span data-stu-id="08729-337">IIS/IIS Express and ASP.NET Core Module</span></span>

<span data-ttu-id="08729-338">Il middleware delle intestazioni inoltrate è abilitato per impostazione predefinita dal [middleware di integrazione IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) quando l'app è ospitata [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) dietro IIS e il modulo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="08729-338">Forwarded Headers Middleware is enabled by default by [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when the app is hosted [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind IIS and the ASP.NET Core Module.</span></span> <span data-ttu-id="08729-339">Il middleware delle intestazioni inoltrate viene attivato per l'esecuzione prima nella pipeline del middleware con una configurazione con restrizioni specifica per il modulo ASP.NET Core per motivi di attendibilità delle intestazioni inoltrate, ad esempio lo [spoofing degli indirizzi IP](https://www.iplocation.net/ip-spoofing).</span><span class="sxs-lookup"><span data-stu-id="08729-339">Forwarded Headers Middleware is activated to run first in the middleware pipeline with a restricted configuration specific to the ASP.NET Core Module due to trust concerns with forwarded headers (for example, [IP spoofing](https://www.iplocation.net/ip-spoofing)).</span></span> <span data-ttu-id="08729-340">Il middleware è configurato per inoltrare le intestazioni `X-Forwarded-For` e `X-Forwarded-Proto` ed è limitato a un singolo proxy localhost.</span><span class="sxs-lookup"><span data-stu-id="08729-340">The middleware is configured to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers and is restricted to a single localhost proxy.</span></span> <span data-ttu-id="08729-341">Se è richiesta una configurazione aggiuntiva, vedere [Opzioni del middleware delle intestazioni inoltrate](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="08729-341">If additional configuration is required, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>

## <a name="other-proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="08729-342">Altri scenari con server proxy e servizi di bilanciamento del carico</span><span class="sxs-lookup"><span data-stu-id="08729-342">Other proxy server and load balancer scenarios</span></span>

<span data-ttu-id="08729-343">Se non si usa il [middleware di integrazione IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) per l'hosting [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model), il middleware delle intestazioni inoltrate non è abilitato per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="08729-343">Outside of using [IIS Integration](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when hosting [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model), Forwarded Headers Middleware isn't enabled by default.</span></span> <span data-ttu-id="08729-344">Il middleware delle intestazioni inoltrate deve essere abilitato per consentire a un'app di inoltrare le intestazioni inoltrate del processo con <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>.</span><span class="sxs-lookup"><span data-stu-id="08729-344">Forwarded Headers Middleware must be enabled for an app to process forwarded headers with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>.</span></span> <span data-ttu-id="08729-345">Dopo aver abilitato il middleware, se non sono specificate opzioni <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> per il middleware, le intestazioni [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) predefinite sono [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="08729-345">After enabling the middleware if no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified to the middleware, the default [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span>

<span data-ttu-id="08729-346">Configurare il middleware con <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> per l'inoltro delle intestazioni `X-Forwarded-For` e `X-Forwarded-Proto` in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="08729-346">Configure the middleware with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="08729-347">Richiamare il metodo <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure` prima di chiamare altro middleware:</span><span class="sxs-lookup"><span data-stu-id="08729-347">Invoke the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> method in `Startup.Configure` before calling other middleware:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();

    services.Configure<ForwardedHeadersOptions>(options =>
    {
        options.ForwardedHeaders = 
            ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto;
    });
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseForwardedHeaders();

    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
    }
    else
    {
        app.UseExceptionHandler("/Home/Error");
    }

    app.UseStaticFiles();
    // In ASP.NET Core 1.x, replace the following line with: app.Use:::no-loc(Identity):::();
    app.UseAuthentication();
    app.UseMvc();
}
```

> [!NOTE]
> <span data-ttu-id="08729-348">Se non vengono specificate <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> in `Startup.ConfigureServices` o direttamente nel metodo di estensione con <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>, le intestazioni predefinite da inoltrare sono [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="08729-348">If no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified in `Startup.ConfigureServices` or directly to the extension method with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>, the default headers to forward are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> <span data-ttu-id="08729-349">La proprietà <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> deve essere configurata con le intestazioni da inoltrare.</span><span class="sxs-lookup"><span data-stu-id="08729-349">The <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> property must be configured with the headers to forward.</span></span>

## <a name="nginx-configuration"></a><span data-ttu-id="08729-350">Configurazione Nginx</span><span class="sxs-lookup"><span data-stu-id="08729-350">Nginx configuration</span></span>

<span data-ttu-id="08729-351">Per inoltrare le intestazioni `X-Forwarded-For` e `X-Forwarded-Proto`, vedere <xref:host-and-deploy/linux-nginx#configure-nginx>.</span><span class="sxs-lookup"><span data-stu-id="08729-351">To forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers, see <xref:host-and-deploy/linux-nginx#configure-nginx>.</span></span> <span data-ttu-id="08729-352">Per altre informazioni, vedere [NGINX: Using the Forwarded header](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/) (NGINX: Uso dell'intestazione Forwarded).</span><span class="sxs-lookup"><span data-stu-id="08729-352">For more information, see [NGINX: Using the Forwarded header](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/).</span></span>

## <a name="apache-configuration"></a><span data-ttu-id="08729-353">Configurazione Apache</span><span class="sxs-lookup"><span data-stu-id="08729-353">Apache configuration</span></span>

<span data-ttu-id="08729-354">`X-Forwarded-For` viene aggiunta automaticamente. Vedere [Apache Module mod_proxy: Reverse Proxy Request Headers](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers) (Modulo Apache mod_proxy: Intestazioni delle richieste del proxy inverso).</span><span class="sxs-lookup"><span data-stu-id="08729-354">`X-Forwarded-For` is added automatically (see [Apache Module mod_proxy: Reverse Proxy Request Headers](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)).</span></span> <span data-ttu-id="08729-355">Per informazioni su come inoltrare l'intestazione `X-Forwarded-Proto`, vedere <xref:host-and-deploy/linux-apache#configure-apache>.</span><span class="sxs-lookup"><span data-stu-id="08729-355">For information on how to forward the `X-Forwarded-Proto` header, see <xref:host-and-deploy/linux-apache#configure-apache>.</span></span>

## <a name="forwarded-headers-middleware-options"></a><span data-ttu-id="08729-356">Opzioni del middleware delle intestazioni inoltrate</span><span class="sxs-lookup"><span data-stu-id="08729-356">Forwarded Headers Middleware options</span></span>

<span data-ttu-id="08729-357"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> controllano il comportamento del middleware delle intestazioni inoltrate.</span><span class="sxs-lookup"><span data-stu-id="08729-357"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> control the behavior of the Forwarded Headers Middleware.</span></span> <span data-ttu-id="08729-358">L'esempio seguente modifica i valori predefiniti:</span><span class="sxs-lookup"><span data-stu-id="08729-358">The following example changes the default values:</span></span>

* <span data-ttu-id="08729-359">Limitare il numero di voci nelle intestazioni inoltrate a `2`.</span><span class="sxs-lookup"><span data-stu-id="08729-359">Limit the number of entries in the forwarded headers to `2`.</span></span>
* <span data-ttu-id="08729-360">Aggiungere un indirizzo proxy noto di `127.0.10.1`.</span><span class="sxs-lookup"><span data-stu-id="08729-360">Add a known proxy address of `127.0.10.1`.</span></span>
* <span data-ttu-id="08729-361">Modificare il nome dell'intestazione inoltrata dal nome predefinito `X-Forwarded-For` a `X-Forwarded-For-My-Custom-Header-Name`.</span><span class="sxs-lookup"><span data-stu-id="08729-361">Change the forwarded header name from the default `X-Forwarded-For` to `X-Forwarded-For-My-Custom-Header-Name`.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardLimit = 2;
    options.KnownProxies.Add(IPAddress.Parse("127.0.10.1"));
    options.ForwardedForHeaderName = "X-Forwarded-For-My-Custom-Header-Name";
});
```

| <span data-ttu-id="08729-362">Opzione</span><span class="sxs-lookup"><span data-stu-id="08729-362">Option</span></span> | <span data-ttu-id="08729-363">Descrizione</span><span class="sxs-lookup"><span data-stu-id="08729-363">Description</span></span> |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> | <span data-ttu-id="08729-364">Limita gli host mediante l'intestazione `X-Forwarded-Host` ai valori specificati.</span><span class="sxs-lookup"><span data-stu-id="08729-364">Restricts hosts by the `X-Forwarded-Host` header to the values provided.</span></span><ul><li><span data-ttu-id="08729-365">I valori vengono confrontati tramite ordinal-ignore-case.</span><span class="sxs-lookup"><span data-stu-id="08729-365">Values are compared using ordinal-ignore-case.</span></span></li><li><span data-ttu-id="08729-366">I numeri di porta devono essere esclusi.</span><span class="sxs-lookup"><span data-stu-id="08729-366">Port numbers must be excluded.</span></span></li><li><span data-ttu-id="08729-367">Se l'elenco è vuoto, sono consentiti tutti gli host.</span><span class="sxs-lookup"><span data-stu-id="08729-367">If the list is empty, all hosts are allowed.</span></span></li><li><span data-ttu-id="08729-368">Un carattere jolly di primo livello `*` consente tutti gli host non vuoti.</span><span class="sxs-lookup"><span data-stu-id="08729-368">A top-level wildcard `*` allows all non-empty hosts.</span></span></li><li><span data-ttu-id="08729-369">I caratteri jolly per i sottodomini sono consentiti, ma non corrispondono al dominio radice.</span><span class="sxs-lookup"><span data-stu-id="08729-369">Subdomain wildcards are permitted but don't match the root domain.</span></span> <span data-ttu-id="08729-370">Ad esempio, `*.contoso.com` corrisponde al sottodominio `foo.contoso.com`, ma non al dominio radice `contoso.com`.</span><span class="sxs-lookup"><span data-stu-id="08729-370">For example, `*.contoso.com` matches the subdomain `foo.contoso.com` but not the root domain `contoso.com`.</span></span></li><li><span data-ttu-id="08729-371">I nomi host Unicode sono consentiti ma vengono convertiti in [Punycode](https://tools.ietf.org/html/rfc3492) per la corrispondenza.</span><span class="sxs-lookup"><span data-stu-id="08729-371">Unicode host names are allowed but are converted to [Punycode](https://tools.ietf.org/html/rfc3492) for matching.</span></span></li><li><span data-ttu-id="08729-372">Gli [indirizzi IPv6](https://tools.ietf.org/html/rfc4291) devono includere le parentesi quadre di delimitazione ed essere in [formato convenzionale](https://tools.ietf.org/html/rfc4291#section-2.2), ad esempio `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`.</span><span class="sxs-lookup"><span data-stu-id="08729-372">[IPv6 addresses](https://tools.ietf.org/html/rfc4291) must include bounding brackets and be in [conventional form](https://tools.ietf.org/html/rfc4291#section-2.2) (for example, `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`).</span></span> <span data-ttu-id="08729-373">Per gli indirizzi IPv6 non viene applicata la distinzione tra maiuscole e minuscole per la verifica dell'uguaglianza logica tra i diversi formati e non viene eseguita alcuna canonizzazione.</span><span class="sxs-lookup"><span data-stu-id="08729-373">IPv6 addresses aren't special-cased to check for logical equality between different formats, and no canonicalization is performed.</span></span></li><li><span data-ttu-id="08729-374">La mancata limitazione degli host consentiti potrebbe permettere a un utente malintenzionato di eseguire lo spoofing dei collegamenti generati dal servizio.</span><span class="sxs-lookup"><span data-stu-id="08729-374">Failure to restrict the allowed hosts may allow an attacker to spoof links generated by the service.</span></span></li></ul><span data-ttu-id="08729-375">Il valore predefinito è un insieme `IList<string>` vuoto.</span><span class="sxs-lookup"><span data-stu-id="08729-375">The default value is an empty `IList<string>`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> | <span data-ttu-id="08729-376">Usare l'intestazione specificata da questa proprietà anziché quella specificata da [ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName).</span><span class="sxs-lookup"><span data-stu-id="08729-376">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName).</span></span> <span data-ttu-id="08729-377">Questa opzione viene usata quando il proxy o il server d'inoltro non usa l'intestazione `X-Forwarded-For` ma usa un'altra intestazione per inoltrare le informazioni.</span><span class="sxs-lookup"><span data-stu-id="08729-377">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-For` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="08729-378">Il valore predefinito è `X-Forwarded-For`.</span><span class="sxs-lookup"><span data-stu-id="08729-378">The default is `X-Forwarded-For`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> | <span data-ttu-id="08729-379">Identifica i server d'inoltro che devono essere elaborati.</span><span class="sxs-lookup"><span data-stu-id="08729-379">Identifies which forwarders should be processed.</span></span> <span data-ttu-id="08729-380">Vedere [ForwardedHeaders Enum](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) (Enumerazione ForwardedHeaders) per l'elenco dei campi applicabili.</span><span class="sxs-lookup"><span data-stu-id="08729-380">See the [ForwardedHeaders Enum](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) for the list of fields that apply.</span></span> <span data-ttu-id="08729-381">I valori tipici assegnati a questa proprietà sono `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`.</span><span class="sxs-lookup"><span data-stu-id="08729-381">Typical values assigned to this property are `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`.</span></span><br><br><span data-ttu-id="08729-382">Il valore predefinito è [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="08729-382">The default value is [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHostHeaderName> | <span data-ttu-id="08729-383">Usare l'intestazione specificata da questa proprietà anziché quella specificata da [ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName).</span><span class="sxs-lookup"><span data-stu-id="08729-383">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName).</span></span> <span data-ttu-id="08729-384">Questa opzione viene usata quando il proxy o il server d'inoltro non usa l'intestazione `X-Forwarded-Host` ma usa un'altra intestazione per inoltrare le informazioni.</span><span class="sxs-lookup"><span data-stu-id="08729-384">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Host` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="08729-385">Il valore predefinito è `X-Forwarded-Host`.</span><span class="sxs-lookup"><span data-stu-id="08729-385">The default is `X-Forwarded-Host`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> | <span data-ttu-id="08729-386">Usare l'intestazione specificata da questa proprietà anziché quella specificata da [ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName).</span><span class="sxs-lookup"><span data-stu-id="08729-386">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName).</span></span> <span data-ttu-id="08729-387">Questa opzione viene usata quando il proxy o il server d'inoltro non usa l'intestazione `X-Forwarded-Proto` ma usa un'altra intestazione per inoltrare le informazioni.</span><span class="sxs-lookup"><span data-stu-id="08729-387">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Proto` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="08729-388">Il valore predefinito è `X-Forwarded-Proto`.</span><span class="sxs-lookup"><span data-stu-id="08729-388">The default is `X-Forwarded-Proto`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardLimit> | <span data-ttu-id="08729-389">Limita il numero di voci nelle intestazioni elaborate.</span><span class="sxs-lookup"><span data-stu-id="08729-389">Limits the number of entries in the headers that are processed.</span></span> <span data-ttu-id="08729-390">Impostare su `null` per disabilitare il limite, ma solo se è configurato `KnownProxies` o `KnownNetworks`.</span><span class="sxs-lookup"><span data-stu-id="08729-390">Set to `null` to disable the limit, but this should only be done if `KnownProxies` or `KnownNetworks` are configured.</span></span> <span data-ttu-id="08729-391">L'impostazione di un valore non `null` è una precauzione (ma non una garanzia) per proteggersi da proxy non configurati correttamente e richieste dannose provenienti da canali laterali in rete.</span><span class="sxs-lookup"><span data-stu-id="08729-391">Setting a non-`null` value is a precaution (but not a guarantee) to guard against misconfigured proxies and malicious requests arriving from side-channels on the network.</span></span><br><br><span data-ttu-id="08729-392">Il middleware delle intestazioni inoltrate elabora le intestazioni in ordine inverso da destra a sinistra.</span><span class="sxs-lookup"><span data-stu-id="08729-392">Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="08729-393">Se viene usato il valore predefinito (`1`), viene elaborato solo il valore più a destra delle intestazioni, a meno che non venga aumentato il valore di `ForwardLimit`.</span><span class="sxs-lookup"><span data-stu-id="08729-393">If the default value (`1`) is used, only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span><br><br><span data-ttu-id="08729-394">Il valore predefinito è `1`.</span><span class="sxs-lookup"><span data-stu-id="08729-394">The default is `1`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks> | <span data-ttu-id="08729-395">Intervalli di indirizzi delle reti note da cui accettare le intestazioni inoltrate.</span><span class="sxs-lookup"><span data-stu-id="08729-395">Address ranges of known networks to accept forwarded headers from.</span></span> <span data-ttu-id="08729-396">Specificare gli intervalli IP usando la notazione CIDR (Classless Interdomain Routing).</span><span class="sxs-lookup"><span data-stu-id="08729-396">Provide IP ranges using Classless Interdomain Routing (CIDR) notation.</span></span><br><br><span data-ttu-id="08729-397">Se il server usa socket dual mode, gli indirizzi IPv4 vengono forniti in un formato IPv6 (ad esempio, `10.0.0.1` in IPv4 rappresentato in IPv6 come `::ffff:10.0.0.1`).</span><span class="sxs-lookup"><span data-stu-id="08729-397">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="08729-398">Vedere [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="08729-398">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="08729-399">Determinare se questo formato è richiesto esaminando [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span><span class="sxs-lookup"><span data-stu-id="08729-399">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="08729-400">Per altre informazioni, vedere la sezione [Configurazione per un indirizzo IPv4 rappresentato come indirizzo IPv6](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address).</span><span class="sxs-lookup"><span data-stu-id="08729-400">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="08729-401">Il valore predefinito è un oggetto `IList`\<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> contenente una singola voce per `IPAddress.Loopback`.</span><span class="sxs-lookup"><span data-stu-id="08729-401">The default is an `IList`\<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> containing a single entry for `IPAddress.Loopback`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies> | <span data-ttu-id="08729-402">Indirizzi dei proxy noti da cui accettare le intestazioni inoltrate.</span><span class="sxs-lookup"><span data-stu-id="08729-402">Addresses of known proxies to accept forwarded headers from.</span></span> <span data-ttu-id="08729-403">Usare `KnownProxies` per specificare le corrispondenze esatte degli indirizzi IP.</span><span class="sxs-lookup"><span data-stu-id="08729-403">Use `KnownProxies` to specify exact IP address matches.</span></span><br><br><span data-ttu-id="08729-404">Se il server usa socket dual mode, gli indirizzi IPv4 vengono forniti in un formato IPv6 (ad esempio, `10.0.0.1` in IPv4 rappresentato in IPv6 come `::ffff:10.0.0.1`).</span><span class="sxs-lookup"><span data-stu-id="08729-404">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="08729-405">Vedere [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="08729-405">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="08729-406">Determinare se questo formato è richiesto esaminando [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span><span class="sxs-lookup"><span data-stu-id="08729-406">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="08729-407">Per altre informazioni, vedere la sezione [Configurazione per un indirizzo IPv4 rappresentato come indirizzo IPv6](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address).</span><span class="sxs-lookup"><span data-stu-id="08729-407">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="08729-408">Il valore predefinito è un oggetto `IList`\<<xref:System.Net.IPAddress>> contenente una singola voce per `IPAddress.IPv6Loopback`.</span><span class="sxs-lookup"><span data-stu-id="08729-408">The default is an `IList`\<<xref:System.Net.IPAddress>> containing a single entry for `IPAddress.IPv6Loopback`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalForHeaderName> | <span data-ttu-id="08729-409">Usare l'intestazione specificata da questa proprietà anziché quella specificata da [ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).</span><span class="sxs-lookup"><span data-stu-id="08729-409">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).</span></span><br><br><span data-ttu-id="08729-410">Il valore predefinito è `X-Original-For`.</span><span class="sxs-lookup"><span data-stu-id="08729-410">The default is `X-Original-For`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalHostHeaderName> | <span data-ttu-id="08729-411">Usare l'intestazione specificata da questa proprietà anziché quella specificata da [ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).</span><span class="sxs-lookup"><span data-stu-id="08729-411">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).</span></span><br><br><span data-ttu-id="08729-412">Il valore predefinito è `X-Original-Host`.</span><span class="sxs-lookup"><span data-stu-id="08729-412">The default is `X-Original-Host`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalProtoHeaderName> | <span data-ttu-id="08729-413">Usare l'intestazione specificata da questa proprietà anziché quella specificata da [ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).</span><span class="sxs-lookup"><span data-stu-id="08729-413">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).</span></span><br><br><span data-ttu-id="08729-414">Il valore predefinito è `X-Original-Proto`.</span><span class="sxs-lookup"><span data-stu-id="08729-414">The default is `X-Original-Proto`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.RequireHeaderSymmetry> | <span data-ttu-id="08729-415">Richiedere il numero di valori di intestazione da sincronizzare tra le intestazioni [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) in fase di elaborazione.</span><span class="sxs-lookup"><span data-stu-id="08729-415">Require the number of header values to be in sync between the [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) being processed.</span></span><br><br><span data-ttu-id="08729-416">Il valore predefinito in ASP.NET Core 1.x è `true`.</span><span class="sxs-lookup"><span data-stu-id="08729-416">The default in ASP.NET Core 1.x is `true`.</span></span> <span data-ttu-id="08729-417">Il valore predefinito in ASP.NET Core 2.0 o versione successiva è `false`.</span><span class="sxs-lookup"><span data-stu-id="08729-417">The default in ASP.NET Core 2.0 or later is `false`.</span></span> |

## <a name="scenarios-and-use-cases"></a><span data-ttu-id="08729-418">Scenari e casi d'uso</span><span class="sxs-lookup"><span data-stu-id="08729-418">Scenarios and use cases</span></span>

### <a name="when-it-isnt-possible-to-add-forwarded-headers-and-all-requests-are-secure"></a><span data-ttu-id="08729-419">Quando non è possibile aggiungere le intestazioni inoltrate e tutte le richieste sono sicure</span><span class="sxs-lookup"><span data-stu-id="08729-419">When it isn't possible to add forwarded headers and all requests are secure</span></span>

<span data-ttu-id="08729-420">In alcuni casi, potrebbe non essere possibile aggiungere le intestazioni inoltrate alle richieste trasmesse tramite proxy all'app.</span><span class="sxs-lookup"><span data-stu-id="08729-420">In some cases, it might not be possible to add forwarded headers to the requests proxied to the app.</span></span> <span data-ttu-id="08729-421">Se il proxy impone che tutte le richieste esterne pubbliche siano HTTPS, lo schema può essere impostato manualmente in `Startup.Configure` prima di usare qualsiasi tipo di middleware:</span><span class="sxs-lookup"><span data-stu-id="08729-421">If the proxy is enforcing that all public external requests are HTTPS, the scheme can be manually set in `Startup.Configure` before using any type of middleware:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.Scheme = "https";
    return next();
});
```

<span data-ttu-id="08729-422">Questo codice può essere disabilitato tramite una variabile di ambiente o un'altra impostazione di configurazione in un ambiente di sviluppo o di gestione temporanea.</span><span class="sxs-lookup"><span data-stu-id="08729-422">This code can be disabled with an environment variable or other configuration setting in a development or staging environment.</span></span>

### <a name="deal-with-path-base-and-proxies-that-change-the-request-path"></a><span data-ttu-id="08729-423">Gestire la base del percorso e i proxy che modificano il percorso della richiesta</span><span class="sxs-lookup"><span data-stu-id="08729-423">Deal with path base and proxies that change the request path</span></span>

<span data-ttu-id="08729-424">Alcuni proxy passano il percorso senza modifiche, ma con un percorso di base dell'app che deve essere rimosso per consentire il corretto funzionamento del routing.</span><span class="sxs-lookup"><span data-stu-id="08729-424">Some proxies pass the path intact but with an app base path that should be removed so that routing works properly.</span></span> <span data-ttu-id="08729-425">Il middleware [UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) suddivide il percorso in [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) e il percorso di base dell'app in [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).</span><span class="sxs-lookup"><span data-stu-id="08729-425">[UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) middleware splits the path into [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) and the app base path into [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).</span></span>

<span data-ttu-id="08729-426">Se `/foo` è il percorso di base dell'app per un percorso proxy passato come `/foo/api/1`, il middleware imposta `Request.PathBase` su `/foo` e `Request.Path` su `/api/1` con il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="08729-426">If `/foo` is the app base path for a proxy path passed as `/foo/api/1`, the middleware sets `Request.PathBase` to `/foo` and `Request.Path` to `/api/1` with the following command:</span></span>

```csharp
app.UsePathBase("/foo");
```

<span data-ttu-id="08729-427">Il percorso originale e la base del percorso vengono riapplicati quando il middleware viene chiamato nuovamente in direzione inversa.</span><span class="sxs-lookup"><span data-stu-id="08729-427">The original path and path base are reapplied when the middleware is called again in reverse.</span></span> <span data-ttu-id="08729-428">Per altre informazioni dell'elaborazione degli ordini del middleware, vedere <xref:fundamentals/middleware/index>.</span><span class="sxs-lookup"><span data-stu-id="08729-428">For more information on middleware order processing, see <xref:fundamentals/middleware/index>.</span></span>

<span data-ttu-id="08729-429">Se il proxy taglia il percorso (ad esempio, inoltrando `/foo/api/1` a `/api/1`), correggere i reindirizzamenti e i collegamenti impostando la proprietà [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) della richiesta:</span><span class="sxs-lookup"><span data-stu-id="08729-429">If the proxy trims the path (for example, forwarding `/foo/api/1` to `/api/1`), fix redirects and links by setting the request's [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) property:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.PathBase = new PathString("/foo");
    return next();
});
```

<span data-ttu-id="08729-430">Se il proxy aggiunge i dati del percorso, eliminare parte del percorso per correggere i reindirizzamenti e i collegamenti usando <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> ed eseguendo l'assegnazione alla proprietà <xref:Microsoft.AspNetCore.Http.HttpRequest.Path>:</span><span class="sxs-lookup"><span data-stu-id="08729-430">If the proxy is adding path data, discard part of the path to fix redirects and links by using <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> and assigning to the <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> property:</span></span>

```csharp
app.Use((context, next) =>
{
    if (context.Request.Path.StartsWithSegments("/foo", out var remainder))
    {
        context.Request.Path = remainder;
    }

    return next();
});
```

### <a name="configuration-for-a-proxy-that-uses-different-header-names"></a><span data-ttu-id="08729-431">Configurazione per un proxy che usa nomi di intestazione diversi</span><span class="sxs-lookup"><span data-stu-id="08729-431">Configuration for a proxy that uses different header names</span></span>

<span data-ttu-id="08729-432">Se il proxy non usa intestazioni denominate `X-Forwarded-For` e `X-Forwarded-Proto` per inoltrare l'indirizzo proxy o la porta e le informazioni dello scherma originali, impostare le opzioni <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> e <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> in modo che corrispondano ai nomi di intestazione usati dal proxy:</span><span class="sxs-lookup"><span data-stu-id="08729-432">If the proxy doesn't use headers named `X-Forwarded-For` and `X-Forwarded-Proto` to forward the proxy address/port and originating scheme information, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the proxy:</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedForHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-For_Header";
    options.ForwardedProtoHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-Proto_Header";
});
```

### <a name="configuration-for-an-ipv4-address-represented-as-an-ipv6-address"></a><span data-ttu-id="08729-433">Configurazione per un indirizzo IPv4 rappresentato come indirizzo IPv6</span><span class="sxs-lookup"><span data-stu-id="08729-433">Configuration for an IPv4 address represented as an IPv6 address</span></span>

<span data-ttu-id="08729-434">Se il server usa socket dual mode, gli indirizzi IPv4 vengono forniti in un formato IPv6 (ad esempio, `10.0.0.1` in IPv4 rappresentato in IPv6 come `::ffff:10.0.0.1` o `::ffff:a00:1`).</span><span class="sxs-lookup"><span data-stu-id="08729-434">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1` or `::ffff:a00:1`).</span></span> <span data-ttu-id="08729-435">Vedere [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="08729-435">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="08729-436">Determinare se questo formato è richiesto esaminando [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span><span class="sxs-lookup"><span data-stu-id="08729-436">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span>

<span data-ttu-id="08729-437">Nell'esempio seguente un indirizzo di rete che fornisce intestazioni inoltrate viene aggiunto all'elenco `KnownNetworks` in formato IPv6.</span><span class="sxs-lookup"><span data-stu-id="08729-437">In the following example, a network address that supplies forwarded headers is added to the `KnownNetworks` list in IPv6 format.</span></span>

<span data-ttu-id="08729-438">Indirizzo IPv4: `10.11.12.1/8`</span><span class="sxs-lookup"><span data-stu-id="08729-438">IPv4 address: `10.11.12.1/8`</span></span>

<span data-ttu-id="08729-439">Indirizzo IPv6 convertito: `::ffff:10.11.12.1`</span><span class="sxs-lookup"><span data-stu-id="08729-439">Converted IPv6 address: `::ffff:10.11.12.1`</span></span>  
<span data-ttu-id="08729-440">Lunghezza del prefisso convertito: 104</span><span class="sxs-lookup"><span data-stu-id="08729-440">Converted prefix length: 104</span></span>

<span data-ttu-id="08729-441">È anche possibile specificare l'indirizzo in formato esadecimale (`10.11.12.1` rappresentato in IPv6 come `::ffff:0a0b:0c01`).</span><span class="sxs-lookup"><span data-stu-id="08729-441">You can also supply the address in hexadecimal format (`10.11.12.1` represented in IPv6 as `::ffff:0a0b:0c01`).</span></span> <span data-ttu-id="08729-442">Durante la conversione di un indirizzo IPv4 in IPv6, aggiungere 96 alla lunghezza del prefisso CIDR (`8` nell'esempio) per tenere conto del prefisso IPv6 aggiuntivo `::ffff:` (8 + 96 = 104).</span><span class="sxs-lookup"><span data-stu-id="08729-442">When converting an IPv4 address to IPv6, add 96 to the CIDR Prefix Length (`8` in the example) to account for the additional `::ffff:` IPv6 prefix (8 + 96 = 104).</span></span> 

```csharp
// To access IPNetwork and IPAddress, add the following namespaces:
// using System.Net;
// using Microsoft.AspNetCore.HttpOverrides;
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedHeaders =
        ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto;
    options.KnownNetworks.Add(new IPNetwork(
        IPAddress.Parse("::ffff:10.11.12.1"), 104));
});
```

## <a name="forward-the-scheme-for-linux-and-non-iis-reverse-proxies"></a><span data-ttu-id="08729-443">Inoltrare lo schema per Linux e proxy inversi non IIS</span><span class="sxs-lookup"><span data-stu-id="08729-443">Forward the scheme for Linux and non-IIS reverse proxies</span></span>

<span data-ttu-id="08729-444">Con le app che chiamano <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> e <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> il sito si ritrova in un ciclo infinito se distribuito in un servizio app Linux di Azure, in una macchina virtuale Linux di Azure o dietro eventuali altri proxy inversi diversi da IIS.</span><span class="sxs-lookup"><span data-stu-id="08729-444">Apps that call <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> and <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> put a site into an infinite loop if deployed to an Azure Linux App Service, Azure Linux virtual machine (VM), or behind any other reverse proxy besides IIS.</span></span> <span data-ttu-id="08729-445">TLS viene terminato dal proxy inverso e Kestrel non viene a conoscenza dello schema di richiesta corretto.</span><span class="sxs-lookup"><span data-stu-id="08729-445">TLS is terminated by the reverse proxy, and Kestrel isn't made aware of the correct request scheme.</span></span> <span data-ttu-id="08729-446">Si verificano errori anche per OAuth e OIDC in questa configurazione perché generano reindirizzamenti non corretti.</span><span class="sxs-lookup"><span data-stu-id="08729-446">OAuth and OIDC also fail in this configuration because they generate incorrect redirects.</span></span> <span data-ttu-id="08729-447"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> aggiunge e configura il middleware delle intestazioni inoltrate in caso di esecuzione dietro IIS, ma non esiste alcuna configurazione automatica corrispondente per Linux (integrazione di Apache o Nginx).</span><span class="sxs-lookup"><span data-stu-id="08729-447"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> adds and configures Forwarded Headers Middleware when running behind IIS, but there's no matching automatic configuration for Linux (Apache or Nginx integration).</span></span>

<span data-ttu-id="08729-448">Per inoltrare lo schema dal proxy in scenari non IIS, aggiungere e configurare il middleware delle intestazioni inoltrate.</span><span class="sxs-lookup"><span data-stu-id="08729-448">To forward the scheme from the proxy in non-IIS scenarios, add and configure Forwarded Headers Middleware.</span></span> <span data-ttu-id="08729-449">In `Startup.ConfigureServices` usare il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="08729-449">In `Startup.ConfigureServices`, use the following code:</span></span>

```csharp
// using Microsoft.AspNetCore.HttpOverrides;

if (string.Equals(
    Environment.GetEnvironmentVariable("ASPNETCORE_FORWARDEDHEADERS_ENABLED"), 
    "true", StringComparison.OrdinalIgnoreCase))
{
    services.Configure<ForwardedHeadersOptions>(options =>
    {
        options.ForwardedHeaders = ForwardedHeaders.XForwardedFor | 
            ForwardedHeaders.XForwardedProto;
        // Only loopback proxies are allowed by default.
        // Clear that restriction because forwarders are enabled by explicit 
        // configuration.
        options.KnownNetworks.Clear();
        options.KnownProxies.Clear();
    });
}
```

## <a name="troubleshoot"></a><span data-ttu-id="08729-450">Risolvere problemi</span><span class="sxs-lookup"><span data-stu-id="08729-450">Troubleshoot</span></span>

<span data-ttu-id="08729-451">Quando le intestazioni non vengono inoltrate come previsto, abilitare la [registrazione](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="08729-451">When headers aren't forwarded as expected, enable [logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="08729-452">Se i log non forniscono informazioni sufficienti per risolvere il problema, enumerare le intestazioni delle richieste ricevute dal server.</span><span class="sxs-lookup"><span data-stu-id="08729-452">If the logs don't provide sufficient information to troubleshoot the problem, enumerate the request headers received by the server.</span></span> <span data-ttu-id="08729-453">Usare il middleware inline per scrivere le intestazioni di richiesta in una risposta dell'app o per registrare le intestazioni.</span><span class="sxs-lookup"><span data-stu-id="08729-453">Use inline middleware to write request headers to an app response or log the headers.</span></span> 

<span data-ttu-id="08729-454">Per scrivere le intestazioni nella risposta dell'app, aggiungere il middleware inline di terminale seguente immediatamente dopo la chiamata a <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="08729-454">To write the headers to the app's response, place the following terminal inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`:</span></span>

```csharp
app.Run(async (context) =>
{
    context.Response.ContentType = "text/plain";

    // Request method, scheme, and path
    await context.Response.WriteAsync(
        $"Request Method: {context.Request.Method}{Environment.NewLine}");
    await context.Response.WriteAsync(
        $"Request Scheme: {context.Request.Scheme}{Environment.NewLine}");
    await context.Response.WriteAsync(
        $"Request Path: {context.Request.Path}{Environment.NewLine}");

    // Headers
    await context.Response.WriteAsync($"Request Headers:{Environment.NewLine}");

    foreach (var header in context.Request.Headers)
    {
        await context.Response.WriteAsync($"{header.Key}: " +
            $"{header.Value}{Environment.NewLine}");
    }

    await context.Response.WriteAsync(Environment.NewLine);

    // Connection: RemoteIp
    await context.Response.WriteAsync(
        $"Request RemoteIp: {context.Connection.RemoteIpAddress}");
});
```

<span data-ttu-id="08729-455">È possibile scrivere nei log invece che nel corpo della risposta.</span><span class="sxs-lookup"><span data-stu-id="08729-455">You can write to logs instead of the response body.</span></span> <span data-ttu-id="08729-456">La scrittura nei log permette il normale funzionamento del sito durante il debug.</span><span class="sxs-lookup"><span data-stu-id="08729-456">Writing to logs allows the site to function normally while debugging.</span></span>

<span data-ttu-id="08729-457">Per scrivere nei log invece che nel corpo della risposta:</span><span class="sxs-lookup"><span data-stu-id="08729-457">To write logs rather than to the response body:</span></span>

* <span data-ttu-id="08729-458">Inserire `ILogger<Startup>` nella classe `Startup` come descritto in [Creare log in Startup](xref:fundamentals/logging/index#create-logs-in-startup).</span><span class="sxs-lookup"><span data-stu-id="08729-458">Inject `ILogger<Startup>` into the `Startup` class as described in [Create logs in Startup](xref:fundamentals/logging/index#create-logs-in-startup).</span></span>
* <span data-ttu-id="08729-459">Aggiungere il middleware inline seguente immediatamente dopo la chiamata a <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="08729-459">Place the following inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`.</span></span>

```csharp
app.Use(async (context, next) =>
{
    // Request method, scheme, and path
    _logger.LogDebug("Request Method: {Method}", context.Request.Method);
    _logger.LogDebug("Request Scheme: {Scheme}", context.Request.Scheme);
    _logger.LogDebug("Request Path: {Path}", context.Request.Path);

    // Headers
    foreach (var header in context.Request.Headers)
    {
        _logger.LogDebug("Header: {Key}: {Value}", header.Key, header.Value);
    }

    // Connection: RemoteIp
    _logger.LogDebug("Request RemoteIp: {RemoteIpAddress}", 
        context.Connection.RemoteIpAddress);

    await next();
});
```

<span data-ttu-id="08729-460">Durante l'elaborazione, i valori di `X-Forwarded-{For|Proto|Host}` vengono spostati in `X-Original-{For|Proto|Host}`.</span><span class="sxs-lookup"><span data-stu-id="08729-460">When processed, `X-Forwarded-{For|Proto|Host}` values are moved to `X-Original-{For|Proto|Host}`.</span></span> <span data-ttu-id="08729-461">Se sono presenti più valori in una determinata intestazione, il middleware delle intestazioni inoltrate elabora le intestazioni in ordine inverso da destra a sinistra.</span><span class="sxs-lookup"><span data-stu-id="08729-461">If there are multiple values in a given header, Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="08729-462">Il valore predefinito di `ForwardLimit` è `1` (uno) e, pertanto, viene elaborato solo il valore più a destra delle intestazioni, a meno che non venga aumentato il valore di `ForwardLimit`.</span><span class="sxs-lookup"><span data-stu-id="08729-462">The default `ForwardLimit` is `1` (one), so only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span>

<span data-ttu-id="08729-463">L'indirizzo IP remoto originale della richiesta deve corrispondere a una voce negli elenchi `KnownProxies` o `KnownNetworks` prima dell'elaborazione delle intestazioni inoltrate.</span><span class="sxs-lookup"><span data-stu-id="08729-463">The request's original remote IP must match an entry in the `KnownProxies` or `KnownNetworks` lists before forwarded headers are processed.</span></span> <span data-ttu-id="08729-464">Questo riduce lo spoofing delle intestazioni, non accettando server di inoltro da proxy non attendibili.</span><span class="sxs-lookup"><span data-stu-id="08729-464">This limits header spoofing by not accepting forwarders from untrusted proxies.</span></span> <span data-ttu-id="08729-465">Quando viene rilevato un proxy sconosciuto, la registrazione indica l'indirizzo del proxy:</span><span class="sxs-lookup"><span data-stu-id="08729-465">When an unknown proxy is detected, logging indicates the address of the proxy:</span></span>

```console
September 20th 2018, 15:49:44.168 Unknown proxy: 10.0.0.100:54321
```

<span data-ttu-id="08729-466">Nell'esempio precedente, 10.0.0.100 è un server proxy.</span><span class="sxs-lookup"><span data-stu-id="08729-466">In the preceding example, 10.0.0.100 is a proxy server.</span></span> <span data-ttu-id="08729-467">Se il server è un proxy attendibile, aggiungere l'indirizzo IP del server a `KnownProxies` (o aggiungere una rete attendibile a `KnownNetworks`) in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="08729-467">If the server is a trusted proxy, add the server's IP address to `KnownProxies` (or add a trusted network to `KnownNetworks`) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="08729-468">Per altre informazioni, vedere la sezione [Opzioni del middleware delle intestazioni inoltrate](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="08729-468">For more information, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) section.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

> [!IMPORTANT]
> <span data-ttu-id="08729-469">Consentire solo a reti e proxy attendibili di inoltrare le intestazioni.</span><span class="sxs-lookup"><span data-stu-id="08729-469">Only allow trusted proxies and networks to forward headers.</span></span> <span data-ttu-id="08729-470">In caso contrario, possono verificarsi attacchi di [spoofing degli indirizzi IP](https://www.iplocation.net/ip-spoofing).</span><span class="sxs-lookup"><span data-stu-id="08729-470">Otherwise, [IP spoofing](https://www.iplocation.net/ip-spoofing) attacks are possible.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="08729-471">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="08729-471">Additional resources</span></span>

* <xref:host-and-deploy/web-farm>
* [<span data-ttu-id="08729-472">Avviso di sicurezza Microsoft CVE-2018-0787: vulnerabilità di elevazione dei privilegi di ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="08729-472">Microsoft Security Advisory CVE-2018-0787: ASP.NET Core Elevation Of Privilege Vulnerability</span></span>](https://github.com/aspnet/Announcements/issues/295)

::: moniker-end
