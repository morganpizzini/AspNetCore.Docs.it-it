---
title: :::no-loc(SignalR):::Risoluzione dei problemi di connessione ASP.NET Core
author: bradygaster
description: :::no-loc(SignalR):::Risoluzione dei problemi di connessione ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/08/2020
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
uid: signalr/troubleshoot
ms.openlocfilehash: f1d9761267d7c6af76c0be6abb238742f40fb016
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059611"
---
# <a name="troubleshoot-connection-errors"></a><span data-ttu-id="cc24b-103">Risolvere gli errori di connessione</span><span class="sxs-lookup"><span data-stu-id="cc24b-103">Troubleshoot connection errors</span></span>

<span data-ttu-id="cc24b-104">Questa sezione fornisce informazioni sugli errori che possono verificarsi quando si tenta di stabilire una connessione a un :::no-loc(SignalR)::: hub ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="cc24b-104">This section provides help with errors that can occur when trying to establish a connection to a ASP.NET Core :::no-loc(SignalR)::: hub.</span></span>

### <a name="response-code-404"></a><span data-ttu-id="cc24b-105">Codice di risposta 404</span><span class="sxs-lookup"><span data-stu-id="cc24b-105">Response code 404</span></span>

<span data-ttu-id="cc24b-106">Quando si usano WebSocket e `skipNegotiation = true`</span><span class="sxs-lookup"><span data-stu-id="cc24b-106">When using WebSockets and `skipNegotiation = true`</span></span>
```log
WebSocket connection to 'wss://xxx/HubName' failed: Error during WebSocket handshake: Unexpected response code: 404
```

* <span data-ttu-id="cc24b-107">Quando si usano più server senza sessioni permanenti, la connessione può essere avviata su un server e quindi passare a un altro server.</span><span class="sxs-lookup"><span data-stu-id="cc24b-107">When using multiple servers without sticky sessions, the connection can start on one server and then switch to another server.</span></span> <span data-ttu-id="cc24b-108">L'altro server non è in grado di riconoscere la connessione precedente.</span><span class="sxs-lookup"><span data-stu-id="cc24b-108">The other server is not aware of the previous connection.</span></span>
* <span data-ttu-id="cc24b-109">Verificare che il client si connetta all'endpoint corretto.</span><span class="sxs-lookup"><span data-stu-id="cc24b-109">Verify the client is connecting to the correct endpoint.</span></span> <span data-ttu-id="cc24b-110">Ad esempio, il server è ospitato in `http://127.0.0.1:5000/hub/myHub` e il client sta provando a connettersi a `http://127.0.0.1:5000/myHub` .</span><span class="sxs-lookup"><span data-stu-id="cc24b-110">For example, the server is hosted at `http://127.0.0.1:5000/hub/myHub` and client is trying to connect to `http://127.0.0.1:5000/myHub`.</span></span>
* <span data-ttu-id="cc24b-111">Se la connessione utilizza l'ID e impiega troppo tempo per inviare una richiesta al server dopo la negoziazione, il server:</span><span class="sxs-lookup"><span data-stu-id="cc24b-111">If the connection uses the ID and takes too long to send a request to the server after the negotiate, the server:</span></span>

  * <span data-ttu-id="cc24b-112">Elimina l'ID.</span><span class="sxs-lookup"><span data-stu-id="cc24b-112">Deletes the ID.</span></span>
  * <span data-ttu-id="cc24b-113">Restituisce 404.</span><span class="sxs-lookup"><span data-stu-id="cc24b-113">Returns a 404.</span></span>

### <a name="response-code-400-or-503"></a><span data-ttu-id="cc24b-114">Codice di risposta 400 o 503</span><span class="sxs-lookup"><span data-stu-id="cc24b-114">Response code 400 or 503</span></span>

<span data-ttu-id="cc24b-115">Per il seguente errore:</span><span class="sxs-lookup"><span data-stu-id="cc24b-115">For the following error:</span></span>

```log
WebSocket connection to 'wss://xxx/HubName' failed: Error during WebSocket handshake: Unexpected response code: 400

Error: Failed to start the connection: Error: There was an error with the transport.
```

<span data-ttu-id="cc24b-116">Questo errore è in genere causato da un client che usa solo il trasporto WebSocket, ma il protocollo WebSockets non è abilitato nel server.</span><span class="sxs-lookup"><span data-stu-id="cc24b-116">This error is usually caused by a client using only the WebSockets transport but the WebSockets protocol is not enabled on the server.</span></span>

### <a name="response-code-307"></a><span data-ttu-id="cc24b-117">Codice di risposta 307</span><span class="sxs-lookup"><span data-stu-id="cc24b-117">Response code 307</span></span>

<span data-ttu-id="cc24b-118">Quando si usano WebSocket e `skipNegotiation = true`</span><span class="sxs-lookup"><span data-stu-id="cc24b-118">When using WebSockets and `skipNegotiation = true`</span></span>
```log
WebSocket connection to 'ws://xxx/HubName' failed: Error during WebSocket handshake: Unexpected response code: 307
```

<span data-ttu-id="cc24b-119">Questo errore può verificarsi anche durante la richiesta di negoziazione.</span><span class="sxs-lookup"><span data-stu-id="cc24b-119">This error can also happen during the negotiate request.</span></span>

<span data-ttu-id="cc24b-120">Motivo comune:</span><span class="sxs-lookup"><span data-stu-id="cc24b-120">Common cause:</span></span>
* <span data-ttu-id="cc24b-121">L'app è configurata in modo da applicare HTTPS chiamando `UseHttpsRedirection` in `Startup` o impone HTTPS tramite una regola di riscrittura URL.</span><span class="sxs-lookup"><span data-stu-id="cc24b-121">App is configured to enforce HTTPS by calling `UseHttpsRedirection` in `Startup`, or enforces HTTPS via URL rewrite rule.</span></span>

<span data-ttu-id="cc24b-122">Possibile soluzione:</span><span class="sxs-lookup"><span data-stu-id="cc24b-122">Possible solution:</span></span>
* <span data-ttu-id="cc24b-123">Modificare l'URL sul lato client da "http" a "https".</span><span class="sxs-lookup"><span data-stu-id="cc24b-123">Change the URL on the client side from "http" to "https".</span></span> `.withUrl("https://xxx/HubName")`

### <a name="response-code-405"></a><span data-ttu-id="cc24b-124">Codice di risposta 405</span><span class="sxs-lookup"><span data-stu-id="cc24b-124">Response code 405</span></span>

<span data-ttu-id="cc24b-125">Codice di stato HTTP 405-metodo non consentito</span><span class="sxs-lookup"><span data-stu-id="cc24b-125">Http status code 405 - Method Not Allowed</span></span>

* <span data-ttu-id="cc24b-126">[CORS](xref:signalr/security#cross-origin-resource-sharing) non è abilitato per l'app</span><span class="sxs-lookup"><span data-stu-id="cc24b-126">The app doesn't have [CORS](xref:signalr/security#cross-origin-resource-sharing) enabled</span></span>

### <a name="response-code-0"></a><span data-ttu-id="cc24b-127">Codice di risposta 0</span><span class="sxs-lookup"><span data-stu-id="cc24b-127">Response code 0</span></span>

<span data-ttu-id="cc24b-128">Codice di stato HTTP 0: in genere un problema [CORS](xref:signalr/security#cross-origin-resource-sharing) , non viene fornito codice di stato</span><span class="sxs-lookup"><span data-stu-id="cc24b-128">Http status code 0 - Usually a [CORS](xref:signalr/security#cross-origin-resource-sharing) issue, no status code is given</span></span>

```log
Cross-Origin Request Blocked: The Same Origin Policy disallows reading the remote resource at http://localhost:5000/default/negotiate?negotiateVersion=1. (Reason: CORS header 'Access-Control-Allow-Origin' missing).
```

* <span data-ttu-id="cc24b-129">Aggiungere le origini previste a `.WithOrigins(...)`</span><span class="sxs-lookup"><span data-stu-id="cc24b-129">Add the expected origins to `.WithOrigins(...)`</span></span>

```log
Cross-Origin Request Blocked: The Same Origin Policy disallows reading the remote resource at http://localhost:5000/default/negotiate?negotiateVersion=1. (Reason: expected 'true' in CORS header 'Access-Control-Allow-Credentials').
```

* <span data-ttu-id="cc24b-130">Aggiungere `.AllowCredentials()` ai criteri di CORS.</span><span class="sxs-lookup"><span data-stu-id="cc24b-130">Add `.AllowCredentials()` to your CORS policy.</span></span> <span data-ttu-id="cc24b-131">Impossibile utilizzare `.AllowAnyOrigin()` o `.WithOrigins("*")` con questa opzione</span><span class="sxs-lookup"><span data-stu-id="cc24b-131">Cannot use `.AllowAnyOrigin()` or `.WithOrigins("*")` with this option</span></span>

### <a name="response-code-413"></a><span data-ttu-id="cc24b-132">Codice di risposta 413</span><span class="sxs-lookup"><span data-stu-id="cc24b-132">Response code 413</span></span>

<span data-ttu-id="cc24b-133">Codice di stato HTTP 413-payload troppo grande</span><span class="sxs-lookup"><span data-stu-id="cc24b-133">Http status code 413 - Payload Too Large</span></span>

<span data-ttu-id="cc24b-134">Questa situazione è spesso causata dalla presenza di un token di accesso superiore a 4K.</span><span class="sxs-lookup"><span data-stu-id="cc24b-134">This is often caused by having an access token that is over 4k.</span></span>

* <span data-ttu-id="cc24b-135">Se si usa il :::no-loc(SignalR)::: servizio di Azure, ridurre le dimensioni del token personalizzando le attestazioni inviate tramite il servizio con:</span><span class="sxs-lookup"><span data-stu-id="cc24b-135">If using the Azure :::no-loc(SignalR)::: Service, reduce the token size by customizing the claims being sent through the Service with:</span></span>
```csharp
.AddAzure:::no-loc(SignalR):::(options =>
{
    options.ClaimsProvider = context => context.User.Claims;
});
```

### <a name="transient-network-failures"></a><span data-ttu-id="cc24b-136">Errori di rete temporanei</span><span class="sxs-lookup"><span data-stu-id="cc24b-136">Transient network failures</span></span>

<span data-ttu-id="cc24b-137">È possibile che gli errori di rete temporanei chiudano la :::no-loc(SignalR)::: connessione.</span><span class="sxs-lookup"><span data-stu-id="cc24b-137">Transient network failures may close the :::no-loc(SignalR)::: connection.</span></span> <span data-ttu-id="cc24b-138">Il server può interpretare la connessione chiusa come una disconnessione client normale.</span><span class="sxs-lookup"><span data-stu-id="cc24b-138">The server may interpret the closed connection as a graceful client disconnect.</span></span> <span data-ttu-id="cc24b-139">Per ottenere altre informazioni sul motivo per cui un client si è disconnesso in questi casi, [raccogliere i log dal client e dal server](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="cc24b-139">To get more info on why a client disconnected in those cases [gather logs from the client and server](xref:signalr/diagnostics).</span></span>