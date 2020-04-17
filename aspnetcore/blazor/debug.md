---
title: Debug ASP.NET Blazor base WebAssembly
author: guardrex
description: Scopri come Blazor eseguire il debug delle app.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/16/2020
no-loc:
- Blazor
- SignalR
uid: blazor/debug
ms.openlocfilehash: 8b63444ba5c8cd45e64e722c8978ba4e6d90af36
ms.sourcegitcommit: 77c046331f3d633d7cc247ba77e58b89e254f487
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81488748"
---
# <a name="debug-aspnet-core-opno-locblazor-webassembly"></a>Debug ASP.NET Blazor base WebAssembly

[Daniel Roth](https://github.com/danroth27)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

BlazorLe app WebAssembly possono essere sottoposte a debug utilizzando gli strumenti di sviluppo del browser nei browser basati su Chromium (Edge/Chrome).  In alternativa, è possibile eseguire il debug dell'app usando Visual Studio o Visual Studio Code.

Gli scenari disponibili includono:

* Impostare e rimuovere i punti di interruzione.
* Eseguire l'app con il supporto per il debug in Visual Studio e Visual Studio Code (supporto<kbd>F5).</kbd>
* Singolo passaggio (<kbd>F10</kbd>) tramite il codice.
* Riprendere l'esecuzione del codice con <kbd>F8</kbd> in un browser o <kbd>F5</kbd> in Visual Studio o Visual Studio Code.
* Nella visualizzazione *Variabili locali* osservare i valori delle variabili locali.
* Vedere lo stack di chiamate, incluse le catene di chiamate che vanno da JavaScript in .NET e da .NET a JavaScript.

Per ora, *non è possibile:*

* Esaminare gli array.
* Passare il mouse per esaminare i membri.
* Eseguire il debug all'azione di debug o all'esterno del codice gestito.
* Disporre del supporto completo per l'ispezione dei tipi di valore.
* Interruzione delle eccezioni non gestite.
* Raggiungi i punti di interruzione durante l'avvio dell'app.
* Eseguire il debug di un'app con un service worker.

Continueremo a migliorare l'esperienza di debug nelle versioni future.

## <a name="prerequisites"></a>Prerequisiti

Il debug richiede uno dei seguenti browser:

* Microsoft Edge (versione 80 o successiva)
* Google Chrome (versione 70 o successiva)

## <a name="enable-debugging-for-visual-studio-and-visual-studio-code"></a>Abilitare il debug per Visual Studio e Visual Studio CodeEnable debugging for Visual Studio and Visual Studio Code

Il debug viene abilitato automaticamente per i nuovi progetti creati utilizzando Blazor il modello di progetto WebAssembly di ASP.NET Core 3.2 Preview 3 o versione successiva[(la versione corrente è 3.2 Preview 4](xref:blazor/get-started)).

Per abilitare il Blazor debug per un'app WebAssembly esistente, aggiornare il `inspectUri` file *launchSettings.json* nel progetto di avvio in modo da includere la proprietà seguente in ogni profilo di avvio:

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/_framework/debug/ws-proxy?browser={browserInspectUri}"
```

Una volta aggiornato, il file launchSettings.json dovrebbe essere simile all'esempio seguente:Once updated, the *launchSettings.json* file should look similar to the following example:

[!code-json[](debug/launchSettings.json?highlight=14,22)]

La `inspectUri` proprietà:

* Consente all'IDE di rilevare Blazor che l'app è un'app WebAssembly.
* Indica all'infrastruttura di debug degli script Blazordi connettersi al browser tramite il proxy di debug di 's.

## <a name="visual-studio"></a>Visual Studio

Per eseguire Blazor il debug di un'app WebAssembly in Visual Studio:

1. Assicurarsi di aver [installato la versione di anteprima più recente di Visual Studio 2019 16.6](https://visualstudio.com/preview) (anteprima 2 o successiva).
1. Creare una nuova app Blazor WebAssembly ospitata ASP.NET Core.
1. Premere <kbd>F5</kbd> per eseguire l'app nel debugger.
1. Impostare un punto di interruzione `IncrementCount` in *Counter.razor* nel metodo.
1. Passare alla scheda **Contatore** e selezionare il pulsante per raggiungere il punto di interruzione:

   ![Contatore di debug](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-counter.png)

1. Scopri il valore `currentCount` del campo nella finestra della gente del posto:

   ![Visualizza la gente del posto](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-locals.png)

1. Premere <kbd>F5</kbd> per continuare l'esecuzione.

Durante il Blazor debug dell'app WebAssembly, è anche possibile eseguire il debug del codice server:

1. Impostare un punto di interruzione nella `OnInitializedAsync`pagina *FetchData.razor* in .
1. Impostare un `WeatherForecastController` punto `Get` di interruzione nel nel metodo di azione.
1. Passare alla scheda **Recupera dati** per raggiungere `FetchData` il primo punto di interruzione nel componente appena prima di emettere una richiesta HTTP al server:

   ![Debug Fetch Data](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-fetch-data.png)

1. Premere <kbd>F5</kbd> per continuare l'esecuzione e quindi `WeatherForecastController`premere il punto di interruzione sul server nel:

   ![Server di debug](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-server.png)

1. Premere di nuovo <kbd>F5</kbd> per consentire l'esecuzione continuare e visualizzare la tabella delle previsioni meteo renderizzata.

## <a name="visual-studio-code"></a>Visual Studio Code

Per eseguire Blazor il debug di un'app WebAssembly nel codice di Visual Studio:
 
1. Installare [l'estensione di C,](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) e l'estensione [JavaScript Debugger (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) con `debug.javascript.usePreview` impostato su `true`.

   ![Estensioni](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-extensions.png)

   ![Debugger di anteprima JS](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-js-use-preview.png)

1. Aprire un'app WebAssembly esistente Blazor con il debug abilitato.

   * Se viene visualizzata la seguente notifica che è necessaria un'installazione aggiuntiva per abilitare il debug, verificare che siano attivate le estensioni corrette e che sia abilitato il debug dell'anteprima JavaScript, quindi ricaricare la finestra:

     ![Configurazione aggiuntiva riprogrammata](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-additional-setup.png)

   * Viene offerta una notifica per aggiungere le risorse necessarie all'app per la compilazione e il debug. Selezionare **Sì**:

     ![Aggiungere le risorse richieste](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-required-assets.png)

1. L'avvio dell'app nel debugger è un processo in due passaggi:Starting the app in the debugger is a two-step process:

   1\. **Innanzitutto,** avviare l'app utilizzando la configurazione di avvio **di .NET Core (Standalone).Blazor **

   2\. **Dopo l'avvio dell'app,** avviare il browser utilizzando l'assembly Web di **debug Blazor ** di .NET Core nella configurazione di avvio di Chrome (richiede Chrome). Per utilizzare Edge invece `type` di Chrome, modificare la configurazione di `pwa-chrome` avvio in *.vscode/launch.json* da a `pwa-msedge`.

1. Impostare un `IncrementCount` punto di `Counter` interruzione nel metodo nel componente, quindi selezionare il pulsante per raggiungere il punto di interruzione:Set a breakpoint in the method in the component and then select the button to hit the breakpoint:

   ![Debug Counter in VS Code](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-debug-counter.png)

## <a name="debug-in-the-browser"></a>Eseguire il debug nel browserDebug in the browser

1. Eseguire una build di debug dell'app nell'ambiente di sviluppo.

1. <kbd>Premete Maiusc</kbd>+<kbd>Alt</kbd>+<kbd>D</kbd>.

1. Il browser deve essere eseguito con il debug remoto abilitato. Se il debug remoto è disabilitato, viene generata una pagina di errore **Impossibile trovare la scheda del browser di cui** è possibile eseguire il debug. La pagina di errore contiene le istruzioni per l'esecuzione del browser con la porta di debug aperta in modo che il Blazor proxy di debug possa connettersi all'app. *Chiudere tutte le istanze del browser* e riavviare il browser come indicato.

Una volta che il browser è in esecuzione con il debug remoto abilitato, il tasto di scelta rapida di debug apre una nuova scheda del debugger. Dopo qualche istante, la scheda **Origini** mostra un elenco degli assembly .NET nell'app. Espandere ogni assembly e trovare i file di origine *con estensione cs*/*Razor* disponibili per il debug. Impostare i punti di interruzione, tornare alla scheda dell'app e i punti di interruzione vengono raggiunti quando viene eseguito il codice. Dopo aver raggiunto un punto di interruzione, il codice viene sottoposto a un singolo passaggio (<kbd>F10</kbd>) tramite il codice o riprendere (<kbd>F8</kbd>) l'esecuzione del codice normalmente.

Blazorfornisce un proxy di debug che implementa il [protocollo Chrome DevTools](https://chromedevtools.github.io/devtools-protocol/) e aumenta il protocollo con . Informazioni specifiche di NET. Quando si preme Blazor il debug dei tasti di scelta rapida, punta Chrome DevTools al proxy. Il proxy si connette alla finestra del browser che si sta cercando di eseguire il debug (da qui la necessità di abilitare il debug remoto).

## <a name="browser-source-maps"></a>Mappe sorgente del browser

Le mappe di origine del browser consentono al browser di eseguire il mapping dei file compilati ai file di origine originali e vengono comunemente utilizzate per il debug sul lato client. Tuttavia, Blazor al momento non esegue il mapping di C , direttamente a JavaScript/WASM. Al Blazor contrario, l'interpretazione IL all'interno del browser, pertanto le mappe di origine non sono rilevanti.

## <a name="troubleshoot"></a>Risolvere problemi

Se si verificano errori, il suggerimento seguente può essere utile:

Nella scheda **Debugger** aprire gli strumenti di sviluppo nel browser. Nella console, `localStorage.clear()` eseguire per rimuovere eventuali punti di interruzione.
