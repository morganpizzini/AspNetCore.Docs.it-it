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
# <a name="basic-json-apis-with-no-locroute-to-code-in-aspnet-core"></a>API JSON di base con Route-to-code in ASP.NET Core

Di [James Newton-King](https://github.com/jamesnk)

ASP.NET Core supporta diversi modi per creare API Web JSON:

* [ASP.NET Core API Web](xref:web-api/index) fornisce un Framework completo per la creazione di API. Un servizio viene creato ereditando da <xref:Microsoft.AspNetCore.Mvc.ControllerBase> . Alcune funzionalità fornite dal Framework includono l'associazione di modelli, la convalida, la negoziazione del contenuto, la formattazione di input e output e OpenAPI.
* Route-to-code è un'alternativa non Framework per ASP.NET Core API Web. Route-to-code connette [ASP.net core routing](xref:fundamentals/routing) diretto al codice. Il codice legge dalla richiesta e scrive la risposta. Route-to-code non ha funzionalità avanzate dell'API Web, ma non è necessaria alcuna configurazione per usarla.

Route-to-code è un approccio efficace per la creazione di API Web JSON semplici e di piccole dimensioni.

## <a name="create-json-web-apis"></a>Creare API Web JSON

ASP.NET Core fornisce metodi helper che semplificano la creazione di API Web JSON:

* <xref:Microsoft.AspNetCore.Http.HttpRequestJsonExtensions.HasJsonContentType%2A> Verifica l' `Content-Type` intestazione per un tipo di contenuto JSON.
* <xref:Microsoft.AspNetCore.Http.HttpRequestJsonExtensions.ReadFromJsonAsync%2A> legge JSON dalla richiesta e lo deserializza nel tipo specificato.
* <xref:Microsoft.AspNetCore.Http.HttpResponseJsonExtensions.WriteAsJsonAsync%2A> scrive il valore specificato come JSON nel corpo della risposta e imposta il tipo di contenuto della risposta su `application/json` .

Le API JSON semplici basate su route vengono specificate in *Startup.cs*. La route e la logica API sono configurate in <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> come parte della pipeline delle richieste di un'app.

### <a name="write-json-response"></a>Scrivi risposta JSON

Si consideri il codice seguente che configura un'API JSON per un'app:

[!code-csharp[](route-to-code/sample/Startup3.cs?name=snippet&highlight=6)]

Il codice precedente:

* Aggiunge un endpoint API GET HTTP con `/hello/{name:alpha}` come modello di route.
* Quando viene confrontata la route, l'API legge il `name` valore di route dalla richiesta.
* Scrive un tipo anonimo come risposta JSON con `WriteAsJsonAsync` .

### <a name="read-json-request"></a>Leggi richiesta JSON

`HasJsonContentType` e `ReadFromJsonAsync` possono essere usati per deserializzare una risposta JSON in un'API JSON basata su Route:

[!code-csharp[](route-to-code/sample/Startup2.cs?name=snippet&highlight=5,11)]

Il codice precedente:

* Aggiunge un endpoint HTTP POST API con `/weather` come modello di route.
* Quando viene confrontata la route, `HasJsonContentType` convalida il tipo di contenuto della richiesta. Un tipo di contenuto non JSON restituisce un codice di stato 415.
* Se il tipo di contenuto è JSON, il contenuto della richiesta viene deserializzato da `ReadFromJsonAsync` .

### <a name="configure-json-serialization"></a>Configurare la serializzazione JSON

Esistono due modi per personalizzare la serializzazione JSON:

* Le opzioni di serializzazione predefinite possono essere configurate con `JsonOptions` nel `Startup.ConfigureServices` metodo.
* `WriteAsJsonAsync` e `ReadFromJsonAsync` hanno overload che accettano un `JsonSerializerOptions` oggetto. Questo `JsonSerializerOptions` oggetto sostituisce le opzioni predefinite.

[!code-csharp[](route-to-code/sample/Startup6.cs?name=snippet)]

## <a name="authentication-and-authorization"></a>Autenticazione e autorizzazione

Route-to-code supporta l'autenticazione e l'autorizzazione. Gli attributi, ad esempio `[Authorize]` e `[AllowAnonymous]` , non possono essere inseriti in endpoint con mapping a un delegato della richiesta. Al contrario, i metadati di autorizzazione vengono aggiunti usando i `RequireAuthorization` `AllowAnonymous` metodi di estensione e.

[!code-csharp[](route-to-code/sample/Startup.cs?name=snippet&highlight=30)]

## <a name="dependency-injection"></a>Inserimento di dipendenze

L' [inserimento di dipendenze (di)](xref:fundamentals/dependency-injection) tramite un costruttore non è possibile con Route-to-code . L'API Web crea automaticamente un controller con i servizi inseriti nel costruttore. Un tipo non viene creato quando viene eseguito un endpoint, quindi i servizi devono essere risolti manualmente.

Le API basate su route possono usare <xref:System.IServiceProvider> per risolvere i servizi:

* I servizi temporanei e di durata con ambito, ad esempio `DbContext` , devono essere risolti da [HttpContext. RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) all'interno del delegato della richiesta di un endpoint.
* I servizi di durata singleton, ad esempio `ILogger` , possono essere risolti da [IEndpointRouteBuilder. ServiceProvider](xref:Microsoft.AspNetCore.Routing.IEndpointRouteBuilder.ServiceProvider). I servizi possono essere risolti all'esterno dei delegati di richiesta e condivisi tra endpoint.

[!code-csharp[](route-to-code/sample/Startup4.cs?name=snippet&highlight=3,7)]

Le API che usano in modo estensivo devono prendere in considerazione l'uso di un ASP.NET Core tipo di app che supporta il. Ad esempio, ASP.NET Core API Web. L'inserimento di servizi tramite il costruttore di un controller è più semplice rispetto alla risoluzione manuale dei servizi.

## <a name="api-project-structure"></a>Struttura del progetto API

Le API basate su route non devono trovarsi in *Startup.cs*. Le API possono essere inserite in altri file e mappate all'avvio con `UseEndpoints` . Questo approccio riduce le dimensioni del file di configurazione di avvio.

Si consideri la classe statica seguente `UserApi` che definisce un `Map` metodo. Il metodo esegue il mapping delle API basate su route.

[!code-csharp[](route-to-code/sample/UserApi.cs?name=snippet)]

Nel `Startup.Configure` metodo, il `Map` metodo e i metodi statici di altre classi vengono chiamati in `UseEndpoints` :

[!code-csharp[](route-to-code/sample/Startup5.cs?name=snippet)]

## <a name="notable-missing-features-compared-to-web-api"></a>Caratteristiche mancanti rilevanti rispetto all'API Web

Route-to-code è progettato per le API JSON di base. Non ha il supporto per molte delle funzionalità avanzate offerte dall'API Web ASP.NET Core.

Le funzionalità non fornite da Route-to-code includono:

* Associazione di modelli
* Convalida modello
* OpenAPI/Swagger
* Negoziazione del contenuto
* Inserimento delle dipendenze del costruttore
* `ProblemDetails` ([https://tools.ietf.org/html/rfc7807](RFC 7807))

Si consiglia di usare [ASP.NET Core API Web](xref:web-api/index) per creare un'API se sono necessarie alcune funzionalità nell'elenco precedente.

## <a name="additional-resources"></a>Risorse aggiuntive

* <xref:web-api/index>
* <xref:fundamentals/routing>
* <xref:fundamentals/dependency-injection>
