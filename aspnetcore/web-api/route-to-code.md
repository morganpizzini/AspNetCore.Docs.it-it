---
title: API JSON di base con Route-to-code in ASP.NET Core
author: jamesnk
description: Informazioni su come usare i Route-to-code metodi di estensione e JSON per creare API Web JSON semplici.
monikerRange: '>= aspnetcore-5.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 11/30/2020
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
- Route-to-code
uid: web-api/route-to-code
ms.openlocfilehash: 1f5f532053f8f5ca7f73df8c1a910a484e2488d9
ms.sourcegitcommit: 0bcc0d6df3145a0727da7c4be2f4bda8f27eeaa3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96513096"
---
# <a name="basic-json-apis-with-no-locroute-to-code-in-aspnet-core"></a><span data-ttu-id="ef16d-103">API JSON di base con Route-to-code in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ef16d-103">Basic JSON APIs with Route-to-code in ASP.NET Core</span></span>

<span data-ttu-id="ef16d-104">Di [James Newton-King](https://github.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="ef16d-104">By [James Newton-King](https://github.com/jamesnk)</span></span>

<span data-ttu-id="ef16d-105">ASP.NET Core supporta diversi modi per creare API Web JSON:</span><span class="sxs-lookup"><span data-stu-id="ef16d-105">ASP.NET Core supports a number of ways of creating JSON web APIs:</span></span>

* <span data-ttu-id="ef16d-106">[ASP.NET Core API Web](xref:web-api/index) fornisce un Framework completo per la creazione di API.</span><span class="sxs-lookup"><span data-stu-id="ef16d-106">[ASP.NET Core web API](xref:web-api/index) provides a complete framework for creating APIs.</span></span> <span data-ttu-id="ef16d-107">Un servizio viene creato ereditando da <xref:Microsoft.AspNetCore.Mvc.ControllerBase> .</span><span class="sxs-lookup"><span data-stu-id="ef16d-107">A service is created by inheriting from <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span></span> <span data-ttu-id="ef16d-108">Alcune funzionalità fornite dal Framework includono l'associazione di modelli, la convalida, la negoziazione del contenuto, la formattazione di input e output e OpenAPI.</span><span class="sxs-lookup"><span data-stu-id="ef16d-108">Some features provided by the framework include model binding, validation, content negotiation, input and output formatting, and OpenAPI.</span></span>
* <span data-ttu-id="ef16d-109">Route-to-code è un'alternativa non Framework per ASP.NET Core API Web.</span><span class="sxs-lookup"><span data-stu-id="ef16d-109">Route-to-code is a non-framework alternative to ASP.NET Core web API.</span></span> <span data-ttu-id="ef16d-110">Route-to-code connette [ASP.net core routing](xref:fundamentals/routing) diretto al codice.</span><span class="sxs-lookup"><span data-stu-id="ef16d-110">Route-to-code connects [ASP.NET Core routing](xref:fundamentals/routing) directly to your code.</span></span> <span data-ttu-id="ef16d-111">Il codice legge dalla richiesta e scrive la risposta.</span><span class="sxs-lookup"><span data-stu-id="ef16d-111">Your code reads from the request and writes the response.</span></span> <span data-ttu-id="ef16d-112">Route-to-code non ha funzionalità avanzate dell'API Web, ma non è necessaria alcuna configurazione per usarla.</span><span class="sxs-lookup"><span data-stu-id="ef16d-112">Route-to-code doesn't have web API's advanced features, but there's also no configuration required to use it.</span></span>

<span data-ttu-id="ef16d-113">Route-to-code è un approccio efficace per la creazione di API Web JSON semplici e di piccole dimensioni.</span><span class="sxs-lookup"><span data-stu-id="ef16d-113">Route-to-code is a good approach when building small and basic JSON web APIs.</span></span>

## <a name="create-json-web-apis"></a><span data-ttu-id="ef16d-114">Creare API Web JSON</span><span class="sxs-lookup"><span data-stu-id="ef16d-114">Create JSON web APIs</span></span>

<span data-ttu-id="ef16d-115">ASP.NET Core fornisce metodi helper che semplificano la creazione di API Web JSON:</span><span class="sxs-lookup"><span data-stu-id="ef16d-115">ASP.NET Core provides helper methods that ease the creation of JSON web APIs:</span></span>

* <span data-ttu-id="ef16d-116"><xref:Microsoft.AspNetCore.Http.HttpRequestJsonExtensions.HasJsonContentType%2A> Verifica l' `Content-Type` intestazione per un tipo di contenuto JSON.</span><span class="sxs-lookup"><span data-stu-id="ef16d-116"><xref:Microsoft.AspNetCore.Http.HttpRequestJsonExtensions.HasJsonContentType%2A> checks the `Content-Type` header for a JSON content type.</span></span>
* <span data-ttu-id="ef16d-117"><xref:Microsoft.AspNetCore.Http.HttpRequestJsonExtensions.ReadFromJsonAsync%2A> legge JSON dalla richiesta e lo deserializza nel tipo specificato.</span><span class="sxs-lookup"><span data-stu-id="ef16d-117"><xref:Microsoft.AspNetCore.Http.HttpRequestJsonExtensions.ReadFromJsonAsync%2A> reads JSON from the request and deserializes it to the specified type.</span></span>
* <span data-ttu-id="ef16d-118"><xref:Microsoft.AspNetCore.Http.HttpResponseJsonExtensions.WriteAsJsonAsync%2A> scrive il valore specificato come JSON nel corpo della risposta e imposta il tipo di contenuto della risposta su `application/json` .</span><span class="sxs-lookup"><span data-stu-id="ef16d-118"><xref:Microsoft.AspNetCore.Http.HttpResponseJsonExtensions.WriteAsJsonAsync%2A> writes the specified value as JSON to the response body and sets the response content type to `application/json`.</span></span>

<span data-ttu-id="ef16d-119">Le API JSON semplici basate su route vengono specificate in *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="ef16d-119">Lightweight, route-based JSON APIs are specified in *Startup.cs*.</span></span> <span data-ttu-id="ef16d-120">La route e la logica API sono configurate in <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> come parte della pipeline delle richieste di un'app.</span><span class="sxs-lookup"><span data-stu-id="ef16d-120">The route and the API logic are configured in <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> as part of an app's request pipeline.</span></span>

### <a name="write-json-response"></a><span data-ttu-id="ef16d-121">Scrivi risposta JSON</span><span class="sxs-lookup"><span data-stu-id="ef16d-121">Write JSON response</span></span>

<span data-ttu-id="ef16d-122">Si consideri il codice seguente che configura un'API JSON per un'app:</span><span class="sxs-lookup"><span data-stu-id="ef16d-122">Consider the following code that configures a JSON API for an app:</span></span>

[!code-csharp[](route-to-code/sample/Startup3.cs?name=snippet&highlight=6)]

<span data-ttu-id="ef16d-123">Il codice precedente:</span><span class="sxs-lookup"><span data-stu-id="ef16d-123">The preceding code:</span></span>

* <span data-ttu-id="ef16d-124">Aggiunge un endpoint API GET HTTP con `/hello/{name:alpha}` come modello di route.</span><span class="sxs-lookup"><span data-stu-id="ef16d-124">Adds an HTTP GET API endpoint with `/hello/{name:alpha}` as the route template.</span></span>
* <span data-ttu-id="ef16d-125">Quando viene confrontata la route, l'API legge il `name` valore di route dalla richiesta.</span><span class="sxs-lookup"><span data-stu-id="ef16d-125">When the route is matched, the API reads the `name` route value from the request.</span></span>
* <span data-ttu-id="ef16d-126">Scrive un tipo anonimo come risposta JSON con `WriteAsJsonAsync` .</span><span class="sxs-lookup"><span data-stu-id="ef16d-126">Writes an anonymous type as a JSON response with `WriteAsJsonAsync`.</span></span>

### <a name="read-json-request"></a><span data-ttu-id="ef16d-127">Leggi richiesta JSON</span><span class="sxs-lookup"><span data-stu-id="ef16d-127">Read JSON request</span></span>

<span data-ttu-id="ef16d-128">`HasJsonContentType` e `ReadFromJsonAsync` possono essere usati per deserializzare una risposta JSON in un'API JSON basata su Route:</span><span class="sxs-lookup"><span data-stu-id="ef16d-128">`HasJsonContentType` and `ReadFromJsonAsync` can be used to deserialize a JSON response in a route-based JSON API:</span></span>

[!code-csharp[](route-to-code/sample/Startup2.cs?name=snippet&highlight=5,11)]

<span data-ttu-id="ef16d-129">Il codice precedente:</span><span class="sxs-lookup"><span data-stu-id="ef16d-129">The preceding code:</span></span>

* <span data-ttu-id="ef16d-130">Aggiunge un endpoint HTTP POST API con `/weather` come modello di route.</span><span class="sxs-lookup"><span data-stu-id="ef16d-130">Adds an HTTP POST API endpoint with `/weather` as the route template.</span></span>
* <span data-ttu-id="ef16d-131">Quando viene confrontata la route, `HasJsonContentType` convalida il tipo di contenuto della richiesta.</span><span class="sxs-lookup"><span data-stu-id="ef16d-131">When the route is matched, `HasJsonContentType` validates the request content type.</span></span> <span data-ttu-id="ef16d-132">Un tipo di contenuto non JSON restituisce un codice di stato 415.</span><span class="sxs-lookup"><span data-stu-id="ef16d-132">A non-JSON content type returns a 415 status code.</span></span>
* <span data-ttu-id="ef16d-133">Se il tipo di contenuto è JSON, il contenuto della richiesta viene deserializzato da `ReadFromJsonAsync` .</span><span class="sxs-lookup"><span data-stu-id="ef16d-133">If the content type is JSON, the request content is deserialized by `ReadFromJsonAsync`.</span></span>

### <a name="configure-json-serialization"></a><span data-ttu-id="ef16d-134">Configurare la serializzazione JSON</span><span class="sxs-lookup"><span data-stu-id="ef16d-134">Configure JSON serialization</span></span>

<span data-ttu-id="ef16d-135">Esistono due modi per personalizzare la serializzazione JSON:</span><span class="sxs-lookup"><span data-stu-id="ef16d-135">There are two ways to customize JSON serialization:</span></span>

* <span data-ttu-id="ef16d-136">Le opzioni di serializzazione predefinite possono essere configurate con `JsonOptions` nel `Startup.ConfigureServices` metodo.</span><span class="sxs-lookup"><span data-stu-id="ef16d-136">Default serialization options can be configured with `JsonOptions` in the `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="ef16d-137">`WriteAsJsonAsync` e `ReadFromJsonAsync` hanno overload che accettano un `JsonSerializerOptions` oggetto.</span><span class="sxs-lookup"><span data-stu-id="ef16d-137">`WriteAsJsonAsync` and `ReadFromJsonAsync` have overloads that accept a `JsonSerializerOptions` object.</span></span> <span data-ttu-id="ef16d-138">Questo `JsonSerializerOptions` oggetto sostituisce le opzioni predefinite.</span><span class="sxs-lookup"><span data-stu-id="ef16d-138">This `JsonSerializerOptions` object overrides the default options.</span></span>

[!code-csharp[](route-to-code/sample/Startup6.cs?name=snippet)]

## <a name="authentication-and-authorization"></a><span data-ttu-id="ef16d-139">Autenticazione e autorizzazione</span><span class="sxs-lookup"><span data-stu-id="ef16d-139">Authentication and authorization</span></span>

<span data-ttu-id="ef16d-140">Route-to-code supporta l'autenticazione e l'autorizzazione.</span><span class="sxs-lookup"><span data-stu-id="ef16d-140">Route-to-code supports authentication and authorization.</span></span> <span data-ttu-id="ef16d-141">Gli attributi, ad esempio `[Authorize]` e `[AllowAnonymous]` , non possono essere inseriti in endpoint con mapping a un delegato della richiesta.</span><span class="sxs-lookup"><span data-stu-id="ef16d-141">Attributes, such as `[Authorize]` and `[AllowAnonymous]`, can't be placed on endpoints that map to a request delegate.</span></span> <span data-ttu-id="ef16d-142">Al contrario, i metadati di autorizzazione vengono aggiunti usando i `RequireAuthorization` `AllowAnonymous` metodi di estensione e.</span><span class="sxs-lookup"><span data-stu-id="ef16d-142">Instead, authorization metadata is added using the `RequireAuthorization` and `AllowAnonymous` extension methods.</span></span>

[!code-csharp[](route-to-code/sample/Startup.cs?name=snippet&highlight=30)]

## <a name="dependency-injection"></a><span data-ttu-id="ef16d-143">Inserimento di dipendenze</span><span class="sxs-lookup"><span data-stu-id="ef16d-143">Dependency injection</span></span>

<span data-ttu-id="ef16d-144">L' [inserimento di dipendenze (di)](xref:fundamentals/dependency-injection) tramite un costruttore non è possibile con Route-to-code .</span><span class="sxs-lookup"><span data-stu-id="ef16d-144">[Dependency injection (DI)](xref:fundamentals/dependency-injection) using a constructor isn't possible with Route-to-code.</span></span> <span data-ttu-id="ef16d-145">L'API Web crea automaticamente un controller con i servizi inseriti nel costruttore.</span><span class="sxs-lookup"><span data-stu-id="ef16d-145">Web API creates a controller for you with services injected into the constructor.</span></span> <span data-ttu-id="ef16d-146">Un tipo non viene creato quando viene eseguito un endpoint, quindi i servizi devono essere risolti manualmente.</span><span class="sxs-lookup"><span data-stu-id="ef16d-146">A type isn't created when an endpoint is executed, so services must be resolved manually.</span></span>

<span data-ttu-id="ef16d-147">Le API basate su route possono usare <xref:System.IServiceProvider> per risolvere i servizi:</span><span class="sxs-lookup"><span data-stu-id="ef16d-147">Route-based APIs can use <xref:System.IServiceProvider> to resolve services:</span></span>

* <span data-ttu-id="ef16d-148">I servizi temporanei e di durata con ambito, ad esempio `DbContext` , devono essere risolti da [HttpContext. RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) all'interno del delegato della richiesta di un endpoint.</span><span class="sxs-lookup"><span data-stu-id="ef16d-148">Transient and scoped lifetime services, such as `DbContext`, must be resolved from [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) inside an endpoint's request delegate.</span></span>
* <span data-ttu-id="ef16d-149">I servizi di durata singleton, ad esempio `ILogger` , possono essere risolti da [IEndpointRouteBuilder. ServiceProvider](xref:Microsoft.AspNetCore.Routing.IEndpointRouteBuilder.ServiceProvider).</span><span class="sxs-lookup"><span data-stu-id="ef16d-149">Singleton lifetime services, such as `ILogger`, can be resolved from [IEndpointRouteBuilder.ServiceProvider](xref:Microsoft.AspNetCore.Routing.IEndpointRouteBuilder.ServiceProvider).</span></span> <span data-ttu-id="ef16d-150">I servizi possono essere risolti all'esterno dei delegati di richiesta e condivisi tra endpoint.</span><span class="sxs-lookup"><span data-stu-id="ef16d-150">Services can be resolved outside of request delegates and shared between endpoints.</span></span>

[!code-csharp[](route-to-code/sample/Startup4.cs?name=snippet&highlight=3,7)]

<span data-ttu-id="ef16d-151">Le API che usano in modo estensivo devono prendere in considerazione l'uso di un ASP.NET Core tipo di app che supporta il.</span><span class="sxs-lookup"><span data-stu-id="ef16d-151">APIs that use DI extensively should consider using an ASP.NET Core app type that supports DI.</span></span> <span data-ttu-id="ef16d-152">Ad esempio, ASP.NET Core API Web.</span><span class="sxs-lookup"><span data-stu-id="ef16d-152">For example, ASP.NET Core web API.</span></span> <span data-ttu-id="ef16d-153">L'inserimento di servizi tramite il costruttore di un controller è più semplice rispetto alla risoluzione manuale dei servizi.</span><span class="sxs-lookup"><span data-stu-id="ef16d-153">Service injection using a controller's constructor is easier than manually resolving services.</span></span>

## <a name="api-project-structure"></a><span data-ttu-id="ef16d-154">Struttura del progetto API</span><span class="sxs-lookup"><span data-stu-id="ef16d-154">API project structure</span></span>

<span data-ttu-id="ef16d-155">Le API basate su route non devono trovarsi in *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="ef16d-155">Route-based APIs don't have to be located in *Startup.cs*.</span></span> <span data-ttu-id="ef16d-156">Le API possono essere inserite in altri file e mappate all'avvio con `UseEndpoints` .</span><span class="sxs-lookup"><span data-stu-id="ef16d-156">APIs can be placed in other files and mapped at startup with `UseEndpoints`.</span></span> <span data-ttu-id="ef16d-157">Questo approccio riduce le dimensioni del file di configurazione di avvio.</span><span class="sxs-lookup"><span data-stu-id="ef16d-157">This approach reduces the startup configuration file size.</span></span>

<span data-ttu-id="ef16d-158">Si consideri la classe statica seguente `UserApi` che definisce un `Map` metodo.</span><span class="sxs-lookup"><span data-stu-id="ef16d-158">Consider the following static `UserApi` class that defines a `Map` method.</span></span> <span data-ttu-id="ef16d-159">Il metodo esegue il mapping delle API basate su route.</span><span class="sxs-lookup"><span data-stu-id="ef16d-159">The method maps route-based APIs.</span></span>

[!code-csharp[](route-to-code/sample/UserApi.cs?name=snippet)]

<span data-ttu-id="ef16d-160">Nel `Startup.Configure` metodo, il `Map` metodo e i metodi statici di altre classi vengono chiamati in `UseEndpoints` :</span><span class="sxs-lookup"><span data-stu-id="ef16d-160">In the `Startup.Configure` method, the `Map` method and other class's static methods are called in `UseEndpoints`:</span></span>

[!code-csharp[](route-to-code/sample/Startup5.cs?name=snippet)]

## <a name="notable-missing-features-compared-to-web-api"></a><span data-ttu-id="ef16d-161">Caratteristiche mancanti rilevanti rispetto all'API Web</span><span class="sxs-lookup"><span data-stu-id="ef16d-161">Notable missing features compared to Web API</span></span>

<span data-ttu-id="ef16d-162">Route-to-code è progettato per le API JSON di base.</span><span class="sxs-lookup"><span data-stu-id="ef16d-162">Route-to-code is designed for basic JSON APIs.</span></span> <span data-ttu-id="ef16d-163">Non ha il supporto per molte delle funzionalità avanzate offerte dall'API Web ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ef16d-163">It doesn't have support for many of the advanced features provided by ASP.NET Core Web API.</span></span>

<span data-ttu-id="ef16d-164">Le funzionalità non fornite da Route-to-code includono:</span><span class="sxs-lookup"><span data-stu-id="ef16d-164">Features not provided by Route-to-code include:</span></span>

* <span data-ttu-id="ef16d-165">Associazione di modelli</span><span class="sxs-lookup"><span data-stu-id="ef16d-165">Model binding</span></span>
* <span data-ttu-id="ef16d-166">Convalida modello</span><span class="sxs-lookup"><span data-stu-id="ef16d-166">Model validation</span></span>
* <span data-ttu-id="ef16d-167">OpenAPI/Swagger</span><span class="sxs-lookup"><span data-stu-id="ef16d-167">OpenAPI/Swagger</span></span>
* <span data-ttu-id="ef16d-168">Negoziazione del contenuto</span><span class="sxs-lookup"><span data-stu-id="ef16d-168">Content negotiation</span></span>
* <span data-ttu-id="ef16d-169">Inserimento delle dipendenze del costruttore</span><span class="sxs-lookup"><span data-stu-id="ef16d-169">Constructor dependency injection</span></span>
* <span data-ttu-id="ef16d-170">`ProblemDetails` ([https://tools.ietf.org/html/rfc7807](RFC 7807))</span><span class="sxs-lookup"><span data-stu-id="ef16d-170">`ProblemDetails` ([https://tools.ietf.org/html/rfc7807](RFC 7807))</span></span>

<span data-ttu-id="ef16d-171">Si consiglia di usare [ASP.NET Core API Web](xref:web-api/index) per creare un'API se sono necessarie alcune funzionalità nell'elenco precedente.</span><span class="sxs-lookup"><span data-stu-id="ef16d-171">Consider using [ASP.NET Core web API](xref:web-api/index) to create an API if it requires some of the features in the preceding list.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ef16d-172">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="ef16d-172">Additional resources</span></span>

* <xref:web-api/index>
* <xref:fundamentals/routing>
* <xref:fundamentals/dependency-injection>
