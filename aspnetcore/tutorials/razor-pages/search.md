---
title: Parte 6, aggiungere la ricerca a Razor pagine ASP.NET Core
author: rick-anderson
description: Parte 6 della serie di esercitazioni sulle Razor pagine.
ms.author: riande
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
uid: tutorials/razor-pages/search
ms.openlocfilehash: b28d228449549e1071df4100ee2d52626c50845b
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/08/2020
ms.locfileid: "88021640"
---
# <a name="part-6-add-search-to-aspnet-core-no-locrazor-pages"></a>Parte 6, aggiungere la ricerca a Razor pagine ASP.NET Core

Autore: [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

Nelle sezioni seguenti viene aggiunta la funzionalità di ricerca di film in base al *genere* oppure al *nome*.

Aggiungere le proprietà evidenziate seguenti in *Pages/Movies/Index.cshtml.cs*:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_newProps&highlight=11-999)]

* `SearchString`: contiene il testo immesso dagli utenti nella casella di testo di ricerca. `SearchString`dispone dell' [`[BindProperty]`](/dotnet/api/microsoft.aspnetcore.mvc.bindpropertyattribute) attributo. `[BindProperty]` associa i valori modulo e le stringhe di query al nome della proprietà. `(SupportsGet = true)` è necessario per l'associazione alle richieste GET.
* `Genres` contiene l'elenco dei generi. `Genres` consente all'utente di selezionare un genere dall'elenco. `SelectList` richiede `using Microsoft.AspNetCore.Mvc.Rendering;`
* `MovieGenre`: contiene il genere specificato selezionato dall'utente, ad esempio "Western".
* `Genres` e `MovieGenre` sono utilizzati più avanti in questa esercitazione.

[!INCLUDE[](~/includes/bind-get.md)]

Aggiornare il metodo `OnGetAsync` della pagina di indice con il codice seguente:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_1stSearch)]

La prima riga del metodo `OnGetAsync` crea una query [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) per selezionare i film:

```csharp
// using System.Linq;
var movies = from m in _context.Movie
             select m;
```

La query viene *solo* definita in questo punto, ma **non** viene eseguita nel database.

Se la proprietà `SearchString` non è null o vuota, la query dei film viene modificata per filtrare gli elementi in base alla stringa di ricerca:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_SearchNull)]

Il codice `s => s.Title.Contains()` è un'[espressione lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions). Le espressioni lambda vengono usate nelle query [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) basate sul metodo come argomenti dei metodi di operatore query standard, ad esempio il metodo [Where](/dotnet/csharp/programming-guide/concepts/linq/query-syntax-and-method-syntax-in-linq) o `Contains` (usato nel codice precedente). Le query LINQ non vengono eseguite al momento della definizione o della modifica chiamando un metodo, ad esempio `Where`, `Contains` o `OrderBy`. L'esecuzione della query viene invece posticipata. Per esecuzione posticipata si intende che la valutazione di un'espressione viene ritardata finché non viene eseguita l'iterazione del valore ottenuto o non viene chiamato il metodo `ToListAsync`. Per altre informazioni, vedere [Esecuzione di query](/dotnet/framework/data/adonet/ef/language-reference/query-execution).

> [!NOTE]
> il metodo [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) viene eseguito nel database, non nel codice C#. La distinzione tra maiuscole/minuscole nella query dipende dal database e dalle regole di confronto. In SQL Server `Contains` esegue il mapping a [SQL LIKE](/sql/t-sql/language-elements/like-transact-sql) che fa distinzione tra maiuscole e minuscole. In SQLite, con le regole di confronto predefinite si fa distinzione tra maiuscole e minuscole.

Passare alla pagina Movies e aggiungere una stringa di query, ad esempio `?searchString=Ghost` all'URL, ad esempio `https://localhost:5001/Movies?searchString=Ghost`. Vengono visualizzati i film filtrati.

![Vista Index](search/_static/ghost.png)

Se il modello di route seguente viene aggiunto alla pagina di indice, la stringa di ricerca può essere passata come segmento di URL, ad esempio `https://localhost:5001/Movies/Ghost`.

```cshtml
@page "{searchString?}"
```

Il vincolo di route precedente consente la ricerca del titolo come dati della route (un segmento URL), anziché come valore della stringa di query.  Il carattere `?` in `"{searchString?}"` indica che questo parametro di route è facoltativo.

![Vista Index con la parola ghost aggiunta all'URL e un elenco restituito di due film: Ghostbusters e Ghostbusters 2](search/_static/g2.png)

Il runtime di ASP.NET Core usa l'[associazione di modelli](xref:mvc/models/model-binding) per impostare il valore della proprietà `SearchString` dalla stringa di query (`?searchString=Ghost`) o dai dati della route (`https://localhost:5001/Movies/Ghost`). All'associazione di modelli non viene applicata la distinzione tra maiuscole e minuscole.

Non è tuttavia possibile supporre che gli utenti modifichino l'URL per cercare un film. In questo passaggio viene aggiunta l'interfaccia utente per filtrare i film. Rimuovere il vincolo di route `"{searchString?}"`, se è stato aggiunto.

Aprire il file *Pages/Movies/Index.cshtml* e aggiungere il markup `<form>` evidenziato nel codice seguente:

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/SnapShots/Index2.cshtml?highlight=14-19&range=1-22)]

Il tag HTML `<form>` usa gli [helper tag](xref:mvc/views/tag-helpers/intro) seguenti:

* [Helper tag di form](xref:mvc/views/working-with-forms#the-form-tag-helper). Quando il modulo viene inviato, la stringa di filtro verrà inviata alla pagina *Pages/Movies/Index* tramite una stringa di query.
* [Helper tag di input](xref:mvc/views/working-with-forms#the-input-tag-helper)

Salvare le modifiche e testare il filtro.

![Vista Index con la parola ghost digitata nella casella di testo del filtro del titolo](search/_static/filter.png)

## <a name="search-by-genre"></a>Ricerca in base al genere

Aggiornare il metodo `OnGetAsync` con il codice seguente:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_SearchGenre)]

Il codice seguente è una query LINQ che recupera tutti i generi dal database.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_LINQ)]

L'elenco `SelectList` di generi viene creato selezionando generi distinti.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_SelectList)]

### <a name="add-search-by-genre-to-the-no-locrazor-page"></a>Aggiungi la ricerca per genere alla Razor pagina

Aggiornare *Index.cshtml* come indicato di seguito:

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/SnapShots/IndexFormGenreNoRating.cshtml?highlight=16-18&range=1-26)]

Eseguire il test dell'app effettuando una ricerca per genere, titolo del film e usando entrambi i filtri.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Versione YouTube dell'esercitazione](https://youtu.be/4B6pHtdyo08)

> [!div class="step-by-step"]
> [Precedente: aggiornamento delle pagine](xref:tutorials/razor-pages/da1) 
>  Passaggio [successivo: aggiunta di un nuovo campo](xref:tutorials/razor-pages/new-field)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

Nelle sezioni seguenti viene aggiunta la funzionalità di ricerca di film in base al *genere* oppure al *nome*.

Aggiungere le proprietà evidenziate seguenti in *Pages/Movies/Index.cshtml.cs*:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_newProps&highlight=11-999)]

* `SearchString`: contiene il testo immesso dagli utenti nella casella di testo di ricerca. `SearchString`dispone dell' [`[BindProperty]`](/dotnet/api/microsoft.aspnetcore.mvc.bindpropertyattribute) attributo. `[BindProperty]` associa i valori modulo e le stringhe di query al nome della proprietà. `(SupportsGet = true)` è necessario per l'associazione alle richieste GET.
* `Genres` contiene l'elenco dei generi. `Genres` consente all'utente di selezionare un genere dall'elenco. `SelectList` richiede `using Microsoft.AspNetCore.Mvc.Rendering;`
* `MovieGenre`: contiene il genere specificato selezionato dall'utente, ad esempio "Western".
* `Genres` e `MovieGenre` sono utilizzati più avanti in questa esercitazione.

[!INCLUDE[](~/includes/bind-get.md)]

Aggiornare il metodo `OnGetAsync` della pagina di indice con il codice seguente:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_1stSearch)]

La prima riga del metodo `OnGetAsync` crea una query [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) per selezionare i film:

```csharp
// using System.Linq;
var movies = from m in _context.Movie
             select m;
```

La query viene *solo* definita in questo punto, ma **non** viene eseguita nel database.

Se la proprietà `SearchString` non è null o vuota, la query dei film viene modificata per filtrare gli elementi in base alla stringa di ricerca:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_SearchNull)]

Il codice `s => s.Title.Contains()` è un'[espressione lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions). Le espressioni lambda vengono usate nelle query [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) basate sul metodo come argomenti dei metodi di operatore query standard, ad esempio il metodo [Where](/dotnet/csharp/programming-guide/concepts/linq/query-syntax-and-method-syntax-in-linq) o `Contains` (usato nel codice precedente). Le query LINQ non vengono eseguite al momento della definizione o della modifica chiamando un metodo, ad esempio `Where`, `Contains` o `OrderBy`. L'esecuzione della query viene invece posticipata. Per esecuzione posticipata si intende che la valutazione di un'espressione viene ritardata finché non viene eseguita l'iterazione del valore ottenuto o non viene chiamato il metodo `ToListAsync`. Per altre informazioni, vedere [Esecuzione di query](/dotnet/framework/data/adonet/ef/language-reference/query-execution).

**Nota:** il metodo [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) viene eseguito sul database, non nel codice C#. La distinzione tra maiuscole/minuscole nella query dipende dal database e dalle regole di confronto. In SQL Server `Contains` esegue il mapping a [SQL LIKE](/sql/t-sql/language-elements/like-transact-sql) che fa distinzione tra maiuscole e minuscole. In SQLite, con le regole di confronto predefinite si fa distinzione tra maiuscole e minuscole.

Passare alla pagina Movies e aggiungere una stringa di query, ad esempio `?searchString=Ghost` all'URL, ad esempio `https://localhost:5001/Movies?searchString=Ghost`. Vengono visualizzati i film filtrati.

![Vista Index](search/_static/ghost.png)

Se il modello di route seguente viene aggiunto alla pagina di indice, la stringa di ricerca può essere passata come segmento di URL, ad esempio `https://localhost:5001/Movies/Ghost`.

```cshtml
@page "{searchString?}"
```

Il vincolo di route precedente consente la ricerca del titolo come dati della route (un segmento URL), anziché come valore della stringa di query.  Il carattere `?` in `"{searchString?}"` indica che questo parametro di route è facoltativo.

![Vista Index con la parola ghost aggiunta all'URL e un elenco restituito di due film: Ghostbusters e Ghostbusters 2](search/_static/g2.png)

Il runtime di ASP.NET Core usa l'[associazione di modelli](xref:mvc/models/model-binding) per impostare il valore della proprietà `SearchString` dalla stringa di query (`?searchString=Ghost`) o dai dati della route (`https://localhost:5001/Movies/Ghost`). All'associazione di modelli non viene applicata la distinzione tra maiuscole e minuscole.

Non è tuttavia possibile supporre che gli utenti modifichino l'URL per cercare un film. In questo passaggio viene aggiunta l'interfaccia utente per filtrare i film. Rimuovere il vincolo di route `"{searchString?}"`, se è stato aggiunto.

Aprire il file *Pages/Movies/Index.cshtml* e aggiungere il markup `<form>` evidenziato nel codice seguente:

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index2.cshtml?highlight=14-19&range=1-22)]

Il tag HTML `<form>` usa gli [helper tag](xref:mvc/views/tag-helpers/intro) seguenti:

* [Helper tag di form](xref:mvc/views/working-with-forms#the-form-tag-helper). Quando il modulo viene inviato, la stringa di filtro verrà inviata alla pagina *Pages/Movies/Index* tramite una stringa di query.
* [Helper tag di input](xref:mvc/views/working-with-forms#the-input-tag-helper)

Salvare le modifiche e testare il filtro.

![Vista Index con la parola ghost digitata nella casella di testo del filtro del titolo](search/_static/filter.png)

## <a name="search-by-genre"></a>Ricerca in base al genere

Aggiornare il metodo `OnGetAsync` con il codice seguente:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_SearchGenre)]

Il codice seguente è una query LINQ che recupera tutti i generi dal database.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_LINQ)]

L'elenco `SelectList` di generi viene creato selezionando generi distinti.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_SelectList)]

### <a name="add-search-by-genre-to-the-no-locrazor-page"></a>Aggiungi la ricerca per genere alla Razor pagina

Aggiornare *Index.cshtml* come indicato di seguito:

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/IndexFormGenreNoRating.cshtml?highlight=16-18&range=1-26)]

Eseguire il test dell'app effettuando una ricerca per genere, titolo del film e usando entrambi i filtri.
Il codice precedente usa l'helper [tag SELECT](xref:mvc/views/working-with-forms#the-select-tag-helper) e l'helper tag option.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Versione YouTube dell'esercitazione](https://youtu.be/4B6pHtdyo08)

> [!div class="step-by-step"]
> [Precedente: aggiornamento delle pagine](xref:tutorials/razor-pages/da1) 
>  Passaggio [successivo: aggiunta di un nuovo campo](xref:tutorials/razor-pages/new-field)

::: moniker-end
