---
title: Usare ASP.NET Core con HTTP/2 in IIS
author: rick-anderson
description: Informazioni su come usare le funzionalità HTTP/2 con IIS.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2020
no-loc:
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
uid: host-and-deploy/iis/protocols
ms.openlocfilehash: 1c3748eeead1b8cccdb6112150cf90fb5597b689
ms.sourcegitcommit: e519d95d17443abafba8f712ac168347b15c8b57
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/02/2020
ms.locfileid: "91654377"
---
# <a name="use-aspnet-core-with-http2-on-iis"></a>Usare ASP.NET Core con HTTP/2 in IIS

Di [Justin Kotalik](https://github.com/jkotalik)

[HTTP/2](https://httpwg.org/specs/rfc7540.html) è supportato con ASP.NET Core negli scenari di distribuzione IIS seguenti:

* Windows Server 2016 o versione successiva/Windows 10 o versione successiva
* IIS 10 o versione successiva
* Connessione TLS 1.2 o successiva
* Quando si [ospita out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model): le connessioni al server perimetrale pubbliche usano http/2, ma la connessione del proxy inverso al [Server gheppio](xref:fundamentals/servers/kestrel) USA http/1.1.

Per una distribuzione in-process quando viene stabilita una connessione HTTP/2, i [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) report `HTTP/2` . Per una distribuzione out-of-process quando viene stabilita una connessione HTTP/2, i [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) report `HTTP/1.1` .

Per altre informazioni sui modelli di hosting in-process e out-of-process, vedere <xref:host-and-deploy/aspnet-core-module>.

HTTP/2 è abilitato per impostazione predefinita per le connessioni HTTPS/TLS. Se non viene stabilita una connessione HTTP/2, la connessione esegue il fallback a HTTP/1.1. Per altre informazioni sulla configurazione di HTTP/2 con le distribuzioni IIS, vedere [HTTP/2 on IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis) (HTTP/2 in IIS).

## <a name="advanced-http2-features-to-support-grpc"></a>Funzionalità HTTP/2 avanzate per supportare gRPC

Funzionalità aggiuntive HTTP/2 in IIS supportano gRPC, incluso il supporto per i trailer di risposta e l'invio di frame di reimpostazione.

Requisiti per l'esecuzione di gRPC in IIS:

* Hosting in-process.
* Windows 10, build del sistema operativo 20300,1000 o versione successiva. Potrebbe richiedere l'uso di compilazioni di Windows Insider.
* Connessione TLS 1.2 o successiva

### <a name="trailers"></a>Trailer

[!INCLUDE[](~/includes/trailers.md)]

### <a name="reset"></a>Reset

[!INCLUDE[](~/includes/reset.md)]
