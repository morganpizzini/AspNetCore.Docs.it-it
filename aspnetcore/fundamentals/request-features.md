---
title: Funzionalità di richiesta in ASP.NET Core
author: ardalis
description: Dettagli dell'implementazione di server Web relativi alle richieste e alle risposte HTTP definiti nelle interfacce per ASP.NET Core.
ms.author: riande
ms.date: 10/14/2016
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
uid: fundamentals/request-features
ms.openlocfilehash: 3b5c929519407de5dc582c10a86745efddc8a38a
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634514"
---
# <a name="request-features-in-aspnet-core"></a><span data-ttu-id="ebd73-103">Funzionalità di richiesta in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ebd73-103">Request Features in ASP.NET Core</span></span>

<span data-ttu-id="ebd73-104">Di [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="ebd73-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="ebd73-105">I dettagli dell'implementazione di server Web relativi alle richieste e alle risposte HTTP vengono definiti nelle interfacce.</span><span class="sxs-lookup"><span data-stu-id="ebd73-105">Web server implementation details related to HTTP requests and responses are defined in interfaces.</span></span> <span data-ttu-id="ebd73-106">Le interfacce vengono usate dalle implementazioni del server e dal middleware per creare e modificare la pipeline di hosting dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="ebd73-106">These interfaces are used by server implementations and middleware to create and modify the application's hosting pipeline.</span></span>

## <a name="feature-interfaces"></a><span data-ttu-id="ebd73-107">Interfacce di funzionalità</span><span class="sxs-lookup"><span data-stu-id="ebd73-107">Feature interfaces</span></span>

<span data-ttu-id="ebd73-108">ASP.NET Core definisce diverse interfacce di funzionalità HTTP in `Microsoft.AspNetCore.Http.Features` che vengono usate dai server per identificare le funzionalità supportate.</span><span class="sxs-lookup"><span data-stu-id="ebd73-108">ASP.NET Core defines a number of HTTP feature interfaces in `Microsoft.AspNetCore.Http.Features` which are used by servers to identify the features they support.</span></span> <span data-ttu-id="ebd73-109">Le interfacce di funzionalità seguenti gestiscono le richieste e restituiscono le risposte:</span><span class="sxs-lookup"><span data-stu-id="ebd73-109">The following feature interfaces handle requests and return responses:</span></span>

<span data-ttu-id="ebd73-110">`IHttpRequestFeature` Definisce la struttura di una richiesta HTTP, inclusi protocollo, percorso, stringa di query, intestazioni e corpo.</span><span class="sxs-lookup"><span data-stu-id="ebd73-110">`IHttpRequestFeature` Defines the structure of an HTTP request, including the protocol, path, query string, headers, and body.</span></span>

<span data-ttu-id="ebd73-111">`IHttpResponseFeature` Definisce la struttura di una risposta HTTP, inclusi codice di stato, intestazioni e corpo della risposta.</span><span class="sxs-lookup"><span data-stu-id="ebd73-111">`IHttpResponseFeature` Defines the structure of an HTTP response, including the status code, headers, and body of the response.</span></span>

<span data-ttu-id="ebd73-112">`IHttpAuthenticationFeature` Definisce il supporto per identificare gli utenti in base a un oggetto `ClaimsPrincipal` e specificare un gestore di autenticazione.</span><span class="sxs-lookup"><span data-stu-id="ebd73-112">`IHttpAuthenticationFeature` Defines support for identifying users based on a `ClaimsPrincipal` and specifying an authentication handler.</span></span>

<span data-ttu-id="ebd73-113">`IHttpUpgradeFeature` Definisce il supporto per gli [aggiornamenti HTTP](https://tools.ietf.org/html/rfc2616.html#section-14.42), che consentono al client di specificare protocolli aggiuntivi da usare se il server vuole cambiare i protocolli.</span><span class="sxs-lookup"><span data-stu-id="ebd73-113">`IHttpUpgradeFeature` Defines support for [HTTP Upgrades](https://tools.ietf.org/html/rfc2616.html#section-14.42), which allow the client to specify which additional protocols it would like to use if the server wishes to switch protocols.</span></span>

<span data-ttu-id="ebd73-114">`IHttpBufferingFeature` Definisce metodi per la disattivazione del buffering di richieste e/o risposte.</span><span class="sxs-lookup"><span data-stu-id="ebd73-114">`IHttpBufferingFeature` Defines methods for disabling buffering of requests and/or responses.</span></span>

<span data-ttu-id="ebd73-115">`IHttpConnectionFeature` Definisce le proprietà per porte e indirizzi locali e remoti.</span><span class="sxs-lookup"><span data-stu-id="ebd73-115">`IHttpConnectionFeature` Defines properties for local and remote addresses and ports.</span></span>

<span data-ttu-id="ebd73-116">`IHttpRequestLifetimeFeature` Definisce il supporto per l'interruzione delle connessioni o per rilevare se una richiesta è stata terminata in modo anomalo, ad esempio da una disconnessione del client.</span><span class="sxs-lookup"><span data-stu-id="ebd73-116">`IHttpRequestLifetimeFeature` Defines support for aborting connections, or detecting if a request has been terminated prematurely, such as by a client disconnect.</span></span>

<span data-ttu-id="ebd73-117">`IHttpSendFileFeature` Definisce un metodo per l'invio di file in modo asincrono.</span><span class="sxs-lookup"><span data-stu-id="ebd73-117">`IHttpSendFileFeature` Defines a method for sending files asynchronously.</span></span>

<span data-ttu-id="ebd73-118">`IHttpWebSocketFeature` Definisce un'API per il supporto di WebSocket.</span><span class="sxs-lookup"><span data-stu-id="ebd73-118">`IHttpWebSocketFeature` Defines an API for supporting web sockets.</span></span>

<span data-ttu-id="ebd73-119">`IHttpRequestIdentifierFeature` Aggiunge una proprietà che può essere implementata per identificare le richieste in modo univoco.</span><span class="sxs-lookup"><span data-stu-id="ebd73-119">`IHttpRequestIdentifierFeature` Adds a property that can be implemented to uniquely identify requests.</span></span>

<span data-ttu-id="ebd73-120">`ISessionFeature` Definisce le astrazioni `ISessionFactory` e `ISession` per supportare le sessioni utente.</span><span class="sxs-lookup"><span data-stu-id="ebd73-120">`ISessionFeature` Defines `ISessionFactory` and `ISession` abstractions for supporting user sessions.</span></span>

<span data-ttu-id="ebd73-121">`ITlsConnectionFeature` Definisce un'API per il recupero dei certificati client.</span><span class="sxs-lookup"><span data-stu-id="ebd73-121">`ITlsConnectionFeature` Defines an API for retrieving client certificates.</span></span>

<span data-ttu-id="ebd73-122">`ITlsTokenBindingFeature` Definisce i metodi per l'uso di parametri di associazione dei token TLS.</span><span class="sxs-lookup"><span data-stu-id="ebd73-122">`ITlsTokenBindingFeature` Defines methods for working with TLS token binding parameters.</span></span>

> [!NOTE]
> <span data-ttu-id="ebd73-123">`ISessionFeature` non è una funzionalità server, ma viene implementato da `SessionMiddleware`. Vedere [Stato di sessioni e app](app-state.md).</span><span class="sxs-lookup"><span data-stu-id="ebd73-123">`ISessionFeature` isn't a server feature, but is implemented by the `SessionMiddleware` (see [Managing Application State](app-state.md)).</span></span>

## <a name="feature-collections"></a><span data-ttu-id="ebd73-124">Raccolte di funzionalità</span><span class="sxs-lookup"><span data-stu-id="ebd73-124">Feature collections</span></span>

<span data-ttu-id="ebd73-125">La proprietà `Features` di `HttpContext` specifica un'interfaccia per ottenere e impostare le funzionalità HTTP disponibili per la richiesta corrente.</span><span class="sxs-lookup"><span data-stu-id="ebd73-125">The `Features` property of `HttpContext` provides an interface for getting and setting the available HTTP features for the current request.</span></span> <span data-ttu-id="ebd73-126">Poiché l'insieme di funzionalità è modificabile anche all'interno del contesto di una richiesta, è possibile usare il middleware per modificare la raccolta e aggiungere il supporto di altre funzionalità.</span><span class="sxs-lookup"><span data-stu-id="ebd73-126">Since the feature collection is mutable even within the context of a request, middleware can be used to modify the collection and add support for additional features.</span></span>

## <a name="middleware-and-request-features"></a><span data-ttu-id="ebd73-127">Middleware e funzionalità di richiesta</span><span class="sxs-lookup"><span data-stu-id="ebd73-127">Middleware and request features</span></span>

<span data-ttu-id="ebd73-128">Anche se sono i server a creare la raccolta di funzionalità, il middleware può sia aggiungere che togliere funzionalità alla raccolta.</span><span class="sxs-lookup"><span data-stu-id="ebd73-128">While servers are responsible for creating the feature collection, middleware can both add to this collection and consume features from the collection.</span></span> <span data-ttu-id="ebd73-129">Ad esempio, l'elemento `StaticFileMiddleware` accede alla funzionalità `IHttpSendFileFeature`.</span><span class="sxs-lookup"><span data-stu-id="ebd73-129">For example, the `StaticFileMiddleware` accesses the `IHttpSendFileFeature` feature.</span></span> <span data-ttu-id="ebd73-130">Se la funzionalità è presente, viene usata per inviare il file statico richiesto dal percorso fisico relativo.</span><span class="sxs-lookup"><span data-stu-id="ebd73-130">If the feature exists, it's used to send the requested static file from its physical path.</span></span> <span data-ttu-id="ebd73-131">In caso contrario, per inviare il file viene usato un metodo alternativo più lento.</span><span class="sxs-lookup"><span data-stu-id="ebd73-131">Otherwise, a slower alternative method is used to send the file.</span></span> <span data-ttu-id="ebd73-132">Quando è disponibile, `IHttpSendFileFeature` consente al sistema operativo di aprire il file ed eseguire una copia in modalità kernel diretta alla scheda di rete.</span><span class="sxs-lookup"><span data-stu-id="ebd73-132">When available, the `IHttpSendFileFeature` allows the operating system to open the file and perform a direct kernel mode copy to the network card.</span></span>

<span data-ttu-id="ebd73-133">Il middleware può anche eseguire aggiunte alla raccolta di funzionalità impostata dal server.</span><span class="sxs-lookup"><span data-stu-id="ebd73-133">Additionally, middleware can add to the feature collection established by the server.</span></span> <span data-ttu-id="ebd73-134">Il middleware può anche sostituire funzionalità esistenti, aumentando così la funzionalità del server.</span><span class="sxs-lookup"><span data-stu-id="ebd73-134">Existing features can even be replaced by middleware, allowing the middleware to augment the functionality of the server.</span></span> <span data-ttu-id="ebd73-135">Le funzionalità aggiunte alla raccolta sono disponibili immediatamente per un altro middleware o per l'applicazione sottostante in un secondo momento nella pipeline della richiesta.</span><span class="sxs-lookup"><span data-stu-id="ebd73-135">Features added to the collection are available immediately to other middleware or the underlying application itself later in the request pipeline.</span></span>

<span data-ttu-id="ebd73-136">Combinando implementazioni server personalizzate e miglioramenti specifici del middleware, è possibile costruire il set di funzionalità preciso richiesto da un'applicazione.</span><span class="sxs-lookup"><span data-stu-id="ebd73-136">By combining custom server implementations and specific middleware enhancements, the precise set of features an application requires can be constructed.</span></span> <span data-ttu-id="ebd73-137">In questo modo è possibile aggiungere le funzionalità mancanti senza che siano necessarie modifiche nel server e garantire che venga esposta solo una quantità minima di funzionalità, limitando in tal modo la superficie di attacco e migliorando le prestazioni.</span><span class="sxs-lookup"><span data-stu-id="ebd73-137">This allows missing features to be added without requiring a change in server, and ensures only the minimal amount of features are exposed, thus limiting attack surface area and improving performance.</span></span>

## <a name="summary"></a><span data-ttu-id="ebd73-138">Riepilogo</span><span class="sxs-lookup"><span data-stu-id="ebd73-138">Summary</span></span>

<span data-ttu-id="ebd73-139">Le interfacce di funzionalità definiscono funzionalità HTTP specifiche supportate da una data richiesta.</span><span class="sxs-lookup"><span data-stu-id="ebd73-139">Feature interfaces define specific HTTP features that a given request may support.</span></span> <span data-ttu-id="ebd73-140">I server definiscono le raccolte di funzionalità e il set iniziale di funzionalità supportato dal server, ma è possibile usare il middleware per migliorare tali funzionalità.</span><span class="sxs-lookup"><span data-stu-id="ebd73-140">Servers define collections of features, and the initial set of features supported by that server, but middleware can be used to enhance these features.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ebd73-141">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="ebd73-141">Additional resources</span></span>

* [<span data-ttu-id="ebd73-142">Server</span><span class="sxs-lookup"><span data-stu-id="ebd73-142">Servers</span></span>](xref:fundamentals/servers/index)
* [<span data-ttu-id="ebd73-143">Middleware</span><span class="sxs-lookup"><span data-stu-id="ebd73-143">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="ebd73-144">Open Web Interface for .NET (OWIN)</span><span class="sxs-lookup"><span data-stu-id="ebd73-144">Open Web Interface for .NET (OWIN)</span></span>](xref:fundamentals/owin)
