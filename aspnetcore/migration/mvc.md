---
title: Informazioni su come eseguire la migrazione da ASP.NET MVC a ASP.NET Core MVC
author: wadepickett
description: Informazioni su come iniziare a migrare un progetto MVC ASP.NET a ASP.NET Core MVC.
ms.author: wpickett
ms.date: 06/18/2020
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
uid: migration/mvc
ms.openlocfilehash: 17f2a2532c58c3796835328260231d63f8fb2e40
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/08/2020
ms.locfileid: "88015049"
---
# <a name="migrate-from-aspnet-mvc-to-aspnet-core-mvc"></a>Eseguire la migrazione da ASP.NET MVC ad ASP.NET Core MVC

::: moniker range=">= aspnetcore-3.0"

Questo articolo illustra come iniziare la migrazione di un progetto MVC ASP.NET a [ASP.NET Core MVC](xref:mvc/overview). Nel processo, vengono evidenziate le modifiche correlate rispetto a ASP.NET MVC.

La migrazione da ASP.NET MVC è un processo in più passaggi. Questo articolo riguarda:

* Configurazione iniziale.
* Controller e visualizzazioni di base.
* Contenuto statico.
* Dipendenze lato client.

Per la migrazione della configurazione e del Identity codice, vedere eseguire la migrazione della [configurazione per ASP.NET Core](xref:migration/configuration) ed [eseguire la migrazione dell'autenticazione e Identity di ASP.NET Core](xref:migration/identity).

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [prerequisites](../includes/net-core-prereqs-vs-3.1.md)]

## <a name="create-the-starter-aspnet-mvc-project"></a>Creare il progetto MVC Starter ASP.NET

Creare un esempio di progetto MVC ASP.NET in Visual Studio per eseguire la migrazione:

1. Scegliere **Nuovo** > **Progetto** dal menu **File**.
1. Selezionare **applicazione Web ASP.NET (.NET Framework)** e quindi fare clic su **Avanti**.
1. Denominare il progetto *app Web 1* in modo che lo spazio dei nomi corrisponda al progetto ASP.NET Core creato nel passaggio successivo. Selezionare **Crea**.
1. Selezionare **MVC**, quindi fare clic su **Crea**.

## <a name="create-the-aspnet-core-project"></a>Creare il progetto di ASP.NET Core

Crea una nuova soluzione con un nuovo progetto di ASP.NET Core di cui eseguire la migrazione:

1. Avviare una seconda istanza di Visual Studio.
1. Scegliere **Nuovo** > **Progetto** dal menu **File**.
1. Selezionare **applicazione web ASP.NET Web Core** e quindi fare clic su **Avanti**.
1. Nella finestra di dialogo **Configura nuovo progetto** assegnare al progetto il nome *app Web 1*.
1. Impostare il percorso su una directory diversa da quella del progetto precedente per usare lo stesso nome di progetto. L'utilizzo dello stesso spazio dei nomi rende più semplice la copia di codice tra i due progetti. Selezionare **Crea**.
1. Nella finestra di dialogo **Crea una nuova applicazione Web ASP.NET Core** verificare che siano selezionati **.net Core** e **ASP.NET Core 3,1** . Selezionare il modello di progetto **applicazione Web (modello-vista-controller)** e selezionare **Crea**.

## <a name="configure-the-aspnet-core-site-to-use-mvc"></a>Configurare il sito di ASP.NET Core per l'uso di MVC

Nei progetti ASP.NET Core 3,0 e versioni successive .NET Framework non è più un Framework di destinazione supportato. Il progetto deve avere come destinazione .NET Core. Il ASP.NET Core Framework condiviso, che include MVC, fa parte dell'installazione del runtime di .NET Core. Al Framework condiviso viene fatto automaticamente riferimento quando si usa l' `Microsoft.NET.Sdk.Web` SDK nel file di progetto:

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

Per ulteriori informazioni, vedere [riferimento a Framework](xref:migration/22-to-30#framework-reference).

In ASP.NET Core la `Startup` classe:

* Sostituisce *Global. asax*.
* Gestisce tutte le attività di avvio dell'app.

Per altre informazioni, vedere <xref:fundamentals/startup>.

Nel progetto ASP.NET Core aprire il file *Startup.cs* :

[!code-csharp[](mvc/samples/3.x/Startup.cs?highlight=13,30,32&name=snippet)]

ASP.NET Core app devono acconsentire esplicitamente alle funzionalità del Framework con middleware. Il codice generato dal modello precedente aggiunge i servizi e il middleware seguenti:

* Il <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllersWithViews%2A> metodo di estensione registra il supporto del servizio MVC per i controller, le funzionalità correlate all'API e le visualizzazioni. Per ulteriori informazioni sulle opzioni di registrazione del servizio MVC, vedere la pagina relativa alla [registrazione del servizio MVC](xref:migration/22-to-30#mvc-service-registration)
* Il <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> metodo di estensione aggiunge il gestore di file statici `Microsoft.AspNetCore.StaticFiles` . Il `UseStaticFiles` metodo di estensione deve essere chiamato prima di `UseRouting` . Per altre informazioni, vedere <xref:fundamentals/static-files>.
* Il <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting%2A> metodo di estensione aggiunge il routing. Per altre informazioni, vedere <xref:fundamentals/routing>.

Questa configurazione esistente include gli elementi necessari per eseguire la migrazione del progetto ASP.NET MVC di esempio. Per ulteriori informazioni sulle opzioni del middleware ASP.NET Core, vedere <xref:fundamentals/startup> .

## <a name="migrate-controllers-and-views"></a>Migrare controller e visualizzazioni

Nel progetto ASP.NET Core verranno aggiunte una nuova classe controller vuota e una classe di visualizzazione per fungere da segnaposto usando gli stessi nomi delle classi controller e View in qualsiasi progetto MVC ASP.NET da cui eseguire la migrazione.

Il progetto *app web 1* ASP.NET Core include già un controller e una visualizzazione di esempio minimi con lo stesso nome del progetto MVC ASP.NET. Quindi, verranno usati come segnaposto per il controller e le visualizzazioni MVC ASP.NET di cui eseguire la migrazione dal progetto ASP.NET MVC *app Web 1* .

1. Copiare i metodi da ASP.NET MVC `HomeController` per sostituire i nuovi metodi di ASP.NET Core `HomeController` . Non è necessario modificare il tipo restituito dei metodi di azione. Il tipo restituito del metodo di azione del controller del modello predefinito MVC ASP.NET è [ActionResult](https://msdn.microsoft.com/library/system.web.mvc.actionresult(v=vs.118).aspx); in ASP.NET Core MVC i metodi di azione restituiscono `IActionResult` invece. `ActionResult` implementa `IActionResult`.
1. Nel progetto ASP.NET Core fare clic con il pulsante destro del mouse sulla directory *views/Home* e scegliere **Aggiungi** > **elemento esistente**.
1. Nella finestra di dialogo **Aggiungi elemento esistente** passare alla *Home* directory ASP.NET MVC *app Web 1* Project.
1. Selezionare i file *di visualizzazione About. cshtml*, *Contact. cshtml*e *index. cshtml* Razor , quindi selezionare **Aggiungi**, sostituendo i file esistenti.

Per altre informazioni, vedere <xref:mvc/controllers/actions> e <xref:mvc/views/overview>.

## <a name="test-each-method"></a>Testare ogni metodo

Ogni endpoint controller può essere testato, tuttavia, il layout e gli stili vengono trattati più avanti nel documento.

1. Eseguire l'app ASP.NET Core.
1. Richiamare le visualizzazioni sottoposte a rendering dal browser nell'app ASP.NET Core in esecuzione sostituendo il numero di porta corrente con il numero di porta usato nel progetto ASP.NET Core. Ad esempio, `https://localhost:44375/home/about`

## <a name="migrate-static-content"></a>Migrare contenuto statico

In ASP.NET MVC 5 e versioni precedenti, il contenuto statico era ospitato dalla directory radice del progetto Web ed era combinato con i file lato server. In ASP.NET Core i file statici vengono archiviati nella directory [radice Web](xref:fundamentals/index#web-root) del progetto. La directory predefinita è *{Content root}/wwwroot*, ma può essere modificata. Per altre informazioni, vedere [File statici in ASP.NET Core](xref:fundamentals/static-files#serve-static-files).

Copiare il contenuto statico dal progetto ASP.NET MVC *app Web 1* alla directory *wwwroot* nel progetto ASP.NET Core *app Web 1* :

1. Nel progetto ASP.NET Core fare clic con il pulsante destro del mouse sulla directory *wwwroot* , quindi scegliere **Aggiungi** > **elemento esistente**.
1. Nella finestra di dialogo **Aggiungi elemento esistente** passare al progetto ASP.NET MVC *app Web 1* .
1. Selezionare il file *favicon. ico* , quindi selezionare **Aggiungi**, sostituendo il file esistente.

## <a name="migrate-the-layout-files"></a>Migrare i file di layout

Copiare i file di layout del progetto MVC ASP.NET nel progetto ASP.NET Core:

1. Nel progetto ASP.NET Core fare clic con il pulsante destro del mouse sulla directory *views* , quindi scegliere **Aggiungi** > **elemento esistente**.
1. Nella finestra di dialogo **Aggiungi elemento esistente** passare alla directory *Views* del progetto ASP.NET MVC *app Web 1* .
1. Selezionare il file *_ViewStart. cshtml,* quindi selezionare **Aggiungi**.

Copiare i file di layout condiviso del progetto MVC ASP.NET nel progetto ASP.NET Core:

1. Nel progetto ASP.NET Core fare clic con il pulsante destro del mouse sulla directory *Views/Shared* e scegliere **Aggiungi** > **elemento esistente**.
1. Nella finestra di dialogo **Aggiungi elemento esistente** passare alla directory *viste/condivise* del progetto ASP.NET MVC *app Web 1* .
1. Selezionare il file *_Layout. cshtml* , quindi selezionare **Aggiungi**, sostituendo il file esistente.

Nel progetto ASP.NET Core aprire il file *_Layout. cshtml* . Apportare le seguenti modifiche in modo che corrispondano al codice completato riportato di seguito:

Aggiornare l'inclusione CSS bootstrap in modo che corrisponda al codice completato riportato di seguito:

1. Sostituire `@Styles.Render("~/Content/css")` con un `<link>` elemento per caricare *bootstrap. CSS* (vedere di seguito).
1. Rimuovere `@Scripts.Render("~/bundles/modernizr")`.

Markup di sostituzione completato per l'inclusione CSS bootstrap:

```html
<link rel="stylesheet"
    href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css"
    integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u"
    crossorigin="anonymous">
```

Aggiornare l'inclusione di JavaScript jQuery e bootstrap in modo che corrisponda al codice completato riportato di seguito:

1. Sostituire `@Scripts.Render("~/bundles/jquery")` con un `<script>` elemento (vedere di seguito).
1. Sostituire `@Scripts.Render("~/bundles/bootstrap")` con un `<script>` elemento (vedere di seguito).

Markup di sostituzione completato per l'inclusione di jQuery e bootstrap JavaScript:

```html
<script src="https://code.jquery.com/jquery-3.3.1.min.js"></script>
<script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js"
    integrity="sha384-Tc5IQib027qvyjSMfHjOMaLkfuWVxZxUPnCJA7l2mCWNIpG9mGCD8wGNIcPD7Txa" crossorigin="anonymous"></script>
```

Il file *_Layout. cshtml* aggiornato è illustrato di seguito:

[!code-cshtml[](mvc/samples/3.x/Views/Shared/_Layout.cshtml?highlight=7-10,40-42)]

Visualizzare il sito nel browser. Il rendering dovrebbe essere eseguito con gli stili previsti sul posto.

## <a name="configure-bundling-and-minification"></a>Configurare la creazione di bundle e minification

ASP.NET Core è compatibile con diverse soluzioni open source per la creazione di bundle e minification, ad esempio [Weboptimizer](https://github.com/ligershark/WebOptimizer) e altre librerie simili. ASP.NET Core non fornisce una soluzione nativa per la creazione di bundle e minification. Per informazioni sulla configurazione della creazione di bundle e minification, vedere [bundleing and minification](xref:client-side/bundling-and-minification).

## <a name="solve-http-500-errors"></a>Risolvere gli errori HTTP 500

Ci sono molti problemi che possono causare un messaggio di errore HTTP 500 che non contiene informazioni sull'origine del problema. Se, ad esempio, il file *views/_ViewImports. cshtml* contiene uno spazio dei nomi che non esiste nel progetto, viene generato un errore HTTP 500. Per impostazione predefinita, nelle app ASP.NET Core, l' `UseDeveloperExceptionPage` estensione viene aggiunta a `IApplicationBuilder` ed eseguita quando l'ambiente è in *fase di sviluppo*. Questa operazione è descritta in dettaglio nel codice seguente:

[!code-csharp[](mvc/samples/3.x/Startup.cs?highlight=17-21&name=snippet)]

ASP.NET Core converte le eccezioni non gestite in risposte di errore HTTP 500. In genere, i dettagli dell'errore non sono inclusi in queste risposte per impedire la divulgazione di informazioni potenzialmente riservate sul server. Per ulteriori informazioni, vedere [pagina delle eccezioni per sviluppatori](xref:fundamentals/error-handling#developer-exception-page).

## <a name="next-steps"></a>Passaggi successivi

* <xref:migration/identity>

## <a name="additional-resources"></a>Risorse aggiuntive

* <xref:blazor/index>
* <xref:mvc/views/tag-helpers/intro>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

Questo articolo illustra come avviare la migrazione di un progetto MVC ASP.NET a [ASP.NET Core mvc](xref:mvc/overview) 2,2. Nel processo, vengono evidenziati molti degli elementi che sono stati modificati rispetto a ASP.NET MVC. La migrazione da ASP.NET MVC è un processo in più passaggi. Questo articolo riguarda:

* Configurazione iniziale
* Controller e visualizzazioni di base
* Contenuto statico
* Dipendenze lato client.

Per la migrazione della configurazione e del Identity codice, vedere <xref:migration/configuration> e <xref:migration/identity> .

> [!NOTE]
> I numeri di versione negli esempi potrebbero non essere aggiornati, aggiornare i progetti di conseguenza.

## <a name="create-the-starter-aspnet-mvc-project"></a>Creare il progetto MVC Starter ASP.NET

Per illustrare l'aggiornamento, si inizierà con la creazione di un'app MVC ASP.NET. Crearlo con il nome *app Web 1* in modo che lo spazio dei nomi corrisponda al progetto ASP.NET Core creato nel passaggio successivo.

![Finestra di dialogo Nuovo progetto di Visual Studio](mvc/_static/new-project.png)

![Finestra di dialogo nuova applicazione Web: modello di progetto MVC selezionato nel pannello modelli ASP.NET](mvc/_static/new-project-select-mvc-template.png)

*Facoltativo:* Modificare il nome della soluzione da *app Web 1* a *Mvc5*. In Visual Studio viene visualizzato il nuovo nome della soluzione (*Mvc5*), che rende più semplice la creazione di questo progetto dal progetto successivo.

## <a name="create-the-aspnet-core-project"></a>Creare il progetto di ASP.NET Core

Creare una nuova app Web ASP.NET Core *vuota* con lo stesso nome del progetto precedente (*app Web 1*) in modo che gli spazi dei nomi nei due progetti corrispondano. La presenza dello stesso spazio dei nomi rende più semplice la copia di codice tra i due progetti. Creare il progetto in una directory diversa da quella del progetto precedente per usare lo stesso nome.

![Finestra di dialogo Nuovo progetto](mvc/_static/new_core.png)

![Finestra di dialogo nuova applicazione Web ASP.NET: modello di progetto vuoto selezionato nel pannello modelli ASP.NET Core](mvc/_static/new-project-select-empty-aspnet5-template.png)

* *Facoltativo:* Creare una nuova app ASP.NET Core usando il modello di progetto *applicazione Web* . Denominare il progetto *app Web 1*e selezionare un'opzione di autenticazione per **singoli account utente**. Rinominare l'app in *FullAspNetCore*. La creazione di questo progetto consente di risparmiare tempo nella conversione. Il risultato finale può essere visualizzato nel codice generato dal modello, il codice può essere copiato nel progetto di conversione o confrontato con il progetto generato dal modello.

## <a name="configure-the-site-to-use-mvc"></a>Configurare il sito per l'uso di MVC

* Quando la destinazione è .NET Core, per impostazione predefinita viene fatto riferimento al [metapacchetto Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) . Questo pacchetto contiene i pacchetti usati comunemente dalle app MVC. Se la destinazione è .NET Framework, i riferimenti ai pacchetti devono essere elencati singolarmente nel file di progetto.

`Microsoft.AspNetCore.Mvc`è il Framework di MVC ASP.NET Core. `Microsoft.AspNetCore.StaticFiles`è il gestore di file statici. ASP.NET Core le app scelgono esplicitamente il middleware, ad esempio per la conservazione dei file statici. Per altre informazioni, vedere [File statici](xref:fundamentals/static-files).

* Aprire il file *Startup.cs* e modificare il codice in modo che corrisponda a quanto segue:

[!code-csharp[](mvc/samples/2.x/Startup.cs?highlight=7,20-25&name=snippet)]

Il <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*> metodo di estensione aggiunge il gestore di file statici. Per ulteriori informazioni, vedere Startup e [routing](xref:fundamentals/routing) [dell'applicazione](xref:fundamentals/startup) .

## <a name="add-a-controller-and-view"></a>Aggiungere un controller e una visualizzazione

In questa sezione vengono aggiunti un controller e una visualizzazione minimi per fungere da segnaposto per il controller MVC ASP.NET e le visualizzazioni migrate nella sezione successiva.

* Aggiungere una directory *Controllers* .

* Aggiungere una **classe controller** denominata *HomeController.cs* alla directory *Controllers* .

![Finestra di dialogo Aggiungi nuovo elemento](mvc/_static/add_mvc_ctl.png)

* Aggiungere una directory *views* .

* Aggiungere una directory *views/Home* .

* Aggiungere una ** Razor vista** denominata *index. cshtml* alla directory *views/Home* .

![Finestra di dialogo Aggiungi nuovo elemento](mvc/_static/view.png)

La struttura del progetto è illustrata di seguito:

![Esplora soluzioni la visualizzazione di file e directory di app Web 1](mvc/_static/project-structure-controller-view.png)

Sostituire il contenuto del file *Views/Home/Index.cshtml* con il markup seguente:

```html
<h1>Hello world!</h1>
```

Eseguire l'app.

![App Web aperta in Microsoft Edge](mvc/_static/hello-world.png)

Per altre informazioni, vedere [controller](xref:mvc/controllers/actions) e [visualizzazioni](xref:mvc/views/overview).

Le funzionalità seguenti richiedono la migrazione dal progetto MVC ASP.NET di esempio al progetto ASP.NET Core:

* contenuto lato client (CSS, tipi di carattere e script)

* controllers

* Viste

* modelli

* Bundle

* filters

* Accesso/uscita Identity (operazione eseguita nell'esercitazione successiva).

## <a name="controllers-and-views"></a>Controller e visualizzazioni

* Copiare tutti i metodi da ASP.NET MVC `HomeController` al nuovo `HomeController` . In ASP.NET MVC, il tipo restituito del metodo di azione del controller del modello predefinito è [ActionResult](https://msdn.microsoft.com/library/system.web.mvc.actionresult(v=vs.118).aspx); in ASP.NET Core MVC i metodi di azione restituiscono `IActionResult` invece. `ActionResult`implementa `IActionResult` , quindi non è necessario modificare il tipo restituito dei metodi di azione.

* Copiare i file *di visualizzazione About. cshtml*, *Contact. cshtml*e *index. cshtml* Razor dal progetto MVC ASP.NET al progetto ASP.NET Core.

## <a name="test-each-method"></a>Testare ogni metodo

Il file di layout e gli stili non sono ancora stati migrati, quindi le visualizzazioni sottoposte a rendering contengono solo il contenuto dei file di visualizzazione. Il file di layout i collegamenti generati per le `About` `Contact` visualizzazioni e non saranno ancora disponibili.

Richiamare le visualizzazioni sottoposte a rendering dal browser nell'app ASP.NET Core in esecuzione sostituendo il numero di porta corrente con il numero di porta usato nel progetto ASP.NET Core. Ad esempio: `https://localhost:44375/home/about`.

![Pagina contatto](mvc/_static/contact-page.png)

Si noti la mancanza di stili e voci di menu. Lo stile verrà risolto nella sezione successiva.

## <a name="static-content"></a>Contenuto statico

In ASP.NET MVC 5 e versioni precedenti, il contenuto statico era ospitato dalla radice del progetto Web ed era combinato con i file lato server. In ASP.NET Core, il contenuto statico è ospitato nella directory *wwwroot* Copiare il contenuto statico dall'app MVC ASP.NET alla directory *wwwroot* nel progetto ASP.NET Core. In questo esempio di conversione:

* Copiare il file *favicon. ico* dal progetto MVC ASP.NET alla directory *wwwroot* nel progetto ASP.NET Core.

Il progetto MVC ASP.NET usa [bootstrap](https://getbootstrap.com/) per lo stile e archivia i file bootstrap nelle directory *Content* e *Scripts* . Il modello, che ha generato il progetto MVC ASP.NET, fa riferimento a bootstrap nel file di layout (*Views/Shared/_Layout. cshtml*). È possibile copiare i file *bootstrap.js* e *bootstrap. CSS* dal progetto MVC ASP.NET alla directory *wwwroot* del nuovo progetto. Al contrario, questo documento aggiunge il supporto per bootstrap (e altre librerie lato client) usando CDNs nella sezione successiva.

## <a name="migrate-the-layout-file"></a>Eseguire la migrazione del file di layout

* Copiare il file *_ViewStart. cshtml* dalla directory *views* del progetto MVC ASP.NET alla directory *views* del progetto ASP.NET Core. Il file *_ViewStart. cshtml* non è stato modificato in ASP.NET Core MVC.

* Creare una *vista/directory condivisa* .

* *Facoltativo:* Copiare *_ViewImports. cshtml* dalla directory *views* del progetto MVC *FullAspNetCore* alla directory *views* del progetto ASP.NET Core. Rimuovere qualsiasi dichiarazione dello spazio dei nomi nel file *_ViewImports. cshtml* . Il file *_ViewImports. cshtml* fornisce gli spazi dei nomi per tutti i file di visualizzazione e porta gli [Helper Tag](xref:mvc/views/tag-helpers/intro). Gli helper tag vengono usati nel nuovo file di layout. Il file *_ViewImports. cshtml* è nuovo per ASP.NET Core.

* Copiare il file *_Layout. cshtml* dalla directory *Views/Shared* del progetto MVC ASP.NET nella directory *views/* Shared del progetto ASP.NET Core.

Aprire *_Layout file cshtml* e apportare le modifiche seguenti (il codice completato è riportato di seguito):

* Sostituire `@Styles.Render("~/Content/css")` con un `<link>` elemento per caricare *bootstrap. CSS* (vedere di seguito).

* Rimuovere `@Scripts.Render("~/bundles/modernizr")`.

* Imposta come commento la riga, che `@Html.Partial("_LoginPartial")` racchiude la riga con `@*...*@` . Per ulteriori informazioni, vedere la pagina relativa [alla migrazione dell'autenticazione e Identity alla ASP.NET Core](xref:migration/identity)

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

[!code-cshtml[](mvc/samples/2.x/Views/Shared/_Layout.cshtml?highlight=7-10,29,41-44)]

Visualizzare il sito nel browser. A questo punto, dovrebbe essere caricato correttamente con gli stili previsti.

* *Facoltativo:* Provare a usare il nuovo file di layout. Copiare il file di layout dal progetto *FullAspNetCore* . Il nuovo file di layout utilizza gli [Helper Tag](xref:mvc/views/tag-helpers/intro) e presenta altri miglioramenti.

## <a name="configure-bundling-and-minification"></a>Configurare la creazione di bundle e minification

Per informazioni su come configurare la creazione di bundle e minification, vedere [aggregazione e minification](xref:client-side/bundling-and-minification).

## <a name="solve-http-500-errors"></a>Risolvere gli errori HTTP 500

Ci sono molti problemi che possono causare un messaggio di errore HTTP 500 che non contengono informazioni sull'origine del problema. Se, ad esempio, il file *views/_ViewImports. cshtml* contiene uno spazio dei nomi che non esiste nel progetto, viene generato un errore HTTP 500. Per impostazione predefinita, nelle app ASP.NET Core, l' `UseDeveloperExceptionPage` estensione viene aggiunta a `IApplicationBuilder` ed eseguita quando la configurazione è in *fase di sviluppo*. Vedere un esempio nel codice seguente:

[!code-csharp[](mvc/samples/2.x/Startup.cs?highlight=11-15&name=snippet)]

ASP.NET Core converte le eccezioni non gestite in risposte di errore HTTP 500. In genere, i dettagli dell'errore non sono inclusi in queste risposte per impedire la divulgazione di informazioni potenzialmente riservate sul server. Per ulteriori informazioni, vedere [pagina delle eccezioni per sviluppatori](xref:fundamentals/error-handling#developer-exception-page).

## <a name="additional-resources"></a>Risorse aggiuntive

* <xref:blazor/index>
* <xref:mvc/views/tag-helpers/intro>

::: moniker-end

::: moniker range="<= aspnetcore-2.1"

Questo articolo illustra come avviare la migrazione di un progetto MVC ASP.NET a [ASP.NET Core mvc](xref:mvc/overview) 2,1. Nel processo, vengono evidenziati molti degli elementi che sono stati modificati rispetto a ASP.NET MVC. La migrazione da ASP.NET MVC è un processo in più passaggi. Questo articolo riguarda:

* Configurazione iniziale
* Controller e visualizzazioni di base
* Contenuto statico
* Dipendenze lato client.

Per la migrazione della configurazione e del Identity codice, vedere eseguire la migrazione della [configurazione per ASP.NET Core](xref:migration/configuration) ed [eseguire la migrazione dell'autenticazione e Identity di ASP.NET Core](xref:migration/identity).

> [!NOTE]
> I numeri di versione negli esempi potrebbero non essere aggiornati, aggiornare i progetti di conseguenza.

## <a name="create-the-starter-aspnet-mvc-project"></a>Creare il progetto MVC Starter ASP.NET

Per illustrare l'aggiornamento, si inizierà con la creazione di un'app MVC ASP.NET. Crearlo con il nome *app Web 1* in modo che lo spazio dei nomi corrisponda al progetto ASP.NET Core creato nel passaggio successivo.

![Finestra di dialogo Nuovo progetto di Visual Studio](mvc/_static/new-project.png)

![Finestra di dialogo nuova applicazione Web: modello di progetto MVC selezionato nel pannello modelli ASP.NET](mvc/_static/new-project-select-mvc-template.png)

*Facoltativo:* Modificare il nome della soluzione da *app Web 1* a *Mvc5*. In Visual Studio viene visualizzato il nuovo nome della soluzione (*Mvc5*), che rende più semplice la creazione di questo progetto dal progetto successivo.

## <a name="create-the-aspnet-core-project"></a>Creare il progetto di ASP.NET Core

Creare una nuova app Web ASP.NET Core *vuota* con lo stesso nome del progetto precedente (*app Web 1*) in modo che gli spazi dei nomi nei due progetti corrispondano. La presenza dello stesso spazio dei nomi rende più semplice la copia di codice tra i due progetti. Creare il progetto in una directory diversa da quella del progetto precedente per usare lo stesso nome.

![Finestra di dialogo Nuovo progetto](mvc/_static/new_core.png)

![Finestra di dialogo nuova applicazione Web ASP.NET: modello di progetto vuoto selezionato nel pannello modelli ASP.NET Core](mvc/_static/new-project-select-empty-aspnet5-template.png)

* *Facoltativo:* Creare una nuova app ASP.NET Core usando il modello di progetto *applicazione Web* . Denominare il progetto *app Web 1*e selezionare un'opzione di autenticazione per **singoli account utente**. Rinominare l'app in *FullAspNetCore*. La creazione di questo progetto consente di risparmiare tempo nella conversione. Il risultato finale può essere visualizzato nel codice generato dal modello, il codice può essere copiato nel progetto di conversione o confrontato con il progetto generato dal modello.

## <a name="configure-the-site-to-use-mvc"></a>Configurare il sito per l'uso di MVC

* Quando la destinazione è .NET Core, per impostazione predefinita viene fatto riferimento al [metapacchetto Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) . Questo pacchetto contiene i pacchetti usati comunemente dalle app MVC. Se la destinazione è .NET Framework, i riferimenti ai pacchetti devono essere elencati singolarmente nel file di progetto.

`Microsoft.AspNetCore.Mvc`è il Framework di MVC ASP.NET Core. `Microsoft.AspNetCore.StaticFiles`è il gestore di file statici. ASP.NET Core le app scelgono esplicitamente il middleware, ad esempio per la conservazione dei file statici. Per altre informazioni, vedere [File statici](xref:fundamentals/static-files).

* Aprire il file *Startup.cs* e modificare il codice in modo che corrisponda a quanto segue:

[!code-csharp[](mvc/samples/2.x/Startup.cs?highlight=7,20-25&name=snippet)]

Il <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*> metodo di estensione aggiunge il gestore di file statici. Il `UseMvc` metodo di estensione aggiunge il routing. Per ulteriori informazioni, vedere Startup e [routing](xref:fundamentals/routing) [dell'applicazione](xref:fundamentals/startup) .

## <a name="add-a-controller-and-view"></a>Aggiungere un controller e una visualizzazione

In questa sezione vengono aggiunti un controller e una visualizzazione minimi per fungere da segnaposto per il controller MVC ASP.NET e le visualizzazioni migrate nella sezione successiva.

* Aggiungere una directory *Controllers* .

* Aggiungere una **classe controller** denominata *HomeController.cs* alla directory *Controllers* .

![Finestra di dialogo Aggiungi nuovo elemento](mvc/_static/add_mvc_ctl.png)

* Aggiungere una directory *views* .

* Aggiungere una directory *views/Home* .

* Aggiungere una ** Razor vista** denominata *index. cshtml* alla directory *views/Home* .

![Finestra di dialogo Aggiungi nuovo elemento](mvc/_static/view.png)

La struttura del progetto è illustrata di seguito:

![Esplora soluzioni la visualizzazione di file e directory di app Web 1](mvc/_static/project-structure-controller-view.png)

Sostituire il contenuto del file *Views/Home/Index.cshtml* con il markup seguente:

```html
<h1>Hello world!</h1>
```

Eseguire l'app.

![App Web aperta in Microsoft Edge](mvc/_static/hello-world.png)

Per altre informazioni, vedere [controller](xref:mvc/controllers/actions) e [visualizzazioni](xref:mvc/views/overview).

Le funzionalità seguenti richiedono la migrazione dal progetto MVC ASP.NET di esempio al progetto ASP.NET Core:

* contenuto lato client (CSS, tipi di carattere e script)

* controllers

* Viste

* modelli

* Bundle

* filters

* Accesso/uscita Identity (operazione eseguita nell'esercitazione successiva).

## <a name="controllers-and-views"></a>Controller e visualizzazioni

* Copiare tutti i metodi da ASP.NET MVC `HomeController` al nuovo `HomeController` . In ASP.NET MVC, il tipo restituito del metodo di azione del controller del modello predefinito è [ActionResult](https://msdn.microsoft.com/library/system.web.mvc.actionresult(v=vs.118).aspx); in ASP.NET Core MVC i metodi di azione restituiscono `IActionResult` invece. `ActionResult`implementa `IActionResult` , quindi non è necessario modificare il tipo restituito dei metodi di azione.

* Copiare i file *di visualizzazione About. cshtml*, *Contact. cshtml*e *index. cshtml* Razor dal progetto MVC ASP.NET al progetto ASP.NET Core.

## <a name="test-each-method"></a>Testare ogni metodo

Il file di layout e gli stili non sono ancora stati migrati, quindi le visualizzazioni sottoposte a rendering contengono solo il contenuto dei file di visualizzazione. Il file di layout i collegamenti generati per le `About` `Contact` visualizzazioni e non saranno ancora disponibili.

* Richiamare le visualizzazioni sottoposte a rendering dal browser nell'app ASP.NET Core in esecuzione sostituendo il numero di porta corrente con il numero di porta usato nel progetto ASP.NET Core. Ad esempio: `https://localhost:44375/home/about`.

![Pagina contatto](mvc/_static/contact-page.png)

Si noti la mancanza di stili e voci di menu. Lo stile verrà risolto nella sezione successiva.

## <a name="static-content"></a>Contenuto statico

In ASP.NET MVC 5 e versioni precedenti, il contenuto statico era ospitato dalla radice del progetto Web ed era combinato con i file lato server. In ASP.NET Core, il contenuto statico è ospitato nella directory *wwwroot* Copiare il contenuto statico dall'app MVC ASP.NET alla directory *wwwroot* nel progetto ASP.NET Core. In questo esempio di conversione:

* Copiare il file *favicon. ico* dal progetto MVC ASP.NET alla directory *wwwroot* nel progetto ASP.NET Core.

Il progetto MVC ASP.NET usa [bootstrap](https://getbootstrap.com/) per lo stile e archivia i file bootstrap nelle directory *Content* e *Scripts* . Il modello, che ha generato il progetto MVC ASP.NET, fa riferimento a bootstrap nel file di layout (*Views/Shared/_Layout. cshtml*). È possibile copiare i file *bootstrap.js* e *bootstrap. CSS* dal progetto MVC ASP.NET alla directory *wwwroot* del nuovo progetto. Al contrario, questo documento aggiunge il supporto per bootstrap (e altre librerie lato client) usando CDNs nella sezione successiva.

## <a name="migrate-the-layout-file"></a>Eseguire la migrazione del file di layout

* Copiare il file *_ViewStart. cshtml* dalla directory *views* del progetto MVC ASP.NET alla directory *views* del progetto ASP.NET Core. Il file *_ViewStart. cshtml* non è stato modificato in ASP.NET Core MVC.

* Creare una *vista/directory condivisa* .

* *Facoltativo:* Copiare *_ViewImports. cshtml* dalla directory *views* del progetto MVC *FullAspNetCore* alla directory *views* del progetto ASP.NET Core. Rimuovere qualsiasi dichiarazione dello spazio dei nomi nel file *_ViewImports. cshtml* . Il file *_ViewImports. cshtml* fornisce gli spazi dei nomi per tutti i file di visualizzazione e porta gli [Helper Tag](xref:mvc/views/tag-helpers/intro). Gli helper tag vengono usati nel nuovo file di layout. Il file *_ViewImports. cshtml* è nuovo per ASP.NET Core.

* Copiare il file *_Layout. cshtml* dalla directory *Views/Shared* del progetto MVC ASP.NET nella directory *views/* Shared del progetto ASP.NET Core.

Aprire *_Layout file cshtml* e apportare le modifiche seguenti (il codice completato è riportato di seguito):

* Sostituire `@Styles.Render("~/Content/css")` con un `<link>` elemento per caricare *bootstrap. CSS* (vedere di seguito).

* Rimuovere `@Scripts.Render("~/bundles/modernizr")`.

* Imposta come commento la riga, che `@Html.Partial("_LoginPartial")` racchiude la riga con `@*...*@` . Per ulteriori informazioni, vedere la pagina relativa [alla migrazione dell'autenticazione e Identity alla ASP.NET Core](xref:migration/identity)

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

[!code-cshtml[](mvc/samples/2.x/Views/Shared/_Layout.cshtml?highlight=7-10,29,41-44)]

Visualizzare il sito nel browser. A questo punto, dovrebbe essere caricato correttamente con gli stili previsti.

* *Facoltativo:* Provare a usare il nuovo file di layout. Copiare il file di layout dal progetto *FullAspNetCore* . Il nuovo file di layout utilizza gli [Helper Tag](xref:mvc/views/tag-helpers/intro) e presenta altri miglioramenti.

## <a name="configure-bundling-and-minification"></a>Configurare la creazione di bundle e minification

Per informazioni su come configurare la creazione di bundle e minification, vedere [aggregazione e minification](xref:client-side/bundling-and-minification).

## <a name="solve-http-500-errors"></a>Risolvere gli errori HTTP 500

Ci sono molti problemi che possono causare un messaggio di errore HTTP 500 che non contengono informazioni sull'origine del problema. Se, ad esempio, il file *views/_ViewImports. cshtml* contiene uno spazio dei nomi che non esiste nel progetto, viene generato un errore HTTP 500. Per impostazione predefinita, nelle app ASP.NET Core, l' `UseDeveloperExceptionPage` estensione viene aggiunta a `IApplicationBuilder` ed eseguita quando la configurazione è in *fase di sviluppo*. Vedere un esempio nel codice seguente:

[!code-csharp[](mvc/samples/2.x/Startup.cs?highlight=11-15&name=snippet)]

ASP.NET Core converte le eccezioni non gestite in risposte di errore HTTP 500. In genere, i dettagli dell'errore non sono inclusi in queste risposte per impedire la divulgazione di informazioni potenzialmente riservate sul server. Per ulteriori informazioni, vedere [pagina delle eccezioni per sviluppatori](xref:fundamentals/error-handling#developer-exception-page).

## <a name="additional-resources"></a>Risorse aggiuntive

* <xref:blazor/index>
* <xref:mvc/views/tag-helpers/intro>

::: moniker-end
