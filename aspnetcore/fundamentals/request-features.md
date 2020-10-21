---
title: Funzionalità di richiesta in ASP.NET Core
author: ardalis
description: Dettagli dell'implementazione di server Web relativi alle richieste e alle risposte HTTP definiti nelle interfacce per ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 10/20/2020
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
ms.openlocfilehash: 879b775ba2998ee803708ebf231b5fcd363b811c
ms.sourcegitcommit: b5ebaf42422205d212e3dade93fcefcf7f16db39
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92326435"
---
# <a name="request-features-in-aspnet-core"></a>Funzionalità di richiesta in ASP.NET Core

Di [Steve Smith](https://ardalis.com/)

L' `HttpContext` API usata dalle applicazioni e dal middleware per elaborare le richieste ha un livello di astrazione undernieth denominato *interfacce di funzionalità*. Ogni interfaccia della funzionalità fornisce un subset granulare delle funzionalità esposte da `HttpContext` . Queste interfacce possono essere aggiunte, modificate, sottoposto a Wrapped, sostituite o addirittura rimosse dal server o dal middleware, perché la richiesta viene elaborata senza dover implementare nuovamente l'intero `HttpContext` . Possono anche essere usati per simulare le funzionalità durante i test.

## <a name="feature-collections"></a>Raccolte di funzionalità

La <xref:Microsoft.AspNetCore.Http.HttpContext.Features> proprietà di `HttpContext` fornisce l'accesso alla raccolta di interfacce di funzionalità per la richiesta corrente. Poiché l'insieme di funzionalità è modificabile anche all'interno del contesto di una richiesta, è possibile usare il middleware per modificare la raccolta e aggiungere il supporto di altre funzionalità. Alcune funzionalità avanzate sono disponibili solo accedendo all'interfaccia associata tramite la raccolta di funzionalità.

## <a name="feature-interfaces"></a>Interfacce di funzionalità

ASP.NET Core definisce una serie di interfacce di funzionalità HTTP comuni in <xref:Microsoft.AspNetCore.Http.Features?displayProperty=fullName> , condivise da vari server e middleware per identificare le funzionalità supportate. I server e il middleware possono anche fornire interfacce personalizzate con funzionalità aggiuntive.

La maggior parte delle interfacce di funzionalità forniscono funzionalità facoltative e leggere e le `HttpContext` API associate forniscono impostazioni predefinite se la funzionalità non è presente. Alcune interfacce sono indicate nel contenuto seguente come richiesto perché forniscono la funzionalità di base di richiesta e risposta e devono essere implementate per elaborare la richiesta.

Di seguito sono riportate le interfacce delle funzionalità seguenti <xref:Microsoft.AspNetCore.Http.Features?displayProperty=fullName> :

<xref:Microsoft.AspNetCore.Http.Features.IHttpRequestFeature>: Definisce la struttura di una richiesta HTTP, inclusi il protocollo, il percorso, la stringa di query, le intestazioni e il corpo. Questa funzionalità è necessaria per elaborare le richieste.

<xref:Microsoft.AspNetCore.Http.Features.IHttpResponseFeature>: Definisce la struttura di una risposta HTTP, inclusi il codice di stato, le intestazioni e il corpo della risposta. Questa funzionalità è necessaria per elaborare le richieste.

::: moniker range=">= aspnetcore-3.0"

<xref:Microsoft.AspNetCore.Http.Features.IHttpResponseBodyFeature>: Definisce modi diversi per scrivere il corpo della risposta, usando un oggetto `Stream` , un oggetto `PipeWriter` o un file. Questa funzionalità è necessaria per elaborare le richieste. Sostituisce `IHttpResponseFeature.Body` e `IHttpSendFileFeature` .

::: moniker-end

<xref:Microsoft.AspNetCore.Http.Features.Authentication.IHttpAuthenticationFeature>: Include l'oggetto <xref:System.Security.Claims.ClaimsPrincipal> attualmente associato alla richiesta.

<xref:Microsoft.AspNetCore.Http.Features.IFormFeature>: Utilizzato per analizzare e memorizzare nella cache gli invii di form HTTP e multipart in ingresso.

::: moniker range=">= aspnetcore-2.0"

<xref:Microsoft.AspNetCore.Http.Features.IHttpBodyControlFeature>: Usato per controllare se sono consentite operazioni di i/o sincrone per i corpi della richiesta o della risposta.

::: moniker-end
   
::: moniker range="< aspnetcore-3.0"

<xref:Microsoft.AspNetCore.Http.Features.IHttpBufferingFeature>: Definisce i metodi per disabilitare il buffering di richieste e/o risposte.

::: moniker-end

<xref:Microsoft.AspNetCore.Http.Features.IHttpConnectionFeature>: Definisce le proprietà per l'ID connessione e gli indirizzi e le porte locali e remoti.

::: moniker range=">= aspnetcore-2.0"

<xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>: Controlla la dimensione massima consentita del corpo della richiesta per la richiesta corrente.

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

`IHttpRequestBodyDetectionFeature`: Indica se la richiesta può avere un corpo.

::: moniker-end

<xref:Microsoft.AspNetCore.Http.Features.IHttpRequestIdentifierFeature>: Aggiunge una proprietà che può essere implementata per identificare in modo univoco le richieste.

<xref:Microsoft.AspNetCore.Http.Features.IHttpRequestLifetimeFeature>: Definisce il supporto per l'interruzione delle connessioni o il rilevamento se una richiesta è stata terminata in modo anomalo, ad esempio dalla disconnessione di un client.

::: moniker range=">= aspnetcore-3.0"

<xref:Microsoft.AspNetCore.Http.Features.IHttpRequestTrailersFeature>: Fornisce l'accesso alle intestazioni del trailer della richiesta, se presenti.

<xref:Microsoft.AspNetCore.Http.Features.IHttpResetFeature>: Usato per inviare messaggi di reimpostazione per i protocolli che li supportano, ad esempio HTTP/2 o HTTP/3.

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<xref:Microsoft.AspNetCore.Http.Features.IHttpResponseTrailersFeature>: Consente all'applicazione di fornire le intestazioni del trailer della risposta, se supportate.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<xref:Microsoft.AspNetCore.Http.Features.IHttpSendFileFeature>: Definisce un metodo per l'invio di file in modo asincrono.

::: moniker-end

<xref:Microsoft.AspNetCore.Http.Features.IHttpUpgradeFeature>: Definisce il supporto per gli [aggiornamenti http](https://tools.ietf.org/html/rfc2616.html#section-14.42)che consentono al client di specificare quali protocolli aggiuntivi si desidera utilizzare se il server desidera cambiare i protocolli.

<xref:Microsoft.AspNetCore.Http.Features.IHttpWebSocketFeature>: Definisce un'API per il supporto di Web Socket.

::: moniker range=">= aspnetcore-3.0"

<xref:Microsoft.AspNetCore.Http.Features.IHttpsCompressionFeature>: Controlla se la compressione della risposta deve essere utilizzata su connessioni HTTPS.

::: moniker-end

<xref:Microsoft.AspNetCore.Http.Features.IItemsFeature>: Archivia la <xref:Microsoft.AspNetCore.Http.Features.IItemsFeature.Items> raccolta per lo stato di applicazione per richiesta.

<xref:Microsoft.AspNetCore.Http.Features.IQueryFeature>: Analizza e memorizza nella cache la stringa di query.
   
::: moniker range=">= aspnetcore-3.0"

<xref:Microsoft.AspNetCore.Http.Features.IRequestBodyPipeFeature>: Rappresenta il corpo della richiesta come <xref:System.IO.Pipelines.PipeReader> .
 
::: moniker-end

<xref:Microsoft.AspNetCore.Http.Features.IRequestCookiesFeature>: Analizza e memorizza nella cache i valori dell'intestazione della richiesta `Cookie` .

<xref:Microsoft.AspNetCore.Http.Features.IResponseCookiesFeature>: Controlla la modalità cookie di applicazione della risposta all' `Set-Cookie` intestazione.

::: moniker range=">= aspnetcore-2.2"

<xref:Microsoft.AspNetCore.Http.Features.IServerVariablesFeature>: Questa funzionalità fornisce l'accesso alle variabili del server di richiesta, ad esempio quelle fornite da IIS.

::: moniker-end
   
<xref:Microsoft.AspNetCore.Http.Features.IServiceProvidersFeature>: Fornisce l'accesso a un <xref:System.IServiceProvider> con servizi di richiesta con ambito.

<xref:Microsoft.AspNetCore.Http.Features.ISessionFeature>: Definisce `ISessionFactory` le <xref:Microsoft.AspNetCore.Http.ISession> astrazioni e per supportare le sessioni utente. `ISessionFeature` viene implementato da <xref:Microsoft.AspNetCore.Session.SessionMiddleware> (vedere <xref:fundamentals/app-state> ).

<xref:Microsoft.AspNetCore.Http.Features.ITlsConnectionFeature>: Definisce un'API per il recupero dei certificati client.

<xref:Microsoft.AspNetCore.Http.Features.ITlsTokenBindingFeature>: Definisce i metodi per l'utilizzo dei parametri di associazione dei token TLS.
   
::: moniker range=">= aspnetcore-2.2"
   
<xref:Microsoft.AspNetCore.Http.Features.ITrackingConsentFeature>: Usato per eseguire query, concedere e prelevare il consenso dell'utente per l'archiviazione di informazioni sull'utente correlate all'attività e alla funzionalità del sito.
   
::: moniker-end

## <a name="additional-resources"></a>Risorse aggiuntive

* <xref:fundamentals/servers/index>
* <xref:fundamentals/middleware/index>
