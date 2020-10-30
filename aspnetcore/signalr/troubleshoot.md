---
title: SignalRRisoluzione dei problemi di connessione ASP.NET Core
author: bradygaster
description: SignalRRisoluzione dei problemi di connessione ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/08/2020
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/troubleshoot
ms.openlocfilehash: f1d9761267d7c6af76c0be6abb238742f40fb016
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059611"
---
# <a name="troubleshoot-connection-errors"></a>Risolvere gli errori di connessione

Questa sezione fornisce informazioni sugli errori che possono verificarsi quando si tenta di stabilire una connessione a un SignalR hub ASP.NET Core.

### <a name="response-code-404"></a>Codice di risposta 404

Quando si usano WebSocket e `skipNegotiation = true`
```log
WebSocket connection to 'wss://xxx/HubName' failed: Error during WebSocket handshake: Unexpected response code: 404
```

* Quando si usano più server senza sessioni permanenti, la connessione può essere avviata su un server e quindi passare a un altro server. L'altro server non è in grado di riconoscere la connessione precedente.
* Verificare che il client si connetta all'endpoint corretto. Ad esempio, il server è ospitato in `http://127.0.0.1:5000/hub/myHub` e il client sta provando a connettersi a `http://127.0.0.1:5000/myHub` .
* Se la connessione utilizza l'ID e impiega troppo tempo per inviare una richiesta al server dopo la negoziazione, il server:

  * Elimina l'ID.
  * Restituisce 404.

### <a name="response-code-400-or-503"></a>Codice di risposta 400 o 503

Per il seguente errore:

```log
WebSocket connection to 'wss://xxx/HubName' failed: Error during WebSocket handshake: Unexpected response code: 400

Error: Failed to start the connection: Error: There was an error with the transport.
```

Questo errore è in genere causato da un client che usa solo il trasporto WebSocket, ma il protocollo WebSockets non è abilitato nel server.

### <a name="response-code-307"></a>Codice di risposta 307

Quando si usano WebSocket e `skipNegotiation = true`
```log
WebSocket connection to 'ws://xxx/HubName' failed: Error during WebSocket handshake: Unexpected response code: 307
```

Questo errore può verificarsi anche durante la richiesta di negoziazione.

Motivo comune:
* L'app è configurata in modo da applicare HTTPS chiamando `UseHttpsRedirection` in `Startup` o impone HTTPS tramite una regola di riscrittura URL.

Possibile soluzione:
* Modificare l'URL sul lato client da "http" a "https". `.withUrl("https://xxx/HubName")`

### <a name="response-code-405"></a>Codice di risposta 405

Codice di stato HTTP 405-metodo non consentito

* [CORS](xref:signalr/security#cross-origin-resource-sharing) non è abilitato per l'app

### <a name="response-code-0"></a>Codice di risposta 0

Codice di stato HTTP 0: in genere un problema [CORS](xref:signalr/security#cross-origin-resource-sharing) , non viene fornito codice di stato

```log
Cross-Origin Request Blocked: The Same Origin Policy disallows reading the remote resource at http://localhost:5000/default/negotiate?negotiateVersion=1. (Reason: CORS header 'Access-Control-Allow-Origin' missing).
```

* Aggiungere le origini previste a `.WithOrigins(...)`

```log
Cross-Origin Request Blocked: The Same Origin Policy disallows reading the remote resource at http://localhost:5000/default/negotiate?negotiateVersion=1. (Reason: expected 'true' in CORS header 'Access-Control-Allow-Credentials').
```

* Aggiungere `.AllowCredentials()` ai criteri di CORS. Impossibile utilizzare `.AllowAnyOrigin()` o `.WithOrigins("*")` con questa opzione

### <a name="response-code-413"></a>Codice di risposta 413

Codice di stato HTTP 413-payload troppo grande

Questa situazione è spesso causata dalla presenza di un token di accesso superiore a 4K.

* Se si usa il SignalR servizio di Azure, ridurre le dimensioni del token personalizzando le attestazioni inviate tramite il servizio con:
```csharp
.AddAzureSignalR(options =>
{
    options.ClaimsProvider = context => context.User.Claims;
});
```

### <a name="transient-network-failures"></a>Errori di rete temporanei

È possibile che gli errori di rete temporanei chiudano la SignalR connessione. Il server può interpretare la connessione chiusa come una disconnessione client normale. Per ottenere altre informazioni sul motivo per cui un client si è disconnesso in questi casi, [raccogliere i log dal client e dal server](xref:signalr/diagnostics).