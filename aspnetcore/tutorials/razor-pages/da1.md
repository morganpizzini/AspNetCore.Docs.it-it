---
title: 'Parte 5: aggiornare le pagine generate'
author: rick-anderson
description: Parte 5 della serie di esercitazioni sulle Razor pagine.
ms.author: riande
ms.date: 09/20/2020
ms.custom: contperf-fy21q2
no-loc:
- Index
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
uid: tutorials/razor-pages/da1
ms.openlocfilehash: 46fbfb50afd03f918f9e02bcc8c1dbde9a080ca4
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/04/2021
ms.locfileid: "97485940"
---
# <a name="part-5-update-the-generated-pages-in-an-aspnet-core-app"></a>Parte 5: aggiornare le pagine generate in un'app ASP.NET Core

Autore: [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

Le operazioni iniziali con l'app per i film creata con scaffolding sono state efficaci, ma la presentazione non è ottimale. Il rilascio **deve essere** costituito da due parole, **Data di rilascio**.

![App per i film aperta in Chrome](sql/_static/5/m55.png)

## <a name="update-the-generated-code"></a>Aggiornare il codice generato

Aprire il file *Models/Movie.cs* e aggiungere le righe evidenziate illustrate nel codice seguente:

[!code-csharp[Main](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateFixed.cs?name=snippet_1&highlight=3,12,17)]

Nel codice precedente:

* L'annotazione dei dati `[Column(TypeName = "decimal(18, 2)")]` consente a Entity Framework Core di eseguire correttamente il mapping di `Price` nella valuta del database. Per altre informazioni, vedere [Tipi di dati](/ef/core/modeling/relational/data-types).
* L'attributo [[Display]](xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DisplayMetadata) specifica il nome visualizzato di un campo. Nel codice precedente, "release date" invece di "ReleaseDate".
* L'attributo [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) specifica il tipo di dati ( `Date` ). Le informazioni sull'ora archiviate nel campo non vengono visualizzate.

L'attributo [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) viene esaminato nell'esercitazione successiva.

Passare a *pagine/filmati* e passare il puntatore del mouse su un collegamento **modifica** per visualizzare l'URL di destinazione.

![Finestra del browser con il passaggio del mouse sul collegamento Edit (Modifica) e un URL di collegamento di https://localhost:1234/Movies/Edit/5](~/tutorials/razor-pages/da1/edit7.png)

I collegamenti **modifica**, **Dettagli** ed **Elimina** vengono generati dall' [Helper tag di ancoraggio](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) nel file *pages/Movies/ Index . cshtml* .

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?highlight=16-18&range=32-)]

Gli [Helper Tag](xref:mvc/views/tag-helpers/intro) consentono al codice lato server di partecipare alla creazione e al rendering di elementi HTML nei Razor file.

Nel codice precedente, l' [Helper tag di ancoraggio](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) genera in modo dinamico il `href` valore dell'attributo HTML dalla Razor pagina (la route è relativa), `asp-page` e l'identificatore della route ( `asp-route-id` ). Per ulteriori informazioni, vedere [generazione di URL per le pagine](xref:razor-pages/index#url-generation-for-pages).

Usare **Visualizza origine** da un browser per esaminare il markup generato. Di seguito è riportata una parte del codice HTML generato:

```html
<td>
  <a href="/Movies/Edit?id=1">Edit</a> |
  <a href="/Movies/Details?id=1">Details</a> |
  <a href="/Movies/Delete?id=1">Delete</a>
</td>
```

   I collegamenti generati dinamicamente passano l'ID del film con una stringa di query. Ad esempio, `?id=1` in `https://localhost:5001/Movies/Details?id=1` .

### <a name="add-route-template"></a>Aggiungere un modello di route

Aggiornare le pagine modifica, dettagli ed Elimina Razor per usare il `{id:int}` modello di route. Modificare la direttiva page per ognuna di queste pagine da `@page` a `@page "{id:int}"`. Eseguire l'app e quindi visualizzare l'origine.

Il codice HTML generato aggiunge l'ID alla parte di percorso dell'URL:

```html
<td>
  <a href="/Movies/Edit/1">Edit</a> |
  <a href="/Movies/Details/1">Details</a> |
  <a href="/Movies/Delete/1">Delete</a>
</td>
```

Una richiesta alla pagina con il `{id:int}` modello di route che **non** include il valore integer restituirà un errore HTTP 404 (non trovato). Ad esempio, `https://localhost:5001/Movies/Details` restituirà un errore 404. Per rendere l'ID facoltativo, aggiungere `?` al vincolo di route:

```cshtml
@page "{id:int?}"
```

Testare il comportamento di `@page "{id:int?}"` :

1. Impostare la direttiva page in *Pages/Movies/Details.cshtml* su `@page "{id:int?}"`.
1. Impostare un punto di rottura in `public async Task<IActionResult> OnGetAsync(int? id)` , in *pages/Movies/details. cshtml. cs*.
1. Accedere a `https://localhost:5001/Movies/Details/`.

Con l'istruzione `@page "{id:int}"`, il punto di interruzione non viene mai raggiunto. Il motore di routing restituisce HTTP 404. Utilizzando `@page "{id:int?}"` , il `OnGetAsync` metodo restituisce `NotFound` (http 404):

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Pages/Movies/Details.cshtml.cs?name=snippet1&highlight=10-13)]

### <a name="review-concurrency-exception-handling"></a>Verificare la gestione delle eccezioni di concorrenza

Verificare il metodo `OnPostAsync` nel file *Pages/Movies/Edit.cshtml.cs*:

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Edit.cshtml.cs?name=snippet)]

Il codice precedente rileva le eccezioni di concorrenza quando un client elimina il film e l'altro client invia le modifiche al film.

Per testare il blocco `catch`:

1. Impostare un punto di interruzione su `catch (DbUpdateConcurrencyException)` .
1. Selezionare **Edit** (Modifica) per un film, apportare modifiche, ma non immettere **Save** (Salva).
1. In un'altra finestra del browser, selezionare il collegamento **Delete** (Elimina) per lo stesso film e quindi eliminare il film.
1. Nella finestra del browser precedente inviare le modifiche al film.

In alcuni casi, il codice utilizzabile in ambienti di produzione potrebbe voler rilevare i conflitti di concorrenza. Per altre informazioni, vedere [Gestire i conflitti di concorrenza](xref:data/ef-rp/concurrency).

### <a name="posting-and-binding-review"></a>Invio di post e analisi delle associazioni

Esaminare il file *Pages/Movies/Edit.cshtml.cs*: 

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/SnapShots/Edit.cshtml.cs?name=snippet2)]

Quando viene effettuata una richiesta HTTP GET alla pagina Movies/Edit, ad esempio `https://localhost:5001/Movies/Edit/3` :

* Il metodo `OnGetAsync` recupera il film dal database e restituisce il metodo `Page`.
* Il `Page` metodo esegue il rendering della pagina *pages/Movies/Edit. cshtml* Razor . Il file *pages/Movies/Edit. cshtml* contiene la direttiva Model `@model RazorPagesMovie.Pages.Movies.EditModel` che rende il modello di film disponibile nella pagina.
* Il modulo Edit (Modifica) viene visualizzato con i valori dal film.

Quando viene inviata la pagina Movies/Edit (Film/Modifica):

* I valori del modulo nella pagina vengono associati alla proprietà `Movie`. L'attributo `[BindProperty]` abilita l'[associazione di modelli](xref:mvc/models/model-binding).

  ```csharp
  [BindProperty]
  public Movie Movie { get; set; }
  ```

* Se si verificano errori nello stato del modello, ad esempio, `ReleaseDate` non può essere convertito in una data, il form viene visualizzato nuovamente con i valori inviati.
* Se non sono presenti errori del modello, il film viene salvato.

I metodi HTTP GET nelle Index pagine, create ed Delete Razor seguono un modello simile. Il metodo HTTP POST `OnPostAsync` nella pagina Create Razor segue un modello simile al `OnPostAsync` metodo nella pagina Edit (modifica) Razor .

## <a name="additional-resources"></a>Risorse aggiuntive

> [!div class="step-by-step"]
> [Precedente: utilizzo di un database](xref:tutorials/razor-pages/sql) 
>  Passaggio [successivo: aggiungere la ricerca](xref:tutorials/razor-pages/search)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Le operazioni iniziali con l'app per i film creata con scaffolding sono state efficaci, ma la presentazione non è ottimale. Il rilascio **deve essere** costituito da due parole, **Data di rilascio**.

![App per i film aperta in Chrome](sql/_static/m55https.png)

## <a name="update-the-generated-code"></a>Aggiornare il codice generato

Aprire il file *Models/Movie.cs* e aggiungere le righe evidenziate illustrate nel codice seguente:

[!code-csharp[Main](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/MovieDateFixed.cs?name=snippet_1&highlight=3,12,17)]

L'annotazione dei dati `[Column(TypeName = "decimal(18, 2)")]` consente a Entity Framework Core di eseguire correttamente il mapping di `Price` nella valuta del database. Per altre informazioni, vedere [Tipi di dati](/ef/core/modeling/relational/data-types).

L'attributo [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) viene esaminato nell'esercitazione successiva. L'attributo [[Display]](xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DisplayMetadata) specifica gli elementi da visualizzare per il nome di un campo, in questo caso "release date" invece di "ReleaseDate". L'attributo [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) specifica il tipo di dati ( `Date` ), quindi le informazioni sull'ora archiviate nel campo non vengono visualizzate.

Accedere a Pages/Movies e passare il mouse su un collegamento **Edit** (Modifica) per visualizzare l'URL di destinazione.

![Finestra del browser con il passaggio del mouse sul collegamento Edit (Modifica) e un URL di collegamento di http://localhost:1234/Movies/Edit/5](~/tutorials/razor-pages/da1/edit7.png)

I collegamenti **modifica**, **Dettagli** ed **Elimina** vengono generati dall' [Helper tag di ancoraggio](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) nel file *pages/Movies/ Index . cshtml* .

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?highlight=16-18&range=32-)]

Gli [Helper Tag](xref:mvc/views/tag-helpers/intro) consentono al codice lato server di partecipare alla creazione e al rendering di elementi HTML nei Razor file. Nel codice precedente, genera in `AnchorTagHelper` modo dinamico il valore dell' `href` attributo HTML dalla Razor pagina (la route è relativa), `asp-page` , e l'ID di route ( `asp-route-id` ). Per altre informazioni, vedere [Generazione di URL per le pagine](xref:razor-pages/index#url-generation-for-pages).

Usare **Visualizza origine** da un browser per esaminare il markup generato. Di seguito è riportata una parte del codice HTML generato:

```html
<td>
  <a href="/Movies/Edit?id=1">Edit</a> |
  <a href="/Movies/Details?id=1">Details</a> |
  <a href="/Movies/Delete?id=1">Delete</a>
</td>
```

I collegamenti generati dinamicamente passano l'ID del film con una stringa di query. Ad esempio, `?id=1` in  `https://localhost:5001/Movies/Details?id=1` .

Aggiornare le pagine modifica, dettagli ed Elimina Razor per usare il modello di route "{ID: int}". Modificare la direttiva page per ognuna di queste pagine da `@page` a `@page "{id:int}"`. Eseguire l'app e quindi visualizzare l'origine. Il codice HTML generato aggiunge l'ID alla parte di percorso dell'URL:

```html
<td>
  <a href="/Movies/Edit/1">Edit</a> |
  <a href="/Movies/Details/1">Details</a> |
  <a href="/Movies/Delete/1">Delete</a>
</td>
```

Una richiesta alla pagina con il modello di route "{id: int}" che **non** include l'intero restituirà un errore HTTP 404 (Non trovato). Ad esempio, `https://localhost:5001/Movies/Details` restituirà un errore 404. Per rendere l'ID facoltativo, aggiungere `?` al vincolo di route:

 ```cshtml
@page "{id:int?}"
```

Per testare il comportamento di `@page "{id:int?}"`:

* Impostare la direttiva page in *Pages/Movies/Details.cshtml* su `@page "{id:int?}"`.
* Impostare un punto di rottura in `public async Task<IActionResult> OnGetAsync(int? id)` , in *pages/Movies/details. cshtml. cs*.
* Accedere a `https://localhost:5001/Movies/Details/`.

Con l'istruzione `@page "{id:int}"`, il punto di interruzione non viene mai raggiunto. Il motore di routing restituisce HTTP 404. Utilizzando `@page "{id:int?}"` , il `OnGetAsync` metodo restituisce `NotFound` (http 404):

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Details.cshtml.cs?name=snippet1&highlight=10-13)]

### <a name="review-concurrency-exception-handling"></a>Verificare la gestione delle eccezioni di concorrenza

Verificare il metodo `OnPostAsync` nel file *Pages/Movies/Edit.cshtml.cs*:

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Edit.cshtml.cs?name=snippet)]

Il codice precedente rileva le eccezioni di concorrenza quando un client elimina il film e l'altro client invia modifiche al film.

Per testare il blocco `catch`:

* Impostare un punto di interruzione su `catch (DbUpdateConcurrencyException)`
* Selezionare **Edit** (Modifica) per un film, apportare modifiche, ma non immettere **Save** (Salva).
* In un'altra finestra del browser, selezionare il collegamento **Delete** (Elimina) per lo stesso film e quindi eliminare il film.
* Nella finestra del browser precedente inviare le modifiche al film.

In alcuni casi, il codice utilizzabile in ambienti di produzione potrebbe voler rilevare i conflitti di concorrenza. Per altre informazioni, vedere [Gestire i conflitti di concorrenza](xref:data/ef-rp/concurrency).

### <a name="posting-and-binding-review"></a>Invio di post e analisi delle associazioni

Esaminare il file *Pages/Movies/Edit.cshtml.cs*: 

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Edit21.cshtml.cs?name=snippet2)]

Quando viene effettuata una richiesta HTTP GET alla pagina Movies/Edit, ad esempio `https://localhost:5001/Movies/Edit/3` :

* Il metodo `OnGetAsync` recupera il film dal database e restituisce il metodo `Page`. 
* Il `Page` metodo esegue il rendering della pagina *pages/Movies/Edit. cshtml* Razor . Il file *pages/Movies/Edit. cshtml* contiene la direttiva Model `@model RazorPagesMovie.Pages.Movies.EditModel` che rende il modello di film disponibile nella pagina.
* Il modulo Edit (Modifica) viene visualizzato con i valori dal film.

Quando viene inviata la pagina Movies/Edit (Film/Modifica):

* I valori del modulo nella pagina vengono associati alla proprietà `Movie`. L'attributo `[BindProperty]` abilita l'[associazione di modelli](xref:mvc/models/model-binding).

  ```csharp
  [BindProperty]
  public Movie Movie { get; set; }
  ```

* Se si verificano errori nello stato del modello, ad esempio, `ReleaseDate` non può essere convertito in una data, il form viene visualizzato con i valori inviati.
* Se non sono presenti errori del modello, il film viene salvato.

I metodi HTTP GET nelle Index pagine, create ed Delete Razor seguono un modello simile. Il metodo HTTP POST `OnPostAsync` nella pagina Create Razor segue un modello simile al `OnPostAsync` metodo nella pagina Edit (modifica) Razor .

La funzionalità di ricerca viene aggiunta nell'esercitazione successiva.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Versione YouTube dell'esercitazione](https://youtu.be/yLnnleREMtQ)

> [!div class="step-by-step"]
> [Precedente: utilizzo di un database](xref:tutorials/razor-pages/sql) 
>  Passaggio [successivo: aggiungere la ricerca](xref:tutorials/razor-pages/search)

::: moniker-end
