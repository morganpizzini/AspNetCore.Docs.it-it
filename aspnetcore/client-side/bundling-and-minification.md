---
title: Aggregare e minimizzare asset statici in ASP.NET Core
author: scottaddie
description: Informazioni su come ottimizzare le risorse statiche in un'applicazione Web ASP.NET Core applicando tecniche di aggregazione e minification.
ms.author: scaddie
ms.custom: mvc
ms.date: 04/15/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: client-side/bundling-and-minification
ms.openlocfilehash: de7c155189008e1f78bfb1eba062fcc86f9e4839
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/26/2020
ms.locfileid: "85401909"
---
# <a name="bundle-and-minify-static-assets-in-aspnet-core"></a>Aggregare e minimizzare asset statici in ASP.NET Core

Di [Scott Addie](https://twitter.com/Scott_Addie) e [David Pine](https://twitter.com/davidpine7)

Questo articolo illustra i vantaggi dell'applicazione di bundle e minification, incluso il modo in cui queste funzionalità possono essere usate con ASP.NET Core app Web.

## <a name="what-is-bundling-and-minification"></a>Che cos'è la creazione di bundle e minification

La creazione di bundle e minification sono due ottimizzazioni delle prestazioni distinte che è possibile applicare in un'app Web. Usati insieme, bundleing e minification migliorano le prestazioni riducendo il numero di richieste server e riducendo le dimensioni degli asset statici richiesti.

La creazione di bundle e minification migliora principalmente il tempo di caricamento della prima richiesta di pagina. Una volta richiesta una pagina Web, il browser memorizza nella cache gli asset statici (JavaScript, CSS e immagini). Di conseguenza, la creazione di bundle e minification non migliora le prestazioni quando si richiede la stessa pagina o pagine nello stesso sito che richiede le stesse risorse. Se l'intestazione Expires non è impostata correttamente negli asset e se non si usa la funzionalità di aggregazione e minification, l'euristica di aggiornamento del browser contrassegna gli asset obsoleti dopo alcuni giorni. Inoltre, il browser richiede una richiesta di convalida per ogni asset. In questo caso, la creazione di bundle e minification fornisce un miglioramento delle prestazioni anche dopo la prima richiesta di pagina.

### <a name="bundling"></a>Bundle

La creazione di bundle consente di combinare più file in un unico file. La creazione di bundle riduce il numero di richieste del server necessarie per il rendering di un asset Web, ad esempio una pagina Web. È possibile creare un numero qualsiasi di bundle individuali in modo specifico per CSS, JavaScript e così via. Un numero inferiore di file indica un minor numero di richieste HTTP dal browser al server o dal servizio che fornisce l'applicazione. Ciò comporta un miglioramento delle prime prestazioni di caricamento della pagina.

### <a name="minification"></a>Minimizzazione

Minification rimuove i caratteri non necessari dal codice senza alterare le funzionalità. Il risultato è una riduzione significativa delle dimensioni negli asset richiesti, ad esempio CSS, immagini e file JavaScript. Gli effetti collaterali comuni di minification includono l'abbreviazione di nomi di variabili a un carattere e la rimozione di commenti e spazi vuoti superflui.

Si consideri la funzione JavaScript seguente:

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/js/site.js)]

Minification riduce la funzione a quanto segue:

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/js/site.min.js)]

Oltre a rimuovere i commenti e gli spazi vuoti superflui, i nomi dei parametri e delle variabili seguenti sono stati rinominati come segue:

Originale | Ridenominazione
--- | :---:
`imageTagAndImageID` | `t`
`imageContext` | `a`
`imageElement` | `r`

## <a name="impact-of-bundling-and-minification"></a>Effetti della creazione di bundle e minification

Nella tabella seguente vengono descritte le differenze tra il caricamento individuale degli asset e l'utilizzo di bundle e minification:

Azione | Con B/M | Senza B/M | Modifica
--- | :---: | :---: | :---:
Richieste di file  | 7   | 18     | 157%
KB trasferiti | 156 | 264,68 | 70%
Tempo di caricamento (MS) | 885 | 2360   | 167%

I browser sono piuttosto dettagliati rispetto alle intestazioni delle richieste HTTP. La metrica totale dei byte inviati ha rilevato una riduzione significativa durante la creazione del bundle. Il tempo di caricamento Mostra un miglioramento significativo, tuttavia questo esempio è stato eseguito localmente. Quando si usa la creazione di bundle e minification con asset trasferiti in una rete, si ottengono maggiori vantaggi a livello di prestazioni.

## <a name="choose-a-bundling-and-minification-strategy"></a>Scegliere una strategia di raggruppamento e minification

I modelli di Razor progetto MVC e Pages forniscono una soluzione per la creazione di bundle e minification costituiti da un file di configurazione JSON. Gli strumenti di terze parti, ad [esempio l'attività](xref:client-side/using-grunt) Runner, eseguono le stesse attività con una maggiore complessità. Uno strumento di terze parti è un'ottima soluzione quando il flusso di lavoro di sviluppo richiede l'elaborazione oltre la creazione di bundle e minification &mdash; , ad esempio l'ottimizzazione delle immagini e dei pelucchi. Con la creazione di bundle e minification in fase di progettazione, i file minimizzati vengono creati prima della distribuzione dell'app. La creazione di bundle e minimizzazione prima della distribuzione offre il vantaggio di ridurre il carico del server. Tuttavia, è importante riconoscere che la creazione di bundle in fase di progettazione e minification aumenta la complessità della compilazione e funziona solo con i file statici.

## <a name="configure-bundling-and-minification"></a>Configurare la creazione di bundle e minification

::: moniker range="<= aspnetcore-2.0"

In ASP.NET Core 2,0 o versioni precedenti, i modelli di progetto MVC e Razor pages forniscono una *bundleconfig.jsnel* file di configurazione che definisce le opzioni per ogni bundle:

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

In ASP.NET Core 2,1 o versioni successive aggiungere un nuovo file JSON, denominato *bundleconfig.json*, alla radice del progetto MVC o Razor pages. Includere nel file il codice JSON seguente come punto di partenza:

::: moniker-end

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/bundleconfig.json)]

Il *bundleconfig.jsnel* file definisce le opzioni per ogni bundle. Nell'esempio precedente viene definita una singola configurazione di bundle per i file JavaScript (*wwwroot/JS/site.js*) e StyleSheet (*wwwroot/CSS/site. CSS*) personalizzati.

Le opzioni di configurazione possibili sono:

* `outputFileName`: Nome del file di bundle da restituire. Può contenere un percorso relativo dal *bundleconfig.jssul* file. **Obbligatorio**
* `inputFiles`: Matrice di file da raggruppare. Si tratta di percorsi relativi del file di configurazione. **facoltativo**, * un valore vuoto restituisce un file di output vuoto. sono supportati i modelli [glob](https://www.tldp.org/LDP/abs/html/globbingref.html) .
* `minify`: Opzioni minification per il tipo di output. **facoltativo**, *valore predefinito `minify: { enabled: true }` -*
  * Le opzioni di configurazione sono disponibili per ogni tipo di file di output.
    * [Minifier CSS](https://github.com/madskristensen/BundlerMinifier/wiki/cssminifier)
    * [Minifier JavaScript](https://github.com/madskristensen/BundlerMinifier/wiki/JavaScript-Minifier-settings)
    * [Minifier HTML](https://github.com/madskristensen/BundlerMinifier/wiki)
* `includeInProject`: Flag che indica se aggiungere i file generati al file di progetto. **facoltativo**, *valore predefinito-false*
* `sourceMap`: Flag che indica se generare una mappa di origine per il file in bundle. **facoltativo**, *valore predefinito-false*
* `sourceMapRootPath`: Percorso radice per l'archiviazione del file di mapping di origine generato.

## <a name="add-files-to-workflow"></a>Aggiunta di file al flusso di lavoro

Si consideri un esempio in cui viene aggiunto un file *CSS personalizzato* aggiuntivo simile al seguente:

[!code-css[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/css/custom.css)]

Per minimizzare *Custom. CSS* e aggregarlo con *site. CSS* in un file *site. min. CSS* , aggiungere il percorso relativo *bundleconfig.jsin*:

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/bundleconfig2.json?highlight=6)]

> [!NOTE]
> In alternativa, è possibile usare il modello glob seguente:
>
> ```json
> "inputFiles": ["wwwroot/**/!(*.min).css" ]
> ```
>
> Questo modello glob corrisponde a tutti i file CSS ed esclude il modello di file minimizzati.

Compilare l'applicazione. Aprire *site. min. CSS* e notare che il contenuto di *Custom. CSS* viene aggiunto alla fine del file.

## <a name="environment-based-bundling-and-minification"></a>Creazione di bundle e minification basati su ambiente

Come procedura consigliata, i file in bundle e minimizzati dell'app devono essere usati in un ambiente di produzione. Durante lo sviluppo, i file originali rendono più semplice il debug dell'app.

Specificare i file da includere nelle pagine usando l' [Helper tag di ambiente](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) nelle visualizzazioni. L'helper tag di ambiente esegue il rendering del relativo contenuto solo quando è in esecuzione in [ambienti](xref:fundamentals/environments)specifici.

Il `environment` tag seguente esegue il rendering dei file CSS non elaborati durante l'esecuzione nell' `Development` ambiente:

::: moniker range=">= aspnetcore-2.0"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=21-24)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=9-12)]

::: moniker-end

Il `environment` tag seguente esegue il rendering dei file CSS in bundle e minimizzati quando è in esecuzione in un ambiente diverso da `Development` . Ad esempio, in esecuzione in `Production` o viene `Staging` attivato il rendering di questi fogli di stile:

::: moniker range=">= aspnetcore-2.0"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=5&range=25-30)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=13-18)]

::: moniker-end

## <a name="consume-bundleconfigjson-from-gulp"></a>Utilizzare bundleconfig.jsda Gulp

Ci sono casi in cui il flusso di lavoro di aggregazione e minification di un'app richiede un'elaborazione aggiuntiva. Gli esempi includono l'ottimizzazione delle immagini, la memorizzazione nella cache e l'elaborazione delle risorse della rete CDN. Per soddisfare questi requisiti, è possibile convertire il flusso di lavoro di aggregazione e minification per usare Gulp.

### <a name="manually-convert-the-bundling-and-minification-workflow-to-use-gulp"></a>Convertire manualmente il flusso di lavoro di aggregazione e minification per usare Gulp

Aggiungere un *package.jsnel* file, con il codice seguente `devDependencies` , alla radice del progetto:

> [!WARNING]
> Il `gulp-uglify` modulo non supporta ECMAScript (es) 2015/ES6 e versioni successive. Installare [Gulp-Terser](https://www.npmjs.com/package/gulp-terser) invece di `gulp-uglify` per usare ES2015/ES6 o versione successiva.

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/package.json?range=5-13)]

Installare le dipendenze eseguendo il comando seguente allo stesso livello di *package.jsin*:

```console
npm i
```

Installare l'interfaccia della riga di comando di Gulp come dipendenza globale:

```console
npm i -g gulp-cli
```

Copiare il file di *gulpfile.js* seguente nella radice del progetto:

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/gulpfile.js?range=1-11,14-)]

### <a name="run-gulp-tasks"></a>Eseguire attività Gulp

Per attivare l'attività minification di Gulp prima della compilazione del progetto in Visual Studio, aggiungere la [destinazione MSBuild](/visualstudio/msbuild/msbuild-targets) seguente al file *. csproj:

[!code-xml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/BuildBundlerMinifierApp.csproj?range=14-16)]

In questo esempio, tutte le attività definite nella `MyPreCompileTarget` destinazione vengono eseguite prima della `Build` destinazione predefinita. Viene visualizzato un output simile al seguente nella finestra di output di Visual Studio:

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
