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
# <a name="use-http2-with-the-aspnet-core-kestrel-web-server"></a><span data-ttu-id="8edfd-103">Usare HTTP/2 con il server Web ASP.NET Core gheppio</span><span class="sxs-lookup"><span data-stu-id="8edfd-103">Use HTTP/2 with the ASP.NET Core Kestrel web server</span></span>

<span data-ttu-id="8edfd-104">[HTTP/2](https://httpwg.org/specs/rfc7540.html) è disponibile per le app ASP.NET Core se vengono soddisfatti i requisiti di base seguenti:</span><span class="sxs-lookup"><span data-stu-id="8edfd-104">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is available for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="8edfd-105">Sistema operativo&dagger;</span><span class="sxs-lookup"><span data-stu-id="8edfd-105">Operating system&dagger;</span></span>
  * <span data-ttu-id="8edfd-106">Windows Server 2016/Windows 10 o versioni successive&Dagger;</span><span class="sxs-lookup"><span data-stu-id="8edfd-106">Windows Server 2016/Windows 10 or later&Dagger;</span></span>
  * <span data-ttu-id="8edfd-107">Linux con OpenSSL 1.0.2 o versioni successive (ad esempio, Ubuntu 16.04 o versioni successive)</span><span class="sxs-lookup"><span data-stu-id="8edfd-107">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
* <span data-ttu-id="8edfd-108">Framework di destinazione: .NET Core 2.2 o versioni successive</span><span class="sxs-lookup"><span data-stu-id="8edfd-108">Target framework: .NET Core 2.2 or later</span></span>
* <span data-ttu-id="8edfd-109">Connessione [ALPN (Application-Layer Protocol Negotiation)](https://tools.ietf.org/html/rfc7301#section-3)</span><span class="sxs-lookup"><span data-stu-id="8edfd-109">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="8edfd-110">Connessione TLS 1.2 o successiva</span><span class="sxs-lookup"><span data-stu-id="8edfd-110">TLS 1.2 or later connection</span></span>

<span data-ttu-id="8edfd-111">&dagger;HTTP/2 verrà supportato in macOS in una versione futura.</span><span class="sxs-lookup"><span data-stu-id="8edfd-111">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>
<span data-ttu-id="8edfd-112">&Dagger;Kestrel ha un supporto limitato per HTTP/2 in Windows Server 2012 R2 e Windows 8.1.</span><span class="sxs-lookup"><span data-stu-id="8edfd-112">&Dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="8edfd-113">Il supporto è limitato perché l'elenco di suite di crittografia TLS supportate disponibili in questi sistemi operativi è limitato.</span><span class="sxs-lookup"><span data-stu-id="8edfd-113">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="8edfd-114">Un certificato generato con un algoritmo ECDSA potrebbe essere necessario per proteggere le connessioni TLS.</span><span class="sxs-lookup"><span data-stu-id="8edfd-114">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

<span data-ttu-id="8edfd-115">Se viene stabilita una connessione HTTP/2, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol%2A) corrisponde a `HTTP/2`.</span><span class="sxs-lookup"><span data-stu-id="8edfd-115">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol%2A) reports `HTTP/2`.</span></span>

<span data-ttu-id="8edfd-116">A partire da .NET Core 3,0, HTTP/2 è abilitato per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="8edfd-116">Starting with .NET Core 3.0, HTTP/2 is enabled by default.</span></span> <span data-ttu-id="8edfd-117">Per ulteriori informazioni sulla configurazione, vedere la sezione relativa ai [limiti di Gheppio http/2](xref:fundamentals/servers/kestrel/options#http2-limits) e [ListenOptions. Protocols](xref:fundamentals/servers/kestrel/endpoints#listenoptionsprotocols) .</span><span class="sxs-lookup"><span data-stu-id="8edfd-117">For more information on configuration, see the [Kestrel HTTP/2 limits](xref:fundamentals/servers/kestrel/options#http2-limits) and [ListenOptions.Protocols](xref:fundamentals/servers/kestrel/endpoints#listenoptionsprotocols) sections.</span></span>

## <a name="advanced-http2-features"></a><span data-ttu-id="8edfd-118">Funzionalità HTTP/2 avanzate</span><span class="sxs-lookup"><span data-stu-id="8edfd-118">Advanced HTTP/2 features</span></span>

<span data-ttu-id="8edfd-119">Funzionalità HTTP/2 aggiuntive in gheppio supportano gRPC, incluso il supporto per i trailer di risposta e l'invio di frame di reimpostazione.</span><span class="sxs-lookup"><span data-stu-id="8edfd-119">Additional HTTP/2 features in Kestrel support gRPC, including support for response trailers and sending reset frames.</span></span>

### <a name="trailers"></a><span data-ttu-id="8edfd-120">Trailer</span><span class="sxs-lookup"><span data-stu-id="8edfd-120">Trailers</span></span>

[!INCLUDE[](~/includes/trailers.md)]

### <a name="reset"></a><span data-ttu-id="8edfd-121">Reset</span><span class="sxs-lookup"><span data-stu-id="8edfd-121">Reset</span></span>

[!INCLUDE[](~/includes/reset.md)]
