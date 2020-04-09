---
title: Configurare ASP.NET Core per l'uso di server proxy e servizi di bilanciamento del carico
author: rick-anderson
description: Informazioni sulla configurazione per le app ospitate dietro server proxy e servizi di bilanciamento del carico, che spesso nascondono informazioni importanti sulle richieste.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
uid: host-and-deploy/proxy-load-balancer
ms.openlocfilehash: b5c81e0cfa29cddeb1aeed1119a711fca4d91ae4
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "78659383"
---
# <a name="configure-aspnet-core-to-work-with-proxy-servers-and-load-balancers"></a><span data-ttu-id="89334-103">Configurare ASP.NET Core per l'uso di server proxy e servizi di bilanciamento del carico</span><span class="sxs-lookup"><span data-stu-id="89334-103">Configure ASP.NET Core to work with proxy servers and load balancers</span></span>

<span data-ttu-id="89334-104">Di [Chris Ross](https://github.com/Tratcher)</span><span class="sxs-lookup"><span data-stu-id="89334-104">By [Chris Ross](https://github.com/Tratcher)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="89334-105">Nella configurazione consigliata per ASP.NET Core, l'app è ospitata mediante IIS e il modulo ASP.NET Core, Nginx o Apache.</span><span class="sxs-lookup"><span data-stu-id="89334-105">In the recommended configuration for ASP.NET Core, the app is hosted using IIS/ASP.NET Core Module, Nginx, or Apache.</span></span> <span data-ttu-id="89334-106">Server proxy, servizi di bilanciamento del carico e altre appliance di rete spesso nascondono informazioni sulla richiesta prima che questa raggiunga l'app:</span><span class="sxs-lookup"><span data-stu-id="89334-106">Proxy servers, load balancers, and other network appliances often obscure information about the request before it reaches the app:</span></span>

* <span data-ttu-id="89334-107">Quando le richieste HTTPS vengono trasmesse tramite proxy su HTTP, lo schema originale (HTTPS) viene perso e deve essere inoltrato in un'intestazione.</span><span class="sxs-lookup"><span data-stu-id="89334-107">When HTTPS requests are proxied over HTTP, the original scheme (HTTPS) is lost and must be forwarded in a header.</span></span>
* <span data-ttu-id="89334-108">Poiché un'app riceve una richiesta dal proxy e non dall'effettiva origine su Internet o nella rete aziendale, anche l'indirizzo IP di origine del client deve essere inoltrato in un'intestazione.</span><span class="sxs-lookup"><span data-stu-id="89334-108">Because an app receives a request from the proxy and not its true source on the Internet or corporate network, the originating client IP address must also be forwarded in a header.</span></span>

<span data-ttu-id="89334-109">Queste informazioni potrebbero essere importanti per l'elaborazione delle richieste, ad esempio per i reindirizzamenti, l'autenticazione, la generazione di collegamenti, la valutazione dei criteri e la georilevazione dei client.</span><span class="sxs-lookup"><span data-stu-id="89334-109">This information may be important in request processing, for example in redirects, authentication, link generation, policy evaluation, and client geolocation.</span></span>

## <a name="forwarded-headers"></a><span data-ttu-id="89334-110">Intestazioni inoltrate</span><span class="sxs-lookup"><span data-stu-id="89334-110">Forwarded headers</span></span>

<span data-ttu-id="89334-111">Per convenzione, i proxy inoltrano le informazioni nelle intestazioni HTTP.</span><span class="sxs-lookup"><span data-stu-id="89334-111">By convention, proxies forward information in HTTP headers.</span></span>

| <span data-ttu-id="89334-112">Intestazione</span><span class="sxs-lookup"><span data-stu-id="89334-112">Header</span></span> | <span data-ttu-id="89334-113">Descrizione</span><span class="sxs-lookup"><span data-stu-id="89334-113">Description</span></span> |
| ------ | ----------- |
| <span data-ttu-id="89334-114">X-Forwarded-For</span><span class="sxs-lookup"><span data-stu-id="89334-114">X-Forwarded-For</span></span> | <span data-ttu-id="89334-115">Contiene informazioni sul client che ha avviato la richiesta e sui proxy successivi in una catena di proxy.</span><span class="sxs-lookup"><span data-stu-id="89334-115">Holds information about the client that initiated the request and subsequent proxies in a chain of proxies.</span></span> <span data-ttu-id="89334-116">Questo parametro può contenere indirizzi IP (e, facoltativamente, numeri di porta).</span><span class="sxs-lookup"><span data-stu-id="89334-116">This parameter may contain IP addresses (and, optionally, port numbers).</span></span> <span data-ttu-id="89334-117">In una catena di server proxy, il primo parametro indica il client in cui è stata eseguita inizialmente la richiesta.</span><span class="sxs-lookup"><span data-stu-id="89334-117">In a chain of proxy servers, the first parameter indicates the client where the request was first made.</span></span> <span data-ttu-id="89334-118">Seguono gli identificatori dei proxy successivi.</span><span class="sxs-lookup"><span data-stu-id="89334-118">Subsequent proxy identifiers follow.</span></span> <span data-ttu-id="89334-119">L'ultimo proxy della catena non è incluso nell'elenco dei parametri.</span><span class="sxs-lookup"><span data-stu-id="89334-119">The last proxy in the chain isn't in the list of parameters.</span></span> <span data-ttu-id="89334-120">L'indirizzo IP dell'ultimo proxy e, facoltativamente, un numero di porta sono disponibili come indirizzo IP remoto a livello di trasporto.</span><span class="sxs-lookup"><span data-stu-id="89334-120">The last proxy's IP address, and optionally a port number, are available as the remote IP address at the transport layer.</span></span> |
| <span data-ttu-id="89334-121">X-Forwarded-Proto</span><span class="sxs-lookup"><span data-stu-id="89334-121">X-Forwarded-Proto</span></span> | <span data-ttu-id="89334-122">Il valore dello schema di origine (HTTP/HTTPS).</span><span class="sxs-lookup"><span data-stu-id="89334-122">The value of the originating scheme (HTTP/HTTPS).</span></span> <span data-ttu-id="89334-123">Il valore può essere anche un elenco di schemi, se la richiesta ha attraversato più proxy.</span><span class="sxs-lookup"><span data-stu-id="89334-123">The value may also be a list of schemes if the request has traversed multiple proxies.</span></span> |
| <span data-ttu-id="89334-124">X-Forwarded-Host</span><span class="sxs-lookup"><span data-stu-id="89334-124">X-Forwarded-Host</span></span> | <span data-ttu-id="89334-125">Il valore originale del campo dell'intestazione host.</span><span class="sxs-lookup"><span data-stu-id="89334-125">The original value of the Host header field.</span></span> <span data-ttu-id="89334-126">I proxy in genere non modificano l'intestazione host.</span><span class="sxs-lookup"><span data-stu-id="89334-126">Usually, proxies don't modify the Host header.</span></span> <span data-ttu-id="89334-127">Vedere l'[avviso di sicurezza Microsoft CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) per informazioni su una vulnerabilità di elevazione dei privilegi che interessa i sistemi in cui il proxy non convalida o limita le intestazioni host a valori di riferimento noti.</span><span class="sxs-lookup"><span data-stu-id="89334-127">See [Microsoft Security Advisory CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) for information on an elevation-of-privileges vulnerability that affects systems where the proxy doesn't validate or restrict Host headers to known good values.</span></span> |

<span data-ttu-id="89334-128">Il middleware delle intestazioni inoltrate, dal pacchetto [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/), legge le intestazioni e compila i campi associati in <xref:Microsoft.AspNetCore.Http.HttpContext>.</span><span class="sxs-lookup"><span data-stu-id="89334-128">The Forwarded Headers Middleware, from the [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) package, reads these headers and fills in the associated fields on <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span>

<span data-ttu-id="89334-129">Il middleware aggiorna:</span><span class="sxs-lookup"><span data-stu-id="89334-129">The middleware updates:</span></span>

* <span data-ttu-id="89334-130">[Set HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress) &ndash; `X-Forwarded-For` utilizzando il valore dell'intestazione.</span><span class="sxs-lookup"><span data-stu-id="89334-130">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress) &ndash; Set using the `X-Forwarded-For` header value.</span></span> <span data-ttu-id="89334-131">Impostazioni aggiuntive influiscono sul modo in cui il middleware imposta `RemoteIpAddress`.</span><span class="sxs-lookup"><span data-stu-id="89334-131">Additional settings influence how the middleware sets `RemoteIpAddress`.</span></span> <span data-ttu-id="89334-132">Per informazioni dettagliate, vedere [Opzioni del middleware delle intestazioni inoltrate](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="89334-132">For details, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>
* <span data-ttu-id="89334-133">[Set HttpContext.Request.Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme) &ndash; `X-Forwarded-Proto` utilizzando il valore dell'intestazione.</span><span class="sxs-lookup"><span data-stu-id="89334-133">[HttpContext.Request.Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme) &ndash; Set using the `X-Forwarded-Proto` header value.</span></span>
* <span data-ttu-id="89334-134">[Set HttpContext.Request.Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host) &ndash; `X-Forwarded-Host` utilizzando il valore dell'intestazione.</span><span class="sxs-lookup"><span data-stu-id="89334-134">[HttpContext.Request.Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host) &ndash; Set using the `X-Forwarded-Host` header value.</span></span>

<span data-ttu-id="89334-135">È possibile configurare le [impostazioni predefinite](#forwarded-headers-middleware-options) del middleware delle intestazioni inoltrate.</span><span class="sxs-lookup"><span data-stu-id="89334-135">Forwarded Headers Middleware [default settings](#forwarded-headers-middleware-options) can be configured.</span></span> <span data-ttu-id="89334-136">Le impostazioni predefinite sono le seguenti:</span><span class="sxs-lookup"><span data-stu-id="89334-136">The default settings are:</span></span>

* <span data-ttu-id="89334-137">È presente *un solo proxy* tra l'app e l'origine delle richieste.</span><span class="sxs-lookup"><span data-stu-id="89334-137">There is only *one proxy* between the app and the source of the requests.</span></span>
* <span data-ttu-id="89334-138">Sono configurati indirizzi di loopback solo per reti e proxy noti.</span><span class="sxs-lookup"><span data-stu-id="89334-138">Only loopback addresses are configured for known proxies and known networks.</span></span>
* <span data-ttu-id="89334-139">Le intestazioni inoltrate sono denominate `X-Forwarded-For` e `X-Forwarded-Proto`.</span><span class="sxs-lookup"><span data-stu-id="89334-139">The forwarded headers are named `X-Forwarded-For` and `X-Forwarded-Proto`.</span></span>

<span data-ttu-id="89334-140">Non tutte le appliance di rete aggiungono le intestazioni `X-Forwarded-For` e `X-Forwarded-Proto` senza alcuna configurazione aggiuntiva.</span><span class="sxs-lookup"><span data-stu-id="89334-140">Not all network appliances add the `X-Forwarded-For` and `X-Forwarded-Proto` headers without additional configuration.</span></span> <span data-ttu-id="89334-141">Se le richieste trasmesse tramite proxy non contengono queste intestazioni quando raggiungono l'app, fare riferimento alle indicazioni del produttore dell'appliance.</span><span class="sxs-lookup"><span data-stu-id="89334-141">Consult your appliance manufacturer's guidance if proxied requests don't contain these headers when they reach the app.</span></span> <span data-ttu-id="89334-142">Se l'appliance usa nomi di intestazione diversi da `X-Forwarded-For` e `X-Forwarded-Proto`, impostare le opzioni <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> e <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> in modo che corrispondano ai nomi di intestazione usati dall'appliance.</span><span class="sxs-lookup"><span data-stu-id="89334-142">If the appliance uses different header names than `X-Forwarded-For` and `X-Forwarded-Proto`, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the appliance.</span></span> <span data-ttu-id="89334-143">Per altre informazioni, vedere [Opzioni del middleware delle intestazioni inoltrate](#forwarded-headers-middleware-options) e [Configurazione per un proxy che usa nomi di intestazione diversi](#configuration-for-a-proxy-that-uses-different-header-names).</span><span class="sxs-lookup"><span data-stu-id="89334-143">For more information, see [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) and [Configuration for a proxy that uses different header names](#configuration-for-a-proxy-that-uses-different-header-names).</span></span>

## <a name="iisiis-express-and-aspnet-core-module"></a><span data-ttu-id="89334-144">IIS/IIS Express e modulo Core ASP.NET</span><span class="sxs-lookup"><span data-stu-id="89334-144">IIS/IIS Express and ASP.NET Core Module</span></span>

<span data-ttu-id="89334-145">Il middleware delle intestazioni inoltrate è abilitato per impostazione predefinita dal [middleware di integrazione IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) quando l'app è ospitata [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) dietro IIS e il modulo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="89334-145">Forwarded Headers Middleware is enabled by default by [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when the app is hosted [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind IIS and the ASP.NET Core Module.</span></span> <span data-ttu-id="89334-146">Il middleware delle intestazioni inoltrate viene attivato per l'esecuzione prima nella pipeline del middleware con una configurazione con restrizioni specifica per il modulo ASP.NET Core per motivi di attendibilità delle intestazioni inoltrate, ad esempio lo [spoofing degli indirizzi IP](https://www.iplocation.net/ip-spoofing).</span><span class="sxs-lookup"><span data-stu-id="89334-146">Forwarded Headers Middleware is activated to run first in the middleware pipeline with a restricted configuration specific to the ASP.NET Core Module due to trust concerns with forwarded headers (for example, [IP spoofing](https://www.iplocation.net/ip-spoofing)).</span></span> <span data-ttu-id="89334-147">Il middleware è configurato per inoltrare le intestazioni `X-Forwarded-For` e `X-Forwarded-Proto` ed è limitato a un singolo proxy localhost.</span><span class="sxs-lookup"><span data-stu-id="89334-147">The middleware is configured to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers and is restricted to a single localhost proxy.</span></span> <span data-ttu-id="89334-148">Se è richiesta una configurazione aggiuntiva, vedere [Opzioni del middleware delle intestazioni inoltrate](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="89334-148">If additional configuration is required, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>

## <a name="other-proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="89334-149">Altri scenari con server proxy e servizi di bilanciamento del carico</span><span class="sxs-lookup"><span data-stu-id="89334-149">Other proxy server and load balancer scenarios</span></span>

<span data-ttu-id="89334-150">Se non si usa il [middleware di integrazione IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) per l'hosting [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model), il middleware delle intestazioni inoltrate non è abilitato per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="89334-150">Outside of using [IIS Integration](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when hosting [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model), Forwarded Headers Middleware isn't enabled by default.</span></span> <span data-ttu-id="89334-151">Il middleware delle intestazioni inoltrate deve essere abilitato per consentire a un'app di inoltrare le intestazioni inoltrate del processo con <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>.</span><span class="sxs-lookup"><span data-stu-id="89334-151">Forwarded Headers Middleware must be enabled for an app to process forwarded headers with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>.</span></span> <span data-ttu-id="89334-152">Dopo aver abilitato il middleware, se non sono specificate opzioni <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> per il middleware, le intestazioni [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) predefinite sono [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="89334-152">After enabling the middleware if no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified to the middleware, the default [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span>

<span data-ttu-id="89334-153">Configurare il middleware con <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> per l'inoltro delle intestazioni `X-Forwarded-For` e `X-Forwarded-Proto` in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="89334-153">Configure the middleware with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="89334-154">Richiamare il metodo <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure` prima di chiamare altro middleware:</span><span class="sxs-lookup"><span data-stu-id="89334-154">Invoke the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> method in `Startup.Configure` before calling other middleware:</span></span>

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
    // In ASP.NET Core 1.x, replace the following line with: app.UseIdentity();
    app.UseAuthentication();
    app.UseMvc();
}
```

> [!NOTE]
> <span data-ttu-id="89334-155">Se non vengono specificate <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> in `Startup.ConfigureServices` o direttamente nel metodo di estensione con <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>, le intestazioni predefinite da inoltrare sono [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="89334-155">If no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified in `Startup.ConfigureServices` or directly to the extension method with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>, the default headers to forward are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> <span data-ttu-id="89334-156">La proprietà <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> deve essere configurata con le intestazioni da inoltrare.</span><span class="sxs-lookup"><span data-stu-id="89334-156">The <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> property must be configured with the headers to forward.</span></span>

## <a name="nginx-configuration"></a><span data-ttu-id="89334-157">Configurazione Nginx</span><span class="sxs-lookup"><span data-stu-id="89334-157">Nginx configuration</span></span>

<span data-ttu-id="89334-158">Per inoltrare le intestazioni `X-Forwarded-For` e `X-Forwarded-Proto`, vedere <xref:host-and-deploy/linux-nginx#configure-nginx>.</span><span class="sxs-lookup"><span data-stu-id="89334-158">To forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers, see <xref:host-and-deploy/linux-nginx#configure-nginx>.</span></span> <span data-ttu-id="89334-159">Per altre informazioni, vedere [NGINX: Using the Forwarded header](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/) (NGINX: Uso dell'intestazione Forwarded).</span><span class="sxs-lookup"><span data-stu-id="89334-159">For more information, see [NGINX: Using the Forwarded header](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/).</span></span>

## <a name="apache-configuration"></a><span data-ttu-id="89334-160">Configurazione Apache</span><span class="sxs-lookup"><span data-stu-id="89334-160">Apache configuration</span></span>

<span data-ttu-id="89334-161">`X-Forwarded-For` viene aggiunta automaticamente. Vedere [Apache Module mod_proxy: Reverse Proxy Request Headers](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers) (Modulo Apache mod_proxy: Intestazioni delle richieste del proxy inverso).</span><span class="sxs-lookup"><span data-stu-id="89334-161">`X-Forwarded-For` is added automatically (see [Apache Module mod_proxy: Reverse Proxy Request Headers](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)).</span></span> <span data-ttu-id="89334-162">Per informazioni su come inoltrare l'intestazione `X-Forwarded-Proto`, vedere <xref:host-and-deploy/linux-apache#configure-apache>.</span><span class="sxs-lookup"><span data-stu-id="89334-162">For information on how to forward the `X-Forwarded-Proto` header, see <xref:host-and-deploy/linux-apache#configure-apache>.</span></span>

## <a name="forwarded-headers-middleware-options"></a><span data-ttu-id="89334-163">Opzioni del middleware delle intestazioni inoltrate</span><span class="sxs-lookup"><span data-stu-id="89334-163">Forwarded Headers Middleware options</span></span>

<span data-ttu-id="89334-164"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> controllano il comportamento del middleware delle intestazioni inoltrate.</span><span class="sxs-lookup"><span data-stu-id="89334-164"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> control the behavior of the Forwarded Headers Middleware.</span></span> <span data-ttu-id="89334-165">L'esempio seguente modifica i valori predefiniti:</span><span class="sxs-lookup"><span data-stu-id="89334-165">The following example changes the default values:</span></span>

* <span data-ttu-id="89334-166">Limitare il numero di voci nelle intestazioni inoltrate a `2`.</span><span class="sxs-lookup"><span data-stu-id="89334-166">Limit the number of entries in the forwarded headers to `2`.</span></span>
* <span data-ttu-id="89334-167">Aggiungere un indirizzo proxy noto di `127.0.10.1`.</span><span class="sxs-lookup"><span data-stu-id="89334-167">Add a known proxy address of `127.0.10.1`.</span></span>
* <span data-ttu-id="89334-168">Modificare il nome dell'intestazione inoltrata dal nome predefinito `X-Forwarded-For` a `X-Forwarded-For-My-Custom-Header-Name`.</span><span class="sxs-lookup"><span data-stu-id="89334-168">Change the forwarded header name from the default `X-Forwarded-For` to `X-Forwarded-For-My-Custom-Header-Name`.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardLimit = 2;
    options.KnownProxies.Add(IPAddress.Parse("127.0.10.1"));
    options.ForwardedForHeaderName = "X-Forwarded-For-My-Custom-Header-Name";
});
```

| <span data-ttu-id="89334-169">Opzione</span><span class="sxs-lookup"><span data-stu-id="89334-169">Option</span></span> | <span data-ttu-id="89334-170">Descrizione</span><span class="sxs-lookup"><span data-stu-id="89334-170">Description</span></span> |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> | <span data-ttu-id="89334-171">Limita gli host mediante l'intestazione `X-Forwarded-Host` ai valori specificati.</span><span class="sxs-lookup"><span data-stu-id="89334-171">Restricts hosts by the `X-Forwarded-Host` header to the values provided.</span></span><ul><li><span data-ttu-id="89334-172">I valori vengono confrontati tramite ordinal-ignore-case.</span><span class="sxs-lookup"><span data-stu-id="89334-172">Values are compared using ordinal-ignore-case.</span></span></li><li><span data-ttu-id="89334-173">I numeri di porta devono essere esclusi.</span><span class="sxs-lookup"><span data-stu-id="89334-173">Port numbers must be excluded.</span></span></li><li><span data-ttu-id="89334-174">Se l'elenco è vuoto, sono consentiti tutti gli host.</span><span class="sxs-lookup"><span data-stu-id="89334-174">If the list is empty, all hosts are allowed.</span></span></li><li><span data-ttu-id="89334-175">Un carattere jolly di primo livello `*` consente tutti gli host non vuoti.</span><span class="sxs-lookup"><span data-stu-id="89334-175">A top-level wildcard `*` allows all non-empty hosts.</span></span></li><li><span data-ttu-id="89334-176">I caratteri jolly per i sottodomini sono consentiti, ma non corrispondono al dominio radice.</span><span class="sxs-lookup"><span data-stu-id="89334-176">Subdomain wildcards are permitted but don't match the root domain.</span></span> <span data-ttu-id="89334-177">Ad esempio, `*.contoso.com` corrisponde al sottodominio `foo.contoso.com`, ma non al dominio radice `contoso.com`.</span><span class="sxs-lookup"><span data-stu-id="89334-177">For example, `*.contoso.com` matches the subdomain `foo.contoso.com` but not the root domain `contoso.com`.</span></span></li><li><span data-ttu-id="89334-178">I nomi host Unicode sono consentiti ma vengono convertiti in [Punycode](https://tools.ietf.org/html/rfc3492) per la corrispondenza.</span><span class="sxs-lookup"><span data-stu-id="89334-178">Unicode host names are allowed but are converted to [Punycode](https://tools.ietf.org/html/rfc3492) for matching.</span></span></li><li><span data-ttu-id="89334-179">Gli [indirizzi IPv6](https://tools.ietf.org/html/rfc4291) devono includere le parentesi quadre di delimitazione ed essere in [formato convenzionale](https://tools.ietf.org/html/rfc4291#section-2.2), ad esempio `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`.</span><span class="sxs-lookup"><span data-stu-id="89334-179">[IPv6 addresses](https://tools.ietf.org/html/rfc4291) must include bounding brackets and be in [conventional form](https://tools.ietf.org/html/rfc4291#section-2.2) (for example, `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`).</span></span> <span data-ttu-id="89334-180">Per gli indirizzi IPv6 non viene applicata la distinzione tra maiuscole e minuscole per la verifica dell'uguaglianza logica tra i diversi formati e non viene eseguita alcuna canonizzazione.</span><span class="sxs-lookup"><span data-stu-id="89334-180">IPv6 addresses aren't special-cased to check for logical equality between different formats, and no canonicalization is performed.</span></span></li><li><span data-ttu-id="89334-181">La mancata limitazione degli host consentiti potrebbe permettere a un utente malintenzionato di eseguire lo spoofing dei collegamenti generati dal servizio.</span><span class="sxs-lookup"><span data-stu-id="89334-181">Failure to restrict the allowed hosts may allow an attacker to spoof links generated by the service.</span></span></li></ul><span data-ttu-id="89334-182">Il valore predefinito è un insieme `IList<string>` vuoto.</span><span class="sxs-lookup"><span data-stu-id="89334-182">The default value is an empty `IList<string>`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> | <span data-ttu-id="89334-183">Usare l'intestazione specificata da questa proprietà anziché quella specificata da [ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName).</span><span class="sxs-lookup"><span data-stu-id="89334-183">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName).</span></span> <span data-ttu-id="89334-184">Questa opzione viene usata quando il proxy o il server d'inoltro non usa l'intestazione `X-Forwarded-For` ma usa un'altra intestazione per inoltrare le informazioni.</span><span class="sxs-lookup"><span data-stu-id="89334-184">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-For` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="89334-185">Il valore predefinito è `X-Forwarded-For`.</span><span class="sxs-lookup"><span data-stu-id="89334-185">The default is `X-Forwarded-For`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> | <span data-ttu-id="89334-186">Identifica i server d'inoltro che devono essere elaborati.</span><span class="sxs-lookup"><span data-stu-id="89334-186">Identifies which forwarders should be processed.</span></span> <span data-ttu-id="89334-187">Vedere [ForwardedHeaders Enum](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) (Enumerazione ForwardedHeaders) per l'elenco dei campi applicabili.</span><span class="sxs-lookup"><span data-stu-id="89334-187">See the [ForwardedHeaders Enum](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) for the list of fields that apply.</span></span> <span data-ttu-id="89334-188">I valori tipici assegnati a questa proprietà sono `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`.</span><span class="sxs-lookup"><span data-stu-id="89334-188">Typical values assigned to this property are `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`.</span></span><br><br><span data-ttu-id="89334-189">Il valore predefinito è [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="89334-189">The default value is [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHostHeaderName> | <span data-ttu-id="89334-190">Usare l'intestazione specificata da questa proprietà anziché quella specificata da [ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName).</span><span class="sxs-lookup"><span data-stu-id="89334-190">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName).</span></span> <span data-ttu-id="89334-191">Questa opzione viene usata quando il proxy o il server d'inoltro non usa l'intestazione `X-Forwarded-Host` ma usa un'altra intestazione per inoltrare le informazioni.</span><span class="sxs-lookup"><span data-stu-id="89334-191">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Host` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="89334-192">Il valore predefinito è `X-Forwarded-Host`.</span><span class="sxs-lookup"><span data-stu-id="89334-192">The default is `X-Forwarded-Host`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> | <span data-ttu-id="89334-193">Usare l'intestazione specificata da questa proprietà anziché quella specificata da [ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName).</span><span class="sxs-lookup"><span data-stu-id="89334-193">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName).</span></span> <span data-ttu-id="89334-194">Questa opzione viene usata quando il proxy o il server d'inoltro non usa l'intestazione `X-Forwarded-Proto` ma usa un'altra intestazione per inoltrare le informazioni.</span><span class="sxs-lookup"><span data-stu-id="89334-194">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Proto` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="89334-195">Il valore predefinito è `X-Forwarded-Proto`.</span><span class="sxs-lookup"><span data-stu-id="89334-195">The default is `X-Forwarded-Proto`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardLimit> | <span data-ttu-id="89334-196">Limita il numero di voci nelle intestazioni elaborate.</span><span class="sxs-lookup"><span data-stu-id="89334-196">Limits the number of entries in the headers that are processed.</span></span> <span data-ttu-id="89334-197">Impostare su `null` per disabilitare il limite, ma solo se è configurato `KnownProxies` o `KnownNetworks`.</span><span class="sxs-lookup"><span data-stu-id="89334-197">Set to `null` to disable the limit, but this should only be done if `KnownProxies` or `KnownNetworks` are configured.</span></span> <span data-ttu-id="89334-198">L'impostazione di un valore non `null` è una precauzione (ma non una garanzia) per proteggersi da proxy non configurati correttamente e richieste dannose provenienti da canali laterali in rete.</span><span class="sxs-lookup"><span data-stu-id="89334-198">Setting a non-`null` value is a precaution (but not a guarantee) to guard against misconfigured proxies and malicious requests arriving from side-channels on the network.</span></span><br><br><span data-ttu-id="89334-199">Il middleware delle intestazioni inoltrate elabora le intestazioni in ordine inverso da destra a sinistra.</span><span class="sxs-lookup"><span data-stu-id="89334-199">Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="89334-200">Se viene usato il valore predefinito (`1`), viene elaborato solo il valore più a destra delle intestazioni, a meno che non venga aumentato il valore di `ForwardLimit`.</span><span class="sxs-lookup"><span data-stu-id="89334-200">If the default value (`1`) is used, only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span><br><br><span data-ttu-id="89334-201">Il valore predefinito è `1`.</span><span class="sxs-lookup"><span data-stu-id="89334-201">The default is `1`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks> | <span data-ttu-id="89334-202">Intervalli di indirizzi delle reti note da cui accettare le intestazioni inoltrate.</span><span class="sxs-lookup"><span data-stu-id="89334-202">Address ranges of known networks to accept forwarded headers from.</span></span> <span data-ttu-id="89334-203">Specificare gli intervalli IP usando la notazione CIDR (Classless Interdomain Routing).</span><span class="sxs-lookup"><span data-stu-id="89334-203">Provide IP ranges using Classless Interdomain Routing (CIDR) notation.</span></span><br><br><span data-ttu-id="89334-204">Se il server usa socket dual mode, gli indirizzi IPv4 vengono forniti in un formato IPv6 (ad esempio, `10.0.0.1` in IPv4 rappresentato in IPv6 come `::ffff:10.0.0.1`).</span><span class="sxs-lookup"><span data-stu-id="89334-204">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="89334-205">Vedere [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="89334-205">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="89334-206">Determinare se questo formato è richiesto esaminando [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span><span class="sxs-lookup"><span data-stu-id="89334-206">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="89334-207">Per altre informazioni, vedere la sezione [Configurazione per un indirizzo IPv4 rappresentato come indirizzo IPv6](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address).</span><span class="sxs-lookup"><span data-stu-id="89334-207">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="89334-208">Il valore predefinito è un oggetto `IList`\<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> contenente una singola voce per `IPAddress.Loopback`.</span><span class="sxs-lookup"><span data-stu-id="89334-208">The default is an `IList`\<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> containing a single entry for `IPAddress.Loopback`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies> | <span data-ttu-id="89334-209">Indirizzi dei proxy noti da cui accettare le intestazioni inoltrate.</span><span class="sxs-lookup"><span data-stu-id="89334-209">Addresses of known proxies to accept forwarded headers from.</span></span> <span data-ttu-id="89334-210">Usare `KnownProxies` per specificare le corrispondenze esatte degli indirizzi IP.</span><span class="sxs-lookup"><span data-stu-id="89334-210">Use `KnownProxies` to specify exact IP address matches.</span></span><br><br><span data-ttu-id="89334-211">Se il server usa socket dual mode, gli indirizzi IPv4 vengono forniti in un formato IPv6 (ad esempio, `10.0.0.1` in IPv4 rappresentato in IPv6 come `::ffff:10.0.0.1`).</span><span class="sxs-lookup"><span data-stu-id="89334-211">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="89334-212">Vedere [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="89334-212">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="89334-213">Determinare se questo formato è richiesto esaminando [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span><span class="sxs-lookup"><span data-stu-id="89334-213">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="89334-214">Per altre informazioni, vedere la sezione [Configurazione per un indirizzo IPv4 rappresentato come indirizzo IPv6](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address).</span><span class="sxs-lookup"><span data-stu-id="89334-214">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="89334-215">Il valore predefinito è un oggetto `IList`\<<xref:System.Net.IPAddress>> contenente una singola voce per `IPAddress.IPv6Loopback`.</span><span class="sxs-lookup"><span data-stu-id="89334-215">The default is an `IList`\<<xref:System.Net.IPAddress>> containing a single entry for `IPAddress.IPv6Loopback`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalForHeaderName> | <span data-ttu-id="89334-216">Usare l'intestazione specificata da questa proprietà anziché quella specificata da [ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).</span><span class="sxs-lookup"><span data-stu-id="89334-216">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).</span></span><br><br><span data-ttu-id="89334-217">Il valore predefinito è `X-Original-For`.</span><span class="sxs-lookup"><span data-stu-id="89334-217">The default is `X-Original-For`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalHostHeaderName> | <span data-ttu-id="89334-218">Usare l'intestazione specificata da questa proprietà anziché quella specificata da [ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).</span><span class="sxs-lookup"><span data-stu-id="89334-218">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).</span></span><br><br><span data-ttu-id="89334-219">Il valore predefinito è `X-Original-Host`.</span><span class="sxs-lookup"><span data-stu-id="89334-219">The default is `X-Original-Host`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalProtoHeaderName> | <span data-ttu-id="89334-220">Usare l'intestazione specificata da questa proprietà anziché quella specificata da [ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).</span><span class="sxs-lookup"><span data-stu-id="89334-220">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).</span></span><br><br><span data-ttu-id="89334-221">Il valore predefinito è `X-Original-Proto`.</span><span class="sxs-lookup"><span data-stu-id="89334-221">The default is `X-Original-Proto`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.RequireHeaderSymmetry> | <span data-ttu-id="89334-222">Richiedere il numero di valori di intestazione da sincronizzare tra le intestazioni [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) in fase di elaborazione.</span><span class="sxs-lookup"><span data-stu-id="89334-222">Require the number of header values to be in sync between the [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) being processed.</span></span><br><br><span data-ttu-id="89334-223">Il valore predefinito in ASP.NET Core 1.x è `true`.</span><span class="sxs-lookup"><span data-stu-id="89334-223">The default in ASP.NET Core 1.x is `true`.</span></span> <span data-ttu-id="89334-224">Il valore predefinito in ASP.NET Core 2.0 o versione successiva è `false`.</span><span class="sxs-lookup"><span data-stu-id="89334-224">The default in ASP.NET Core 2.0 or later is `false`.</span></span> |

## <a name="scenarios-and-use-cases"></a><span data-ttu-id="89334-225">Scenari e casi d'uso</span><span class="sxs-lookup"><span data-stu-id="89334-225">Scenarios and use cases</span></span>

### <a name="when-it-isnt-possible-to-add-forwarded-headers-and-all-requests-are-secure"></a><span data-ttu-id="89334-226">Quando non è possibile aggiungere le intestazioni inoltrate e tutte le richieste sono sicure</span><span class="sxs-lookup"><span data-stu-id="89334-226">When it isn't possible to add forwarded headers and all requests are secure</span></span>

<span data-ttu-id="89334-227">In alcuni casi, potrebbe non essere possibile aggiungere le intestazioni inoltrate alle richieste trasmesse tramite proxy all'app.</span><span class="sxs-lookup"><span data-stu-id="89334-227">In some cases, it might not be possible to add forwarded headers to the requests proxied to the app.</span></span> <span data-ttu-id="89334-228">Se il proxy impone che tutte le richieste esterne pubbliche siano HTTPS, lo schema può essere impostato manualmente in `Startup.Configure` prima di usare qualsiasi tipo di middleware:</span><span class="sxs-lookup"><span data-stu-id="89334-228">If the proxy is enforcing that all public external requests are HTTPS, the scheme can be manually set in `Startup.Configure` before using any type of middleware:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.Scheme = "https";
    return next();
});
```

<span data-ttu-id="89334-229">Questo codice può essere disabilitato tramite una variabile di ambiente o un'altra impostazione di configurazione in un ambiente di sviluppo o di gestione temporanea.</span><span class="sxs-lookup"><span data-stu-id="89334-229">This code can be disabled with an environment variable or other configuration setting in a development or staging environment.</span></span>

### <a name="deal-with-path-base-and-proxies-that-change-the-request-path"></a><span data-ttu-id="89334-230">Gestire la base del percorso e i proxy che modificano il percorso della richiesta</span><span class="sxs-lookup"><span data-stu-id="89334-230">Deal with path base and proxies that change the request path</span></span>

<span data-ttu-id="89334-231">Alcuni proxy passano il percorso senza modifiche, ma con un percorso di base dell'app che deve essere rimosso per consentire il corretto funzionamento del routing.</span><span class="sxs-lookup"><span data-stu-id="89334-231">Some proxies pass the path intact but with an app base path that should be removed so that routing works properly.</span></span> <span data-ttu-id="89334-232">Il middleware [UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) suddivide il percorso in [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) e il percorso di base dell'app in [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).</span><span class="sxs-lookup"><span data-stu-id="89334-232">[UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) middleware splits the path into [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) and the app base path into [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).</span></span>

<span data-ttu-id="89334-233">Se `/foo` è il percorso di base dell'app per un percorso proxy passato come `/foo/api/1`, il middleware imposta `Request.PathBase` su `/foo` e `Request.Path` su `/api/1` con il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="89334-233">If `/foo` is the app base path for a proxy path passed as `/foo/api/1`, the middleware sets `Request.PathBase` to `/foo` and `Request.Path` to `/api/1` with the following command:</span></span>

```csharp
app.UsePathBase("/foo");
```

<span data-ttu-id="89334-234">Il percorso originale e la base del percorso vengono riapplicati quando il middleware viene chiamato nuovamente in direzione inversa.</span><span class="sxs-lookup"><span data-stu-id="89334-234">The original path and path base are reapplied when the middleware is called again in reverse.</span></span> <span data-ttu-id="89334-235">Per altre informazioni dell'elaborazione degli ordini del middleware, vedere <xref:fundamentals/middleware/index>.</span><span class="sxs-lookup"><span data-stu-id="89334-235">For more information on middleware order processing, see <xref:fundamentals/middleware/index>.</span></span>

<span data-ttu-id="89334-236">Se il proxy taglia il percorso (ad esempio, inoltrando `/foo/api/1` a `/api/1`), correggere i reindirizzamenti e i collegamenti impostando la proprietà [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) della richiesta:</span><span class="sxs-lookup"><span data-stu-id="89334-236">If the proxy trims the path (for example, forwarding `/foo/api/1` to `/api/1`), fix redirects and links by setting the request's [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) property:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.PathBase = new PathString("/foo");
    return next();
});
```

<span data-ttu-id="89334-237">Se il proxy aggiunge i dati del percorso, eliminare parte del percorso per correggere i reindirizzamenti e i collegamenti usando <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> ed eseguendo l'assegnazione alla proprietà <xref:Microsoft.AspNetCore.Http.HttpRequest.Path>:</span><span class="sxs-lookup"><span data-stu-id="89334-237">If the proxy is adding path data, discard part of the path to fix redirects and links by using <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> and assigning to the <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> property:</span></span>

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

### <a name="configuration-for-a-proxy-that-uses-different-header-names"></a><span data-ttu-id="89334-238">Configurazione per un proxy che usa nomi di intestazione diversi</span><span class="sxs-lookup"><span data-stu-id="89334-238">Configuration for a proxy that uses different header names</span></span>

<span data-ttu-id="89334-239">Se il proxy non usa intestazioni denominate `X-Forwarded-For` e `X-Forwarded-Proto` per inoltrare l'indirizzo proxy o la porta e le informazioni dello scherma originali, impostare le opzioni <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> e <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> in modo che corrispondano ai nomi di intestazione usati dal proxy:</span><span class="sxs-lookup"><span data-stu-id="89334-239">If the proxy doesn't use headers named `X-Forwarded-For` and `X-Forwarded-Proto` to forward the proxy address/port and originating scheme information, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the proxy:</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedForHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-For_Header";
    options.ForwardedProtoHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-Proto_Header";
});
```

### <a name="configuration-for-an-ipv4-address-represented-as-an-ipv6-address"></a><span data-ttu-id="89334-240">Configurazione per un indirizzo IPv4 rappresentato come indirizzo IPv6</span><span class="sxs-lookup"><span data-stu-id="89334-240">Configuration for an IPv4 address represented as an IPv6 address</span></span>

<span data-ttu-id="89334-241">Se il server usa socket dual mode, gli indirizzi IPv4 vengono forniti in un formato IPv6 (ad esempio, `10.0.0.1` in IPv4 rappresentato in IPv6 come `::ffff:10.0.0.1` o `::ffff:a00:1`).</span><span class="sxs-lookup"><span data-stu-id="89334-241">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1` or `::ffff:a00:1`).</span></span> <span data-ttu-id="89334-242">Vedere [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="89334-242">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="89334-243">Determinare se questo formato è richiesto esaminando [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span><span class="sxs-lookup"><span data-stu-id="89334-243">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span>

<span data-ttu-id="89334-244">Nell'esempio seguente un indirizzo di rete che fornisce intestazioni inoltrate viene aggiunto all'elenco `KnownNetworks` in formato IPv6.</span><span class="sxs-lookup"><span data-stu-id="89334-244">In the following example, a network address that supplies forwarded headers is added to the `KnownNetworks` list in IPv6 format.</span></span>

<span data-ttu-id="89334-245">Indirizzo IPv4: `10.11.12.1/8`</span><span class="sxs-lookup"><span data-stu-id="89334-245">IPv4 address: `10.11.12.1/8`</span></span>

<span data-ttu-id="89334-246">Indirizzo IPv6 convertito: `::ffff:10.11.12.1`</span><span class="sxs-lookup"><span data-stu-id="89334-246">Converted IPv6 address: `::ffff:10.11.12.1`</span></span>  
<span data-ttu-id="89334-247">Lunghezza del prefisso convertito: 104</span><span class="sxs-lookup"><span data-stu-id="89334-247">Converted prefix length: 104</span></span>

<span data-ttu-id="89334-248">È anche possibile specificare l'indirizzo in formato esadecimale (`10.11.12.1` rappresentato in IPv6 come `::ffff:0a0b:0c01`).</span><span class="sxs-lookup"><span data-stu-id="89334-248">You can also supply the address in hexadecimal format (`10.11.12.1` represented in IPv6 as `::ffff:0a0b:0c01`).</span></span> <span data-ttu-id="89334-249">Durante la conversione di un indirizzo IPv4 in IPv6, aggiungere 96 alla lunghezza del prefisso CIDR (`8` nell'esempio) per tenere conto del prefisso IPv6 aggiuntivo `::ffff:` (8 + 96 = 104).</span><span class="sxs-lookup"><span data-stu-id="89334-249">When converting an IPv4 address to IPv6, add 96 to the CIDR Prefix Length (`8` in the example) to account for the additional `::ffff:` IPv6 prefix (8 + 96 = 104).</span></span> 

```csharp
// To access IPNetwork and IPAddress, add the following namespaces:
// using using System.Net;
// using Microsoft.AspNetCore.HttpOverrides;
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedHeaders =
        ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto;
    options.KnownNetworks.Add(new IPNetwork(
        IPAddress.Parse("::ffff:10.11.12.1"), 104));
});
```

## <a name="forward-the-scheme-for-linux-and-non-iis-reverse-proxies"></a><span data-ttu-id="89334-250">Inoltrare lo schema per Linux e proxy inversi non IIS</span><span class="sxs-lookup"><span data-stu-id="89334-250">Forward the scheme for Linux and non-IIS reverse proxies</span></span>

<span data-ttu-id="89334-251">Con le app che chiamano <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> e <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> il sito si ritrova in un ciclo infinito se distribuito in un servizio app Linux di Azure, in una macchina virtuale Linux di Azure o dietro eventuali altri proxy inversi diversi da IIS.</span><span class="sxs-lookup"><span data-stu-id="89334-251">Apps that call <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> and <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> put a site into an infinite loop if deployed to an Azure Linux App Service, Azure Linux virtual machine (VM), or behind any other reverse proxy besides IIS.</span></span> <span data-ttu-id="89334-252">TLS viene terminato dal proxy inverso e Kestrel non viene a conoscenza dello schema di richiesta corretto.</span><span class="sxs-lookup"><span data-stu-id="89334-252">TLS is terminated by the reverse proxy, and Kestrel isn't made aware of the correct request scheme.</span></span> <span data-ttu-id="89334-253">Si verificano errori anche per OAuth e OIDC in questa configurazione perché generano reindirizzamenti non corretti.</span><span class="sxs-lookup"><span data-stu-id="89334-253">OAuth and OIDC also fail in this configuration because they generate incorrect redirects.</span></span> <span data-ttu-id="89334-254"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> aggiunge e configura il middleware delle intestazioni inoltrate in caso di esecuzione dietro IIS, ma non esiste alcuna configurazione automatica corrispondente per Linux (integrazione di Apache o Nginx).</span><span class="sxs-lookup"><span data-stu-id="89334-254"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> adds and configures Forwarded Headers Middleware when running behind IIS, but there's no matching automatic configuration for Linux (Apache or Nginx integration).</span></span>

<span data-ttu-id="89334-255">Per inoltrare lo schema dal proxy in scenari non IIS, aggiungere e configurare il middleware delle intestazioni inoltrate.</span><span class="sxs-lookup"><span data-stu-id="89334-255">To forward the scheme from the proxy in non-IIS scenarios, add and configure Forwarded Headers Middleware.</span></span> <span data-ttu-id="89334-256">In `Startup.ConfigureServices` usare il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="89334-256">In `Startup.ConfigureServices`, use the following code:</span></span>

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

## <a name="certificate-forwarding"></a><span data-ttu-id="89334-257">Inoltro di certificati</span><span class="sxs-lookup"><span data-stu-id="89334-257">Certificate forwarding</span></span> 

### <a name="azure"></a><span data-ttu-id="89334-258">Azure</span><span class="sxs-lookup"><span data-stu-id="89334-258">Azure</span></span>

<span data-ttu-id="89334-259">Per configurare il servizio app di Azure per l'inoltro dei certificati, vedere [Configurare l'autenticazione reciproca TLS per](/azure/app-service/app-service-web-configure-tls-mutual-auth)il servizio app di Azure.To configure Azure App Service for certificate forwarding, see Configure TLS mutual authentication for Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="89334-259">To configure Azure App Service for certificate forwarding, see [Configure TLS mutual authentication for Azure App Service](/azure/app-service/app-service-web-configure-tls-mutual-auth).</span></span> <span data-ttu-id="89334-260">Le indicazioni seguenti riguardano la configurazione dell'app ASP.NET Core.The following guidance app etains to configuring the ASP.NET Core app.</span><span class="sxs-lookup"><span data-stu-id="89334-260">The following guidance pertains to configuring the ASP.NET Core app.</span></span>

<span data-ttu-id="89334-261">In `Startup.Configure`, aggiungere il codice `app.UseAuthentication();`seguente prima della chiamata a :</span><span class="sxs-lookup"><span data-stu-id="89334-261">In `Startup.Configure`, add the following code before the call to `app.UseAuthentication();`:</span></span>

```csharp
app.UseCertificateForwarding();
```


<span data-ttu-id="89334-262">Configurare il middleware per l'inoltro dei certificati per specificare il nome dell'intestazione usato da Azure.Configure Certificate Forwarding Middleware to specify the header name that Azure uses.</span><span class="sxs-lookup"><span data-stu-id="89334-262">Configure Certificate Forwarding Middleware to specify the header name that Azure uses.</span></span> <span data-ttu-id="89334-263">In `Startup.ConfigureServices`, aggiungere il codice seguente per configurare l'intestazione da cui il middleware compila un certificato:</span><span class="sxs-lookup"><span data-stu-id="89334-263">In `Startup.ConfigureServices`, add the following code to configure the header from which the middleware builds a certificate:</span></span>

```csharp
services.AddCertificateForwarding(options =>
    options.CertificateHeader = "X-ARR-ClientCert");
```

### <a name="other-web-proxies"></a><span data-ttu-id="89334-264">Altri proxy Web</span><span class="sxs-lookup"><span data-stu-id="89334-264">Other web proxies</span></span>

<span data-ttu-id="89334-265">Se viene utilizzato un proxy che non è IIS o Azure App Service Application Request Routing (ARR), configurare il proxy per inoltrare il certificato ricevuto in un'intestazione HTTP.</span><span class="sxs-lookup"><span data-stu-id="89334-265">If a proxy is used that isn't IIS or Azure App Service's Application Request Routing (ARR), configure the proxy to forward the certificate that it received in an HTTP header.</span></span> <span data-ttu-id="89334-266">In `Startup.Configure`, aggiungere il codice `app.UseAuthentication();`seguente prima della chiamata a :</span><span class="sxs-lookup"><span data-stu-id="89334-266">In `Startup.Configure`, add the following code before the call to `app.UseAuthentication();`:</span></span>

```csharp
app.UseCertificateForwarding();
```

<span data-ttu-id="89334-267">Configurare il middleware di inoltro certificati per specificare il nome dell'intestazione.</span><span class="sxs-lookup"><span data-stu-id="89334-267">Configure the Certificate Forwarding Middleware to specify the header name.</span></span> <span data-ttu-id="89334-268">In `Startup.ConfigureServices`, aggiungere il codice seguente per configurare l'intestazione da cui il middleware compila un certificato:</span><span class="sxs-lookup"><span data-stu-id="89334-268">In `Startup.ConfigureServices`, add the following code to configure the header from which the middleware builds a certificate:</span></span>

```csharp
services.AddCertificateForwarding(options =>
    options.CertificateHeader = "YOUR_CERTIFICATE_HEADER_NAME");
```

<span data-ttu-id="89334-269">Se il proxy non è basato su base64-codifica il certificato `HeaderConverter` (come nel caso di Nginx), impostare l'opzione.</span><span class="sxs-lookup"><span data-stu-id="89334-269">If the proxy isn't base64-encoding the certificate (as is the case with Nginx), set the `HeaderConverter` option.</span></span> <span data-ttu-id="89334-270">Si consideri l'esempio seguente in `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="89334-270">Consider the following example in `Startup.ConfigureServices`:</span></span>

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

## <a name="troubleshoot"></a><span data-ttu-id="89334-271">Risolvere problemi</span><span class="sxs-lookup"><span data-stu-id="89334-271">Troubleshoot</span></span>

<span data-ttu-id="89334-272">Quando le intestazioni non vengono inoltrate come previsto, abilitare la [registrazione](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="89334-272">When headers aren't forwarded as expected, enable [logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="89334-273">Se i log non forniscono informazioni sufficienti per risolvere il problema, enumerare le intestazioni delle richieste ricevute dal server.</span><span class="sxs-lookup"><span data-stu-id="89334-273">If the logs don't provide sufficient information to troubleshoot the problem, enumerate the request headers received by the server.</span></span> <span data-ttu-id="89334-274">Usare il middleware inline per scrivere le intestazioni di richiesta in una risposta dell'app o per registrare le intestazioni.</span><span class="sxs-lookup"><span data-stu-id="89334-274">Use inline middleware to write request headers to an app response or log the headers.</span></span> 

<span data-ttu-id="89334-275">Per scrivere le intestazioni nella risposta dell'app, aggiungere il middleware inline di terminale seguente immediatamente dopo la chiamata a <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="89334-275">To write the headers to the app's response, place the following terminal inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`:</span></span>

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

<span data-ttu-id="89334-276">È possibile scrivere nei log invece che nel corpo della risposta.</span><span class="sxs-lookup"><span data-stu-id="89334-276">You can write to logs instead of the response body.</span></span> <span data-ttu-id="89334-277">La scrittura nei log permette il normale funzionamento del sito durante il debug.</span><span class="sxs-lookup"><span data-stu-id="89334-277">Writing to logs allows the site to function normally while debugging.</span></span>

<span data-ttu-id="89334-278">Per scrivere nei log invece che nel corpo della risposta:</span><span class="sxs-lookup"><span data-stu-id="89334-278">To write logs rather than to the response body:</span></span>

* <span data-ttu-id="89334-279">Inserire `ILogger<Startup>` nella classe `Startup` come descritto in [Creare log in Startup](xref:fundamentals/logging/index#create-logs-in-startup).</span><span class="sxs-lookup"><span data-stu-id="89334-279">Inject `ILogger<Startup>` into the `Startup` class as described in [Create logs in Startup](xref:fundamentals/logging/index#create-logs-in-startup).</span></span>
* <span data-ttu-id="89334-280">Aggiungere il middleware inline seguente immediatamente dopo la chiamata a <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="89334-280">Place the following inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`.</span></span>

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

<span data-ttu-id="89334-281">Durante l'elaborazione, i valori di `X-Forwarded-{For|Proto|Host}` vengono spostati in `X-Original-{For|Proto|Host}`.</span><span class="sxs-lookup"><span data-stu-id="89334-281">When processed, `X-Forwarded-{For|Proto|Host}` values are moved to `X-Original-{For|Proto|Host}`.</span></span> <span data-ttu-id="89334-282">Se sono presenti più valori in una determinata intestazione, il middleware delle intestazioni inoltrate elabora le intestazioni in ordine inverso da destra a sinistra.</span><span class="sxs-lookup"><span data-stu-id="89334-282">If there are multiple values in a given header, Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="89334-283">Il valore predefinito di `ForwardLimit` è `1` (uno) e, pertanto, viene elaborato solo il valore più a destra delle intestazioni, a meno che non venga aumentato il valore di `ForwardLimit`.</span><span class="sxs-lookup"><span data-stu-id="89334-283">The default `ForwardLimit` is `1` (one), so only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span>

<span data-ttu-id="89334-284">L'indirizzo IP remoto originale della richiesta deve corrispondere a una voce negli elenchi `KnownProxies` o `KnownNetworks` prima dell'elaborazione delle intestazioni inoltrate.</span><span class="sxs-lookup"><span data-stu-id="89334-284">The request's original remote IP must match an entry in the `KnownProxies` or `KnownNetworks` lists before forwarded headers are processed.</span></span> <span data-ttu-id="89334-285">Questo riduce lo spoofing delle intestazioni, non accettando server di inoltro da proxy non attendibili.</span><span class="sxs-lookup"><span data-stu-id="89334-285">This limits header spoofing by not accepting forwarders from untrusted proxies.</span></span> <span data-ttu-id="89334-286">Quando viene rilevato un proxy sconosciuto, la registrazione indica l'indirizzo del proxy:</span><span class="sxs-lookup"><span data-stu-id="89334-286">When an unknown proxy is detected, logging indicates the address of the proxy:</span></span>

```console
September 20th 2018, 15:49:44.168 Unknown proxy: 10.0.0.100:54321
```

<span data-ttu-id="89334-287">Nell'esempio precedente, 10.0.0.100 è un server proxy.</span><span class="sxs-lookup"><span data-stu-id="89334-287">In the preceding example, 10.0.0.100 is a proxy server.</span></span> <span data-ttu-id="89334-288">Se il server è un proxy attendibile, aggiungere l'indirizzo IP del server a `KnownProxies` (o aggiungere una rete attendibile a `KnownNetworks`) in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="89334-288">If the server is a trusted proxy, add the server's IP address to `KnownProxies` (or add a trusted network to `KnownNetworks`) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="89334-289">Per altre informazioni, vedere la sezione [Opzioni del middleware delle intestazioni inoltrate](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="89334-289">For more information, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) section.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

> [!IMPORTANT]
> <span data-ttu-id="89334-290">Consentire solo a reti e proxy attendibili di inoltrare le intestazioni.</span><span class="sxs-lookup"><span data-stu-id="89334-290">Only allow trusted proxies and networks to forward headers.</span></span> <span data-ttu-id="89334-291">In caso contrario, possono verificarsi attacchi di [spoofing degli indirizzi IP](https://www.iplocation.net/ip-spoofing).</span><span class="sxs-lookup"><span data-stu-id="89334-291">Otherwise, [IP spoofing](https://www.iplocation.net/ip-spoofing) attacks are possible.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="89334-292">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="89334-292">Additional resources</span></span>

* <xref:host-and-deploy/web-farm>
* [<span data-ttu-id="89334-293">Avviso di sicurezza Microsoft CVE-2018-0787: vulnerabilità di elevazione dei privilegi di ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="89334-293">Microsoft Security Advisory CVE-2018-0787: ASP.NET Core Elevation Of Privilege Vulnerability</span></span>](https://github.com/aspnet/Announcements/issues/295)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="89334-294">Nella configurazione consigliata per ASP.NET Core, l'app è ospitata mediante IIS e il modulo ASP.NET Core, Nginx o Apache.</span><span class="sxs-lookup"><span data-stu-id="89334-294">In the recommended configuration for ASP.NET Core, the app is hosted using IIS/ASP.NET Core Module, Nginx, or Apache.</span></span> <span data-ttu-id="89334-295">Server proxy, servizi di bilanciamento del carico e altre appliance di rete spesso nascondono informazioni sulla richiesta prima che questa raggiunga l'app:</span><span class="sxs-lookup"><span data-stu-id="89334-295">Proxy servers, load balancers, and other network appliances often obscure information about the request before it reaches the app:</span></span>

* <span data-ttu-id="89334-296">Quando le richieste HTTPS vengono trasmesse tramite proxy su HTTP, lo schema originale (HTTPS) viene perso e deve essere inoltrato in un'intestazione.</span><span class="sxs-lookup"><span data-stu-id="89334-296">When HTTPS requests are proxied over HTTP, the original scheme (HTTPS) is lost and must be forwarded in a header.</span></span>
* <span data-ttu-id="89334-297">Poiché un'app riceve una richiesta dal proxy e non dall'effettiva origine su Internet o nella rete aziendale, anche l'indirizzo IP di origine del client deve essere inoltrato in un'intestazione.</span><span class="sxs-lookup"><span data-stu-id="89334-297">Because an app receives a request from the proxy and not its true source on the Internet or corporate network, the originating client IP address must also be forwarded in a header.</span></span>

<span data-ttu-id="89334-298">Queste informazioni potrebbero essere importanti per l'elaborazione delle richieste, ad esempio per i reindirizzamenti, l'autenticazione, la generazione di collegamenti, la valutazione dei criteri e la georilevazione dei client.</span><span class="sxs-lookup"><span data-stu-id="89334-298">This information may be important in request processing, for example in redirects, authentication, link generation, policy evaluation, and client geolocation.</span></span>

## <a name="forwarded-headers"></a><span data-ttu-id="89334-299">Intestazioni inoltrate</span><span class="sxs-lookup"><span data-stu-id="89334-299">Forwarded headers</span></span>

<span data-ttu-id="89334-300">Per convenzione, i proxy inoltrano le informazioni nelle intestazioni HTTP.</span><span class="sxs-lookup"><span data-stu-id="89334-300">By convention, proxies forward information in HTTP headers.</span></span>

| <span data-ttu-id="89334-301">Intestazione</span><span class="sxs-lookup"><span data-stu-id="89334-301">Header</span></span> | <span data-ttu-id="89334-302">Descrizione</span><span class="sxs-lookup"><span data-stu-id="89334-302">Description</span></span> |
| ------ | ----------- |
| <span data-ttu-id="89334-303">X-Forwarded-For</span><span class="sxs-lookup"><span data-stu-id="89334-303">X-Forwarded-For</span></span> | <span data-ttu-id="89334-304">Contiene informazioni sul client che ha avviato la richiesta e sui proxy successivi in una catena di proxy.</span><span class="sxs-lookup"><span data-stu-id="89334-304">Holds information about the client that initiated the request and subsequent proxies in a chain of proxies.</span></span> <span data-ttu-id="89334-305">Questo parametro può contenere indirizzi IP (e, facoltativamente, numeri di porta).</span><span class="sxs-lookup"><span data-stu-id="89334-305">This parameter may contain IP addresses (and, optionally, port numbers).</span></span> <span data-ttu-id="89334-306">In una catena di server proxy, il primo parametro indica il client in cui è stata eseguita inizialmente la richiesta.</span><span class="sxs-lookup"><span data-stu-id="89334-306">In a chain of proxy servers, the first parameter indicates the client where the request was first made.</span></span> <span data-ttu-id="89334-307">Seguono gli identificatori dei proxy successivi.</span><span class="sxs-lookup"><span data-stu-id="89334-307">Subsequent proxy identifiers follow.</span></span> <span data-ttu-id="89334-308">L'ultimo proxy della catena non è incluso nell'elenco dei parametri.</span><span class="sxs-lookup"><span data-stu-id="89334-308">The last proxy in the chain isn't in the list of parameters.</span></span> <span data-ttu-id="89334-309">L'indirizzo IP dell'ultimo proxy e, facoltativamente, un numero di porta sono disponibili come indirizzo IP remoto a livello di trasporto.</span><span class="sxs-lookup"><span data-stu-id="89334-309">The last proxy's IP address, and optionally a port number, are available as the remote IP address at the transport layer.</span></span> |
| <span data-ttu-id="89334-310">X-Forwarded-Proto</span><span class="sxs-lookup"><span data-stu-id="89334-310">X-Forwarded-Proto</span></span> | <span data-ttu-id="89334-311">Il valore dello schema di origine (HTTP/HTTPS).</span><span class="sxs-lookup"><span data-stu-id="89334-311">The value of the originating scheme (HTTP/HTTPS).</span></span> <span data-ttu-id="89334-312">Il valore può essere anche un elenco di schemi, se la richiesta ha attraversato più proxy.</span><span class="sxs-lookup"><span data-stu-id="89334-312">The value may also be a list of schemes if the request has traversed multiple proxies.</span></span> |
| <span data-ttu-id="89334-313">X-Forwarded-Host</span><span class="sxs-lookup"><span data-stu-id="89334-313">X-Forwarded-Host</span></span> | <span data-ttu-id="89334-314">Il valore originale del campo dell'intestazione host.</span><span class="sxs-lookup"><span data-stu-id="89334-314">The original value of the Host header field.</span></span> <span data-ttu-id="89334-315">I proxy in genere non modificano l'intestazione host.</span><span class="sxs-lookup"><span data-stu-id="89334-315">Usually, proxies don't modify the Host header.</span></span> <span data-ttu-id="89334-316">Vedere l'[avviso di sicurezza Microsoft CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) per informazioni su una vulnerabilità di elevazione dei privilegi che interessa i sistemi in cui il proxy non convalida o limita le intestazioni host a valori di riferimento noti.</span><span class="sxs-lookup"><span data-stu-id="89334-316">See [Microsoft Security Advisory CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) for information on an elevation-of-privileges vulnerability that affects systems where the proxy doesn't validate or restrict Host headers to known good values.</span></span> |

<span data-ttu-id="89334-317">Il middleware delle intestazioni inoltrate, dal pacchetto [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/), legge le intestazioni e compila i campi associati in <xref:Microsoft.AspNetCore.Http.HttpContext>.</span><span class="sxs-lookup"><span data-stu-id="89334-317">The Forwarded Headers Middleware, from the [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) package, reads these headers and fills in the associated fields on <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span>

<span data-ttu-id="89334-318">Il middleware aggiorna:</span><span class="sxs-lookup"><span data-stu-id="89334-318">The middleware updates:</span></span>

* <span data-ttu-id="89334-319">[Set HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress) &ndash; `X-Forwarded-For` utilizzando il valore dell'intestazione.</span><span class="sxs-lookup"><span data-stu-id="89334-319">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress) &ndash; Set using the `X-Forwarded-For` header value.</span></span> <span data-ttu-id="89334-320">Impostazioni aggiuntive influiscono sul modo in cui il middleware imposta `RemoteIpAddress`.</span><span class="sxs-lookup"><span data-stu-id="89334-320">Additional settings influence how the middleware sets `RemoteIpAddress`.</span></span> <span data-ttu-id="89334-321">Per informazioni dettagliate, vedere [Opzioni del middleware delle intestazioni inoltrate](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="89334-321">For details, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>
* <span data-ttu-id="89334-322">[Set HttpContext.Request.Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme) &ndash; `X-Forwarded-Proto` utilizzando il valore dell'intestazione.</span><span class="sxs-lookup"><span data-stu-id="89334-322">[HttpContext.Request.Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme) &ndash; Set using the `X-Forwarded-Proto` header value.</span></span>
* <span data-ttu-id="89334-323">[Set HttpContext.Request.Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host) &ndash; `X-Forwarded-Host` utilizzando il valore dell'intestazione.</span><span class="sxs-lookup"><span data-stu-id="89334-323">[HttpContext.Request.Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host) &ndash; Set using the `X-Forwarded-Host` header value.</span></span>

<span data-ttu-id="89334-324">È possibile configurare le [impostazioni predefinite](#forwarded-headers-middleware-options) del middleware delle intestazioni inoltrate.</span><span class="sxs-lookup"><span data-stu-id="89334-324">Forwarded Headers Middleware [default settings](#forwarded-headers-middleware-options) can be configured.</span></span> <span data-ttu-id="89334-325">Le impostazioni predefinite sono le seguenti:</span><span class="sxs-lookup"><span data-stu-id="89334-325">The default settings are:</span></span>

* <span data-ttu-id="89334-326">È presente *un solo proxy* tra l'app e l'origine delle richieste.</span><span class="sxs-lookup"><span data-stu-id="89334-326">There is only *one proxy* between the app and the source of the requests.</span></span>
* <span data-ttu-id="89334-327">Sono configurati indirizzi di loopback solo per reti e proxy noti.</span><span class="sxs-lookup"><span data-stu-id="89334-327">Only loopback addresses are configured for known proxies and known networks.</span></span>
* <span data-ttu-id="89334-328">Le intestazioni inoltrate sono denominate `X-Forwarded-For` e `X-Forwarded-Proto`.</span><span class="sxs-lookup"><span data-stu-id="89334-328">The forwarded headers are named `X-Forwarded-For` and `X-Forwarded-Proto`.</span></span>

<span data-ttu-id="89334-329">Non tutte le appliance di rete aggiungono le intestazioni `X-Forwarded-For` e `X-Forwarded-Proto` senza alcuna configurazione aggiuntiva.</span><span class="sxs-lookup"><span data-stu-id="89334-329">Not all network appliances add the `X-Forwarded-For` and `X-Forwarded-Proto` headers without additional configuration.</span></span> <span data-ttu-id="89334-330">Se le richieste trasmesse tramite proxy non contengono queste intestazioni quando raggiungono l'app, fare riferimento alle indicazioni del produttore dell'appliance.</span><span class="sxs-lookup"><span data-stu-id="89334-330">Consult your appliance manufacturer's guidance if proxied requests don't contain these headers when they reach the app.</span></span> <span data-ttu-id="89334-331">Se l'appliance usa nomi di intestazione diversi da `X-Forwarded-For` e `X-Forwarded-Proto`, impostare le opzioni <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> e <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> in modo che corrispondano ai nomi di intestazione usati dall'appliance.</span><span class="sxs-lookup"><span data-stu-id="89334-331">If the appliance uses different header names than `X-Forwarded-For` and `X-Forwarded-Proto`, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the appliance.</span></span> <span data-ttu-id="89334-332">Per altre informazioni, vedere [Opzioni del middleware delle intestazioni inoltrate](#forwarded-headers-middleware-options) e [Configurazione per un proxy che usa nomi di intestazione diversi](#configuration-for-a-proxy-that-uses-different-header-names).</span><span class="sxs-lookup"><span data-stu-id="89334-332">For more information, see [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) and [Configuration for a proxy that uses different header names](#configuration-for-a-proxy-that-uses-different-header-names).</span></span>

## <a name="iisiis-express-and-aspnet-core-module"></a><span data-ttu-id="89334-333">IIS/IIS Express e modulo Core ASP.NET</span><span class="sxs-lookup"><span data-stu-id="89334-333">IIS/IIS Express and ASP.NET Core Module</span></span>

<span data-ttu-id="89334-334">Il middleware delle intestazioni inoltrate è abilitato per impostazione predefinita dal [middleware di integrazione IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) quando l'app è ospitata [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) dietro IIS e il modulo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="89334-334">Forwarded Headers Middleware is enabled by default by [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when the app is hosted [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind IIS and the ASP.NET Core Module.</span></span> <span data-ttu-id="89334-335">Il middleware delle intestazioni inoltrate viene attivato per l'esecuzione prima nella pipeline del middleware con una configurazione con restrizioni specifica per il modulo ASP.NET Core per motivi di attendibilità delle intestazioni inoltrate, ad esempio lo [spoofing degli indirizzi IP](https://www.iplocation.net/ip-spoofing).</span><span class="sxs-lookup"><span data-stu-id="89334-335">Forwarded Headers Middleware is activated to run first in the middleware pipeline with a restricted configuration specific to the ASP.NET Core Module due to trust concerns with forwarded headers (for example, [IP spoofing](https://www.iplocation.net/ip-spoofing)).</span></span> <span data-ttu-id="89334-336">Il middleware è configurato per inoltrare le intestazioni `X-Forwarded-For` e `X-Forwarded-Proto` ed è limitato a un singolo proxy localhost.</span><span class="sxs-lookup"><span data-stu-id="89334-336">The middleware is configured to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers and is restricted to a single localhost proxy.</span></span> <span data-ttu-id="89334-337">Se è richiesta una configurazione aggiuntiva, vedere [Opzioni del middleware delle intestazioni inoltrate](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="89334-337">If additional configuration is required, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>

## <a name="other-proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="89334-338">Altri scenari con server proxy e servizi di bilanciamento del carico</span><span class="sxs-lookup"><span data-stu-id="89334-338">Other proxy server and load balancer scenarios</span></span>

<span data-ttu-id="89334-339">Se non si usa il [middleware di integrazione IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) per l'hosting [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model), il middleware delle intestazioni inoltrate non è abilitato per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="89334-339">Outside of using [IIS Integration](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when hosting [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model), Forwarded Headers Middleware isn't enabled by default.</span></span> <span data-ttu-id="89334-340">Il middleware delle intestazioni inoltrate deve essere abilitato per consentire a un'app di inoltrare le intestazioni inoltrate del processo con <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>.</span><span class="sxs-lookup"><span data-stu-id="89334-340">Forwarded Headers Middleware must be enabled for an app to process forwarded headers with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>.</span></span> <span data-ttu-id="89334-341">Dopo aver abilitato il middleware, se non sono specificate opzioni <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> per il middleware, le intestazioni [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) predefinite sono [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="89334-341">After enabling the middleware if no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified to the middleware, the default [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span>

<span data-ttu-id="89334-342">Configurare il middleware con <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> per l'inoltro delle intestazioni `X-Forwarded-For` e `X-Forwarded-Proto` in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="89334-342">Configure the middleware with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="89334-343">Richiamare il metodo <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure` prima di chiamare altro middleware:</span><span class="sxs-lookup"><span data-stu-id="89334-343">Invoke the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> method in `Startup.Configure` before calling other middleware:</span></span>

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
    // In ASP.NET Core 1.x, replace the following line with: app.UseIdentity();
    app.UseAuthentication();
    app.UseMvc();
}
```

> [!NOTE]
> <span data-ttu-id="89334-344">Se non vengono specificate <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> in `Startup.ConfigureServices` o direttamente nel metodo di estensione con <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>, le intestazioni predefinite da inoltrare sono [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="89334-344">If no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified in `Startup.ConfigureServices` or directly to the extension method with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>, the default headers to forward are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> <span data-ttu-id="89334-345">La proprietà <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> deve essere configurata con le intestazioni da inoltrare.</span><span class="sxs-lookup"><span data-stu-id="89334-345">The <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> property must be configured with the headers to forward.</span></span>

## <a name="nginx-configuration"></a><span data-ttu-id="89334-346">Configurazione Nginx</span><span class="sxs-lookup"><span data-stu-id="89334-346">Nginx configuration</span></span>

<span data-ttu-id="89334-347">Per inoltrare le intestazioni `X-Forwarded-For` e `X-Forwarded-Proto`, vedere <xref:host-and-deploy/linux-nginx#configure-nginx>.</span><span class="sxs-lookup"><span data-stu-id="89334-347">To forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers, see <xref:host-and-deploy/linux-nginx#configure-nginx>.</span></span> <span data-ttu-id="89334-348">Per altre informazioni, vedere [NGINX: Using the Forwarded header](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/) (NGINX: Uso dell'intestazione Forwarded).</span><span class="sxs-lookup"><span data-stu-id="89334-348">For more information, see [NGINX: Using the Forwarded header](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/).</span></span>

## <a name="apache-configuration"></a><span data-ttu-id="89334-349">Configurazione Apache</span><span class="sxs-lookup"><span data-stu-id="89334-349">Apache configuration</span></span>

<span data-ttu-id="89334-350">`X-Forwarded-For` viene aggiunta automaticamente. Vedere [Apache Module mod_proxy: Reverse Proxy Request Headers](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers) (Modulo Apache mod_proxy: Intestazioni delle richieste del proxy inverso).</span><span class="sxs-lookup"><span data-stu-id="89334-350">`X-Forwarded-For` is added automatically (see [Apache Module mod_proxy: Reverse Proxy Request Headers](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)).</span></span> <span data-ttu-id="89334-351">Per informazioni su come inoltrare l'intestazione `X-Forwarded-Proto`, vedere <xref:host-and-deploy/linux-apache#configure-apache>.</span><span class="sxs-lookup"><span data-stu-id="89334-351">For information on how to forward the `X-Forwarded-Proto` header, see <xref:host-and-deploy/linux-apache#configure-apache>.</span></span>

## <a name="forwarded-headers-middleware-options"></a><span data-ttu-id="89334-352">Opzioni del middleware delle intestazioni inoltrate</span><span class="sxs-lookup"><span data-stu-id="89334-352">Forwarded Headers Middleware options</span></span>

<span data-ttu-id="89334-353"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> controllano il comportamento del middleware delle intestazioni inoltrate.</span><span class="sxs-lookup"><span data-stu-id="89334-353"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> control the behavior of the Forwarded Headers Middleware.</span></span> <span data-ttu-id="89334-354">L'esempio seguente modifica i valori predefiniti:</span><span class="sxs-lookup"><span data-stu-id="89334-354">The following example changes the default values:</span></span>

* <span data-ttu-id="89334-355">Limitare il numero di voci nelle intestazioni inoltrate a `2`.</span><span class="sxs-lookup"><span data-stu-id="89334-355">Limit the number of entries in the forwarded headers to `2`.</span></span>
* <span data-ttu-id="89334-356">Aggiungere un indirizzo proxy noto di `127.0.10.1`.</span><span class="sxs-lookup"><span data-stu-id="89334-356">Add a known proxy address of `127.0.10.1`.</span></span>
* <span data-ttu-id="89334-357">Modificare il nome dell'intestazione inoltrata dal nome predefinito `X-Forwarded-For` a `X-Forwarded-For-My-Custom-Header-Name`.</span><span class="sxs-lookup"><span data-stu-id="89334-357">Change the forwarded header name from the default `X-Forwarded-For` to `X-Forwarded-For-My-Custom-Header-Name`.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardLimit = 2;
    options.KnownProxies.Add(IPAddress.Parse("127.0.10.1"));
    options.ForwardedForHeaderName = "X-Forwarded-For-My-Custom-Header-Name";
});
```

| <span data-ttu-id="89334-358">Opzione</span><span class="sxs-lookup"><span data-stu-id="89334-358">Option</span></span> | <span data-ttu-id="89334-359">Descrizione</span><span class="sxs-lookup"><span data-stu-id="89334-359">Description</span></span> |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> | <span data-ttu-id="89334-360">Limita gli host mediante l'intestazione `X-Forwarded-Host` ai valori specificati.</span><span class="sxs-lookup"><span data-stu-id="89334-360">Restricts hosts by the `X-Forwarded-Host` header to the values provided.</span></span><ul><li><span data-ttu-id="89334-361">I valori vengono confrontati tramite ordinal-ignore-case.</span><span class="sxs-lookup"><span data-stu-id="89334-361">Values are compared using ordinal-ignore-case.</span></span></li><li><span data-ttu-id="89334-362">I numeri di porta devono essere esclusi.</span><span class="sxs-lookup"><span data-stu-id="89334-362">Port numbers must be excluded.</span></span></li><li><span data-ttu-id="89334-363">Se l'elenco è vuoto, sono consentiti tutti gli host.</span><span class="sxs-lookup"><span data-stu-id="89334-363">If the list is empty, all hosts are allowed.</span></span></li><li><span data-ttu-id="89334-364">Un carattere jolly di primo livello `*` consente tutti gli host non vuoti.</span><span class="sxs-lookup"><span data-stu-id="89334-364">A top-level wildcard `*` allows all non-empty hosts.</span></span></li><li><span data-ttu-id="89334-365">I caratteri jolly per i sottodomini sono consentiti, ma non corrispondono al dominio radice.</span><span class="sxs-lookup"><span data-stu-id="89334-365">Subdomain wildcards are permitted but don't match the root domain.</span></span> <span data-ttu-id="89334-366">Ad esempio, `*.contoso.com` corrisponde al sottodominio `foo.contoso.com`, ma non al dominio radice `contoso.com`.</span><span class="sxs-lookup"><span data-stu-id="89334-366">For example, `*.contoso.com` matches the subdomain `foo.contoso.com` but not the root domain `contoso.com`.</span></span></li><li><span data-ttu-id="89334-367">I nomi host Unicode sono consentiti ma vengono convertiti in [Punycode](https://tools.ietf.org/html/rfc3492) per la corrispondenza.</span><span class="sxs-lookup"><span data-stu-id="89334-367">Unicode host names are allowed but are converted to [Punycode](https://tools.ietf.org/html/rfc3492) for matching.</span></span></li><li><span data-ttu-id="89334-368">Gli [indirizzi IPv6](https://tools.ietf.org/html/rfc4291) devono includere le parentesi quadre di delimitazione ed essere in [formato convenzionale](https://tools.ietf.org/html/rfc4291#section-2.2), ad esempio `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`.</span><span class="sxs-lookup"><span data-stu-id="89334-368">[IPv6 addresses](https://tools.ietf.org/html/rfc4291) must include bounding brackets and be in [conventional form](https://tools.ietf.org/html/rfc4291#section-2.2) (for example, `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`).</span></span> <span data-ttu-id="89334-369">Per gli indirizzi IPv6 non viene applicata la distinzione tra maiuscole e minuscole per la verifica dell'uguaglianza logica tra i diversi formati e non viene eseguita alcuna canonizzazione.</span><span class="sxs-lookup"><span data-stu-id="89334-369">IPv6 addresses aren't special-cased to check for logical equality between different formats, and no canonicalization is performed.</span></span></li><li><span data-ttu-id="89334-370">La mancata limitazione degli host consentiti potrebbe permettere a un utente malintenzionato di eseguire lo spoofing dei collegamenti generati dal servizio.</span><span class="sxs-lookup"><span data-stu-id="89334-370">Failure to restrict the allowed hosts may allow an attacker to spoof links generated by the service.</span></span></li></ul><span data-ttu-id="89334-371">Il valore predefinito è un insieme `IList<string>` vuoto.</span><span class="sxs-lookup"><span data-stu-id="89334-371">The default value is an empty `IList<string>`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> | <span data-ttu-id="89334-372">Usare l'intestazione specificata da questa proprietà anziché quella specificata da [ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName).</span><span class="sxs-lookup"><span data-stu-id="89334-372">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName).</span></span> <span data-ttu-id="89334-373">Questa opzione viene usata quando il proxy o il server d'inoltro non usa l'intestazione `X-Forwarded-For` ma usa un'altra intestazione per inoltrare le informazioni.</span><span class="sxs-lookup"><span data-stu-id="89334-373">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-For` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="89334-374">Il valore predefinito è `X-Forwarded-For`.</span><span class="sxs-lookup"><span data-stu-id="89334-374">The default is `X-Forwarded-For`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> | <span data-ttu-id="89334-375">Identifica i server d'inoltro che devono essere elaborati.</span><span class="sxs-lookup"><span data-stu-id="89334-375">Identifies which forwarders should be processed.</span></span> <span data-ttu-id="89334-376">Vedere [ForwardedHeaders Enum](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) (Enumerazione ForwardedHeaders) per l'elenco dei campi applicabili.</span><span class="sxs-lookup"><span data-stu-id="89334-376">See the [ForwardedHeaders Enum](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) for the list of fields that apply.</span></span> <span data-ttu-id="89334-377">I valori tipici assegnati a questa proprietà sono `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`.</span><span class="sxs-lookup"><span data-stu-id="89334-377">Typical values assigned to this property are `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`.</span></span><br><br><span data-ttu-id="89334-378">Il valore predefinito è [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="89334-378">The default value is [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHostHeaderName> | <span data-ttu-id="89334-379">Usare l'intestazione specificata da questa proprietà anziché quella specificata da [ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName).</span><span class="sxs-lookup"><span data-stu-id="89334-379">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName).</span></span> <span data-ttu-id="89334-380">Questa opzione viene usata quando il proxy o il server d'inoltro non usa l'intestazione `X-Forwarded-Host` ma usa un'altra intestazione per inoltrare le informazioni.</span><span class="sxs-lookup"><span data-stu-id="89334-380">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Host` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="89334-381">Il valore predefinito è `X-Forwarded-Host`.</span><span class="sxs-lookup"><span data-stu-id="89334-381">The default is `X-Forwarded-Host`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> | <span data-ttu-id="89334-382">Usare l'intestazione specificata da questa proprietà anziché quella specificata da [ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName).</span><span class="sxs-lookup"><span data-stu-id="89334-382">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName).</span></span> <span data-ttu-id="89334-383">Questa opzione viene usata quando il proxy o il server d'inoltro non usa l'intestazione `X-Forwarded-Proto` ma usa un'altra intestazione per inoltrare le informazioni.</span><span class="sxs-lookup"><span data-stu-id="89334-383">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Proto` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="89334-384">Il valore predefinito è `X-Forwarded-Proto`.</span><span class="sxs-lookup"><span data-stu-id="89334-384">The default is `X-Forwarded-Proto`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardLimit> | <span data-ttu-id="89334-385">Limita il numero di voci nelle intestazioni elaborate.</span><span class="sxs-lookup"><span data-stu-id="89334-385">Limits the number of entries in the headers that are processed.</span></span> <span data-ttu-id="89334-386">Impostare su `null` per disabilitare il limite, ma solo se è configurato `KnownProxies` o `KnownNetworks`.</span><span class="sxs-lookup"><span data-stu-id="89334-386">Set to `null` to disable the limit, but this should only be done if `KnownProxies` or `KnownNetworks` are configured.</span></span> <span data-ttu-id="89334-387">L'impostazione di un valore non `null` è una precauzione (ma non una garanzia) per proteggersi da proxy non configurati correttamente e richieste dannose provenienti da canali laterali in rete.</span><span class="sxs-lookup"><span data-stu-id="89334-387">Setting a non-`null` value is a precaution (but not a guarantee) to guard against misconfigured proxies and malicious requests arriving from side-channels on the network.</span></span><br><br><span data-ttu-id="89334-388">Il middleware delle intestazioni inoltrate elabora le intestazioni in ordine inverso da destra a sinistra.</span><span class="sxs-lookup"><span data-stu-id="89334-388">Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="89334-389">Se viene usato il valore predefinito (`1`), viene elaborato solo il valore più a destra delle intestazioni, a meno che non venga aumentato il valore di `ForwardLimit`.</span><span class="sxs-lookup"><span data-stu-id="89334-389">If the default value (`1`) is used, only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span><br><br><span data-ttu-id="89334-390">Il valore predefinito è `1`.</span><span class="sxs-lookup"><span data-stu-id="89334-390">The default is `1`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks> | <span data-ttu-id="89334-391">Intervalli di indirizzi delle reti note da cui accettare le intestazioni inoltrate.</span><span class="sxs-lookup"><span data-stu-id="89334-391">Address ranges of known networks to accept forwarded headers from.</span></span> <span data-ttu-id="89334-392">Specificare gli intervalli IP usando la notazione CIDR (Classless Interdomain Routing).</span><span class="sxs-lookup"><span data-stu-id="89334-392">Provide IP ranges using Classless Interdomain Routing (CIDR) notation.</span></span><br><br><span data-ttu-id="89334-393">Se il server usa socket dual mode, gli indirizzi IPv4 vengono forniti in un formato IPv6 (ad esempio, `10.0.0.1` in IPv4 rappresentato in IPv6 come `::ffff:10.0.0.1`).</span><span class="sxs-lookup"><span data-stu-id="89334-393">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="89334-394">Vedere [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="89334-394">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="89334-395">Determinare se questo formato è richiesto esaminando [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span><span class="sxs-lookup"><span data-stu-id="89334-395">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="89334-396">Per altre informazioni, vedere la sezione [Configurazione per un indirizzo IPv4 rappresentato come indirizzo IPv6](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address).</span><span class="sxs-lookup"><span data-stu-id="89334-396">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="89334-397">Il valore predefinito è un oggetto `IList`\<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> contenente una singola voce per `IPAddress.Loopback`.</span><span class="sxs-lookup"><span data-stu-id="89334-397">The default is an `IList`\<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> containing a single entry for `IPAddress.Loopback`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies> | <span data-ttu-id="89334-398">Indirizzi dei proxy noti da cui accettare le intestazioni inoltrate.</span><span class="sxs-lookup"><span data-stu-id="89334-398">Addresses of known proxies to accept forwarded headers from.</span></span> <span data-ttu-id="89334-399">Usare `KnownProxies` per specificare le corrispondenze esatte degli indirizzi IP.</span><span class="sxs-lookup"><span data-stu-id="89334-399">Use `KnownProxies` to specify exact IP address matches.</span></span><br><br><span data-ttu-id="89334-400">Se il server usa socket dual mode, gli indirizzi IPv4 vengono forniti in un formato IPv6 (ad esempio, `10.0.0.1` in IPv4 rappresentato in IPv6 come `::ffff:10.0.0.1`).</span><span class="sxs-lookup"><span data-stu-id="89334-400">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="89334-401">Vedere [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="89334-401">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="89334-402">Determinare se questo formato è richiesto esaminando [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span><span class="sxs-lookup"><span data-stu-id="89334-402">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="89334-403">Per altre informazioni, vedere la sezione [Configurazione per un indirizzo IPv4 rappresentato come indirizzo IPv6](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address).</span><span class="sxs-lookup"><span data-stu-id="89334-403">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="89334-404">Il valore predefinito è un oggetto `IList`\<<xref:System.Net.IPAddress>> contenente una singola voce per `IPAddress.IPv6Loopback`.</span><span class="sxs-lookup"><span data-stu-id="89334-404">The default is an `IList`\<<xref:System.Net.IPAddress>> containing a single entry for `IPAddress.IPv6Loopback`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalForHeaderName> | <span data-ttu-id="89334-405">Usare l'intestazione specificata da questa proprietà anziché quella specificata da [ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).</span><span class="sxs-lookup"><span data-stu-id="89334-405">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).</span></span><br><br><span data-ttu-id="89334-406">Il valore predefinito è `X-Original-For`.</span><span class="sxs-lookup"><span data-stu-id="89334-406">The default is `X-Original-For`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalHostHeaderName> | <span data-ttu-id="89334-407">Usare l'intestazione specificata da questa proprietà anziché quella specificata da [ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).</span><span class="sxs-lookup"><span data-stu-id="89334-407">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).</span></span><br><br><span data-ttu-id="89334-408">Il valore predefinito è `X-Original-Host`.</span><span class="sxs-lookup"><span data-stu-id="89334-408">The default is `X-Original-Host`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalProtoHeaderName> | <span data-ttu-id="89334-409">Usare l'intestazione specificata da questa proprietà anziché quella specificata da [ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).</span><span class="sxs-lookup"><span data-stu-id="89334-409">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).</span></span><br><br><span data-ttu-id="89334-410">Il valore predefinito è `X-Original-Proto`.</span><span class="sxs-lookup"><span data-stu-id="89334-410">The default is `X-Original-Proto`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.RequireHeaderSymmetry> | <span data-ttu-id="89334-411">Richiedere il numero di valori di intestazione da sincronizzare tra le intestazioni [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) in fase di elaborazione.</span><span class="sxs-lookup"><span data-stu-id="89334-411">Require the number of header values to be in sync between the [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) being processed.</span></span><br><br><span data-ttu-id="89334-412">Il valore predefinito in ASP.NET Core 1.x è `true`.</span><span class="sxs-lookup"><span data-stu-id="89334-412">The default in ASP.NET Core 1.x is `true`.</span></span> <span data-ttu-id="89334-413">Il valore predefinito in ASP.NET Core 2.0 o versione successiva è `false`.</span><span class="sxs-lookup"><span data-stu-id="89334-413">The default in ASP.NET Core 2.0 or later is `false`.</span></span> |

## <a name="scenarios-and-use-cases"></a><span data-ttu-id="89334-414">Scenari e casi d'uso</span><span class="sxs-lookup"><span data-stu-id="89334-414">Scenarios and use cases</span></span>

### <a name="when-it-isnt-possible-to-add-forwarded-headers-and-all-requests-are-secure"></a><span data-ttu-id="89334-415">Quando non è possibile aggiungere le intestazioni inoltrate e tutte le richieste sono sicure</span><span class="sxs-lookup"><span data-stu-id="89334-415">When it isn't possible to add forwarded headers and all requests are secure</span></span>

<span data-ttu-id="89334-416">In alcuni casi, potrebbe non essere possibile aggiungere le intestazioni inoltrate alle richieste trasmesse tramite proxy all'app.</span><span class="sxs-lookup"><span data-stu-id="89334-416">In some cases, it might not be possible to add forwarded headers to the requests proxied to the app.</span></span> <span data-ttu-id="89334-417">Se il proxy impone che tutte le richieste esterne pubbliche siano HTTPS, lo schema può essere impostato manualmente in `Startup.Configure` prima di usare qualsiasi tipo di middleware:</span><span class="sxs-lookup"><span data-stu-id="89334-417">If the proxy is enforcing that all public external requests are HTTPS, the scheme can be manually set in `Startup.Configure` before using any type of middleware:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.Scheme = "https";
    return next();
});
```

<span data-ttu-id="89334-418">Questo codice può essere disabilitato tramite una variabile di ambiente o un'altra impostazione di configurazione in un ambiente di sviluppo o di gestione temporanea.</span><span class="sxs-lookup"><span data-stu-id="89334-418">This code can be disabled with an environment variable or other configuration setting in a development or staging environment.</span></span>

### <a name="deal-with-path-base-and-proxies-that-change-the-request-path"></a><span data-ttu-id="89334-419">Gestire la base del percorso e i proxy che modificano il percorso della richiesta</span><span class="sxs-lookup"><span data-stu-id="89334-419">Deal with path base and proxies that change the request path</span></span>

<span data-ttu-id="89334-420">Alcuni proxy passano il percorso senza modifiche, ma con un percorso di base dell'app che deve essere rimosso per consentire il corretto funzionamento del routing.</span><span class="sxs-lookup"><span data-stu-id="89334-420">Some proxies pass the path intact but with an app base path that should be removed so that routing works properly.</span></span> <span data-ttu-id="89334-421">Il middleware [UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) suddivide il percorso in [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) e il percorso di base dell'app in [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).</span><span class="sxs-lookup"><span data-stu-id="89334-421">[UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) middleware splits the path into [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) and the app base path into [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).</span></span>

<span data-ttu-id="89334-422">Se `/foo` è il percorso di base dell'app per un percorso proxy passato come `/foo/api/1`, il middleware imposta `Request.PathBase` su `/foo` e `Request.Path` su `/api/1` con il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="89334-422">If `/foo` is the app base path for a proxy path passed as `/foo/api/1`, the middleware sets `Request.PathBase` to `/foo` and `Request.Path` to `/api/1` with the following command:</span></span>

```csharp
app.UsePathBase("/foo");
```

<span data-ttu-id="89334-423">Il percorso originale e la base del percorso vengono riapplicati quando il middleware viene chiamato nuovamente in direzione inversa.</span><span class="sxs-lookup"><span data-stu-id="89334-423">The original path and path base are reapplied when the middleware is called again in reverse.</span></span> <span data-ttu-id="89334-424">Per altre informazioni dell'elaborazione degli ordini del middleware, vedere <xref:fundamentals/middleware/index>.</span><span class="sxs-lookup"><span data-stu-id="89334-424">For more information on middleware order processing, see <xref:fundamentals/middleware/index>.</span></span>

<span data-ttu-id="89334-425">Se il proxy taglia il percorso (ad esempio, inoltrando `/foo/api/1` a `/api/1`), correggere i reindirizzamenti e i collegamenti impostando la proprietà [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) della richiesta:</span><span class="sxs-lookup"><span data-stu-id="89334-425">If the proxy trims the path (for example, forwarding `/foo/api/1` to `/api/1`), fix redirects and links by setting the request's [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) property:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.PathBase = new PathString("/foo");
    return next();
});
```

<span data-ttu-id="89334-426">Se il proxy aggiunge i dati del percorso, eliminare parte del percorso per correggere i reindirizzamenti e i collegamenti usando <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> ed eseguendo l'assegnazione alla proprietà <xref:Microsoft.AspNetCore.Http.HttpRequest.Path>:</span><span class="sxs-lookup"><span data-stu-id="89334-426">If the proxy is adding path data, discard part of the path to fix redirects and links by using <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> and assigning to the <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> property:</span></span>

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

### <a name="configuration-for-a-proxy-that-uses-different-header-names"></a><span data-ttu-id="89334-427">Configurazione per un proxy che usa nomi di intestazione diversi</span><span class="sxs-lookup"><span data-stu-id="89334-427">Configuration for a proxy that uses different header names</span></span>

<span data-ttu-id="89334-428">Se il proxy non usa intestazioni denominate `X-Forwarded-For` e `X-Forwarded-Proto` per inoltrare l'indirizzo proxy o la porta e le informazioni dello scherma originali, impostare le opzioni <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> e <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> in modo che corrispondano ai nomi di intestazione usati dal proxy:</span><span class="sxs-lookup"><span data-stu-id="89334-428">If the proxy doesn't use headers named `X-Forwarded-For` and `X-Forwarded-Proto` to forward the proxy address/port and originating scheme information, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the proxy:</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedForHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-For_Header";
    options.ForwardedProtoHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-Proto_Header";
});
```

### <a name="configuration-for-an-ipv4-address-represented-as-an-ipv6-address"></a><span data-ttu-id="89334-429">Configurazione per un indirizzo IPv4 rappresentato come indirizzo IPv6</span><span class="sxs-lookup"><span data-stu-id="89334-429">Configuration for an IPv4 address represented as an IPv6 address</span></span>

<span data-ttu-id="89334-430">Se il server usa socket dual mode, gli indirizzi IPv4 vengono forniti in un formato IPv6 (ad esempio, `10.0.0.1` in IPv4 rappresentato in IPv6 come `::ffff:10.0.0.1` o `::ffff:a00:1`).</span><span class="sxs-lookup"><span data-stu-id="89334-430">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1` or `::ffff:a00:1`).</span></span> <span data-ttu-id="89334-431">Vedere [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="89334-431">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="89334-432">Determinare se questo formato è richiesto esaminando [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span><span class="sxs-lookup"><span data-stu-id="89334-432">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span>

<span data-ttu-id="89334-433">Nell'esempio seguente un indirizzo di rete che fornisce intestazioni inoltrate viene aggiunto all'elenco `KnownNetworks` in formato IPv6.</span><span class="sxs-lookup"><span data-stu-id="89334-433">In the following example, a network address that supplies forwarded headers is added to the `KnownNetworks` list in IPv6 format.</span></span>

<span data-ttu-id="89334-434">Indirizzo IPv4: `10.11.12.1/8`</span><span class="sxs-lookup"><span data-stu-id="89334-434">IPv4 address: `10.11.12.1/8`</span></span>

<span data-ttu-id="89334-435">Indirizzo IPv6 convertito: `::ffff:10.11.12.1`</span><span class="sxs-lookup"><span data-stu-id="89334-435">Converted IPv6 address: `::ffff:10.11.12.1`</span></span>  
<span data-ttu-id="89334-436">Lunghezza del prefisso convertito: 104</span><span class="sxs-lookup"><span data-stu-id="89334-436">Converted prefix length: 104</span></span>

<span data-ttu-id="89334-437">È anche possibile specificare l'indirizzo in formato esadecimale (`10.11.12.1` rappresentato in IPv6 come `::ffff:0a0b:0c01`).</span><span class="sxs-lookup"><span data-stu-id="89334-437">You can also supply the address in hexadecimal format (`10.11.12.1` represented in IPv6 as `::ffff:0a0b:0c01`).</span></span> <span data-ttu-id="89334-438">Durante la conversione di un indirizzo IPv4 in IPv6, aggiungere 96 alla lunghezza del prefisso CIDR (`8` nell'esempio) per tenere conto del prefisso IPv6 aggiuntivo `::ffff:` (8 + 96 = 104).</span><span class="sxs-lookup"><span data-stu-id="89334-438">When converting an IPv4 address to IPv6, add 96 to the CIDR Prefix Length (`8` in the example) to account for the additional `::ffff:` IPv6 prefix (8 + 96 = 104).</span></span> 

```csharp
// To access IPNetwork and IPAddress, add the following namespaces:
// using using System.Net;
// using Microsoft.AspNetCore.HttpOverrides;
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedHeaders =
        ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto;
    options.KnownNetworks.Add(new IPNetwork(
        IPAddress.Parse("::ffff:10.11.12.1"), 104));
});
```

## <a name="forward-the-scheme-for-linux-and-non-iis-reverse-proxies"></a><span data-ttu-id="89334-439">Inoltrare lo schema per Linux e proxy inversi non IIS</span><span class="sxs-lookup"><span data-stu-id="89334-439">Forward the scheme for Linux and non-IIS reverse proxies</span></span>

<span data-ttu-id="89334-440">Con le app che chiamano <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> e <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> il sito si ritrova in un ciclo infinito se distribuito in un servizio app Linux di Azure, in una macchina virtuale Linux di Azure o dietro eventuali altri proxy inversi diversi da IIS.</span><span class="sxs-lookup"><span data-stu-id="89334-440">Apps that call <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> and <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> put a site into an infinite loop if deployed to an Azure Linux App Service, Azure Linux virtual machine (VM), or behind any other reverse proxy besides IIS.</span></span> <span data-ttu-id="89334-441">TLS viene terminato dal proxy inverso e Kestrel non viene a conoscenza dello schema di richiesta corretto.</span><span class="sxs-lookup"><span data-stu-id="89334-441">TLS is terminated by the reverse proxy, and Kestrel isn't made aware of the correct request scheme.</span></span> <span data-ttu-id="89334-442">Si verificano errori anche per OAuth e OIDC in questa configurazione perché generano reindirizzamenti non corretti.</span><span class="sxs-lookup"><span data-stu-id="89334-442">OAuth and OIDC also fail in this configuration because they generate incorrect redirects.</span></span> <span data-ttu-id="89334-443"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> aggiunge e configura il middleware delle intestazioni inoltrate in caso di esecuzione dietro IIS, ma non esiste alcuna configurazione automatica corrispondente per Linux (integrazione di Apache o Nginx).</span><span class="sxs-lookup"><span data-stu-id="89334-443"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> adds and configures Forwarded Headers Middleware when running behind IIS, but there's no matching automatic configuration for Linux (Apache or Nginx integration).</span></span>

<span data-ttu-id="89334-444">Per inoltrare lo schema dal proxy in scenari non IIS, aggiungere e configurare il middleware delle intestazioni inoltrate.</span><span class="sxs-lookup"><span data-stu-id="89334-444">To forward the scheme from the proxy in non-IIS scenarios, add and configure Forwarded Headers Middleware.</span></span> <span data-ttu-id="89334-445">In `Startup.ConfigureServices` usare il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="89334-445">In `Startup.ConfigureServices`, use the following code:</span></span>

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

## <a name="troubleshoot"></a><span data-ttu-id="89334-446">Risolvere problemi</span><span class="sxs-lookup"><span data-stu-id="89334-446">Troubleshoot</span></span>

<span data-ttu-id="89334-447">Quando le intestazioni non vengono inoltrate come previsto, abilitare la [registrazione](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="89334-447">When headers aren't forwarded as expected, enable [logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="89334-448">Se i log non forniscono informazioni sufficienti per risolvere il problema, enumerare le intestazioni delle richieste ricevute dal server.</span><span class="sxs-lookup"><span data-stu-id="89334-448">If the logs don't provide sufficient information to troubleshoot the problem, enumerate the request headers received by the server.</span></span> <span data-ttu-id="89334-449">Usare il middleware inline per scrivere le intestazioni di richiesta in una risposta dell'app o per registrare le intestazioni.</span><span class="sxs-lookup"><span data-stu-id="89334-449">Use inline middleware to write request headers to an app response or log the headers.</span></span> 

<span data-ttu-id="89334-450">Per scrivere le intestazioni nella risposta dell'app, aggiungere il middleware inline di terminale seguente immediatamente dopo la chiamata a <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="89334-450">To write the headers to the app's response, place the following terminal inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`:</span></span>

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

<span data-ttu-id="89334-451">È possibile scrivere nei log invece che nel corpo della risposta.</span><span class="sxs-lookup"><span data-stu-id="89334-451">You can write to logs instead of the response body.</span></span> <span data-ttu-id="89334-452">La scrittura nei log permette il normale funzionamento del sito durante il debug.</span><span class="sxs-lookup"><span data-stu-id="89334-452">Writing to logs allows the site to function normally while debugging.</span></span>

<span data-ttu-id="89334-453">Per scrivere nei log invece che nel corpo della risposta:</span><span class="sxs-lookup"><span data-stu-id="89334-453">To write logs rather than to the response body:</span></span>

* <span data-ttu-id="89334-454">Inserire `ILogger<Startup>` nella classe `Startup` come descritto in [Creare log in Startup](xref:fundamentals/logging/index#create-logs-in-startup).</span><span class="sxs-lookup"><span data-stu-id="89334-454">Inject `ILogger<Startup>` into the `Startup` class as described in [Create logs in Startup](xref:fundamentals/logging/index#create-logs-in-startup).</span></span>
* <span data-ttu-id="89334-455">Aggiungere il middleware inline seguente immediatamente dopo la chiamata a <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="89334-455">Place the following inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`.</span></span>

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

<span data-ttu-id="89334-456">Durante l'elaborazione, i valori di `X-Forwarded-{For|Proto|Host}` vengono spostati in `X-Original-{For|Proto|Host}`.</span><span class="sxs-lookup"><span data-stu-id="89334-456">When processed, `X-Forwarded-{For|Proto|Host}` values are moved to `X-Original-{For|Proto|Host}`.</span></span> <span data-ttu-id="89334-457">Se sono presenti più valori in una determinata intestazione, il middleware delle intestazioni inoltrate elabora le intestazioni in ordine inverso da destra a sinistra.</span><span class="sxs-lookup"><span data-stu-id="89334-457">If there are multiple values in a given header, Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="89334-458">Il valore predefinito di `ForwardLimit` è `1` (uno) e, pertanto, viene elaborato solo il valore più a destra delle intestazioni, a meno che non venga aumentato il valore di `ForwardLimit`.</span><span class="sxs-lookup"><span data-stu-id="89334-458">The default `ForwardLimit` is `1` (one), so only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span>

<span data-ttu-id="89334-459">L'indirizzo IP remoto originale della richiesta deve corrispondere a una voce negli elenchi `KnownProxies` o `KnownNetworks` prima dell'elaborazione delle intestazioni inoltrate.</span><span class="sxs-lookup"><span data-stu-id="89334-459">The request's original remote IP must match an entry in the `KnownProxies` or `KnownNetworks` lists before forwarded headers are processed.</span></span> <span data-ttu-id="89334-460">Questo riduce lo spoofing delle intestazioni, non accettando server di inoltro da proxy non attendibili.</span><span class="sxs-lookup"><span data-stu-id="89334-460">This limits header spoofing by not accepting forwarders from untrusted proxies.</span></span> <span data-ttu-id="89334-461">Quando viene rilevato un proxy sconosciuto, la registrazione indica l'indirizzo del proxy:</span><span class="sxs-lookup"><span data-stu-id="89334-461">When an unknown proxy is detected, logging indicates the address of the proxy:</span></span>

```console
September 20th 2018, 15:49:44.168 Unknown proxy: 10.0.0.100:54321
```

<span data-ttu-id="89334-462">Nell'esempio precedente, 10.0.0.100 è un server proxy.</span><span class="sxs-lookup"><span data-stu-id="89334-462">In the preceding example, 10.0.0.100 is a proxy server.</span></span> <span data-ttu-id="89334-463">Se il server è un proxy attendibile, aggiungere l'indirizzo IP del server a `KnownProxies` (o aggiungere una rete attendibile a `KnownNetworks`) in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="89334-463">If the server is a trusted proxy, add the server's IP address to `KnownProxies` (or add a trusted network to `KnownNetworks`) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="89334-464">Per altre informazioni, vedere la sezione [Opzioni del middleware delle intestazioni inoltrate](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="89334-464">For more information, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) section.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

> [!IMPORTANT]
> <span data-ttu-id="89334-465">Consentire solo a reti e proxy attendibili di inoltrare le intestazioni.</span><span class="sxs-lookup"><span data-stu-id="89334-465">Only allow trusted proxies and networks to forward headers.</span></span> <span data-ttu-id="89334-466">In caso contrario, possono verificarsi attacchi di [spoofing degli indirizzi IP](https://www.iplocation.net/ip-spoofing).</span><span class="sxs-lookup"><span data-stu-id="89334-466">Otherwise, [IP spoofing](https://www.iplocation.net/ip-spoofing) attacks are possible.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="89334-467">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="89334-467">Additional resources</span></span>

* <xref:host-and-deploy/web-farm>
* [<span data-ttu-id="89334-468">Avviso di sicurezza Microsoft CVE-2018-0787: vulnerabilità di elevazione dei privilegi di ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="89334-468">Microsoft Security Advisory CVE-2018-0787: ASP.NET Core Elevation Of Privilege Vulnerability</span></span>](https://github.com/aspnet/Announcements/issues/295)

::: moniker-end
