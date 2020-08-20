---
title: Parte 3, Razor pagine con impalcatura in ASP.NET Core
author: rick-anderson
description: Parte 3 della serie di esercitazioni sulle Razor pagine.
ms.author: riande
ms.date: 08/17/2019
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
uid: tutorials/razor-pages/page
ms.openlocfilehash: 9fc44d228a31b8ae6c78dc12988392806dd95823
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/19/2020
ms.locfileid: "88633435"
---
# <a name="part-3-scaffolded-no-locrazor-pages-in-aspnet-core"></a>Parte 3, Razor pagine con impalcatura in ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Autore: [Rick Anderson](https://twitter.com/RickAndMSFT)

Questa esercitazione esamina le Razor pagine create dall'impalcatura nell' [esercitazione precedente](xref:tutorials/razor-pages/model).

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

## <a name="the-create-delete-details-and-edit-pages"></a>Pagine di creazione, eliminazione, dettagli e modifica

Esaminare il modello di pagina *Pages/Movies/Index.cshtml.cs*: 

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs)]

Razor Le pagine sono derivate da `PageModel` . Per convenzione, la classe derivata `PageModel` viene denominata `<PageName>Model`. Il costruttore usa l'[inserimento delle dipendenze](xref:fundamentals/dependency-injection) per aggiungere `RazorPagesMovieContext` alla pagina. Tutte le pagine create tramite scaffolding seguono questo schema. Vedere [Codice asincrono](xref:data/ef-rp/intro#asynchronous-code) per altre informazioni sulla programmazione asincrona con Entity Framework.

Quando viene effettuata una richiesta per la pagina, il `OnGetAsync` metodo restituisce un elenco di filmati alla Razor pagina. `OnGetAsync``OnGet`viene chiamato o per inizializzare lo stato della pagina. In questo caso, `OnGetAsync` ottiene un elenco di filmati e li visualizza.

Quando `OnGet` restituisce `void` o `OnGetAsync` restituisce `Task` , non viene utilizzata alcuna istruzione return. Quando il tipo restituito è `IActionResult` o `Task<IActionResult>`, è necessario specificare un'istruzione return. Ad esempio, il metodo *Pages/Movies/Create.cshtml.cs* `OnPostAsync`:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippet)]

<a name="index"></a> Esaminare la pagina *pages/movies/index. cshtml* Razor :

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml)]

Razor consente di eseguire la transizione da HTML a C# o a un Razor markup specifico. Quando un `@` simbolo è seguito da una [ Razor parola chiave riservata](xref:mvc/views/razor#razor-reserved-keywords), esegue la transizione in un Razor markup specifico, in caso contrario passa a C#.

### <a name="the-page-directive"></a>La direttiva @page

La `@page` Razor direttiva rende il file un'azione MVC, il che significa che può gestire le richieste. `@page` deve essere la prima Razor direttiva in una pagina. `@page` è un esempio di transizione di Razor markup specifico a. Per ulteriori informazioni, vedere la [ Razor sintassi](xref:mvc/views/razor#razor-syntax) .

Esaminare l'espressione lambda usata nell'helper HTML seguente:

```cshtml
@Html.DisplayNameFor(model => model.Movie[0].Title)
```

L'helper HTML `DisplayNameFor` controlla la proprietà `Title` a cui fa riferimento nell'espressione lambda per determinare il nome visualizzato. L'espressione lambda viene controllata anziché valutata. Ciò significa che non esiste alcuna violazione di accesso quando `model` , `model.Movie` o `model.Movie[0]` è `null` o vuoto. Quando invece l'espressione lambda viene valutata (ad esempio con `@Html.DisplayFor(modelItem => item.Title)`), vengono valutati i valori proprietà del modello.

<a name="md"></a>

### <a name="the-model-directive"></a>La direttiva @model

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml?range=1-2&highlight=2)]

La `@model` direttiva specifica il tipo del modello passato alla Razor pagina. Nell'esempio precedente la `@model` riga rende `PageModel` disponibile la classe derivata da per la Razor pagina. Il modello viene usato negli  [helper HTML](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers)`@Html.DisplayNameFor` e `@Html.DisplayFor` nella pagina.

### <a name="the-layout-page"></a>Pagina di layout

Selezionare i collegamenti ai menu (** Razor PagesMovie**, **Home**e **privacy**). Ogni pagina mostra lo stesso layout di menu. Il layout di menu viene implementato nel file *Pages/Shared/_Layout.cshtml*. Aprire il file *Pages/Shared/_Layout.cshtml*.

I modelli [Layout](xref:mvc/views/layout) consentono di:

* Specificare il layout del contenitore HTML in un'unica posizione.
* Applicare il layout in più pagine del sito.

Trovare la riga `@RenderBody()`. `RenderBody` è un segnaposto dove tutte le visualizzazioni specifiche della pagina vengono presentate, *incapsulate* nella pagina di layout. Ad esempio, selezionare il collegamento **Privacy** per eseguire il rendering della visualizzazione *Pages/Privacy.cshtml* all'interno del metodo `RenderBody`.

<a name="vd"></a>

### <a name="viewdata-and-layout"></a>ViewData e layout

Si consideri il markup seguente dal file *Pages/Movies/Index.cshtml*:

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml?range=1-6&highlight=4-999)]

Il markup evidenziato sopra è un esempio di Razor transizione in C#. Tra i caratteri `{` e `}` è racchiuso un blocco di codice C#.

La `PageModel` classe base contiene una `ViewData` Proprietà Dictionary che può essere utilizzata per passare dati a una visualizzazione. Gli oggetti vengono aggiunti al dizionario `ViewData` usando uno schema chiave/valore. Nell'esempio precedente la proprietà `"Title"` viene aggiunta al dizionario `ViewData`.

La proprietà `"Title"` viene usata nel file *Pages/Shared/_Layout.cshtml*. Il markup seguente illustra le prime righe del file *_Layout.cshtml*.

<!-- we need a snapshot copy of layout because we are
changing in in the next step.
-->
[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/NU/_Layout.cshtml?highlight=6)]

La riga `@*Markup removed for brevity.*@` è un Razor commento. Diversamente dai commenti HTML ( `<!-- -->` ), Razor i commenti non vengono inviati al client.

### <a name="update-the-layout"></a>Aggiornare il layout

Modificare l' `<title>` elemento nel file *pages/Shared/_Layout. cshtml* per visualizzare il **film** anziché ** Razor PagesMovie**.

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/Pages/Shared/_Layout.cshtml?range=1-6&highlight=6)]

Trovare l'elemento di ancoraggio seguente nel file *Pages/Shared/_Layout.cshtml*.

```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Index">RazorPagesMovie</a>
```

Sostituire l'elemento precedente con il markup seguente:

```cshtml
<a class="navbar-brand" asp-page="/Movies/Index">RpMovie</a>
```

L'elemento di ancoraggio precedente è un [helper tag](xref:mvc/views/tag-helpers/intro). In questo caso, si tratta dell'[helper tag di ancoraggio](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper). L' `asp-page="/Movies/Index"` attributo e il valore dell'helper Tag creano un collegamento alla `/Movies/Index` Razor pagina. Poiché il valore dell'attributo `asp-area` è vuoto, l'area non viene usata nel collegamento. Per altre informazioni, vedere [Aree](xref:mvc/controllers/areas).

Salvare le modifiche e testare l'app selezionando il collegamento **RpMovie**. In caso di problemi, vedere il file [_Layout.cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Shared/_Layout.cshtml) in GitHub.

Testare gli altri collegamenti (**Home**, **RpMovie**, **Create**, **Edit** e **Delete**). Ogni pagina imposta il titolo, che è possibile visualizzare nella scheda del browser. Quando si aggiunge un segnalibro a una pagina, il titolo viene usato per il segnalibro.

> [!NOTE]
> Potrebbe non essere possibile immettere virgole decimali nel campo `Price`. Per supportare la [convalida jQuery](https://jqueryvalidation.org/) per impostazioni locali diverse dall'inglese che usano la virgola (",") come separatore decimale e per formati di data diversi da quello dell'inglese degli Stati Uniti, è necessario eseguire alcuni passaggi per globalizzare l'app. Vedere questo [problema 4076 su GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) per istruzioni sull'aggiunta della virgola decimale.

La proprietà `Layout` viene impostata nel file *Pages/_ViewStart.cshtml*:

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/Pages/_ViewStart.cshtml)]

Il markup precedente imposta il file di layout su *pages/Shared/_Layout. cshtml* per tutti Razor i file nella cartella *pages* . Vedere [Layout](xref:razor-pages/index#layout) per altre informazioni.

### <a name="the-create-page-model"></a>Modello di pagina di creazione

Esaminare il modello di pagina *Pages/Movies/Create.cshtml.cs*:

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippetALL)]

Il metodo `OnGet` inizializza lo stato necessario per la pagina. La pagina di creazione non possiede uno stato da inizializzare. Viene restituito `Page`. Più avanti nell'esercitazione viene visualizzato un esempio di inizializzazione dello stato con `OnGet`. Il metodo `Page` crea un oggetto `PageResult` che esegue il rendering della pagina *Create.cshtml*.

La proprietà `Movie` usa l'attributo `[BindProperty]` per consentire l'[associazione di modelli](xref:mvc/models/model-binding). Dopo che il modulo di creazione registra i valori, il runtime di ASP.NET Core associa i valori registrati al modello `Movie`.

Il metodo `OnPostAsync` viene eseguito quando la pagina inserisce i dati del modulo:

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippetPost)]

Se il modello contiene errori, il modulo viene nuovamente visualizzato insieme ai dati del modulo inviati. La maggior parte degli errori del modello sono riscontrabili sul lato client prima che il modulo sia inviato. La registrazione di un valore per il campo data non convertibile in una data è un esempio di errore del modello. Durante l'esercitazione saranno poi trattate nel dettaglio la convalida lato client e la convalida del modello.

Se non sono presenti errori nel modello, i dati vengono salvati e il browser viene reindirizzato alla pagina di indice.

### <a name="the-create-no-locrazor-page"></a>Pagina Crea Razor

Esaminare il file di paging *pages/Movies/create. cshtml* Razor :

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml)]

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Visual Studio visualizza i tag seguenti con un tipo di carattere in grassetto distintivo specifico per gli helper tag:

* `<form method="post">`
* `<div asp-validation-summary="ModelOnly" class="text-danger"></div>`
* `<label asp-for="Movie.Title" class="control-label"></label>`
* `<input asp-for="Movie.Title" class="form-control" />`
* `<span asp-validation-for="Movie.Title" class="text-danger"></span>`

![Visualizzazione di VS17 della pagina Create.cshtml](page/_static/th3.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Nel markup precedente sono visualizzati gli helper tag seguenti:

* `<form method="post">`
* `<div asp-validation-summary="ModelOnly" class="text-danger"></div>`
* `<label asp-for="Movie.Title" class="control-label"></label>`
* `<input asp-for="Movie.Title" class="form-control" />`
* `<span asp-validation-for="Movie.Title" class="text-danger"></span>`

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/visual-studio-mac)

Visual Studio visualizza i tag seguenti con un tipo di carattere in grassetto distintivo specifico per gli helper tag:

* `<form method="post">`
* `<div asp-validation-summary="ModelOnly" class="text-danger"></div>`
* `<label asp-for="Movie.Title" class="control-label"></label>`
* `<input asp-for="Movie.Title" class="form-control" />`
* `<span asp-validation-for="Movie.Title" class="text-danger"></span>`

---

L'elemento `<form method="post">` è un [helper tag del modulo](xref:mvc/views/working-with-forms#the-form-tag-helper). L'helper tag del modulo include automaticamente un [token antifalsificazione](xref:security/anti-request-forgery).

Il motore di ponteggi crea Razor markup per ogni campo nel modello (ad eccezione dell'ID) simile al seguente:

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml?range=15-20)]

Gli [Helper tag di convalida](xref:mvc/views/working-with-forms#the-validation-tag-helpers) ( `<div asp-validation-summary` e `<span asp-validation-for` ) visualizzano gli errori di convalida. Il tema della convalida viene trattato in modo più dettagliato successivamente in questa serie.

L' [Helper tag di etichetta](xref:mvc/views/working-with-forms#the-label-tag-helper) ( `<label asp-for="Movie.Title" class="control-label"></label>` ) genera la didascalia dell'etichetta e l' `for` attributo per la `Title` Proprietà.

L' [Helper tag di input](xref:mvc/views/working-with-forms) ( `<input asp-for="Movie.Title" class="form-control">` ) USA gli attributi [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) e produce gli attributi HTML necessari per la convalida jQuery sul lato client.

Per altre informazioni sugli helper tag, ad esempio `<form method="post">`, vedere [Helper tag in ASP.NET Core](xref:mvc/views/tag-helpers/intro).

## <a name="additional-resources"></a>Risorse aggiuntive

> [!div class="step-by-step"]
> [Precedente: aggiunta di un modello](xref:tutorials/razor-pages/model) 
>  Passaggio [successivo: database](xref:tutorials/razor-pages/sql)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Autore: [Rick Anderson](https://twitter.com/RickAndMSFT)

Questa esercitazione esamina le Razor pagine create dall'impalcatura nell' [esercitazione precedente](xref:tutorials/razor-pages/model).

[Visualizzare o scaricare il campione ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22).

## <a name="the-create-delete-details-and-edit-pages"></a>Pagine di creazione, eliminazione, dettagli e modifica

Esaminare il modello di pagina *Pages/Movies/Index.cshtml.cs*: 

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml.cs)]

Razor Le pagine sono derivate da `PageModel` . Per convenzione, la classe derivata `PageModel` viene denominata `<PageName>Model`. Il costruttore usa l'[inserimento delle dipendenze](xref:fundamentals/dependency-injection) per aggiungere `RazorPagesMovieContext` alla pagina. Tutte le pagine create tramite scaffolding seguono questo schema. Vedere [Codice asincrono](xref:data/ef-rp/intro#asynchronous-code) per altre informazioni sulla programmazione asincrona con Entity Framework.

Quando viene effettuata una richiesta per la pagina, il `OnGetAsync` metodo restituisce un elenco di filmati alla Razor pagina. `OnGetAsync` o `OnGet` viene chiamato su una Razor pagina per inizializzare lo stato della pagina. In questo caso, `OnGetAsync` ottiene un elenco di filmati e li visualizza.

Quando `OnGet` restituisce `void` o `OnGetAsync` restituisce`Task`, non viene usato alcun metodo restituito. Quando il tipo restituito è `IActionResult` o `Task<IActionResult>`, è necessario specificare un'istruzione return. Ad esempio, il metodo *Pages/Movies/Create.cshtml.cs* `OnPostAsync`:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml.cs?name=snippet)]

<a name="index"></a> Esaminare la pagina *pages/movies/index. cshtml* Razor :

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml)]

Razor consente di eseguire la transizione da HTML a C# o a un Razor markup specifico. Quando un `@` simbolo è seguito da una [ Razor parola chiave riservata](xref:mvc/views/razor#razor-reserved-keywords), esegue la transizione in un Razor markup specifico, in caso contrario passa a C#.

La `@page` Razor direttiva trasforma il file in un'azione MVC, il che significa che può gestire le richieste. `@page` deve essere la prima Razor direttiva in una pagina. `@page` è un esempio di transizione di Razor markup specifico a. Per ulteriori informazioni, vedere la [ Razor sintassi](xref:mvc/views/razor#razor-syntax) .

Esaminare l'espressione lambda usata nell'helper HTML seguente:

```cshtml
@Html.DisplayNameFor(model => model.Movie[0].Title)
```

L'helper HTML `DisplayNameFor` controlla la proprietà `Title` a cui fa riferimento nell'espressione lambda per determinare il nome visualizzato. L'espressione lambda viene controllata anziché valutata. Non sussiste pertanto violazione di accesso quando `model`, `model.Movie`, o `model.Movie[0]` sono `null` o vuoti. Quando invece l'espressione lambda viene valutata (ad esempio con `@Html.DisplayFor(modelItem => item.Title)`), vengono valutati i valori proprietà del modello.

<a name="md"></a>

### <a name="the-model-directive"></a>La direttiva @model

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?range=1-2&highlight=2)]

La `@model` direttiva specifica il tipo del modello passato alla Razor pagina. Nell'esempio precedente la `@model` riga rende `PageModel` disponibile la classe derivata da per la Razor pagina. Il modello viene usato negli  [helper HTML](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers)`@Html.DisplayNameFor` e `@Html.DisplayFor` nella pagina.

### <a name="the-layout-page"></a>Pagina di layout

Selezionare i collegamenti ai menu (** Razor PagesMovie**, **Home**e **privacy**). Ogni pagina mostra lo stesso layout di menu. Il layout di menu viene implementato nel file *Pages/Shared/_Layout.cshtml*. Aprire il file *Pages/Shared/_Layout.cshtml*.

I modelli di [layout](xref:mvc/views/layout) consentono di specificare il layout del contenitore HTML del sito in un'unica posizione e quindi di applicarlo in più pagine del sito. Trovare la riga `@RenderBody()`. `RenderBody` è un segnaposto dove tutte le visualizzazioni specifiche della pagina vengono presentate, *incapsulate* nella pagina di layout. Ad esempio, se si seleziona il collegamento **Privacy**, il rendering della visualizzazione **Pages/Privacy.cshtml** viene eseguito all'interno del metodo `RenderBody`.

<a name="vd"></a>

### <a name="viewdata-and-layout"></a>ViewData e layout

Si consideri il seguente codice del file *Pages/Movies/Index.cshtml*:

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?range=1-6&highlight=4-999)]

Il codice evidenziato precedente è un esempio di Razor transizione in C#. Tra i caratteri `{` e `}` è racchiuso un blocco di codice C#.

La classe di base `PageModel` ha una proprietà del dizionario `ViewData` che può essere usata per aggiungere i dati da passare a una visualizzazione. Gli oggetti vengono aggiunti al dizionario `ViewData` usando uno schema chiave/valore. Nell'esempio precedente la proprietà "Title" viene aggiunta al dizionario `ViewData`.

La proprietà "Title" viene usata nel file *Pages/Shared/_Layout.cshtml*. Il markup seguente illustra le prime righe del file *_Layout.cshtml*.

<!-- we need a snapshot copy of layout because we are
changing in in the next step.
-->
[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/NU/_Layout.cshtml?highlight=6-99)]

La riga `@*Markup removed for brevity.*@` è un Razor commento che non viene visualizzato nel file di layout. Diversamente dai commenti HTML ( `<!-- -->` ), Razor i commenti non vengono inviati al client.

### <a name="update-the-layout"></a>Aggiornare il layout

Modificare l' `<title>` elemento nel file *pages/Shared/_Layout. cshtml* per visualizzare il **film** anziché ** Razor PagesMovie**.

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Shared/_Layout.cshtml?range=1-6&highlight=6)]

Trovare l'elemento di ancoraggio seguente nel file *Pages/Shared/_Layout.cshtml*.

```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Index">RazorPagesMovie</a>
```

Sostituire l'elemento precedente con il markup seguente.

```cshtml
<a class="navbar-brand" asp-page="/Movies/Index">RpMovie</a>
```

L'elemento di ancoraggio precedente è un [helper tag](xref:mvc/views/tag-helpers/intro). In questo caso, si tratta dell'[helper tag di ancoraggio](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper). L' `asp-page="/Movies/Index"` attributo e il valore dell'helper Tag creano un collegamento alla `/Movies/Index` Razor pagina. Poiché il valore dell'attributo `asp-area` è vuoto, l'area non viene usata nel collegamento. Per altre informazioni, vedere [Aree](xref:mvc/controllers/areas).

Salvare le modifiche e testare l'app selezionando il collegamento **RpMovie**. In caso di problemi, vedere il file [_Layout.cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Shared/_Layout.cshtml) in GitHub.

Testare gli altri collegamenti (**Home**, **RpMovie**, **Create**, **Edit** e **Delete**). Ogni pagina imposta il titolo, che è possibile visualizzare nella scheda del browser. Quando si aggiunge un segnalibro a una pagina, il titolo viene usato per il segnalibro.

> [!NOTE]
> Potrebbe non essere possibile immettere virgole decimali nel campo `Price`. Per supportare la [convalida jQuery](https://jqueryvalidation.org/) per impostazioni locali diverse dall'inglese che usano la virgola (",") come separatore decimale e per formati di data diversi da quello dell'inglese degli Stati Uniti, è necessario eseguire alcuni passaggi per globalizzare l'app. [Problema 4076 su GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) per istruzioni sull'aggiunta della virgola decimale.

La proprietà `Layout` viene impostata nel file *Pages/_ViewStart.cshtml*:

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/_ViewStart.cshtml)]

Il markup precedente imposta il file di layout su *pages/Shared/_Layout. cshtml* per tutti Razor i file nella cartella *pages* . Vedere [Layout](xref:razor-pages/index#layout) per altre informazioni.

### <a name="the-create-page-model"></a>Modello di pagina di creazione

Esaminare il modello di pagina *Pages/Movies/Create.cshtml.cs*:

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml.cs?name=snippetALL)]

Il metodo `OnGet` inizializza lo stato necessario per la pagina. La pagina di creazione non possiede uno stato da inizializzare. Viene restituito `Page`. Più avanti nell'esercitazione viene illustrato lo stato di inizializzazione del metodo `OnGet`. Il metodo `Page` crea un oggetto `PageResult` che esegue il rendering della pagina *Create.cshtml*.

La proprietà `Movie` usa l'attributo `[BindProperty]` per consentire l'[associazione di modelli](xref:mvc/models/model-binding). Dopo che il modulo di creazione registra i valori, il runtime di ASP.NET Core associa i valori registrati al modello `Movie`.

Il metodo `OnPostAsync` viene eseguito quando la pagina inserisce i dati del modulo:

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml.cs?name=snippetPost)]

Se il modello contiene errori, il modulo viene nuovamente visualizzato insieme ai dati del modulo inviati. La maggior parte degli errori del modello sono riscontrabili sul lato client prima che il modulo sia inviato. La registrazione di un valore per il campo data non convertibile in una data è un esempio di errore del modello. Durante l'esercitazione saranno poi trattate nel dettaglio la convalida lato client e la convalida del modello.

Se non sono presenti errori nel modello, i dati vengono salvati e il browser viene reindirizzato alla pagina di indice.

### <a name="the-create-no-locrazor-page"></a>Pagina Crea Razor

Esaminare il file di paging *pages/Movies/create. cshtml* Razor :

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml)]

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Per visualizzare il tag `<form method="post">`, Visual Studio usa un tipo di carattere in grassetto distintivo specifico per gli helper tag:

![Visualizzazione di VS17 della pagina Create.cshtml](page/_static/th.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Per altre informazioni sugli helper tag, ad esempio `<form method="post">`, vedere [Helper tag in ASP.NET Core](xref:mvc/views/tag-helpers/intro).

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/visual-studio-mac)

Per visualizzare il tag `<form method="post">`, Visual Studio per Mac usa un tipo di carattere in grassetto distintivo specifico per gli helper tag.

---

L'elemento `<form method="post">` è un [helper tag del modulo](xref:mvc/views/working-with-forms#the-form-tag-helper). L'helper tag del modulo include automaticamente un [token antifalsificazione](xref:security/anti-request-forgery).

Il motore di ponteggi crea Razor markup per ogni campo nel modello (ad eccezione dell'ID) simile al seguente:

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml?range=15-20)]

Gli [Helper tag di convalida](xref:mvc/views/working-with-forms#the-validation-tag-helpers) ( `<div asp-validation-summary` e `<span asp-validation-for` ) visualizzano gli errori di convalida. Il tema della convalida viene trattato in modo più dettagliato successivamente in questa serie.

L' [Helper tag di etichetta](xref:mvc/views/working-with-forms#the-label-tag-helper) ( `<label asp-for="Movie.Title" class="control-label"></label>` ) genera la didascalia dell'etichetta e l' `for` attributo per la `Title` Proprietà.

L' [Helper tag di input](xref:mvc/views/working-with-forms) ( `<input asp-for="Movie.Title" class="form-control">` ) USA gli attributi [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) e produce gli attributi HTML necessari per la convalida jQuery sul lato client.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Versione YouTube dell'esercitazione](https://youtu.be/zxgKjPYnOMM)

> [!div class="step-by-step"]
> [Precedente: aggiunta di un modello](xref:tutorials/razor-pages/model) 
>  Passaggio [successivo: database](xref:tutorials/razor-pages/sql)

::: moniker-end
