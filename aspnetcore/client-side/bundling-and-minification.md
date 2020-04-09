---
title: Bundle e minimifico le risorse statiche in ASP.NET Core
author: scottaddie
description: Informazioni su come ottimizzare le risorse statiche in un'applicazione Web ASP.NET Core applicando tecniche di raggruppamento e minimizzazione.
ms.author: scaddie
ms.custom: mvc
ms.date: 06/17/2019
uid: client-side/bundling-and-minification
ms.openlocfilehash: a7a5c40d6c31c4416212c02c1b491dd794f2a1d3
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "78658270"
---
# <a name="bundle-and-minify-static-assets-in-aspnet-core"></a>Bundle e minimifico le risorse statiche in ASP.NET Core

Di [Scott Addie](https://twitter.com/Scott_Addie) e [David Pine](https://twitter.com/davidpine7)

In questo articolo vengono illustrati i vantaggi dell'applicazione di raggruppamento e minimizzazione, incluso il modo in cui queste funzionalità possono essere utilizzate con le app Web di base di ASP.NET.

## <a name="what-is-bundling-and-minification"></a>Che cos'è l'aggregazione e la minimizzazione

L'aggregazione e la minimizzazione sono due ottimizzazioni delle prestazioni distinte che è possibile applicare in un'app Web. Utilizzati insieme, l'aggregazione e la minimizzazione migliorano le prestazioni riducendo il numero di richieste del server e riducendo le dimensioni degli asset statici richiesti.

L'aggregazione e la minimizzazione migliorano principalmente il tempo di caricamento della prima richiesta di pagina. Una volta richiesta una pagina Web, il browser memorizza nella cache le risorse statiche (JavaScript, CSS e immagini). Di conseguenza, l'aggregazione e la minimizzazione non migliorano le prestazioni quando si richiede la stessa pagina, o pagine, sullo stesso sito che richiede le stesse risorse. Se l'intestazione della scadenza non è impostata correttamente sulle risorse e se non vengono utilizzate le caratteristiche di raggruppamento e minimizzazione, l'euristica di aggiornamento del browser contrassegna le risorse come obsolete dopo alcuni giorni. Inoltre, il browser richiede una richiesta di convalida per ogni asset. In questo caso, l'aggregazione e la minimizzazione forniscono un miglioramento delle prestazioni anche dopo la richiesta della prima pagina.

### <a name="bundling"></a>Impacchettare

La creazione di bundle consente di combinare più file in un unico file. L'aggregazione riduce il numero di richieste del server necessarie per eseguire il rendering di un asset Web, ad esempio una pagina Web. È possibile creare un numero qualsiasi di singoli bundle specificamente per CSS, JavaScript, ecc. Meno file significa meno richieste HTTP dal browser al server o dal servizio che fornisce l'applicazione. Ciò si traduce in un miglioramento delle prestazioni di caricamento della prima pagina.

### <a name="minification"></a>Minimizzazione

La minimizzazione rimuove i caratteri non necessari dal codice senza alterare la funzionalità. Il risultato è una riduzione significativa delle dimensioni delle risorse richieste (ad esempio CSS, immagini e file JavaScript). Gli effetti collaterali comuni della minimizzazione includono l'accorciamento dei nomi delle variabili a un carattere e la rimozione di commenti e spazi vuoti non necessari.

Si consideri la seguente funzione JavaScript:

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/js/site.js)]

La minimizzazione riduce la funzione al seguente:

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/js/site.min.js)]

Oltre a rimuovere i commenti e gli spazi vuoti non necessari, i seguenti nomi di parametri e variabili sono stati rinominati come segue:

Originale | Ridenominazione
--- | :---:
`imageTagAndImageID` | `t`
`imageContext` | `a`
`imageElement` | `r`

## <a name="impact-of-bundling-and-minification"></a>Impatto dell'aggregazione e della minimizzazione

La tabella seguente illustra le differenze tra il caricamento individuale delle attività e l'utilizzo dell'aggregazione e della minimizzazione:

Azione | Con B/M | Senza B/M | Modifica
--- | :---: | :---: | :---:
Richieste di file  | 7   | 18     | 157%
KB trasferito | 156 | 264.68 | 70%
Tempo di caricamento (ms) | 885 | 2360   | 167%

I browser sono piuttosto dettagliati per quanto riguarda le intestazioni di richiesta HTTP. La metrica dei byte totali inviati ha registrato una riduzione significativa durante l'aggregazione. Il tempo di caricamento mostra un miglioramento significativo, tuttavia questo esempio è stato eseguito localmente. Maggiori miglioramenti delle prestazioni si realizzano quando si utilizza l'aggregazione e la minimizzazione con le attività trasferite in rete.

## <a name="choose-a-bundling-and-minification-strategy"></a>Scegliere una strategia di raggruppamento e minimizzazione

I modelli di progetto MVC e Razor Pages forniscono una soluzione predefinita per l'aggregazione e la minimizzazione costituita da un file di configurazione JSON. Strumenti di terze parti, come il corridore di attività [Grunt,](xref:client-side/using-grunt) eseguire le stesse attività con un po 'più di complessità. Uno strumento di terze parti è ideale quando il flusso di lavoro&mdash;di sviluppo richiede l'elaborazione oltre l'aggregazione e la minimizzazione, ad esempio il linting e l'ottimizzazione delle immagini. Utilizzando l'aggregazione e la minimizzazione in fase di progettazione, i file minimizzati vengono creati prima della distribuzione dell'app. L'aggregazione e la minimizzazione prima della distribuzione offrono il vantaggio di ridurre il carico del server. Tuttavia, è importante riconoscere che l'aggregazione e la minimizzazione in fase di progettazione aumenta la complessità di compilazione e funziona solo con i file statici.

## <a name="configure-bundling-and-minification"></a>Configurare l'aggregazione e la minimizzazione

::: moniker range="<= aspnetcore-2.0"

In ASP.NET Core 2.0 o versioni precedenti, i modelli di progetto MVC e Razor Pages forniscono un file di configurazione bundleconfig.json che definisce le opzioni per ogni bundle:In the Example 2.0 or earlier, the MVC and Razor Pages project templates provide a *bundleconfig.json* configuration file that defines the options for each bundle:

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

In ASP.NET Core 2.1 o versioni successive aggiungere un nuovo file JSON, denominato *bundleconfig.json*, alla radice del progetto MVC o Razor Pages. Includere il codice JSON seguente nel file come punto di partenza:Include the following JSON in that file as a starting point:

::: moniker-end

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/bundleconfig.json)]

Il file *bundleconfig.json* definisce le opzioni per ogni bundle. Nell'esempio precedente viene definita una singola configurazione bundle per i file JavaScript (*wwwroot/js/site.js*) e di foglio di stile (*wwwroot/css/site.css*).

Le opzioni di configurazione possibili sono:

* `outputFileName`: il nome del file del pacchetto da restituire. Può contenere un percorso relativo dal file *bundleconfig.json.* **Obbligatorio**
* `inputFiles`: una serie di file da raggruppare. Si tratta di percorsi relativi al file di configurazione. **facoltativo**, un valore vuoto restituisce un file di output vuoto. sono supportati i modelli [di globbing.](https://www.tldp.org/LDP/abs/html/globbingref.html)
* `minify`: le opzioni di minimizzazione per il tipo di output. **facoltativo**, *predefinito `minify: { enabled: true }` -*
  * Le opzioni di configurazione sono disponibili per ogni tipo di file di output.
    * [CSS Minifier](https://github.com/madskristensen/BundlerMinifier/wiki/cssminifier)
    * [Classificatore JavaScript](https://github.com/madskristensen/BundlerMinifier/wiki/JavaScript-Minifier-settings)
    * [HTML Minifier](https://github.com/madskristensen/BundlerMinifier/wiki)
* `includeInProject`: flag che indica se aggiungere i file generati al file di progetto. **facoltativo**, *default - false*
* `sourceMap`: flag che indica se generare una mappa di origine per il file in bundle. **facoltativo**, *default - false*
* `sourceMapRootPath`: percorso radice per l'archiviazione del file di mappa di origine generato.

## <a name="build-time-execution-of-bundling-and-minification"></a>Esecuzione in fase di compilazione di bundling e minimizzazione

Il pacchetto [BuildBundlerMinifier](https://www.nuget.org/packages/BuildBundlerMinifier/) NuGet consente l'esecuzione di raggruppamento e minimizzazione in fase di compilazione. Il pacchetto inserisce [destinazioni MSBuild](/visualstudio/msbuild/msbuild-targets) che vengono eseguite in fase di compilazione e pulizia. Il file *bundleconfig.json* viene analizzato dal processo di compilazione per produrre i file di output in base alla configurazione definita.

> [!NOTE]
> BuildBundlerMinifier appartiene a un progetto basato sulla community su GitHub per il quale Microsoft non fornisce alcun supporto. I problemi dovrebbero essere depositati [qui](https://github.com/madskristensen/BundlerMinifier/issues).

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Aggiungere il pacchetto *BuildBundlerMinifier* al progetto.

Compilare il progetto. Nella finestra Output viene visualizzato quanto segue:

```console
1>------ Build started: Project: BuildBundlerMinifierApp, Configuration: Debug Any CPU ------
1>
1>Bundler: Begin processing bundleconfig.json
1>  Minified wwwroot/css/site.min.css
1>  Minified wwwroot/js/site.min.js
1>Bundler: Done processing bundleconfig.json
1>BuildBundlerMinifierApp -> C:\BuildBundlerMinifierApp\bin\Debug\netcoreapp2.0\BuildBundlerMinifierApp.dll
========== Build: 1 succeeded, 0 failed, 0 up-to-date, 0 skipped ==========
```

Pulire il progetto. Nella finestra Output viene visualizzato quanto segue:

```console
1>------ Clean started: Project: BuildBundlerMinifierApp, Configuration: Debug Any CPU ------
1>
1>Bundler: Cleaning output from bundleconfig.json
1>Bundler: Done cleaning output file from bundleconfig.json
========== Clean: 1 succeeded, 0 failed, 0 skipped ==========
```

# <a name="net-core-cli"></a>[Interfaccia della riga di comando di .NET Core](#tab/netcore-cli)

Aggiungere il pacchetto BuildBundlerMinifier al progetto:Add the *BuildBundlerMinifier* package to your project:

```dotnetcli
dotnet add package BuildBundlerMinifier
```

Se si usa ASP.NET Core 1.x, ripristinare il pacchetto appena aggiunto:

```dotnetcli
dotnet restore
```

Compilare il progetto:

```dotnetcli
dotnet build
```

Viene visualizzato quanto segue:

```console
Microsoft (R) Build Engine version 15.4.8.50001 for .NET Core
Copyright (C) Microsoft Corporation. All rights reserved.


    Bundler: Begin processing bundleconfig.json
    Bundler: Done processing bundleconfig.json
    BuildBundlerMinifierApp -> C:\BuildBundlerMinifierApp\bin\Debug\netcoreapp2.0\BuildBundlerMinifierApp.dll
```

Pulire il progetto:

```dotnetcli
dotnet clean
```

Viene visualizzato l'output seguente:

```console
Microsoft (R) Build Engine version 15.4.8.50001 for .NET Core
Copyright (C) Microsoft Corporation. All rights reserved.


  Bundler: Cleaning output from bundleconfig.json
  Bundler: Done cleaning output file from bundleconfig.json
```

---

## <a name="ad-hoc-execution-of-bundling-and-minification"></a>Esecuzione ad hoc di raggruppamento e minimizzazione

È possibile eseguire le attività di raggruppamento e minimizzazione su base ad hoc, senza compilare il progetto. Aggiungere il pacchetto BundlerMinifier.Core NuGet al progetto:Add the [BundlerMinifier.Core](https://www.nuget.org/packages/BundlerMinifier.Core/) NuGet package to your project:

[!code-xml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/BuildBundlerMinifierApp.csproj?range=10)]

> [!NOTE]
> BundlerMinifier.Core appartiene a un progetto basato sulla community su GitHub per il quale Microsoft non fornisce alcun supporto. I problemi dovrebbero essere depositati [qui](https://github.com/madskristensen/BundlerMinifier/issues).

Questo pacchetto estende l'interfaccia della riga di comando di .NET Core per includere lo strumento *dotnet-bundle.* Il comando seguente può essere eseguito nella finestra della console di gestione pacchetti (PMC) o in una shell dei comandi:

```dotnetcli
dotnet bundle
```

> [!IMPORTANT]
> NuGet Package Manager aggiunge dipendenze al file `<PackageReference />` con estensione csproj come nodi. Il `dotnet bundle` comando viene registrato con l'interfaccia `<DotNetCliToolReference />` della riga di comando di .NET Core solo quando viene utilizzato un nodo. Modificare di conseguenza il file con estensione csproj.

## <a name="add-files-to-workflow"></a>Aggiungere file al flusso di lavoro

Si consideri un esempio in cui viene aggiunto un file *custom.css* aggiuntivo simile al seguente:

[!code-css[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/css/custom.css)]

Per minify *custom.css* e impacchettarlo con *site.css* in un file *site.min.css,* aggiungete il percorso relativo a *bundleconfig.json*:

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/bundleconfig2.json?highlight=6)]

> [!NOTE]
> In alternativa, si potrebbe usare il seguente modello di globbing:
>
> ```json
> "inputFiles": ["wwwroot/**/!(*.min).css" ]
> ```
>
> Questo modello di globbing corrisponde a tutti i file CSS ed esclude il modello di file minified.

Compilare l'applicazione. Apri *site.min.css* e nota che il contenuto di *custom.css* viene aggiunto alla fine del file.

## <a name="environment-based-bundling-and-minification"></a>Raggruppamento e minimizzazione basati sull'ambiente

Come procedura consigliata, i file in bundle e minificati dell'app devono essere usati in un ambiente di produzione. Durante lo sviluppo, i file originali semplificano il debug dell'app.

Specificare i file da includere nelle pagine utilizzando [Environment Tag Helper](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) nelle visualizzazioni. Environment Tag Helper esegue il rendering del contenuto solo quando viene eseguito in [ambienti](xref:fundamentals/environments)specifici.

Il `environment` tag seguente esegue il rendering dei `Development` file CSS non elaborati durante l'esecuzione nell'ambiente:

::: moniker range=">= aspnetcore-2.0"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=21-24)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=9-12)]

::: moniker-end

Il `environment` tag seguente esegue il rendering dei file CSS in `Development`bundle e minificati durante l'esecuzione in un ambiente diverso da . Ad esempio, `Production` l'esecuzione o `Staging` l'attivazione del rendering di questi fogli di stile:

::: moniker range=">= aspnetcore-2.0"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=5&range=25-30)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=13-18)]

::: moniker-end

## <a name="consume-bundleconfigjson-from-gulp"></a>Usare bundleconfig.json da GulpConsume bundleconfig.json from Gulp

In alcuni casi il flusso di lavoro di aggregazione e minimizzazione di un'app richiede un'ulteriore elaborazione. Gli esempi includono l'ottimizzazione delle immagini, il busting della cache e l'elaborazione delle risorse CDN. Per soddisfare questi requisiti, è possibile convertire il flusso di lavoro di raggruppamento e minimizzazione per utilizzare Gulp.

### <a name="use-the-bundler--minifier-extension"></a>Usare l'estensione Bundler & Minifier

L'estensione & Minifier di Visual Studio Gestisce la conversione in Gulp.The Visual Studio [Bundler & Minifier](https://marketplace.visualstudio.com/items?itemName=MadsKristensen.BundlerMinifier) extension handles the conversion to Gulp.

> [!NOTE]
> Il Bundler & Minifier estensione appartiene a un progetto basato sulla comunità su GitHub per il quale Microsoft non fornisce alcun supporto. I problemi dovrebbero essere depositati [qui](https://github.com/madskristensen/BundlerMinifier/issues).

Fare clic con il pulsante destro del mouse sul file *bundleconfig.json* in Esplora soluzioni e selezionare **Bundler & Minifier** > **Converti in Gulp...**:

![Voce di menu di scelta rapida Converti in Gulp](../client-side/bundling-and-minification/_static/convert-to-gulp.png)

I file *gulpfile.js* e *package.json* vengono aggiunti al progetto. Vengono installati i pacchetti [npm](https://www.npmjs.com/) di supporto `devDependencies` elencati nella sezione del file *package.json.*

Eseguire il comando seguente nella finestra PMC per installare l'interfaccia della riga di comando Gulp come dipendenza globale:

```console
npm i -g gulp-cli
```

Il file *gulpfile.js* legge il file *bundleconfig.json* per gli input, gli output e le impostazioni.

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/gulpfile.js?range=1-12&highlight=10)]

### <a name="convert-manually"></a>Convertire manualmente

Se Visual Studio e/o l'estensione Bundler & Minifier non sono disponibili, eseguire la conversione manualmente.

Aggiungere un file *package.json,* con il seguente `devDependencies`, alla radice del progetto:

> [!WARNING]
> Il `gulp-uglify` modulo non supporta ECMAScript (ES) 2015 / ES6 e versioni successive. Installare [gulp-terser](https://www.npmjs.com/package/gulp-terser) `gulp-uglify` invece di utilizzare ES2015 / ES6 o versioni successive.

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/package.json?range=5-13)]

Installare le dipendenze eseguendo il comando seguente allo stesso livello di *package.json*:

```console
npm i
```

Installare l'interfaccia della riga di comando Gulp come dipendenza globale:Install the Gulp CLI as a global dependency:

```console
npm i -g gulp-cli
```

Copiare il file *gulpfile.js* riportato di seguito nella radice del progetto:

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/gulpfile.js?range=1-11,14-)]

### <a name="run-gulp-tasks"></a>Eseguire le attività GulpRun Gulp tasks

Per attivare l'attività di minimizzazione Gulp prima della compilazione del progetto in Visual Studio, aggiungere la destinazione [MSBuild](/visualstudio/msbuild/msbuild-targets) seguente al file con estensione csproj:

[!code-xml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/BuildBundlerMinifierApp.csproj?range=14-16)]

In questo esempio, tutte `MyPreCompileTarget` le attività definite `Build` all'interno della destinazione vengono eseguite prima della destinazione predefinita. Nell'output simile al seguente viene visualizzato nella finestra Output di Visual Studio:

```console
1>------ Build started: Project: BuildBundlerMinifierApp, Configuration: Debug Any CPU ------
1>BuildBundlerMinifierApp -> C:\BuildBundlerMinifierApp\bin\Debug\netcoreapp2.0\BuildBundlerMinifierApp.dll
1>[14:17:49] Using gulpfile C:\BuildBundlerMinifierApp\gulpfile.js
1>[14:17:49] Starting 'min:js'...
1>[14:17:49] Starting 'min:css'...
1>[14:17:49] Starting 'min:html'...
1>[14:17:49] Finished 'min:js' after 83 ms
1>[14:17:49] Finished 'min:css' after 88 ms
========== Build: 1 succeeded, 0 failed, 0 up-to-date, 0 skipped ==========
```

## <a name="additional-resources"></a>Risorse aggiuntive

* [Usare Grunt](xref:client-side/using-grunt)
* [Usare più ambienti](xref:fundamentals/environments)
* [Helper tag](xref:mvc/views/tag-helpers/intro)
