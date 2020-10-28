---
title: "Esercitazione: chiamare un'API Web ASP.NET Core con JavaScript"
author: rick-anderson
description: Informazioni su come chiamare un'API Web ASP.NET Core con JavaScript.
ms.author: riande
ms.custom: mvc, devx-track-js
ms.date: 11/26/2019
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
uid: tutorials/web-api-javascript
ms.openlocfilehash: b41288bd63267a9aa7035e25ebc8d838eed5d93b
ms.sourcegitcommit: 2e3a967331b2c69f585dd61e9ad5c09763615b44
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92690683"
---
# <a name="tutorial-call-an-aspnet-core-web-api-with-javascript"></a>Esercitazione: chiamare un'API Web ASP.NET Core con JavaScript

Autore: [Rick Anderson](https://twitter.com/RickAndMSFT)

Questa esercitazione illustra come chiamare un'API Web ASP.NET Core con JavaScript usando l'[API Fetch](https://developer.mozilla.org/docs/Web/API/Fetch_API).

::: moniker range="< aspnetcore-3.0"

Per ASP.NET Core 2.2, vedere la versione 2.2 di [Chiamare l'API Web con JavaScript](xref:tutorials/first-web-api#call-the-web-api-with-javascript).

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

## <a name="prerequisites"></a>Prerequisiti

* [Esercitazione completa: creare un'API Web](xref:tutorials/first-web-api)
* Familiarità con CSS, HTML e JavaScript

## <a name="call-the-web-api-with-javascript"></a>Chiamare l'API Web con JavaScript

In questa sezione verrà aggiunta una pagina HTML contenente i moduli per la creazione e la gestione di elementi attività. Agli elementi della pagina vengono associati gestori eventi. I gestori eventi generano richieste HTTP ai metodi di azione dell'API Web. La funzione `fetch` dell'API Fetch avvia ogni richiesta HTTP.

La `fetch` funzione restituisce un oggetto [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) , che contiene una risposta HTTP rappresentata come un `Response` oggetto. Un modello comune consiste nell'estrarre il corpo della risposta JSON richiamando la funzione `json` per l'oggetto `Response`. JavaScript aggiorna la pagina con i dettagli della risposta dell'API Web.

La chiamata `fetch` più semplice accetta un solo parametro che rappresenta la route. Un secondo parametro, noto come oggetto `init`, è facoltativo. `init` viene usato per configurare la richiesta HTTP.

1. Configurare l'app in modo da [gestire i file statici](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) e [abilitare il mapping dei file predefiniti](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_). Il codice evidenziato seguente è necessario nel metodo `Configure` di *Startup.cs* :

    [!code-csharp[](first-web-api/samples/3.0/TodoApi/StartupJavaScript.cs?highlight=8-9&name=snippet_configure)]

1. Creare una cartella *wwwroot* nella radice del progetto.

1. Creare una cartella *JS* all'interno della cartella *wwwroot* .

1. Aggiungere un file HTML denominato *index.html* alla cartella *wwwroot* Sostituire il contenuto di *index.html* con il markup seguente:

    [!code-html[](first-web-api/samples/3.0/TodoApi/wwwroot/index.html)]

1. Aggiungere un file JavaScript denominato *site.js* alla cartella *wwwroot/JS* . Sostituire il contenuto di *site.js* con il codice seguente:

    [!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_SiteJs)]

Può essere necessario modificare le impostazioni di avvio del progetto ASP.NET Core per il test della pagina HTML in locale:

1. Aprire *Properties\launchSettings.json* .
1. Rimuovere la `launchUrl` proprietà per forzare l'apertura dell'app a *index.html* &mdash; file predefinito del progetto.

In questo esempio vengono chiamati tutti i metodi CRUD dell'API Web. Di seguito sono disponibili spiegazioni per le richieste dell'API Web.

### <a name="get-a-list-of-to-do-items"></a>Ottenere un elenco di elementi attività

Nel codice seguente viene inviata una richiesta HTTP GET alla route *api/TodoItems* :

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_GetItems)]

Quando l'API Web restituisce un codice di stato di esecuzione riuscita, viene chiamata la funzione `_displayItems`. Ogni elemento attività nel parametro matrice accettato da `_displayItems` viene aggiunto a una tabella con i pulsanti **Edit** (Modifica) e **Delete** (Elimina). Se la richiesta dell'API Web ha esito negativo, viene registrato un errore nella console del browser.

### <a name="add-a-to-do-item"></a>Aggiungere un elemento attività

Nel codice seguente:

* Viene dichiarata una variabile `item` per costruire una rappresentazione letterale dell'oggetto dell'elemento attività.
* Viene configurata una richiesta Fetch con le opzioni seguenti:
  * `method`&mdash;specifica il verbo di azione HTTP POST.
  * `body`&mdash;specifica la rappresentazione JSON del corpo della richiesta. Il codice JSON viene prodotto passando il valore letterale dell'oggetto archiviato in `item` alla funzione [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).
  * `headers`&mdash;specifica le intestazioni della richiesta HTTP `Accept` e `Content-Type`. Entrambe le intestazioni vengono impostate su `application/json` per specificare rispettivamente il tipo di supporto ricevuto e inviato.
* Una richiesta HTTP POST viene inviata alla route *api/TodoItems* .

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_AddItem)]

Quando l'API Web restituisce un codice di stato di esecuzione riuscita, viene richiamata la funzione `getItems` per aggiornare la tabella HTML. Se la richiesta dell'API Web ha esito negativo, viene registrato un errore nella console del browser.

### <a name="update-a-to-do-item"></a>Aggiornare un elemento attività

L'aggiornamento di un elemento attività è simile all'aggiunta di un elemento. Esistono tuttavia due differenze significative:

* Alla route viene aggiunto come suffisso l'identificatore univoco dell'elemento da aggiornare. Ad esempio, *api/TodoItems/1* .
* Il verbo di azione HTTP è PUT, come indicato dall'opzione `method`.

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_UpdateItem)]

### <a name="delete-a-to-do-item"></a>Eliminare un elemento attività

Per eliminare un elemento attività, impostare l'opzione `method` della richiesta su `DELETE` e specificare l'identificatore univoco dell'elemento nell'URL.

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_DeleteItem)]

Passare all'esercitazione successiva per apprendere come generare pagine della Guida dell'API Web:

> [!div class="nextstepaction"]
> <xref:tutorials/get-started-with-swashbuckle>

::: moniker-end
