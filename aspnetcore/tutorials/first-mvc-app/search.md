---
title: Aggiungere la funzionalità di ricerca a un'app ASP.NET Core MVC
author: rick-anderson
description: Illustra come aggiungere la funzionalità di ricerca a un'app ASP.NET Core MVC di base
ms.author: riande
ms.date: 12/13/2018
uid: tutorials/first-mvc-app/search
ms.openlocfilehash: 89f1fa84783430f160ca0b840bf7ae9699520cb7
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "78662869"
---
# <a name="add-search-to-an-aspnet-core-mvc-app"></a>Aggiungere la funzionalità di ricerca a un'app ASP.NET Core MVC

Autore: [Rick Anderson](https://twitter.com/RickAndMSFT)

In questa sezione si aggiunge la funzionalità di ricerca al metodo di azione `Index` che consente di cercare film in base al *genere* o al *nome*.

Aggiornare il metodo `Index` all'interno di *Controllers/MoviesController.cs* con il codice seguente:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_1stSearch)]

La prima riga del metodo di azione `Index` crea una query [LINQ](/dotnet/standard/using-linq) per selezionare i film:

```csharp
var movies = from m in _context.Movie
             select m;
```

La query viene *solo* definita in questo punto, ma **non** viene eseguita nel database.

Se il parametro `searchString` contiene una stringa, la query dei film viene modificata per filtrare in base al valore della stringa di ricerca:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_SearchNull2)]

Il codice `s => s.Title.Contains()` precedente è un'[espressione lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions). Le espressioni lambda vengono usate nelle query [LINQ](/dotnet/standard/using-linq) basate sul metodo come argomenti dei metodi di operatori di query standard, quali il metodo [Where](/dotnet/api/system.linq.enumerable.where) o `Contains` (usato nel codice precedente). Le query LINQ non vengono eseguite al momento della definizione o della modifica chiamando un metodo quale `Where`, `Contains` o `OrderBy`. L'esecuzione della query viene invece posticipata.  Per esecuzione posticipata si intende che la valutazione di un'espressione viene ritardata finché non viene eseguita l'iterazione del valore o non viene chiamato il metodo `ToListAsync`. Per altre informazioni sull'esecuzione posticipata di query, vedere [Esecuzione di query](/dotnet/framework/data/adonet/ef/language-reference/query-execution).

Nota: il metodo [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) viene eseguito sul database, non nel codice C# indicato in precedenza. La distinzione tra maiuscole/minuscole nella query dipende dal database e dalle regole di confronto. In SQL Server, [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) esegue il mapping a [SQL LIKE](/sql/t-sql/language-elements/like-transact-sql), che fa distinzione tra maiuscole e minuscole. In SQLite, con le regole di confronto predefinite si fa distinzione tra maiuscole e minuscole.

Accedere a `/Movies/Index`. Accodare una stringa di query, ad esempio `?searchString=Ghost`, all'URL. Vengono visualizzati i film filtrati.

![Vista Index](~/tutorials/first-mvc-app/search/_static/ghost.png)

Se si modifica la firma del metodo `Index` in modo che contenga un parametro denominato `id`, il parametro `id` corrisponderà al segnaposto `{id}` facoltativo per le route predefinite impostate in *Startup.cs*.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Startup.cs?highlight=5&name=snippet_1)]

Modificare il parametro in `id` e tutte le occorrenze di `searchString` cambiano in `id`.

Il metodo `Index` precedente:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1,6,8&name=snippet_1stSearch)]

Il metodo `Index` aggiornato con il parametro `id`:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1,6,8&name=snippet_SearchID)]

È ora possibile passare il titolo della ricerca come dati di route (un segmento di URL), anziché come valore della stringa di query.

![Vista Index con la parola ghost aggiunta all'URL e un elenco restituito di due film: Ghostbusters e Ghostbusters 2](~/tutorials/first-mvc-app/search/_static/g2.png)

Tuttavia, non è possibile supporre che gli utenti modifichino l'URL ogni volta che desiderano cercare un film. A questo punto si aggiungeranno elementi dell'interfaccia utente per filtrare i film. Se è stata modificata la firma del metodo `Index` per testare come passare il parametro `ID` associato alla route, impostarlo di nuovo in modo che accetti un parametro denominato `searchString`:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1,6,8&name=snippet_1stSearch)]

Aprire il file *Views/Movies/Index.cshtml* e aggiungere il markup `<form>` evidenziato di seguito:

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Movies/IndexForm1.cshtml?highlight=10-16&range=4-21)]

Il tag `<form>` HTML usa l'[helper tag del modulo](xref:mvc/views/working-with-forms) e quindi quando si invia il modulo, la stringa di filtro viene registrata nell'azione `Index` del controller di film. Salvare le modifiche e quindi testare il filtro.

![Vista Index con la parola ghost digitata nella casella di testo del filtro del titolo](~/tutorials/first-mvc-app/search/_static/filter.png)

Non è presente alcun overload del metodo `[HttpPost]``Index` come previsto. Non è necessario perché il metodo non modifica lo stato dell'app, ma filtra solo i dati.

È possibile aggiungere il metodo `[HttpPost] Index` seguente.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1&name=snippet_SearchPost)]

Il parametro `notUsed` viene usato per creare un overload per il metodo `Index`. Questo aspetto verrà trattato in una fase successiva dell'esercitazione.

Se si aggiunge questo metodo, l'invoker di azione trova la corrispondenza con il metodo `[HttpPost] Index` e il metodo `[HttpPost] Index` viene eseguito come illustrato nell'immagine seguente.

![Finestra del browser con la risposta dell'applicazione From HttpPost Index: filter on ghost](~/tutorials/first-mvc-app/search/_static/fo.png)

Tuttavia, anche se si aggiunge questa versione `[HttpPost]` del metodo `Index`, esiste una limitazione sul modo sulla relativa implementazione. Si supponga che si desideri usare come segnalibro una ricerca specifica o inviare un collegamento agli amici su cui possono fare clic per visualizzare lo stesso elenco filtrato di film. Si noti che l'URL per la richiesta HTTP POST è lo stesso URL per la richiesta GET (localhost:{PORTA}/Movies/Index). Le informazioni sulla ricerca non sono disponibili nell'URL. Le informazioni sulla stringa di ricerca vengono inviate al server come un [valore del campo modulo](https://developer.mozilla.org/docs/Learn/HTML/Forms/Sending_and_retrieving_form_data). È possibile eseguire una verifica con gli strumenti di sviluppo del browser o l'eccellente [strumento Fiddler](https://www.telerik.com/fiddler). L'immagine seguente mostra gli strumenti di sviluppo del browser Chrome:

![Scheda di rete degli strumenti di sviluppo in Microsoft Edge che mostra il corpo di una richiesta con un valore searchString di ghost](~/tutorials/first-mvc-app/search/_static/f12_rb.png)

È possibile esaminare il parametro di ricerca e il token [XSRF](xref:security/anti-request-forgery) nel corpo della richiesta. Si noti, come indicato nell'esercitazione precedente, che l'[helper tag del modulo](xref:mvc/views/working-with-forms) genera un token antifalsificazione [XSRF](xref:security/anti-request-forgery). Poiché non si stanno modificando i dati, non è necessario convalidare il token nel metodo del controller.

Poiché il parametro di ricerca si trova nel corpo della richiesta e non nell'URL, non è possibile acquisire queste informazioni sulla ricerca da usare come segnalibro o condividerle con altri utenti. Per risolvere il problema, specificare che la richiesta deve essere `HTTP GET` nel file *Views/Movies/Index.cshtml*.

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexGet.cshtml?highlight=12&range=1-23)]

A questo punto quando si invia una ricerca, l'URL contiene la stringa di query della ricerca. La ricerca passerà anche al metodo di azione `HttpGet Index`, anche se si dispone di un metodo `HttpPost Index`.

![Finestra del browser che mostra searchString=ghost nell'URL e i film restituiti, Ghostbusters e Ghostbusters 2, contengono la parola ghost](~/tutorials/first-mvc-app/search/_static/search_get.png)

Il markup seguente mostra la modifica al tag `form`:

```cshtml
<form asp-controller="Movies" asp-action="Index" method="get">
```

## <a name="add-search-by-genre"></a>Aggiungere la funzionalità di ricerca in base al genere

Aggiungere la classe `MovieGenreViewModel` seguente alla cartella *Models*:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Models/MovieGenreViewModel.cs)]

Il modello di vista movie-genre conterrà:

* Un elenco di film.
* `SelectList` contiene l'elenco dei generi. Consente all'utente di selezionare un genere dall'elenco.
* `MovieGenre` che contiene il genere selezionato.
* `SearchString`, che contiene il testo immesso dagli utenti nella casella di testo di ricerca.

Sostituire il metodo `Index` in `MoviesController.cs` con il codice seguente:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MoviesController.cs?name=snippet_SearchGenre)]

Il codice seguente è una query `LINQ` che recupera tutti i generi dal database.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MoviesController.cs?name=snippet_LINQ)]

L'elenco `SelectList` di generi viene creato presentando generi distinti per evitare che l'elenco di selezione includa generi duplicati.

Quando l'utente cerca l'elemento, viene mantenuto il valore di ricerca nella casella di ricerca.

## <a name="add-search-by-genre-to-the-index-view"></a>Aggiungere la funzionalità di ricerca in base al genere alla vista Index

Aggiornare `Index.cshtml` in *Views/Movies/* come segue:

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexFormGenreNoRating.cshtml?highlight=1,15,16,17,19,28,31,34,37,43)]

Esaminare l'espressione lambda usata nell'helper HTML seguente:

`@Html.DisplayNameFor(model => model.Movies[0].Title)`

Nel codice precedente, l'helper HTML `DisplayNameFor` controlla la proprietà `Title` a cui si fa riferimento nell'espressione lambda per determinare il nome visualizzato. Poiché l'espressione lambda viene controllata e anziché essere valutata, non viene generata una violazione di accesso quando `model`, `model.Movies` o `model.Movies[0]` sono `null` o vuoti. Quando invece l'espressione lambda viene valutata, ad esempio `@Html.DisplayFor(modelItem => item.Title)`, vengono valutati i valori delle proprietà del modello.

Eseguire il test dell'app effettuando una ricerca per genere, titolo del film ed entrambi:

![Finestra del browser con i risultati di https://localhost:5001/Movies?MovieGenre=Comedy&SearchString=2](~/tutorials/first-mvc-app/search/_static/s2.png)

> [!div class="step-by-step"]
> [Successivo](controller-methods-views.md)
> [precedente](new-field.md)
