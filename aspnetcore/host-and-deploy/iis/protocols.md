---
title: Usare ASP.NET Core con HTTP/2 in IIS
author: rick-anderson
description: Informazioni su come usare le funzionalità HTTP/2 con IIS.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2020
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
uid: host-and-deploy/iis/protocols
ms.openlocfilehash: 4d0dc1239467e92c4127f631709c2ffd6098bfc5
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/04/2021
ms.locfileid: "93057414"
---
# <a name="use-aspnet-core-with-http2-on-iis"></a><span data-ttu-id="378e3-103">Usare ASP.NET Core con HTTP/2 in IIS</span><span class="sxs-lookup"><span data-stu-id="378e3-103">Use ASP.NET Core with HTTP/2 on IIS</span></span>

<span data-ttu-id="378e3-104">Di [Justin Kotalik](https://github.com/jkotalik)</span><span class="sxs-lookup"><span data-stu-id="378e3-104">By [Justin Kotalik](https://github.com/jkotalik)</span></span>

<span data-ttu-id="378e3-105">[HTTP/2](https://httpwg.org/specs/rfc7540.html) è supportato con ASP.NET Core negli scenari di distribuzione IIS seguenti:</span><span class="sxs-lookup"><span data-stu-id="378e3-105">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is supported with ASP.NET Core in the following IIS deployment scenarios:</span></span>

* <span data-ttu-id="378e3-106">Windows Server 2016 o versione successiva/Windows 10 o versione successiva</span><span class="sxs-lookup"><span data-stu-id="378e3-106">Windows Server 2016 or later / Windows 10 or later</span></span>
* <span data-ttu-id="378e3-107">IIS 10 o versione successiva</span><span class="sxs-lookup"><span data-stu-id="378e3-107">IIS 10 or later</span></span>
* <span data-ttu-id="378e3-108">Connessione TLS 1.2 o successiva</span><span class="sxs-lookup"><span data-stu-id="378e3-108">TLS 1.2 or later connection</span></span>
* <span data-ttu-id="378e3-109">Quando si [ospita out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model): le connessioni al server perimetrale pubbliche usano http/2, ma la connessione del proxy inverso al [Server gheppio](xref:fundamentals/servers/kestrel) USA http/1.1.</span><span class="sxs-lookup"><span data-stu-id="378e3-109">When [hosting out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model): Public-facing edge server connections use HTTP/2, but the reverse proxy connection to the [Kestrel server](xref:fundamentals/servers/kestrel) uses HTTP/1.1.</span></span>

<span data-ttu-id="378e3-110">Per una distribuzione in-process quando viene stabilita una connessione HTTP/2, i [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) report `HTTP/2` .</span><span class="sxs-lookup"><span data-stu-id="378e3-110">For an in-process deployment when an HTTP/2 connection is established, [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span> <span data-ttu-id="378e3-111">Per una distribuzione out-of-process quando viene stabilita una connessione HTTP/2, i [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) report `HTTP/1.1` .</span><span class="sxs-lookup"><span data-stu-id="378e3-111">For an out-of-process deployment when an HTTP/2 connection is established, [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/1.1`.</span></span>

<span data-ttu-id="378e3-112">Per altre informazioni sui modelli di hosting in-process e out-of-process, vedere <xref:host-and-deploy/aspnet-core-module>.</span><span class="sxs-lookup"><span data-stu-id="378e3-112">For more information on the in-process and out-of-process hosting models, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="378e3-113">HTTP/2 è abilitato per impostazione predefinita per le connessioni HTTPS/TLS.</span><span class="sxs-lookup"><span data-stu-id="378e3-113">HTTP/2 is enabled by default for HTTPS/TLS connections.</span></span> <span data-ttu-id="378e3-114">Se non viene stabilita una connessione HTTP/2, la connessione esegue il fallback a HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="378e3-114">Connections fall back to HTTP/1.1 if an HTTP/2 connection isn't established.</span></span> <span data-ttu-id="378e3-115">Per altre informazioni sulla configurazione di HTTP/2 con le distribuzioni IIS, vedere [HTTP/2 on IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis) (HTTP/2 in IIS).</span><span class="sxs-lookup"><span data-stu-id="378e3-115">For more information on HTTP/2 configuration with IIS deployments, see [HTTP/2 on IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).</span></span>

## <a name="advanced-http2-features-to-support-grpc"></a><span data-ttu-id="378e3-116">Funzionalità HTTP/2 avanzate per supportare gRPC</span><span class="sxs-lookup"><span data-stu-id="378e3-116">Advanced HTTP/2 features to support gRPC</span></span>

<span data-ttu-id="378e3-117">Funzionalità aggiuntive HTTP/2 in IIS supportano gRPC, incluso il supporto per i trailer di risposta e l'invio di frame di reimpostazione.</span><span class="sxs-lookup"><span data-stu-id="378e3-117">Additional HTTP/2 features in IIS support gRPC, including support for response trailers and sending reset frames.</span></span>

<span data-ttu-id="378e3-118">Requisiti per l'esecuzione di gRPC in IIS:</span><span class="sxs-lookup"><span data-stu-id="378e3-118">Requirements to run gRPC on IIS:</span></span>

* <span data-ttu-id="378e3-119">Hosting in-process.</span><span class="sxs-lookup"><span data-stu-id="378e3-119">In-process hosting.</span></span>
* <span data-ttu-id="378e3-120">Windows 10, build del sistema operativo 20300,1000 o versione successiva.</span><span class="sxs-lookup"><span data-stu-id="378e3-120">Windows 10, OS Build 20300.1000 or later.</span></span> <span data-ttu-id="378e3-121">Potrebbe richiedere l'uso di compilazioni di Windows Insider.</span><span class="sxs-lookup"><span data-stu-id="378e3-121">May require use of Windows Insider Builds.</span></span>
* <span data-ttu-id="378e3-122">Connessione TLS 1.2 o successiva</span><span class="sxs-lookup"><span data-stu-id="378e3-122">TLS 1.2 or later connection</span></span>

### <a name="trailers"></a><span data-ttu-id="378e3-123">Trailer</span><span class="sxs-lookup"><span data-stu-id="378e3-123">Trailers</span></span>

[!INCLUDE[](~/includes/trailers.md)]

### <a name="reset"></a><span data-ttu-id="378e3-124">Reset</span><span class="sxs-lookup"><span data-stu-id="378e3-124">Reset</span></span>

[!INCLUDE[](~/includes/reset.md)]
