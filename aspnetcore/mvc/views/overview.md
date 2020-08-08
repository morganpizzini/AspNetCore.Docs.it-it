---
title: Visualizzazioni in ASP.NET Core MVC
author: ardalis
description: Informazioni su come le visualizzazioni gestiscono la presentazione dei dati dell'app e l'interazione dell'utente in ASP.NET Core MVC.
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
uid: mvc/views/overview
ms.openlocfilehash: 8630df0ad8ea556c6edf0ab251b3c86493f751e2
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/08/2020
ms.locfileid: "88020873"
---
# <a name="views-in-aspnet-core-mvc"></a>Visualizzazioni in ASP.NET Core MVC

Di [Steve Smith](https://ardalis.com/)

Questo documento illustra le visualizzazioni usate nelle applicazioni ASP.NET Core MVC. Per informazioni sulle Razor pagine, vedere [Introduzione alle Razor pagine](xref:razor-pages/index).

Nello schema Model-View-Controller (MVC) la *visualizzazione* gestisce la presentazione dei dati dell'app e l'interazione dell'utente. Una vista è un modello HTML con [ Razor markup](xref:mvc/views/razor)incorporato. Razoril markup è codice che interagisce con il markup HTML per produrre una pagina Web che viene inviata al client.

In ASP.NET Core MVC le visualizzazioni sono file con *estensione cshtml* che usano il [linguaggio di programmazione C#](/dotnet/csharp/) nel Razor markup. I file di visualizzazione sono in genere raggruppati in cartelle denominate per ognuno dei [controller](xref:mvc/controllers/actions) dell'app. Le cartelle vengono archiviate in una cartella *Views* alla radice dell'app:

![Cartella delle visualizzazioni aperta in Esplora soluzioni di Visual Studio con la cartella Home aperta per mostrare i file About.cshtml, Contact.cshtml e Index.cshtml](overview/_static/views_solution_explorer.png)

Il controller *Home* è rappresentato da una cartella *Home* all'interno della cartella *Views*. La cartella *Home* contiene le visualizzazioni per le pagine Web *About*, *Contact* e *Index* (home page). Quando un utente richiede una di queste tre pagine Web, le azioni del controller nel controller *Home* determinano quale delle tre visualizzazioni verrà usata per compilare e restituire una pagina Web all'utente.

Usare i [layout](xref:mvc/views/layout) per offrire sezioni di pagine Web coerenti e ridurre la ripetizione del codice. I layout spesso includono l'intestazione, elementi di navigazione e menu, nonché il piè di pagina. L'intestazione e il piè di pagina in genere contengono il markup boilerplate per molti elementi di metadati e i collegamenti alle risorse di script e stile. I layout consentono di evitare questo markup boilerplate nelle visualizzazioni.

Le [visualizzazioni parziali](xref:mvc/views/partial) riducono la duplicazione del codice grazie alla gestione delle parti riutilizzabili delle visualizzazioni. Una visualizzazione parziale è ad esempio utile per la biografia di un autore nel sito Web di un blog che viene mostrata in diverse visualizzazioni. Nel caso della biografia di un autore, il contenuto di visualizzazione è ordinario e non richiede l'esecuzione di codice per produrre il contenuto per la pagina Web. Il contenuto della biografia di un autore è disponibile per la visualizzazione tramite la sola associazione di modelli, quindi l'uso di una visualizzazione parziale per questo tipo di contenuto è ideale.

I [componenti di visualizzazione](xref:mvc/views/view-components) sono simili alle visualizzazioni parziali nel senso che consentono di ridurre il codice ripetitivo, ma sono adatti per visualizzare contenuto che richiede l'esecuzione di codice nel server al fine di eseguire il rendering della pagina Web. I componenti di visualizzazione sono utili quando il contenuto sottoposto a rendering richiede l'interazione con il database, ad esempio nel caso del carrello acquisti di un sito Web. I componenti di visualizzazione non si limitano all'associazione di modelli per produrre l'output della pagina Web.

## <a name="benefits-of-using-views"></a>Vantaggi dell'uso delle visualizzazioni

Le visualizzazioni consentono di stabilire una [separazione dei concetti](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) all'interno di un'app MVC separando il markup dell'interfaccia utente da altre parti dell'app. La progettazione SoC rende l'app modulare offrendo diversi vantaggi:

* L'app risulta più facile da gestire perché è organizzata meglio. Le visualizzazioni sono in genere raggruppate per funzionalità dell'app. Individuare le visualizzazioni correlate quando si lavora su una funzionalità risulterà quindi più semplice.
* Le parti dell'app sono a regime di controllo libero. È possibile compilare e aggiornare le visualizzazioni dell'app separatamente dai componenti di logica di business e accesso ai dati. È possibile modificare le visualizzazioni dell'app senza dover necessariamente aggiornare altre parti dell'app.
* Poiché le visualizzazioni sono unità distinte, testare le parti dell'interfaccia utente dell'app risulta più semplice.
* Grazie alla migliore organizzazione, le probabilità che le sezioni dell'interfaccia utente vengano ripetute accidentalmente sono minori.

## <a name="creating-a-view"></a>Creazione di una visualizzazione

Le visualizzazioni specifiche di un controller vengono create nella cartella *Views/[ControllerName]*. Le visualizzazioni condivise tra i controller vengono inserite nella cartella *Views/Shared*. Per creare una visualizzazione aggiungere un nuovo file e assegnargli lo stesso nome dell'azione del controller a essa associata con l'estensione *cshtml*. Per creare una visualizzazione che corrisponda all'azione *About* nel controller *Home*, creare un file *About.cshtml* nella cartella *Views/Home*:

[!code-cshtml[](../../common/samples/WebApplication1/Views/Home/About.cshtml)]

*Razor* il markup inizia con il `@` simbolo. Eseguire istruzioni C# inserendo il codice C# all'interno di [ Razor blocchi di codice](xref:mvc/views/razor#razor-code-blocks) impostati da parentesi graffe ( `{ ... }` ). Vedere ad esempio l'assegnazione di "About" a `ViewData["Title"]` illustrato sopra. È possibile visualizzare i valori in HTML facendo semplicemente riferimento al valore con il simbolo `@`. Vedere il contenuto degli elementi `<h2>` e `<h3>` riportati sopra.

Il contenuto della visualizzazione illustrato sopra è solo una parte dell'intera pagina Web di cui viene eseguito il rendering per l'utente. Il resto del layout della pagina e altri aspetti comuni della visualizzazione sono specificati in altri file. Per altre informazioni, vedere l'[argomento Layout](xref:mvc/views/layout).

## <a name="how-controllers-specify-views"></a>Modalità di scelta delle visualizzazioni da parte dei controller

Le visualizzazioni vengono in genere restituite da azioni come [ViewResult](/dotnet/api/microsoft.aspnetcore.mvc.viewresult), ovvero un tipo di [ActionResult](/dotnet/api/microsoft.aspnetcore.mvc.actionresult). Il metodo dell'azione può creare e restituire direttamente un elemento `ViewResult`, ma questa procedura non è molto comune. Poiché la maggior parte dei controller ereditano da [Controller](/dotnet/api/microsoft.aspnetcore.mvc.controller), si usa semplicemente il metodo helper `View` per restituire l'elemento `ViewResult`:

*HomeController.cs*

[!code-csharp[](../../common/samples/WebApplication1/Controllers/HomeController.cs?highlight=5&range=16-21)]

Quando viene restituita questa azione, il rendering della visualizzazione *About.cshtml* nell'ultima sezione corrisponderà alla pagina Web seguente:

![Rendering della pagina About nel browser Microsoft Edge](overview/_static/about-page.png)

Il metodo helper `View` ha diversi overload. È possibile specificare:

* Una visualizzazione esplicita da restituire:

  ```csharp
  return View("Orders");
  ```

* Un [modello](xref:mvc/models/model-binding) da passare alla visualizzazione:

  ```csharp
  return View(Orders);
  ```

* Una visualizzazione e un modello:

  ```csharp
  return View("Orders", Orders);
  ```

### <a name="view-discovery"></a>Individuazione delle visualizzazioni

Quando un'azione restituisce una visualizzazione viene eseguito un processo denominato *individuazione delle visualizzazioni*. Questo processo determina il file di visualizzazione che verrà usato in base al nome della visualizzazione. 

In base al comportamento predefinito, il metodo `View` (`return View();`) restituisce una visualizzazione con lo stesso nome del metodo dell'azione dal quale viene chiamato. Nel caso di *About, ad esempio, il nome del metodo * `ActionResult` del controller viene usato per cercare un file di visualizzazione denominato *About.cshtml*. Il runtime cerca prima la visualizzazione nella cartella *Views/[ControllerName]*. Se la cartella non contiene una visualizzazione corrispondente, la ricerca passerà alla cartella *Shared*.

La restituzione implicita di `ViewResult` con `return View();` o il passaggio esplicito del nome della visualizzazione al metodo `View` con `return View("<ViewName>");` non sono rilevanti. In entrambi i casi, l'individuazione delle visualizzazioni cercherà un file di visualizzazione corrispondente in quest'ordine:

   1. *Views/\[ControllerName]/\[ViewName].cshtml*
   1. *Views/Shared/\[ViewName].cshtml*

È possibile fornire il percorso di un file di visualizzazione anziché il nome di una visualizzazione. Se si usa un percorso assoluto che inizia alla radice dell'app (che inizia facoltativamente con "/" o "~/") è necessario specificare l'estensione *cshtml*:

```csharp
return View("Views/Home/About.cshtml");
```

È anche possibile usare un percorso relativo per specificare le visualizzazioni presenti in directory diverse senza l'estensione *cshtml*. All'interno di `HomeController` è possibile restituire la visualizzazione *Index* delle visualizzazioni *Manage* con il percorso relativo:

```csharp
return View("../Manage/Index");
```

In modo analogo, è possibile indicare la directory corrente specifica dei controller con il prefisso "./":

```csharp
return View("./About");
```

Le [visualizzazioni parziali](xref:mvc/views/partial) e i [componenti di visualizzazione](xref:mvc/views/view-components) usano meccanismi di individuazione simili, ma non identici.

È possibile personalizzare la convenzione predefinita per la modalità di ricerca delle visualizzazioni all'interno dell'app usando un'interfaccia [IViewLocationExpander](/dotnet/api/microsoft.aspnetcore.mvc.razor.iviewlocationexpander) personalizzata.

L'individuazione delle visualizzazioni si basa sull'individuazione dei file di visualizzazione in base al nome del file. Se il file system sottostante prevede la distinzione tra maiuscole e minuscole, per i nomi delle visualizzazioni verrà probabilmente applicata questa distinzione. Per la compatibilità tra sistemi operativi, rispettare la distinzione maiuscole/minuscole tra i nomi di controller e azioni e i nomi di cartelle e file di visualizzazione associati. In caso di errore relativo a un file di visualizzazione non trovato durante l'uso di un file system con distinzione tra maiuscole e minuscole, verificare che tra il nome del file di visualizzazione richiesto e il nome effettivo del file di visualizzazione l'uso delle maiuscole e minuscole corrisponda.

Per manutenibilità e chiarezza, seguire le procedure consigliate relative all'organizzazione della struttura di file per le visualizzazioni in modo da riflettere le relazioni tra controller, azioni e visualizzazioni.

## <a name="passing-data-to-views"></a>Passaggio dei dati alle visualizzazioni

Passare i dati alle visualizzazioni usando diversi approcci:

* Dati fortemente tipizzati: viewmodel
* Dati con tipizzazione debole
  * `ViewData` (`ViewDataAttribute`)
  * `ViewBag`

### <a name="strongly-typed-data-viewmodel"></a>Dati fortemente tipizzati: (viewmodel)

L'approccio più efficace consiste nello specificare un tipo di [modello](xref:mvc/models/model-binding) nella visualizzazione. Questo modello è comunemente noto come *viewmodel*. Un'istanza del tipo viewmodel viene passato alla visualizzazione dall'azione.

L'uso di un viewmodel per passare i dati a una visualizzazione consente a quest'ultima di trarre vantaggio dal controllo con tipizzazione *forte*. L'espressione *tipizzazione forte* o *fortemente tipizzato* indica che ogni variabile e costante ha un tipo definito in modo esplicito, ad esempio `string`, `int` o `DateTime`. La validità dei tipi usati in una visualizzazione viene verificata in fase di compilazione.

In [Visual Studio](https://visualstudio.microsoft.com) e [Visual Studio Code](https://code.visualstudio.com/) i membri delle classi fortemente tipizzati vengono elencati usando una funzionalità denominata [IntelliSense](/visualstudio/ide/using-intellisense). Per visualizzare le proprietà di un elemento viewmodel, digitare il nome della variabile per l'elemento viewmodel seguito da un punto (`.`). Ciò consente di scrivere il codice più velocemente con un minor numero di errori.

Specificare un modello usando la direttiva `@model`. Usare il modello con `@Model`:

```cshtml
@model WebApplication1.ViewModels.Address

<h2>Contact</h2>
<address>
    @Model.Street<br>
    @Model.City, @Model.State @Model.PostalCode<br>
    <abbr title="Phone">P:</abbr> 425.555.0100
</address>
```

Per fornire il modello alla visualizzazione, il controller lo passa come parametro:

```csharp
public IActionResult Contact()
{
    ViewData["Message"] = "Your contact page.";

    var viewModel = new Address()
    {
        Name = "Microsoft",
        Street = "One Microsoft Way",
        City = "Redmond",
        State = "WA",
        PostalCode = "98052-6399"
    };

    return View(viewModel);
}
```

Non ci sono limitazioni ai tipi di modello che è possibile fornire a una visualizzazione. È consigliabile usare viewmodel Plain Old CLR Object (POCO) per i quali non siano stati definiti comportamenti (metodi) o ne siano stati definiti solo alcuni. Le classi viewmodel sono in genere archiviate nella cartella *Models* o in una cartella *ViewModels* separata alla radice dell'app. Il viewmodel *Address* usato nell'esempio precedente è un viewmodel POCO archiviato in un file denominato *Address.cs*:

```csharp
namespace WebApplication1.ViewModels
{
    public class Address
    {
        public string Name { get; set; }
        public string Street { get; set; }
        public string City { get; set; }
        public string State { get; set; }
        public string PostalCode { get; set; }
    }
}
```

Nulla impedisce di usare le stesse classi per i tipi viewmodel e i tipi del modello aziendale. L'uso di modelli separati consente tuttavia la variazione delle visualizzazioni indipendentemente dalle parti relative alla logica di business e all'accesso ai dati dell'app. La separazione di modelli e viewmodel offre anche vantaggi in termini di sicurezza quando i modelli usano l'[associazione di modelli](xref:mvc/models/model-binding) e la [convalida](xref:mvc/models/validation) per i dati inviati all'app dall'utente.

<a name="VD_VB"></a>

### <a name="weakly-typed-data-viewdata-viewdata-attribute-and-viewbag"></a>Dati con tipizzazione debole (ViewData, attributo ViewData e ViewBag)

`ViewBag`*non è disponibile in Razor Pagine.*

Oltre alle visualizzazioni fortemente tipizzate, le visualizzazioni hanno accesso a una raccolta di dati *con tipizzazione debole* o *debolmente tipizzati*. A differenza della tipizzazione forte, la *tipizzazione debole*, o l'espressione *debolmente tipizzato*, indica che il tipo di dati in uso non viene dichiarato in modo esplicito. È possibile usare la raccolta di dati con tipizzazione debole per passare piccole quantità di dati da e verso i controller e le visualizzazioni.

| Passaggio dei dati tra...                        | Esempio                                                                        |
| ------------------------------------------------- | ------------------------------------------------------------------------------ |
| Un controller e una visualizzazione                             | Popolamento di dati in un elenco a discesa.                                          |
| Una visualizzazione e una [visualizzazione Layout](xref:mvc/views/layout)   | Impostazione del **\<title>** contenuto dell'elemento nella visualizzazione layout da un file di visualizzazione.  |
| Una [visualizzazione parziale](xref:mvc/views/partial) e una visualizzazione | Widget che visualizza i dati in base alla pagina Web richiesta dall'utente.      |

È possibile fare riferimento a questa raccolta tramite le proprietà `ViewData` o `ViewBag` nei controller e nelle visualizzazioni. La proprietà `ViewData` è un dizionario di oggetti con tipizzazione debole. La proprietà `ViewBag` è un wrapper di `ViewData` che offre proprietà dinamiche per la raccolta `ViewData` sottostante. Nota: le ricerche chiave non fanno distinzione tra maiuscole e minuscole sia per `ViewData` che per `ViewBag` .

`ViewData` e `ViewBag` vengono risolte in modo dinamico in fase di esecuzione. Poiché non offrono il controllo del tipo in fase di compilazione, entrambe sono in genere più soggette a errori rispetto all'uso di un elemento viewmodel. Per questo motivo, alcuni sviluppatori preferiscono non usare mai `ViewData` e `ViewBag` o usarle il meno possibile.

<a name="VD"></a>

**ViewData**

`ViewData` è un oggetto [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) a cui si accede tramite le chiavi `string`. I dati di tipo stringa possono essere archiviati e usati direttamente, senza la necessità di un cast, ma è necessario eseguire il cast di altri valori dell'oggetto `ViewData` in tipi specifici quando vengono estratti. È possibile usare `ViewData` per passare i dati dai controller alle visualizzazioni e al loro interno, inclusi [visualizzazioni parziali](xref:mvc/views/partial) e [layout](xref:mvc/views/layout).

Nell'esempio seguente vengono impostati i valori per una formula di saluto e un indirizzo usando `ViewData` in un'azione:

```csharp
public IActionResult SomeAction()
{
    ViewData["Greeting"] = "Hello";
    ViewData["Address"]  = new Address()
    {
        Name = "Steve",
        Street = "123 Main St",
        City = "Hudson",
        State = "OH",
        PostalCode = "44236"
    };

    return View();
}
```

Lavorare con i dati in una visualizzazione:

```cshtml
@{
    // Since Address isn't a string, it requires a cast.
    var address = ViewData["Address"] as Address;
}

@ViewData["Greeting"] World!

<address>
    @address.Name<br>
    @address.Street<br>
    @address.City, @address.State @address.PostalCode
</address>
```

::: moniker range=">= aspnetcore-2.1"

**Attributo viewData**

Un altro approccio che usa [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) è [ViewDataAttribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute). I valori delle proprietà nei controller o nei Razor modelli di pagina contrassegnati con l' `[ViewData]` attributo sono archiviati e caricati dal dizionario.

Nell'esempio seguente il controller Home contiene una `Title` proprietà contrassegnata con `[ViewData]` . Il metodo `About` imposta il titolo per la visualizzazione About (Informazioni):

```csharp
public class HomeController : Controller
{
    [ViewData]
    public string Title { get; set; }

    public IActionResult About()
    {
        Title = "About Us";
        ViewData["Message"] = "Your application description page.";

        return View();
    }
}
```

Nel layout il titolo viene letto dal dizionario ViewData:

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"] - WebApplication</title>
    ...
```

::: moniker-end

**ViewBag**

`ViewBag`*non è disponibile in Razor Pagine.*

`ViewBag`è un oggetto [DynamicViewData](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.internal.dynamicviewdata) che fornisce l'accesso dinamico agli oggetti archiviati in `ViewData` . `ViewBag` può risultare più comodo da usare poiché non richiede l'esecuzione del cast. Nell'esempio seguente viene illustrato come usare `ViewBag` con lo stesso risultato che si ottiene con l'uso di `ViewData` descritto in precedenza:

```csharp
public IActionResult SomeAction()
{
    ViewBag.Greeting = "Hello";
    ViewBag.Address  = new Address()
    {
        Name = "Steve",
        Street = "123 Main St",
        City = "Hudson",
        State = "OH",
        PostalCode = "44236"
    };

    return View();
}
```

```cshtml
@ViewBag.Greeting World!

<address>
    @ViewBag.Address.Name<br>
    @ViewBag.Address.Street<br>
    @ViewBag.Address.City, @ViewBag.Address.State @ViewBag.Address.PostalCode
</address>
```

**Uso simultaneo di ViewData e ViewBag**

`ViewBag`*non è disponibile in Razor Pagine.*

Dal momento che `ViewData` e `ViewBag` fanno riferimento alla stessa raccolta `ViewData` sottostante, è possibile usare `ViewData` e `ViewBag` e combinarle durante la lettura e la scrittura dei valori.

Impostare il titolo usando `ViewBag` e la descrizione usando `ViewData` all'inizio di una visualizzazione *About.cshtml*:

```cshtml
@{
    Layout = "/Views/Shared/_Layout.cshtml";
    ViewBag.Title = "About Contoso";
    ViewData["Description"] = "Let us tell you about Contoso's philosophy and mission.";
}
```

Leggere le proprietà ma invertire l'uso di `ViewData` e `ViewBag`. Nel file *_Layout.cshtml* ottenere il titolo usando `ViewData` e ottenere la descrizione usando `ViewBag`:

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"]</title>
    <meta name="description" content="@ViewBag.Description">
    ...
```

Tenere presente che per `ViewData` non è necessario eseguire il cast delle stringhe. È possibile usare `@ViewData["Title"]` senza eseguire il cast.

L'uso simultaneo di `ViewData` e `ViewBag` funziona, così come funziona la combinazione di entrambe durante la lettura e la scrittura delle proprietà. Viene eseguito il rendering del markup seguente:

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <title>About Contoso</title>
    <meta name="description" content="Let us tell you about Contoso's philosophy and mission.">
    ...
```

**Riepilogo delle differenze tra ViewData e ViewBag**

 `ViewBag`non è disponibile nelle Razor pagine.

* `ViewData`
  * Deriva da [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary), quindi presenta proprietà del dizionario che possono essere utili, ad esempio `ContainsKey` ,, `Add` `Remove` e `Clear` .
  * Le chiavi nel dizionario sono stringhe, pertanto lo spazio vuoto è consentito. Esempio: `ViewData["Some Key With Whitespace"]`
  * Per usare `ViewData` è necessario eseguire il cast di tutti i tipi diversi da `string` nella visualizzazione.
* `ViewBag`
  * Deriva da [DynamicViewData](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.internal.dynamicviewdata) e consente quindi la creazione di proprietà dinamiche usando la notazione del punto (`@ViewBag.SomeKey = <value or object>`). L'esecuzione del cast non è necessaria. La sintassi di `ViewBag` velocizza l'aggiunta in controller e visualizzazioni.
  * Verificare la presenza di valori Null è più facile. Esempio: `@ViewBag.Person?.Name`

**Quando usare ViewData o ViewBag**

`ViewData` e `ViewBag` sono entrambi approcci ugualmente validi per passare piccole quantità di dati tra controller e visualizzazioni. La scelta di quello da usare è basata sulla preferenza. È possibile combinare oggetti `ViewData` e `ViewBag`. La lettura e la gestione del codice risulteranno tuttavia più semplici con un unico approccio usato in modo coerente. Entrambi gli approcci vengono risolti in modo dinamico in fase di esecuzione e possono quindi determinare errori di runtime. Alcuni team di sviluppo li evitano.

### <a name="dynamic-views"></a>Visualizzazioni dinamiche

Le visualizzazioni che non dichiarano un tipo di modello usando `@model` ma a cui è stata passata l'istanza di un modello, ad esempio `return View(Address);`, possono fare riferimento alle proprietà dell'istanza in modo dinamico:

```cshtml
<address>
    @Model.Street<br>
    @Model.City, @Model.State @Model.PostalCode<br>
    <abbr title="Phone">P:</abbr> 425.555.0100
</address>
```

Questa funzionalità offre flessibilità ma non offre la protezione della compilazione o IntelliSense. Se la proprietà non esiste, in fase di esecuzione la generazione della pagina Web non riesce.

## <a name="more-view-features"></a>Altre funzionalità delle visualizzazioni

Gli [helper tag](xref:mvc/views/tag-helpers/intro) semplificano l'aggiunta del comportamento sul lato server ai tag HTML esistenti. Grazie all'uso degli helper tag, è possibile evitare di scrivere codice personalizzato o helper all'interno delle visualizzazioni. Gli helper tag vengono applicati come attributi agli elementi HTML e vengono ignorati dagli editor che non sono in grado di elaborarli. Ciò consente di modificare ed eseguire il rendering del markup delle visualizzazioni in un'ampia gamma di strumenti.

La generazione di markup HTML personalizzato può essere ottenuta con molti helper HTML predefiniti. Una logica dell'interfaccia utente più complessa può essere gestita dai [componenti di visualizzazione](xref:mvc/views/view-components). I componenti di visualizzazione offrono lo stesso tipo di progettazione SoC offerta dai controller e dalle visualizzazioni. Possono eliminare la necessità di azioni e visualizzazioni che gestiscono i dati usati da elementi comuni dell'interfaccia utente.

Come molti altri aspetti di ASP.NET Core, le visualizzazioni supportano l'[inserimento di dipendenze](xref:fundamentals/dependency-injection), consentendo che i servizi siano [inseriti nelle visualizzazioni](xref:mvc/views/dependency-injection).
