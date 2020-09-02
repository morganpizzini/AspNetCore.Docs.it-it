---
title: Parte 10, esaminare i dettagli ed eliminare i metodi di un'app ASP.NET Core
author: rick-anderson
description: Parte 10 della serie di esercitazioni su ASP.NET Core MVC.
ms.author: riande
ms.date: 12/13/2018
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
uid: tutorials/first-mvc-app/details
ms.openlocfilehash: 4683f106b3372a2f14f289b39c8a0b8b6c8b5aa1
ms.sourcegitcommit: c9b03d8a6a4dcc59e4aacb30a691f349235a74c8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/02/2020
ms.locfileid: "89379367"
---
# <a name="part-10-examine-the-details-and-delete-methods-of-an-aspnet-core-app"></a>Parte 10, esaminare i dettagli ed eliminare i metodi di un'app ASP.NET Core

Autore: [Rick Anderson](https://twitter.com/RickAndMSFT)

Aprire il controller di film ed esaminare il metodo `Details`:

[!code-csharp[](start-mvc/sample/MvcMovie22/Controllers/MoviesController.cs?name=snippet_details)]

Il motore di scaffolding MVC che ha creato questo metodo di azione aggiunge un commento che mostra una richiesta HTTP che richiama il metodo. In questo caso si tratta di una richiesta GET con tre segmenti di URL, il controller `Movies`, il metodo `Details` e un valore `id`. Tenere presente che questi segmenti vengono definiti nel file *Startup.cs*.

[!code-csharp[](start-mvc/sample/MvcMovie3/Startup.cs?highlight=5&name=snippet_1)]

Entity Framework semplifica la ricerca di dati usando il metodo `FirstOrDefaultAsync`. Un'importante funzionalità di sicurezza integrata nel metodo è che il codice verifica che il metodo di ricerca abbia trovato un film prima di tentare di eseguire una qualsiasi operazione su di esso. Ad esempio, un hacker potrebbe introdurre errori nel sito modificando l'URL creato dai collegamenti di `http://localhost:{PORT}/Movies/Details/1` in qualcosa di simile a `http://localhost:{PORT}/Movies/Details/12345` o usando altri valori che non rappresentano un film effettivo. Se non è stato cercato un film con valore Null, l'app genera un'eccezione.

Esaminare i metodi `Delete` e `DeleteConfirmed`.

[!code-csharp[](start-mvc/sample/MvcMovie22/Controllers/MoviesController.cs?name=snippet_delete)]

Si noti che il metodo `HTTP GET Delete` non elimina il film specificato, ma restituisce una vista del film in cui è possibile inviare (HttpPost) la richiesta di eliminazione. L'esecuzione di un'operazione di eliminazione in risposta a una richiesta GET (o l'esecuzione di un'operazione di modifica, di creazione o di qualsiasi altra azione che modifica i dati) introduce un problema di sicurezza.

Il metodo `[HttpPost]` che elimina i dati è denominato `DeleteConfirmed` per fornire al metodo HTTP POST un nome o una firma univoca. Le firme dei due metodi sono illustrate di seguito:

[!code-csharp[](start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_delete2)]

[!code-csharp[](start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_delete3)]

Il Common Language Runtime (CLR) richiede metodi in rapporto di overload per disporre di una firma di parametro univoca, ovvero lo stesso nome di metodo ma un elenco diverso di parametri. Tuttavia, qui è necessario usare due `Delete` metodi: uno per GET e uno per POST, entrambi con la stessa firma di parametro. Entrambi devono accettare un singolo intero come parametro.

Per risolvere questo problema esistono due approcci. Una possibilità è assegnare nomi diversi ai metodi. Questa operazione è stata eseguita dal meccanismo di scaffolding nell'esempio precedente. Tuttavia in questo modo si introduce un piccolo problema: ASP.NET esegue il mapping dei segmenti di un URL ai metodi di azione in base al nome e, se si rinomina un metodo, generalmente il routing non è in grado di trovare questo metodo. La soluzione è mostrata nell'esempio e consiste nell'aggiungere l'attributo `ActionName("Delete")` al metodo `DeleteConfirmed`. Questo attributo esegue il mapping per il sistema di routing in modo che un URL che includa /Delete/ per una richiesta POST troverà il metodo `DeleteConfirmed`.

Un'altra alternativa comune per i metodi con nomi e firme identici consiste nel modificare artificialmente la firma del metodo POST in modo da includere un parametro aggiuntivo (non usato). Questa azione è stata eseguita in un post precedente quando è stato aggiunto il parametro `notUsed`. È possibile eseguire la stessa operazione qui per il metodo `[HttpPost] Delete`:

```csharp
// POST: Movies/Delete/6
[HttpPost]
[ValidateAntiForgeryToken]
public async Task<IActionResult> Delete(int id, bool notUsed)
```

### <a name="publish-to-azure"></a>Pubblicazione in Azure

Per informazioni sulla distribuzione in Azure, vedere [esercitazione: creare un'app per database SQL e ASP.NET Core in app Azure Service](/azure/app-service/tutorial-dotnetcore-sqldb-app).

> [!div class="step-by-step"]
> [Indietro](validation.md)
