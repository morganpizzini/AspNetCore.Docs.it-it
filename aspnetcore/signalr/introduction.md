---
title: Introduzione a ASP.NET CoreSignalR
author: bradygaster
description: Scopri come la SignalR libreria ASP.NET Core semplifica l'aggiunta di funzionalità in tempo reale alle app.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/27/2019
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
uid: signalr/introduction
ms.openlocfilehash: 6f3cec83c9af5ec6e820db4a15061eddac613f36
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/08/2020
ms.locfileid: "88022173"
---
# <a name="introduction-to-aspnet-core-no-locsignalr"></a><span data-ttu-id="6d175-103">Introduzione a ASP.NET CoreSignalR</span><span class="sxs-lookup"><span data-stu-id="6d175-103">Introduction to ASP.NET Core SignalR</span></span>

## <a name="what-is-no-locsignalr"></a><span data-ttu-id="6d175-104">Che cos'è SignalR ?</span><span class="sxs-lookup"><span data-stu-id="6d175-104">What is SignalR?</span></span>

<span data-ttu-id="6d175-105">ASP.NET Core SignalR è una libreria open source che semplifica l'aggiunta di funzionalità Web in tempo reale alle app.</span><span class="sxs-lookup"><span data-stu-id="6d175-105">ASP.NET Core SignalR is an open-source library that simplifies adding real-time web functionality to apps.</span></span> <span data-ttu-id="6d175-106">La funzionalità Web in tempo reale consente di eseguire il push del contenuto ai client immediatamente dal codice lato server.</span><span class="sxs-lookup"><span data-stu-id="6d175-106">Real-time web functionality enables server-side code to push content to clients instantly.</span></span>

<span data-ttu-id="6d175-107">Candidati validi per SignalR :</span><span class="sxs-lookup"><span data-stu-id="6d175-107">Good candidates for SignalR:</span></span>

* <span data-ttu-id="6d175-108">App che richiedono aggiornamenti con frequenza elevata dal server,</span><span class="sxs-lookup"><span data-stu-id="6d175-108">Apps that require high frequency updates from the server.</span></span> <span data-ttu-id="6d175-109">ad esempio giochi, social network, mappe, aste e app GPS e di voto.</span><span class="sxs-lookup"><span data-stu-id="6d175-109">Examples are gaming, social networks, voting, auction, maps, and GPS apps.</span></span>
* <span data-ttu-id="6d175-110">Dashboard e app di monitoraggio,</span><span class="sxs-lookup"><span data-stu-id="6d175-110">Dashboards and monitoring apps.</span></span> <span data-ttu-id="6d175-111">ad esempio dashboard aziendali, aggiornamenti di vendite istantanee o avvisi di viaggio.</span><span class="sxs-lookup"><span data-stu-id="6d175-111">Examples include company dashboards, instant sales updates, or travel alerts.</span></span>
* <span data-ttu-id="6d175-112">App di collaborazione,</span><span class="sxs-lookup"><span data-stu-id="6d175-112">Collaborative apps.</span></span> <span data-ttu-id="6d175-113">ad esempio app per lavagne e software per riunioni in team.</span><span class="sxs-lookup"><span data-stu-id="6d175-113">Whiteboard apps and team meeting software are examples of collaborative apps.</span></span>
* <span data-ttu-id="6d175-114">App che richiedono notifiche.</span><span class="sxs-lookup"><span data-stu-id="6d175-114">Apps that require notifications.</span></span> <span data-ttu-id="6d175-115">Social network, posta elettronica, chat, giochi, avvisi di viaggio e molte altre app usano le notifiche.</span><span class="sxs-lookup"><span data-stu-id="6d175-115">Social networks, email, chat, games, travel alerts, and many other apps use notifications.</span></span>

<span data-ttu-id="6d175-116">SignalRfornisce un'API per la creazione di [chiamate a procedure remote (RPC)](https://wikipedia.org/wiki/Remote_procedure_call)da server a client.</span><span class="sxs-lookup"><span data-stu-id="6d175-116">SignalR provides an API for creating server-to-client [remote procedure calls (RPC)](https://wikipedia.org/wiki/Remote_procedure_call).</span></span> <span data-ttu-id="6d175-117">Le chiamate RPC chiamano funzioni JavaScript nei client dal codice .NET Core lato server.</span><span class="sxs-lookup"><span data-stu-id="6d175-117">The RPCs call JavaScript functions on clients from server-side .NET Core code.</span></span>

<span data-ttu-id="6d175-118">Di seguito sono riportate alcune funzionalità di SignalR per ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="6d175-118">Here are some features of SignalR for ASP.NET Core:</span></span>

* <span data-ttu-id="6d175-119">Gestisce automaticamente la gestione delle connessioni.</span><span class="sxs-lookup"><span data-stu-id="6d175-119">Handles connection management automatically.</span></span>
* <span data-ttu-id="6d175-120">Invia messaggi contemporaneamente a tutti i client connessi.</span><span class="sxs-lookup"><span data-stu-id="6d175-120">Sends messages to all connected clients simultaneously.</span></span> <span data-ttu-id="6d175-121">Ad esempio, una chat room.</span><span class="sxs-lookup"><span data-stu-id="6d175-121">For example, a chat room.</span></span>
* <span data-ttu-id="6d175-122">Invia messaggi a client o gruppi di client specifici.</span><span class="sxs-lookup"><span data-stu-id="6d175-122">Sends messages to specific clients or groups of clients.</span></span>
* <span data-ttu-id="6d175-123">Ridimensiona per gestire l'aumento del traffico.</span><span class="sxs-lookup"><span data-stu-id="6d175-123">Scales to handle increasing traffic.</span></span>

<span data-ttu-id="6d175-124">L'origine è ospitata in un [ SignalR repository in GitHub](https://github.com/dotnet/AspNetCore/tree/master/src/SignalR).</span><span class="sxs-lookup"><span data-stu-id="6d175-124">The source is hosted in a [SignalR repository on GitHub](https://github.com/dotnet/AspNetCore/tree/master/src/SignalR).</span></span>

## <a name="transports"></a><span data-ttu-id="6d175-125">Trasporti</span><span class="sxs-lookup"><span data-stu-id="6d175-125">Transports</span></span>

<span data-ttu-id="6d175-126">SignalRsupporta le tecniche seguenti per la gestione della comunicazione in tempo reale (in ordine di fallback normale):</span><span class="sxs-lookup"><span data-stu-id="6d175-126">SignalR supports the following techniques for handling real-time communication (in order of graceful fallback):</span></span>

* [<span data-ttu-id="6d175-127">WebSocket</span><span class="sxs-lookup"><span data-stu-id="6d175-127">WebSockets</span></span>](https://tools.ietf.org/html/rfc7118)
* <span data-ttu-id="6d175-128">Eventi inviati dal server</span><span class="sxs-lookup"><span data-stu-id="6d175-128">Server-Sent Events</span></span>
* <span data-ttu-id="6d175-129">Polling prolungato</span><span class="sxs-lookup"><span data-stu-id="6d175-129">Long Polling</span></span>

<span data-ttu-id="6d175-130">SignalRsceglie automaticamente il metodo di trasporto migliore all'interno delle funzionalità del server e del client.</span><span class="sxs-lookup"><span data-stu-id="6d175-130">SignalR automatically chooses the best transport method that is within the capabilities of the server and client.</span></span>

## <a name="hubs"></a><span data-ttu-id="6d175-131">Hub</span><span class="sxs-lookup"><span data-stu-id="6d175-131">Hubs</span></span>

<span data-ttu-id="6d175-132">SignalRUSA gli *Hub* per la comunicazione tra client e server.</span><span class="sxs-lookup"><span data-stu-id="6d175-132">SignalR uses *hubs* to communicate between clients and servers.</span></span>

<span data-ttu-id="6d175-133">Un hub è una pipeline di alto livello che consente a un client e a un server di chiamare i metodi tra loro.</span><span class="sxs-lookup"><span data-stu-id="6d175-133">A hub is a high-level pipeline that allows a client and server to call methods on each other.</span></span> <span data-ttu-id="6d175-134">SignalRgestisce automaticamente l'invio tra i limiti del computer, consentendo ai client di chiamare i metodi sul server e viceversa.</span><span class="sxs-lookup"><span data-stu-id="6d175-134">SignalR handles the dispatching across machine boundaries automatically, allowing clients to call methods on the server and vice versa.</span></span> <span data-ttu-id="6d175-135">È possibile passare parametri fortemente tipizzati ai metodi, che Abilita l'associazione di modelli.</span><span class="sxs-lookup"><span data-stu-id="6d175-135">You can pass strongly-typed parameters to methods, which enables model binding.</span></span> <span data-ttu-id="6d175-136">SignalRin sono disponibili due protocolli Hub predefiniti: un protocollo di testo basato su JSON e un protocollo binario basato su [MessagePack](https://msgpack.org/).</span><span class="sxs-lookup"><span data-stu-id="6d175-136">SignalR provides two built-in hub protocols: a text protocol based on JSON and a binary protocol based on [MessagePack](https://msgpack.org/).</span></span>  <span data-ttu-id="6d175-137">MessagePack crea in genere messaggi più piccoli rispetto a JSON.</span><span class="sxs-lookup"><span data-stu-id="6d175-137">MessagePack generally creates smaller messages compared to JSON.</span></span> <span data-ttu-id="6d175-138">I browser meno recenti devono supportare il [livello XHR 2](https://caniuse.com/#feat=xhr2) per fornire il supporto del protocollo MessagePack.</span><span class="sxs-lookup"><span data-stu-id="6d175-138">Older browsers must support [XHR level 2](https://caniuse.com/#feat=xhr2) to provide MessagePack protocol support.</span></span>

<span data-ttu-id="6d175-139">Gli hub chiamano il codice lato client inviando messaggi che contengono il nome e i parametri del metodo lato client.</span><span class="sxs-lookup"><span data-stu-id="6d175-139">Hubs call client-side code by sending messages that contain the name and parameters of the client-side method.</span></span> <span data-ttu-id="6d175-140">Gli oggetti inviati come parametri del metodo vengono deserializzati utilizzando il protocollo configurato.</span><span class="sxs-lookup"><span data-stu-id="6d175-140">Objects sent as method parameters are deserialized using the configured protocol.</span></span> <span data-ttu-id="6d175-141">Il client tenta di trovare una corrispondenza tra il nome e un metodo nel codice lato client.</span><span class="sxs-lookup"><span data-stu-id="6d175-141">The client tries to match the name to a method in the client-side code.</span></span> <span data-ttu-id="6d175-142">Quando il client trova una corrispondenza, chiama il metodo e passa ai dati dei parametri deserializzati.</span><span class="sxs-lookup"><span data-stu-id="6d175-142">When the client finds a match, it calls the method and passes to it the deserialized parameter data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6d175-143">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="6d175-143">Additional resources</span></span>

* [<span data-ttu-id="6d175-144">Introduzione a SignalR per ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6d175-144">Get started with SignalR for ASP.NET Core</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="6d175-145">Piattaforme supportate</span><span class="sxs-lookup"><span data-stu-id="6d175-145">Supported Platforms</span></span>](xref:signalr/supported-platforms)
* [<span data-ttu-id="6d175-146">Hub</span><span class="sxs-lookup"><span data-stu-id="6d175-146">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="6d175-147">Client JavaScript</span><span class="sxs-lookup"><span data-stu-id="6d175-147">JavaScript client</span></span>](xref:signalr/javascript-client)
