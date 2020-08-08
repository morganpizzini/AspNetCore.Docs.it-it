---
title: Novità di ASP.NET Core 2.2
author: rick-anderson
description: Informazioni sulle nuove funzionalità di ASP.NET Core 2.2.
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
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
uid: aspnetcore-2.2
ms.openlocfilehash: 354839abe295b4adcbf6becfd3bd926330f0823f
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/08/2020
ms.locfileid: "88021146"
---
# <a name="whats-new-in-aspnet-core-22"></a>Novità di ASP.NET Core 2.2

Questo articolo evidenzia le modifiche più significative apportate ad ASP.NET Core 2.2, con collegamenti alla relativa documentazione.

## <a name="openapi-analyzers--conventions"></a>Convenzioni e analizzatori di OpenAPI

OpenAPI, chiamata in precedenza Swagger, è una specifica indipendente dal linguaggio per la descrizione delle API REST. L'ecosistema OpenAPI include strumenti che consentono di individuare, testare e produrre codice client usando la specifica. Il supporto per la generazione e la visualizzazione di documenti OpenAPI in ASP.NET Core MVC viene fornito tramite progetti basati sulla community, ad esempio [NSwag](https://github.com/RicoSuter/NSwag) e [Swashbuckle. AspNetCore](https://github.com/domaindrivendev/Swashbuckle.AspNetCore). ASP.NET Core 2.2 offre strumenti ottimizzati e migliori esperienze con il runtime per la creazione di documenti OpenAPI.

Per altre informazioni, vedere le seguenti risorse:

* <xref:web-api/advanced/analyzers>
* <xref:web-api/advanced/conventions>
* [ASP.NET Core 2.2.0-preview1: OpenAPI Analyzers & Conventions](https://blogs.msdn.microsoft.com/webdev/2018/08/23/asp-net-core-2-20-preview1-open-api-analyzers-conventions/)

## <a name="problem-details-support"></a>Assistenza per i dettagli del problema

Con ASP.NET Core 2.1 è stata introdotta la classe `ProblemDetails`, basata sulla specifica [RFC 7807](https://tools.ietf.org/html/rfc7807), il cui compito è trasmettere i dettagli di un errore con una risposta HTTP. Nella versione 2.2 `ProblemDetails` è la risposta standard per i codici di errore client nei controller a cui è stato attribuito `ApiControllerAttribute`. Un'interfaccia `IActionResult` che restituisce un codice di stato per l'errore client (4xx) ora restituisce un corpo `ProblemDetails`. Il risultato include anche un ID di correlazione che può essere utilizzato per correlare l'errore usando i log richieste. Per gli errori client, per impostazione predefinita `ProducesResponseType` utilizza `ProblemDetails` come tipo di risposta. Ciò è documentato nell'output generato in OpenAPI/Swagger usando NSwag o Swashbuckle.AspNetCore.

## <a name="endpoint-routing"></a>Routing di endpoint

ASP.NET Core 2.2 prevede un nuovo sistema di *routing di endpoint* che rende più efficace l'invio delle richieste. Le modifiche includono una nuova funzione di generazione dei collegamenti per i membri dell'API e l'aggiunta di trasformatori per i parametri di routing.

Per altre informazioni, vedere le seguenti risorse:

* [Endpoint routing in 2.2](https://blogs.msdn.microsoft.com/webdev/2018/08/27/asp-net-core-2-2-0-preview1-endpoint-routing/) (Routing di endpoint in 2.2)
* [Parameter transformers](https://www.hanselman.com/blog/ASPNETCore22ParameterTransformersForCleanURLGenerationAndSlugsInRazorPagesOrMVC.aspx) (Trasformatori dei parametri), sezione **Routing**
* [Differenze tra IRouter e routing basati su endpoint](xref:fundamentals/routing?view=aspnetcore-2.2#differences-from-earlier-versions-of-routing)

## <a name="health-checks"></a>Controlli di integrità

Un nuovo servizio di controllo di integrità rende più semplice usare ASP.NET Core in ambienti che richiedono controlli di integrità, ad esempio Kubernetes. I controlli di integrità includono middleware e un set di librerie che definiscono il servizio e un'astrazione `IHealthCheck`.

Tramite i controlli di integrità, un agente di orchestrazione o un servizio di bilanciamento del carico determina rapidamente se un sistema risponde normalmente alle richieste. Un agente di orchestrazione potrebbe rispondere a un controllo di integrità non superato arrestando una distribuzione in sequenza o riavviando un contenitore. Un servizio di bilanciamento del carico potrebbe reagire a un controllo di integrità deviando il traffico dall'istanza con errori del servizio.

I controlli di integrità vengono esposti da un'applicazione come endpoint HTTP utilizzato dai sistemi di monitoraggio. È possibile configurare i controlli di integrità per svariati scenari di monitoraggio in tempo reale e sistemi di monitoraggio. Il servizio dei controlli di integrità si integra con il [progetto BeatPulse](https://github.com/Xabaril/BeatPulse) che rende più semplice aggiungere controlli per decine di dipendenze e di sistemi più diffusi.

Per altre informazioni, vedere [Controlli di integrità in ASP.NET Core](xref:host-and-deploy/health-checks).

## <a name="http2-in-kestrel"></a>HTTP/2 in Kestrel

In ASP.NET Core 2.2 è stato aggiunto il supporto per HTTP/2.

HTTP/2 è una revisione principale del protocollo HTTP. Le funzionalità importanti di HTTP/2 includono:

* Supporto per la compressione dell'intestazione.
* Flussi completamente multiplexati in una singola connessione.

Quando HTTP/2 conserva la semantica HTTP (ad esempio, intestazioni e metodi HTTP), si tratta di una modifica sostanziale rispetto a HTTP/1. x sul modo in cui i dati vengono incorniciati e inviati tra il client e il server.

Come conseguenza di questa modifica del framing, i server e i client devono negoziare la versione del protocollo usata. ALPN (Application Layer Protocol Negotiation) è un'estensione TLS che consente al server e al client di negoziare la versione del protocollo usata nel corso dell'handshake TLS. Sebbene sia possibile che il server e il client dispongano di conoscenze precedenti rispetto al protocollo, tutti i principali browser supportano ALPN e la considerano l'unico modo per stabilire una connessione HTTP/2.

Per altre informazioni, vedere [Supporto per HTTP/2](xref:fundamentals/servers/index?view=aspnetcore-2.2#http2-support).

## <a name="kestrel-configuration"></a>Configurazione di Kestrel

Nelle versioni precedenti di ASP.NET Core, le opzioni di Kestrel venivano configurate chiamando `UseKestrel`. Nella versione 2.2, le opzioni di Kestrel sono configurate chiamando `ConfigureKestrel` nel generatore di host. Questa modifica risolve un problema legato all'ordine delle registrazioni `IServer` per l'hosting in-process. Per altre informazioni, vedere le seguenti risorse:

* [Mitigate UseIIS conflict](https://github.com/aspnet/KestrelHttpServer/issues/2760) (Ridurre i conflitti UseIIS)
* [Configurare le opzioni del server Kestrel con ConfigureKestrel](xref:fundamentals/servers/kestrel?view=aspnetcore-2.2#how-to-use-kestrel-in-aspnet-core-apps)

## <a name="iis-in-process-hosting"></a>Hosting in-process IIS

Nelle versioni precedenti di ASP.NET Core, IIS funge da proxy inverso. Nella versione 2.2, il modulo ASP.NET Core può avviare CoreCLR e ospitare un'app all'interno del processo di lavoro IIS (*w3wp.exe*). Durante l'esecuzione con IIS, l'hosting in-process offre prestazioni e risultati di diagnostica migliori.

Per altre informazioni, vedere [Hosting in-process per IIS](xref:host-and-deploy/aspnet-core-module?view=aspnetcore-2.2#in-process-hosting-model).

## <a name="no-locsignalr-java-client"></a>SignalRClient Java

ASP.NET Core 2,2 introduce un client Java per SignalR . Questo client supporta la connessione a un SignalR server di ASP.NET Core dal codice Java, incluse le app Android.

Per ulteriori informazioni, vedere [ASP.NET Core SignalR Java client](https://docs.microsoft.com/aspnet/core/signalr/java-client?view=aspnetcore-2.2).

## <a name="cors-improvements"></a>Miglioramenti a CORS

Nelle versioni precedenti di ASP.NET Core, middleware CORS consente l'invio delle intestazioni `Accept`, `Accept-Language`, `Content-Language` e `Origin` indipendentemente dai valori configurati in `CorsPolicy.Headers`. Nella versione 2.2 è possibile stabilire una corrispondenza dei criteri di middleware CORS solo quando le intestazioni inviate in `Access-Control-Request-Headers` corrispondono esattamente alle intestazioni indicate in `WithHeaders`.

Per altre informazioni, vedere [CORS Middleware](xref:security/cors?view=aspnetcore-2.2#set-the-allowed-request-headers).

## <a name="response-compression"></a>Compressione delle risposte

Con ASP.NET Core 2.2 è possibile comprimere le risposte con il [formato di compressione Brotli](https://tools.ietf.org/html/rfc7932).

Per altre informazioni, vedere [Brotli Compression Provider](xref:performance/response-compression?view=aspnetcore-2.2#brotli-compression-provider) (Provider compressione Brotli).

## <a name="project-templates"></a>Modelli di progetto

I modelli di progetto Web ASP.NET Core sono stati aggiornati a [Bootstrap 4](https://getbootstrap.com/docs/4.1/migration/) e [Angular 6](https://blog.angular.io/version-6-of-angular-now-available-cc56b0efa7a4). Il nuovo aspetto è visivamente più immediato e rende più facile visualizzare le strutture importanti dell'app.

![Pagina Home o di indice](~/tutorials/razor-pages/razor-pages-start/_static/home2.2.png)

## <a name="validation-performance"></a>Prestazioni della convalida

Il sistema di convalida di MVC è progettato per essere estendibile e flessibile, consentendo di determinare in base alle singole richieste quali validator si applicano a un determinato modello. Ciò è molto utile per la creazione di provider di convalida complessi. Tuttavia, nel caso più comune, un'applicazione utilizza solo i validator incorporati e non richiede questa flessibilità aggiuntiva. I validator incorporati includono DataAnnotations, ad esempio [Required], [StringLength] e `IValidatableObject`.

In ASP.NET Core 2.2 MVC può bloccare la convalida se determina che un grafico del modello specifico non richiede la convalida. Ignorando la convalida si ottengono miglioramenti significativi per i modelli che non hanno o non possono avere alcun validator. Ciò riguarda oggetti quali raccolte di primitive (`byte[]`, `string[]`, `Dictionary<string, string>` e così via) o oggetti grafici complessi senza molti validator.

## <a name="http-client-performance"></a>Prestazioni del client HTTP

In ASP.NET Core 2.2 le prestazioni di `SocketsHttpHandler` sono state migliorate riducendo la contesa di blocco dei pool di connessioni. Per le app che generano molte richieste HTTP in uscita, ad esempio alcune architetture di microservizi, la velocità effettiva risulta migliorata. In condizioni di carico, la velocità effettiva di `HttpClient` può essere migliorata fino al 60% in Linux e al 20% in Windows.

Per altre informazioni, vedere [la richiesta pull responsabile di questo miglioramento](https://github.com/dotnet/corefx/pull/32568).

## <a name="additional-information"></a>Informazioni aggiuntive

Per l'elenco completo delle modifiche, vedere le [Note sulla versione di ASP.NET Core 2.2](https://github.com/dotnet/aspnetcore/releases/tag/2.2.0).
