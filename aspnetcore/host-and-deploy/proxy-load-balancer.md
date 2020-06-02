---
<span data-ttu-id="390d5-101">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="390d5-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="390d5-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="390d5-102">'Blazor'</span></span>
- <span data-ttu-id="390d5-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="390d5-103">'Identity'</span></span>
- <span data-ttu-id="390d5-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="390d5-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="390d5-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="390d5-105">'Razor'</span></span>
- <span data-ttu-id="390d5-106">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="390d5-106">'SignalR' uid:</span></span> 

---
# <a name="configure-aspnet-core-to-work-with-proxy-servers-and-load-balancers"></a><span data-ttu-id="390d5-107">Configurare ASP.NET Core per l'uso di server proxy e servizi di bilanciamento del carico</span><span class="sxs-lookup"><span data-stu-id="390d5-107">Configure ASP.NET Core to work with proxy servers and load balancers</span></span>

<span data-ttu-id="390d5-108">Di [Chris Ross](https://github.com/Tratcher)</span><span class="sxs-lookup"><span data-stu-id="390d5-108">By [Chris Ross](https://github.com/Tratcher)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="390d5-109">Nella configurazione consigliata per ASP.NET Core, l'app è ospitata mediante IIS e il modulo ASP.NET Core, Nginx o Apache.</span><span class="sxs-lookup"><span data-stu-id="390d5-109">In the recommended configuration for ASP.NET Core, the app is hosted using IIS/ASP.NET Core Module, Nginx, or Apache.</span></span> <span data-ttu-id="390d5-110">Server proxy, servizi di bilanciamento del carico e altre appliance di rete spesso nascondono informazioni sulla richiesta prima che questa raggiunga l'app:</span><span class="sxs-lookup"><span data-stu-id="390d5-110">Proxy servers, load balancers, and other network appliances often obscure information about the request before it reaches the app:</span></span>

* <span data-ttu-id="390d5-111">Quando le richieste HTTPS vengono trasmesse tramite proxy su HTTP, lo schema originale (HTTPS) viene perso e deve essere inoltrato in un'intestazione.</span><span class="sxs-lookup"><span data-stu-id="390d5-111">When HTTPS requests are proxied over HTTP, the original scheme (HTTPS) is lost and must be forwarded in a header.</span></span>
* <span data-ttu-id="390d5-112">Poiché un'app riceve una richiesta dal proxy e non dall'effettiva origine su Internet o nella rete aziendale, anche l'indirizzo IP di origine del client deve essere inoltrato in un'intestazione.</span><span class="sxs-lookup"><span data-stu-id="390d5-112">Because an app receives a request from the proxy and not its true source on the Internet or corporate network, the originating client IP address must also be forwarded in a header.</span></span>

<span data-ttu-id="390d5-113">Queste informazioni potrebbero essere importanti per l'elaborazione delle richieste, ad esempio per i reindirizzamenti, l'autenticazione, la generazione di collegamenti, la valutazione dei criteri e la georilevazione dei client.</span><span class="sxs-lookup"><span data-stu-id="390d5-113">This information may be important in request processing, for example in redirects, authentication, link generation, policy evaluation, and client geolocation.</span></span>

## <a name="forwarded-headers"></a><span data-ttu-id="390d5-114">Intestazioni inoltrate</span><span class="sxs-lookup"><span data-stu-id="390d5-114">Forwarded headers</span></span>

<span data-ttu-id="390d5-115">Per convenzione, i proxy inoltrano le informazioni nelle intestazioni HTTP.</span><span class="sxs-lookup"><span data-stu-id="390d5-115">By convention, proxies forward information in HTTP headers.</span></span>

| <span data-ttu-id="390d5-116">Header</span><span class="sxs-lookup"><span data-stu-id="390d5-116">Header</span></span> | <span data-ttu-id="390d5-117">Description</span><span class="sxs-lookup"><span data-stu-id="390d5-117">Description</span></span> |
| ---
<span data-ttu-id="390d5-118">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="390d5-118">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="390d5-119">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="390d5-119">'Blazor'</span></span>
- <span data-ttu-id="390d5-120">'Identity'</span><span class="sxs-lookup"><span data-stu-id="390d5-120">'Identity'</span></span>
- <span data-ttu-id="390d5-121">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="390d5-121">'Let's Encrypt'</span></span>
- <span data-ttu-id="390d5-122">'Razor'</span><span class="sxs-lookup"><span data-stu-id="390d5-122">'Razor'</span></span>
- <span data-ttu-id="390d5-123">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="390d5-123">'SignalR' uid:</span></span> 

<span data-ttu-id="390d5-124">--- | Titolo---: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="390d5-124">--- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="390d5-125">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="390d5-125">'Blazor'</span></span>
- <span data-ttu-id="390d5-126">'Identity'</span><span class="sxs-lookup"><span data-stu-id="390d5-126">'Identity'</span></span>
- <span data-ttu-id="390d5-127">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="390d5-127">'Let's Encrypt'</span></span>
- <span data-ttu-id="390d5-128">'Razor'</span><span class="sxs-lookup"><span data-stu-id="390d5-128">'Razor'</span></span>
- <span data-ttu-id="390d5-129">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="390d5-129">'SignalR' uid:</span></span> 

-
<span data-ttu-id="390d5-130">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="390d5-130">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="390d5-131">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="390d5-131">'Blazor'</span></span>
- <span data-ttu-id="390d5-132">'Identity'</span><span class="sxs-lookup"><span data-stu-id="390d5-132">'Identity'</span></span>
- <span data-ttu-id="390d5-133">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="390d5-133">'Let's Encrypt'</span></span>
- <span data-ttu-id="390d5-134">'Razor'</span><span class="sxs-lookup"><span data-stu-id="390d5-134">'Razor'</span></span>
- <span data-ttu-id="390d5-135">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="390d5-135">'SignalR' uid:</span></span> 

-
<span data-ttu-id="390d5-136">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="390d5-136">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="390d5-137">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="390d5-137">'Blazor'</span></span>
- <span data-ttu-id="390d5-138">'Identity'</span><span class="sxs-lookup"><span data-stu-id="390d5-138">'Identity'</span></span>
- <span data-ttu-id="390d5-139">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="390d5-139">'Let's Encrypt'</span></span>
- <span data-ttu-id="390d5-140">'Razor'</span><span class="sxs-lookup"><span data-stu-id="390d5-140">'Razor'</span></span>
- <span data-ttu-id="390d5-141">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="390d5-141">'SignalR' uid:</span></span> 

<span data-ttu-id="390d5-142">------ | | X-Inoltred-per | Include informazioni sul client che ha avviato la richiesta e i proxy successivi in una catena di proxy.</span><span class="sxs-lookup"><span data-stu-id="390d5-142">------ | | X-Forwarded-For | Holds information about the client that initiated the request and subsequent proxies in a chain of proxies.</span></span> <span data-ttu-id="390d5-143">Questo parametro può contenere indirizzi IP (e, facoltativamente, numeri di porta).</span><span class="sxs-lookup"><span data-stu-id="390d5-143">This parameter may contain IP addresses (and, optionally, port numbers).</span></span> <span data-ttu-id="390d5-144">In una catena di server proxy, il primo parametro indica il client in cui è stata eseguita inizialmente la richiesta.</span><span class="sxs-lookup"><span data-stu-id="390d5-144">In a chain of proxy servers, the first parameter indicates the client where the request was first made.</span></span> <span data-ttu-id="390d5-145">Seguono gli identificatori dei proxy successivi.</span><span class="sxs-lookup"><span data-stu-id="390d5-145">Subsequent proxy identifiers follow.</span></span> <span data-ttu-id="390d5-146">L'ultimo proxy della catena non è incluso nell'elenco dei parametri.</span><span class="sxs-lookup"><span data-stu-id="390d5-146">The last proxy in the chain isn't in the list of parameters.</span></span> <span data-ttu-id="390d5-147">L'indirizzo IP dell'ultimo proxy e, facoltativamente, un numero di porta sono disponibili come indirizzo IP remoto a livello di trasporto.</span><span class="sxs-lookup"><span data-stu-id="390d5-147">The last proxy's IP address, and optionally a port number, are available as the remote IP address at the transport layer.</span></span> <span data-ttu-id="390d5-148">| | X-Inoltred-proto | Valore dello schema di origine (HTTP/HTTPS).</span><span class="sxs-lookup"><span data-stu-id="390d5-148">| | X-Forwarded-Proto | The value of the originating scheme (HTTP/HTTPS).</span></span> <span data-ttu-id="390d5-149">Il valore può essere anche un elenco di schemi, se la richiesta ha attraversato più proxy.</span><span class="sxs-lookup"><span data-stu-id="390d5-149">The value may also be a list of schemes if the request has traversed multiple proxies.</span></span> <span data-ttu-id="390d5-150">| | X-Inoltred-host | Valore originale del campo dell'intestazione host.</span><span class="sxs-lookup"><span data-stu-id="390d5-150">| | X-Forwarded-Host | The original value of the Host header field.</span></span> <span data-ttu-id="390d5-151">I proxy in genere non modificano l'intestazione host.</span><span class="sxs-lookup"><span data-stu-id="390d5-151">Usually, proxies don't modify the Host header.</span></span> <span data-ttu-id="390d5-152">Vedere l'[avviso di sicurezza Microsoft CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) per informazioni su una vulnerabilità di elevazione dei privilegi che interessa i sistemi in cui il proxy non convalida o limita le intestazioni host a valori di riferimento noti.</span><span class="sxs-lookup"><span data-stu-id="390d5-152">See [Microsoft Security Advisory CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) for information on an elevation-of-privileges vulnerability that affects systems where the proxy doesn't validate or restrict Host headers to known good values.</span></span> |

<span data-ttu-id="390d5-153">Il middleware delle intestazioni inoltrate, dal pacchetto [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/), legge le intestazioni e compila i campi associati in <xref:Microsoft.AspNetCore.Http.HttpContext>.</span><span class="sxs-lookup"><span data-stu-id="390d5-153">The Forwarded Headers Middleware, from the [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) package, reads these headers and fills in the associated fields on <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span>

<span data-ttu-id="390d5-154">Il middleware aggiorna:</span><span class="sxs-lookup"><span data-stu-id="390d5-154">The middleware updates:</span></span>

* <span data-ttu-id="390d5-155">[HttpContext. Connection. RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress): set usando il `X-Forwarded-For` valore dell'intestazione.</span><span class="sxs-lookup"><span data-stu-id="390d5-155">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress): Set using the `X-Forwarded-For` header value.</span></span> <span data-ttu-id="390d5-156">Impostazioni aggiuntive influiscono sul modo in cui il middleware imposta `RemoteIpAddress`.</span><span class="sxs-lookup"><span data-stu-id="390d5-156">Additional settings influence how the middleware sets `RemoteIpAddress`.</span></span> <span data-ttu-id="390d5-157">Per informazioni dettagliate, vedere [Opzioni del middleware delle intestazioni inoltrate](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="390d5-157">For details, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>
* <span data-ttu-id="390d5-158">[HttpContext. Request. Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme): impostare usando il `X-Forwarded-Proto` valore dell'intestazione.</span><span class="sxs-lookup"><span data-stu-id="390d5-158">[HttpContext.Request.Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme): Set using the `X-Forwarded-Proto` header value.</span></span>
* <span data-ttu-id="390d5-159">[HttpContext. Request. host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host): impostare usando il `X-Forwarded-Host` valore dell'intestazione.</span><span class="sxs-lookup"><span data-stu-id="390d5-159">[HttpContext.Request.Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host): Set using the `X-Forwarded-Host` header value.</span></span>

<span data-ttu-id="390d5-160">È possibile configurare le [impostazioni predefinite](#forwarded-headers-middleware-options) del middleware delle intestazioni inoltrate.</span><span class="sxs-lookup"><span data-stu-id="390d5-160">Forwarded Headers Middleware [default settings](#forwarded-headers-middleware-options) can be configured.</span></span> <span data-ttu-id="390d5-161">Le impostazioni predefinite sono le seguenti:</span><span class="sxs-lookup"><span data-stu-id="390d5-161">The default settings are:</span></span>

* <span data-ttu-id="390d5-162">È presente *un solo proxy* tra l'app e l'origine delle richieste.</span><span class="sxs-lookup"><span data-stu-id="390d5-162">There is only *one proxy* between the app and the source of the requests.</span></span>
* <span data-ttu-id="390d5-163">Sono configurati indirizzi di loopback solo per reti e proxy noti.</span><span class="sxs-lookup"><span data-stu-id="390d5-163">Only loopback addresses are configured for known proxies and known networks.</span></span>
* <span data-ttu-id="390d5-164">Le intestazioni inoltrate sono denominate `X-Forwarded-For` e `X-Forwarded-Proto`.</span><span class="sxs-lookup"><span data-stu-id="390d5-164">The forwarded headers are named `X-Forwarded-For` and `X-Forwarded-Proto`.</span></span>

<span data-ttu-id="390d5-165">Non tutte le appliance di rete aggiungono le intestazioni `X-Forwarded-For` e `X-Forwarded-Proto` senza alcuna configurazione aggiuntiva.</span><span class="sxs-lookup"><span data-stu-id="390d5-165">Not all network appliances add the `X-Forwarded-For` and `X-Forwarded-Proto` headers without additional configuration.</span></span> <span data-ttu-id="390d5-166">Se le richieste trasmesse tramite proxy non contengono queste intestazioni quando raggiungono l'app, fare riferimento alle indicazioni del produttore dell'appliance.</span><span class="sxs-lookup"><span data-stu-id="390d5-166">Consult your appliance manufacturer's guidance if proxied requests don't contain these headers when they reach the app.</span></span> <span data-ttu-id="390d5-167">Se l'appliance usa nomi di intestazione diversi da `X-Forwarded-For` e `X-Forwarded-Proto`, impostare le opzioni <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> e <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> in modo che corrispondano ai nomi di intestazione usati dall'appliance.</span><span class="sxs-lookup"><span data-stu-id="390d5-167">If the appliance uses different header names than `X-Forwarded-For` and `X-Forwarded-Proto`, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the appliance.</span></span> <span data-ttu-id="390d5-168">Per altre informazioni, vedere [Opzioni del middleware delle intestazioni inoltrate](#forwarded-headers-middleware-options) e [Configurazione per un proxy che usa nomi di intestazione diversi](#configuration-for-a-proxy-that-uses-different-header-names).</span><span class="sxs-lookup"><span data-stu-id="390d5-168">For more information, see [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) and [Configuration for a proxy that uses different header names](#configuration-for-a-proxy-that-uses-different-header-names).</span></span>

## <a name="iisiis-express-and-aspnet-core-module"></a><span data-ttu-id="390d5-169">IIS/IIS Express e modulo Core ASP.NET</span><span class="sxs-lookup"><span data-stu-id="390d5-169">IIS/IIS Express and ASP.NET Core Module</span></span>

<span data-ttu-id="390d5-170">Il middleware delle intestazioni inoltrate è abilitato per impostazione predefinita dal [middleware di integrazione IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) quando l'app è ospitata [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) dietro IIS e il modulo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="390d5-170">Forwarded Headers Middleware is enabled by default by [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when the app is hosted [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind IIS and the ASP.NET Core Module.</span></span> <span data-ttu-id="390d5-171">Il middleware delle intestazioni inoltrate viene attivato per l'esecuzione prima nella pipeline del middleware con una configurazione con restrizioni specifica per il modulo ASP.NET Core per motivi di attendibilità delle intestazioni inoltrate, ad esempio lo [spoofing degli indirizzi IP](https://www.iplocation.net/ip-spoofing).</span><span class="sxs-lookup"><span data-stu-id="390d5-171">Forwarded Headers Middleware is activated to run first in the middleware pipeline with a restricted configuration specific to the ASP.NET Core Module due to trust concerns with forwarded headers (for example, [IP spoofing](https://www.iplocation.net/ip-spoofing)).</span></span> <span data-ttu-id="390d5-172">Il middleware è configurato per inoltrare le intestazioni `X-Forwarded-For` e `X-Forwarded-Proto` ed è limitato a un singolo proxy localhost.</span><span class="sxs-lookup"><span data-stu-id="390d5-172">The middleware is configured to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers and is restricted to a single localhost proxy.</span></span> <span data-ttu-id="390d5-173">Se è richiesta una configurazione aggiuntiva, vedere [Opzioni del middleware delle intestazioni inoltrate](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="390d5-173">If additional configuration is required, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>

## <a name="other-proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="390d5-174">Altri scenari con server proxy e servizi di bilanciamento del carico</span><span class="sxs-lookup"><span data-stu-id="390d5-174">Other proxy server and load balancer scenarios</span></span>

<span data-ttu-id="390d5-175">Se non si usa il [middleware di integrazione IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) per l'hosting [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model), il middleware delle intestazioni inoltrate non è abilitato per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="390d5-175">Outside of using [IIS Integration](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when hosting [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model), Forwarded Headers Middleware isn't enabled by default.</span></span> <span data-ttu-id="390d5-176">Il middleware delle intestazioni inoltrate deve essere abilitato per consentire a un'app di inoltrare le intestazioni inoltrate del processo con <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>.</span><span class="sxs-lookup"><span data-stu-id="390d5-176">Forwarded Headers Middleware must be enabled for an app to process forwarded headers with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>.</span></span> <span data-ttu-id="390d5-177">Dopo aver abilitato il middleware, se non sono specificate opzioni <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> per il middleware, le intestazioni [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) predefinite sono [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="390d5-177">After enabling the middleware if no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified to the middleware, the default [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span>

<span data-ttu-id="390d5-178">Configurare il middleware con <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> per l'inoltro delle intestazioni `X-Forwarded-For` e `X-Forwarded-Proto` in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="390d5-178">Configure the middleware with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="390d5-179">Richiamare il metodo <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure` prima di chiamare altro middleware:</span><span class="sxs-lookup"><span data-stu-id="390d5-179">Invoke the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> method in `Startup.Configure` before calling other middleware:</span></span>

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
> <span data-ttu-id="390d5-180">Se non vengono specificate <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> in `Startup.ConfigureServices` o direttamente nel metodo di estensione con <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>, le intestazioni predefinite da inoltrare sono [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="390d5-180">If no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified in `Startup.ConfigureServices` or directly to the extension method with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>, the default headers to forward are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> <span data-ttu-id="390d5-181">La proprietà <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> deve essere configurata con le intestazioni da inoltrare.</span><span class="sxs-lookup"><span data-stu-id="390d5-181">The <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> property must be configured with the headers to forward.</span></span>

## <a name="nginx-configuration"></a><span data-ttu-id="390d5-182">Configurazione Nginx</span><span class="sxs-lookup"><span data-stu-id="390d5-182">Nginx configuration</span></span>

<span data-ttu-id="390d5-183">Per inoltrare le intestazioni `X-Forwarded-For` e `X-Forwarded-Proto`, vedere <xref:host-and-deploy/linux-nginx#configure-nginx>.</span><span class="sxs-lookup"><span data-stu-id="390d5-183">To forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers, see <xref:host-and-deploy/linux-nginx#configure-nginx>.</span></span> <span data-ttu-id="390d5-184">Per altre informazioni, vedere [NGINX: Using the Forwarded header](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/) (NGINX: Uso dell'intestazione Forwarded).</span><span class="sxs-lookup"><span data-stu-id="390d5-184">For more information, see [NGINX: Using the Forwarded header](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/).</span></span>

## <a name="apache-configuration"></a><span data-ttu-id="390d5-185">Configurazione Apache</span><span class="sxs-lookup"><span data-stu-id="390d5-185">Apache configuration</span></span>

<span data-ttu-id="390d5-186">`X-Forwarded-For` viene aggiunta automaticamente. Vedere [Apache Module mod_proxy: Reverse Proxy Request Headers](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers) (Modulo Apache mod_proxy: Intestazioni delle richieste del proxy inverso).</span><span class="sxs-lookup"><span data-stu-id="390d5-186">`X-Forwarded-For` is added automatically (see [Apache Module mod_proxy: Reverse Proxy Request Headers](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)).</span></span> <span data-ttu-id="390d5-187">Per informazioni su come inoltrare l'intestazione `X-Forwarded-Proto`, vedere <xref:host-and-deploy/linux-apache#configure-apache>.</span><span class="sxs-lookup"><span data-stu-id="390d5-187">For information on how to forward the `X-Forwarded-Proto` header, see <xref:host-and-deploy/linux-apache#configure-apache>.</span></span>

## <a name="forwarded-headers-middleware-options"></a><span data-ttu-id="390d5-188">Opzioni del middleware delle intestazioni inoltrate</span><span class="sxs-lookup"><span data-stu-id="390d5-188">Forwarded Headers Middleware options</span></span>

<span data-ttu-id="390d5-189"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> controllano il comportamento del middleware delle intestazioni inoltrate.</span><span class="sxs-lookup"><span data-stu-id="390d5-189"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> control the behavior of the Forwarded Headers Middleware.</span></span> <span data-ttu-id="390d5-190">L'esempio seguente modifica i valori predefiniti:</span><span class="sxs-lookup"><span data-stu-id="390d5-190">The following example changes the default values:</span></span>

* <span data-ttu-id="390d5-191">Limitare il numero di voci nelle intestazioni inoltrate a `2`.</span><span class="sxs-lookup"><span data-stu-id="390d5-191">Limit the number of entries in the forwarded headers to `2`.</span></span>
* <span data-ttu-id="390d5-192">Aggiungere un indirizzo proxy noto di `127.0.10.1`.</span><span class="sxs-lookup"><span data-stu-id="390d5-192">Add a known proxy address of `127.0.10.1`.</span></span>
* <span data-ttu-id="390d5-193">Modificare il nome dell'intestazione inoltrata dal nome predefinito `X-Forwarded-For` a `X-Forwarded-For-My-Custom-Header-Name`.</span><span class="sxs-lookup"><span data-stu-id="390d5-193">Change the forwarded header name from the default `X-Forwarded-For` to `X-Forwarded-For-My-Custom-Header-Name`.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardLimit = 2;
    options.KnownProxies.Add(IPAddress.Parse("127.0.10.1"));
    options.ForwardedForHeaderName = "X-Forwarded-For-My-Custom-Header-Name";
});
```

| <span data-ttu-id="390d5-194">Opzione</span><span class="sxs-lookup"><span data-stu-id="390d5-194">Option</span></span> | <span data-ttu-id="390d5-195">Description</span><span class="sxs-lookup"><span data-stu-id="390d5-195">Description</span></span> |
| ---
<span data-ttu-id="390d5-196">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="390d5-196">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="390d5-197">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="390d5-197">'Blazor'</span></span>
- <span data-ttu-id="390d5-198">'Identity'</span><span class="sxs-lookup"><span data-stu-id="390d5-198">'Identity'</span></span>
- <span data-ttu-id="390d5-199">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="390d5-199">'Let's Encrypt'</span></span>
- <span data-ttu-id="390d5-200">'Razor'</span><span class="sxs-lookup"><span data-stu-id="390d5-200">'Razor'</span></span>
- <span data-ttu-id="390d5-201">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="390d5-201">'SignalR' uid:</span></span> 

<span data-ttu-id="390d5-202">--- | Titolo---: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="390d5-202">--- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="390d5-203">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="390d5-203">'Blazor'</span></span>
- <span data-ttu-id="390d5-204">'Identity'</span><span class="sxs-lookup"><span data-stu-id="390d5-204">'Identity'</span></span>
- <span data-ttu-id="390d5-205">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="390d5-205">'Let's Encrypt'</span></span>
- <span data-ttu-id="390d5-206">'Razor'</span><span class="sxs-lookup"><span data-stu-id="390d5-206">'Razor'</span></span>
- <span data-ttu-id="390d5-207">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="390d5-207">'SignalR' uid:</span></span> 

-
<span data-ttu-id="390d5-208">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="390d5-208">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="390d5-209">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="390d5-209">'Blazor'</span></span>
- <span data-ttu-id="390d5-210">'Identity'</span><span class="sxs-lookup"><span data-stu-id="390d5-210">'Identity'</span></span>
- <span data-ttu-id="390d5-211">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="390d5-211">'Let's Encrypt'</span></span>
- <span data-ttu-id="390d5-212">'Razor'</span><span class="sxs-lookup"><span data-stu-id="390d5-212">'Razor'</span></span>
- <span data-ttu-id="390d5-213">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="390d5-213">'SignalR' uid:</span></span> 

-
<span data-ttu-id="390d5-214">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="390d5-214">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="390d5-215">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="390d5-215">'Blazor'</span></span>
- <span data-ttu-id="390d5-216">'Identity'</span><span class="sxs-lookup"><span data-stu-id="390d5-216">'Identity'</span></span>
- <span data-ttu-id="390d5-217">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="390d5-217">'Let's Encrypt'</span></span>
- <span data-ttu-id="390d5-218">'Razor'</span><span class="sxs-lookup"><span data-stu-id="390d5-218">'Razor'</span></span>
- <span data-ttu-id="390d5-219">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="390d5-219">'SignalR' uid:</span></span> 

<span data-ttu-id="390d5-220">------ | | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> | Limita gli host in base all' `X-Forwarded-Host` intestazione ai valori forniti.</span><span class="sxs-lookup"><span data-stu-id="390d5-220">------ | | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> | Restricts hosts by the `X-Forwarded-Host` header to the values provided.</span></span><ul><li><span data-ttu-id="390d5-221">I valori vengono confrontati tramite ordinal-ignore-case.</span><span class="sxs-lookup"><span data-stu-id="390d5-221">Values are compared using ordinal-ignore-case.</span></span></li><li><span data-ttu-id="390d5-222">I numeri di porta devono essere esclusi.</span><span class="sxs-lookup"><span data-stu-id="390d5-222">Port numbers must be excluded.</span></span></li><li><span data-ttu-id="390d5-223">Se l'elenco è vuoto, sono consentiti tutti gli host.</span><span class="sxs-lookup"><span data-stu-id="390d5-223">If the list is empty, all hosts are allowed.</span></span></li><li><span data-ttu-id="390d5-224">Un carattere jolly di primo livello `*` consente tutti gli host non vuoti.</span><span class="sxs-lookup"><span data-stu-id="390d5-224">A top-level wildcard `*` allows all non-empty hosts.</span></span></li><li><span data-ttu-id="390d5-225">I caratteri jolly per i sottodomini sono consentiti, ma non corrispondono al dominio radice.</span><span class="sxs-lookup"><span data-stu-id="390d5-225">Subdomain wildcards are permitted but don't match the root domain.</span></span> <span data-ttu-id="390d5-226">Ad esempio, `*.contoso.com` corrisponde al sottodominio `foo.contoso.com`, ma non al dominio radice `contoso.com`.</span><span class="sxs-lookup"><span data-stu-id="390d5-226">For example, `*.contoso.com` matches the subdomain `foo.contoso.com` but not the root domain `contoso.com`.</span></span></li><li><span data-ttu-id="390d5-227">I nomi host Unicode sono consentiti ma vengono convertiti in [Punycode](https://tools.ietf.org/html/rfc3492) per la corrispondenza.</span><span class="sxs-lookup"><span data-stu-id="390d5-227">Unicode host names are allowed but are converted to [Punycode](https://tools.ietf.org/html/rfc3492) for matching.</span></span></li><li><span data-ttu-id="390d5-228">Gli [indirizzi IPv6](https://tools.ietf.org/html/rfc4291) devono includere le parentesi quadre di delimitazione ed essere in [formato convenzionale](https://tools.ietf.org/html/rfc4291#section-2.2), ad esempio `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`.</span><span class="sxs-lookup"><span data-stu-id="390d5-228">[IPv6 addresses](https://tools.ietf.org/html/rfc4291) must include bounding brackets and be in [conventional form](https://tools.ietf.org/html/rfc4291#section-2.2) (for example, `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`).</span></span> <span data-ttu-id="390d5-229">Per gli indirizzi IPv6 non viene applicata la distinzione tra maiuscole e minuscole per la verifica dell'uguaglianza logica tra i diversi formati e non viene eseguita alcuna canonizzazione.</span><span class="sxs-lookup"><span data-stu-id="390d5-229">IPv6 addresses aren't special-cased to check for logical equality between different formats, and no canonicalization is performed.</span></span></li><li><span data-ttu-id="390d5-230">La mancata limitazione degli host consentiti potrebbe permettere a un utente malintenzionato di eseguire lo spoofing dei collegamenti generati dal servizio.</span><span class="sxs-lookup"><span data-stu-id="390d5-230">Failure to restrict the allowed hosts may allow an attacker to spoof links generated by the service.</span></span></li></ul><span data-ttu-id="390d5-231">Il valore predefinito è un insieme `IList<string>` vuoto.</span><span class="sxs-lookup"><span data-stu-id="390d5-231">The default value is an empty `IList<string>`.</span></span> <span data-ttu-id="390d5-232">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> | Usare l'intestazione specificata da questa proprietà anziché quella specificata da [ForwardedHeadersDefaults. XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName).</span><span class="sxs-lookup"><span data-stu-id="390d5-232">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> | Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName).</span></span> <span data-ttu-id="390d5-233">Questa opzione viene usata quando il proxy o il server d'inoltro non usa l'intestazione `X-Forwarded-For` ma usa un'altra intestazione per inoltrare le informazioni.</span><span class="sxs-lookup"><span data-stu-id="390d5-233">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-For` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="390d5-234">Il valore predefinito è `X-Forwarded-For`.</span><span class="sxs-lookup"><span data-stu-id="390d5-234">The default is `X-Forwarded-For`.</span></span> <span data-ttu-id="390d5-235">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> | Identifica i server d'esecuzione che devono essere elaborati.</span><span class="sxs-lookup"><span data-stu-id="390d5-235">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> | Identifies which forwarders should be processed.</span></span> <span data-ttu-id="390d5-236">Vedere [ForwardedHeaders Enum](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) (Enumerazione ForwardedHeaders) per l'elenco dei campi applicabili.</span><span class="sxs-lookup"><span data-stu-id="390d5-236">See the [ForwardedHeaders Enum](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) for the list of fields that apply.</span></span> <span data-ttu-id="390d5-237">I valori tipici assegnati a questa proprietà sono `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`.</span><span class="sxs-lookup"><span data-stu-id="390d5-237">Typical values assigned to this property are `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`.</span></span><br><br><span data-ttu-id="390d5-238">Il valore predefinito è [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="390d5-238">The default value is [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> <span data-ttu-id="390d5-239">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHostHeaderName> | Usare l'intestazione specificata da questa proprietà anziché quella specificata da [ForwardedHeadersDefaults. XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName).</span><span class="sxs-lookup"><span data-stu-id="390d5-239">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHostHeaderName> | Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName).</span></span> <span data-ttu-id="390d5-240">Questa opzione viene usata quando il proxy o il server d'inoltro non usa l'intestazione `X-Forwarded-Host` ma usa un'altra intestazione per inoltrare le informazioni.</span><span class="sxs-lookup"><span data-stu-id="390d5-240">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Host` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="390d5-241">Il valore predefinito è `X-Forwarded-Host`.</span><span class="sxs-lookup"><span data-stu-id="390d5-241">The default is `X-Forwarded-Host`.</span></span> <span data-ttu-id="390d5-242">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> | Usare l'intestazione specificata da questa proprietà anziché quella specificata da [ForwardedHeadersDefaults. XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName).</span><span class="sxs-lookup"><span data-stu-id="390d5-242">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> | Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName).</span></span> <span data-ttu-id="390d5-243">Questa opzione viene usata quando il proxy o il server d'inoltro non usa l'intestazione `X-Forwarded-Proto` ma usa un'altra intestazione per inoltrare le informazioni.</span><span class="sxs-lookup"><span data-stu-id="390d5-243">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Proto` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="390d5-244">Il valore predefinito è `X-Forwarded-Proto`.</span><span class="sxs-lookup"><span data-stu-id="390d5-244">The default is `X-Forwarded-Proto`.</span></span> <span data-ttu-id="390d5-245">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardLimit> | Limita il numero di voci nelle intestazioni elaborate.</span><span class="sxs-lookup"><span data-stu-id="390d5-245">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardLimit> | Limits the number of entries in the headers that are processed.</span></span> <span data-ttu-id="390d5-246">Impostare su `null` per disabilitare il limite, ma solo se è configurato `KnownProxies` o `KnownNetworks`.</span><span class="sxs-lookup"><span data-stu-id="390d5-246">Set to `null` to disable the limit, but this should only be done if `KnownProxies` or `KnownNetworks` are configured.</span></span> <span data-ttu-id="390d5-247">L'impostazione di un valore non `null` è una precauzione (ma non una garanzia) per proteggersi da proxy non configurati correttamente e richieste dannose provenienti da canali laterali in rete.</span><span class="sxs-lookup"><span data-stu-id="390d5-247">Setting a non-`null` value is a precaution (but not a guarantee) to guard against misconfigured proxies and malicious requests arriving from side-channels on the network.</span></span><br><br><span data-ttu-id="390d5-248">Il middleware delle intestazioni inoltrate elabora le intestazioni in ordine inverso da destra a sinistra.</span><span class="sxs-lookup"><span data-stu-id="390d5-248">Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="390d5-249">Se viene usato il valore predefinito (`1`), viene elaborato solo il valore più a destra delle intestazioni, a meno che non venga aumentato il valore di `ForwardLimit`.</span><span class="sxs-lookup"><span data-stu-id="390d5-249">If the default value (`1`) is used, only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span><br><br><span data-ttu-id="390d5-250">Il valore predefinito è `1`.</span><span class="sxs-lookup"><span data-stu-id="390d5-250">The default is `1`.</span></span> <span data-ttu-id="390d5-251">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks> | Intervalli di indirizzi delle reti note da cui accettare le intestazioni di inoltri.</span><span class="sxs-lookup"><span data-stu-id="390d5-251">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks> | Address ranges of known networks to accept forwarded headers from.</span></span> <span data-ttu-id="390d5-252">Specificare gli intervalli IP usando la notazione CIDR (Classless Interdomain Routing).</span><span class="sxs-lookup"><span data-stu-id="390d5-252">Provide IP ranges using Classless Interdomain Routing (CIDR) notation.</span></span><br><br><span data-ttu-id="390d5-253">Se il server usa socket dual mode, gli indirizzi IPv4 vengono forniti in un formato IPv6 (ad esempio, `10.0.0.1` in IPv4 rappresentato in IPv6 come `::ffff:10.0.0.1`).</span><span class="sxs-lookup"><span data-stu-id="390d5-253">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="390d5-254">Vedere [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="390d5-254">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="390d5-255">Determinare se questo formato è richiesto esaminando [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span><span class="sxs-lookup"><span data-stu-id="390d5-255">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="390d5-256">Per altre informazioni, vedere la sezione [Configurazione per un indirizzo IPv4 rappresentato come indirizzo IPv6](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address).</span><span class="sxs-lookup"><span data-stu-id="390d5-256">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="390d5-257">Il valore predefinito è un oggetto `IList`\<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> contenente una singola voce per `IPAddress.Loopback`.</span><span class="sxs-lookup"><span data-stu-id="390d5-257">The default is an `IList`\<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> containing a single entry for `IPAddress.Loopback`.</span></span> <span data-ttu-id="390d5-258">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies> | Indirizzi di proxy noti da cui accettare le intestazioni inoltrate.</span><span class="sxs-lookup"><span data-stu-id="390d5-258">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies> | Addresses of known proxies to accept forwarded headers from.</span></span> <span data-ttu-id="390d5-259">Usare `KnownProxies` per specificare le corrispondenze esatte degli indirizzi IP.</span><span class="sxs-lookup"><span data-stu-id="390d5-259">Use `KnownProxies` to specify exact IP address matches.</span></span><br><br><span data-ttu-id="390d5-260">Se il server usa socket dual mode, gli indirizzi IPv4 vengono forniti in un formato IPv6 (ad esempio, `10.0.0.1` in IPv4 rappresentato in IPv6 come `::ffff:10.0.0.1`).</span><span class="sxs-lookup"><span data-stu-id="390d5-260">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="390d5-261">Vedere [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="390d5-261">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="390d5-262">Determinare se questo formato è richiesto esaminando [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span><span class="sxs-lookup"><span data-stu-id="390d5-262">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="390d5-263">Per altre informazioni, vedere la sezione [Configurazione per un indirizzo IPv4 rappresentato come indirizzo IPv6](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address).</span><span class="sxs-lookup"><span data-stu-id="390d5-263">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="390d5-264">Il valore predefinito è un oggetto `IList`\<<xref:System.Net.IPAddress>> contenente una singola voce per `IPAddress.IPv6Loopback`.</span><span class="sxs-lookup"><span data-stu-id="390d5-264">The default is an `IList`\<<xref:System.Net.IPAddress>> containing a single entry for `IPAddress.IPv6Loopback`.</span></span> <span data-ttu-id="390d5-265">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalForHeaderName> | Usare l'intestazione specificata da questa proprietà anziché quella specificata da [ForwardedHeadersDefaults. XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).</span><span class="sxs-lookup"><span data-stu-id="390d5-265">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalForHeaderName> | Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).</span></span><br><br><span data-ttu-id="390d5-266">Il valore predefinito è `X-Original-For`.</span><span class="sxs-lookup"><span data-stu-id="390d5-266">The default is `X-Original-For`.</span></span> <span data-ttu-id="390d5-267">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalHostHeaderName> | Usare l'intestazione specificata da questa proprietà anziché quella specificata da [ForwardedHeadersDefaults. XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).</span><span class="sxs-lookup"><span data-stu-id="390d5-267">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalHostHeaderName> | Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).</span></span><br><br><span data-ttu-id="390d5-268">Il valore predefinito è `X-Original-Host`.</span><span class="sxs-lookup"><span data-stu-id="390d5-268">The default is `X-Original-Host`.</span></span> <span data-ttu-id="390d5-269">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalProtoHeaderName> | Usare l'intestazione specificata da questa proprietà anziché quella specificata da [ForwardedHeadersDefaults. XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).</span><span class="sxs-lookup"><span data-stu-id="390d5-269">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalProtoHeaderName> | Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).</span></span><br><br><span data-ttu-id="390d5-270">Il valore predefinito è `X-Original-Proto`.</span><span class="sxs-lookup"><span data-stu-id="390d5-270">The default is `X-Original-Proto`.</span></span> <span data-ttu-id="390d5-271">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.RequireHeaderSymmetry> | Richiedere che il numero di valori di intestazione sia sincronizzato tra [ForwardedHeadersOptions. ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) in fase di elaborazione.</span><span class="sxs-lookup"><span data-stu-id="390d5-271">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.RequireHeaderSymmetry> | Require the number of header values to be in sync between the [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) being processed.</span></span><br><br><span data-ttu-id="390d5-272">Il valore predefinito in ASP.NET Core 1.x è `true`.</span><span class="sxs-lookup"><span data-stu-id="390d5-272">The default in ASP.NET Core 1.x is `true`.</span></span> <span data-ttu-id="390d5-273">Il valore predefinito in ASP.NET Core 2.0 o versione successiva è `false`.</span><span class="sxs-lookup"><span data-stu-id="390d5-273">The default in ASP.NET Core 2.0 or later is `false`.</span></span> |

## <a name="scenarios-and-use-cases"></a><span data-ttu-id="390d5-274">Scenari e casi d'uso</span><span class="sxs-lookup"><span data-stu-id="390d5-274">Scenarios and use cases</span></span>

### <a name="when-it-isnt-possible-to-add-forwarded-headers-and-all-requests-are-secure"></a><span data-ttu-id="390d5-275">Quando non è possibile aggiungere le intestazioni inoltrate e tutte le richieste sono sicure</span><span class="sxs-lookup"><span data-stu-id="390d5-275">When it isn't possible to add forwarded headers and all requests are secure</span></span>

<span data-ttu-id="390d5-276">In alcuni casi, potrebbe non essere possibile aggiungere le intestazioni inoltrate alle richieste trasmesse tramite proxy all'app.</span><span class="sxs-lookup"><span data-stu-id="390d5-276">In some cases, it might not be possible to add forwarded headers to the requests proxied to the app.</span></span> <span data-ttu-id="390d5-277">Se il proxy impone che tutte le richieste esterne pubbliche siano HTTPS, lo schema può essere impostato manualmente in `Startup.Configure` prima di usare qualsiasi tipo di middleware:</span><span class="sxs-lookup"><span data-stu-id="390d5-277">If the proxy is enforcing that all public external requests are HTTPS, the scheme can be manually set in `Startup.Configure` before using any type of middleware:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.Scheme = "https";
    return next();
});
```

<span data-ttu-id="390d5-278">Questo codice può essere disabilitato tramite una variabile di ambiente o un'altra impostazione di configurazione in un ambiente di sviluppo o di gestione temporanea.</span><span class="sxs-lookup"><span data-stu-id="390d5-278">This code can be disabled with an environment variable or other configuration setting in a development or staging environment.</span></span>

### <a name="deal-with-path-base-and-proxies-that-change-the-request-path"></a><span data-ttu-id="390d5-279">Gestire la base del percorso e i proxy che modificano il percorso della richiesta</span><span class="sxs-lookup"><span data-stu-id="390d5-279">Deal with path base and proxies that change the request path</span></span>

<span data-ttu-id="390d5-280">Alcuni proxy passano il percorso senza modifiche, ma con un percorso di base dell'app che deve essere rimosso per consentire il corretto funzionamento del routing.</span><span class="sxs-lookup"><span data-stu-id="390d5-280">Some proxies pass the path intact but with an app base path that should be removed so that routing works properly.</span></span> <span data-ttu-id="390d5-281">Il middleware [UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) suddivide il percorso in [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) e il percorso di base dell'app in [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).</span><span class="sxs-lookup"><span data-stu-id="390d5-281">[UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) middleware splits the path into [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) and the app base path into [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).</span></span>

<span data-ttu-id="390d5-282">Se `/foo` è il percorso di base dell'app per un percorso proxy passato come `/foo/api/1`, il middleware imposta `Request.PathBase` su `/foo` e `Request.Path` su `/api/1` con il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="390d5-282">If `/foo` is the app base path for a proxy path passed as `/foo/api/1`, the middleware sets `Request.PathBase` to `/foo` and `Request.Path` to `/api/1` with the following command:</span></span>

```csharp
app.UsePathBase("/foo");
```

<span data-ttu-id="390d5-283">Il percorso originale e la base del percorso vengono riapplicati quando il middleware viene chiamato nuovamente in direzione inversa.</span><span class="sxs-lookup"><span data-stu-id="390d5-283">The original path and path base are reapplied when the middleware is called again in reverse.</span></span> <span data-ttu-id="390d5-284">Per altre informazioni dell'elaborazione degli ordini del middleware, vedere <xref:fundamentals/middleware/index>.</span><span class="sxs-lookup"><span data-stu-id="390d5-284">For more information on middleware order processing, see <xref:fundamentals/middleware/index>.</span></span>

<span data-ttu-id="390d5-285">Se il proxy taglia il percorso (ad esempio, inoltrando `/foo/api/1` a `/api/1`), correggere i reindirizzamenti e i collegamenti impostando la proprietà [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) della richiesta:</span><span class="sxs-lookup"><span data-stu-id="390d5-285">If the proxy trims the path (for example, forwarding `/foo/api/1` to `/api/1`), fix redirects and links by setting the request's [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) property:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.PathBase = new PathString("/foo");
    return next();
});
```

<span data-ttu-id="390d5-286">Se il proxy aggiunge i dati del percorso, eliminare parte del percorso per correggere i reindirizzamenti e i collegamenti usando <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> ed eseguendo l'assegnazione alla proprietà <xref:Microsoft.AspNetCore.Http.HttpRequest.Path>:</span><span class="sxs-lookup"><span data-stu-id="390d5-286">If the proxy is adding path data, discard part of the path to fix redirects and links by using <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> and assigning to the <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> property:</span></span>

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

### <a name="configuration-for-a-proxy-that-uses-different-header-names"></a><span data-ttu-id="390d5-287">Configurazione per un proxy che usa nomi di intestazione diversi</span><span class="sxs-lookup"><span data-stu-id="390d5-287">Configuration for a proxy that uses different header names</span></span>

<span data-ttu-id="390d5-288">Se il proxy non usa intestazioni denominate `X-Forwarded-For` e `X-Forwarded-Proto` per inoltrare l'indirizzo proxy o la porta e le informazioni dello scherma originali, impostare le opzioni <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> e <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> in modo che corrispondano ai nomi di intestazione usati dal proxy:</span><span class="sxs-lookup"><span data-stu-id="390d5-288">If the proxy doesn't use headers named `X-Forwarded-For` and `X-Forwarded-Proto` to forward the proxy address/port and originating scheme information, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the proxy:</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedForHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-For_Header";
    options.ForwardedProtoHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-Proto_Header";
});
```

### <a name="configuration-for-an-ipv4-address-represented-as-an-ipv6-address"></a><span data-ttu-id="390d5-289">Configurazione per un indirizzo IPv4 rappresentato come indirizzo IPv6</span><span class="sxs-lookup"><span data-stu-id="390d5-289">Configuration for an IPv4 address represented as an IPv6 address</span></span>

<span data-ttu-id="390d5-290">Se il server usa socket dual mode, gli indirizzi IPv4 vengono forniti in un formato IPv6 (ad esempio, `10.0.0.1` in IPv4 rappresentato in IPv6 come `::ffff:10.0.0.1` o `::ffff:a00:1`).</span><span class="sxs-lookup"><span data-stu-id="390d5-290">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1` or `::ffff:a00:1`).</span></span> <span data-ttu-id="390d5-291">Vedere [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="390d5-291">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="390d5-292">Determinare se questo formato è richiesto esaminando [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span><span class="sxs-lookup"><span data-stu-id="390d5-292">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span>

<span data-ttu-id="390d5-293">Nell'esempio seguente un indirizzo di rete che fornisce intestazioni inoltrate viene aggiunto all'elenco `KnownNetworks` in formato IPv6.</span><span class="sxs-lookup"><span data-stu-id="390d5-293">In the following example, a network address that supplies forwarded headers is added to the `KnownNetworks` list in IPv6 format.</span></span>

<span data-ttu-id="390d5-294">Indirizzo IPv4: `10.11.12.1/8`</span><span class="sxs-lookup"><span data-stu-id="390d5-294">IPv4 address: `10.11.12.1/8`</span></span>

<span data-ttu-id="390d5-295">Indirizzo IPv6 convertito: `::ffff:10.11.12.1`</span><span class="sxs-lookup"><span data-stu-id="390d5-295">Converted IPv6 address: `::ffff:10.11.12.1`</span></span>  
<span data-ttu-id="390d5-296">Lunghezza del prefisso convertito: 104</span><span class="sxs-lookup"><span data-stu-id="390d5-296">Converted prefix length: 104</span></span>

<span data-ttu-id="390d5-297">È anche possibile specificare l'indirizzo in formato esadecimale (`10.11.12.1` rappresentato in IPv6 come `::ffff:0a0b:0c01`).</span><span class="sxs-lookup"><span data-stu-id="390d5-297">You can also supply the address in hexadecimal format (`10.11.12.1` represented in IPv6 as `::ffff:0a0b:0c01`).</span></span> <span data-ttu-id="390d5-298">Durante la conversione di un indirizzo IPv4 in IPv6, aggiungere 96 alla lunghezza del prefisso CIDR (`8` nell'esempio) per tenere conto del prefisso IPv6 aggiuntivo `::ffff:` (8 + 96 = 104).</span><span class="sxs-lookup"><span data-stu-id="390d5-298">When converting an IPv4 address to IPv6, add 96 to the CIDR Prefix Length (`8` in the example) to account for the additional `::ffff:` IPv6 prefix (8 + 96 = 104).</span></span> 

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

## <a name="forward-the-scheme-for-linux-and-non-iis-reverse-proxies"></a><span data-ttu-id="390d5-299">Inoltrare lo schema per Linux e proxy inversi non IIS</span><span class="sxs-lookup"><span data-stu-id="390d5-299">Forward the scheme for Linux and non-IIS reverse proxies</span></span>

<span data-ttu-id="390d5-300">Con le app che chiamano <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> e <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> il sito si ritrova in un ciclo infinito se distribuito in un servizio app Linux di Azure, in una macchina virtuale Linux di Azure o dietro eventuali altri proxy inversi diversi da IIS.</span><span class="sxs-lookup"><span data-stu-id="390d5-300">Apps that call <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> and <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> put a site into an infinite loop if deployed to an Azure Linux App Service, Azure Linux virtual machine (VM), or behind any other reverse proxy besides IIS.</span></span> <span data-ttu-id="390d5-301">TLS viene terminato dal proxy inverso e Kestrel non viene a conoscenza dello schema di richiesta corretto.</span><span class="sxs-lookup"><span data-stu-id="390d5-301">TLS is terminated by the reverse proxy, and Kestrel isn't made aware of the correct request scheme.</span></span> <span data-ttu-id="390d5-302">Si verificano errori anche per OAuth e OIDC in questa configurazione perché generano reindirizzamenti non corretti.</span><span class="sxs-lookup"><span data-stu-id="390d5-302">OAuth and OIDC also fail in this configuration because they generate incorrect redirects.</span></span> <span data-ttu-id="390d5-303"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> aggiunge e configura il middleware delle intestazioni inoltrate in caso di esecuzione dietro IIS, ma non esiste alcuna configurazione automatica corrispondente per Linux (integrazione di Apache o Nginx).</span><span class="sxs-lookup"><span data-stu-id="390d5-303"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> adds and configures Forwarded Headers Middleware when running behind IIS, but there's no matching automatic configuration for Linux (Apache or Nginx integration).</span></span>

<span data-ttu-id="390d5-304">Per inoltrare lo schema dal proxy in scenari non IIS, aggiungere e configurare il middleware delle intestazioni inoltrate.</span><span class="sxs-lookup"><span data-stu-id="390d5-304">To forward the scheme from the proxy in non-IIS scenarios, add and configure Forwarded Headers Middleware.</span></span> <span data-ttu-id="390d5-305">In `Startup.ConfigureServices` usare il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="390d5-305">In `Startup.ConfigureServices`, use the following code:</span></span>

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

## <a name="certificate-forwarding"></a><span data-ttu-id="390d5-306">Inoltro di certificati</span><span class="sxs-lookup"><span data-stu-id="390d5-306">Certificate forwarding</span></span> 

### <a name="azure"></a><span data-ttu-id="390d5-307">Azure</span><span class="sxs-lookup"><span data-stu-id="390d5-307">Azure</span></span>

<span data-ttu-id="390d5-308">Per configurare app Azure servizio per l'invio di certificati, vedere [configurare l'autenticazione reciproca TLS per il servizio app Azure](/azure/app-service/app-service-web-configure-tls-mutual-auth).</span><span class="sxs-lookup"><span data-stu-id="390d5-308">To configure Azure App Service for certificate forwarding, see [Configure TLS mutual authentication for Azure App Service](/azure/app-service/app-service-web-configure-tls-mutual-auth).</span></span> <span data-ttu-id="390d5-309">Le linee guida seguenti riguardano la configurazione dell'app ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="390d5-309">The following guidance pertains to configuring the ASP.NET Core app.</span></span>

<span data-ttu-id="390d5-310">In `Startup.Configure` aggiungere il codice seguente prima della chiamata a `app.UseAuthentication();` :</span><span class="sxs-lookup"><span data-stu-id="390d5-310">In `Startup.Configure`, add the following code before the call to `app.UseAuthentication();`:</span></span>

```csharp
app.UseCertificateForwarding();
```


<span data-ttu-id="390d5-311">Configurare il middleware di invio del certificato per specificare il nome dell'intestazione usato da Azure.</span><span class="sxs-lookup"><span data-stu-id="390d5-311">Configure Certificate Forwarding Middleware to specify the header name that Azure uses.</span></span> <span data-ttu-id="390d5-312">In `Startup.ConfigureServices` aggiungere il codice seguente per configurare l'intestazione da cui il middleware compila un certificato:</span><span class="sxs-lookup"><span data-stu-id="390d5-312">In `Startup.ConfigureServices`, add the following code to configure the header from which the middleware builds a certificate:</span></span>

```csharp
services.AddCertificateForwarding(options =>
    options.CertificateHeader = "X-ARR-ClientCert");
```

### <a name="other-web-proxies"></a><span data-ttu-id="390d5-313">Altri proxy Web</span><span class="sxs-lookup"><span data-stu-id="390d5-313">Other web proxies</span></span>

<span data-ttu-id="390d5-314">Se viene usato un proxy che non è IIS o app Azure Application Request Routing (ARR) del servizio, configurare il proxy in modo che inoltri il certificato ricevuto in un'intestazione HTTP.</span><span class="sxs-lookup"><span data-stu-id="390d5-314">If a proxy is used that isn't IIS or Azure App Service's Application Request Routing (ARR), configure the proxy to forward the certificate that it received in an HTTP header.</span></span> <span data-ttu-id="390d5-315">In `Startup.Configure` aggiungere il codice seguente prima della chiamata a `app.UseAuthentication();` :</span><span class="sxs-lookup"><span data-stu-id="390d5-315">In `Startup.Configure`, add the following code before the call to `app.UseAuthentication();`:</span></span>

```csharp
app.UseCertificateForwarding();
```

<span data-ttu-id="390d5-316">Configurare il middleware di invio del certificato per specificare il nome dell'intestazione.</span><span class="sxs-lookup"><span data-stu-id="390d5-316">Configure the Certificate Forwarding Middleware to specify the header name.</span></span> <span data-ttu-id="390d5-317">In `Startup.ConfigureServices` aggiungere il codice seguente per configurare l'intestazione da cui il middleware compila un certificato:</span><span class="sxs-lookup"><span data-stu-id="390d5-317">In `Startup.ConfigureServices`, add the following code to configure the header from which the middleware builds a certificate:</span></span>

```csharp
services.AddCertificateForwarding(options =>
    options.CertificateHeader = "YOUR_CERTIFICATE_HEADER_NAME");
```

<span data-ttu-id="390d5-318">Se il proxy non prevede la codifica Base64 del certificato (come nel caso di nginx), impostare l' `HeaderConverter` opzione.</span><span class="sxs-lookup"><span data-stu-id="390d5-318">If the proxy isn't base64-encoding the certificate (as is the case with Nginx), set the `HeaderConverter` option.</span></span> <span data-ttu-id="390d5-319">Si consideri l'esempio seguente in `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="390d5-319">Consider the following example in `Startup.ConfigureServices`:</span></span>

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

## <a name="troubleshoot"></a><span data-ttu-id="390d5-320">Risolvere problemi</span><span class="sxs-lookup"><span data-stu-id="390d5-320">Troubleshoot</span></span>

<span data-ttu-id="390d5-321">Quando le intestazioni non vengono inoltrate come previsto, abilitare la [registrazione](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="390d5-321">When headers aren't forwarded as expected, enable [logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="390d5-322">Se i log non forniscono informazioni sufficienti per risolvere il problema, enumerare le intestazioni delle richieste ricevute dal server.</span><span class="sxs-lookup"><span data-stu-id="390d5-322">If the logs don't provide sufficient information to troubleshoot the problem, enumerate the request headers received by the server.</span></span> <span data-ttu-id="390d5-323">Usare il middleware inline per scrivere le intestazioni di richiesta in una risposta dell'app o per registrare le intestazioni.</span><span class="sxs-lookup"><span data-stu-id="390d5-323">Use inline middleware to write request headers to an app response or log the headers.</span></span> 

<span data-ttu-id="390d5-324">Per scrivere le intestazioni nella risposta dell'app, aggiungere il middleware inline di terminale seguente immediatamente dopo la chiamata a <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="390d5-324">To write the headers to the app's response, place the following terminal inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`:</span></span>

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

<span data-ttu-id="390d5-325">È possibile scrivere nei log invece che nel corpo della risposta.</span><span class="sxs-lookup"><span data-stu-id="390d5-325">You can write to logs instead of the response body.</span></span> <span data-ttu-id="390d5-326">La scrittura nei log permette il normale funzionamento del sito durante il debug.</span><span class="sxs-lookup"><span data-stu-id="390d5-326">Writing to logs allows the site to function normally while debugging.</span></span>

<span data-ttu-id="390d5-327">Per scrivere nei log invece che nel corpo della risposta:</span><span class="sxs-lookup"><span data-stu-id="390d5-327">To write logs rather than to the response body:</span></span>

* <span data-ttu-id="390d5-328">Inserire `ILogger<Startup>` nella classe `Startup` come descritto in [Creare log in Startup](xref:fundamentals/logging/index#create-logs-in-startup).</span><span class="sxs-lookup"><span data-stu-id="390d5-328">Inject `ILogger<Startup>` into the `Startup` class as described in [Create logs in Startup](xref:fundamentals/logging/index#create-logs-in-startup).</span></span>
* <span data-ttu-id="390d5-329">Aggiungere il middleware inline seguente immediatamente dopo la chiamata a <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="390d5-329">Place the following inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`.</span></span>

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

<span data-ttu-id="390d5-330">Durante l'elaborazione, i valori di `X-Forwarded-{For|Proto|Host}` vengono spostati in `X-Original-{For|Proto|Host}`.</span><span class="sxs-lookup"><span data-stu-id="390d5-330">When processed, `X-Forwarded-{For|Proto|Host}` values are moved to `X-Original-{For|Proto|Host}`.</span></span> <span data-ttu-id="390d5-331">Se sono presenti più valori in una determinata intestazione, il middleware delle intestazioni inoltrate elabora le intestazioni in ordine inverso da destra a sinistra.</span><span class="sxs-lookup"><span data-stu-id="390d5-331">If there are multiple values in a given header, Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="390d5-332">Il valore predefinito di `ForwardLimit` è `1` (uno) e, pertanto, viene elaborato solo il valore più a destra delle intestazioni, a meno che non venga aumentato il valore di `ForwardLimit`.</span><span class="sxs-lookup"><span data-stu-id="390d5-332">The default `ForwardLimit` is `1` (one), so only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span>

<span data-ttu-id="390d5-333">L'indirizzo IP remoto originale della richiesta deve corrispondere a una voce negli elenchi `KnownProxies` o `KnownNetworks` prima dell'elaborazione delle intestazioni inoltrate.</span><span class="sxs-lookup"><span data-stu-id="390d5-333">The request's original remote IP must match an entry in the `KnownProxies` or `KnownNetworks` lists before forwarded headers are processed.</span></span> <span data-ttu-id="390d5-334">Questo riduce lo spoofing delle intestazioni, non accettando server di inoltro da proxy non attendibili.</span><span class="sxs-lookup"><span data-stu-id="390d5-334">This limits header spoofing by not accepting forwarders from untrusted proxies.</span></span> <span data-ttu-id="390d5-335">Quando viene rilevato un proxy sconosciuto, la registrazione indica l'indirizzo del proxy:</span><span class="sxs-lookup"><span data-stu-id="390d5-335">When an unknown proxy is detected, logging indicates the address of the proxy:</span></span>

```console
September 20th 2018, 15:49:44.168 Unknown proxy: 10.0.0.100:54321
```

<span data-ttu-id="390d5-336">Nell'esempio precedente, 10.0.0.100 è un server proxy.</span><span class="sxs-lookup"><span data-stu-id="390d5-336">In the preceding example, 10.0.0.100 is a proxy server.</span></span> <span data-ttu-id="390d5-337">Se il server è un proxy attendibile, aggiungere l'indirizzo IP del server a `KnownProxies` (o aggiungere una rete attendibile a `KnownNetworks`) in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="390d5-337">If the server is a trusted proxy, add the server's IP address to `KnownProxies` (or add a trusted network to `KnownNetworks`) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="390d5-338">Per altre informazioni, vedere la sezione [Opzioni del middleware delle intestazioni inoltrate](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="390d5-338">For more information, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) section.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

> [!IMPORTANT]
> <span data-ttu-id="390d5-339">Consentire solo a reti e proxy attendibili di inoltrare le intestazioni.</span><span class="sxs-lookup"><span data-stu-id="390d5-339">Only allow trusted proxies and networks to forward headers.</span></span> <span data-ttu-id="390d5-340">In caso contrario, possono verificarsi attacchi di [spoofing degli indirizzi IP](https://www.iplocation.net/ip-spoofing).</span><span class="sxs-lookup"><span data-stu-id="390d5-340">Otherwise, [IP spoofing](https://www.iplocation.net/ip-spoofing) attacks are possible.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="390d5-341">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="390d5-341">Additional resources</span></span>

* <xref:host-and-deploy/web-farm>
* [<span data-ttu-id="390d5-342">Avviso di sicurezza Microsoft CVE-2018-0787: vulnerabilità di elevazione dei privilegi di ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="390d5-342">Microsoft Security Advisory CVE-2018-0787: ASP.NET Core Elevation Of Privilege Vulnerability</span></span>](https://github.com/aspnet/Announcements/issues/295)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="390d5-343">Nella configurazione consigliata per ASP.NET Core, l'app è ospitata mediante IIS e il modulo ASP.NET Core, Nginx o Apache.</span><span class="sxs-lookup"><span data-stu-id="390d5-343">In the recommended configuration for ASP.NET Core, the app is hosted using IIS/ASP.NET Core Module, Nginx, or Apache.</span></span> <span data-ttu-id="390d5-344">Server proxy, servizi di bilanciamento del carico e altre appliance di rete spesso nascondono informazioni sulla richiesta prima che questa raggiunga l'app:</span><span class="sxs-lookup"><span data-stu-id="390d5-344">Proxy servers, load balancers, and other network appliances often obscure information about the request before it reaches the app:</span></span>

* <span data-ttu-id="390d5-345">Quando le richieste HTTPS vengono trasmesse tramite proxy su HTTP, lo schema originale (HTTPS) viene perso e deve essere inoltrato in un'intestazione.</span><span class="sxs-lookup"><span data-stu-id="390d5-345">When HTTPS requests are proxied over HTTP, the original scheme (HTTPS) is lost and must be forwarded in a header.</span></span>
* <span data-ttu-id="390d5-346">Poiché un'app riceve una richiesta dal proxy e non dall'effettiva origine su Internet o nella rete aziendale, anche l'indirizzo IP di origine del client deve essere inoltrato in un'intestazione.</span><span class="sxs-lookup"><span data-stu-id="390d5-346">Because an app receives a request from the proxy and not its true source on the Internet or corporate network, the originating client IP address must also be forwarded in a header.</span></span>

<span data-ttu-id="390d5-347">Queste informazioni potrebbero essere importanti per l'elaborazione delle richieste, ad esempio per i reindirizzamenti, l'autenticazione, la generazione di collegamenti, la valutazione dei criteri e la georilevazione dei client.</span><span class="sxs-lookup"><span data-stu-id="390d5-347">This information may be important in request processing, for example in redirects, authentication, link generation, policy evaluation, and client geolocation.</span></span>

## <a name="forwarded-headers"></a><span data-ttu-id="390d5-348">Intestazioni inoltrate</span><span class="sxs-lookup"><span data-stu-id="390d5-348">Forwarded headers</span></span>

<span data-ttu-id="390d5-349">Per convenzione, i proxy inoltrano le informazioni nelle intestazioni HTTP.</span><span class="sxs-lookup"><span data-stu-id="390d5-349">By convention, proxies forward information in HTTP headers.</span></span>

| <span data-ttu-id="390d5-350">Header</span><span class="sxs-lookup"><span data-stu-id="390d5-350">Header</span></span> | <span data-ttu-id="390d5-351">Description</span><span class="sxs-lookup"><span data-stu-id="390d5-351">Description</span></span> |
| ---
<span data-ttu-id="390d5-352">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="390d5-352">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="390d5-353">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="390d5-353">'Blazor'</span></span>
- <span data-ttu-id="390d5-354">'Identity'</span><span class="sxs-lookup"><span data-stu-id="390d5-354">'Identity'</span></span>
- <span data-ttu-id="390d5-355">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="390d5-355">'Let's Encrypt'</span></span>
- <span data-ttu-id="390d5-356">'Razor'</span><span class="sxs-lookup"><span data-stu-id="390d5-356">'Razor'</span></span>
- <span data-ttu-id="390d5-357">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="390d5-357">'SignalR' uid:</span></span> 

<span data-ttu-id="390d5-358">--- | Titolo---: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="390d5-358">--- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="390d5-359">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="390d5-359">'Blazor'</span></span>
- <span data-ttu-id="390d5-360">'Identity'</span><span class="sxs-lookup"><span data-stu-id="390d5-360">'Identity'</span></span>
- <span data-ttu-id="390d5-361">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="390d5-361">'Let's Encrypt'</span></span>
- <span data-ttu-id="390d5-362">'Razor'</span><span class="sxs-lookup"><span data-stu-id="390d5-362">'Razor'</span></span>
- <span data-ttu-id="390d5-363">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="390d5-363">'SignalR' uid:</span></span> 

-
<span data-ttu-id="390d5-364">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="390d5-364">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="390d5-365">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="390d5-365">'Blazor'</span></span>
- <span data-ttu-id="390d5-366">'Identity'</span><span class="sxs-lookup"><span data-stu-id="390d5-366">'Identity'</span></span>
- <span data-ttu-id="390d5-367">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="390d5-367">'Let's Encrypt'</span></span>
- <span data-ttu-id="390d5-368">'Razor'</span><span class="sxs-lookup"><span data-stu-id="390d5-368">'Razor'</span></span>
- <span data-ttu-id="390d5-369">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="390d5-369">'SignalR' uid:</span></span> 

-
<span data-ttu-id="390d5-370">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="390d5-370">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="390d5-371">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="390d5-371">'Blazor'</span></span>
- <span data-ttu-id="390d5-372">'Identity'</span><span class="sxs-lookup"><span data-stu-id="390d5-372">'Identity'</span></span>
- <span data-ttu-id="390d5-373">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="390d5-373">'Let's Encrypt'</span></span>
- <span data-ttu-id="390d5-374">'Razor'</span><span class="sxs-lookup"><span data-stu-id="390d5-374">'Razor'</span></span>
- <span data-ttu-id="390d5-375">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="390d5-375">'SignalR' uid:</span></span> 

<span data-ttu-id="390d5-376">------ | | X-Inoltred-per | Include informazioni sul client che ha avviato la richiesta e i proxy successivi in una catena di proxy.</span><span class="sxs-lookup"><span data-stu-id="390d5-376">------ | | X-Forwarded-For | Holds information about the client that initiated the request and subsequent proxies in a chain of proxies.</span></span> <span data-ttu-id="390d5-377">Questo parametro può contenere indirizzi IP (e, facoltativamente, numeri di porta).</span><span class="sxs-lookup"><span data-stu-id="390d5-377">This parameter may contain IP addresses (and, optionally, port numbers).</span></span> <span data-ttu-id="390d5-378">In una catena di server proxy, il primo parametro indica il client in cui è stata eseguita inizialmente la richiesta.</span><span class="sxs-lookup"><span data-stu-id="390d5-378">In a chain of proxy servers, the first parameter indicates the client where the request was first made.</span></span> <span data-ttu-id="390d5-379">Seguono gli identificatori dei proxy successivi.</span><span class="sxs-lookup"><span data-stu-id="390d5-379">Subsequent proxy identifiers follow.</span></span> <span data-ttu-id="390d5-380">L'ultimo proxy della catena non è incluso nell'elenco dei parametri.</span><span class="sxs-lookup"><span data-stu-id="390d5-380">The last proxy in the chain isn't in the list of parameters.</span></span> <span data-ttu-id="390d5-381">L'indirizzo IP dell'ultimo proxy e, facoltativamente, un numero di porta sono disponibili come indirizzo IP remoto a livello di trasporto.</span><span class="sxs-lookup"><span data-stu-id="390d5-381">The last proxy's IP address, and optionally a port number, are available as the remote IP address at the transport layer.</span></span> <span data-ttu-id="390d5-382">| | X-Inoltred-proto | Valore dello schema di origine (HTTP/HTTPS).</span><span class="sxs-lookup"><span data-stu-id="390d5-382">| | X-Forwarded-Proto | The value of the originating scheme (HTTP/HTTPS).</span></span> <span data-ttu-id="390d5-383">Il valore può essere anche un elenco di schemi, se la richiesta ha attraversato più proxy.</span><span class="sxs-lookup"><span data-stu-id="390d5-383">The value may also be a list of schemes if the request has traversed multiple proxies.</span></span> <span data-ttu-id="390d5-384">| | X-Inoltred-host | Valore originale del campo dell'intestazione host.</span><span class="sxs-lookup"><span data-stu-id="390d5-384">| | X-Forwarded-Host | The original value of the Host header field.</span></span> <span data-ttu-id="390d5-385">I proxy in genere non modificano l'intestazione host.</span><span class="sxs-lookup"><span data-stu-id="390d5-385">Usually, proxies don't modify the Host header.</span></span> <span data-ttu-id="390d5-386">Vedere l'[avviso di sicurezza Microsoft CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) per informazioni su una vulnerabilità di elevazione dei privilegi che interessa i sistemi in cui il proxy non convalida o limita le intestazioni host a valori di riferimento noti.</span><span class="sxs-lookup"><span data-stu-id="390d5-386">See [Microsoft Security Advisory CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) for information on an elevation-of-privileges vulnerability that affects systems where the proxy doesn't validate or restrict Host headers to known good values.</span></span> |

<span data-ttu-id="390d5-387">Il middleware delle intestazioni inoltrate, dal pacchetto [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/), legge le intestazioni e compila i campi associati in <xref:Microsoft.AspNetCore.Http.HttpContext>.</span><span class="sxs-lookup"><span data-stu-id="390d5-387">The Forwarded Headers Middleware, from the [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) package, reads these headers and fills in the associated fields on <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span>

<span data-ttu-id="390d5-388">Il middleware aggiorna:</span><span class="sxs-lookup"><span data-stu-id="390d5-388">The middleware updates:</span></span>

* <span data-ttu-id="390d5-389">[HttpContext. Connection. RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress): set usando il `X-Forwarded-For` valore dell'intestazione.</span><span class="sxs-lookup"><span data-stu-id="390d5-389">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress): Set using the `X-Forwarded-For` header value.</span></span> <span data-ttu-id="390d5-390">Impostazioni aggiuntive influiscono sul modo in cui il middleware imposta `RemoteIpAddress`.</span><span class="sxs-lookup"><span data-stu-id="390d5-390">Additional settings influence how the middleware sets `RemoteIpAddress`.</span></span> <span data-ttu-id="390d5-391">Per informazioni dettagliate, vedere [Opzioni del middleware delle intestazioni inoltrate](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="390d5-391">For details, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>
* <span data-ttu-id="390d5-392">[HttpContext. Request. Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme): impostare usando il `X-Forwarded-Proto` valore dell'intestazione.</span><span class="sxs-lookup"><span data-stu-id="390d5-392">[HttpContext.Request.Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme): Set using the `X-Forwarded-Proto` header value.</span></span>
* <span data-ttu-id="390d5-393">[HttpContext. Request. host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host): impostare usando il `X-Forwarded-Host` valore dell'intestazione.</span><span class="sxs-lookup"><span data-stu-id="390d5-393">[HttpContext.Request.Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host): Set using the `X-Forwarded-Host` header value.</span></span>

<span data-ttu-id="390d5-394">È possibile configurare le [impostazioni predefinite](#forwarded-headers-middleware-options) del middleware delle intestazioni inoltrate.</span><span class="sxs-lookup"><span data-stu-id="390d5-394">Forwarded Headers Middleware [default settings](#forwarded-headers-middleware-options) can be configured.</span></span> <span data-ttu-id="390d5-395">Le impostazioni predefinite sono le seguenti:</span><span class="sxs-lookup"><span data-stu-id="390d5-395">The default settings are:</span></span>

* <span data-ttu-id="390d5-396">È presente *un solo proxy* tra l'app e l'origine delle richieste.</span><span class="sxs-lookup"><span data-stu-id="390d5-396">There is only *one proxy* between the app and the source of the requests.</span></span>
* <span data-ttu-id="390d5-397">Sono configurati indirizzi di loopback solo per reti e proxy noti.</span><span class="sxs-lookup"><span data-stu-id="390d5-397">Only loopback addresses are configured for known proxies and known networks.</span></span>
* <span data-ttu-id="390d5-398">Le intestazioni inoltrate sono denominate `X-Forwarded-For` e `X-Forwarded-Proto`.</span><span class="sxs-lookup"><span data-stu-id="390d5-398">The forwarded headers are named `X-Forwarded-For` and `X-Forwarded-Proto`.</span></span>

<span data-ttu-id="390d5-399">Non tutte le appliance di rete aggiungono le intestazioni `X-Forwarded-For` e `X-Forwarded-Proto` senza alcuna configurazione aggiuntiva.</span><span class="sxs-lookup"><span data-stu-id="390d5-399">Not all network appliances add the `X-Forwarded-For` and `X-Forwarded-Proto` headers without additional configuration.</span></span> <span data-ttu-id="390d5-400">Se le richieste trasmesse tramite proxy non contengono queste intestazioni quando raggiungono l'app, fare riferimento alle indicazioni del produttore dell'appliance.</span><span class="sxs-lookup"><span data-stu-id="390d5-400">Consult your appliance manufacturer's guidance if proxied requests don't contain these headers when they reach the app.</span></span> <span data-ttu-id="390d5-401">Se l'appliance usa nomi di intestazione diversi da `X-Forwarded-For` e `X-Forwarded-Proto`, impostare le opzioni <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> e <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> in modo che corrispondano ai nomi di intestazione usati dall'appliance.</span><span class="sxs-lookup"><span data-stu-id="390d5-401">If the appliance uses different header names than `X-Forwarded-For` and `X-Forwarded-Proto`, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the appliance.</span></span> <span data-ttu-id="390d5-402">Per altre informazioni, vedere [Opzioni del middleware delle intestazioni inoltrate](#forwarded-headers-middleware-options) e [Configurazione per un proxy che usa nomi di intestazione diversi](#configuration-for-a-proxy-that-uses-different-header-names).</span><span class="sxs-lookup"><span data-stu-id="390d5-402">For more information, see [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) and [Configuration for a proxy that uses different header names](#configuration-for-a-proxy-that-uses-different-header-names).</span></span>

## <a name="iisiis-express-and-aspnet-core-module"></a><span data-ttu-id="390d5-403">IIS/IIS Express e modulo Core ASP.NET</span><span class="sxs-lookup"><span data-stu-id="390d5-403">IIS/IIS Express and ASP.NET Core Module</span></span>

<span data-ttu-id="390d5-404">Il middleware delle intestazioni inoltrate è abilitato per impostazione predefinita dal [middleware di integrazione IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) quando l'app è ospitata [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) dietro IIS e il modulo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="390d5-404">Forwarded Headers Middleware is enabled by default by [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when the app is hosted [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind IIS and the ASP.NET Core Module.</span></span> <span data-ttu-id="390d5-405">Il middleware delle intestazioni inoltrate viene attivato per l'esecuzione prima nella pipeline del middleware con una configurazione con restrizioni specifica per il modulo ASP.NET Core per motivi di attendibilità delle intestazioni inoltrate, ad esempio lo [spoofing degli indirizzi IP](https://www.iplocation.net/ip-spoofing).</span><span class="sxs-lookup"><span data-stu-id="390d5-405">Forwarded Headers Middleware is activated to run first in the middleware pipeline with a restricted configuration specific to the ASP.NET Core Module due to trust concerns with forwarded headers (for example, [IP spoofing](https://www.iplocation.net/ip-spoofing)).</span></span> <span data-ttu-id="390d5-406">Il middleware è configurato per inoltrare le intestazioni `X-Forwarded-For` e `X-Forwarded-Proto` ed è limitato a un singolo proxy localhost.</span><span class="sxs-lookup"><span data-stu-id="390d5-406">The middleware is configured to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers and is restricted to a single localhost proxy.</span></span> <span data-ttu-id="390d5-407">Se è richiesta una configurazione aggiuntiva, vedere [Opzioni del middleware delle intestazioni inoltrate](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="390d5-407">If additional configuration is required, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>

## <a name="other-proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="390d5-408">Altri scenari con server proxy e servizi di bilanciamento del carico</span><span class="sxs-lookup"><span data-stu-id="390d5-408">Other proxy server and load balancer scenarios</span></span>

<span data-ttu-id="390d5-409">Se non si usa il [middleware di integrazione IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) per l'hosting [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model), il middleware delle intestazioni inoltrate non è abilitato per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="390d5-409">Outside of using [IIS Integration](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when hosting [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model), Forwarded Headers Middleware isn't enabled by default.</span></span> <span data-ttu-id="390d5-410">Il middleware delle intestazioni inoltrate deve essere abilitato per consentire a un'app di inoltrare le intestazioni inoltrate del processo con <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>.</span><span class="sxs-lookup"><span data-stu-id="390d5-410">Forwarded Headers Middleware must be enabled for an app to process forwarded headers with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>.</span></span> <span data-ttu-id="390d5-411">Dopo aver abilitato il middleware, se non sono specificate opzioni <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> per il middleware, le intestazioni [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) predefinite sono [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="390d5-411">After enabling the middleware if no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified to the middleware, the default [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span>

<span data-ttu-id="390d5-412">Configurare il middleware con <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> per l'inoltro delle intestazioni `X-Forwarded-For` e `X-Forwarded-Proto` in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="390d5-412">Configure the middleware with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="390d5-413">Richiamare il metodo <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure` prima di chiamare altro middleware:</span><span class="sxs-lookup"><span data-stu-id="390d5-413">Invoke the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> method in `Startup.Configure` before calling other middleware:</span></span>

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
> <span data-ttu-id="390d5-414">Se non vengono specificate <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> in `Startup.ConfigureServices` o direttamente nel metodo di estensione con <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>, le intestazioni predefinite da inoltrare sono [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="390d5-414">If no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified in `Startup.ConfigureServices` or directly to the extension method with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>, the default headers to forward are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> <span data-ttu-id="390d5-415">La proprietà <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> deve essere configurata con le intestazioni da inoltrare.</span><span class="sxs-lookup"><span data-stu-id="390d5-415">The <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> property must be configured with the headers to forward.</span></span>

## <a name="nginx-configuration"></a><span data-ttu-id="390d5-416">Configurazione Nginx</span><span class="sxs-lookup"><span data-stu-id="390d5-416">Nginx configuration</span></span>

<span data-ttu-id="390d5-417">Per inoltrare le intestazioni `X-Forwarded-For` e `X-Forwarded-Proto`, vedere <xref:host-and-deploy/linux-nginx#configure-nginx>.</span><span class="sxs-lookup"><span data-stu-id="390d5-417">To forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers, see <xref:host-and-deploy/linux-nginx#configure-nginx>.</span></span> <span data-ttu-id="390d5-418">Per altre informazioni, vedere [NGINX: Using the Forwarded header](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/) (NGINX: Uso dell'intestazione Forwarded).</span><span class="sxs-lookup"><span data-stu-id="390d5-418">For more information, see [NGINX: Using the Forwarded header](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/).</span></span>

## <a name="apache-configuration"></a><span data-ttu-id="390d5-419">Configurazione Apache</span><span class="sxs-lookup"><span data-stu-id="390d5-419">Apache configuration</span></span>

<span data-ttu-id="390d5-420">`X-Forwarded-For` viene aggiunta automaticamente. Vedere [Apache Module mod_proxy: Reverse Proxy Request Headers](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers) (Modulo Apache mod_proxy: Intestazioni delle richieste del proxy inverso).</span><span class="sxs-lookup"><span data-stu-id="390d5-420">`X-Forwarded-For` is added automatically (see [Apache Module mod_proxy: Reverse Proxy Request Headers](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)).</span></span> <span data-ttu-id="390d5-421">Per informazioni su come inoltrare l'intestazione `X-Forwarded-Proto`, vedere <xref:host-and-deploy/linux-apache#configure-apache>.</span><span class="sxs-lookup"><span data-stu-id="390d5-421">For information on how to forward the `X-Forwarded-Proto` header, see <xref:host-and-deploy/linux-apache#configure-apache>.</span></span>

## <a name="forwarded-headers-middleware-options"></a><span data-ttu-id="390d5-422">Opzioni del middleware delle intestazioni inoltrate</span><span class="sxs-lookup"><span data-stu-id="390d5-422">Forwarded Headers Middleware options</span></span>

<span data-ttu-id="390d5-423"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> controllano il comportamento del middleware delle intestazioni inoltrate.</span><span class="sxs-lookup"><span data-stu-id="390d5-423"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> control the behavior of the Forwarded Headers Middleware.</span></span> <span data-ttu-id="390d5-424">L'esempio seguente modifica i valori predefiniti:</span><span class="sxs-lookup"><span data-stu-id="390d5-424">The following example changes the default values:</span></span>

* <span data-ttu-id="390d5-425">Limitare il numero di voci nelle intestazioni inoltrate a `2`.</span><span class="sxs-lookup"><span data-stu-id="390d5-425">Limit the number of entries in the forwarded headers to `2`.</span></span>
* <span data-ttu-id="390d5-426">Aggiungere un indirizzo proxy noto di `127.0.10.1`.</span><span class="sxs-lookup"><span data-stu-id="390d5-426">Add a known proxy address of `127.0.10.1`.</span></span>
* <span data-ttu-id="390d5-427">Modificare il nome dell'intestazione inoltrata dal nome predefinito `X-Forwarded-For` a `X-Forwarded-For-My-Custom-Header-Name`.</span><span class="sxs-lookup"><span data-stu-id="390d5-427">Change the forwarded header name from the default `X-Forwarded-For` to `X-Forwarded-For-My-Custom-Header-Name`.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardLimit = 2;
    options.KnownProxies.Add(IPAddress.Parse("127.0.10.1"));
    options.ForwardedForHeaderName = "X-Forwarded-For-My-Custom-Header-Name";
});
```

| <span data-ttu-id="390d5-428">Opzione</span><span class="sxs-lookup"><span data-stu-id="390d5-428">Option</span></span> | <span data-ttu-id="390d5-429">Description</span><span class="sxs-lookup"><span data-stu-id="390d5-429">Description</span></span> |
| ---
<span data-ttu-id="390d5-430">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="390d5-430">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="390d5-431">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="390d5-431">'Blazor'</span></span>
- <span data-ttu-id="390d5-432">'Identity'</span><span class="sxs-lookup"><span data-stu-id="390d5-432">'Identity'</span></span>
- <span data-ttu-id="390d5-433">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="390d5-433">'Let's Encrypt'</span></span>
- <span data-ttu-id="390d5-434">'Razor'</span><span class="sxs-lookup"><span data-stu-id="390d5-434">'Razor'</span></span>
- <span data-ttu-id="390d5-435">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="390d5-435">'SignalR' uid:</span></span> 

<span data-ttu-id="390d5-436">--- | Titolo---: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="390d5-436">--- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="390d5-437">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="390d5-437">'Blazor'</span></span>
- <span data-ttu-id="390d5-438">'Identity'</span><span class="sxs-lookup"><span data-stu-id="390d5-438">'Identity'</span></span>
- <span data-ttu-id="390d5-439">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="390d5-439">'Let's Encrypt'</span></span>
- <span data-ttu-id="390d5-440">'Razor'</span><span class="sxs-lookup"><span data-stu-id="390d5-440">'Razor'</span></span>
- <span data-ttu-id="390d5-441">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="390d5-441">'SignalR' uid:</span></span> 

-
<span data-ttu-id="390d5-442">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="390d5-442">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="390d5-443">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="390d5-443">'Blazor'</span></span>
- <span data-ttu-id="390d5-444">'Identity'</span><span class="sxs-lookup"><span data-stu-id="390d5-444">'Identity'</span></span>
- <span data-ttu-id="390d5-445">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="390d5-445">'Let's Encrypt'</span></span>
- <span data-ttu-id="390d5-446">'Razor'</span><span class="sxs-lookup"><span data-stu-id="390d5-446">'Razor'</span></span>
- <span data-ttu-id="390d5-447">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="390d5-447">'SignalR' uid:</span></span> 

-
<span data-ttu-id="390d5-448">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="390d5-448">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="390d5-449">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="390d5-449">'Blazor'</span></span>
- <span data-ttu-id="390d5-450">'Identity'</span><span class="sxs-lookup"><span data-stu-id="390d5-450">'Identity'</span></span>
- <span data-ttu-id="390d5-451">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="390d5-451">'Let's Encrypt'</span></span>
- <span data-ttu-id="390d5-452">'Razor'</span><span class="sxs-lookup"><span data-stu-id="390d5-452">'Razor'</span></span>
- <span data-ttu-id="390d5-453">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="390d5-453">'SignalR' uid:</span></span> 

<span data-ttu-id="390d5-454">------ | | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> | Limita gli host in base all' `X-Forwarded-Host` intestazione ai valori forniti.</span><span class="sxs-lookup"><span data-stu-id="390d5-454">------ | | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> | Restricts hosts by the `X-Forwarded-Host` header to the values provided.</span></span><ul><li><span data-ttu-id="390d5-455">I valori vengono confrontati tramite ordinal-ignore-case.</span><span class="sxs-lookup"><span data-stu-id="390d5-455">Values are compared using ordinal-ignore-case.</span></span></li><li><span data-ttu-id="390d5-456">I numeri di porta devono essere esclusi.</span><span class="sxs-lookup"><span data-stu-id="390d5-456">Port numbers must be excluded.</span></span></li><li><span data-ttu-id="390d5-457">Se l'elenco è vuoto, sono consentiti tutti gli host.</span><span class="sxs-lookup"><span data-stu-id="390d5-457">If the list is empty, all hosts are allowed.</span></span></li><li><span data-ttu-id="390d5-458">Un carattere jolly di primo livello `*` consente tutti gli host non vuoti.</span><span class="sxs-lookup"><span data-stu-id="390d5-458">A top-level wildcard `*` allows all non-empty hosts.</span></span></li><li><span data-ttu-id="390d5-459">I caratteri jolly per i sottodomini sono consentiti, ma non corrispondono al dominio radice.</span><span class="sxs-lookup"><span data-stu-id="390d5-459">Subdomain wildcards are permitted but don't match the root domain.</span></span> <span data-ttu-id="390d5-460">Ad esempio, `*.contoso.com` corrisponde al sottodominio `foo.contoso.com`, ma non al dominio radice `contoso.com`.</span><span class="sxs-lookup"><span data-stu-id="390d5-460">For example, `*.contoso.com` matches the subdomain `foo.contoso.com` but not the root domain `contoso.com`.</span></span></li><li><span data-ttu-id="390d5-461">I nomi host Unicode sono consentiti ma vengono convertiti in [Punycode](https://tools.ietf.org/html/rfc3492) per la corrispondenza.</span><span class="sxs-lookup"><span data-stu-id="390d5-461">Unicode host names are allowed but are converted to [Punycode](https://tools.ietf.org/html/rfc3492) for matching.</span></span></li><li><span data-ttu-id="390d5-462">Gli [indirizzi IPv6](https://tools.ietf.org/html/rfc4291) devono includere le parentesi quadre di delimitazione ed essere in [formato convenzionale](https://tools.ietf.org/html/rfc4291#section-2.2), ad esempio `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`.</span><span class="sxs-lookup"><span data-stu-id="390d5-462">[IPv6 addresses](https://tools.ietf.org/html/rfc4291) must include bounding brackets and be in [conventional form](https://tools.ietf.org/html/rfc4291#section-2.2) (for example, `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`).</span></span> <span data-ttu-id="390d5-463">Per gli indirizzi IPv6 non viene applicata la distinzione tra maiuscole e minuscole per la verifica dell'uguaglianza logica tra i diversi formati e non viene eseguita alcuna canonizzazione.</span><span class="sxs-lookup"><span data-stu-id="390d5-463">IPv6 addresses aren't special-cased to check for logical equality between different formats, and no canonicalization is performed.</span></span></li><li><span data-ttu-id="390d5-464">La mancata limitazione degli host consentiti potrebbe permettere a un utente malintenzionato di eseguire lo spoofing dei collegamenti generati dal servizio.</span><span class="sxs-lookup"><span data-stu-id="390d5-464">Failure to restrict the allowed hosts may allow an attacker to spoof links generated by the service.</span></span></li></ul><span data-ttu-id="390d5-465">Il valore predefinito è un insieme `IList<string>` vuoto.</span><span class="sxs-lookup"><span data-stu-id="390d5-465">The default value is an empty `IList<string>`.</span></span> <span data-ttu-id="390d5-466">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> | Usare l'intestazione specificata da questa proprietà anziché quella specificata da [ForwardedHeadersDefaults. XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName).</span><span class="sxs-lookup"><span data-stu-id="390d5-466">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> | Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName).</span></span> <span data-ttu-id="390d5-467">Questa opzione viene usata quando il proxy o il server d'inoltro non usa l'intestazione `X-Forwarded-For` ma usa un'altra intestazione per inoltrare le informazioni.</span><span class="sxs-lookup"><span data-stu-id="390d5-467">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-For` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="390d5-468">Il valore predefinito è `X-Forwarded-For`.</span><span class="sxs-lookup"><span data-stu-id="390d5-468">The default is `X-Forwarded-For`.</span></span> <span data-ttu-id="390d5-469">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> | Identifica i server d'esecuzione che devono essere elaborati.</span><span class="sxs-lookup"><span data-stu-id="390d5-469">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> | Identifies which forwarders should be processed.</span></span> <span data-ttu-id="390d5-470">Vedere [ForwardedHeaders Enum](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) (Enumerazione ForwardedHeaders) per l'elenco dei campi applicabili.</span><span class="sxs-lookup"><span data-stu-id="390d5-470">See the [ForwardedHeaders Enum](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) for the list of fields that apply.</span></span> <span data-ttu-id="390d5-471">I valori tipici assegnati a questa proprietà sono `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`.</span><span class="sxs-lookup"><span data-stu-id="390d5-471">Typical values assigned to this property are `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`.</span></span><br><br><span data-ttu-id="390d5-472">Il valore predefinito è [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="390d5-472">The default value is [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> <span data-ttu-id="390d5-473">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHostHeaderName> | Usare l'intestazione specificata da questa proprietà anziché quella specificata da [ForwardedHeadersDefaults. XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName).</span><span class="sxs-lookup"><span data-stu-id="390d5-473">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHostHeaderName> | Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName).</span></span> <span data-ttu-id="390d5-474">Questa opzione viene usata quando il proxy o il server d'inoltro non usa l'intestazione `X-Forwarded-Host` ma usa un'altra intestazione per inoltrare le informazioni.</span><span class="sxs-lookup"><span data-stu-id="390d5-474">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Host` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="390d5-475">Il valore predefinito è `X-Forwarded-Host`.</span><span class="sxs-lookup"><span data-stu-id="390d5-475">The default is `X-Forwarded-Host`.</span></span> <span data-ttu-id="390d5-476">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> | Usare l'intestazione specificata da questa proprietà anziché quella specificata da [ForwardedHeadersDefaults. XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName).</span><span class="sxs-lookup"><span data-stu-id="390d5-476">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> | Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName).</span></span> <span data-ttu-id="390d5-477">Questa opzione viene usata quando il proxy o il server d'inoltro non usa l'intestazione `X-Forwarded-Proto` ma usa un'altra intestazione per inoltrare le informazioni.</span><span class="sxs-lookup"><span data-stu-id="390d5-477">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Proto` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="390d5-478">Il valore predefinito è `X-Forwarded-Proto`.</span><span class="sxs-lookup"><span data-stu-id="390d5-478">The default is `X-Forwarded-Proto`.</span></span> <span data-ttu-id="390d5-479">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardLimit> | Limita il numero di voci nelle intestazioni elaborate.</span><span class="sxs-lookup"><span data-stu-id="390d5-479">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardLimit> | Limits the number of entries in the headers that are processed.</span></span> <span data-ttu-id="390d5-480">Impostare su `null` per disabilitare il limite, ma solo se è configurato `KnownProxies` o `KnownNetworks`.</span><span class="sxs-lookup"><span data-stu-id="390d5-480">Set to `null` to disable the limit, but this should only be done if `KnownProxies` or `KnownNetworks` are configured.</span></span> <span data-ttu-id="390d5-481">L'impostazione di un valore non `null` è una precauzione (ma non una garanzia) per proteggersi da proxy non configurati correttamente e richieste dannose provenienti da canali laterali in rete.</span><span class="sxs-lookup"><span data-stu-id="390d5-481">Setting a non-`null` value is a precaution (but not a guarantee) to guard against misconfigured proxies and malicious requests arriving from side-channels on the network.</span></span><br><br><span data-ttu-id="390d5-482">Il middleware delle intestazioni inoltrate elabora le intestazioni in ordine inverso da destra a sinistra.</span><span class="sxs-lookup"><span data-stu-id="390d5-482">Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="390d5-483">Se viene usato il valore predefinito (`1`), viene elaborato solo il valore più a destra delle intestazioni, a meno che non venga aumentato il valore di `ForwardLimit`.</span><span class="sxs-lookup"><span data-stu-id="390d5-483">If the default value (`1`) is used, only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span><br><br><span data-ttu-id="390d5-484">Il valore predefinito è `1`.</span><span class="sxs-lookup"><span data-stu-id="390d5-484">The default is `1`.</span></span> <span data-ttu-id="390d5-485">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks> | Intervalli di indirizzi delle reti note da cui accettare le intestazioni di inoltri.</span><span class="sxs-lookup"><span data-stu-id="390d5-485">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks> | Address ranges of known networks to accept forwarded headers from.</span></span> <span data-ttu-id="390d5-486">Specificare gli intervalli IP usando la notazione CIDR (Classless Interdomain Routing).</span><span class="sxs-lookup"><span data-stu-id="390d5-486">Provide IP ranges using Classless Interdomain Routing (CIDR) notation.</span></span><br><br><span data-ttu-id="390d5-487">Se il server usa socket dual mode, gli indirizzi IPv4 vengono forniti in un formato IPv6 (ad esempio, `10.0.0.1` in IPv4 rappresentato in IPv6 come `::ffff:10.0.0.1`).</span><span class="sxs-lookup"><span data-stu-id="390d5-487">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="390d5-488">Vedere [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="390d5-488">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="390d5-489">Determinare se questo formato è richiesto esaminando [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span><span class="sxs-lookup"><span data-stu-id="390d5-489">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="390d5-490">Per altre informazioni, vedere la sezione [Configurazione per un indirizzo IPv4 rappresentato come indirizzo IPv6](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address).</span><span class="sxs-lookup"><span data-stu-id="390d5-490">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="390d5-491">Il valore predefinito è un oggetto `IList`\<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> contenente una singola voce per `IPAddress.Loopback`.</span><span class="sxs-lookup"><span data-stu-id="390d5-491">The default is an `IList`\<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> containing a single entry for `IPAddress.Loopback`.</span></span> <span data-ttu-id="390d5-492">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies> | Indirizzi di proxy noti da cui accettare le intestazioni inoltrate.</span><span class="sxs-lookup"><span data-stu-id="390d5-492">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies> | Addresses of known proxies to accept forwarded headers from.</span></span> <span data-ttu-id="390d5-493">Usare `KnownProxies` per specificare le corrispondenze esatte degli indirizzi IP.</span><span class="sxs-lookup"><span data-stu-id="390d5-493">Use `KnownProxies` to specify exact IP address matches.</span></span><br><br><span data-ttu-id="390d5-494">Se il server usa socket dual mode, gli indirizzi IPv4 vengono forniti in un formato IPv6 (ad esempio, `10.0.0.1` in IPv4 rappresentato in IPv6 come `::ffff:10.0.0.1`).</span><span class="sxs-lookup"><span data-stu-id="390d5-494">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="390d5-495">Vedere [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="390d5-495">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="390d5-496">Determinare se questo formato è richiesto esaminando [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span><span class="sxs-lookup"><span data-stu-id="390d5-496">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="390d5-497">Per altre informazioni, vedere la sezione [Configurazione per un indirizzo IPv4 rappresentato come indirizzo IPv6](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address).</span><span class="sxs-lookup"><span data-stu-id="390d5-497">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="390d5-498">Il valore predefinito è un oggetto `IList`\<<xref:System.Net.IPAddress>> contenente una singola voce per `IPAddress.IPv6Loopback`.</span><span class="sxs-lookup"><span data-stu-id="390d5-498">The default is an `IList`\<<xref:System.Net.IPAddress>> containing a single entry for `IPAddress.IPv6Loopback`.</span></span> <span data-ttu-id="390d5-499">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalForHeaderName> | Usare l'intestazione specificata da questa proprietà anziché quella specificata da [ForwardedHeadersDefaults. XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).</span><span class="sxs-lookup"><span data-stu-id="390d5-499">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalForHeaderName> | Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).</span></span><br><br><span data-ttu-id="390d5-500">Il valore predefinito è `X-Original-For`.</span><span class="sxs-lookup"><span data-stu-id="390d5-500">The default is `X-Original-For`.</span></span> <span data-ttu-id="390d5-501">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalHostHeaderName> | Usare l'intestazione specificata da questa proprietà anziché quella specificata da [ForwardedHeadersDefaults. XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).</span><span class="sxs-lookup"><span data-stu-id="390d5-501">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalHostHeaderName> | Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).</span></span><br><br><span data-ttu-id="390d5-502">Il valore predefinito è `X-Original-Host`.</span><span class="sxs-lookup"><span data-stu-id="390d5-502">The default is `X-Original-Host`.</span></span> <span data-ttu-id="390d5-503">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalProtoHeaderName> | Usare l'intestazione specificata da questa proprietà anziché quella specificata da [ForwardedHeadersDefaults. XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).</span><span class="sxs-lookup"><span data-stu-id="390d5-503">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalProtoHeaderName> | Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).</span></span><br><br><span data-ttu-id="390d5-504">Il valore predefinito è `X-Original-Proto`.</span><span class="sxs-lookup"><span data-stu-id="390d5-504">The default is `X-Original-Proto`.</span></span> <span data-ttu-id="390d5-505">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.RequireHeaderSymmetry> | Richiedere che il numero di valori di intestazione sia sincronizzato tra [ForwardedHeadersOptions. ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) in fase di elaborazione.</span><span class="sxs-lookup"><span data-stu-id="390d5-505">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.RequireHeaderSymmetry> | Require the number of header values to be in sync between the [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) being processed.</span></span><br><br><span data-ttu-id="390d5-506">Il valore predefinito in ASP.NET Core 1.x è `true`.</span><span class="sxs-lookup"><span data-stu-id="390d5-506">The default in ASP.NET Core 1.x is `true`.</span></span> <span data-ttu-id="390d5-507">Il valore predefinito in ASP.NET Core 2.0 o versione successiva è `false`.</span><span class="sxs-lookup"><span data-stu-id="390d5-507">The default in ASP.NET Core 2.0 or later is `false`.</span></span> |

## <a name="scenarios-and-use-cases"></a><span data-ttu-id="390d5-508">Scenari e casi d'uso</span><span class="sxs-lookup"><span data-stu-id="390d5-508">Scenarios and use cases</span></span>

### <a name="when-it-isnt-possible-to-add-forwarded-headers-and-all-requests-are-secure"></a><span data-ttu-id="390d5-509">Quando non è possibile aggiungere le intestazioni inoltrate e tutte le richieste sono sicure</span><span class="sxs-lookup"><span data-stu-id="390d5-509">When it isn't possible to add forwarded headers and all requests are secure</span></span>

<span data-ttu-id="390d5-510">In alcuni casi, potrebbe non essere possibile aggiungere le intestazioni inoltrate alle richieste trasmesse tramite proxy all'app.</span><span class="sxs-lookup"><span data-stu-id="390d5-510">In some cases, it might not be possible to add forwarded headers to the requests proxied to the app.</span></span> <span data-ttu-id="390d5-511">Se il proxy impone che tutte le richieste esterne pubbliche siano HTTPS, lo schema può essere impostato manualmente in `Startup.Configure` prima di usare qualsiasi tipo di middleware:</span><span class="sxs-lookup"><span data-stu-id="390d5-511">If the proxy is enforcing that all public external requests are HTTPS, the scheme can be manually set in `Startup.Configure` before using any type of middleware:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.Scheme = "https";
    return next();
});
```

<span data-ttu-id="390d5-512">Questo codice può essere disabilitato tramite una variabile di ambiente o un'altra impostazione di configurazione in un ambiente di sviluppo o di gestione temporanea.</span><span class="sxs-lookup"><span data-stu-id="390d5-512">This code can be disabled with an environment variable or other configuration setting in a development or staging environment.</span></span>

### <a name="deal-with-path-base-and-proxies-that-change-the-request-path"></a><span data-ttu-id="390d5-513">Gestire la base del percorso e i proxy che modificano il percorso della richiesta</span><span class="sxs-lookup"><span data-stu-id="390d5-513">Deal with path base and proxies that change the request path</span></span>

<span data-ttu-id="390d5-514">Alcuni proxy passano il percorso senza modifiche, ma con un percorso di base dell'app che deve essere rimosso per consentire il corretto funzionamento del routing.</span><span class="sxs-lookup"><span data-stu-id="390d5-514">Some proxies pass the path intact but with an app base path that should be removed so that routing works properly.</span></span> <span data-ttu-id="390d5-515">Il middleware [UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) suddivide il percorso in [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) e il percorso di base dell'app in [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).</span><span class="sxs-lookup"><span data-stu-id="390d5-515">[UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) middleware splits the path into [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) and the app base path into [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).</span></span>

<span data-ttu-id="390d5-516">Se `/foo` è il percorso di base dell'app per un percorso proxy passato come `/foo/api/1`, il middleware imposta `Request.PathBase` su `/foo` e `Request.Path` su `/api/1` con il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="390d5-516">If `/foo` is the app base path for a proxy path passed as `/foo/api/1`, the middleware sets `Request.PathBase` to `/foo` and `Request.Path` to `/api/1` with the following command:</span></span>

```csharp
app.UsePathBase("/foo");
```

<span data-ttu-id="390d5-517">Il percorso originale e la base del percorso vengono riapplicati quando il middleware viene chiamato nuovamente in direzione inversa.</span><span class="sxs-lookup"><span data-stu-id="390d5-517">The original path and path base are reapplied when the middleware is called again in reverse.</span></span> <span data-ttu-id="390d5-518">Per altre informazioni dell'elaborazione degli ordini del middleware, vedere <xref:fundamentals/middleware/index>.</span><span class="sxs-lookup"><span data-stu-id="390d5-518">For more information on middleware order processing, see <xref:fundamentals/middleware/index>.</span></span>

<span data-ttu-id="390d5-519">Se il proxy taglia il percorso (ad esempio, inoltrando `/foo/api/1` a `/api/1`), correggere i reindirizzamenti e i collegamenti impostando la proprietà [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) della richiesta:</span><span class="sxs-lookup"><span data-stu-id="390d5-519">If the proxy trims the path (for example, forwarding `/foo/api/1` to `/api/1`), fix redirects and links by setting the request's [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) property:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.PathBase = new PathString("/foo");
    return next();
});
```

<span data-ttu-id="390d5-520">Se il proxy aggiunge i dati del percorso, eliminare parte del percorso per correggere i reindirizzamenti e i collegamenti usando <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> ed eseguendo l'assegnazione alla proprietà <xref:Microsoft.AspNetCore.Http.HttpRequest.Path>:</span><span class="sxs-lookup"><span data-stu-id="390d5-520">If the proxy is adding path data, discard part of the path to fix redirects and links by using <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> and assigning to the <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> property:</span></span>

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

### <a name="configuration-for-a-proxy-that-uses-different-header-names"></a><span data-ttu-id="390d5-521">Configurazione per un proxy che usa nomi di intestazione diversi</span><span class="sxs-lookup"><span data-stu-id="390d5-521">Configuration for a proxy that uses different header names</span></span>

<span data-ttu-id="390d5-522">Se il proxy non usa intestazioni denominate `X-Forwarded-For` e `X-Forwarded-Proto` per inoltrare l'indirizzo proxy o la porta e le informazioni dello scherma originali, impostare le opzioni <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> e <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> in modo che corrispondano ai nomi di intestazione usati dal proxy:</span><span class="sxs-lookup"><span data-stu-id="390d5-522">If the proxy doesn't use headers named `X-Forwarded-For` and `X-Forwarded-Proto` to forward the proxy address/port and originating scheme information, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the proxy:</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedForHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-For_Header";
    options.ForwardedProtoHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-Proto_Header";
});
```

### <a name="configuration-for-an-ipv4-address-represented-as-an-ipv6-address"></a><span data-ttu-id="390d5-523">Configurazione per un indirizzo IPv4 rappresentato come indirizzo IPv6</span><span class="sxs-lookup"><span data-stu-id="390d5-523">Configuration for an IPv4 address represented as an IPv6 address</span></span>

<span data-ttu-id="390d5-524">Se il server usa socket dual mode, gli indirizzi IPv4 vengono forniti in un formato IPv6 (ad esempio, `10.0.0.1` in IPv4 rappresentato in IPv6 come `::ffff:10.0.0.1` o `::ffff:a00:1`).</span><span class="sxs-lookup"><span data-stu-id="390d5-524">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1` or `::ffff:a00:1`).</span></span> <span data-ttu-id="390d5-525">Vedere [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="390d5-525">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="390d5-526">Determinare se questo formato è richiesto esaminando [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span><span class="sxs-lookup"><span data-stu-id="390d5-526">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span>

<span data-ttu-id="390d5-527">Nell'esempio seguente un indirizzo di rete che fornisce intestazioni inoltrate viene aggiunto all'elenco `KnownNetworks` in formato IPv6.</span><span class="sxs-lookup"><span data-stu-id="390d5-527">In the following example, a network address that supplies forwarded headers is added to the `KnownNetworks` list in IPv6 format.</span></span>

<span data-ttu-id="390d5-528">Indirizzo IPv4: `10.11.12.1/8`</span><span class="sxs-lookup"><span data-stu-id="390d5-528">IPv4 address: `10.11.12.1/8`</span></span>

<span data-ttu-id="390d5-529">Indirizzo IPv6 convertito: `::ffff:10.11.12.1`</span><span class="sxs-lookup"><span data-stu-id="390d5-529">Converted IPv6 address: `::ffff:10.11.12.1`</span></span>  
<span data-ttu-id="390d5-530">Lunghezza del prefisso convertito: 104</span><span class="sxs-lookup"><span data-stu-id="390d5-530">Converted prefix length: 104</span></span>

<span data-ttu-id="390d5-531">È anche possibile specificare l'indirizzo in formato esadecimale (`10.11.12.1` rappresentato in IPv6 come `::ffff:0a0b:0c01`).</span><span class="sxs-lookup"><span data-stu-id="390d5-531">You can also supply the address in hexadecimal format (`10.11.12.1` represented in IPv6 as `::ffff:0a0b:0c01`).</span></span> <span data-ttu-id="390d5-532">Durante la conversione di un indirizzo IPv4 in IPv6, aggiungere 96 alla lunghezza del prefisso CIDR (`8` nell'esempio) per tenere conto del prefisso IPv6 aggiuntivo `::ffff:` (8 + 96 = 104).</span><span class="sxs-lookup"><span data-stu-id="390d5-532">When converting an IPv4 address to IPv6, add 96 to the CIDR Prefix Length (`8` in the example) to account for the additional `::ffff:` IPv6 prefix (8 + 96 = 104).</span></span> 

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

## <a name="forward-the-scheme-for-linux-and-non-iis-reverse-proxies"></a><span data-ttu-id="390d5-533">Inoltrare lo schema per Linux e proxy inversi non IIS</span><span class="sxs-lookup"><span data-stu-id="390d5-533">Forward the scheme for Linux and non-IIS reverse proxies</span></span>

<span data-ttu-id="390d5-534">Con le app che chiamano <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> e <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> il sito si ritrova in un ciclo infinito se distribuito in un servizio app Linux di Azure, in una macchina virtuale Linux di Azure o dietro eventuali altri proxy inversi diversi da IIS.</span><span class="sxs-lookup"><span data-stu-id="390d5-534">Apps that call <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> and <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> put a site into an infinite loop if deployed to an Azure Linux App Service, Azure Linux virtual machine (VM), or behind any other reverse proxy besides IIS.</span></span> <span data-ttu-id="390d5-535">TLS viene terminato dal proxy inverso e Kestrel non viene a conoscenza dello schema di richiesta corretto.</span><span class="sxs-lookup"><span data-stu-id="390d5-535">TLS is terminated by the reverse proxy, and Kestrel isn't made aware of the correct request scheme.</span></span> <span data-ttu-id="390d5-536">Si verificano errori anche per OAuth e OIDC in questa configurazione perché generano reindirizzamenti non corretti.</span><span class="sxs-lookup"><span data-stu-id="390d5-536">OAuth and OIDC also fail in this configuration because they generate incorrect redirects.</span></span> <span data-ttu-id="390d5-537"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> aggiunge e configura il middleware delle intestazioni inoltrate in caso di esecuzione dietro IIS, ma non esiste alcuna configurazione automatica corrispondente per Linux (integrazione di Apache o Nginx).</span><span class="sxs-lookup"><span data-stu-id="390d5-537"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> adds and configures Forwarded Headers Middleware when running behind IIS, but there's no matching automatic configuration for Linux (Apache or Nginx integration).</span></span>

<span data-ttu-id="390d5-538">Per inoltrare lo schema dal proxy in scenari non IIS, aggiungere e configurare il middleware delle intestazioni inoltrate.</span><span class="sxs-lookup"><span data-stu-id="390d5-538">To forward the scheme from the proxy in non-IIS scenarios, add and configure Forwarded Headers Middleware.</span></span> <span data-ttu-id="390d5-539">In `Startup.ConfigureServices` usare il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="390d5-539">In `Startup.ConfigureServices`, use the following code:</span></span>

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

## <a name="troubleshoot"></a><span data-ttu-id="390d5-540">Risolvere problemi</span><span class="sxs-lookup"><span data-stu-id="390d5-540">Troubleshoot</span></span>

<span data-ttu-id="390d5-541">Quando le intestazioni non vengono inoltrate come previsto, abilitare la [registrazione](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="390d5-541">When headers aren't forwarded as expected, enable [logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="390d5-542">Se i log non forniscono informazioni sufficienti per risolvere il problema, enumerare le intestazioni delle richieste ricevute dal server.</span><span class="sxs-lookup"><span data-stu-id="390d5-542">If the logs don't provide sufficient information to troubleshoot the problem, enumerate the request headers received by the server.</span></span> <span data-ttu-id="390d5-543">Usare il middleware inline per scrivere le intestazioni di richiesta in una risposta dell'app o per registrare le intestazioni.</span><span class="sxs-lookup"><span data-stu-id="390d5-543">Use inline middleware to write request headers to an app response or log the headers.</span></span> 

<span data-ttu-id="390d5-544">Per scrivere le intestazioni nella risposta dell'app, aggiungere il middleware inline di terminale seguente immediatamente dopo la chiamata a <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="390d5-544">To write the headers to the app's response, place the following terminal inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`:</span></span>

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

<span data-ttu-id="390d5-545">È possibile scrivere nei log invece che nel corpo della risposta.</span><span class="sxs-lookup"><span data-stu-id="390d5-545">You can write to logs instead of the response body.</span></span> <span data-ttu-id="390d5-546">La scrittura nei log permette il normale funzionamento del sito durante il debug.</span><span class="sxs-lookup"><span data-stu-id="390d5-546">Writing to logs allows the site to function normally while debugging.</span></span>

<span data-ttu-id="390d5-547">Per scrivere nei log invece che nel corpo della risposta:</span><span class="sxs-lookup"><span data-stu-id="390d5-547">To write logs rather than to the response body:</span></span>

* <span data-ttu-id="390d5-548">Inserire `ILogger<Startup>` nella classe `Startup` come descritto in [Creare log in Startup](xref:fundamentals/logging/index#create-logs-in-startup).</span><span class="sxs-lookup"><span data-stu-id="390d5-548">Inject `ILogger<Startup>` into the `Startup` class as described in [Create logs in Startup](xref:fundamentals/logging/index#create-logs-in-startup).</span></span>
* <span data-ttu-id="390d5-549">Aggiungere il middleware inline seguente immediatamente dopo la chiamata a <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="390d5-549">Place the following inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`.</span></span>

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

<span data-ttu-id="390d5-550">Durante l'elaborazione, i valori di `X-Forwarded-{For|Proto|Host}` vengono spostati in `X-Original-{For|Proto|Host}`.</span><span class="sxs-lookup"><span data-stu-id="390d5-550">When processed, `X-Forwarded-{For|Proto|Host}` values are moved to `X-Original-{For|Proto|Host}`.</span></span> <span data-ttu-id="390d5-551">Se sono presenti più valori in una determinata intestazione, il middleware delle intestazioni inoltrate elabora le intestazioni in ordine inverso da destra a sinistra.</span><span class="sxs-lookup"><span data-stu-id="390d5-551">If there are multiple values in a given header, Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="390d5-552">Il valore predefinito di `ForwardLimit` è `1` (uno) e, pertanto, viene elaborato solo il valore più a destra delle intestazioni, a meno che non venga aumentato il valore di `ForwardLimit`.</span><span class="sxs-lookup"><span data-stu-id="390d5-552">The default `ForwardLimit` is `1` (one), so only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span>

<span data-ttu-id="390d5-553">L'indirizzo IP remoto originale della richiesta deve corrispondere a una voce negli elenchi `KnownProxies` o `KnownNetworks` prima dell'elaborazione delle intestazioni inoltrate.</span><span class="sxs-lookup"><span data-stu-id="390d5-553">The request's original remote IP must match an entry in the `KnownProxies` or `KnownNetworks` lists before forwarded headers are processed.</span></span> <span data-ttu-id="390d5-554">Questo riduce lo spoofing delle intestazioni, non accettando server di inoltro da proxy non attendibili.</span><span class="sxs-lookup"><span data-stu-id="390d5-554">This limits header spoofing by not accepting forwarders from untrusted proxies.</span></span> <span data-ttu-id="390d5-555">Quando viene rilevato un proxy sconosciuto, la registrazione indica l'indirizzo del proxy:</span><span class="sxs-lookup"><span data-stu-id="390d5-555">When an unknown proxy is detected, logging indicates the address of the proxy:</span></span>

```console
September 20th 2018, 15:49:44.168 Unknown proxy: 10.0.0.100:54321
```

<span data-ttu-id="390d5-556">Nell'esempio precedente, 10.0.0.100 è un server proxy.</span><span class="sxs-lookup"><span data-stu-id="390d5-556">In the preceding example, 10.0.0.100 is a proxy server.</span></span> <span data-ttu-id="390d5-557">Se il server è un proxy attendibile, aggiungere l'indirizzo IP del server a `KnownProxies` (o aggiungere una rete attendibile a `KnownNetworks`) in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="390d5-557">If the server is a trusted proxy, add the server's IP address to `KnownProxies` (or add a trusted network to `KnownNetworks`) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="390d5-558">Per altre informazioni, vedere la sezione [Opzioni del middleware delle intestazioni inoltrate](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="390d5-558">For more information, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) section.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

> [!IMPORTANT]
> <span data-ttu-id="390d5-559">Consentire solo a reti e proxy attendibili di inoltrare le intestazioni.</span><span class="sxs-lookup"><span data-stu-id="390d5-559">Only allow trusted proxies and networks to forward headers.</span></span> <span data-ttu-id="390d5-560">In caso contrario, possono verificarsi attacchi di [spoofing degli indirizzi IP](https://www.iplocation.net/ip-spoofing).</span><span class="sxs-lookup"><span data-stu-id="390d5-560">Otherwise, [IP spoofing](https://www.iplocation.net/ip-spoofing) attacks are possible.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="390d5-561">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="390d5-561">Additional resources</span></span>

* <xref:host-and-deploy/web-farm>
* [<span data-ttu-id="390d5-562">Avviso di sicurezza Microsoft CVE-2018-0787: vulnerabilità di elevazione dei privilegi di ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="390d5-562">Microsoft Security Advisory CVE-2018-0787: ASP.NET Core Elevation Of Privilege Vulnerability</span></span>](https://github.com/aspnet/Announcements/issues/295)

::: moniker-end
