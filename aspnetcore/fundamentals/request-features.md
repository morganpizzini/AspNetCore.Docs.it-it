---
title: Funzionalità di richiesta in ASP.NET Core
author: ardalis
description: Dettagli dell'implementazione di server Web relativi alle richieste e alle risposte HTTP definiti nelle interfacce per ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 10/20/2020
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
uid: fundamentals/request-features
ms.openlocfilehash: 88e97d88341789a76a79da8d92098c2e00396fe7
ms.sourcegitcommit: 59d95a9106301d5ec5c9f612600903a69c4580ef
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "95870425"
---
# <a name="request-features-in-aspnet-core"></a><span data-ttu-id="f2a62-103">Funzionalità di richiesta in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f2a62-103">Request Features in ASP.NET Core</span></span>

<span data-ttu-id="f2a62-104">Di [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="f2a62-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="f2a62-105">L' `HttpContext` API utilizzata dalle applicazioni e dal middleware per elaborare le richieste dispone di un livello di astrazione sottostante denominato *interfacce di funzionalità*.</span><span class="sxs-lookup"><span data-stu-id="f2a62-105">The `HttpContext` API that applications and middleware use to process requests has an abstraction layer underneath it called *feature interfaces*.</span></span> <span data-ttu-id="f2a62-106">Ogni interfaccia della funzionalità fornisce un subset granulare delle funzionalità esposte da `HttpContext` .</span><span class="sxs-lookup"><span data-stu-id="f2a62-106">Each feature interface provides a granular subset of the functionality exposed by `HttpContext`.</span></span> <span data-ttu-id="f2a62-107">Queste interfacce possono essere aggiunte, modificate, sottoposto a Wrapped, sostituite o addirittura rimosse dal server o dal middleware, perché la richiesta viene elaborata senza dover implementare nuovamente l'intero `HttpContext` .</span><span class="sxs-lookup"><span data-stu-id="f2a62-107">These interfaces can be added, modified, wrapped, replaced, or even removed by the server or middleware as the request is processed without having to re-implement the entire `HttpContext`.</span></span> <span data-ttu-id="f2a62-108">Possono anche essere usati per simulare le funzionalità durante i test.</span><span class="sxs-lookup"><span data-stu-id="f2a62-108">They can also be used to mock functionality when testing.</span></span>

## <a name="feature-collections"></a><span data-ttu-id="f2a62-109">Raccolte di funzionalità</span><span class="sxs-lookup"><span data-stu-id="f2a62-109">Feature collections</span></span>

<span data-ttu-id="f2a62-110">La <xref:Microsoft.AspNetCore.Http.HttpContext.Features> proprietà di `HttpContext` fornisce l'accesso alla raccolta di interfacce di funzionalità per la richiesta corrente.</span><span class="sxs-lookup"><span data-stu-id="f2a62-110">The <xref:Microsoft.AspNetCore.Http.HttpContext.Features> property of `HttpContext` provides access to the collection of feature interfaces for the current request.</span></span> <span data-ttu-id="f2a62-111">Poiché l'insieme di funzionalità è modificabile anche all'interno del contesto di una richiesta, è possibile usare il middleware per modificare la raccolta e aggiungere il supporto di altre funzionalità.</span><span class="sxs-lookup"><span data-stu-id="f2a62-111">Since the feature collection is mutable even within the context of a request, middleware can be used to modify the collection and add support for additional features.</span></span> <span data-ttu-id="f2a62-112">Alcune funzionalità avanzate sono disponibili solo accedendo all'interfaccia associata tramite la raccolta di funzionalità.</span><span class="sxs-lookup"><span data-stu-id="f2a62-112">Some advanced features are only available by accessing the associated interface through the feature collection.</span></span>

## <a name="feature-interfaces"></a><span data-ttu-id="f2a62-113">Interfacce di funzionalità</span><span class="sxs-lookup"><span data-stu-id="f2a62-113">Feature interfaces</span></span>

<span data-ttu-id="f2a62-114">ASP.NET Core definisce una serie di interfacce di funzionalità HTTP comuni in <xref:Microsoft.AspNetCore.Http.Features?displayProperty=fullName> , condivise da vari server e middleware per identificare le funzionalità supportate.</span><span class="sxs-lookup"><span data-stu-id="f2a62-114">ASP.NET Core defines a number of common HTTP feature interfaces in <xref:Microsoft.AspNetCore.Http.Features?displayProperty=fullName>, which are shared by various servers and middleware to identify the features that they support.</span></span> <span data-ttu-id="f2a62-115">I server e il middleware possono anche fornire interfacce personalizzate con funzionalità aggiuntive.</span><span class="sxs-lookup"><span data-stu-id="f2a62-115">Servers and middleware may also provide their own interfaces with additional functionality.</span></span>

<span data-ttu-id="f2a62-116">La maggior parte delle interfacce di funzionalità forniscono funzionalità facoltative e leggere e le `HttpContext` API associate forniscono impostazioni predefinite se la funzionalità non è presente.</span><span class="sxs-lookup"><span data-stu-id="f2a62-116">Most feature interfaces provide optional, light-up functionality, and their associated `HttpContext` APIs provide defaults if the feature isn't present.</span></span> <span data-ttu-id="f2a62-117">Alcune interfacce sono indicate nel contenuto seguente come richiesto perché forniscono la funzionalità di base di richiesta e risposta e devono essere implementate per elaborare la richiesta.</span><span class="sxs-lookup"><span data-stu-id="f2a62-117">A few interfaces are indicated in the following content as required because they provide core request and response functionality and must be implemented in order to process the request.</span></span>

<span data-ttu-id="f2a62-118">Di seguito sono riportate le interfacce delle funzionalità seguenti <xref:Microsoft.AspNetCore.Http.Features?displayProperty=fullName> :</span><span class="sxs-lookup"><span data-stu-id="f2a62-118">The following feature interfaces are from <xref:Microsoft.AspNetCore.Http.Features?displayProperty=fullName>:</span></span>

<span data-ttu-id="f2a62-119"><xref:Microsoft.AspNetCore.Http.Features.IHttpRequestFeature>: Definisce la struttura di una richiesta HTTP, inclusi il protocollo, il percorso, la stringa di query, le intestazioni e il corpo.</span><span class="sxs-lookup"><span data-stu-id="f2a62-119"><xref:Microsoft.AspNetCore.Http.Features.IHttpRequestFeature>: Defines the structure of an HTTP request, including the protocol, path, query string, headers, and body.</span></span> <span data-ttu-id="f2a62-120">Questa funzionalità è necessaria per elaborare le richieste.</span><span class="sxs-lookup"><span data-stu-id="f2a62-120">This feature is required in order to process requests.</span></span>

<span data-ttu-id="f2a62-121"><xref:Microsoft.AspNetCore.Http.Features.IHttpResponseFeature>: Definisce la struttura di una risposta HTTP, inclusi il codice di stato, le intestazioni e il corpo della risposta.</span><span class="sxs-lookup"><span data-stu-id="f2a62-121"><xref:Microsoft.AspNetCore.Http.Features.IHttpResponseFeature>: Defines the structure of an HTTP response, including the status code, headers, and body of the response.</span></span> <span data-ttu-id="f2a62-122">Questa funzionalità è necessaria per elaborare le richieste.</span><span class="sxs-lookup"><span data-stu-id="f2a62-122">This feature is required in order to process requests.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f2a62-123"><xref:Microsoft.AspNetCore.Http.Features.IHttpResponseBodyFeature>: Definisce modi diversi per scrivere il corpo della risposta, usando un oggetto `Stream` , un oggetto `PipeWriter` o un file.</span><span class="sxs-lookup"><span data-stu-id="f2a62-123"><xref:Microsoft.AspNetCore.Http.Features.IHttpResponseBodyFeature>: Defines different ways of writing out the response body, using either a `Stream`, a `PipeWriter`, or a file.</span></span> <span data-ttu-id="f2a62-124">Questa funzionalità è necessaria per elaborare le richieste.</span><span class="sxs-lookup"><span data-stu-id="f2a62-124">This feature is required in order to process requests.</span></span> <span data-ttu-id="f2a62-125">Sostituisce `IHttpResponseFeature.Body` e `IHttpSendFileFeature` .</span><span class="sxs-lookup"><span data-stu-id="f2a62-125">This replaces `IHttpResponseFeature.Body` and `IHttpSendFileFeature`.</span></span>

::: moniker-end

<span data-ttu-id="f2a62-126"><xref:Microsoft.AspNetCore.Http.Features.Authentication.IHttpAuthenticationFeature>: Include l'oggetto <xref:System.Security.Claims.ClaimsPrincipal> attualmente associato alla richiesta.</span><span class="sxs-lookup"><span data-stu-id="f2a62-126"><xref:Microsoft.AspNetCore.Http.Features.Authentication.IHttpAuthenticationFeature>: Holds the <xref:System.Security.Claims.ClaimsPrincipal> currently associated with the request.</span></span>

<span data-ttu-id="f2a62-127"><xref:Microsoft.AspNetCore.Http.Features.IFormFeature>: Utilizzato per analizzare e memorizzare nella cache gli invii di form HTTP e multipart in ingresso.</span><span class="sxs-lookup"><span data-stu-id="f2a62-127"><xref:Microsoft.AspNetCore.Http.Features.IFormFeature>: Used to parse and cache incoming HTTP and multipart form submissions.</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="f2a62-128"><xref:Microsoft.AspNetCore.Http.Features.IHttpBodyControlFeature>: Usato per controllare se sono consentite operazioni di i/o sincrone per i corpi della richiesta o della risposta.</span><span class="sxs-lookup"><span data-stu-id="f2a62-128"><xref:Microsoft.AspNetCore.Http.Features.IHttpBodyControlFeature>: Used to control if synchronous IO operations are allowed for the request or response bodies.</span></span>

::: moniker-end
   
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="f2a62-129"><xref:Microsoft.AspNetCore.Http.Features.IHttpBufferingFeature>: Definisce i metodi per disabilitare il buffering di richieste e/o risposte.</span><span class="sxs-lookup"><span data-stu-id="f2a62-129"><xref:Microsoft.AspNetCore.Http.Features.IHttpBufferingFeature>: Defines methods for disabling buffering of requests and/or responses.</span></span>

::: moniker-end

<span data-ttu-id="f2a62-130"><xref:Microsoft.AspNetCore.Http.Features.IHttpConnectionFeature>: Definisce le proprietà per l'ID connessione e gli indirizzi e le porte locali e remoti.</span><span class="sxs-lookup"><span data-stu-id="f2a62-130"><xref:Microsoft.AspNetCore.Http.Features.IHttpConnectionFeature>: Defines properties for the connection id and local and remote addresses and ports.</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="f2a62-131"><xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>: Controlla la dimensione massima consentita del corpo della richiesta per la richiesta corrente.</span><span class="sxs-lookup"><span data-stu-id="f2a62-131"><xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>: Controls the maximum allowed request body size for the current request.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="f2a62-132">`IHttpRequestBodyDetectionFeature`: Indica se la richiesta può avere un corpo.</span><span class="sxs-lookup"><span data-stu-id="f2a62-132">`IHttpRequestBodyDetectionFeature`: Indicates if the request can have a body.</span></span>

::: moniker-end

<span data-ttu-id="f2a62-133"><xref:Microsoft.AspNetCore.Http.Features.IHttpRequestIdentifierFeature>: Aggiunge una proprietà che può essere implementata per identificare in modo univoco le richieste.</span><span class="sxs-lookup"><span data-stu-id="f2a62-133"><xref:Microsoft.AspNetCore.Http.Features.IHttpRequestIdentifierFeature>: Adds a property that can be implemented to uniquely identify requests.</span></span>

<span data-ttu-id="f2a62-134"><xref:Microsoft.AspNetCore.Http.Features.IHttpRequestLifetimeFeature>: Definisce il supporto per l'interruzione delle connessioni o il rilevamento se una richiesta è stata terminata in modo anomalo, ad esempio dalla disconnessione di un client.</span><span class="sxs-lookup"><span data-stu-id="f2a62-134"><xref:Microsoft.AspNetCore.Http.Features.IHttpRequestLifetimeFeature>: Defines support for aborting connections or detecting if a request has been terminated prematurely, such as by a client disconnect.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f2a62-135"><xref:Microsoft.AspNetCore.Http.Features.IHttpRequestTrailersFeature>: Fornisce l'accesso alle intestazioni del trailer della richiesta, se presenti.</span><span class="sxs-lookup"><span data-stu-id="f2a62-135"><xref:Microsoft.AspNetCore.Http.Features.IHttpRequestTrailersFeature>: Provides access to the request trailer headers, if any.</span></span>

<span data-ttu-id="f2a62-136"><xref:Microsoft.AspNetCore.Http.Features.IHttpResetFeature>: Usato per inviare messaggi di reimpostazione per i protocolli che li supportano, ad esempio HTTP/2 o HTTP/3.</span><span class="sxs-lookup"><span data-stu-id="f2a62-136"><xref:Microsoft.AspNetCore.Http.Features.IHttpResetFeature>: Used to send reset messages for protocols that support them such as HTTP/2 or HTTP/3.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="f2a62-137"><xref:Microsoft.AspNetCore.Http.Features.IHttpResponseTrailersFeature>: Consente all'applicazione di fornire le intestazioni del trailer della risposta, se supportate.</span><span class="sxs-lookup"><span data-stu-id="f2a62-137"><xref:Microsoft.AspNetCore.Http.Features.IHttpResponseTrailersFeature>: Enables the application to provide response trailer headers if supported.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="f2a62-138"><xref:Microsoft.AspNetCore.Http.Features.IHttpSendFileFeature>: Definisce un metodo per l'invio di file in modo asincrono.</span><span class="sxs-lookup"><span data-stu-id="f2a62-138"><xref:Microsoft.AspNetCore.Http.Features.IHttpSendFileFeature>: Defines a method for sending files asynchronously.</span></span>

::: moniker-end

<span data-ttu-id="f2a62-139"><xref:Microsoft.AspNetCore.Http.Features.IHttpUpgradeFeature>: Definisce il supporto per gli [aggiornamenti http](https://tools.ietf.org/html/rfc2616.html#section-14.42)che consentono al client di specificare quali protocolli aggiuntivi si desidera utilizzare se il server desidera cambiare i protocolli.</span><span class="sxs-lookup"><span data-stu-id="f2a62-139"><xref:Microsoft.AspNetCore.Http.Features.IHttpUpgradeFeature>: Defines support for [HTTP Upgrades](https://tools.ietf.org/html/rfc2616.html#section-14.42), which allow the client to specify which additional protocols it would like to use if the server wishes to switch protocols.</span></span>

<span data-ttu-id="f2a62-140"><xref:Microsoft.AspNetCore.Http.Features.IHttpWebSocketFeature>: Definisce un'API per il supporto di Web Socket.</span><span class="sxs-lookup"><span data-stu-id="f2a62-140"><xref:Microsoft.AspNetCore.Http.Features.IHttpWebSocketFeature>: Defines an API for supporting web sockets.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f2a62-141"><xref:Microsoft.AspNetCore.Http.Features.IHttpsCompressionFeature>: Controlla se la compressione della risposta deve essere utilizzata su connessioni HTTPS.</span><span class="sxs-lookup"><span data-stu-id="f2a62-141"><xref:Microsoft.AspNetCore.Http.Features.IHttpsCompressionFeature>: Controls if response compression should be used over HTTPS connections.</span></span>

::: moniker-end

<span data-ttu-id="f2a62-142"><xref:Microsoft.AspNetCore.Http.Features.IItemsFeature>: Archivia la <xref:Microsoft.AspNetCore.Http.Features.IItemsFeature.Items> raccolta per lo stato di applicazione per richiesta.</span><span class="sxs-lookup"><span data-stu-id="f2a62-142"><xref:Microsoft.AspNetCore.Http.Features.IItemsFeature>: Stores the <xref:Microsoft.AspNetCore.Http.Features.IItemsFeature.Items> collection for per request application state.</span></span>

<span data-ttu-id="f2a62-143"><xref:Microsoft.AspNetCore.Http.Features.IQueryFeature>: Analizza e memorizza nella cache la stringa di query.</span><span class="sxs-lookup"><span data-stu-id="f2a62-143"><xref:Microsoft.AspNetCore.Http.Features.IQueryFeature>: Parses and caches the query string.</span></span>
   
::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f2a62-144"><xref:Microsoft.AspNetCore.Http.Features.IRequestBodyPipeFeature>: Rappresenta il corpo della richiesta come <xref:System.IO.Pipelines.PipeReader> .</span><span class="sxs-lookup"><span data-stu-id="f2a62-144"><xref:Microsoft.AspNetCore.Http.Features.IRequestBodyPipeFeature>: Represents the request body as a <xref:System.IO.Pipelines.PipeReader>.</span></span>
 
::: moniker-end

<span data-ttu-id="f2a62-145"><xref:Microsoft.AspNetCore.Http.Features.IRequestCookiesFeature>: Analizza e memorizza nella cache i valori dell'intestazione della richiesta `Cookie` .</span><span class="sxs-lookup"><span data-stu-id="f2a62-145"><xref:Microsoft.AspNetCore.Http.Features.IRequestCookiesFeature>: Parses and caches the request `Cookie` header values.</span></span>

<span data-ttu-id="f2a62-146"><xref:Microsoft.AspNetCore.Http.Features.IResponseCookiesFeature>: Controlla la modalità cookie di applicazione della risposta all' `Set-Cookie` intestazione.</span><span class="sxs-lookup"><span data-stu-id="f2a62-146"><xref:Microsoft.AspNetCore.Http.Features.IResponseCookiesFeature>: Controls how response cookies are applied to the `Set-Cookie` header.</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="f2a62-147"><xref:Microsoft.AspNetCore.Http.Features.IServerVariablesFeature>: Questa funzionalità fornisce l'accesso alle variabili del server di richiesta, ad esempio quelle fornite da IIS.</span><span class="sxs-lookup"><span data-stu-id="f2a62-147"><xref:Microsoft.AspNetCore.Http.Features.IServerVariablesFeature>: This feature provides access to request server variables such as those provided by IIS.</span></span>

::: moniker-end
   
<span data-ttu-id="f2a62-148"><xref:Microsoft.AspNetCore.Http.Features.IServiceProvidersFeature>: Fornisce l'accesso a un <xref:System.IServiceProvider> con servizi di richiesta con ambito.</span><span class="sxs-lookup"><span data-stu-id="f2a62-148"><xref:Microsoft.AspNetCore.Http.Features.IServiceProvidersFeature>: Provides access to an <xref:System.IServiceProvider> with scoped request services.</span></span>

<span data-ttu-id="f2a62-149"><xref:Microsoft.AspNetCore.Http.Features.ISessionFeature>: Definisce `ISessionFactory` le <xref:Microsoft.AspNetCore.Http.ISession> astrazioni e per supportare le sessioni utente.</span><span class="sxs-lookup"><span data-stu-id="f2a62-149"><xref:Microsoft.AspNetCore.Http.Features.ISessionFeature>: Defines `ISessionFactory` and <xref:Microsoft.AspNetCore.Http.ISession> abstractions for supporting user sessions.</span></span> <span data-ttu-id="f2a62-150">`ISessionFeature` viene implementato da <xref:Microsoft.AspNetCore.Session.SessionMiddleware> (vedere <xref:fundamentals/app-state> ).</span><span class="sxs-lookup"><span data-stu-id="f2a62-150">`ISessionFeature` is implemented by the <xref:Microsoft.AspNetCore.Session.SessionMiddleware> (see <xref:fundamentals/app-state>).</span></span>

<span data-ttu-id="f2a62-151"><xref:Microsoft.AspNetCore.Http.Features.ITlsConnectionFeature>: Definisce un'API per il recupero dei certificati client.</span><span class="sxs-lookup"><span data-stu-id="f2a62-151"><xref:Microsoft.AspNetCore.Http.Features.ITlsConnectionFeature>: Defines an API for retrieving client certificates.</span></span>

<span data-ttu-id="f2a62-152"><xref:Microsoft.AspNetCore.Http.Features.ITlsTokenBindingFeature>: Definisce i metodi per l'utilizzo dei parametri di associazione dei token TLS.</span><span class="sxs-lookup"><span data-stu-id="f2a62-152"><xref:Microsoft.AspNetCore.Http.Features.ITlsTokenBindingFeature>: Defines methods for working with TLS token binding parameters.</span></span>
   
::: moniker range=">= aspnetcore-2.2"
   
<span data-ttu-id="f2a62-153"><xref:Microsoft.AspNetCore.Http.Features.ITrackingConsentFeature>: Usato per eseguire query, concedere e prelevare il consenso dell'utente per l'archiviazione di informazioni sull'utente correlate all'attività e alla funzionalità del sito.</span><span class="sxs-lookup"><span data-stu-id="f2a62-153"><xref:Microsoft.AspNetCore.Http.Features.ITrackingConsentFeature>: Used to query, grant, and withdraw user consent regarding the storage of user information related to site activity and functionality.</span></span>
   
::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="f2a62-154">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="f2a62-154">Additional resources</span></span>

* <xref:fundamentals/servers/index>
* <xref:fundamentals/middleware/index>
