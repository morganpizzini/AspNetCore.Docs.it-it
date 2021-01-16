---
title: Quando usare un proxy inverso con il server Web ASP.NET Core gheppio
author: rick-anderson
description: Informazioni su quando usare un proxy inverso davanti a gheppio, il server Web multipiattaforma per ASP.NET Core.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 01/14/2021
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
uid: fundamentals/servers/kestrel/when-to-use-a-reverse-proxy
ms.openlocfilehash: fc89a9f841403bbccedff0a9c0720a08c11abdd6
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253940"
---
# <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="dc542-103">Quando usare Kestrel con un proxy inverso</span><span class="sxs-lookup"><span data-stu-id="dc542-103">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="dc542-104">È possibile usare Kestrel da solo o in combinazione con un *server proxy inverso*, ad esempio [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org) o [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="dc542-104">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="dc542-105">Il server proxy inverso riceve le richieste HTTP dalla rete e le inoltra a Kestrel.</span><span class="sxs-lookup"><span data-stu-id="dc542-105">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="dc542-106">Kestrel usato come server Web perimetrale (esposto a Internet):</span><span class="sxs-lookup"><span data-stu-id="dc542-106">Kestrel used as an edge (Internet-facing) web server:</span></span>

![Kestrel comunica direttamente con Internet senza un server proxy inverso](_static/kestrel-to-internet2.png)

<span data-ttu-id="dc542-108">Kestrel usato in una configurazione proxy inverso:</span><span class="sxs-lookup"><span data-stu-id="dc542-108">Kestrel used in a reverse proxy configuration:</span></span>

![Kestrel comunica indirettamente con Internet attraverso un server proxy inverso, ad esempio IIS, Nginx o Apache](_static/kestrel-to-internet.png)

<span data-ttu-id="dc542-110">La configurazione, con o senza un server proxy inverso, è una configurazione di hosting supportata.</span><span class="sxs-lookup"><span data-stu-id="dc542-110">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="dc542-111">Quando il gheppio viene usato come server perimetrale senza un server proxy inverso, la condivisione dello stesso indirizzo IP e della stessa porta tra più processi non è supportata.</span><span class="sxs-lookup"><span data-stu-id="dc542-111">When Kestrel is used as an edge server without a reverse proxy server, sharing of the same IP address and port among multiple processes is unsupported.</span></span> <span data-ttu-id="dc542-112">Quando il gheppio è configurato per l'ascolto su una porta, gheppio gestisce tutto il traffico per tale porta indipendentemente dalle intestazioni delle richieste `Host` .</span><span class="sxs-lookup"><span data-stu-id="dc542-112">When Kestrel is configured to listen on a port, Kestrel handles all traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="dc542-113">Un proxy inverso che può condividere le porte può inviare le richieste a gheppio su un IP e una porta univoci.</span><span class="sxs-lookup"><span data-stu-id="dc542-113">A reverse proxy that can share ports can forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="dc542-114">Anche se la presenza di un server proxy inverso non è necessaria, può risultare utile per i motivi seguenti.</span><span class="sxs-lookup"><span data-stu-id="dc542-114">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="dc542-115">Un proxy inverso:</span><span class="sxs-lookup"><span data-stu-id="dc542-115">A reverse proxy:</span></span>

* <span data-ttu-id="dc542-116">Può limitare l'area della superficie di attacco pubblica esposta delle app ospitate.</span><span class="sxs-lookup"><span data-stu-id="dc542-116">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="dc542-117">Offre un livello aggiuntivo di configurazione e protezione.</span><span class="sxs-lookup"><span data-stu-id="dc542-117">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="dc542-118">Potrebbe offrire un'integrazione migliore con l'infrastruttura esistente.</span><span class="sxs-lookup"><span data-stu-id="dc542-118">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="dc542-119">Semplifica il bilanciamento del carico e la configurazione di comunicazioni protette (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="dc542-119">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="dc542-120">Solo il server proxy inverso richiede un certificato X. 509 e il server è in grado di comunicare con i server dell'app nella rete interna tramite HTTP normale.</span><span class="sxs-lookup"><span data-stu-id="dc542-120">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="dc542-121">Una configurazione che prevede un proxy inverso richiede il [filtro host](xref:fundamentals/servers/kestrel/host-filtering).</span><span class="sxs-lookup"><span data-stu-id="dc542-121">Hosting in a reverse proxy configuration requires [host filtering](xref:fundamentals/servers/kestrel/host-filtering).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="dc542-122">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="dc542-122">Additional resources</span></span>

<xref:host-and-deploy/proxy-load-balancer>

