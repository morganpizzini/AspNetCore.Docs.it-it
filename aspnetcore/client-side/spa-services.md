---
title: Usare i servizi JavaScript per creare applicazioni a pagina singola in ASP.NET Core
author: scottaddie
description: Informazioni sui vantaggi derivanti dall'uso dei servizi JavaScript per creare un'applicazione a pagina singola (SPA) supportata da ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: H1Hack27Feb2017, devx-track-js
ms.date: 09/06/2019
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
uid: client-side/spa-services
ms.openlocfilehash: 33ae16c033142aa1c0f4cea53ca746e8a6aaf052
ms.sourcegitcommit: 2e3a967331b2c69f585dd61e9ad5c09763615b44
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92690656"
---
# <a name="use-javascript-services-to-create-single-page-applications-in-aspnet-core"></a>Usare i servizi JavaScript per creare applicazioni a pagina singola in ASP.NET Core

Di [Scott Addie](https://github.com/scottaddie) e [Fiyaz Hasan](https://fiyazhasan.me/)

Un'applicazione a pagina singola (SPA) è un tipo comune di applicazione Web grazie alla relativa esperienza utente avanzata. L'integrazione di Framework o librerie di SPA lato client, ad esempio [angolare](https://angular.io/) o [React](https://facebook.github.io/react/), con framework lato server come ASP.NET Core può essere difficile. Servizi JavaScript è stato sviluppato per ridurre l'attrito nel processo di integrazione. Consente un funzionamento uniforme tra i diversi stack di tecnologie client e server.

::: moniker range=">= aspnetcore-3.0"

> [!WARNING]
> Le funzionalità descritte in questo articolo sono obsolete a partire da ASP.NET Core 3,0. Un meccanismo di integrazione di Framework SPA più semplice è disponibile nel pacchetto NuGet [Microsoft. AspNetCore. SpaServices. Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices.Extensions) . Per ulteriori informazioni, vedere [[annuncio] Obsoleting Microsoft. AspNetCore. SpaServices e Microsoft. AspNetCore. NodeServices](https://github.com/dotnet/AspNetCore/issues/12890).

::: moniker-end

## <a name="what-is-javascript-services"></a>Informazioni sui servizi JavaScript

JavaScript Services è una raccolta di tecnologie lato client per ASP.NET Core. Il suo obiettivo è quello di posizionare ASP.NET Core come la piattaforma lato server preferita dagli sviluppatori per la creazione di Spa.

Servizi JavaScript è costituito da due pacchetti NuGet distinti:

* [Microsoft. AspNetCore. NodeServices](https://www.nuget.org/packages/Microsoft.AspNetCore.NodeServices/) (NodeServices)
* [Microsoft. AspNetCore. SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) (SpaServices)

Questi pacchetti sono utili negli scenari seguenti:

* Esegui JavaScript sul server
* Usare un Framework o una libreria SPA
* Crea asset lato client con Webpack

Gran parte di questo articolo è dedicata all'uso del pacchetto SpaServices.

## <a name="what-is-spaservices"></a>Informazioni su SpaServices

SpaServices è stato creato per posizionare ASP.NET Core la piattaforma lato server preferita dagli sviluppatori per la creazione di Spa. SpaServices non è necessario per sviluppare Spa con ASP.NET Core e non blocca gli sviluppatori in un particolare framework client.

SpaServices fornisce un'infrastruttura utile, ad esempio:

* [Prerendering lato server](#server-side-prerendering)
* [Middleware dev per Webpack](#webpack-dev-middleware)
* [Sostituzione del modulo attivo](#hot-module-replacement)
* [Helper di routing](#routing-helpers)

Insieme, questi componenti dell'infrastruttura migliorano sia il flusso di lavoro di sviluppo che l'esperienza di Runtime. I componenti possono essere adottati singolarmente.

## <a name="prerequisites-for-using-spaservices"></a>Prerequisiti per l'uso di SpaServices

Per usare SpaServices, installare quanto segue:

* [Node.js](https://nodejs.org/) (versione 6 o successiva) con NPM

  * Per verificare che questi componenti siano installati ed è possibile trovarli, eseguire il comando seguente dalla riga di comando:

    ```console
    node -v && npm -v
    ```

  * Se si esegue la distribuzione in un sito Web di Azure, non è necessaria alcuna azione &mdash;Node.js viene installato e disponibile negli ambienti server.

* [!INCLUDE [](~/includes/net-core-sdk-download-link.md)]

  * In Windows con Visual Studio 2017, l'SDK viene installato selezionando il carico di lavoro **sviluppo multipiattaforma .NET Core** .

* Pacchetto NuGet [Microsoft. AspNetCore. SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/)

## <a name="server-side-prerendering"></a>Prerendering lato server

Un'applicazione universale (nota anche come isomorfi) è un'applicazione JavaScript in grado di eseguire sia nel server che nel client. Angolari, React e altri Framework diffusi forniscono una piattaforma universale per questo stile di sviluppo di applicazioni. L'idea è eseguire prima di tutto il rendering dei componenti del Framework nel server tramite Node.js e quindi delegare un'ulteriore esecuzione al client.

ASP.NET Core [Helper Tag](xref:mvc/views/tag-helpers/intro) forniti da SpaServices semplificano l'implementazione del prerendering lato server richiamando le funzioni JavaScript sul server.

### <a name="server-side-prerendering-prerequisites"></a>Prerequisiti per il rendering lato server

Installare il pacchetto NPM per l'esecuzione del [prerendering di ASPNET](https://www.npmjs.com/package/aspnet-prerendering) :

```console
npm i -S aspnet-prerendering
```

### <a name="server-side-prerendering-configuration"></a>Configurazione del prerendering lato server

Gli helper tag vengono resi individuabili tramite la registrazione dello spazio dei nomi nel file *_ViewImports. cshtml* del progetto:

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/_ViewImports.cshtml?highlight=3)]

Questi helper Tag astraggono le complessità della comunicazione diretta con le API di basso livello sfruttando una sintassi simile a HTML all'interno della Razor vista:

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/Home/Index.cshtml?range=5)]

### <a name="asp-prerender-module-tag-helper"></a>ASP-PreRender-Helper tag del modulo

L' `asp-prerender-module` Helper tag, usato nell'esempio di codice precedente, esegue *ClientApp/dist/main-server.js* sul server tramite Node.js. Per maggiore chiarezza, *main-server.js* file è un artefatto dell'attività Transpilazione da typescript a JavaScript nel processo di compilazione di [Webpack](https://webpack.github.io/) . Webpack definisce un alias del punto di ingresso `main-server` e l'attraversamento del grafico delle dipendenze per questo alias inizia dal file *ClientApp/boot-server. TS* :

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=53)]

Nell'esempio angolare seguente il file *ClientApp/boot-server. TS* usa la `createServerRenderer` funzione e il `RenderResult` tipo del `aspnet-prerendering` pacchetto NPM per configurare il rendering del server tramite Node.js. Il markup HTML destinato al rendering sul lato server viene passato a una chiamata di funzione Resolve, che è racchiusa in un oggetto JavaScript fortemente tipizzato `Promise` . Il `Promise` significato dell'oggetto è che fornisce in modo asincrono il markup HTML alla pagina per l'inserimento nell'elemento segnaposto del Dom.

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-34,79-)]

### <a name="asp-prerender-data-tag-helper"></a>ASP-PreRender-Helper tag di dati

Quando è associato all' `asp-prerender-module` Helper tag, `asp-prerender-data` è possibile usare l'helper tag per passare le informazioni contestuali dalla Razor visualizzazione al codice JavaScript sul lato server. Il markup seguente, ad esempio, passa i dati utente al `main-server` modulo:

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/Home/Index.cshtml?range=9-12)]

L'argomento received `UserName` viene serializzato usando il serializzatore JSON incorporato e viene archiviato nell' `params.data` oggetto. Nell'esempio angolare seguente i dati vengono usati per costruire un messaggio di saluto personalizzato all'interno di un `h1` elemento:

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-21,38-52,79-)]

I nomi delle proprietà passati negli Helper tag sono rappresentati con la notazione **PascalCase** . A differenza di JavaScript, in cui gli stessi nomi di proprietà sono rappresentati con **CamelCase** . La configurazione della serializzazione JSON predefinita è responsabile di questa differenza.

Per espandersi sull'esempio di codice precedente, è possibile passare i dati dal server alla visualizzazione idratando la `globals` proprietà fornita alla `resolve` funzione:

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-21,57-77,79-)]

La `postList` matrice definita all'interno dell' `globals` oggetto è associata all'oggetto globale del browser `window` . Questa variabile che solleva l'ambito globale elimina la duplicazione del lavoro, in particolare per quanto riguarda il caricamento degli stessi dati una volta sul server e di nuovo sul client.

![variabile di postlist globale collegata all'oggetto finestra](spa-services/_static/global_variable.png)

## <a name="webpack-dev-middleware"></a>Middleware dev per Webpack

Il [middleware dev per Webpack](https://webpack.js.org/guides/development/#using-webpack-dev-middleware) introduce un flusso di lavoro di sviluppo semplificato, in base al quale Webpack crea risorse su richiesta. Il middleware compila e serve automaticamente le risorse sul lato client quando una pagina viene ricaricata nel browser. L'approccio alternativo consiste nel richiamare manualmente Webpack tramite lo script di compilazione NPM del progetto quando viene modificata una dipendenza di terze parti o il codice personalizzato. Nell'esempio seguente viene illustrato uno script di compilazione NPM nella *package.jssu* file:

```json
"build": "npm run build:vendor && npm run build:custom",
```

### <a name="webpack-dev-middleware-prerequisites"></a>Prerequisiti del middleware dev per Webpack

Installare il pacchetto NPM [ASPNET-Webpack](https://www.npmjs.com/package/aspnet-webpack) :

```console
npm i -D aspnet-webpack
```

### <a name="webpack-dev-middleware-configuration"></a>Configurazione di Webpack dev middleware

Il middleware dev per Webpack viene registrato nella pipeline di richieste HTTP tramite il codice seguente nel metodo del file *Startup.cs* `Configure` :

[!code-csharp[](../client-side/spa-services/sample/SpaServicesSampleApp/Startup.cs?name=snippet_WebpackMiddlewareRegistration&highlight=4)]

Il `UseWebpackDevMiddleware` metodo di estensione deve essere chiamato prima di [registrare l'hosting di file statici](xref:fundamentals/static-files) tramite il `UseStaticFiles` metodo di estensione. Per motivi di sicurezza, registrare il middleware solo quando l'app viene eseguita in modalità di sviluppo.

La proprietà del file *webpack.config.js* `output.publicPath` indica al middleware di controllare la `dist` cartella per le modifiche:

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=6,13-16)]

## <a name="hot-module-replacement"></a>Sostituzione del modulo attivo

Si pensi alla funzionalità HMR ( [Hot Module Replacement](https://webpack.js.org/concepts/hot-module-replacement/) ) di Webpack come un'evoluzione del [middleware dev di Webpack](#webpack-dev-middleware). HMR introduce tutti gli stessi vantaggi, ma semplifica ulteriormente il flusso di lavoro di sviluppo aggiornando automaticamente il contenuto della pagina dopo la compilazione delle modifiche. Non confonderlo con un aggiornamento del browser, che interferisce con lo stato in memoria e la sessione di debug correnti della SPA. Esiste un collegamento attivo tra il servizio middleware dev per Webpack e il browser, che significa che le modifiche vengono inserite nel browser.

### <a name="hot-module-replacement-prerequisites"></a>Prerequisiti per la sostituzione dei moduli sensibili

Installare il pacchetto NPM per [Webpack-Hot-middleware](https://www.npmjs.com/package/webpack-hot-middleware) :

```console
npm i -D webpack-hot-middleware
```

### <a name="hot-module-replacement-configuration"></a>Configurazione della sostituzione del modulo attivo

Il componente HMR deve essere registrato nella pipeline di richieste HTTP di MVC nel `Configure` Metodo:

```csharp
app.UseWebpackDevMiddleware(new WebpackDevMiddlewareOptions {
    HotModuleReplacement = true
});
```

Come accadeva con il [middleware dev di Webpack](#webpack-dev-middleware), il `UseWebpackDevMiddleware` metodo di estensione deve essere chiamato prima del `UseStaticFiles` metodo di estensione. Per motivi di sicurezza, registrare il middleware solo quando l'app viene eseguita in modalità di sviluppo.

Il file di *webpack.config.js* deve definire una `plugins` matrice, anche se viene lasciato vuoto:

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=6,25)]

Dopo aver caricato l'app nel browser, la scheda della console degli strumenti per sviluppatori fornisce la conferma dell'attivazione di HMR:

![Messaggio connesso di sostituzione del modulo attivo](spa-services/_static/hmr_connected.png)

## <a name="routing-helpers"></a>Helper di routing

Nella maggior parte delle applicazioni Spa basate su ASP.NET Core, il routing lato client spesso è necessario oltre al routing lato server. I sistemi di routing SPA e MVC possono funzionare in modo indipendente senza interferenze. Esiste, tuttavia, un caso marginale che pone problemi: identificazione delle risposte HTTP 404.

Si consideri lo scenario in cui viene utilizzata una route con estensione `/some/page` . Si supponga che la richiesta non corrisponda a una route sul lato server, ma il modello corrispondente corrisponde a una route sul lato client. Si consideri ora una richiesta in ingresso per `/images/user-512.png` , che in genere prevede di trovare un file di immagine nel server. Se il percorso della risorsa richiesto non corrisponde ad alcuna route sul lato server o a un file statico, è improbabile che l'applicazione sul lato client la gestisca &mdash; in genere restituendo un codice di stato HTTP 404.

### <a name="routing-helpers-prerequisites"></a>Prerequisiti per il routing degli helper

Installare il pacchetto NPM di routing lato client. Uso di angolari come esempio:

```console
npm i -S @angular/router
```

### <a name="routing-helpers-configuration"></a>Configurazione degli helper di routing

Un metodo di estensione denominato `MapSpaFallbackRoute` viene usato nel `Configure` Metodo:

[!code-csharp[](../client-side/spa-services/sample/SpaServicesSampleApp/Startup.cs?name=snippet_MvcRoutingTable&highlight=7-9)]

Le route vengono valutate nell'ordine in cui sono state configurate. Di conseguenza, la `default` route nell'esempio di codice precedente viene usata per prima per i criteri di ricerca.

## <a name="create-a-new-project"></a>Creare un nuovo progetto

I servizi JavaScript forniscono modelli di applicazione preconfigurati. SpaServices viene usato in questi modelli insieme a Framework e librerie diversi, ad esempio angolare, React e Redux.

Questi modelli possono essere installati tramite il interfaccia della riga di comando di .NET Core eseguendo il comando seguente:

```dotnetcli
dotnet new --install Microsoft.AspNetCore.SpaTemplates::*
```

Viene visualizzato un elenco di modelli di SPA disponibili:

| Modelli                                 | Nome breve | Linguaggio | Tag        |
| ------------------------------------------| :--------: | :------: | :---------: |
| ASP.NET Core MVC con angolare             | angular    | [C#]     | Web/MVC/SPA |
| ASP.NET Core MVC con React.js            | react      | [C#]     | Web/MVC/SPA |
| ASP.NET Core MVC con React.js e Redux  | reactredux | [C#]     | Web/MVC/SPA |

Per creare un nuovo progetto usando uno dei modelli di SPA, includere il **nome breve** del modello nel comando [DotNet New](/dotnet/core/tools/dotnet-new) . Il comando seguente crea un'applicazione angolare con ASP.NET Core MVC configurato per il lato server:

```dotnetcli
dotnet new angular
```

### <a name="set-the-runtime-configuration-mode"></a>Impostare la modalità di configurazione del runtime

Esistono due modalità di configurazione di runtime primarie:

* **Sviluppo** :
  * Include i mapping di origine per semplificare il debug.
  * Non ottimizza il codice lato client per le prestazioni.
* **Produzione** :
  * Esclude i mapping di origine.
  * Ottimizza il codice lato client tramite la creazione di bundle e minification.

ASP.NET Core usa una variabile di ambiente denominata `ASPNETCORE_ENVIRONMENT` per archiviare la modalità di configurazione. Per ulteriori informazioni, vedere [impostazione dell'ambiente](xref:fundamentals/environments#set-the-environment).

### <a name="run-with-net-core-cli"></a>Esegui con interfaccia della riga di comando di .NET Core

Ripristinare i pacchetti NuGet e NPM richiesti eseguendo il comando seguente nella radice del progetto:

```dotnetcli
dotnet restore && npm i
```

Compilare ed eseguire l'applicazione:

```dotnetcli
dotnet run
```

L'applicazione viene avviata in localhost in base alla [modalità di configurazione del runtime](#set-the-runtime-configuration-mode). Se si passa a `http://localhost:5000` nel browser, viene visualizzata la pagina di destinazione.

### <a name="run-with-visual-studio-2017"></a>Eseguire con Visual Studio 2017

Aprire il file con *estensione csproj* generato dal comando [DotNet New](/dotnet/core/tools/dotnet-new) . I pacchetti NuGet e NPM richiesti vengono ripristinati automaticamente al momento dell'apertura del progetto. Questo processo di ripristino può richiedere alcuni minuti e l'applicazione è pronta per essere eseguita al completamento. Fare clic sul pulsante di esecuzione verde o premere `Ctrl + F5` e il browser si apre alla pagina di destinazione dell'applicazione. L'applicazione viene eseguita in localhost in base alla [modalità di configurazione del runtime](#set-the-runtime-configuration-mode).

## <a name="test-the-app"></a>Testare l'app

I modelli SpaServices sono preconfigurati per eseguire test sul lato client usando [Karma](https://karma-runner.github.io/1.0/index.html) e [Jasmine](https://jasmine.github.io/). Jasmine è un Framework di testing unità popolare per JavaScript, mentre Karma è un test runner per i test. Karma è configurato per funzionare con il [middleware dev di Webpack](#webpack-dev-middleware) in modo che lo sviluppatore non debba arrestare ed eseguire il test ogni volta che vengono apportate modifiche. Indipendentemente dal fatto che il codice sia in esecuzione sul test case o sul test case stesso, il test viene eseguito automaticamente.

Usando l'applicazione angolare come esempio, due test case Jasmine sono già disponibili per `CounterComponent` nel file *Counter. Component. spec. TS* :

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/app/components/counter/counter.component.spec.ts?range=15-28)]

Aprire il prompt dei comandi nella directory *ClientApp* Eseguire il comando seguente:

```console
npm test
```

Lo script avvia Karma Test Runner, che legge le impostazioni definite nel file di *karma.conf.js* . Tra le altre impostazioni, il *karma.conf.js* identifica i file di test da eseguire tramite la relativa `files` matrice:

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/test/karma.conf.js?range=4-5,8-11)]

## <a name="publish-the-app"></a>Pubblicare l'app

Per altre informazioni sulla pubblicazione in Azure, vedere [questo problema di GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/12474) .

La combinazione degli asset lato client generati e degli elementi ASP.NET Core pubblicati in un pacchetto pronto per la distribuzione può essere complessa. Fortunatamente, SpaServices Orchestra l'intero processo di pubblicazione con una destinazione MSBuild personalizzata denominata `RunWebpack` :

[!code-xml[](../client-side/spa-services/sample/SpaServicesSampleApp/SpaServicesSampleApp.csproj?range=31-45)]

La destinazione MSBuild ha le responsabilità seguenti:

1. Ripristinare i pacchetti NPM.
1. Creare una build di livello produzione degli asset lato client di terze parti.
1. Creare una compilazione di livello di produzione degli asset personalizzati sul lato client.
1. Copiare gli asset generati da Webpack nella cartella di pubblicazione.

La destinazione MSBuild viene richiamata durante l'esecuzione di:

```dotnetcli
dotnet publish -c Release
```

## <a name="additional-resources"></a>Risorse aggiuntive

* [Documenti angolari](https://angular.io/docs)
