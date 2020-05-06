---
title: Eseguire la migrazione da ASP.NET MVC ad ASP.NET Core MVC
author: ardalis
description: Informazioni su come iniziare a migrare un progetto MVC ASP.NET a ASP.NET Core MVC.
ms.author: riande
ms.date: 04/06/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: migration/mvc
ms.openlocfilehash: 59a10c002958e5f719dbd59686f21df69da5f43e
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82777046"
---
# <a name="migrate-from-aspnet-mvc-to-aspnet-core-mvc"></a>Eseguire la migrazione da ASP.NET MVC ad ASP.NET Core MVC

Di [Rick Anderson](https://twitter.com/RickAndMSFT), [Daniel Roth](https://github.com/danroth27), [Steve Smith](https://ardalis.com/)e [Scott Addie](https://scottaddie.com)

Questo articolo illustra come iniziare a migrare un progetto MVC ASP.NET a [ASP.NET Core MVC](../mvc/overview.md). Nel processo, vengono evidenziati molti degli elementi che sono stati modificati rispetto a ASP.NET MVC. La migrazione da ASP.NET MVC è un processo in più passaggi e in questo articolo viene illustrata la configurazione iniziale, i controller e le visualizzazioni di base, il contenuto statico e le dipendenze lato client. Altri articoli riguardano la migrazione del codice di configurazione e di identità trovato in molti progetti MVC ASP.NET.

> [!NOTE]
> I numeri di versione negli esempi potrebbero non essere aggiornati. Potrebbe essere necessario aggiornare i progetti di conseguenza.

## <a name="create-the-starter-aspnet-mvc-project"></a>Creare il progetto MVC Starter ASP.NET

Per illustrare l'aggiornamento, si inizierà con la creazione di un'app MVC ASP.NET. Crearlo con il nome *app Web 1* in modo che lo spazio dei nomi corrisponda al progetto ASP.NET Core creato nel passaggio successivo.

![Finestra di dialogo Nuovo progetto di Visual Studio](mvc/_static/new-project.png)

![Finestra di dialogo nuova applicazione Web: modello di progetto MVC selezionato nel pannello modelli ASP.NET](mvc/_static/new-project-select-mvc-template.png)

*Facoltativo:* Modificare il nome della soluzione da *app Web 1* a *Mvc5*. In Visual Studio viene visualizzato il nuovo nome della soluzione (*Mvc5*), che rende più semplice la creazione di questo progetto dal progetto successivo.

## <a name="create-the-aspnet-core-project"></a>Creare il progetto di ASP.NET Core

Creare una nuova app Web ASP.NET Core *vuota* con lo stesso nome del progetto precedente (*app Web 1*) in modo che gli spazi dei nomi nei due progetti corrispondano. La presenza dello stesso spazio dei nomi rende più semplice la copia di codice tra i due progetti. È necessario creare questo progetto in una directory diversa da quella del progetto precedente per usare lo stesso nome.

![Finestra di dialogo Nuovo progetto](mvc/_static/new_core.png)

![Finestra di dialogo nuova applicazione Web ASP.NET: modello di progetto vuoto selezionato nel pannello modelli ASP.NET Core](mvc/_static/new-project-select-empty-aspnet5-template.png)

* *Facoltativo:* Creare una nuova app ASP.NET Core usando il modello di progetto *applicazione Web* . Denominare il progetto *app Web 1*e selezionare un'opzione di autenticazione per **singoli account utente**. Rinominare l'app in *FullAspNetCore*. La creazione di questo progetto consente di risparmiare tempo nella conversione. È possibile esaminare il codice generato dal modello per visualizzare il risultato finale o copiare il codice nel progetto di conversione. Questa operazione è utile anche quando ci si blocca in un passaggio di conversione da confrontare con il progetto generato dal modello.

## <a name="configure-the-site-to-use-mvc"></a>Configurare il sito per l'uso di MVC

::: moniker range=">= aspnetcore-2.1"

* Quando la destinazione è .NET Core, per impostazione predefinita viene fatto riferimento al [metapacchetto Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) . Questo pacchetto contiene i pacchetti usati comunemente dalle app MVC. Se la destinazione è .NET Framework, i riferimenti ai pacchetti devono essere elencati singolarmente nel file di progetto.

::: moniker-end

::: moniker range="= aspnetcore-2.0"

* Quando la destinazione è .NET Core, per impostazione predefinita viene fatto riferimento al [metapacchetto Microsoft. AspNetCore. All](xref:fundamentals/metapackage) . Questo pacchetto contiene pacchetti usati comunemente da app MVC. Se la destinazione è .NET Framework, i riferimenti ai pacchetti devono essere elencati singolarmente nel file di progetto.

::: moniker-end

::: moniker range="< aspnetcore-2.0"

* Quando la destinazione è .NET Core o .NET Framework, i pacchetti usati comunemente da app MVC vengono elencati singolarmente nel file di progetto.

::: moniker-end

`Microsoft.AspNetCore.Mvc`è il Framework di MVC ASP.NET Core. `Microsoft.AspNetCore.StaticFiles`è il gestore di file statici. Il runtime di ASP.NET Core è modulare ed è necessario acconsentire esplicitamente a gestire i file statici (vedere [file statici](xref:fundamentals/static-files)).

* Aprire il file *Startup.cs* e modificare il codice in modo che corrisponda a quanto segue:

  [!code-csharp[](mvc/sample/Startup.cs?highlight=13,26-31)]

Il `UseStaticFiles` metodo di estensione aggiunge il gestore di file statici. Come indicato in precedenza, il runtime di ASP.NET è modulare ed è necessario acconsentire esplicitamente a gestire i file statici. Il `UseMvc` metodo di estensione aggiunge il routing. Per ulteriori informazioni, vedere Startup e [routing](xref:fundamentals/routing) [dell'applicazione](xref:fundamentals/startup) .

## <a name="add-a-controller-and-view"></a>Aggiungere un controller e una visualizzazione

In questa sezione si aggiungeranno un controller e una visualizzazione minimi per fungere da segnaposto per il controller e le visualizzazioni MVC ASP.NET di cui verrà eseguita la migrazione nella sezione successiva.

* Aggiungere una cartella *Controllers* .

* Aggiungere una **classe controller** denominata *HomeController.cs* alla cartella *Controllers* .

![Finestra di dialogo Aggiungi nuovo elemento](mvc/_static/add_mvc_ctl.png)

* Aggiungere una cartella *views* .

* Aggiungere una cartella *views/Home* .

* Aggiungere una ** Razor vista** denominata *index. cshtml* alla cartella *views/Home* .

![Finestra di dialogo Aggiungi nuovo elemento](mvc/_static/view.png)

La struttura del progetto è illustrata di seguito:

![Esplora soluzioni la visualizzazione di file e cartelle di app Web 1](mvc/_static/project-structure-controller-view.png)

Sostituire il contenuto del file *views/Home/index. cshtml* con il codice seguente:

```html
<h1>Hello world!</h1>
```

Eseguire l'app.

![App Web aperta in Microsoft Edge](mvc/_static/hello-world.png)

Per ulteriori informazioni, vedere [controller](xref:mvc/controllers/actions) e [visualizzazioni](xref:mvc/views/overview) .

Ora che è disponibile un progetto di ASP.NET Core di lavoro minimo, è possibile avviare la migrazione della funzionalità dal progetto MVC ASP.NET. È necessario spostare gli elementi seguenti:

* contenuto lato client (CSS, tipi di carattere e script)

* controllers

* Viste

* modelli

* Bundle

* filters

* Accesso/uscita Identity (operazione eseguita nell'esercitazione successiva).

## <a name="controllers-and-views"></a>Controller e visualizzazioni

* Copiare tutti i metodi da ASP.NET MVC `HomeController` al nuovo. `HomeController` Si noti che in ASP.NET MVC il tipo restituito del metodo di azione del controller del modello predefinito è [ActionResult](https://msdn.microsoft.com/library/system.web.mvc.actionresult(v=vs.118).aspx); in ASP.NET Core MVC i metodi di azione restituiscono `IActionResult` invece. `ActionResult`implementa `IActionResult`, quindi non è necessario modificare il tipo restituito dei metodi di azione.

* Copiare i file *di visualizzazione About. cshtml*, *Contact. cshtml*e *index. cshtml* Razor dal progetto MVC ASP.NET al progetto ASP.NET Core.

* Eseguire l'app ASP.NET Core e testare ogni metodo. Non è ancora stata eseguita la migrazione del file di layout o degli stili, quindi le visualizzazioni sottoposte a rendering contengono solo il contenuto dei file di visualizzazione. Il file di layout non include collegamenti generati per le `About` visualizzazioni `Contact` e, quindi è necessario richiamarli dal browser (sostituire **4492** con il numero di porta usato nel progetto).

  * `http://localhost:4492/home/about`

  * `http://localhost:4492/home/contact`

![Pagina contatto](mvc/_static/contact-page.png)

Si noti la mancanza di stili e voci di menu. Questo problema verrà corretto nella prossima sezione.

## <a name="static-content"></a>Contenuto statico

Nelle versioni precedenti di ASP.NET MVC, il contenuto statico era ospitato dalla radice del progetto Web ed era combinato con i file lato server. In ASP.NET Core, il contenuto statico è ospitato nella cartella *wwwroot* È possibile copiare il contenuto statico dall'app ASP.NET MVC precedente alla cartella *wwwroot* nel progetto ASP.NET Core. In questo esempio di conversione:

* Copiare il file *favicon. ico* dal progetto MVC precedente alla cartella *wwwroot* nel progetto ASP.NET Core.

Il vecchio progetto MVC ASP.NET usa [bootstrap](https://getbootstrap.com/) per lo stile e archivia i file bootstrap nelle cartelle *Content* e *Scripts* . Il modello, che ha generato il vecchio progetto MVC ASP.NET, fa riferimento a bootstrap nel file di layout (*Views/Shared/_Layout. cshtml*). È possibile copiare i file *bootstrap. js* e *bootstrap. CSS* dal progetto MVC ASP.NET alla cartella *wwwroot* del nuovo progetto. Al contrario, verrà aggiunto il supporto per bootstrap (e altre librerie lato client) usando CDNs nella sezione successiva.

## <a name="migrate-the-layout-file"></a>Eseguire la migrazione del file di layout

* Copiare il file *_ViewStart. cshtml* dalla cartella *views* del progetto MVC ASP.NET precedente nella cartella *views* del progetto ASP.NET Core. Il file *_ViewStart. cshtml* non è stato modificato in ASP.NET Core MVC.

* Creare una cartella *Views/Shared* .

* *Facoltativo:* Copiare *_ViewImports. cshtml* dalla cartella *views* del progetto MVC *FullAspNetCore* nella cartella *views* del progetto ASP.NET Core. Rimuovere qualsiasi dichiarazione dello spazio dei nomi nel file *_ViewImports. cshtml* . Il file *_ViewImports. cshtml* fornisce gli spazi dei nomi per tutti i file di visualizzazione e porta gli [Helper Tag](xref:mvc/views/tag-helpers/intro). Gli helper tag vengono usati nel nuovo file di layout. Il file *_ViewImports. cshtml* è nuovo per ASP.NET Core.

* Copiare il file *_Layout. cshtml* dalla cartella *Views/Shared* del progetto MVC ASP.NET precedente nella cartella *views* /Shared del progetto ASP.NET Core.

Aprire *_Layout file cshtml* e apportare le modifiche seguenti (il codice completato è riportato di seguito):

* Sostituire `@Styles.Render("~/Content/css")` con un `<link>` elemento per caricare *bootstrap. CSS* (vedere di seguito).

* Rimuovere `@Scripts.Render("~/bundles/modernizr")`.

* Imposta come commento `@Html.Partial("_LoginPartial")` la riga, che racchiude la `@*...*@`riga con. Per ulteriori informazioni, vedere la pagina relativa [alla migrazione dell'autenticazione Identity e alla ASP.NET Core](xref:migration/identity)

* Sostituire `@Scripts.Render("~/bundles/jquery")` con un `<script>` elemento (vedere di seguito).

* Sostituire `@Scripts.Render("~/bundles/bootstrap")` con un `<script>` elemento (vedere di seguito).

Markup sostitutivo per l'inclusione CSS bootstrap:

```html
<link rel="stylesheet"
    href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css"
    integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u"
    crossorigin="anonymous">
```

Markup sostitutivo per jQuery e bootstrap JavaScript inclusion:

```html
<script src="https://code.jquery.com/jquery-3.3.1.min.js"></script>
<script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js"
    integrity="sha384-Tc5IQib027qvyjSMfHjOMaLkfuWVxZxUPnCJA7l2mCWNIpG9mGCD8wGNIcPD7Txa" crossorigin="anonymous"></script>
```

Il file *_Layout. cshtml* aggiornato è illustrato di seguito:

[!code-cshtml[](mvc/sample/Views/Shared/_Layout.cshtml?highlight=7-10,29,41-44)]

Visualizzare il sito nel browser. A questo punto, dovrebbe essere caricato correttamente con gli stili previsti.

* *Facoltativo:* Potrebbe essere necessario provare a utilizzare il nuovo file di layout. Per questo progetto è possibile copiare il file di layout dal progetto *FullAspNetCore* . Il nuovo file di layout utilizza gli [Helper Tag](xref:mvc/views/tag-helpers/intro) e presenta altri miglioramenti.

## <a name="configure-bundling-and-minification"></a>Configurare la creazione di bundle e minification

Per informazioni su come configurare la creazione di bundle e minification, vedere [aggregazione e minification](../client-side/bundling-and-minification.md).

## <a name="solve-http-500-errors"></a>Risolvere gli errori HTTP 500

Ci sono molti problemi che possono causare un messaggio di errore HTTP 500 che non contiene informazioni sull'origine del problema. Se, ad esempio, il file *views/_ViewImports. cshtml* contiene uno spazio dei nomi che non esiste nel progetto, verrà ricevuto un errore HTTP 500. Per impostazione predefinita, nelle app ASP.NET Core `UseDeveloperExceptionPage` , l'estensione viene aggiunta `IApplicationBuilder` a ed eseguita quando la configurazione è in *fase di sviluppo*. Questa operazione è descritta in dettaglio nel codice seguente:

[!code-csharp[](mvc/sample/Startup.cs?highlight=19-22)]

ASP.NET Core converte le eccezioni non gestite in un'app Web in risposte di errore HTTP 500. In genere, i dettagli dell'errore non sono inclusi in queste risposte per impedire la divulgazione di informazioni potenzialmente riservate sul server. Per ulteriori informazioni, vedere **utilizzo della pagina delle eccezioni per sviluppatori** in [Gestisci errori](../fundamentals/error-handling.md) .

## <a name="additional-resources"></a>Risorse aggiuntive

* <xref:blazor/index>
* <xref:mvc/views/tag-helpers/intro>
