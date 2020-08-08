---
title: Considerazioni sulla sicurezza in ASP.NET CoreSignalR
author: bradygaster
description: Informazioni su come usare l'autenticazione e l'autorizzazione in ASP.NET Core SignalR .
monikerRange: '>= aspnetcore-2.1'
ms.author: anurse
ms.custom: mvc
ms.date: 01/16/2020
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
uid: signalr/security
ms.openlocfilehash: e004899e334738f723cb98638cb31de8d314a830
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/08/2020
ms.locfileid: "88022472"
---
# <a name="security-considerations-in-aspnet-core-no-locsignalr"></a><span data-ttu-id="8a793-103">Considerazioni sulla sicurezza in ASP.NET CoreSignalR</span><span class="sxs-lookup"><span data-stu-id="8a793-103">Security considerations in ASP.NET Core SignalR</span></span>

<span data-ttu-id="8a793-104">Di [Andrew Stanton-Nurse](https://twitter.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="8a793-104">By [Andrew Stanton-Nurse](https://twitter.com/anurse)</span></span>

<span data-ttu-id="8a793-105">In questo articolo vengono fornite informazioni sulla protezione di SignalR .</span><span class="sxs-lookup"><span data-stu-id="8a793-105">This article provides information on securing SignalR.</span></span>

## <a name="cross-origin-resource-sharing"></a><span data-ttu-id="8a793-106">Condivisione di risorse tra le origini</span><span class="sxs-lookup"><span data-stu-id="8a793-106">Cross-origin resource sharing</span></span>

<span data-ttu-id="8a793-107">La [condivisione di risorse tra le origini (CORS)](https://www.w3.org/TR/cors/) può essere usata per consentire SignalR le connessioni tra le origini nel browser.</span><span class="sxs-lookup"><span data-stu-id="8a793-107">[Cross-origin resource sharing (CORS)](https://www.w3.org/TR/cors/) can be used to allow cross-origin SignalR connections in the browser.</span></span> <span data-ttu-id="8a793-108">Se il codice JavaScript è ospitato in un dominio diverso dall' SignalR app, è necessario abilitare il [middleware CORS](xref:security/cors) per consentire a JavaScript di connettersi all' SignalR app.</span><span class="sxs-lookup"><span data-stu-id="8a793-108">If JavaScript code is hosted on a different domain from the SignalR app, [CORS middleware](xref:security/cors) must be enabled to allow the JavaScript to connect to the SignalR app.</span></span> <span data-ttu-id="8a793-109">Consenti richieste tra le origini solo da domini che consideri attendibili o controlli.</span><span class="sxs-lookup"><span data-stu-id="8a793-109">Allow cross-origin requests only from domains you trust or control.</span></span> <span data-ttu-id="8a793-110">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="8a793-110">For example:</span></span>

* <span data-ttu-id="8a793-111">Il sito è ospitato in`http://www.example.com`</span><span class="sxs-lookup"><span data-stu-id="8a793-111">Your site is hosted on `http://www.example.com`</span></span>
* <span data-ttu-id="8a793-112">L' SignalR app è ospitata in`http://signalr.example.com`</span><span class="sxs-lookup"><span data-stu-id="8a793-112">Your SignalR app is hosted on `http://signalr.example.com`</span></span>

<span data-ttu-id="8a793-113">CORS deve essere configurato nell' SignalR app per consentire solo l'origine `www.example.com` .</span><span class="sxs-lookup"><span data-stu-id="8a793-113">CORS should be configured in the SignalR app to only allow the origin `www.example.com`.</span></span>

<span data-ttu-id="8a793-114">Per altre informazioni sulla configurazione di CORS, vedere [abilitare le richieste tra le origini (CORS)](xref:security/cors).</span><span class="sxs-lookup"><span data-stu-id="8a793-114">For more information on configuring CORS, see [Enable Cross-Origin Requests (CORS)](xref:security/cors).</span></span> <span data-ttu-id="8a793-115">SignalR**richiede** i seguenti criteri CORS:</span><span class="sxs-lookup"><span data-stu-id="8a793-115">SignalR **requires** the following CORS policies:</span></span>

* <span data-ttu-id="8a793-116">Consente le origini previste specifiche.</span><span class="sxs-lookup"><span data-stu-id="8a793-116">Allow the specific expected origins.</span></span> <span data-ttu-id="8a793-117">Consentire qualsiasi origine è possibile, ma **non** è sicura o consigliata.</span><span class="sxs-lookup"><span data-stu-id="8a793-117">Allowing any origin is possible but is **not** secure or recommended.</span></span>
* <span data-ttu-id="8a793-118">I metodi HTTP `GET` e `POST` devono essere consentiti.</span><span class="sxs-lookup"><span data-stu-id="8a793-118">HTTP methods `GET` and `POST` must be allowed.</span></span>
* <span data-ttu-id="8a793-119">Per il cookie corretto funzionamento delle sessioni permanenti basate su, è necessario che le credenziali siano consentite.</span><span class="sxs-lookup"><span data-stu-id="8a793-119">Credentials must be allowed in order for cookie-based sticky sessions to work correctly.</span></span> <span data-ttu-id="8a793-120">Devono essere abilitati anche quando non viene utilizzata l'autenticazione.</span><span class="sxs-lookup"><span data-stu-id="8a793-120">They must be enabled even when authentication isn't used.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="8a793-121">Tuttavia, in 5,0 è stata fornita un'opzione nel client di TypeScript per non usare le credenziali.</span><span class="sxs-lookup"><span data-stu-id="8a793-121">However, in 5.0 we have provided an option in the TypeScript client to not use credentials.</span></span>
<span data-ttu-id="8a793-122">L'opzione per non usare le credenziali deve essere usata solo quando si conosce il 100% che le credenziali come Cookie le non sono necessarie nell'app ( cookie i vengono usati dal servizio app di Azure quando si usano più server per le sessioni permanenti).</span><span class="sxs-lookup"><span data-stu-id="8a793-122">The option to not use credentials should only be used when you know 100% that credentials like Cookies are not needed in your app (cookies are used by azure app service when using multiple servers for sticky sessions).</span></span>

::: moniker-end

<span data-ttu-id="8a793-123">Ad esempio, il criterio CORS seguente consente a un SignalR client browser ospitato in `https://example.com` di accedere all' SignalR app ospitata in `https://signalr.example.com` :</span><span class="sxs-lookup"><span data-stu-id="8a793-123">For example, the following CORS policy allows a SignalR browser client hosted on `https://example.com` to access the SignalR app hosted on `https://signalr.example.com`:</span></span>

::: moniker range=">= aspnetcore-3.0"

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    // ... other middleware ...

    // Make sure the CORS middleware is ahead of SignalR.
    app.UseCors(builder =>
    {
        builder.WithOrigins("https://example.com")
            .AllowAnyHeader()
            .WithMethods("GET", "POST")
            .AllowCredentials();
    });

    // ... other middleware ...
    app.UseRouting();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<ChatHub>("/chathub");
    });

    // ... other middleware ...
}
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[Main](security/sample/Startup.cs?name=snippet1)]

::: moniker-end

## <a name="websocket-origin-restriction"></a><span data-ttu-id="8a793-124">Restrizione origine WebSocket</span><span class="sxs-lookup"><span data-stu-id="8a793-124">WebSocket Origin Restriction</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="8a793-125">La protezione fornita da CORS non si applica agli oggetti WebSocket.</span><span class="sxs-lookup"><span data-stu-id="8a793-125">The protections provided by CORS don't apply to WebSockets.</span></span> <span data-ttu-id="8a793-126">Per la restrizione Origin su WebSocket, leggere la [restrizione Origin di WebSockets](xref:fundamentals/websockets#websocket-origin-restriction).</span><span class="sxs-lookup"><span data-stu-id="8a793-126">For origin restriction on WebSockets, read [WebSockets origin restriction](xref:fundamentals/websockets#websocket-origin-restriction).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="8a793-127">La protezione fornita da CORS non si applica agli oggetti WebSocket.</span><span class="sxs-lookup"><span data-stu-id="8a793-127">The protections provided by CORS don't apply to WebSockets.</span></span> <span data-ttu-id="8a793-128">I browser **non**:</span><span class="sxs-lookup"><span data-stu-id="8a793-128">Browsers do **not**:</span></span>

* <span data-ttu-id="8a793-129">Eseguono richieste CORS preventive.</span><span class="sxs-lookup"><span data-stu-id="8a793-129">Perform CORS pre-flight requests.</span></span>
* <span data-ttu-id="8a793-130">Rispettano le restrizioni specificate nelle intestazioni `Access-Control` quando eseguono richieste WebSocket.</span><span class="sxs-lookup"><span data-stu-id="8a793-130">Respect the restrictions specified in `Access-Control` headers when making WebSocket requests.</span></span>

<span data-ttu-id="8a793-131">I browser, tuttavia, inviano l'intestazione `Origin` quando rilasciano richieste WebSocket.</span><span class="sxs-lookup"><span data-stu-id="8a793-131">However, browsers do send the `Origin` header when issuing WebSocket requests.</span></span> <span data-ttu-id="8a793-132">Le applicazioni devono essere configurate per la convalida di queste intestazioni per assicurarsi che siano consentiti solo WebSocket provenienti dalle origini previste.</span><span class="sxs-lookup"><span data-stu-id="8a793-132">Applications should be configured to validate these headers to ensure that only WebSockets coming from the expected origins are allowed.</span></span>

<span data-ttu-id="8a793-133">In ASP.NET Core 2,1 e versioni successive, è possibile ottenere la convalida dell'intestazione usando un middleware personalizzato inserito **prima `UseSignalR` e il middleware di autenticazione** in `Configure` :</span><span class="sxs-lookup"><span data-stu-id="8a793-133">In ASP.NET Core 2.1 and later, header validation can be achieved using a custom middleware placed **before `UseSignalR`, and authentication middleware** in `Configure`:</span></span>

[!code-csharp[Main](security/sample/Startup.cs?name=snippet2)]

> [!NOTE]
> <span data-ttu-id="8a793-134">L'intestazione `Origin` viene controllata dal client e, come l'intestazione `Referer`, può essere falsificata.</span><span class="sxs-lookup"><span data-stu-id="8a793-134">The `Origin` header is controlled by the client and, like the `Referer` header, can be faked.</span></span> <span data-ttu-id="8a793-135">Queste intestazioni **non** devono essere usate come meccanismo di autenticazione.</span><span class="sxs-lookup"><span data-stu-id="8a793-135">These headers should **not** be used as an authentication mechanism.</span></span>

::: moniker-end

## <a name="connectionid"></a><span data-ttu-id="8a793-136">ConnectionId</span><span class="sxs-lookup"><span data-stu-id="8a793-136">ConnectionId</span></span>

<span data-ttu-id="8a793-137">L'esposizione `ConnectionId` può causare la rappresentazione dannosa se la SignalR versione del server o del client è ASP.NET Core 2,2 o versioni precedenti.</span><span class="sxs-lookup"><span data-stu-id="8a793-137">Exposing `ConnectionId` can lead to malicious impersonation if the SignalR server or client version is ASP.NET Core 2.2 or earlier.</span></span> <span data-ttu-id="8a793-138">Se la SignalR versione del server e del client è ASP.NET Core 3,0 o versioni successive, il `ConnectionToken` anziché il `ConnectionId` deve essere mantenuto segreto.</span><span class="sxs-lookup"><span data-stu-id="8a793-138">If the SignalR server and client version are ASP.NET Core 3.0 or later, the `ConnectionToken` rather than the `ConnectionId` must be kept secret.</span></span> <span data-ttu-id="8a793-139">L'oggetto `ConnectionToken` non viene esposto in alcuna API.</span><span class="sxs-lookup"><span data-stu-id="8a793-139">The `ConnectionToken` is purposely not exposed in any API.</span></span>  <span data-ttu-id="8a793-140">Può essere difficile assicurarsi che i client meno recenti SignalR non si connettano al server, quindi anche se la SignalR versione del server è ASP.NET Core 3,0 o versione successiva, `ConnectionId` non è necessario esporre.</span><span class="sxs-lookup"><span data-stu-id="8a793-140">It can be difficult to ensure that older SignalR clients aren't connecting to the server, so even if your SignalR server version is ASP.NET Core 3.0 or later, the `ConnectionId` shouldn't be exposed.</span></span>

## <a name="access-token-logging"></a><span data-ttu-id="8a793-141">Registrazione del token di accesso</span><span class="sxs-lookup"><span data-stu-id="8a793-141">Access token logging</span></span>

<span data-ttu-id="8a793-142">Quando si usano WebSocket o eventi inviati dal server, il client browser invia il token di accesso nella stringa di query.</span><span class="sxs-lookup"><span data-stu-id="8a793-142">When using WebSockets or Server-Sent Events, the browser client sends the access token in the query string.</span></span> <span data-ttu-id="8a793-143">La ricezione del token di accesso tramite la stringa di query è in genere sicura come se si utilizzasse l' `Authorization` intestazione standard.</span><span class="sxs-lookup"><span data-stu-id="8a793-143">Receiving the access token via query string is generally secure as using the standard `Authorization` header.</span></span> <span data-ttu-id="8a793-144">Usare sempre HTTPS per garantire una connessione end-to-end sicura tra il client e il server.</span><span class="sxs-lookup"><span data-stu-id="8a793-144">Always use HTTPS to ensure a secure end-to-end connection between the client and the server.</span></span> <span data-ttu-id="8a793-145">Molti server Web registrano l'URL per ogni richiesta, inclusa la stringa di query.</span><span class="sxs-lookup"><span data-stu-id="8a793-145">Many web servers log the URL for each request, including the query string.</span></span> <span data-ttu-id="8a793-146">La registrazione degli URL può registrare il token di accesso.</span><span class="sxs-lookup"><span data-stu-id="8a793-146">Logging the URLs may log the access token.</span></span> <span data-ttu-id="8a793-147">Per impostazione predefinita, ASP.NET Core registra l'URL per ogni richiesta, che include la stringa di query.</span><span class="sxs-lookup"><span data-stu-id="8a793-147">ASP.NET Core logs the URL for each request by default, which will include the query string.</span></span> <span data-ttu-id="8a793-148">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="8a793-148">For example:</span></span>

```
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[1]
      Request starting HTTP/1.1 GET http://localhost:5000/chathub?access_token=1234
```

<span data-ttu-id="8a793-149">In caso di dubbi sulla registrazione di questi dati con i log del server, è possibile disabilitare completamente questa registrazione configurando il `Microsoft.AspNetCore.Hosting` logger sul `Warning` livello o sopra (questi messaggi vengono scritti a `Info` livello di).</span><span class="sxs-lookup"><span data-stu-id="8a793-149">If you have concerns about logging this data with your server logs, you can disable this logging entirely by configuring the `Microsoft.AspNetCore.Hosting` logger to the `Warning` level or above (these messages are written at `Info` level).</span></span> <span data-ttu-id="8a793-150">Per ulteriori informazioni, vedere [filtro dei log](xref:fundamentals/logging/index#log-filtering) per ulteriori informazioni.</span><span class="sxs-lookup"><span data-stu-id="8a793-150">For more information, see [Log Filtering](xref:fundamentals/logging/index#log-filtering) for more information.</span></span> <span data-ttu-id="8a793-151">Se si vuole comunque registrare determinate informazioni sulle richieste, è possibile [scrivere un middleware](xref:fundamentals/middleware/write) per registrare i dati necessari e filtrare il valore della `access_token` stringa di query (se presente).</span><span class="sxs-lookup"><span data-stu-id="8a793-151">If you still want to log certain request information, you can [write a middleware](xref:fundamentals/middleware/write) to log the data you require and filter out the `access_token` query string value (if present).</span></span>

## <a name="exceptions"></a><span data-ttu-id="8a793-152">Eccezioni</span><span class="sxs-lookup"><span data-stu-id="8a793-152">Exceptions</span></span>

<span data-ttu-id="8a793-153">I messaggi di eccezione vengono in genere considerati dati sensibili che non devono essere rivelati a un client.</span><span class="sxs-lookup"><span data-stu-id="8a793-153">Exception messages are generally considered sensitive data that shouldn't be revealed to a client.</span></span> <span data-ttu-id="8a793-154">Per impostazione predefinita, SignalR non invia al client i dettagli di un'eccezione generata da un metodo dell'hub.</span><span class="sxs-lookup"><span data-stu-id="8a793-154">By default, SignalR doesn't send the details of an exception thrown by a hub method to the client.</span></span> <span data-ttu-id="8a793-155">Il client riceve invece un messaggio generico che indica che si è verificato un errore.</span><span class="sxs-lookup"><span data-stu-id="8a793-155">Instead, the client receives a generic message indicating an error occurred.</span></span> <span data-ttu-id="8a793-156">Il recapito dei messaggi di eccezione al client può essere sottoposto a override (ad esempio in fase di sviluppo o test) con [EnableDetailedErrors](xref:signalr/configuration#configure-server-options).</span><span class="sxs-lookup"><span data-stu-id="8a793-156">Exception message delivery to the client can be overridden (for example in development or test) with [EnableDetailedErrors](xref:signalr/configuration#configure-server-options).</span></span> <span data-ttu-id="8a793-157">I messaggi di eccezione non devono essere esposti al client nelle app di produzione.</span><span class="sxs-lookup"><span data-stu-id="8a793-157">Exception messages should not be exposed to the client in production apps.</span></span>

## <a name="buffer-management"></a><span data-ttu-id="8a793-158">Gestione del buffer</span><span class="sxs-lookup"><span data-stu-id="8a793-158">Buffer management</span></span>

<span data-ttu-id="8a793-159">SignalRUSA i buffer per connessione per gestire i messaggi in ingresso e in uscita.</span><span class="sxs-lookup"><span data-stu-id="8a793-159">SignalR uses per-connection buffers to manage incoming and outgoing messages.</span></span> <span data-ttu-id="8a793-160">Per impostazione predefinita, SignalR i buffer vengono limitati a 32 KB.</span><span class="sxs-lookup"><span data-stu-id="8a793-160">By default, SignalR limits these buffers to 32 KB.</span></span> <span data-ttu-id="8a793-161">Il messaggio più grande che può essere inviato da un client o da un server è 32 KB.</span><span class="sxs-lookup"><span data-stu-id="8a793-161">The largest message a client or server can send is 32 KB.</span></span> <span data-ttu-id="8a793-162">La quantità massima di memoria utilizzata da una connessione per i messaggi è 32 KB.</span><span class="sxs-lookup"><span data-stu-id="8a793-162">The maximum memory consumed by a connection for messages is 32 KB.</span></span> <span data-ttu-id="8a793-163">Se i messaggi sono sempre minori di 32 KB, è possibile ridurre il limite, che:</span><span class="sxs-lookup"><span data-stu-id="8a793-163">If your messages are always smaller than 32 KB, you can reduce the limit, which:</span></span>

* <span data-ttu-id="8a793-164">Impedisce a un client di inviare un messaggio di dimensioni maggiori.</span><span class="sxs-lookup"><span data-stu-id="8a793-164">Prevents a client from being able to send a larger message.</span></span>
* <span data-ttu-id="8a793-165">Il server non dovrà mai allocare buffer di grandi dimensioni per accettare messaggi.</span><span class="sxs-lookup"><span data-stu-id="8a793-165">The server will never need to allocate large buffers to accept messages.</span></span>

<span data-ttu-id="8a793-166">Se i messaggi sono maggiori di 32 KB, è possibile aumentare il limite.</span><span class="sxs-lookup"><span data-stu-id="8a793-166">If your messages are larger than 32 KB, you can increase the limit.</span></span> <span data-ttu-id="8a793-167">L'aumento di questo limite significa:</span><span class="sxs-lookup"><span data-stu-id="8a793-167">Increasing this limit means:</span></span>

* <span data-ttu-id="8a793-168">Il client può causare l'allocazione di buffer di memoria di grandi dimensioni da parte del server.</span><span class="sxs-lookup"><span data-stu-id="8a793-168">The client can cause the server to allocate large memory buffers.</span></span>
* <span data-ttu-id="8a793-169">L'allocazione dei server di buffer di grandi dimensioni può ridurre il numero di connessioni simultanee.</span><span class="sxs-lookup"><span data-stu-id="8a793-169">Server allocation of large buffers may reduce the number of concurrent connections.</span></span>

<span data-ttu-id="8a793-170">Esistono limiti per i messaggi in ingresso e in uscita, entrambi possono essere configurati nell'oggetto [HttpConnectionDispatcherOptions](xref:signalr/configuration#configure-server-options) configurato in `MapHub` :</span><span class="sxs-lookup"><span data-stu-id="8a793-170">There are limits for incoming and outgoing messages, both can be configured on the [HttpConnectionDispatcherOptions](xref:signalr/configuration#configure-server-options) object configured in `MapHub`:</span></span>

* <span data-ttu-id="8a793-171">`ApplicationMaxBufferSize`rappresenta il numero massimo di byte dal client che il server memorizza nel buffer.</span><span class="sxs-lookup"><span data-stu-id="8a793-171">`ApplicationMaxBufferSize` represents the maximum number of bytes from the client that the server buffers.</span></span> <span data-ttu-id="8a793-172">Se il client tenta di inviare un messaggio di dimensioni superiori a questo limite, è possibile che la connessione venga chiusa.</span><span class="sxs-lookup"><span data-stu-id="8a793-172">If the client attempts to send a message larger than this limit, the connection may be closed.</span></span>
* <span data-ttu-id="8a793-173">`TransportMaxBufferSize`rappresenta il numero massimo di byte che il server può inviare.</span><span class="sxs-lookup"><span data-stu-id="8a793-173">`TransportMaxBufferSize` represents the maximum number of bytes the server can send.</span></span> <span data-ttu-id="8a793-174">Se il server tenta di inviare un messaggio (compresi i valori restituiti dai metodi dell'hub) maggiore di questo limite, verrà generata un'eccezione.</span><span class="sxs-lookup"><span data-stu-id="8a793-174">If the server attempts to send a message (including return values from hub methods) larger than this limit, an exception will be thrown.</span></span>

<span data-ttu-id="8a793-175">Impostando il limite su `0` , il limite viene disabilitato.</span><span class="sxs-lookup"><span data-stu-id="8a793-175">Setting the limit to `0` disables the limit.</span></span> <span data-ttu-id="8a793-176">La rimozione del limite consente a un client di inviare un messaggio di qualsiasi dimensione.</span><span class="sxs-lookup"><span data-stu-id="8a793-176">Removing the limit allows a client to send a message of any size.</span></span> <span data-ttu-id="8a793-177">I client malintenzionati che inviano messaggi di grandi dimensioni possono causare l'allocazione di memoria.</span><span class="sxs-lookup"><span data-stu-id="8a793-177">Malicious clients sending large messages can cause excess memory to be allocated.</span></span> <span data-ttu-id="8a793-178">Un utilizzo eccessivo della memoria può ridurre significativamente il numero di connessioni simultanee.</span><span class="sxs-lookup"><span data-stu-id="8a793-178">Excess memory usage can significantly reduce the number of concurrent connections.</span></span>
