---
title: Gestire le richieste con controller in ASP.NET Core MVC
author: ardalis
description: ''
ms.author: riande
ms.date: 12/05/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/controllers/actions
ms.openlocfilehash: 0c91edc947b1a17f2dd36b281afe348aa8611bd7
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/26/2020
ms.locfileid: "85406914"
---
# <a name="handle-requests-with-controllers-in-aspnet-core-mvc"></a>Gestire le richieste con controller in ASP.NET Core MVC

Di [Steve Smith](https://ardalis.com/) e [Scott Addie](https://github.com/scottaddie)

I controller, le azioni e risultati delle azioni sono parti fondamentali dello sviluppo di app tramite ASP.NET Core MVC.

## <a name="what-is-a-controller"></a>Che cos'è un controller?

Un controller viene usato per definire e raggruppare un set di azioni. Un'azione (o *metodo di azione*) è un metodo in un controller che gestisce richieste. I controller raggruppano azioni simili in modo logico. Questa aggregazione di azioni consente l'applicazione collettiva di set di regole comuni, ad esempio routing, memorizzazione nella cache e autorizzazione. Le richieste vengono mappate alle azioni tramite [routing](xref:mvc/controllers/routing).

Per convenzione, le classi controller:

* Si trova nella cartella *controller* a livello radice del progetto.
* Ereditare da `Microsoft.AspNetCore.Mvc.Controller`.

Un controller è una classe istanziabile per cui almeno una delle condizioni seguenti è vera:

* Il nome della classe è con suffisso `Controller` .
* La classe eredita da una classe il cui nome è con suffisso `Controller` .
* L' `[Controller]` attributo viene applicato alla classe.

A una classe controller non deve essere associato un attributo `[NonController]`.

I controller devono seguire il [principio delle dipendenze esplicite](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies). Per l'implementazione di questo principio esistono due approcci. Se più azioni del controller richiedono lo stesso servizio, prendere in considerazione l'uso dell'[inserimento di costruttori](xref:mvc/controllers/dependency-injection#constructor-injection) per richiedere tali dipendenze. Se il servizio è richiesto da un solo metodo di azione, prendere in considerazione l'uso dell'[inserimento di azioni](xref:mvc/controllers/dependency-injection#action-injection-with-fromservices) per richiedere la dipendenza.

All'interno del modello **M**odel-**V**iew-**C**ontroller, un controller è responsabile dell'elaborazione iniziale della richiesta e della creazione di istanze del modello. In genere, per le decisioni aziendali è consigliabile seguire il modello.

Il controller riceve il risultato di un'eventuale elaborazione del modello e restituisce la visualizzazione corretta e i dati associati oppure il risultato della chiamata API. Per altre informazioni, vedere [Panoramica di ASP.NET Core MVC](xref:mvc/overview) e [Introduzione ad ASP.NET Core MVC e Visual Studio](xref:tutorials/first-mvc-app/start-mvc).

Il controller è un'astrazione *a livello di interfaccia utente*. Il suo compito è di verificare che i dati della richiesta siano validi e di scegliere la visualizzazione (o il risultato per un'API) da restituire. Nelle app con factoring corretto, il controller non include direttamente accesso ai dati o logica di business, ma delega la gestione di tali responsabilità a servizi specifici.

## <a name="defining-actions"></a>Definizione di azioni

I metodi pubblici in un controller, ad eccezione di quelli con l' `[NonAction]` attributo, sono azioni. I parametri delle azioni sono associati a dati di richiesta e vengono convalidati tramite [associazione di modelli](xref:mvc/models/model-binding). La convalida del modello viene eseguita per tutto ciò che è associato a un modello. Il valore della proprietà `ModelState.IsValid` indica se l'associazione e la convalida dei modelli hanno avuto esito positivo.

I metodi di azione devono contenere la logica per il mapping di una richiesta a un problema di business. È di solito consigliabile rappresentare i problemi di business come servizi a cui il controller accede tramite [inserimento di dipendenze](xref:mvc/controllers/dependency-injection). Le azioni eseguono quindi il mapping del risultato dell'azione di business a uno stato dell'applicazione.

Le azioni possono restituire qualsiasi valore, ma spesso restituiscono un'istanza di `IActionResult` (o di `Task<IActionResult>` per i metodi asincroni) che genera una risposta. Il metodo di azione è responsabile della scelta del *tipo di risposta*. Il risultato dell'azione * esegue la risposta*.

### <a name="controller-helper-methods"></a>Metodi helper dei controller

I controller in genere ereditano dalla classe [Controller](/dotnet/api/microsoft.aspnetcore.mvc.controller), anche se questo non è obbligatorio. La derivazione da `Controller` consente l'accesso a tre categorie di metodi helper:

#### <a name="1-methods-resulting-in-an-empty-response-body"></a>1. metodi che derivano da un corpo della risposta vuoto

Non è inclusa un'intestazione di risposta HTTP `Content-Type`, poiché il corpo della risposta non ha contenuto da descrivere.

All'interno di questa categoria sono presenti due tipi di risultati: reindirizzamento e codice di stato HTTP.

* **Codice di stato HTTP**

    Questo tipo restituisce un codice di stato HTTP. Alcuni metodi helper di questo tipo sono `BadRequest`, `NotFound` e `Ok`. Il metodo `return BadRequest();`, ad esempio, quando viene eseguito genera un codice di stato 400. Quando metodi come `BadRequest`, `NotFound` e `Ok` vengono sottoposti a overload, non sono più risponditori del codice di stato HTTP, poiché è in corso la negoziazione del contenuto.

* **reindirizzamento**

    Questo tipo restituisce un reindirizzamento a un'azione o a una destinazione (tramite `Redirect`, `LocalRedirect`, `RedirectToAction` o `RedirectToRoute`). `return RedirectToAction("Complete", new {id = 123});`, ad esempio, reindirizza a `Complete`, passando un oggetto anonimo.

    Il tipo di risultato del reindirizzamento è diverso dal tipo del codice di stato HTTP principalmente per l'aggiunta di una intestazione della risposta HTTP `Location`.

#### <a name="2-methods-resulting-in-a-non-empty-response-body-with-a-predefined-content-type"></a>2. metodi che derivano da un corpo della risposta non vuoto con un tipo di contenuto predefinito

La maggior parte dei metodi helper di questa categoria includono una proprietà `ContentType`, che consente di impostare l'intestazione della risposta `Content-Type` in modo da descrivere il corpo della risposta.

All'interno di questa categoria sono presenti due tipi di risultato: [visualizzazione](xref:mvc/views/overview) e [risposta formattata](xref:web-api/advanced/formatting).

* **Visualizza**

    Questo tipo restituisce una visualizzazione che esegue il rendering HTML usando un modello. `return View(customer);`, ad esempio, passa un modello alla visualizzazione per eseguire il data binding.

* **Risposta formattata**

    Questo tipo restituisce il formato JSON o un formato di scambio di dati simile per rappresentare un oggetto in un modo specifico. `return Json(customer);`, ad esempio, serializza l'oggetto specificato in formato JSON.
    
    Altri metodi comuni di questo tipo sono `File` e `PhysicalFile`. Ad esempio, `return PhysicalFile(customerFilePath, "text/xml");` restituisce [PhysicalFileResult](/dotnet/api/microsoft.aspnetcore.mvc.physicalfileresult).

#### <a name="3-methods-resulting-in-a-non-empty-response-body-formatted-in-a-content-type-negotiated-with-the-client"></a>3. metodi che derivano da un corpo della risposta non vuoto formattato in un tipo di contenuto negoziato con il client

Questa categoria è più nota come **negoziazione del contenuto**. La [negoziazione del contenuto](xref:web-api/advanced/formatting#content-negotiation) si applica ogni volta che un'azione restituisce un tipo [ObjectResult](/dotnet/api/microsoft.aspnetcore.mvc.objectresult) o qualcosa di diverso da un'implementazione di [IActionResult](/dotnet/api/microsoft.aspnetcore.mvc.iactionresult). Anche un'azione che restituisce un'implementazione non di `IActionResult` (ad esempio, `object`) restituisce una risposta formattata.

Alcuni metodi helper di questo tipo sono `BadRequest`, `CreatedAtRoute` e `Ok`. Alcuni esempi di questi metodi sono, rispettivamente, `return BadRequest(modelState);`, `return CreatedAtRoute("routename", values, newobject);` e `return Ok(value);`. Si noti che `BadRequest` e `Ok` eseguono la negoziazione del contenuto solo quando viene passato loro un valore. Se non viene loro passato alcun valore, fungono invece da tipi di risultato codice di stato HTTP. Il metodo `CreatedAtRoute`, d'altra parte, esegue sempre la negoziazione del contenuto, perché tutti gli overload di questo metodo richiedono che venga passato un valore.

### <a name="cross-cutting-concerns"></a>Problemi di montaggio incrociato

Le applicazioni condividono in genere parti del flusso di lavoro. Tra gli esempi possibili, un'app che richiede l'autenticazione per accedere al carrello o un'app che memorizza nella cache i dati di alcune pagine. Per eseguire della logica prima o dopo un metodo di azione, usare un *filtro*. L'uso di [filtri](xref:mvc/controllers/filters) su problemi di montaggio incrociato può ridurre la duplicazione.

La maggior parte degli attributi di filtro, ad esempio `[Authorize]`, può essere applicata a livello di controller o di azione, a seconda del livello di granularità desiderato.

La gestione degli errori e la memorizzazione nella cache delle risposte rappresentano spesso problemi di montaggio incrociato:
* [Gestire gli errori](xref:mvc/controllers/filters#exception-filters)
* [Memorizzazione nella cache delle risposte](xref:performance/caching/response)

Molti problemi di montaggio incrociato possono essere gestiti tramite filtri o [middleware](xref:fundamentals/middleware/index) personalizzato.
