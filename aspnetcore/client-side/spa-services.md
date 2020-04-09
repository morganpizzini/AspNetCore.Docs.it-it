---
title: Utilizzare i servizi JavaScript per creare applicazioni a pagina singola in ASP.NET Core
author: scottaddie
description: Scopri i vantaggi dell'uso di JavaScript Services per creare un'applicazione a pagina singola (SPA) supportata da ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: H1Hack27Feb2017
ms.date: 09/06/2019
uid: client-side/spa-services
ms.openlocfilehash: c0c73882afd579510ad9cdf5b485c1d6fbeadd1c
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "78663779"
---
# <a name="use-javascript-services-to-create-single-page-applications-in-aspnet-core"></a>Utilizzare i servizi JavaScript per creare applicazioni a pagina singola in ASP.NET Core

Di [Scott Addie](https://github.com/scottaddie) e [Fiyaz Hasan](https://fiyazhasan.me/)

Un'applicazione a pagina singola (SPA) è un tipo popolare di applicazione web a causa della sua esperienza utente ricca e intrinseca. L'integrazione di framework o librerie SPA lato client, ad esempio [Angular](https://angular.io/) o [React](https://facebook.github.io/react/), con framework sul lato server, ad esempio ASP.NET Core, può essere difficile. JavaScript Services è stato sviluppato per ridurre l'attrito nel processo di integrazione. Consente un funzionamento senza problemi tra i diversi stack di tecnologia client e server.

::: moniker range=">= aspnetcore-3.0"

> [!WARNING]
> Le funzionalità descritte in questo articolo sono obsolete a partire da ASP.NET Core 3.0. Un meccanismo di integrazione dei framework SPA più semplice è disponibile nel pacchetto [Microsoft.AspNetCore.SpaServices.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices.Extensions) NuGet. Per ulteriori informazioni, vedere [[Annuncio] Obsoleto Microsoft.AspNetCore.SpaServices e Microsoft.AspNetCore.NodeServices](https://github.com/dotnet/AspNetCore/issues/12890).

::: moniker-end

## <a name="what-is-javascript-services"></a>Che cos'è servizi JavaScript

JavaScript Services è una raccolta di tecnologie lato client per ASP.NET Core. Il suo obiettivo è quello di posizionare ASP.NET Core come piattaforma lato server preferito dagli sviluppatori per la creazione di GIR.

JavaScript Services è costituito da due pacchetti NuGet distinti:

* [Microsoft.AspNetCore.NodeServices](https://www.nuget.org/packages/Microsoft.AspNetCore.NodeServices/) (NodeServices)
* [Microsoft.AspNetCore.SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) (SpaServices)

Questi pacchetti sono utili negli scenari seguenti:These packages are useful in the following scenarios:

* Eseguire JavaScript sul server
* Utilizzare un framework o una libreria SPA
* Crea risorse lato client con Webpack

Gran parte dell'attenzione in questo articolo è posta sull'utilizzo del pacchetto SpaServices.

## <a name="what-is-spaservices"></a>Che cos'è SpaServices

SpaServices è stato creato per posizionare ASP.NET Core come piattaforma lato server preferito dagli sviluppatori per la creazione di SPA. SpaServices non è necessario per sviluppare SPAAs con ASP.NET Core e non blocca gli sviluppatori in un framework client specifico.

SpaServices fornisce un'infrastruttura utile come:

* [Prerendering lato server](#server-side-prerendering)
* [Middleware per sviluppatori di webpack](#webpack-dev-middleware)
* [Sostituzione modulo caldo](#hot-module-replacement)
* [Helper di routing](#routing-helpers)

Collettivamente, questi componenti dell'infrastruttura migliorano sia il flusso di lavoro di sviluppo che l'esperienza di runtime. I componenti possono essere adottati singolarmente.

## <a name="prerequisites-for-using-spaservices"></a>Prerequisiti per l'utilizzo di SpaServicesPrerequisites for using SpaServices

Per utilizzare SpaServices, installare quanto segue:

* [Node.js](https://nodejs.org/) (versione 6 o successiva) con npm

  * Per verificare che questi componenti siano installati e reperibili, eseguire quanto segue dalla riga di comando:

    ```console
    node -v && npm -v
    ```

  * Se si esegue la distribuzione in un&mdash;sito Web di Azure, non è richiesta alcuna azione Node.js che è installata e disponibile negli ambienti server.

* [!INCLUDE [](~/includes/net-core-sdk-download-link.md)]

  * In Windows con Visual Studio 2017, l'SDK viene installato selezionando il carico di lavoro di **sviluppo multipiattaforma .NET Core.On** Windows using Visual Studio 2017, the SDK is installed by selecting the .NET Core cross-platform development workload.

* [Pacchetto Microsoft.AspNetCore.SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) NuGet

## <a name="server-side-prerendering"></a>Prerendering lato server

Un'applicazione universale (nota anche come isomorfica) è un'applicazione JavaScript in grado di essere eseguita sia sul server che sul client. Angolare, React e altri framework popolari forniscono una piattaforma universale per questo stile di sviluppo dell'applicazione. L'idea è prima di eseguire il rendering dei componenti del framework sul server tramite Node.js e quindi delegare ulteriormente l'esecuzione al client.

ASP.NET [gli helper](xref:mvc/views/tag-helpers/intro) Core Tag forniti da SpaServices semplificano l'implementazione del prerendering sul lato server richiamando le funzioni JavaScript sul server.

### <a name="server-side-prerendering-prerequisites"></a>Prerequisiti di prerendering sul lato serverServer-side prerendering prerequisites

Installare il pacchetto [npm di aspnet-prerendering:](https://www.npmjs.com/package/aspnet-prerendering)

```console
npm i -S aspnet-prerendering
```

### <a name="server-side-prerendering-configuration"></a>Configurazione del prerendering sul lato server

Gli helper tag vengono resi individuabili tramite la registrazione dello spazio dei nomi nel file _ViewImports.cshtml del progetto:The Tag Helpers are made discoverable via namespace registration in the project's *_ViewImports.cshtml* file:

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/_ViewImports.cshtml?highlight=3)]

Questi tag helper astraggono la complessità della comunicazione diretta con le API di basso livello sfruttando una sintassi simile a HTML all'interno della visualizzazione Razor:

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/Home/Index.cshtml?range=5)]

### <a name="asp-prerender-module-tag-helper"></a>asp-prerender-module Tag Helper

Tag `asp-prerender-module` Helper, utilizzato nell'esempio di codice precedente, esegue *ClientApp/dist/main-server.js* sul server tramite Node.js. Per motivi di chiarezza, il file *main-server.js* è un elemento dell'attività di transpilazione da TypeScript a JavaScript nel processo di compilazione [Webpack.](https://webpack.github.io/) Webpack definisce un alias `main-server`del punto di ingresso di ; e, l'attraversamento del grafico delle dipendenze per questo alias inizia dal file *ClientApp/boot-server.ts:*

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=53)]

Nell'esempio angolare seguente, il file *ClientApp/boot-server.ts* utilizza la `createServerRenderer` funzione e `RenderResult` il tipo del pacchetto npm per configurare il `aspnet-prerendering` rendering del server tramite Node.js. Il markup HTML destinato al rendering sul lato server viene passato a una chiamata `Promise` di funzione resolve, di cui viene eseguito il wrapping in un oggetto JavaScript fortemente tipizzato. Il `Promise` significato dell'oggetto è che fornisce in modo asincrono il markup HTML alla pagina per l'inserimento nell'elemento segnaposto del DOM.

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-34,79-)]

### <a name="asp-prerender-data-tag-helper"></a>asp-prerender-data Tag Helper

Se associato a `asp-prerender-module` Tag Helper, l'helper `asp-prerender-data` Tag può essere utilizzato per passare informazioni contestuali dalla visualizzazione Razor al JavaScript lato server. Ad esempio, il markup seguente `main-server` passa i dati utente al modulo:For example, the following markup passes user data to the module:

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/Home/Index.cshtml?range=9-12)]

L'argomento received `UserName` viene serializzato utilizzando il serializzatore `params.data` JSON incorporato e viene archiviato nell'oggetto. Nell'esempio angolare seguente, i dati vengono utilizzati per `h1` creare un messaggio di saluto personalizzato all'interno di un elemento:In the following Angular example, the data is used to construct a personalized greeting within an element:

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-21,38-52,79-)]

I nomi delle proprietà passati negli helper Tag sono rappresentati con la notazione **PascalCase.Property** names passed in Tag Helpers are represented with PascalCase notation. A differenza di JavaScript, in cui gli stessi nomi di proprietà sono rappresentati con **camelCase**. La configurazione di serializzazione JSON predefinita è responsabile di questa differenza.

Per espandere l'esempio di codice precedente, i dati possono essere `globals` passati dal `resolve` server alla visualizzazione idratando la proprietà fornita alla funzione:

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-21,57-77,79-)]

La `postList` matrice definita `globals` all'interno dell'oggetto è `window` associata all'oggetto globale del browser. Questo sollevamento variabile in ambito globale elimina la duplicazione dello sforzo, in particolare per quanto riguarda il caricamento degli stessi dati una volta sul server e di nuovo sul client.

![variabile globale postList associata all'oggetto finestra](spa-services/_static/global_variable.png)

## <a name="webpack-dev-middleware"></a>Middleware per sviluppatori di webpack

[Webpack Dev Middleware](https://webpack.js.org/guides/development/#using-webpack-dev-middleware) introduce un flusso di lavoro di sviluppo semplificato in base al quale Webpack crea risorse su richiesta. Il middleware compila e serve automaticamente le risorse lato client quando una pagina viene ricaricata nel browser. L'approccio alternativo consiste nel richiamare manualmente Webpack tramite lo script di compilazione npm del progetto quando viene modificata una dipendenza di terze parti o il codice personalizzato. Nell'esempio seguente viene illustrato uno script di compilazione npm nel file *package.json:An* npm build script in the package.json file is shown in the following example:

```json
"build": "npm run build:vendor && npm run build:custom",
```

### <a name="webpack-dev-middleware-prerequisites"></a>Prerequisiti del Middleware per il sviluppo Di Webpack

Installare il pacchetto [aspnet-webpack](https://www.npmjs.com/package/aspnet-webpack) npm:

```console
npm i -D aspnet-webpack
```

### <a name="webpack-dev-middleware-configuration"></a>Configurazione di Webpack Dev Middleware

Webpack Dev Middleware viene registrato nella pipeline delle richieste HTTP `Configure` tramite il codice seguente nel metodo del file *Startup.cs:*

[!code-csharp[](../client-side/spa-services/sample/SpaServicesSampleApp/Startup.cs?name=snippet_WebpackMiddlewareRegistration&highlight=4)]

Il `UseWebpackDevMiddleware` metodo di estensione deve essere chiamato `UseStaticFiles` prima di registrare l'hosting di file [statici](xref:fundamentals/static-files) tramite il metodo di estensione. Per motivi di sicurezza, registra il middleware solo quando l'app viene eseguita in modalità di sviluppo.

La proprietà del `output.publicPath` file *webpack.config.js* indica al `dist` middleware di controllare la cartella per le modifiche:

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=6,13-16)]

## <a name="hot-module-replacement"></a>Sostituzione modulo caldo

Pensate alla funzionalità [Hot Module Replacement](https://webpack.js.org/concepts/hot-module-replacement/) (HMR) di Webpack come a un'evoluzione di [Webpack Dev Middleware](#webpack-dev-middleware). HMR introduce tutti gli stessi vantaggi, ma semplifica ulteriormente il flusso di lavoro di sviluppo aggiornando automaticamente il contenuto della pagina dopo la compilazione delle modifiche. Non confondere questo con un aggiornamento del browser, che interferirebbe con lo stato corrente in memoria e la sessione di debug della SPA. C'è un collegamento live tra il servizio Webpack Dev Middleware e il browser, il che significa che le modifiche vengono inviate al browser.

### <a name="hot-module-replacement-prerequisites"></a>Prerequisiti per la sostituzione del modulo caldo

Installare il pacchetto [webpack-hot-middleware](https://www.npmjs.com/package/webpack-hot-middleware) npm:

```console
npm i -D webpack-hot-middleware
```

### <a name="hot-module-replacement-configuration"></a>Configurazione di sostituzione del modulo a caldo

Il componente HMR deve essere registrato nella pipeline `Configure` delle richieste HTTP di MVC nel metodo:

```csharp
app.UseWebpackDevMiddleware(new WebpackDevMiddlewareOptions {
    HotModuleReplacement = true
});
```

Come per [Webpack Dev Middleware](#webpack-dev-middleware), il `UseWebpackDevMiddleware` metodo `UseStaticFiles` di estensione deve essere chiamato prima del metodo di estensione. Per motivi di sicurezza, registra il middleware solo quando l'app viene eseguita in modalità di sviluppo.

Il file *webpack.config.js* `plugins` deve definire una matrice, anche se viene lasciata vuota:

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=6,25)]

Dopo aver caricato l'app nel browser, la scheda Console degli strumenti di sviluppo fornisce la conferma dell'attivazione HMR:

![Messaggio connesso di sostituzione del modulo a caldo](spa-services/_static/hmr_connected.png)

## <a name="routing-helpers"></a>Helper di routing

Nella maggior parte dei ASP.NET SP basati su Core, il routing sul lato client è spesso desiderato oltre al routing sul lato server. I sistemi di routing SPA e MVC possono funzionare in modo indipendente senza interferenze. C'è, tuttavia, un caso limite che pone sfide: identificare 404 risposte HTTP.

Si consideri lo scenario in `/some/page` cui viene utilizzato un percorso senza estensione di. Si supponga che la richiesta non corrisponda a una route lato server, ma il relativo modello corrisponde a una route lato client. Si consideri ora `/images/user-512.png`una richiesta in ingresso per , che in genere prevede di trovare un file di immagine sul server. Se il percorso della risorsa richiesto non corrisponde ad alcuna route sul lato server o&mdash;file statico, è improbabile che l'applicazione lato client gestisca in genere la restituzione di un codice di stato HTTP 404.

### <a name="routing-helpers-prerequisites"></a>Prerequisiti degli helper di routing

Installare il pacchetto npm di routing sul lato client. Utilizzo di Angolare come esempio:Using Angular as an example:

```console
npm i -S @angular/router
```

### <a name="routing-helpers-configuration"></a>Configurazione degli helper di routing

Un metodo `MapSpaFallbackRoute` di estensione `Configure` denominato viene utilizzato nel metodo:An extension method named is used in the method:

[!code-csharp[](../client-side/spa-services/sample/SpaServicesSampleApp/Startup.cs?name=snippet_MvcRoutingTable&highlight=7-9)]

Le route vengono valutate nell'ordine in cui sono configurate. Di conseguenza, la `default` route nell'esempio di codice precedente viene utilizzata prima per i criteri di ricerca.

## <a name="create-a-new-project"></a>Creare un nuovo progetto

I servizi JavaScript forniscono modelli di applicazione preconfigurati. SpaServices viene utilizzato in questi modelli in combinazione con framework e librerie diversi, ad esempio Angular, React e Redux.

Questi modelli possono essere installati tramite l'interfaccia della riga di comando di .NET Core eseguendo il comando seguente:

```dotnetcli
dotnet new --install Microsoft.AspNetCore.SpaTemplates::*
```

Viene visualizzato un elenco dei modelli SPA disponibili:

| Modelli                                 | Nome breve | Linguaggio | Tag        |
| ------------------------------------------| :--------: | :------: | :---------: |
| MVC ASP.NET Core con angular             | angular    | [C#]     | Web/MVC/SPA |
| MVC ASP.NET Core con React.js            | react      | [C#]     | Web/MVC/SPA |
| MVC ASP.NET Core con React.js e Redux  | reactredux | [C#]     | Web/MVC/SPA |

Per creare un nuovo progetto utilizzando uno dei modelli SPA, includere il **nome breve** del modello nel nuovo comando [dotnet.](/dotnet/core/tools/dotnet-new) Il comando seguente crea un'applicazione angolare con ASP.NET Core MVC configurato per il lato server:

```dotnetcli
dotnet new angular
```

### <a name="set-the-runtime-configuration-mode"></a>Impostare la modalità di configurazione di runtime

Esistono due modalità di configurazione di runtime principale:

* **Sviluppo**:
  * Include mappe di origine per semplificare il debug.
  * Non ottimizza il codice lato client per le prestazioni.
* **Produzione**:
  * Esclude le mappe di origine.
  * Ottimizza il codice lato client tramite l'aggregazione e la minimizzazione.

ASP.NET Core utilizza una `ASPNETCORE_ENVIRONMENT` variabile di ambiente denominata per archiviare la modalità di configurazione. Per ulteriori informazioni, consultate [Impostare l'ambiente.](xref:fundamentals/environments#set-the-environment)

### <a name="run-with-net-core-cli"></a>Esegui con l'interfaccia della riga di comando di .NET CoreRun with .NET Core CLI

Ripristinare i pacchetti NuGet e npm necessari eseguendo il comando seguente nella radice del progetto:

```dotnetcli
dotnet restore && npm i
```

Compilare ed eseguire l'applicazione:

```dotnetcli
dotnet run
```

L'applicazione viene avviata in localhost in base alla modalità di [configurazione runtime.](#set-the-runtime-configuration-mode) Navigando `http://localhost:5000` su nel browser viene visualizzata la pagina di destinazione.

### <a name="run-with-visual-studio-2017"></a>Eseguire con Visual Studio 2017

Aprire il file *con estensione csproj* generato dal [comando dotnet new.](/dotnet/core/tools/dotnet-new) I pacchetti NuGet e npm necessari vengono ripristinati automaticamente all'apertura del progetto. Questo processo di ripristino può richiedere alcuni minuti e l'applicazione è pronta per essere eseguita al termine. Fare clic sul pulsante verde Esegui o premere `Ctrl + F5`e il browser si apre sulla pagina di destinazione dell'applicazione. L'applicazione viene eseguita in localhost in base alla modalità di [configurazione runtime.](#set-the-runtime-configuration-mode)

## <a name="test-the-app"></a>Testare l'app

I modelli SpaServices sono preconfigurati per l'esecuzione di test sul lato client utilizzando [Karma](https://karma-runner.github.io/1.0/index.html) e [Jasmine](https://jasmine.github.io/). Jasmine è un framework di unit test popolare per JavaScript, mentre Karma è un test runner per quei test. Karma è configurato per funzionare con il [Webpack Dev Middleware](#webpack-dev-middleware) in modo che lo sviluppatore non è necessario arrestare ed eseguire il test ogni volta che vengono apportate modifiche. Sia che si tratti del codice in esecuzione sul test case o del test case stesso, il test viene eseguito automaticamente.

Utilizzando l'applicazione angolare come esempio, sono già stati forniti `CounterComponent` due test case Jasmine per il nel file *counter.component.spec.ts:*

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/app/components/counter/counter.component.spec.ts?range=15-28)]

Aprire il prompt dei comandi nella directory *ClientApp.* Eseguire il comando seguente:

```console
npm test
```

Lo script avvia il test runner Karma, che legge le impostazioni definite nel file *karma.conf.js.* Tra le altre impostazioni, il *karma.conf.js* identifica i `files` file di test da eseguire tramite la sua matrice:

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/test/karma.conf.js?range=4-5,8-11)]

## <a name="publish-the-app"></a>Pubblicare l'app

Vedere questo problema di GitHub per altre informazioni sulla pubblicazione in Azure.See this [GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/12474) for more information on publishing to Azure.

La combinazione degli asset sul lato client generati e degli elementi di base ASP.NET pubblicati in un pacchetto pronto per la distribuzione può essere complessa. Fortunatamente, SpaServices orchestra l'intero processo di pubblicazione `RunWebpack`con una destinazione MSBuild personalizzata denominata :

[!code-xml[](../client-side/spa-services/sample/SpaServicesSampleApp/SpaServicesSampleApp.csproj?range=31-45)]

La destinazione MSBuild ha le seguenti responsabilità:

1. Ripristinare i pacchetti npm.
1. Crea una build di livello di produzione delle risorse lato client di terze parti.
1. Creare una build di livello di produzione delle risorse lato client personalizzate.
1. Copiate le risorse generate dal Pacchetto Web nella cartella di pubblicazione.

La destinazione MSBuild viene richiamata durante l'esecuzione:The MSBuild target is invoked when running:

```dotnetcli
dotnet publish -c Release
```

## <a name="additional-resources"></a>Risorse aggiuntive

* [Documenti angolari](https://angular.io/docs)
