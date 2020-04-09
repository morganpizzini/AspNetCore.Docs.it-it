---
title: Funzionalità di richiesta in ASP.NET Core
author: ardalis
description: Dettagli dell'implementazione di server Web relativi alle richieste e alle risposte HTTP definiti nelle interfacce per ASP.NET Core.
ms.author: riande
ms.date: 10/14/2016
uid: fundamentals/request-features
ms.openlocfilehash: d0f3ae521d1f314dd04cb581d9a921da4719273d
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "79416226"
---
# <a name="request-features-in-aspnet-core"></a>Funzionalità di richiesta in ASP.NET Core

Di [Steve Smith](https://ardalis.com/)

I dettagli dell'implementazione di server Web relativi alle richieste e alle risposte HTTP vengono definiti nelle interfacce. Le interfacce vengono usate dalle implementazioni del server e dal middleware per creare e modificare la pipeline di hosting dell'applicazione.

## <a name="feature-interfaces"></a>Interfacce di funzionalità

ASP.NET Core definisce diverse interfacce di funzionalità HTTP in `Microsoft.AspNetCore.Http.Features` che vengono usate dai server per identificare le funzionalità supportate. Le interfacce di funzionalità seguenti gestiscono le richieste e restituiscono le risposte:

`IHttpRequestFeature` Definisce la struttura di una richiesta HTTP, inclusi protocollo, percorso, stringa di query, intestazioni e corpo.

`IHttpResponseFeature` Definisce la struttura di una risposta HTTP, inclusi codice di stato, intestazioni e corpo della risposta.

`IHttpAuthenticationFeature` Definisce il supporto per identificare gli utenti in base a un oggetto `ClaimsPrincipal` e specificare un gestore di autenticazione.

`IHttpUpgradeFeature` Definisce il supporto per gli [aggiornamenti HTTP](https://tools.ietf.org/html/rfc2616.html#section-14.42), che consentono al client di specificare protocolli aggiuntivi da usare se il server vuole cambiare i protocolli.

`IHttpBufferingFeature` Definisce metodi per la disattivazione del buffering di richieste e/o risposte.

`IHttpConnectionFeature` Definisce le proprietà per porte e indirizzi locali e remoti.

`IHttpRequestLifetimeFeature` Definisce il supporto per l'interruzione delle connessioni o per rilevare se una richiesta è stata terminata in modo anomalo, ad esempio da una disconnessione del client.

`IHttpSendFileFeature` Definisce un metodo per l'invio di file in modo asincrono.

`IHttpWebSocketFeature` Definisce un'API per il supporto di WebSocket.

`IHttpRequestIdentifierFeature` Aggiunge una proprietà che può essere implementata per identificare le richieste in modo univoco.

`ISessionFeature` Definisce le astrazioni `ISessionFactory` e `ISession` per supportare le sessioni utente.

`ITlsConnectionFeature` Definisce un'API per il recupero dei certificati client.

`ITlsTokenBindingFeature` Definisce i metodi per l'uso di parametri di associazione dei token TLS.

> [!NOTE]
> `ISessionFeature` non è una funzionalità server, ma viene implementato da `SessionMiddleware`. Vedere [Stato di sessioni e app](app-state.md).

## <a name="feature-collections"></a>Raccolte di funzionalità

La proprietà `Features` di `HttpContext` specifica un'interfaccia per ottenere e impostare le funzionalità HTTP disponibili per la richiesta corrente. Poiché l'insieme di funzionalità è modificabile anche all'interno del contesto di una richiesta, è possibile usare il middleware per modificare la raccolta e aggiungere il supporto di altre funzionalità.

## <a name="middleware-and-request-features"></a>Middleware e funzionalità di richiesta

Anche se sono i server a creare la raccolta di funzionalità, il middleware può sia aggiungere che togliere funzionalità alla raccolta. Ad esempio, l'elemento `StaticFileMiddleware` accede alla funzionalità `IHttpSendFileFeature`. Se la funzionalità è presente, viene usata per inviare il file statico richiesto dal percorso fisico relativo. In caso contrario, per inviare il file viene usato un metodo alternativo più lento. Quando è disponibile, `IHttpSendFileFeature` consente al sistema operativo di aprire il file ed eseguire una copia in modalità kernel diretta alla scheda di rete.

Il middleware può anche eseguire aggiunte alla raccolta di funzionalità impostata dal server. Il middleware può anche sostituire funzionalità esistenti, aumentando così la funzionalità del server. Le funzionalità aggiunte alla raccolta sono disponibili immediatamente per un altro middleware o per l'applicazione sottostante in un secondo momento nella pipeline della richiesta.

Combinando implementazioni server personalizzate e miglioramenti specifici del middleware, è possibile costruire il set di funzionalità preciso richiesto da un'applicazione. In questo modo è possibile aggiungere le funzionalità mancanti senza che siano necessarie modifiche nel server e garantire che venga esposta solo una quantità minima di funzionalità, limitando in tal modo la superficie di attacco e migliorando le prestazioni.

## <a name="summary"></a>Summary

Le interfacce di funzionalità definiscono funzionalità HTTP specifiche supportate da una data richiesta. I server definiscono le raccolte di funzionalità e il set iniziale di funzionalità supportato dal server, ma è possibile usare il middleware per migliorare tali funzionalità.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Server](xref:fundamentals/servers/index)
* [Middleware](xref:fundamentals/middleware/index)
* [Aprire l'interfaccia Web per .NET (OWIN)](xref:fundamentals/owin)
