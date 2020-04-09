---
title: Introduzione a Razor Pages in ASP.NET Core
author: Rick-Anderson
description: Informazioni su come Razor Pages in ASP.NET Core semplifica e rende più produttiva la scrittura di codice incentrata sulle pagine rispetto a MVC.
monikerRange: '>= aspnetcore-2.0'
ms.author: riande
ms.date: 02/12/2020
uid: razor-pages/index
ms.openlocfilehash: 42ffb0d4d2e49663dd53ffeee5d9fa2a931ee5b7
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "78667580"
---
# <a name="introduction-to-razor-pages-in-aspnet-core"></a>Introduzione a Razor Pages in ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Di [Rick Anderson](https://twitter.com/RickAndMSFT) e [Ryan Nowak](https://github.com/rynowak)

Le pagine Razor possono semplificare e più produttività la codifica di scenari incentrati sulle pagine rispetto all'uso di controller e visualizzazioni.

Se si sta cercando un'esercitazione in cui si usa l'approccio Model-View-Controller, vedere [Introduzione ad ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc).

Questo documento offre un'introduzione a Razor Pages. Non è un'esercitazione dettagliata. Se alcune sezioni risultano troppo avanzate, vedere [Introduzione a Razor Pages](xref:tutorials/razor-pages/razor-pages-start). Per una panoramica di ASP.NET Core, vedere [Introduzione a ASP.NET Core](xref:index).

## <a name="prerequisites"></a>Prerequisiti

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

<a name="rpvs17"></a>

## <a name="create-a-razor-pages-project"></a>Creare un progetto Razor Pages

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Per istruzioni dettagliate su come creare un progetto Razor Pages, vedere [Introduzione a Razor Pages](xref:tutorials/razor-pages/razor-pages-start).

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Eseguire `dotnet new webapp` dalla riga di comando.

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/visual-studio-mac)

Eseguire `dotnet new webapp` dalla riga di comando.

Aprire il file *CSPROJ* generato da Visual Studio per Mac.

---

## <a name="razor-pages"></a>Razor Pages

La funzionalità Razor Pages è abilitata in *Startup.cs*:

[!code-cs[](index/3.0sample/RazorPagesIntro/Startup.cs?name=snippet_Startup&highlight=12,36)]

Si consideri una pagina di base: <a name="OnGet"></a>

[!code-cshtml[](index/3.0sample/RazorPagesIntro/Pages/Index.cshtml?highlight=1)]

Il codice precedente è molto simile a un [file di visualizzazione Razor](xref:tutorials/first-mvc-app/adding-view) usato in un'app ASP.NET Core con controller e visualizzazioni. Ciò che la [`@page`](xref:mvc/views/razor#page) rende diversa è la direttiva. `@page` trasforma il file in un'azione MVC, ovvero gestisce le richieste direttamente, senza passare attraverso un controller. `@page` deve essere la prima direttiva Razor in una pagina. `@page`influisce sul comportamento di altri costrutti [Razor.](xref:mvc/views/razor) I nomi dei file Razor Pages hanno un suffisso *con estensione cshtml.*

Nei due file seguenti viene visualizzata una pagina simile che usa una classe `PageModel`. Il file *Pages/Index2.cshtml*:

[!code-cshtml[](index/3.0sample/RazorPagesIntro/Pages/Index2.cshtml)]

Il modello di pagina *Pages/Index2.cshtml.cs*:

[!code-cs[](index/3.0sample/RazorPagesIntro/Pages/Index2.cshtml.cs)]

Per convenzione, il file di classe `PageModel` ha lo stesso nome del file della pagina Razor con l'aggiunta di *.cs*. Ad esempio, la pagina Razor precedente è *Pages/Index2.cshtml*. Il file che contiene la classe `PageModel` è denominato *Pages/Index2.cshtml.cs*.

Le associazioni dei percorsi URL alle pagine sono determinate dalla posizione della pagina nel file system. Nella tabella seguente sono riportati alcuni percorsi di pagina Razor con gli URL corrispondenti:

| Percorso e nome file               | URL corrispondente |
| ----------------- | ------------ |
| */Pages/Index.cshtml* | `/` o `/Index` |
| */Pages/Contact.cshtml* | `/Contact` |
| */Pages/Store/Contact.cshtml* | `/Store/Contact` |
| */Pages/Store/Index.cshtml* | `/Store` o `/Store/Index` |

Note:

* Il runtime cerca i file delle pagine Razor nella cartella *Pages* per impostazione predefinita.
* `Index` è la pagina predefinita quando un URL non include una pagina.

## <a name="write-a-basic-form"></a>Scrivere un form di base

Razor Pages semplifica l'implementazione dei modelli normalmente usati con i Web browser durante la creazione di un'app. L'[associazione di modelli](xref:mvc/models/model-binding), gli [helper tag](xref:mvc/views/tag-helpers/intro) e gli helper HTML *funzionano* tutti con le proprietà definite in una classe di pagina Razor. Si consideri una pagina che implementa un form "contact us" di base per il modello `Contact`:

Per gli esempi riportati in questo documento, `DbContext` viene inizializzato nel file [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/3.0sample/RazorPagesContacts/Startup.cs#L23-L24).

[!code-cs[](index/3.0sample/RazorPagesContacts/Startup.cs?name=snippet)]

Il modello di dati:

[!code-cs[](index/3.0sample/RazorPagesContacts/Models/Customer.cs)]

Il contesto del database:

[!code-cs[](index/3.0sample/RazorPagesContacts/Data/CustomerDbContext.cs)]

Il file di visualizzazione *Pages/Create.cshtml*:

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml)]

Il modello di pagina *Pages/Create.cshtml.cs*:

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_ALL)]

Per convenzione, la classe `PageModel` è denominata `<PageName>Model` e si trova nello stesso spazio dei nomi della pagina.

La classe `PageModel` consente la separazione della logica di una pagina dalla relativa presentazione. Definisce i gestori di pagina per le richieste inviate alla pagina e i dati usati per il rendering della pagina. Questa separazione consente di:

* Gestione delle dipendenze di pagina tramite inserimento delle [dipendenze](xref:fundamentals/dependency-injection).
* [Unit test](xref:test/razor-pages-tests)

La pagina contiene un oggetto , ovvero un `OnPostAsync` *metodo gestore* che viene eseguito per le richieste `POST`, quando un utente invia il form. È possibile aggiungere metodi di gestione per qualsiasi verbo HTTP. I gestori più comuni sono:

* `OnGet` per inizializzare lo stato necessario per la pagina. Nel codice precedente, `OnGet` il metodo visualizza il *CreateModel.cshtml* Razor pagina.
* `OnPost` per gestire gli invii di form.

Il suffisso `Async` nel nome è facoltativo, ma viene spesso usato per convenzione per le funzioni asincrone. Il codice precedente è tipico di Razor Pages.

Se hai familiarità con ASP.NET le app usando controller e visualizzazioni:

* Il `OnPostAsync` codice nell'esempio precedente è simile al codice del controller tipico.
* La maggior parte delle primitive MVC come [l'associazione di modelli](xref:mvc/models/model-binding), la [convalida](xref:mvc/models/validation)e i risultati dell'azione funzionano allo stesso modo con i controller e le pagine Razor. 

Il metodo `OnPostAsync` precedente:

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_OnPostAsync)]

Il flusso di base di `OnPostAsync`:

Verificare se sono presenti errori di convalida.

* Se non sono presenti errori, salvare i dati e reindirizzare.
* Se sono presenti errori, visualizzare di nuovo la pagina con i messaggi di convalida. In molti casi gli errori di convalida vengono rilevati nel client e mai inviati al server.

Il file di visualizzazione *Pages/Create.cshtml*:

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml)]

Il codice HTML di cui è stato eseguito il rendering da *Pages/Create.cshtml*:

[!code-html[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create4.html)]

Nel codice precedente, la registrazione del modulo:

* Con dati validi:

  * Il `OnPostAsync` metodo del <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> gestore chiama il metodo di supporto. `RedirectToPage` restituisce un'istanza di <xref:Microsoft.AspNetCore.Mvc.RedirectToPageResult>. `RedirectToPage`:

    * È un risultato dell'azione.
    * È simile `RedirectToAction` `RedirectToRoute` a o (utilizzato nei controller e nelle viste).
    * È personalizzato per le pagine. Nell'esempio precedente viene reindirizzato alla pagina di indice radice (`/Index`). `RedirectToPage`è descritto in dettaglio nella sezione [Generazione URL per Pages.](#url_gen)

* Con gli errori di convalida passati al server:

  * Il `OnPostAsync` metodo del <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageBase.Page*> gestore chiama il metodo di supporto. `Page` restituisce un'istanza di <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult>. La restituzione di `Page` è simile al modo in cui le azioni nel controller restituiscono `View`. `PageResult`è il tipo restituito predefinito per un metodo del gestore. Un metodo gestore che restituisce `void` esegue il rendering della pagina.
  * Nell'esempio precedente, la registrazione del form senza valore comporta la restituzione di [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) su false. In questo esempio non vengono visualizzati errori di convalida nel client. L'errore di convalida viene trattato più avanti in questo documento.

  [!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=3-6)]

* Con gli errori di convalida rilevati dalla convalida lato client:With validation errors detected by client side validation:

  * I dati **non** vengono inviati al server.
  * La convalida lato client viene illustrata più avanti in questo documento.

La `Customer` proprietà [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) utilizza l'attributo per acconsentire esplicitamente all'associazione del modello:The property uses attribute to opt-in to model binding:

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_PageModel&highlight=15-16)]

`[BindProperty]`**non** deve essere utilizzato su modelli contenenti proprietà che non devono essere modificate dal client. Per ulteriori informazioni, vedere [Overposting](xref:data/ef-rp/crud#overposting).

Per impostazione predefinita, Razor Pages associa le proprietà solo a verbi non `GET`. L'associazione alle proprietà elimina la necessità di scrivere codice per convertire i dati HTTP nel tipo di modello. Riduce il codice usando la stessa proprietà per il eseguire il rendering dei campi del form (`<input asp-for="Customer.Name">`) e accettare l'input.

[!INCLUDE[](~/includes/bind-get.md)]

Revisione del file di visualizzazione *Pages/Create.cshtml:*

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml?highlight=3,9)]

* Nel codice precedente, [l'helper](xref:mvc/views/working-with-forms#the-input-tag-helper) `<input asp-for="Customer.Name" />` del tag `<input>` di `Customer.Name` input associa l'elemento HTML all'espressione del modello.
* [`@addTagHelper`](xref:mvc/views/tag-helpers/intro#addtaghelper-makes-tag-helpers-available)rende disponibili gli helper tag.

### <a name="the-home-page"></a>La home page

*Index.cshtml* è la home page:

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml)]

La classe `PageModel` associata (*Index.cshtml.cs*):

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml.cs?name=snippet)]

Il file *Index.cshtml* contiene il markup seguente:

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml?range=21)]

`<a /a>` [L'helper tag di ancoraggio](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) ha utilizzato l'attributo `asp-route-{value}` per generare un collegamento alla pagina Modifica. Il collegamento contiene i dati della route con l'ID contatto. Ad esempio: `https://localhost:5001/Edit/1`. Gli [helper tag](xref:mvc/views/tag-helpers/intro) consentono al codice lato server di partecipare alla creazione e al rendering di elementi HTML nei file Razor.

Il file Index.cshtml contiene il markup per creare un pulsante di eliminazione per ogni contatto del cliente:The *Index.cshtml* file contains markup to create a delete button for each customer contact:

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml?range=22-23)]

Il codice HTML di cui è stato eseguito il rendering:

```html
<button type="submit" formaction="/Customers?id=1&amp;handler=delete">delete</button>
```

Quando viene eseguito il rendering del pulsante di eliminazione in HTML, [l'azione modulo](https://developer.mozilla.org/docs/Web/HTML/Element/button#attr-formaction) include parametri per:

* ID contatto del cliente, `asp-route-id` specificato dall'attributo.
* Oggetto `handler`specificato dall'attributo , specificato dall'attributo `asp-page-handler` .

Quando il pulsante è selezionato, viene inviata una richiesta `POST` di modulo al server. Per convenzione, il nome del metodo del gestore viene selezionato in base al valore del parametro `handler` in base allo schema `OnPost[handler]Async`.

Poiché in questo esempio l'`handler` è `delete`, il metodo `OnPostDeleteAsync` viene usato per elaborare la richiesta `POST`. Se `asp-page-handler` viene impostato su un valore diverso, ad esempio `remove`, viene selezionato un metodo gestore con il nome `OnPostRemoveAsync`.

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml.cs?name=snippet2)]

Il metodo `OnPostDeleteAsync`:

* Ottiene `id` l'oggetto dalla stringa di query.
* Interroga il database in merito al contatto del cliente con `FindAsync`.
* Se il contatto del cliente viene trovato, viene rimosso e il database viene aggiornato.
* Chiama <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> per reindirizzare alla pagina di indice radice (`/Index`).

### <a name="the-editcshtml-file"></a>Il file Edit.cshtml

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Edit.cshtml?highlight=1)]

La prima riga contiene la direttiva `@page "{id:int}"`. Il vincolo di routing `"{id:int}"` indica alla pagina di accettare le richieste inviate alla pagina che contengono i dati della route `int`. Se una richiesta inviata alla pagina non contiene dati della route che possono essere convertiti in un oggetto `int`, il runtime restituisce un errore HTTP 404 (non trovato). Per rendere l'ID facoltativo, aggiungere `?` al vincolo di route:

 ```cshtml
@page "{id:int?}"
```

Il *file Edit.cshtml.cs:*

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Edit.cshtml.cs?name=snippet)]

## <a name="validation"></a>Convalida

Regole di convalida:

* Vengono specificati in modo dichiarativo nella classe del modello.
* Vengono applicati ovunque nell'app.

Lo <xref:System.ComponentModel.DataAnnotations> spazio dei nomi fornisce un set di attributi di convalida incorporati che vengono applicati in modo dichiarativo a una classe o una proprietà. DataAnnotations contiene anche [`[DataType]`](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attributi di formattazione come che aiutano con la formattazione e non forniscono alcuna convalida.

Si `Customer` consideri il modello:

[!code-cs[](index/3.0sample/RazorPagesContacts/Models/Customer.cs)]

Utilizzando il file di visualizzazione *Create.cshtml* seguente:

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create3.cshtml?highlight=3,8-9,15-99)]

Il codice precedente:

* Include script di convalida jQuery e jQuery.
* Utilizza `<div />` gli `<span />` [helper](xref:mvc/views/tag-helpers/intro) e per abilitare:

  * Convalida lato client.
  * Rendering degli errori di convalida.

* Viene generato il codice HTML seguente:

  [!code-html[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create5.html)]

La registrazione del modulo di creazione senza un valore di nome visualizza il messaggio di errore "Il campo Nome è obbligatorio". nel modulo. Se JavaScript è abilitato sul client, il browser visualizza l'errore senza inviare al server.

L'attributo `[StringLength(10)]` viene generato `data-val-length-max="10"` sul codice HTML di cui è stato eseguito il rendering. `data-val-length-max`impedisce ai browser di immettere una lunghezza superiore a quella massima specificata. Se uno strumento come [Fiddler](https://www.telerik.com/fiddler) viene utilizzato per modificare e riprodurre il post:

* Con il nome più lungo di 10.
* Il messaggio di errore "Il nome del campo deve essere una stringa con una lunghezza massima di 10". un errore imprevisto".

Si consideri il modello seguente:Consider the following `Movie` model:

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDA.cs?name=snippet1)]

Gli attributi di convalida specificano il comportamento da applicare alle proprietà del modello a cui vengono applicati:The validation attributes specify behavior to enforce on the model properties they're applied to:

* Gli `Required` `MinimumLength` attributi e indicano che una proprietà deve avere un valore, ma nulla impedisce all'utente di immettere spazi vuoti per soddisfare questa convalida.
* L'attributo `RegularExpression` viene usato per limitare i caratteri che possono essere inseriti. Nel codice precedente "Genre":

  * Deve includere solo lettere.
  * La prima lettera deve essere maiuscola. Gli spazi, i numeri e i caratteri speciali non sono consentiti.

* `RegularExpression` "Rating":

  * Richiede che il primo carattere sia una lettera maiuscola.
  * Consente caratteri speciali e numeri negli spazi successivi. "PG-13" è valido per una classificazione, ma non per "Genre".

* L'attributo `Range` vincola un valore all'interno di un intervallo specificato.
* L'attributo `StringLength` imposta la lunghezza massima di una proprietà stringa e, facoltativamente, la lunghezza minima.
* I tipi di valore, ad esempio `decimal`, `int`, `float` e `DateTime`, sono intrinsecamente necessari e non richiedono l'attributo `[Required]`.

La pagina Crea `Movie` per il modello mostra gli errori con valori non validi:

![Il modulo di vista del film con più errori di convalida del lato client jQuery](~/tutorials/razor-pages/validation/_static/val.png)

Per altre informazioni, vedere:

* [Aggiungere la convalida all'app Filmato](xref:tutorials/razor-pages/validation)
* [Convalida del modello in ASP.NET Core](xref:mvc/models/validation).

## <a name="handle-head-requests-with-an-onget-handler-fallback"></a>Gestire le richieste HEAD con un fallback del gestore OnGet

`HEAD`le richieste consentono di recuperare le intestazioni per una risorsa specifica. A differenza delle richieste `GET`, le richieste `HEAD` non restituiscono un corpo della risposta.

In genere, per le richieste `HEAD` viene creato e chiamato un gestore `OnHead`:

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Privacy.cshtml.cs?name=snippet)]

Razor Pages esegue il `OnGet` fallback `OnHead` alla chiamata al gestore se non è definito alcun gestore.

<a name="xsrf"></a>

## <a name="xsrfcsrf-and-razor-pages"></a>XSRF/CSRF e Razor Pages

Le Pagine Razor sono protette dalla [convalida Antiforgery.](xref:security/anti-request-forgery) [Il FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) inserisce token antifalsificazione in elementi del modulo HTML.

<a name="layout"></a>

## <a name="using-layouts-partials-templates-and-tag-helpers-with-razor-pages"></a>Uso di layout, righe parzialmente eseguite, modelli e helper tag con Razor Pages

Le pagine funzionano con tutte le funzionalità del motore di visualizzazione Razor. Layout, partial, modelli, helper tag, *_ViewStart.cshtml*e *_ViewImports.cshtml* funzionano nello stesso modo in cui funzionano per le viste Razor convenzionali.

La pagina verrà ora riorganizzata in modo da usufruire di alcune di queste funzionalità.

Aggiungere una [pagina di layout](xref:mvc/views/layout) a *Pages/Shared/_Layout.cshtml*:

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Shared/_Layout2.cshtml?hightlight=12)]

Il [layout](xref:mvc/views/layout):

* Controlla il layout di ogni pagina, a meno che la pagina non accetti il layout.
* Importa le strutture HTML, ad esempio JavaScript e i fogli di stile.
* Viene eseguito il rendering del `@RenderBody()` contenuto della pagina Razor dove viene chiamato.

Per ulteriori informazioni, consultate [pagina di layout.](xref:mvc/views/layout)

La proprietà [Layout](xref:mvc/views/layout#specifying-a-layout) proprietà viene impostata in *Pages/_ViewStart.cshtml*:

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewStart.cshtml)]

Il layout è nella cartella *Pages/Shared*. Le pagine cercano altre visualizzazioni (layout, modelli, righe parzialmente eseguite) in modo gerarchico, partendo dalla stessa cartella della pagina corrente. Un layout nella cartella *Pages/Shared* può essere usato da qualsiasi pagina Razor della cartella *Pages*.

Il file di layout dovrebbe andare nella cartella *Pages/Shared*.

Si consiglia di **non** inserire il file di layout nella cartella *Views/Shared*. *Views/Shared* è un modello destinato alle visualizzazioni MVC. Le pagine Razor devono basarsi sulla gerarchia di cartelle, non su convenzioni di percorso.

La ricerca delle visualizzazioni da una pagina Razor include la cartella *Pages*. I layout, i modelli e i partial utilizzati con i controller MVC e le visualizzazioni Razor convenzionali *funzionano solo.*

Aggiungere un file *Pages/_ViewImports.cshtml*:

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml)]

`@namespace` viene spiegato in seguito nell'esercitazione. La direttiva `@addTagHelper` inserisce gli [helper tag predefiniti](xref:mvc/views/tag-helpers/builtin-th/Index) in tutte le pagine presenti nella cartella *Pages*.

<a name="namespace"></a>

La `@namespace` direttiva impostata in una pagina:

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Customers/Namespace2.cshtml?highlight=2)]

La `@namespace` direttiva imposta lo spazio dei nomi per la pagina. La direttiva `@model` non deve necessariamente includere lo spazio dei nomi.

Se la direttiva `@namespace` è contenuta in *_ViewImports.cshtml*, lo spazio dei nomi specificato indica il prefisso per lo spazio dei nomi generato nella pagina che importa la direttiva `@namespace`. Il resto dello spazio dei nomi generato (la parte del suffisso) è il percorso relativo separato dal punto tra la cartella contenente *_Viewimports.cshtml* e la cartella contenente la pagina.

Ad esempio, la classe `PageModel`*Pages/Customers/Edit.cshtml.cs* imposta in modo esplicito lo spazio dei nomi:

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/Edit.cshtml.cs?name=snippet_namespace)]

Il file *Pages/_ViewImports.cshtml* file imposta il seguente spazio dei nomi:

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml?highlight=1)]

Lo spazio dei nomi generato per la pagina Razor *Pages/Customers/Edit.cshtml* corrisponde alla classe `PageModel`.

`@namespace` *funziona anche con le normali visualizzazioni Razor.*

Si consideri il file di visualizzazione *Pages/Create.cshtml:Consider the Pages/Create.cshtml* view file:

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create3.cshtml?highlight=2-3)]

Il file di visualizzazione *Pages/Create.cshtml* aggiornato con *_ViewImports.cshtml* e il file di layout precedente:

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create4.cshtml?highlight=2)]

Nel codice precedente, *il _ViewImports.cshtml* importato lo spazio dei nomi e tag helper. Il file di layout importava i file JavaScript.

Il [progetto iniziale per Razor Pages](#rpvs17) contiene il file *Pages/_ValidationScriptsPartial.cshtml*, che esegue la convalida sul lato client.

Per altre informazioni sulle visualizzazioni parziali, vedere <xref:mvc/views/partial>.

<a name="url_gen"></a>

## <a name="url-generation-for-pages"></a>Generazione di URL per le pagine

La pagina `Create`, riportata in precedenza, usa `RedirectToPage`:

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_PageModel&highlight=28)]

L'applicazione ha la struttura di file o cartella seguente:

* */Pagine*

  * *Index.cshtml*
  * *Privacy.cshtml*
  * */Clienti*

    * *Create.cshtml*
    * *Edit.cshtml*
    * *Index.cshtml*

Le pagine *Pages/Customers/Create.cshtml* e *Pages/Customers/Edit.cshtml* reindirizzano a *Pages/Customers/Index.cshtml* dopo l'esito positivo. La `./Index` stringa è un nome di pagina relativo utilizzato per accedere alla pagina precedente. Viene utilizzato per generare URL per la pagina *Pages/Customers/Index.cshtml.* Ad esempio:

* `Url.Page("./Index", ...)`
* `<a asp-page="./Index">Customers Index Page</a>`
* `RedirectToPage("./Index")`

Il nome `/Index` assoluto della pagina viene utilizzato per generare URL nella pagina *Pages/Index.cshtml.* Ad esempio:

* `Url.Page("/Index", ...)`
* `<a asp-page="/Index">Home Index Page</a>`
* `RedirectToPage("/Index")`

Il nome della pagina è il percorso alla pagina dalla cartella radice */Pages*, inclusa una barra iniziale `/`, ad esempio `/Index`. Gli esempi di generazione di URL precedenti offrono opzioni avanzate e funzionalità rispetto alla codifica avanzata di un URL. La generazione di URL usa il [routing](xref:mvc/controllers/routing) ed è in grado di generare e codificare i parametri in base al modo in cui la route è definita nel percorso di destinazione.

La generazione di URL per le pagine supporta i nomi relativi. Nella tabella seguente viene illustrata `RedirectToPage` la pagina di indice selezionata utilizzando parametri diversi in *Pages/Customers/Create.cshtml*.

| RedirectToPage(x)| Pagina |
| ----------------- | ------------ |
| RedirectToPage("/Index") | *Pages/Index* |
| RedirectToPage("./Index"); | *Pages/Customers/Index* |
| RedirectToPage("../Index") | *Pages/Index* |
| RedirectToPage("Index")  | *Pages/Customers/Index* |

<!-- Test via ~/razor-pages/index/3.0sample/RazorPagesContacts/Pages/Customers/Details.cshtml.cs -->

`RedirectToPage("Index")`, `RedirectToPage("./Index")`, `RedirectToPage("../Index")` e sono *nomi relativi*. Il parametro `RedirectToPage` è *combinato* con il percorso della pagina corrente per calcolare il nome della pagina di destinazione.

Il collegamento dei nomi relativi è utile quando si compilano siti con una struttura complessa. Quando i nomi relativi vengono utilizzati per il collegamento tra le pagine di una cartella:

* La ridenominazione di una cartella non interrompe i collegamenti relativi.
* I collegamenti non vengono interrotti perché non includono il nome della cartella.

Per reindirizzare la pagina a un'[Area](xref:mvc/controllers/areas) diversa, specificare l'area:

```csharp
RedirectToPage("/Index", new { area = "Services" });
```

Per altre informazioni, vedere <xref:mvc/controllers/areas> e <xref:razor-pages/razor-pages-conventions>.

## <a name="viewdata-attribute"></a>Attributo viewData

I dati possono essere <xref:Microsoft.AspNetCore.Mvc.ViewDataAttribute>passati a una pagina con . Le proprietà `[ViewData]` con l'attributo hanno <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ViewDataDictionary>i valori memorizzati e caricati dal file .

Nell'esempio seguente, `AboutModel` l'attributo `Title` applica la `[ViewData]` proprietà alla proprietà :

```csharp
public class AboutModel : PageModel
{
    [ViewData]
    public string Title { get; } = "About";

    public void OnGet()
    {
    }
}
```

Nella pagina About (Informazioni) accedere alla proprietà `Title` come proprietà del modello:

```cshtml
<h1>@Model.Title</h1>
```

Nel layout il titolo viene letto dal dizionario ViewData:

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"] - WebApplication</title>
    ...
```

## <a name="tempdata"></a>TempData

ASP.NET Core espone <xref:Microsoft.AspNetCore.Mvc.Controller.TempData>il file . Questa proprietà archivia i dati finché non viene letta. I metodi <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Keep*> e <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Peek*> possono essere usati per esaminare i dati senza eliminazione. `TempData`è utile per il reindirizzamento, quando i dati sono necessari per più di una singola richiesta.

Il codice seguente imposta il valore di `Message` usando `TempData`:

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/CreateDot.cshtml.cs?highlight=10-11,25&name=snippet_Temp)]

Il markup seguente nel file *Pages/Customers/Index.cshtml* visualizza il valore di `Message` usando `TempData`.

```cshtml
<h3>Msg: @Model.Message</h3>
```

Il modello di pagina *Pages/Customers/Index.cshtml.cs* applica l'attributo `[TempData]` alla proprietà `Message`.

```csharp
[TempData]
public string Message { get; set; }
```

Per ulteriori informazioni, vedere [TempData](xref:fundamentals/app-state#tempdata).

<a name="mhpp"></a>

## <a name="multiple-handlers-per-page"></a>Più gestori per pagina

La pagina seguente genera markup per due gestori usando l'helper tag `asp-page-handler`:

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?highlight=12-13)]

Il form nell'esempio precedente contiene due pulsanti di invio, ognuno dei quali usa `FormActionTagHelper` per l'invio a un URL diverso. L'attributo `asp-page-handler` è correlato a `asp-page`. `asp-page-handler` genera gli URL che indirizzano a ognuno dei metodi gestore definiti da una pagina. `asp-page` non è specificato poiché l'esempio è collegato alla pagina corrente.

Il modello di pagina:

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml.cs?highlight=20,32)]

Il codice precedente usa *metodi gestore denominati*. I metodi gestore denominati vengono creati usando il testo che nel nome segue `On<HTTP Verb>` e precede `Async` (se presente). Nell'esempio precedente i metodi della pagina sono OnPost**JoinList**Async e OnPost**JoinListUC**Async. Rimuovendo *OnPost* e *Async*, i nomi dei gestori sono `JoinList` e `JoinListUC`.

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?range=12-13)]

Usando il codice precedente, il percorso URL che indirizza a `OnPostJoinListAsync` è `https://localhost:5001/Customers/CreateFATH?handler=JoinList`. Il percorso URL che indirizza a `OnPostJoinListUCAsync` è `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`.

## <a name="custom-routes"></a>Route personalizzate

Usare la direttiva `@page` per:

* Specificare una route personalizzata a una pagina. Ad esempio, è possibile impostare la route alla pagina About (Informazioni) su `/Some/Other/Path` con `@page "/Some/Other/Path"`.
* Aggiungere segmenti alla route predefinita di una pagina. Ad esempio, è possibile aggiungere un segmento "item" alla route predefinita di una pagina con `@page "item"`.
* Aggiungere parametri alla route predefinita di una pagina. Ad esempio, un parametro ID, `id`, può essere necessario per una pagina con `@page "{id}"`.

Un percorso relativo alla directory radice designato da una tilde (`~`) all'inizio del percorso è supportato. Ad esempio, `@page "~/Some/Other/Path"` equivale a `@page "/Some/Other/Path"`.

Se la stringa `?handler=JoinList` di query non è piaciuta nell'URL, modificare la route in modo da inserire il nome del gestore nella parte del percorso dell'URL. Il percorso può essere personalizzato aggiungendo un modello di route `@page` racchiuso tra virgolette doppie dopo la direttiva.

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateRoute.cshtml?highlight=1)]

Usando il codice precedente, il percorso URL che indirizza a `OnPostJoinListAsync` è `https://localhost:5001/Customers/CreateFATH/JoinList`. Il percorso URL che indirizza a `OnPostJoinListUCAsync` è `https://localhost:5001/Customers/CreateFATH/JoinListUC`.

`?` che segue `handler` indica che il parametro di route è facoltativo.

## <a name="advanced-configuration-and-settings"></a>Configurazione e impostazioni avanzate

La configurazione e le impostazioni nelle sezioni seguenti non sono necessarie per la maggior parte delle app.

Per configurare le opzioni <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*>avanzate, utilizzare il metodo di estensione :

[!code-cs[](index/3.0sample/RazorPagesContacts/StartupRPoptions.cs?name=snippet)]

Utilizzare <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions> il per impostare la directory radice per le pagine o aggiungere convenzioni del modello di applicazione per le pagine. Per ulteriori informazioni sulle convenzioni, vedere Convenzioni di [autorizzazione di Razor Pages](xref:security/authorization/razor-pages-authorization).

Per precompilare le visualizzazioni, vedere [Compilazione visualizzazione Razor](xref:mvc/views/view-compilation).

### <a name="specify-that-razor-pages-are-at-the-content-root"></a>Specificare che Razor Pages si trova nella radice del contenuto

Per impostazione predefinita, la directory radice di Razor Pages è */Pages*. Aggiungi <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.WithRazorPagesAtContentRoot*> per specificare che le pagine Razor si trovano nella radice del [contenuto](xref:fundamentals/index#content-root) (<xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath>) dell'app:

[!code-cs[](index/3.0sample/RazorPagesContacts/StartupWithRazorPagesAtContentRoot.cs?name=snippet)]

### <a name="specify-that-razor-pages-are-at-a-custom-root-directory"></a>Specificare che Razor Pages è in una directory radice personalizzata

Aggiungi <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcCoreBuilderExtensions.WithRazorPagesRoot*> per specificare che le pagine Razor si trovano in una directory radice personalizzata nell'app (fornire un percorso relativo):

[!code-cs[](index/3.0sample/RazorPagesContacts/StartupWithRazorPagesRoot.cs?name=snippet)]

## <a name="additional-resources"></a>Risorse aggiuntive

* Vedere [Introduzione alle pagine Razor](xref:tutorials/razor-pages/razor-pages-start), che si basa su questa introduzione
* [Scaricare o visualizzare codice di esempioDownload or view sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/3.0sample)
* <xref:index>
* <xref:mvc/views/razor>
* <xref:mvc/controllers/areas>
* <xref:tutorials/razor-pages/razor-pages-start>
* <xref:security/authorization/razor-pages-authorization>
* <xref:razor-pages/razor-pages-conventions>
* <xref:test/razor-pages-tests>
* <xref:mvc/views/partial>
* <xref:blazor/integrate-components>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Di [Rick Anderson](https://twitter.com/RickAndMSFT) e [Ryan Nowak](https://github.com/rynowak)

Razor Pages è una nuova funzionalità di ASP.NET Core MVC che semplifica e rende più produttiva la scrittura di codice incentrata sulle pagine.

Se si sta cercando un'esercitazione in cui si usa l'approccio Model-View-Controller, vedere [Introduzione ad ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc).

Questo documento offre un'introduzione a Razor Pages. Non è un'esercitazione dettagliata. Se alcune sezioni risultano troppo avanzate, vedere [Introduzione a Razor Pages](xref:tutorials/razor-pages/razor-pages-start). Per una panoramica di ASP.NET Core, vedere [Introduzione a ASP.NET Core](xref:index).

## <a name="prerequisites"></a>Prerequisiti

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

<a name="rpvs17"></a>

## <a name="create-a-razor-pages-project"></a>Creare un progetto Razor Pages

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Per istruzioni dettagliate su come creare un progetto Razor Pages, vedere [Introduzione a Razor Pages](xref:tutorials/razor-pages/razor-pages-start).

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/visual-studio-mac)

Eseguire `dotnet new webapp` dalla riga di comando.

Aprire il file *CSPROJ* generato da Visual Studio per Mac.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Eseguire `dotnet new webapp` dalla riga di comando.

---

## <a name="razor-pages"></a>Razor Pages

La funzionalità Razor Pages è abilitata in *Startup.cs*:

[!code-cs[](index/sample/RazorPagesIntro/Startup.cs?name=snippet_Startup)]

Si consideri una pagina di base: <a name="OnGet"></a>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Index.cshtml)]

Il codice precedente è molto simile a un [file di visualizzazione Razor](xref:tutorials/first-mvc-app/adding-view) usato in un'app ASP.NET Core con controller e visualizzazioni. Ciò che lo differenzia è la direttiva `@page`. `@page` trasforma il file in un'azione MVC, ovvero gestisce le richieste direttamente, senza passare attraverso un controller. `@page` deve essere la prima direttiva Razor in una pagina. `@page` influisce sul comportamento di altri costrutti Razor.

Nei due file seguenti viene visualizzata una pagina simile che usa una classe `PageModel`. Il file *Pages/Index2.cshtml*:

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Index2.cshtml)]

Il modello di pagina *Pages/Index2.cshtml.cs*:

[!code-cs[](index/sample/RazorPagesIntro/Pages/Index2.cshtml.cs)]

Per convenzione, il file di classe `PageModel` ha lo stesso nome del file della pagina Razor con l'aggiunta di *.cs*. Ad esempio, la pagina Razor precedente è *Pages/Index2.cshtml*. Il file che contiene la classe `PageModel` è denominato *Pages/Index2.cshtml.cs*.

Le associazioni dei percorsi URL alle pagine sono determinate dalla posizione della pagina nel file system. Nella tabella seguente sono riportati alcuni percorsi di pagina Razor con gli URL corrispondenti:

| Percorso e nome file               | URL corrispondente |
| ----------------- | ------------ |
| */Pages/Index.cshtml* | `/` o `/Index` |
| */Pages/Contact.cshtml* | `/Contact` |
| */Pages/Store/Contact.cshtml* | `/Store/Contact` |
| */Pages/Store/Index.cshtml* | `/Store` o `/Store/Index` |

Note:

* Il runtime cerca i file delle pagine Razor nella cartella *Pages* per impostazione predefinita.
* `Index` è la pagina predefinita quando un URL non include una pagina.

## <a name="write-a-basic-form"></a>Scrivere un form di base

Razor Pages semplifica l'implementazione dei modelli normalmente usati con i Web browser durante la creazione di un'app. L'[associazione di modelli](xref:mvc/models/model-binding), gli [helper tag](xref:mvc/views/tag-helpers/intro) e gli helper HTML *funzionano* tutti con le proprietà definite in una classe di pagina Razor. Si consideri una pagina che implementa un form "contact us" di base per il modello `Contact`:

Per gli esempi riportati in questo documento, `DbContext` viene inizializzato nel file [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/sample/RazorPagesContacts/Startup.cs#L15-L16).

[!code-cs[](index/sample/RazorPagesContacts/Startup.cs?highlight=15-16)]

Il modello di dati:

[!code-cs[](index/sample/RazorPagesContacts/Data/Customer.cs)]

Il contesto del database:

[!code-cs[](index/sample/RazorPagesContacts/Data/AppDbContext.cs)]

Il file di visualizzazione *Pages/Create.cshtml*:

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Create.cshtml)]

Il modello di pagina *Pages/Create.cshtml.cs*:

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_ALL)]

Per convenzione, la classe `PageModel` è denominata `<PageName>Model` e si trova nello stesso spazio dei nomi della pagina.

La classe `PageModel` consente la separazione della logica di una pagina dalla relativa presentazione. Definisce i gestori di pagina per le richieste inviate alla pagina e i dati usati per il rendering della pagina. Questa separazione consente di:

* Gestione delle dipendenze di pagina tramite inserimento delle [dipendenze](xref:fundamentals/dependency-injection).
* [Unit test](xref:test/razor-pages-tests) delle pagine.

La pagina contiene un oggetto , ovvero un `OnPostAsync` *metodo gestore* che viene eseguito per le richieste `POST`, quando un utente invia il form. È possibile aggiungere metodi gestore per qualsiasi verbo HTTP. I gestori più comuni sono:

* `OnGet` per inizializzare lo stato necessario per la pagina. Esempio di [OnGet](#OnGet).
* `OnPost` per gestire gli invii di form.

Il suffisso `Async` nel nome è facoltativo, ma viene spesso usato per convenzione per le funzioni asincrone. Il codice precedente è tipico di Razor Pages.

Se hai familiarità con ASP.NET le app usando controller e visualizzazioni:

* Il `OnPostAsync` codice nell'esempio precedente è simile al codice del controller tipico.
* La maggior parte delle primitive MVC come [l'associazione di modelli,](xref:mvc/models/model-binding)la [convalida](xref:mvc/models/validation), la [convalida](xref:mvc/models/validation)e i risultati dell'azione sono condivisi.

Il metodo `OnPostAsync` precedente:

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_OnPostAsync)]

Il flusso di base di `OnPostAsync`:

Verificare se sono presenti errori di convalida.

* Se non sono presenti errori, salvare i dati e reindirizzare.
* Se sono presenti errori, visualizzare di nuovo la pagina con i messaggi di convalida. La convalida lato client è identica alle applicazioni ASP.NET Core MVC tradizionali. In molti casi gli errori di convalida vengono rilevati nel client e mai inviati al server.

Quando i dati vengono immessi correttamente, il metodo gestore `OnPostAsync` chiama il metodo helper `RedirectToPage` per restituire un'istanza di `RedirectToPageResult`. `RedirectToPage` è un nuovo risultato dell'azione, simile a `RedirectToAction` o `RedirectToRoute`, ma personalizzato per le pagine. Nell'esempio precedente viene reindirizzato alla pagina di indice radice (`/Index`). `RedirectToPage`è descritto in dettaglio nella sezione [Generazione URL per Pages.](#url_gen)

Quando il form inviato contiene errori di convalida (che vengono passati al server), il metodo gestore `OnPostAsync` chiama il metodo helper `Page`. `Page` restituisce un'istanza di `PageResult`. La restituzione di `Page` è simile al modo in cui le azioni nel controller restituiscono `View`. `PageResult`è il tipo restituito predefinito per un metodo del gestore. Un metodo gestore che restituisce `void` esegue il rendering della pagina.

La proprietà `Customer` usa l'attributo `[BindProperty]` optare per consentire l'associazione di modelli.

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_PageModel&highlight=10-11)]

Per impostazione predefinita, Razor Pages associa le proprietà solo a verbi non `GET`. Il binding alle proprietà può ridurre la quantità di codice da scrivere. Riduce il codice usando la stessa proprietà per il eseguire il rendering dei campi del form (`<input asp-for="Customer.Name">`) e accettare l'input.

[!INCLUDE[](~/includes/bind-get.md)]

La home page (*Index.cshtml*):

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml)]

La classe `PageModel` associata (*Index.cshtml.cs*):

[!code-cs[](index/sample/RazorPagesContacts/Pages/Index.cshtml.cs)]

Il file *Index.cshtml* contiene il markup seguente per creare un collegamento di modifica per ogni contatto:

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml?range=21)]

`<a asp-page="./Edit" asp-route-id="@contact.Id">Edit</a>` [L'helper tag di ancoraggio](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) ha utilizzato l'attributo `asp-route-{value}` per generare un collegamento alla pagina Modifica. Il collegamento contiene i dati della route con l'ID contatto. Ad esempio: `https://localhost:5001/Edit/1`. Gli [helper tag](xref:mvc/views/tag-helpers/intro) consentono al codice lato server di partecipare alla creazione e al rendering di elementi HTML nei file Razor. Gli helper tag sono abilitati da `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`

Il file *Pages/Edit.cshtml*:

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Edit.cshtml?highlight=1)]

La prima riga contiene la direttiva `@page "{id:int}"`. Il vincolo di routing `"{id:int}"` indica alla pagina di accettare le richieste inviate alla pagina che contengono i dati della route `int`. Se una richiesta inviata alla pagina non contiene dati della route che possono essere convertiti in un oggetto `int`, il runtime restituisce un errore HTTP 404 (non trovato). Per rendere l'ID facoltativo, aggiungere `?` al vincolo di route:

 ```cshtml
@page "{id:int?}"
```

Il file *Pages/Edit.cshtml.cs*:

[!code-cs[](index/sample/RazorPagesContacts/Pages/Edit.cshtml.cs)]

Il file *Index.cshtml* contiene anche il markup per creare un pulsante di eliminazione per ogni contatto cliente:

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml?range=22-23)]

Quando viene eseguito il rendering del pulsante di eliminazione in HTML, il relativo `formaction` include i parametri per:

* L'ID di contatto cliente dall'attributo `asp-route-id`.
* L'`handler` specificato dall'attributo `asp-page-handler`.

Di seguito è riportato un esempio di pulsante di eliminazione di cui è stato eseguito il rendering con un ID di contatto cliente `1`:

```html
<button type="submit" formaction="/?id=1&amp;handler=delete">delete</button>
```

Quando il pulsante è selezionato, viene inviata una richiesta `POST` di modulo al server. Per convenzione, il nome del metodo del gestore viene selezionato in base al valore del parametro `handler` in base allo schema `OnPost[handler]Async`.

Poiché in questo esempio l'`handler` è `delete`, il metodo `OnPostDeleteAsync` viene usato per elaborare la richiesta `POST`. Se `asp-page-handler` viene impostato su un valore diverso, ad esempio `remove`, viene selezionato un metodo gestore con il nome `OnPostRemoveAsync`. Il codice seguente `OnPostDeleteAsync` mostra il gestore:The following code shows the handler:

[!code-cs[](index/sample/RazorPagesContacts/Pages/Index.cshtml.cs?range=26-37)]

Il metodo `OnPostDeleteAsync`:

* Accetta l'`id` dalla stringa di query. Se la direttiva della pagina `"{id:int?}"` *Index.cshtml* contenesse un vincolo di routing , `id` proviene dai dati della route. I dati `id` della route per vengono `https://localhost:5001/Customers/2`specificati nell'URI, ad esempio .
* Interroga il database in merito al contatto del cliente con `FindAsync`.
* Se viene trovato il contatto cliente, viene rimosso dall'elenco dei contatti del cliente. Il database viene aggiornato.
* Chiama `RedirectToPage` per reindirizzare alla pagina di indice radice (`/Index`).

## <a name="mark-page-properties-as-required"></a>Contrassegnare le proprietà della pagina in base alle esigenze

Le proprietà `PageModel` di un oggetto possono essere contrassegnate con l'attributo [Required:](/dotnet/api/system.componentmodel.dataannotations.requiredattribute)

[!code-cs[](index/sample/Create.cshtml.cs?highlight=3,15-16)]

Per altre informazioni, vedere [Convalida del modello](xref:mvc/models/validation).

## <a name="handle-head-requests-with-an-onget-handler-fallback"></a>Gestire le richieste HEAD con un fallback del gestore OnGet

Le richieste `HEAD` consentono di recuperare le intestazioni di una risorsa specifica. A differenza delle richieste `GET`, le richieste `HEAD` non restituiscono un corpo della risposta.

In genere, per le richieste `HEAD` viene creato e chiamato un gestore `OnHead`: 

```csharp
public void OnHead()
{
    HttpContext.Response.Headers.Add("HandledBy", "Handled by OnHead!");
}
```

In ASP.NET Core 2.1 o versioni successive se non è definito alcun gestore `OnHead`, Razor Pages esegue un fallback per chiamare il gestore `OnGet`. Questo comportamento è abilitato tramite la chiamata a [SetCompatibilityVersion](xref:mvc/compatibility-version) in `Startup.ConfigureServices`:

```csharp
services.AddMvc()
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
```

I modelli predefiniti generano la chiamata `SetCompatibilityVersion` in ASP.NET Core 2.1 e 2.2. `SetCompatibilityVersion` imposta effettivamente l'opzione di Razor Pages `AllowMappingHeadRequestsToGetHandler` su `true`.

Anziché acconsentire esplicitamente a tutti i comportamenti con `SetCompatibilityVersion`, è possibile acconsentire a comportamenti *specifici*. Il codice seguente acconsente esplicitamente al mapping delle richieste `HEAD` al gestore `OnGet`:

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        options.AllowMappingHeadRequestsToGetHandler = true;
    });
```

<a name="xsrf"></a>

## <a name="xsrfcsrf-and-razor-pages"></a>XSRF/CSRF e Razor Pages

Non è necessario scrivere codice per la [convalida antifalsificazione](xref:security/anti-request-forgery). La generazione e la convalida del token antifalsificazione sono automaticamente incluse in Razor Pages.

<a name="layout"></a>

## <a name="using-layouts-partials-templates-and-tag-helpers-with-razor-pages"></a>Uso di layout, righe parzialmente eseguite, modelli e helper tag con Razor Pages

Le pagine funzionano con tutte le funzionalità del motore di visualizzazione Razor. I layout, le righe parzialmente eseguite, i modelli, gli helper tag, *_ViewStart.cshtml* e *_ViewImports.cshtml* funzionano esattamente come nelle normali visualizzazioni Razor.

La pagina verrà ora riorganizzata in modo da usufruire di alcune di queste funzionalità.

Aggiungere una [pagina di layout](xref:mvc/views/layout) a *Pages/Shared/_Layout.cshtml*:

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_LayoutSimple.cshtml)]

Il [layout](xref:mvc/views/layout):

* Controlla il layout di ogni pagina, a meno che la pagina non accetti il layout.
* Importa le strutture HTML, ad esempio JavaScript e i fogli di stile.

Vedere l'articolo sulla [pagina Layout](xref:mvc/views/layout) per altre informazioni.

La proprietà [Layout](xref:mvc/views/layout#specifying-a-layout) proprietà viene impostata in *Pages/_ViewStart.cshtml*:

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewStart.cshtml)]

Il layout è nella cartella *Pages/Shared*. Le pagine cercano altre visualizzazioni (layout, modelli, righe parzialmente eseguite) in modo gerarchico, partendo dalla stessa cartella della pagina corrente. Un layout nella cartella *Pages/Shared* può essere usato da qualsiasi pagina Razor della cartella *Pages*.

Il file di layout dovrebbe andare nella cartella *Pages/Shared*.

Si consiglia di **non** inserire il file di layout nella cartella *Views/Shared*. *Views/Shared* è un modello destinato alle visualizzazioni MVC. Le pagine Razor devono basarsi sulla gerarchia di cartelle, non su convenzioni di percorso.

La ricerca delle visualizzazioni da una pagina Razor include la cartella *Pages*. Il layout, i modelli e le righe parzialmente eseguite in uso con i controller MVC e le visualizzazioni Razor standard *funzionano solo*.

Aggiungere un file *Pages/_ViewImports.cshtml*:

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml)]

`@namespace` viene spiegato in seguito nell'esercitazione. La direttiva `@addTagHelper` inserisce gli [helper tag predefiniti](xref:mvc/views/tag-helpers/builtin-th/Index) in tutte le pagine presenti nella cartella *Pages*.

<a name="namespace"></a>

Quando la direttiva `@namespace` viene usata in modo esplicito in una pagina:

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Customers/Namespace2.cshtml?highlight=2)]

La direttiva imposta lo spazio dei nomi per la pagina. La direttiva `@model` non deve necessariamente includere lo spazio dei nomi.

Se la direttiva `@namespace` è contenuta in *_ViewImports.cshtml*, lo spazio dei nomi specificato indica il prefisso per lo spazio dei nomi generato nella pagina che importa la direttiva `@namespace`. Il resto dello spazio dei nomi generato (la parte del suffisso) è il percorso relativo separato dal punto tra la cartella contenente *_Viewimports.cshtml* e la cartella contenente la pagina.

Ad esempio, la classe `PageModel`*Pages/Customers/Edit.cshtml.cs* imposta in modo esplicito lo spazio dei nomi:

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/Edit.cshtml.cs?name=snippet_namespace)]

Il file *Pages/_ViewImports.cshtml* file imposta il seguente spazio dei nomi:

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml?highlight=1)]

Lo spazio dei nomi generato per la pagina Razor *Pages/Customers/Edit.cshtml* corrisponde alla classe `PageModel`.

`@namespace` *funziona anche con le normali visualizzazioni Razor.*

Il file di visualizzazione *Pages/Create.cshtml* originale:

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Create.cshtml?highlight=2)]

Il file di visualizzazione *Pages/Create.cshtml* aggiornato:

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/Create.cshtml?highlight=2)]

Il [progetto iniziale per Razor Pages](#rpvs17) contiene il file *Pages/_ValidationScriptsPartial.cshtml*, che esegue la convalida sul lato client.

Per altre informazioni sulle visualizzazioni parziali, vedere <xref:mvc/views/partial>.

<a name="url_gen"></a>

## <a name="url-generation-for-pages"></a>Generazione di URL per le pagine

La pagina `Create`, riportata in precedenza, usa `RedirectToPage`:

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=10)]

L'applicazione ha la struttura di file o cartella seguente:

* */Pagine*

  * *Index.cshtml*
  * */Clienti*

    * *Create.cshtml*
    * *Edit.cshtml*
    * *Index.cshtml*

Le pagine *Pages/Customers/Create.cshtml* e *Pages/Customers/Edit.cshtml* reindirizzano a *Pages/Index.cshtml* dopo l'esecuzione. La stringa `/Index` fa parte dell'URI di accesso alla pagina precedente. La stringa `/Index` può essere usata per generare gli URI alla pagina *Pages/Index.cshtml*. Ad esempio:

* `Url.Page("/Index", ...)`
* `<a asp-page="/Index">My Index Page</a>`
* `RedirectToPage("/Index")`

Il nome della pagina è il percorso alla pagina dalla cartella radice */Pages*, inclusa una barra iniziale `/`, ad esempio `/Index`. Gli esempi di generazione di URL precedenti offrono opzioni e caratteristiche funzionali avanzate rispetto agli URL hardcoded. La generazione di URL usa il [routing](xref:mvc/controllers/routing) ed è in grado di generare e codificare i parametri in base al modo in cui la route è definita nel percorso di destinazione.

La generazione di URL per le pagine supporta i nomi relativi. La tabella seguente indica quale pagina di indice viene selezionata con diversi parametri `RedirectToPage` da *Pages/Customers/Create.cshtml*:

| RedirectToPage(x)| Pagina |
| ----------------- | ------------ |
| RedirectToPage("/Index") | *Pages/Index* |
| RedirectToPage("./Index"); | *Pages/Customers/Index* |
| RedirectToPage("../Index") | *Pages/Index* |
| RedirectToPage("Index")  | *Pages/Customers/Index* |

`RedirectToPage("Index")`, `RedirectToPage("./Index")`, `RedirectToPage("../Index")` e sono *nomi relativi*. Il parametro `RedirectToPage` è *combinato* con il percorso della pagina corrente per calcolare il nome della pagina di destinazione.  <!-- Review: Original had The provided string is combined with the page name of the current page to compute the name of the destination page.  page name, not page path -->

Il collegamento dei nomi relativi è utile quando si compilano siti con una struttura complessa. Se si usano i nomi relativi per il collegamento tra le pagine in una cartella, è possibile rinominare tale cartella. Tutti i collegamenti continuano a funzionare perché non includono il nome della cartella.

Per reindirizzare la pagina a un'[Area](xref:mvc/controllers/areas) diversa, specificare l'area:

```csharp
RedirectToPage("/Index", new { area = "Services" });
```

Per altre informazioni, vedere <xref:mvc/controllers/areas>.

## <a name="viewdata-attribute"></a>Attributo viewData

È possibile passare i dati a una pagina tramite [ViewDataAttribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute). Le proprietà nei controller o `[ViewData]` nei modelli Razor Page con l'attributo hanno i valori archiviati e caricati da [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary).

Nell'esempio riportato `AboutModel` di `Title` seguito, `[ViewData]`l'oggetto contiene una proprietà contrassegnata con . La proprietà `Title` è impostata sul titolo della pagina About (Informazioni):

```csharp
public class AboutModel : PageModel
{
    [ViewData]
    public string Title { get; } = "About";

    public void OnGet()
    {
    }
}
```

Nella pagina About (Informazioni) accedere alla proprietà `Title` come proprietà del modello:

```cshtml
<h1>@Model.Title</h1>
```

Nel layout il titolo viene letto dal dizionario ViewData:

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"] - WebApplication</title>
    ...
```

## <a name="tempdata"></a>TempData

ASP.NET Core espone la proprietà [TempData](/dotnet/api/microsoft.aspnetcore.mvc.controller.tempdata?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Controller_TempData) per un [controller](/dotnet/api/microsoft.aspnetcore.mvc.controller). Questa proprietà archivia i dati finché non viene letta. I metodi `Keep` e `Peek` possono essere usati per esaminare i dati senza eliminazione. `TempData` è utile per il reindirizzamento quando i dati sono necessari per più di una singola richiesta.

Il codice seguente imposta il valore di `Message` usando `TempData`:

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/CreateDot.cshtml.cs?highlight=10-11,25&name=snippet_Temp)]

Il markup seguente nel file *Pages/Customers/Index.cshtml* visualizza il valore di `Message` usando `TempData`.

```cshtml
<h3>Msg: @Model.Message</h3>
```

Il modello di pagina *Pages/Customers/Index.cshtml.cs* applica l'attributo `[TempData]` alla proprietà `Message`.

```csharp
[TempData]
public string Message { get; set; }
```

Per altre informazioni, vedere [TempData](xref:fundamentals/app-state#tempdata).

<a name="mhpp"></a>

## <a name="multiple-handlers-per-page"></a>Più gestori per pagina

La pagina seguente genera markup per due gestori usando l'helper tag `asp-page-handler`:

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?highlight=12-13)]

<!-- Review: the FormActionTagHelper applies to all <form /> elements on a Razor page, even when there's no `asp-` attribute   -->

Il form nell'esempio precedente contiene due pulsanti di invio, ognuno dei quali usa `FormActionTagHelper` per l'invio a un URL diverso. L'attributo `asp-page-handler` è correlato a `asp-page`. `asp-page-handler` genera gli URL che indirizzano a ognuno dei metodi gestore definiti da una pagina. `asp-page` non è specificato poiché l'esempio è collegato alla pagina corrente.

Il modello di pagina:

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml.cs?highlight=20,32)]

Il codice precedente usa *metodi gestore denominati*. I metodi gestore denominati vengono creati usando il testo che nel nome segue `On<HTTP Verb>` e precede `Async` (se presente). Nell'esempio precedente i metodi della pagina sono OnPost**JoinList**Async e OnPost**JoinListUC**Async. Rimuovendo *OnPost* e *Async*, i nomi dei gestori sono `JoinList` e `JoinListUC`.

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?range=12-13)]

Usando il codice precedente, il percorso URL che indirizza a `OnPostJoinListAsync` è `https://localhost:5001/Customers/CreateFATH?handler=JoinList`. Il percorso URL che indirizza a `OnPostJoinListUCAsync` è `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`.

## <a name="custom-routes"></a>Route personalizzate

Usare la direttiva `@page` per:

* Specificare una route personalizzata a una pagina. Ad esempio, è possibile impostare la route alla pagina About (Informazioni) su `/Some/Other/Path` con `@page "/Some/Other/Path"`.
* Aggiungere segmenti alla route predefinita di una pagina. Ad esempio, è possibile aggiungere un segmento "item" alla route predefinita di una pagina con `@page "item"`.
* Aggiungere parametri alla route predefinita di una pagina. Ad esempio, un parametro ID, `id`, può essere necessario per una pagina con `@page "{id}"`.

Un percorso relativo alla directory radice designato da una tilde (`~`) all'inizio del percorso è supportato. Ad esempio, `@page "~/Some/Other/Path"` equivale a `@page "/Some/Other/Path"`.

Se la stringa `?handler=JoinList` di query non è piaciuta nell'URL, modificare la route in modo da inserire il nome del gestore nella parte del percorso dell'URL. Il percorso può essere personalizzato aggiungendo un modello di route `@page` racchiuso tra virgolette doppie dopo la direttiva.

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateRoute.cshtml?highlight=1)]

Usando il codice precedente, il percorso URL che indirizza a `OnPostJoinListAsync` è `https://localhost:5001/Customers/CreateFATH/JoinList`. Il percorso URL che indirizza a `OnPostJoinListUCAsync` è `https://localhost:5001/Customers/CreateFATH/JoinListUC`.

`?` che segue `handler` indica che il parametro di route è facoltativo.

## <a name="configuration-and-settings"></a>Configurazione e impostazioni

Per configurare le opzioni avanzate, usare il metodo di estensione `AddRazorPagesOptions` nel generatore MVC:

[!code-cs[](index/sample/RazorPagesContacts/StartupAdvanced.cs?name=snippet_1)]

Attualmente è possibile usare `RazorPagesOptions` per impostare la directory radice per le pagine o aggiungere convenzioni di modello applicativo per le pagine. In questo modo si potrà garantire una maggiore estendibilità in futuro.

Per la precompilazione delle visualizzazioni, vedere l'articolo sulla [compilazione delle visualizzazioni Razor](xref:mvc/views/view-compilation).

[Scaricare o visualizzare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/sample).

Vedere [Introduzione a Razor Pages](xref:tutorials/razor-pages/razor-pages-start), che si basa su questa introduzione.

### <a name="specify-that-razor-pages-are-at-the-content-root"></a>Specificare che Razor Pages si trova nella radice del contenuto

Per impostazione predefinita, la directory radice di Razor Pages è */Pages*. Aggiungere [WithRazorPagesAtContentRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvcbuilderextensions.withrazorpagesatcontentroot) ad [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) per specificare che le pagine Razor si trovano nella radice del [contenuto](xref:fundamentals/index#content-root) ([ContentRootPath](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment.contentrootpath)) dell'app:

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        ...
    })
    .WithRazorPagesAtContentRoot();
```

### <a name="specify-that-razor-pages-are-at-a-custom-root-directory"></a>Specificare che Razor Pages è in una directory radice personalizzata

Aggiungere [WithRazorPagesRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvccorebuilderextensions.withrazorpagesroot) a [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) per specificare che Razor Pages si trova in una directory radice personalizzata nell'app (fornire un percorso relativo):

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        ...
    })
    .WithRazorPagesRoot("/path/to/razor/pages");
```

## <a name="additional-resources"></a>Risorse aggiuntive

* <xref:index>
* <xref:mvc/views/razor>
* <xref:mvc/controllers/areas>
* <xref:tutorials/razor-pages/razor-pages-start>
* <xref:security/authorization/razor-pages-authorization>
* <xref:razor-pages/razor-pages-conventions>
* <xref:test/razor-pages-tests>
* <xref:mvc/views/partial>

::: moniker-end
