---
title: Aggiungere una vista a un'app ASP.NET Core MVC
author: rick-anderson
description: Aggiunta di una vista a una semplice app ASP.NET Core MVC
ms.author: riande
ms.date: 8/04/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/first-mvc-app/adding-view
ms.openlocfilehash: 233d6cc060481c7b2efc9a1babc12f60f457a075
ms.sourcegitcommit: 05490855e0c70565f0c4b509d392b0828bcfd141
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/08/2020
ms.locfileid: "84507335"
---
# <a name="add-a-view-to-an-aspnet-core-mvc-app"></a>Aggiungere una vista a un'app ASP.NET Core MVC

Autore: [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

In questa sezione viene modificata la `HelloWorldController` classe per l'utilizzo [Razor](xref:mvc/views/razor) dei file di visualizzazione per incapsulare in modo semplice il processo di generazione di risposte HTML a un client.

Si crea un file di modello di visualizzazione utilizzando Razor . Razori modelli di vista basati su hanno un'estensione di file *. cshtml* . Consentono di creare l'output HTML in modo accurato con C#.

Attualmente il metodo `Index` restituisce una stringa con un messaggio hardcoded nella classe controller. Nella classe `HelloWorldController` sostituire il metodo `Index` con il codice seguente:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_4)]

Il codice precedente chiama il metodo <xref:Microsoft.AspNetCore.Mvc.Controller.View*> del controller. Usa un modello di visualizzazione per generare una risposta HTML. I metodi del controller, noti anche come *metodi di azione*, ad esempio il metodo `Index` descritto in precedenza, restituiscono in genere un <xref:Microsoft.AspNetCore.Mvc.IActionResult> (o una classe derivata da <xref:Microsoft.AspNetCore.Mvc.ActionResult>) e non un tipo come `string`.

## <a name="add-a-view"></a>Aggiungere una vista

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Fare clic con il pulsante destro del mouse sulla cartella *Views*, quindi su **Aggiungi > Nuova cartella** e denominare la cartella *HelloWorld*.

* Fare clic con il pulsante destro del mouse sulla cartella *Views/HelloWorld*, quindi su **Aggiungi > Nuovo elemento**.

* Nella finestra di dialogo **Aggiungi nuovo elemento - MvcMovie**

  * Nella casella di ricerca in alto a destra immettere *view* (vista)

  * Selezione ** Razor visualizzazione**

  * Mantenere il valore della casella **Nome**, *Index.cshtml*.

  * Selezionare **Aggiungi**

![Finestra di dialogo Aggiungi nuovo elemento](adding-view/_static/add_view.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Aggiungere una vista `Index` per `HelloWorldController`.

* Aggiungere una nuova cartella denominata *Views/HelloWorld*.
* Aggiungere un nuovo file al nome di cartella *Views/HelloWorld**Index.cshtml*.

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/visual-studio-mac)

* Fare clic con il pulsante destro del mouse sulla cartella *Views*, quindi su **Aggiungi > Nuova cartella** e denominare la cartella *HelloWorld*.
* Fare clic con il pulsante destro del mouse sulla cartella *Views/HelloWorld*, quindi su **Aggiungi > Nuovo file**.
* Nel finestra di dialogo **Nuovo file**:

  * Selezionare **ASP .NET Core** nel riquadro sinistro.
  * Selezionare la **pagina visualizzazione MVC** nel riquadro centrale.
  * Digitare *index* nella casella **nome** .
  * Selezionare **Nuovo**.

![Finestra di dialogo Aggiungi nuovo elemento](adding-view/_static/add_view_mac.png)

---

Sostituire il contenuto del file di visualizzazione *views/HelloWorld/index. cshtml* Razor con il codice seguente:

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/HelloWorld/Index1.cshtml?highlight=7)]

Passare a `https://localhost:{PORT}/HelloWorld`. Il metodo `Index` in `HelloWorldController` non ha eseguito molte operazioni; ha eseguito l'istruzione `return View();` che ha specificato che il metodo deve usare un file di modello della vista per eseguire il rendering di una risposta al browser. Poiché non è stato specificato un nome di file di modello di visualizzazione, per impostazione predefinita MVC usa il file di visualizzazione predefinito. Il file di visualizzazione predefinito ha lo stesso nome del metodo ( `Index` ), quindi viene usato il modello di visualizzazione in */views/HelloWorld/index.cshtml* . L'immagine seguente mostra la stringa "Hello from our View Template!" hardcoded nella vista.

![Finestra del browser](~/tutorials/first-mvc-app/adding-view/_static/hell_template.png)

## <a name="change-views-and-layout-pages"></a>Modificare le viste e le pagine di layout

Selezionare i collegamenti di menu: **MvcMovie**, **Home** e **Privacy**. Ogni pagina mostra lo stesso layout di menu. Il layout di menu viene implementato nel file *Views/Shared/_Layout.cshtml*. Aprire il file *Views/Shared/_Layout.cshtml*.

I modelli di [layout](xref:mvc/views/layout) consentono di specificare il layout del contenitore HTML del sito in un'unica posizione e quindi di applicarlo in più pagine del sito. Trovare la riga `@RenderBody()`. `RenderBody` è un segnaposto dove tutte le pagine specifiche della vista vengono presentate, *incapsulate* nella pagina di layout. Ad esempio, se si seleziona il collegamento **Privacy**, il rendering della vista **Views/Home/Privacy.cshtml** viene eseguito all'interno del metodo `RenderBody`.

## <a name="change-the-title-footer-and-menu-link-in-the-layout-file"></a>Modificare il titolo, il piè di pagina e il collegamento di menu nel file di layout

Sostituire il contenuto del file *Views/Shared/_Layout. cshtml* con il markup seguente. Le modifiche sono evidenziate:

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Views/Shared/_Layout.cshtml?highlight=6,14,40)]

Il markup precedente ha apportato le modifiche seguenti:

* 3 occorrenze di `MvcMovie` sostituite con `Movie App`.
* L'elemento di ancoraggio `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>` è stato sostituito con `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>`.

Nel markup precedente, l'attributo `asp-area=""` [Helper Tag di ancoraggio](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) e il valore dell'attributo sono stati omessi perché questa app non usa le [Aree](xref:mvc/controllers/areas).

**Nota**: il `Movies` controller non è stato implementato. A questo punto, il collegamento `Movie App` non è funzionale.

Salvare le modifiche e selezionare il collegamento **Privacy**. Si noti come il titolo sulla scheda del browser visualizzi **Privacy Policy - Movie App** anziché **Privacy Policy - Mvc Movie**:

![Scheda Privacy](~/tutorials/first-mvc-app/adding-view/_static/about2.png)

Toccare il collegamento **Home** e notare che anche il titolo e il testo di ancoraggio visualizzano **Movie App**. La modifica è stata apportata una volta nel modello di layout e tutte le pagine nel sito riflettono il nuovo testo del collegamento e il nuovo titolo.

Esaminare il file *Views/_ViewStart.cshtml*:

```cshtml
@{
    Layout = "_Layout";
}
```

Il file *Views/_ViewStart.cshtml* attiva il file *Views/Shared/_Layout.cshtml* per ogni vista. È possibile usare la proprietà `Layout` per impostare una vista di layout differente oppure impostarla su `null` e quindi non verrà usato alcun file di layout.

Modificare il titolo e l'elemento `<h2>` del file *Views/HelloWorld/Index.cshtml* di visualizzazione:

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Index2.cshtml?highlight=2,5)]

Il titolo e l'elemento `<h2>`sono leggermente diversi in modo da poter esaminare la parte specifica di codice che modifica la visualizzazione.

`ViewData["Title"] = "Movie List";` nel codice precedente imposta la proprietà `Title` del dizionario `ViewData` su "Movie List". La proprietà `Title` viene usata nell'elemento HTML `<title>` nella pagina di layout:

```cshtml
<title>@ViewData["Title"] - Movie App</title>
```

Salvare la modifica e passare a `https://localhost:{PORT}/HelloWorld`. Si noti che il titolo del browser, l'intestazione primaria e le intestazioni secondarie sono cambiati. Se le modifiche non sono visibili nel browser, è possibile che si stia visualizzando il contenuto memorizzato nella cache. Premere CTRL + F5 nel browser per forzare il caricamento della risposta dal server. Il titolo del browser viene creato con l' `ViewData["Title"]` impostazione nel modello di vista *index. cshtml* e l'aggiunta di "-Movie app" nel file di layout.

Il contenuto del modello di vista *Index.cshtml* viene unito al modello di vista *Views/Shared/_Layout.cshtml*. Viene inviata una singola risposta HTML al browser. I modelli di layout rendono semplice apportare modifiche che si applicano a tutte le pagine di un'app. Per altre informazioni, vedere [layout](xref:mvc/views/layout).

![Vista dell'elenco di film](~/tutorials/first-mvc-app/adding-view/_static/hell3.png)

Il breve testo di "dati", in questo caso il messaggio "Hello from our View Template!", è tuttavia hardcoded. L'applicazione MVC ha un elemento "V" (vista) ed è stato ottenuto un elemento "C" (controller), ma non ancora un elemento "M" (modello).

## <a name="passing-data-from-the-controller-to-the-view"></a>Passaggio di dati dal controller alla vista

Le azioni del controller vengono richiamate in risposta a una richiesta URL in ingresso. Una classe controller è il punto in cui viene scritto il codice che gestisce le richieste in ingresso del browser. Il controller recupera i dati da un'origine dati e determina il tipo di risposta da inviare al browser. I modelli di vista possono essere usati da un controller per generare e formattare una risposta HTML al browser.

Il compito dei controller è fornire i dati necessari affinché un modello di vista possa eseguire il rendering di una risposta. Procedura consigliata: i modelli di vista **non** devono eseguire la logica di business o interagire direttamente con un database. Un modello di vista deve invece usare solo i dati forniti dal controller. Questa "separazione delle problematiche" consente di mantenere il codice pulito e semplifica la gestione e i test.

Attualmente il metodo `Welcome` nella classe `HelloWorldController` accetta un parametro `name` e `ID` e quindi genera i valori direttamente al browser. Invece di ottenere il rendering di questa risposta come stringa, cambiare il controller in modo che usi un modello di vista. Il modello di vista genera una risposta dinamica, il che significa che è necessario passare i bit di dati appropriati dal controller alla vista per generare la risposta. A tale scopo, fare in modo che il controller inserisca i dati dinamici (parametri) di cui il modello di vista necessita in un dizionario `ViewData` a cui il modello di vista può accedere.

In *HelloWorldController.cs*, modificare il metodo `Welcome` in modo da aggiungere un valore `Message` e `NumTimes` al dizionario `ViewData`. Il dizionario `ViewData` è un oggetto dinamico, ovvero è possibile usare qualunque tipo; l'oggetto `ViewData` non dispone di proprietà definite fino a quando non si inserisce un elemento al suo interno. Il sistema di [associazione di modelli MVC](xref:mvc/models/model-binding) esegue il mapping dei parametri denominati (`name` e `numTimes`) dalla stringa di query nella barra degli indirizzi ai parametri nel metodo. Il file *HelloWorldController.cs* completo avrà un aspetto simile al seguente:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_5)]

L'oggetto di dizionario `ViewData` contiene i dati che verranno passati alla vista.

Creare un modello di vista Welcome denominato *Views/HelloWorld/Welcome.cshtml*.

Si creerà un ciclo nel modello di vista *Welcome.cshtml* che visualizza la stringa "Hello" `NumTimes`. Sostituire il contenuto di *Views/HelloWorld/Welcome.cshtml* con quanto segue:

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Welcome.cshtml)]

Salvare le modifiche e passare all'URL seguente:

`https://localhost:{PORT}/HelloWorld/Welcome?name=Rick&numtimes=4`

I dati vengono prelevati dall'URL e passati al controller usando lo [strumento di associazione di modelli MVC](xref:mvc/models/model-binding). Il controller crea un pacchetto di dati in un dizionario `ViewData` e passa tale oggetto alla vista. La vista esegue quindi il rendering dei dati in formato HTML al browser.

![Vista Privacy con un'etichetta di benvenuto e la frase Hello Rick riportata quattro volte](~/tutorials/first-mvc-app/adding-view/_static/rick2.png)

Nell'esempio precedente è stato usato il dizionario `ViewData` per passare i dati dal controller a una vista. Più avanti nell'esercitazione si userà un modello di vista per passare i dati da un controller a una vista. In genere l'approccio basato sul modello di vista per passare i dati è preferito rispetto all'approccio basato sul dizionario `ViewData`. Per altre informazioni, vedere [quando usare ViewBag, ViewData o TempData](https://www.rachelappel.com/when-to-use-viewbag-viewdata-or-tempdata-in-asp-net-mvc-3-applications/) .

Nella prossima esercitazione, viene creato un database di film.

> [!div class="step-by-step"]
> [Precedente](adding-controller.md) 
>  [Avanti](adding-model.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

In questa sezione viene modificata la `HelloWorldController` classe per l'utilizzo [Razor](xref:mvc/views/razor) dei file di visualizzazione per incapsulare in modo semplice il processo di generazione di risposte HTML a un client.

Si crea un file di modello di visualizzazione utilizzando Razor . Razori modelli di vista basati su hanno un'estensione di file *. cshtml* . Consentono di creare l'output HTML in modo accurato con C#.

Attualmente il metodo `Index` restituisce una stringa con un messaggio hardcoded nella classe controller. Nella classe `HelloWorldController` sostituire il metodo `Index` con il codice seguente:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_4)]

Il codice precedente chiama il metodo <xref:Microsoft.AspNetCore.Mvc.Controller.View*> del controller. Usa un modello di visualizzazione per generare una risposta HTML. I metodi del controller, noti anche come *metodi di azione*, ad esempio il metodo `Index` descritto in precedenza, restituiscono in genere un <xref:Microsoft.AspNetCore.Mvc.IActionResult> (o una classe derivata da <xref:Microsoft.AspNetCore.Mvc.ActionResult>) e non un tipo come `string`.

## <a name="add-a-view"></a>Aggiungere una vista

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Fare clic con il pulsante destro del mouse sulla cartella *Views*, quindi su **Aggiungi > Nuova cartella** e denominare la cartella *HelloWorld*.

* Fare clic con il pulsante destro del mouse sulla cartella *Views/HelloWorld*, quindi su **Aggiungi > Nuovo elemento**.

* Nella finestra di dialogo **Aggiungi nuovo elemento - MvcMovie**

  * Nella casella di ricerca in alto a destra immettere *view* (vista)

  * Selezione ** Razor visualizzazione**

  * Mantenere il valore della casella **Nome**, *Index.cshtml*.

  * Selezionare **Aggiungi**

![Finestra di dialogo Aggiungi nuovo elemento](adding-view/_static/add_view.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Aggiungere una vista `Index` per `HelloWorldController`.

* Aggiungere una nuova cartella denominata *Views/HelloWorld*.
* Aggiungere un nuovo file al nome di cartella *Views/HelloWorld**Index.cshtml*.

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/visual-studio-mac)

* Fare clic con il pulsante destro del mouse sulla cartella *Views*, quindi su **Aggiungi > Nuova cartella** e denominare la cartella *HelloWorld*.
* Fare clic con il pulsante destro del mouse sulla cartella *Views/HelloWorld*, quindi su **Aggiungi > Nuovo file**.
* Nel finestra di dialogo **Nuovo file**:

  * Selezionare **Web** nel riquadro a sinistra.
  * Selezionare **File HTML vuoto** nel riquadro centrale.
  * Digitare *Index.cshtml* nella casella **Nome**.
  * Selezionare **Nuovo**.

![Finestra di dialogo Aggiungi nuovo elemento](adding-view/_static/add_view_mac.png)

---

Sostituire il contenuto del file di visualizzazione *views/HelloWorld/index. cshtml* Razor con il codice seguente:

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/HelloWorld/Index1.cshtml?highlight=7)]

Passare a `https://localhost:{PORT}/HelloWorld`. Il metodo `Index` in `HelloWorldController` non ha eseguito molte operazioni; ha eseguito l'istruzione `return View();` che ha specificato che il metodo deve usare un file di modello della vista per eseguire il rendering di una risposta al browser. Poiché non è stato specificato un nome di file di modello di visualizzazione, per impostazione predefinita MVC usa il file di visualizzazione predefinito. Il file di visualizzazione predefinito ha lo stesso nome del metodo (`Index`), quindi viene usato */Views/HelloWorld/Index.cshtml*. L'immagine seguente mostra la stringa "Hello from our View Template!" hardcoded nella vista.

![Finestra del browser](~/tutorials/first-mvc-app/adding-view/_static/hell_template.png)

## <a name="change-views-and-layout-pages"></a>Modificare le viste e le pagine di layout

Selezionare i collegamenti di menu: **MvcMovie**, **Home** e **Privacy**. Ogni pagina mostra lo stesso layout di menu. Il layout di menu viene implementato nel file *Views/Shared/_Layout.cshtml*. Aprire il file *Views/Shared/_Layout.cshtml*.

I modelli di [layout](xref:mvc/views/layout) consentono di specificare il layout del contenitore HTML del sito in un'unica posizione e quindi di applicarlo in più pagine del sito. Trovare la riga `@RenderBody()`. `RenderBody` è un segnaposto dove tutte le pagine specifiche della vista vengono presentate, *incapsulate* nella pagina di layout. Ad esempio, se si seleziona il collegamento **Privacy**, il rendering della vista **Views/Home/Privacy.cshtml** viene eseguito all'interno del metodo `RenderBody`.

## <a name="change-the-title-footer-and-menu-link-in-the-layout-file"></a>Modificare il titolo, il piè di pagina e il collegamento di menu nel file di layout

* Negli elementi del titolo e del piè di pagina modificare `MvcMovie` in `Movie App`.
* Modificare l'elemento ancora `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>` in `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>`.

Il markup seguente visualizza le modifiche evidenziate:

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Shared/_Layout.cshtml?highlight=6,24,51)]

Nel markup precedente, l'attributo `asp-area` [Helper Tag di ancoraggio](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) è stato omesso perché questa app non usa le [Aree](xref:mvc/controllers/areas).

<!-- Routing has changed in 2.2, it's going to the last route.
>[!WARNING]
> We haven't implemented the `Movies` controller yet, so if you click the `Movie App` link, you get a 404 (Not found) error.
-->

**Nota**: il `Movies` controller non è stato implementato. A questo punto, il collegamento `Movie App` non è funzionale.

Salvare le modifiche e selezionare il collegamento **Privacy**. Si noti come il titolo sulla scheda del browser visualizzi **Privacy Policy - Movie App** anziché **Privacy Policy - Mvc Movie**:

![Scheda Privacy](~/tutorials/first-mvc-app/adding-view/_static/about2.png)

Toccare il collegamento **Home** e notare che anche il titolo e il testo di ancoraggio visualizzano **Movie App**. La modifica è stata apportata una volta nel modello di layout e tutte le pagine nel sito riflettono il nuovo testo del collegamento e il nuovo titolo.

Esaminare il file *Views/_ViewStart.cshtml*:

```cshtml
@{
    Layout = "_Layout";
}
```

Il file *Views/_ViewStart.cshtml* attiva il file *Views/Shared/_Layout.cshtml* per ogni vista. È possibile usare la proprietà `Layout` per impostare una vista di layout differente oppure impostarla su `null` e quindi non verrà usato alcun file di layout.

Modificare il titolo e l'elemento `<h2>` del file *Views/HelloWorld/Index.cshtml* di visualizzazione:

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Index2.cshtml?highlight=2,5)]

Il titolo e l'elemento `<h2>`sono leggermente diversi in modo da poter esaminare la parte specifica di codice che modifica la visualizzazione.

`ViewData["Title"] = "Movie List";` nel codice precedente imposta la proprietà `Title` del dizionario `ViewData` su "Movie List". La proprietà `Title` viene usata nell'elemento HTML `<title>` nella pagina di layout:

```cshtml
<title>@ViewData["Title"] - Movie App</title>
```

Salvare la modifica e passare a `https://localhost:{PORT}/HelloWorld`. Si noti che il titolo del browser, l'intestazione primaria e le intestazioni secondarie sono cambiati. Se le modifiche non sono visibili nel browser, è possibile che si stia visualizzando il contenuto memorizzato nella cache. Premere CTRL + F5 nel browser per forzare il caricamento della risposta dal server. Il titolo del browser viene creato con l' `ViewData["Title"]` impostazione nel modello di vista *index. cshtml* e l'aggiunta di "-Movie app" nel file di layout.

Si noti anche come il contenuto del modello di vista *Index.cshtml* sia stato unito con il modello di vista *Views/Shared/_Layout.cshtml* e sia stata inviata una singola risposta HTML al browser. I modelli di layout rendono molto semplice apportare modifiche che si applicano a tutte le pagine dell'applicazione. Per altre informazioni, vedere [Layout](xref:mvc/views/layout).

![Vista dell'elenco di film](~/tutorials/first-mvc-app/adding-view/_static/hell3.png)

Il breve testo di "dati", in questo caso il messaggio "Hello from our View Template!", è tuttavia hardcoded. L'applicazione MVC ha un elemento "V" (vista) ed è stato ottenuto un elemento "C" (controller), ma non ancora un elemento "M" (modello).

## <a name="passing-data-from-the-controller-to-the-view"></a>Passaggio di dati dal controller alla vista

Le azioni del controller vengono richiamate in risposta a una richiesta URL in ingresso. Una classe controller è il punto in cui viene scritto il codice che gestisce le richieste in ingresso del browser. Il controller recupera i dati da un'origine dati e determina il tipo di risposta da inviare al browser. I modelli di vista possono essere usati da un controller per generare e formattare una risposta HTML al browser.

Il compito dei controller è fornire i dati necessari affinché un modello di vista possa eseguire il rendering di una risposta. Procedura consigliata: i modelli di vista **non** devono eseguire la logica di business o interagire direttamente con un database. Un modello di vista deve invece usare solo i dati forniti dal controller. Questa "separazione delle problematiche" consente di mantenere il codice pulito e semplifica la gestione e i test.

Attualmente il metodo `Welcome` nella classe `HelloWorldController` accetta un parametro `name` e `ID` e quindi genera i valori direttamente al browser. Invece di ottenere il rendering di questa risposta come stringa, cambiare il controller in modo che usi un modello di vista. Il modello di vista genera una risposta dinamica, il che significa che è necessario passare i bit di dati appropriati dal controller alla vista per generare la risposta. A tale scopo, fare in modo che il controller inserisca i dati dinamici (parametri) di cui il modello di vista necessita in un dizionario `ViewData` a cui il modello di vista può accedere.

In *HelloWorldController.cs*, modificare il metodo `Welcome` in modo da aggiungere un valore `Message` e `NumTimes` al dizionario `ViewData`. Il dizionario `ViewData` è un oggetto dinamico, ovvero è possibile usare qualunque tipo; l'oggetto `ViewData` non dispone di proprietà definite fino a quando non si inserisce un elemento al suo interno. Il sistema di [associazione di modelli MVC](xref:mvc/models/model-binding) esegue il mapping dei parametri denominati (`name` e `numTimes`) dalla stringa di query nella barra degli indirizzi ai parametri nel metodo. Il file *HelloWorldController.cs* completo avrà un aspetto simile al seguente:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_5)]

L'oggetto di dizionario `ViewData` contiene i dati che verranno passati alla vista.

Creare un modello di vista Welcome denominato *Views/HelloWorld/Welcome.cshtml*.

Si creerà un ciclo nel modello di vista *Welcome.cshtml* che visualizza la stringa "Hello" `NumTimes`. Sostituire il contenuto di *Views/HelloWorld/Welcome.cshtml* con quanto segue:

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Welcome.cshtml)]

Salvare le modifiche e passare all'URL seguente:

`https://localhost:{PORT}/HelloWorld/Welcome?name=Rick&numtimes=4`

I dati vengono prelevati dall'URL e passati al controller usando lo [strumento di associazione di modelli MVC](xref:mvc/models/model-binding). Il controller crea un pacchetto di dati in un dizionario `ViewData` e passa tale oggetto alla vista. La vista esegue quindi il rendering dei dati in formato HTML al browser.

![Vista Privacy con un'etichetta di benvenuto e la frase Hello Rick riportata quattro volte](~/tutorials/first-mvc-app/adding-view/_static/rick2.png)

Nell'esempio precedente è stato usato il dizionario `ViewData` per passare i dati dal controller a una vista. Più avanti nell'esercitazione si userà un modello di vista per passare i dati da un controller a una vista. In genere l'approccio basato sul modello di vista per passare i dati è preferito rispetto all'approccio basato sul dizionario `ViewData`. Per altre informazioni, vedere [quando usare ViewBag, ViewData o TempData](https://www.rachelappel.com/when-to-use-viewbag-viewdata-or-tempdata-in-asp-net-mvc-3-applications/) .

Nella prossima esercitazione, viene creato un database di film.

> [!div class="step-by-step"]
> [Precedente](adding-controller.md) 
>  [Avanti](adding-model.md)

::: moniker-end
