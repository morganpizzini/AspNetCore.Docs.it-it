---
title: Aggiungere un controller a un'app ASP.NET Core MVC
author: rick-anderson
description: Informazioni su come aggiungere un controller a una semplice app ASP.NET Core MVC.
ms.author: riande
ms.date: 08/05/2017
uid: tutorials/first-mvc-app/adding-controller
ms.openlocfilehash: fb670902b0dafa7dce2b3372e550095387844936
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "78666992"
---
# <a name="add-a-controller-to-an-aspnet-core-mvc-app"></a>Aggiungere un controller a un'app ASP.NET Core MVC

Autore: [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

Il pattern architetturale Model-View-Controller (MVC) suddivide le app in tre componenti principali: **M**odello, **V**ista e **C**ontroller. Il pattern MVC consente di creare app più semplici da aggiornare e sottoporre a test rispetto alle app monolitiche tradizionali. Le app basate sul pattern MVC contengono:

* **M**odelli: classi che rappresentano i dati dell'app. Le classi modello usano la logica di convalida per applicare le regole business per tali dati. In genere, gli oggetti del modello recuperano e archiviano lo stato del modello in un database. In questa esercitazione, un modello `Movie` recupera i dati dei film da un database, li fornisce alla vista o li aggiorna. I dati aggiornati vengono scritti in un database.

* **V**iste: componenti che visualizzano l'interfaccia utente dell'app. In genere questa interfaccia utente presenta i dati del modello.

* **C**ontroller: classi che gestiscono le richieste del browser. Recuperano i dati del modello e chiamano i modelli di vista che restituiscono una risposta. In un'applicazione MVC, la vista presenta solo le informazioni; il controller gestisce e risponde all'input dell'utente e alle azioni di interazione. Ad esempio, il controller gestisce i valori dei dati della route e della stringa di query e li passa al modello. Il modello può usare questi valori per eseguire query sul database. Ad esempio, `https://localhost:5001/Home/Privacy` contiene come i dati della route `Home` (il controller) e `Privacy` (il metodo di azione da chiamare sul controller Home). `https://localhost:5001/Movies/Edit/5` è una richiesta di modificare il film con ID = 5 usando il controller di film. I dati della route vengono spiegati in seguito nell'esercitazione.

Il pattern MVC consente di creare app che separano i diversi aspetti dell'app (logica di input, logica di business e logica dell'interfaccia utente), fornendo un accoppiamento libero tra questi elementi. Il pattern specifica il punto in cui deve risiedere ogni tipo di logica nell'app. La logica dell'interfaccia utente risiede nella vista. La logica di input risiede nel controller. La logica di business risiede nel modello. Questa separazione semplifica la complessità in fase di compilazione di un'app perché consente di lavorare su un aspetto dell'implementazione alla volta senza conseguenze sul codice di un altro aspetto. Ad esempio, è possibile usare il codice della vista senza dipendere dal codice della logica di business.

Questi concetti vengono illustrati in questa serie di esercitazioni e descrivono come compilare un'app per i film. Il progetto MVC contiene le cartelle per i *controller* e le *viste*.

## <a name="add-a-controller"></a>Aggiunta di un controller

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* In **Esplora soluzioni**fare clic con il pulsante destro del mouse su **Controller > menu contestuale Aggiungi controller > controller**
  ![](adding-controller/_static/add_controller.png)

* Nella finestra di dialogo **Aggiungi scaffolding**, selezionare **Controller MVC - vuoto**

  ![Aggiungere il controller MVC e assegnarli un nome](adding-controller/_static/ac.png)

* Nella **finestra di dialogo Aggiungi Controller MVC vuoto**, immettere **HelloWorldController** e selezionare **AGGIUNGI**.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Selezionare l'icona **EXPLORER** e quindi fare CTRL+clic (clic con il pulsante destro del mouse) su **Controller > Nuovo File** e assegnare al nuovo file il nome *HelloWorldController.cs*.

  ![Menu di scelta rapida](~/tutorials/first-mvc-app-xplat/adding-controller/_static/new_file.png)

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/visual-studio-mac)

In **Esplora soluzioni** fare clic con il pulsante destro del mouse su **Controller > Aggiungi > Nuovo file**.
![Menu di scelta rapida](~/tutorials/first-mvc-app-mac/adding-controller/_static/add_controller.png)

Selezionare **ASP.NET Core** e **Classe controller MVC**.

Assegnare il nome **HelloWorldController** al controller.

![Aggiungere il controller MVC e assegnarli un nome](~/tutorials/first-mvc-app-mac/adding-controller/_static/ac.png)

---

Sostituire il contenuto di *Controllers/HelloWorldController.cs* con quanto segue:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_1)]

È possibile chiamare ogni metodo `public` in un controller come endpoint HTTP. Nell'esempio precedente entrambi i metodi restituiscono una stringa. Notare i commenti che precedono ogni metodo.

Un endpoint HTTP è un URL definibile come destinazione nell'applicazione Web, ad esempio `https://localhost:5001/HelloWorld`, e combina il protocollo usato (`HTTPS`), il percorso di rete del server Web, tra cui la porta TCP (`localhost:5001`) e l'URI di destinazione (`HelloWorld`).

Il primo commento indica che si tratta di un metodo [HTTP GET](https://www.w3schools.com/tags/ref_httpmethods.asp) che viene richiamato aggiungendo `/HelloWorld/` all'URL di base. Il secondo commento specifica un metodo [HTTP GET](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html) che viene richiamato aggiungendo `/HelloWorld/Welcome/` all'URL. In una fase successiva dell'esercitazione il motore di scaffolding viene usato per generare i metodi `HTTP POST` che aggiornano i dati.

Eseguire l'app in modalità non di debug e aggiungere "HelloWorld" al percorso nella barra degli indirizzi. Il metodo `Index` restituisce una stringa.

![Finestra del browser con una risposta dell'applicazione, This is my default action](~/tutorials/first-mvc-app/adding-controller/_static/hell1.png)

MVC richiama le classi controller, e i metodi di azione in esse contenute, in base all'URL in ingresso. Il valore predefinito della [logica di routing degli URL](xref:mvc/controllers/routing) usata da MVC usa un formato simile al seguente per determinare il codice di richiamare:

`/[Controller]/[ActionName]/[Parameters]`

Il formato per il routing viene impostato nel metodo `Configure` nel file *Startup.cs*.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_1&highlight=5)]

Quando si sceglie l'app e non si forniscono i segmenti di URL, i valori predefiniti sono il controller "Home" e il metodo "Index" specificati nella riga del modello evidenziata sopra.

Il primo segmento di URL determina la classe di controller da eseguire. `localhost:{PORT}/HelloWorld` esegue quindi il mapping alla classe di controller **HelloWorld**. La seconda parte del segmento URL determina il metodo di azione per la classe. Pertanto `localhost:{PORT}/HelloWorld/Index` causa l'esecuzione del metodo `Index` della classe `HelloWorldController`. Si noti che è necessario solo passare a `localhost:{PORT}/HelloWorld` e il metodo `Index` viene chiamato per impostazione predefinita. In questo modo `Index` è il metodo predefinito che verrà chiamato per un controller, se non viene specificato un nome di metodo in modo esplicito. La terza parte del segmento di URL ( `id`) è relativa ai dati di route. I dati della route vengono spiegati in seguito nell'esercitazione.

Passare a `https://localhost:{PORT}/HelloWorld/Welcome`. Il metodo `Welcome` viene eseguito e restituisce la stringa `This is the Welcome action method...`. Per questo URL, il controller è `HelloWorld` e il metodo di azione è `Welcome`. Non è stata ancora usata la parte `[Parameters]` dell'URL.

![Finestra del browser con una risposta dell'applicazione, This is the Welcome action method](~/tutorials/first-mvc-app/adding-controller/_static/welcome.png)

Modificare il codice in modo da passare le informazioni dei parametri dall'URL al controller. Ad esempio: `/HelloWorld/Welcome?name=Rick&numtimes=4`. Modificare il metodo `Welcome` in modo da includere due parametri, come illustrato nel codice seguente.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_2)]

Il codice precedente:

* Usa la funzionalità di parametro facoltativo in C# per indicare che il parametro `numTimes` viene impostato come predefinito su 1, se non viene passato alcun valore. <!-- remove for simplified -->
* Usa `HtmlEncoder.Default.Encode` per proteggere le app da input dannoso, in particolare in JavaScript.
* Usa [stringhe interpolate](/dotnet/articles/csharp/language-reference/keywords/interpolated-strings) in `$"Hello {name}, NumTimes is: {numTimes}"`. <!-- remove for simplified -->

Eseguire l'app e passare a:

   `https://localhost:{PORT}/HelloWorld/Welcome?name=Rick&numtimes=4`

(Sostituire `{PORT}` con il numero di porta.) È possibile provare `name` valori `numtimes` diversi per e nell'URL. Il sistema di [associazione di modelli](xref:mvc/models/model-binding) MVC esegue il mapping dei parametri denominati dalla stringa di query nella barra degli indirizzi ai parametri nel metodo. Per altre informazioni, vedere [Associazione di modelli](xref:mvc/models/model-binding).

![Finestra del browser che mostra una risposta\: dell'applicazione di Hello Rick, NumTimes è 4](~/tutorials/first-mvc-app/adding-controller/_static/rick4.png)

Nell'immagine precedente, il`Parameters`segmento URL ( `name` ) `numTimes` non viene utilizzato, i parametri e vengono passati nella stringa di [query](https://wikipedia.org/wiki/Query_string). Il `?` (punto interrogativo) nell'URL precedente è un separatore e la stringa di query segue. Il `&` carattere separa le coppie campo-valore.

Sostituire il metodo `Welcome` con il codice seguente:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_3)]

Eseguire l'app e immettere l'URL seguente: `https://localhost:{PORT}/HelloWorld/Welcome/3?name=Rick`

Questa volta il terzo segmento di URL corrisponde al parametro di route `id`. Il metodo `Welcome` contiene un parametro `id` che corrisponde al modello di URL nel metodo `MapControllerRoute`. Il carattere finale `?` (in `id?`) indica che il parametro `id` è facoltativo.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_1&highlight=5)]

In questi esempi il controller rappresenta la parte "VC" di MVC, ovvero il ruolo di **v**ista e **c**ontroller. Il controller restituisce direttamente l'HTML. In genere è preferibile che i controller non restituiscano direttamente l'HTML, dal momento che la codifica e la gestione sono molto complesse. In genere usare invece un file del modello di vista Razor separato per generare la risposta HTML. Questa operazione viene eseguita nell'esercitazione successiva.

> [!div class="step-by-step"]
> [Successivo](start-mvc.md)
> [precedente](adding-view.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Il pattern architetturale Model-View-Controller (MVC) suddivide le app in tre componenti principali: **M**odello, **V**ista e **C**ontroller. Il pattern MVC consente di creare app più semplici da aggiornare e sottoporre a test rispetto alle app monolitiche tradizionali. Le app basate sul pattern MVC contengono:

* **M**odelli: classi che rappresentano i dati dell'app. Le classi modello usano la logica di convalida per applicare le regole business per tali dati. In genere, gli oggetti del modello recuperano e archiviano lo stato del modello in un database. In questa esercitazione, un modello `Movie` recupera i dati dei film da un database, li fornisce alla vista o li aggiorna. I dati aggiornati vengono scritti in un database.

* **V**iste: componenti che visualizzano l'interfaccia utente dell'app. In genere questa interfaccia utente presenta i dati del modello.

* **C**ontroller: classi che gestiscono le richieste del browser. Recuperano i dati del modello e chiamano i modelli di vista che restituiscono una risposta. In un'applicazione MVC, la vista presenta solo le informazioni; il controller gestisce e risponde all'input dell'utente e alle azioni di interazione. Ad esempio, il controller gestisce i valori dei dati della route e della stringa di query e li passa al modello. Il modello può usare questi valori per eseguire query sul database. Ad esempio, `https://localhost:5001/Home/About` contiene come i dati della route `Home` (il controller) e `About` (il metodo di azione da chiamare sul controller Home). `https://localhost:5001/Movies/Edit/5` è una richiesta di modificare il film con ID = 5 usando il controller di film. I dati della route vengono spiegati in seguito nell'esercitazione.

Il pattern MVC consente di creare app che separano i diversi aspetti dell'app (logica di input, logica di business e logica dell'interfaccia utente), fornendo un accoppiamento libero tra questi elementi. Il pattern specifica il punto in cui deve risiedere ogni tipo di logica nell'app. La logica dell'interfaccia utente risiede nella vista. La logica di input risiede nel controller. La logica di business risiede nel modello. Questa separazione semplifica la complessità in fase di compilazione di un'app perché consente di lavorare su un aspetto dell'implementazione alla volta senza conseguenze sul codice di un altro aspetto. Ad esempio, è possibile usare il codice della vista senza dipendere dal codice della logica di business.

Questi concetti vengono illustrati in questa serie di esercitazioni e descrivono come compilare un'app per i film. Il progetto MVC contiene le cartelle per i *controller* e le *viste*.

## <a name="add-a-controller"></a>Aggiunta di un controller

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* In **Esplora soluzioni**fare clic con il pulsante destro del mouse su **Controller > menu contestuale Aggiungi controller > controller**
  ![](adding-controller/_static/add_controller.png)

* Nella finestra di dialogo **Aggiungi scaffolding**, selezionare **Controller MVC - vuoto**

  ![Aggiungere il controller MVC e assegnarli un nome](adding-controller/_static/ac.png)

* Nella **finestra di dialogo Aggiungi Controller MVC vuoto**, immettere **HelloWorldController** e selezionare **AGGIUNGI**.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Selezionare l'icona **EXPLORER** e quindi fare CTRL+clic (clic con il pulsante destro del mouse) su **Controller > Nuovo File** e assegnare al nuovo file il nome *HelloWorldController.cs*.

  ![Menu di scelta rapida](~/tutorials/first-mvc-app-xplat/adding-controller/_static/new_file.png)

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/visual-studio-mac)

In **Esplora soluzioni** fare clic con il pulsante destro del mouse su **Controller > Aggiungi > Nuovo file**.
![Menu di scelta rapida](~/tutorials/first-mvc-app-mac/adding-controller/_static/add_controller.png)

Selezionare **ASP.NET Core** e **Classe controller MVC**.

Assegnare il nome **HelloWorldController** al controller.

![Aggiungere il controller MVC e assegnarli un nome](~/tutorials/first-mvc-app-mac/adding-controller/_static/ac.png)

---

Sostituire il contenuto di *Controllers/HelloWorldController.cs* con quanto segue:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_1)]

È possibile chiamare ogni metodo `public` in un controller come endpoint HTTP. Nell'esempio precedente entrambi i metodi restituiscono una stringa. Notare i commenti che precedono ogni metodo.

Un endpoint HTTP è un URL definibile come destinazione nell'applicazione Web, ad esempio `https://localhost:5001/HelloWorld`, e combina il protocollo usato (`HTTPS`), il percorso di rete del server Web, tra cui la porta TCP (`localhost:5001`) e l'URI di destinazione (`HelloWorld`).

Il primo commento indica che si tratta di un metodo [HTTP GET](https://www.w3schools.com/tags/ref_httpmethods.asp) che viene richiamato aggiungendo `/HelloWorld/` all'URL di base. Il secondo commento specifica un metodo [HTTP GET](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html) che viene richiamato aggiungendo `/HelloWorld/Welcome/` all'URL. In una fase successiva dell'esercitazione il motore di scaffolding viene usato per generare i metodi `HTTP POST` che aggiornano i dati.

Eseguire l'app in modalità non di debug e aggiungere "HelloWorld" al percorso nella barra degli indirizzi. Il metodo `Index` restituisce una stringa.

![Finestra del browser con una risposta dell'applicazione, This is my default action](~/tutorials/first-mvc-app/adding-controller/_static/hell1.png)

MVC richiama le classi controller, e i metodi di azione in esse contenute, in base all'URL in ingresso. Il valore predefinito della [logica di routing degli URL](xref:mvc/controllers/routing) usata da MVC usa un formato simile al seguente per determinare il codice di richiamare:

`/[Controller]/[ActionName]/[Parameters]`

Il formato per il routing viene impostato nel metodo `Configure` nel file *Startup.cs*.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Startup.cs?name=snippet_1&highlight=5)]

<!-- 
Add link to explain lambda.
Remove link for simplified tutorial.
-->

Quando si sceglie l'app e non si forniscono i segmenti di URL, i valori predefiniti sono il controller "Home" e il metodo "Index" specificati nella riga del modello evidenziata sopra.

Il primo segmento di URL determina la classe di controller da eseguire. `localhost:{PORT}/HelloWorld` esegue quindi il mapping alla classe `HelloWorldController`. La seconda parte del segmento URL determina il metodo di azione per la classe. Pertanto `localhost:{PORT}/HelloWorld/Index` causa l'esecuzione del metodo `Index` della classe `HelloWorldController`. Si noti che è necessario solo passare a `localhost:{PORT}/HelloWorld` e il metodo `Index` viene chiamato per impostazione predefinita. In questo modo `Index` è il metodo predefinito che verrà chiamato per un controller, se non viene specificato un nome di metodo in modo esplicito. La terza parte del segmento di URL ( `id`) è relativa ai dati di route. I dati della route vengono spiegati in seguito nell'esercitazione.

Passare a `https://localhost:{PORT}/HelloWorld/Welcome`. Il metodo `Welcome` viene eseguito e restituisce la stringa `This is the Welcome action method...`. Per questo URL, il controller è `HelloWorld` e il metodo di azione è `Welcome`. Non è stata ancora usata la parte `[Parameters]` dell'URL.

![Finestra del browser con una risposta dell'applicazione, This is the Welcome action method](~/tutorials/first-mvc-app/adding-controller/_static/welcome.png)

Modificare il codice in modo da passare le informazioni dei parametri dall'URL al controller. Ad esempio: `/HelloWorld/Welcome?name=Rick&numtimes=4`. Modificare il metodo `Welcome` in modo da includere due parametri, come illustrato nel codice seguente.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_2)]

Il codice precedente:

* Usa la funzionalità di parametro facoltativo in C# per indicare che il parametro `numTimes` viene impostato come predefinito su 1, se non viene passato alcun valore. <!-- remove for simplified -->
* Usa `HtmlEncoder.Default.Encode` per proteggere le app da input dannoso, in particolare in JavaScript.
* Usa [stringhe interpolate](/dotnet/articles/csharp/language-reference/keywords/interpolated-strings) in `$"Hello {name}, NumTimes is: {numTimes}"`. <!-- remove for simplified -->

Eseguire l'app e passare a:

   `https://localhost:{PORT}/HelloWorld/Welcome?name=Rick&numtimes=4`

(Sostituire `{PORT}` con il numero di porta.) È possibile provare `name` valori `numtimes` diversi per e nell'URL. Il sistema di [associazione di modelli](xref:mvc/models/model-binding) MVC esegue il mapping dei parametri denominati dalla stringa di query nella barra degli indirizzi ai parametri nel metodo. Per altre informazioni, vedere [Associazione di modelli](xref:mvc/models/model-binding).

![Finestra del browser che mostra una risposta\: dell'applicazione di Hello Rick, NumTimes è 4](~/tutorials/first-mvc-app/adding-controller/_static/rick4.png)

Nell'immagine precedente, il`Parameters`segmento URL ( `name` ) `numTimes` non viene utilizzato, i parametri e vengono passati nella stringa di [query](https://wikipedia.org/wiki/Query_string). Il `?` (punto interrogativo) nell'URL precedente è un separatore e la stringa di query segue. Il `&` carattere separa le coppie campo-valore.

Sostituire il metodo `Welcome` con il codice seguente:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_3)]

Eseguire l'app e immettere l'URL seguente: `https://localhost:{PORT}/HelloWorld/Welcome/3?name=Rick`

Questa volta il terzo segmento di URL corrisponde al parametro di route `id`. Il metodo `Welcome` contiene un parametro `id` che corrisponde al modello di URL nel metodo `MapRoute`. Il carattere finale `?` (in `id?`) indica che il parametro `id` è facoltativo.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Startup.cs?name=snippet_1&highlight=5)]

In questi esempi il controller rappresenta la parte "VC" di MVC, ovvero il ruolo di vista e controller. Il controller restituisce direttamente l'HTML. In genere è preferibile che i controller non restituiscano direttamente l'HTML, dal momento che la codifica e la gestione sono molto complesse. In genere usare invece un file del modello di vista Razor separato per generare la risposta HTML. Questa operazione viene eseguita nell'esercitazione successiva.

> [!div class="step-by-step"]
> [Successivo](start-mvc.md)
> [precedente](adding-view.md)

::: moniker-end
