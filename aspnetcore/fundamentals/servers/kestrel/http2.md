---
title: Usare HTTP/2 con il server Web ASP.NET Core gheppio
author: rick-anderson
description: Informazioni sull'uso di HTTP/2 con gheppio, il server Web multipiattaforma per ASP.NET Core.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 05/04/2020
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
uid: fundamentals/servers/kestrel/http2
ms.openlocfilehash: 431459bb6ece1d054146558ef865e44845a22686
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253984"
---
# <a name="use-http2-with-the-aspnet-core-kestrel-web-server"></a>Usare HTTP/2 con il server Web ASP.NET Core gheppio

[HTTP/2](https://httpwg.org/specs/rfc7540.html) è disponibile per le app ASP.NET Core se vengono soddisfatti i requisiti di base seguenti:

* Sistema operativo&dagger;
  * Windows Server 2016/Windows 10 o versioni successive&Dagger;
  * Linux con OpenSSL 1.0.2 o versioni successive (ad esempio, Ubuntu 16.04 o versioni successive)
* Framework di destinazione: .NET Core 2.2 o versioni successive
* Connessione [ALPN (Application-Layer Protocol Negotiation)](https://tools.ietf.org/html/rfc7301#section-3)
* Connessione TLS 1.2 o successiva

&dagger;HTTP/2 verrà supportato in macOS in una versione futura.
&Dagger;Kestrel ha un supporto limitato per HTTP/2 in Windows Server 2012 R2 e Windows 8.1. Il supporto è limitato perché l'elenco di suite di crittografia TLS supportate disponibili in questi sistemi operativi è limitato. Un certificato generato con un algoritmo ECDSA potrebbe essere necessario per proteggere le connessioni TLS.

Se viene stabilita una connessione HTTP/2, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol%2A) corrisponde a `HTTP/2`.

A partire da .NET Core 3,0, HTTP/2 è abilitato per impostazione predefinita. Per ulteriori informazioni sulla configurazione, vedere la sezione relativa ai [limiti di Gheppio http/2](xref:fundamentals/servers/kestrel/options#http2-limits) e [ListenOptions. Protocols](xref:fundamentals/servers/kestrel/endpoints#listenoptionsprotocols) .

## <a name="advanced-http2-features"></a>Funzionalità HTTP/2 avanzate

Funzionalità HTTP/2 aggiuntive in gheppio supportano gRPC, incluso il supporto per i trailer di risposta e l'invio di frame di reimpostazione.

### <a name="trailers"></a>Trailer

[!INCLUDE[](~/includes/trailers.md)]

### <a name="reset"></a>Reset

[!INCLUDE[](~/includes/reset.md)]
