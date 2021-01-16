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
# <a name="request-draining-with-aspnet-core-kestrel-web-server"></a>Svuotamento delle richieste con il server Web ASP.NET Core gheppio

L'apertura di connessioni HTTP richiede molto tempo. Per HTTPS, è anche a elevato utilizzo di risorse. Pertanto, gheppio tenta di riutilizzare le connessioni per il protocollo HTTP/1.1. Il corpo della richiesta deve essere completamente utilizzato per consentire il riutilizzo della connessione. L'app non usa sempre il corpo della richiesta, ad esempio le richieste HTTP POST in cui il server restituisce una risposta di reindirizzamento o 404. Nel caso del reindirizzamento HTTP POST:

* Il client potrebbe avere già inviato parte dei dati POST.
* Il server scrive la risposta 301.
* Non è possibile usare la connessione per una nuova richiesta fino a quando i dati POST del corpo della richiesta precedente non sono stati completamente letti.
* Gheppio tenta di svuotare il corpo della richiesta. Lo svuotamento del corpo della richiesta implica la lettura e l'eliminazione dei dati senza elaborarli.

Il processo di svuotamento costituisce un compromesso tra consentire il riutilizzo della connessione e il tempo necessario per svuotare i dati rimanenti:

* Lo svuotamento ha un timeout di cinque secondi, che non è configurabile.
* Se tutti i dati specificati dall' `Content-Length` `Transfer-Encoding` intestazione o non sono stati letti prima del timeout, la connessione viene chiusa.

In alcuni casi può essere necessario terminare immediatamente la richiesta, prima o dopo la scrittura della risposta. È ad esempio possibile che i client dispongano di limiti di dati restrittivi. La limitazione dei dati caricati potrebbe avere una priorità. In questi casi, per terminare una richiesta, chiamare [HttpContext. Abort](xref:Microsoft.AspNetCore.Http.HttpContext.Abort%2A) da un controller, una Razor pagina o un middleware.

Ci sono alcune avvertenze per chiamare `Abort` :

* La creazione di nuove connessioni può essere lenta e dispendiosa.
* Non vi è alcuna garanzia che il client abbia letto la risposta prima che la connessione venga chiusa.
* `Abort`La chiamata a deve essere rara e riservata per i casi di errore gravi, non per errori comuni.
  * Chiamare solo `Abort` quando è necessario risolvere un problema specifico. Ad esempio, chiamare `Abort` se i client malintenzionati tentano di pubblicare dati o quando si verifica un bug nel codice client che provoca richieste di grandi dimensioni o diverse.
  * Non chiamare le `Abort` situazioni di errore comuni, ad esempio HTTP 404 (non trovato).

Quando si chiama [HttpResponse. CompleteAsync](xref:Microsoft.AspNetCore.Http.HttpResponse.CompleteAsync%2A) , prima `Abort` di chiamare si garantisce che il server abbia completato la scrittura della risposta. Tuttavia, il comportamento del client non è prevedibile e potrebbe non essere in grado di leggere la risposta prima che la connessione venga interrotta.

Questo processo è diverso per HTTP/2 poiché il protocollo supporta l'interruzione dei singoli flussi di richiesta senza chiudere la connessione. Il timeout di svuotamento di cinque secondi non è applicabile. Se dopo il completamento di una risposta sono presenti dati del corpo della richiesta non letti, il server invia un frame RST HTTP/2. I frame di dati del corpo della richiesta aggiuntivi vengono ignorati.

Se possibile, è preferibile che i client usino l'intestazione della richiesta [expect: 100-continue](https://developer.mozilla.org/docs/Web/HTTP/Status/100) e attendano la risposta del server prima di iniziare a inviare il corpo della richiesta. Che fornisce al client la possibilità di esaminare la risposta e interrompere prima di inviare dati non necessari.
