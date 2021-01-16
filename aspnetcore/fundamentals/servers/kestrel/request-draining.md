---
title: Svuotamento delle richieste con il server Web ASP.NET Core gheppio
author: rick-anderson
description: Informazioni sullo svuotamento delle richieste con gheppio, il server Web multipiattaforma per ASP.NET Core.
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
uid: fundamentals/servers/kestrel/request-draining
ms.openlocfilehash: 41d517dae939ad0a83a3402e72eefc4e9db7b32e
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253951"
---
# <a name="request-draining-with-aspnet-core-kestrel-web-server"></a><span data-ttu-id="8f10f-103">Svuotamento delle richieste con il server Web ASP.NET Core gheppio</span><span class="sxs-lookup"><span data-stu-id="8f10f-103">Request draining with ASP.NET Core Kestrel web server</span></span>

<span data-ttu-id="8f10f-104">L'apertura di connessioni HTTP richiede molto tempo.</span><span class="sxs-lookup"><span data-stu-id="8f10f-104">Opening HTTP connections is time consuming.</span></span> <span data-ttu-id="8f10f-105">Per HTTPS, è anche a elevato utilizzo di risorse.</span><span class="sxs-lookup"><span data-stu-id="8f10f-105">For HTTPS, it's also resource intensive.</span></span> <span data-ttu-id="8f10f-106">Pertanto, gheppio tenta di riutilizzare le connessioni per il protocollo HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="8f10f-106">Therefore, Kestrel tries to reuse connections per the HTTP/1.1 protocol.</span></span> <span data-ttu-id="8f10f-107">Il corpo della richiesta deve essere completamente utilizzato per consentire il riutilizzo della connessione.</span><span class="sxs-lookup"><span data-stu-id="8f10f-107">A request body must be fully consumed to allow the connection to be reused.</span></span> <span data-ttu-id="8f10f-108">L'app non usa sempre il corpo della richiesta, ad esempio le richieste HTTP POST in cui il server restituisce una risposta di reindirizzamento o 404.</span><span class="sxs-lookup"><span data-stu-id="8f10f-108">The app doesn't always consume the request body, such as HTTP POST requests where the server returns a redirect or 404 response.</span></span> <span data-ttu-id="8f10f-109">Nel caso del reindirizzamento HTTP POST:</span><span class="sxs-lookup"><span data-stu-id="8f10f-109">In the HTTP POST redirect case:</span></span>

* <span data-ttu-id="8f10f-110">Il client potrebbe avere già inviato parte dei dati POST.</span><span class="sxs-lookup"><span data-stu-id="8f10f-110">The client may already have sent part of the POST data.</span></span>
* <span data-ttu-id="8f10f-111">Il server scrive la risposta 301.</span><span class="sxs-lookup"><span data-stu-id="8f10f-111">The server writes the 301 response.</span></span>
* <span data-ttu-id="8f10f-112">Non è possibile usare la connessione per una nuova richiesta fino a quando i dati POST del corpo della richiesta precedente non sono stati completamente letti.</span><span class="sxs-lookup"><span data-stu-id="8f10f-112">The connection can't be used for a new request until the POST data from the previous request body has been fully read.</span></span>
* <span data-ttu-id="8f10f-113">Gheppio tenta di svuotare il corpo della richiesta.</span><span class="sxs-lookup"><span data-stu-id="8f10f-113">Kestrel tries to drain the request body.</span></span> <span data-ttu-id="8f10f-114">Lo svuotamento del corpo della richiesta implica la lettura e l'eliminazione dei dati senza elaborarli.</span><span class="sxs-lookup"><span data-stu-id="8f10f-114">Draining the request body means reading and discarding the data without processing it.</span></span>

<span data-ttu-id="8f10f-115">Il processo di svuotamento costituisce un compromesso tra consentire il riutilizzo della connessione e il tempo necessario per svuotare i dati rimanenti:</span><span class="sxs-lookup"><span data-stu-id="8f10f-115">The draining process makes a tradeoff between allowing the connection to be reused and the time it takes to drain any remaining data:</span></span>

* <span data-ttu-id="8f10f-116">Lo svuotamento ha un timeout di cinque secondi, che non è configurabile.</span><span class="sxs-lookup"><span data-stu-id="8f10f-116">Draining has a timeout of five seconds, which isn't configurable.</span></span>
* <span data-ttu-id="8f10f-117">Se tutti i dati specificati dall' `Content-Length` `Transfer-Encoding` intestazione o non sono stati letti prima del timeout, la connessione viene chiusa.</span><span class="sxs-lookup"><span data-stu-id="8f10f-117">If all of the data specified by the `Content-Length` or `Transfer-Encoding` header hasn't been read before the timeout, the connection is closed.</span></span>

<span data-ttu-id="8f10f-118">In alcuni casi può essere necessario terminare immediatamente la richiesta, prima o dopo la scrittura della risposta.</span><span class="sxs-lookup"><span data-stu-id="8f10f-118">Sometimes you may want to terminate the request immediately, before or after writing the response.</span></span> <span data-ttu-id="8f10f-119">È ad esempio possibile che i client dispongano di limiti di dati restrittivi.</span><span class="sxs-lookup"><span data-stu-id="8f10f-119">For example, clients may have restrictive data caps.</span></span> <span data-ttu-id="8f10f-120">La limitazione dei dati caricati potrebbe avere una priorità.</span><span class="sxs-lookup"><span data-stu-id="8f10f-120">Limiting uploaded data might be a priority.</span></span> <span data-ttu-id="8f10f-121">In questi casi, per terminare una richiesta, chiamare [HttpContext. Abort](xref:Microsoft.AspNetCore.Http.HttpContext.Abort%2A) da un controller, una Razor pagina o un middleware.</span><span class="sxs-lookup"><span data-stu-id="8f10f-121">In such cases to terminate a request, call [HttpContext.Abort](xref:Microsoft.AspNetCore.Http.HttpContext.Abort%2A) from a controller, Razor Page, or middleware.</span></span>

<span data-ttu-id="8f10f-122">Ci sono alcune avvertenze per chiamare `Abort` :</span><span class="sxs-lookup"><span data-stu-id="8f10f-122">There are caveats to calling `Abort`:</span></span>

* <span data-ttu-id="8f10f-123">La creazione di nuove connessioni può essere lenta e dispendiosa.</span><span class="sxs-lookup"><span data-stu-id="8f10f-123">Creating new connections can be slow and expensive.</span></span>
* <span data-ttu-id="8f10f-124">Non vi è alcuna garanzia che il client abbia letto la risposta prima che la connessione venga chiusa.</span><span class="sxs-lookup"><span data-stu-id="8f10f-124">There's no guarantee that the client has read the response before the connection closes.</span></span>
* <span data-ttu-id="8f10f-125">`Abort`La chiamata a deve essere rara e riservata per i casi di errore gravi, non per errori comuni.</span><span class="sxs-lookup"><span data-stu-id="8f10f-125">Calling `Abort` should be rare and reserved for severe error cases, not common errors.</span></span>
  * <span data-ttu-id="8f10f-126">Chiamare solo `Abort` quando è necessario risolvere un problema specifico.</span><span class="sxs-lookup"><span data-stu-id="8f10f-126">Only call `Abort` when a specific problem needs to be solved.</span></span> <span data-ttu-id="8f10f-127">Ad esempio, chiamare `Abort` se i client malintenzionati tentano di pubblicare dati o quando si verifica un bug nel codice client che provoca richieste di grandi dimensioni o diverse.</span><span class="sxs-lookup"><span data-stu-id="8f10f-127">For example, call `Abort` if malicious clients are trying to POST data or when there's a bug in client code that causes large or several requests.</span></span>
  * <span data-ttu-id="8f10f-128">Non chiamare le `Abort` situazioni di errore comuni, ad esempio HTTP 404 (non trovato).</span><span class="sxs-lookup"><span data-stu-id="8f10f-128">Don't call `Abort` for common error situations, such as HTTP 404 (Not Found).</span></span>

<span data-ttu-id="8f10f-129">Quando si chiama [HttpResponse. CompleteAsync](xref:Microsoft.AspNetCore.Http.HttpResponse.CompleteAsync%2A) , prima `Abort` di chiamare si garantisce che il server abbia completato la scrittura della risposta.</span><span class="sxs-lookup"><span data-stu-id="8f10f-129">Calling [HttpResponse.CompleteAsync](xref:Microsoft.AspNetCore.Http.HttpResponse.CompleteAsync%2A) before calling `Abort` ensures that the server has completed writing the response.</span></span> <span data-ttu-id="8f10f-130">Tuttavia, il comportamento del client non è prevedibile e potrebbe non essere in grado di leggere la risposta prima che la connessione venga interrotta.</span><span class="sxs-lookup"><span data-stu-id="8f10f-130">However, client behavior isn't predictable and they may not read the response before the connection is aborted.</span></span>

<span data-ttu-id="8f10f-131">Questo processo è diverso per HTTP/2 poiché il protocollo supporta l'interruzione dei singoli flussi di richiesta senza chiudere la connessione.</span><span class="sxs-lookup"><span data-stu-id="8f10f-131">This process is different for HTTP/2 because the protocol supports aborting individual request streams without closing the connection.</span></span> <span data-ttu-id="8f10f-132">Il timeout di svuotamento di cinque secondi non è applicabile.</span><span class="sxs-lookup"><span data-stu-id="8f10f-132">The five-second drain timeout doesn't apply.</span></span> <span data-ttu-id="8f10f-133">Se dopo il completamento di una risposta sono presenti dati del corpo della richiesta non letti, il server invia un frame RST HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="8f10f-133">If there's any unread request body data after completing a response, then the server sends an HTTP/2 RST frame.</span></span> <span data-ttu-id="8f10f-134">I frame di dati del corpo della richiesta aggiuntivi vengono ignorati.</span><span class="sxs-lookup"><span data-stu-id="8f10f-134">Additional request body data frames are ignored.</span></span>

<span data-ttu-id="8f10f-135">Se possibile, è preferibile che i client usino l'intestazione della richiesta [expect: 100-continue](https://developer.mozilla.org/docs/Web/HTTP/Status/100) e attendano la risposta del server prima di iniziare a inviare il corpo della richiesta.</span><span class="sxs-lookup"><span data-stu-id="8f10f-135">If possible, it's better for clients to use the [Expect: 100-continue](https://developer.mozilla.org/docs/Web/HTTP/Status/100) request header and wait for the server to respond before starting to send the request body.</span></span> <span data-ttu-id="8f10f-136">Che fornisce al client la possibilità di esaminare la risposta e interrompere prima di inviare dati non necessari.</span><span class="sxs-lookup"><span data-stu-id="8f10f-136">That gives the client an opportunity to examine the response and abort before sending unneeded data.</span></span>
