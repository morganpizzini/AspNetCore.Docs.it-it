---
title: Debug ASP.NET Core Blazor Webassembly
author: guardrex
description: Informazioni su come eseguire il debug delle Blazor app.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/31/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/debug
ms.openlocfilehash: 193dc656c2ee0154f0ae534bc00f8dc29bab3258
ms.sourcegitcommit: cd73744bd75fdefb31d25ab906df237f07ee7a0a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2020
ms.locfileid: "84239214"
---
# <a name="debug-aspnet-core-blazor-webassembly"></a>Debug ASP.NET Core Blazor Webassembly

[Daniel Roth](https://github.com/danroth27)

BlazorÈ possibile eseguire il debug delle app webassembly usando gli strumenti di sviluppo del browser nei browser basati su cromo (Edge/Chrome). In alternativa, è possibile eseguire il debug dell'app usando Visual Studio o Visual Studio Code.

Gli scenari disponibili includono:

* Impostare e rimuovere punti di interruzione.
* Eseguire l'app con supporto per il debug in Visual Studio e Visual Studio Code (supporto<kbd>F5</kbd> ).
* Singolo passaggio (<kbd>F10</kbd>) tramite il codice.
* Riprendere l'esecuzione del codice con <kbd>F8</kbd> in un browser o <kbd>F5</kbd> in Visual Studio o Visual Studio Code.
* Nella visualizzazione variabili *locali* osservare i valori delle variabili locali.
* Vedere lo stack di chiamate, incluse le catene di chiamate che passano da JavaScript a .NET e da .NET a JavaScript.

Per il momento *non è possibile*:

* Interrompi in corrispondenza di eccezioni non gestite.
* Raggiunge i punti di interruzione durante l'avvio dell'app.

Si continuerà a migliorare l'esperienza di debug nelle prossime versioni.

## <a name="prerequisites"></a>Prerequisiti

Il debug richiede uno dei seguenti browser:

* Microsoft Edge (versione 80 o successiva)
* Google Chrome (versione 70 o successiva)

## <a name="enable-debugging-for-visual-studio-and-visual-studio-code"></a>Abilitare il debug per Visual Studio e Visual Studio Code

Per abilitare il debug per un' Blazor app webassembly esistente, aggiornare il file *launchSettings. JSON* nel progetto di avvio per includere la `inspectUri` proprietà seguente in ogni profilo di avvio:

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/_framework/debug/ws-proxy?browser={browserInspectUri}"
```

Al termine dell'aggiornamento, il file *launchSettings. JSON* dovrebbe avere un aspetto simile all'esempio seguente:

[!code-json[](debug/launchSettings.json?highlight=14,22)]

`inspectUri`Proprietà:

* Consente all'IDE di rilevare che l'app è un' Blazor app webassembly.
* Indica all'infrastruttura di debug degli script di connettersi al browser tramite il Blazor proxy di debug.

I valori segnaposto per i protocolli WebSockets ( `wsProtocol` ), host ( `url.hostname` ), Port ( `url.port` ) e Inspector URI nel browser avviato ( `browserInspectUri` ) sono forniti dal Framework.

## <a name="visual-studio"></a>Visual Studio

Per eseguire il debug di un' Blazor app webassembly in Visual Studio:

1. Creare una nuova app ASP.NET Core Blazor webassembly ospitata.
1. Premere <kbd>F5</kbd> per eseguire l'app nel debugger.
1. Impostare un punto di interruzione in *Counter. Razor* nel `IncrementCount` metodo.
1. Passare alla scheda **contatore** e selezionare il pulsante per raggiungere il punto di interruzione:

   ![Contatore debug](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-counter.png)

1. Verificare il valore del `currentCount` campo nella finestra variabili locali:

   ![Visualizza variabili locali](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-locals.png)

1. Premere <kbd>F5</kbd> per continuare l'esecuzione.

Quando si esegue il debug dell' Blazor app webassembly, è anche possibile eseguire il debug del codice del server:

1. Impostare un punto di interruzione nella pagina *fetchData. Razor* in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> .
1. Impostare un punto di interruzione in `WeatherForecastController` nel `Get` metodo di azione.
1. Passare alla scheda **Recupera dati** per raggiungere il primo punto di interruzione nel `FetchData` componente appena prima di eseguire una richiesta HTTP al server:

   ![Eseguire il debug dei dati di recupero](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-fetch-data.png)

1. Premere <kbd>F5</kbd> per continuare l'esecuzione e quindi raggiungere il punto di interruzione sul server nell' `WeatherForecastController` :

   ![Server di debug](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-server.png)

1. Premere di nuovo <kbd>F5</kbd> per consentire l'esecuzione continua e visualizzare la tabella delle previsioni meteorologiche sottoposta a rendering.

<a id="vscode"></a>

## <a name="visual-studio-code"></a>Visual Studio Code

Per eseguire il debug di un' Blazor app webassembly in Visual Studio Code:
 
Installare l' [estensione C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) e l'estensione del [debugger JavaScript (notturno)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) con `debug.javascript.usePreview` impostato su `true` .

![Estensioni](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-extensions.png)

![Debugger di anteprima JS](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-js-use-preview.png)

### <a name="debug-standalone-blazor-webassembly"></a>Debug del Blazor webassembly autonomo

1. Aprire l' Blazor app webassembly autonoma in vs code.

   Se viene visualizzata la notifica seguente, è necessaria un'installazione aggiuntiva per abilitare il debug:
   
   * Verificare che siano installate le estensioni corrette.
   * Verificare che l'anteprima JavaScript sia abilitata.
   * Ricaricare la finestra.

   ![Installazione aggiuntiva richiesta](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-additional-setup.png)

1. Avviare il debug usando il tasto di scelta rapida <kbd>F5</kbd> o la voce di menu.

1. Quando richiesto, selezionare l'opzione di ** Blazor debug webassembly** per avviare il debug.

   ![Elenco delle opzioni di debug disponibili](index/_static/blazor-vscode-debugtypes.png)

1. Viene avviata l'app autonoma e viene aperto un browser di debug.

1. Impostare un punto di interruzione nel `IncrementCount` metodo nel `Counter` componente, quindi selezionare il pulsante per raggiungere il punto di interruzione:

   ![Esegui il debug del contatore in VS Code](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-debug-counter.png)

### <a name="debug-hosted-blazor-webassembly"></a>Debug Blazor webassembly ospitato

1. Aprire l' Blazor app webassembly ospitata in vs code.

1. Se non è impostata alcuna configurazione di avvio per il progetto, viene visualizzata la notifica seguente. Selezionare **Sì**.

   ![Aggiungi asset necessari](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-required-assets.png)

1. Nella finestra di selezione selezionare il progetto *Server* all'interno della soluzione ospitata.

Viene generato un file *Launch. JSON* con la configurazione di avvio per l'avvio del debugger.

### <a name="attach-to-an-existing-debugging-session"></a>Connetti a una sessione di debug esistente

Per connettersi a un'app in esecuzione Blazor , creare un file *Launch. JSON* con la configurazione seguente:

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach to Existing Blazor WebAssembly Application"
}
```

> [!NOTE]
> Il fissaggio a una sessione di debug è supportato solo per le app autonome. Per usare il debug dello stack completo, è necessario avviare l'app da VS Code.

### <a name="launch-configuration-options"></a>Opzioni di configurazione di avvio

Per il tipo di debug sono supportate le seguenti opzioni di configurazione di avvio `blazorwasm` .

| Opzione    | Descrizione |
| --------- | ----------- |
| `request` | Usare `launch` per avviare e alleghi una sessione di debug a un' Blazor app webassembly o `attach` per alleghi una sessione di debug a un'app già in esecuzione. |
| `url`     | URL da aprire nel browser durante il debug. Il valore predefinito è `https://localhost:5001`. |
| `browser` | Browser da avviare per la sessione di debug. Impostare su `edge` o `chrome`. Il valore predefinito è `chrome`. |
| `trace`   | Usato per generare log dal debugger JS. Impostare su `true` per generare i log. |
| `hosted`  | Deve essere impostato su `true` se si avvia e si esegue il debug di un' Blazor app webassembly ospitata. |
| `webRoot` | Specifica il percorso assoluto del server Web. Deve essere impostato se un'app viene gestita da una route secondaria. |
| `timeout` | Numero di millisecondi di attesa per il fissaggio della sessione di debug. Il valore predefinito è 30.000 millisecondi (30 secondi). |
| `program` | Riferimento al file eseguibile per eseguire il server dell'app ospitata. Deve essere impostato se `hosted` è `true` . |
| `cwd`     | Directory di lavoro in cui avviare l'app. Deve essere impostato se `hosted` è `true` . |
| `env`     | Variabili di ambiente da fornire al processo avviato. Applicabile solo se `hosted` è impostato su `true` . |

### <a name="example-launch-configurations"></a>Configurazioni di avvio di esempio

#### <a name="launch-and-debug-a-standalone-blazor-webassembly-app"></a>Avviare ed eseguire il debug di un' Blazor app webassembly autonoma

```json
{
  "type": "blazorwasm",
  "request": "launch",
  "name": "Launch and Debug"
}
```

#### <a name="attach-to-a-running-app-at-a-specified-url"></a>Connettersi a un'app in esecuzione in un URL specificato

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach and Debug",
  "url": "http://localhost:5000"
}
```

#### <a name="launch-and-debug-a-hosted-blazor-webassembly-app"></a>Avviare ed eseguire il debug di un' Blazor app webassembly ospitata

```json
{
  "type": "blazorwasm",
  "request": "launch",
  "name": "Launch and Debug Hosted App",
  "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/MyHostedApp.Server.dll",
  "cwd": "${workspaceFolder}"
}
```

## <a name="debug-in-the-browser"></a>Debug nel browser

1. Eseguire una build di debug dell'app nell'ambiente di sviluppo.

1. Premere <kbd>MAIUSC</kbd> + <kbd>ALT</kbd> + <kbd>D</kbd>.

1. Il browser deve essere eseguito con il debug remoto abilitato. Se il debug remoto è disabilitato, viene generata una pagina di errore della **scheda del browser di cui è possibile eseguire il debug** . La pagina di errore contiene le istruzioni per eseguire il browser con la porta di debug aperta, in modo che il Blazor proxy di debug possa connettersi all'app. *Chiudere tutte le istanze del browser* e riavviare il browser come indicato.

Quando il browser è in esecuzione con il debug remoto abilitato, il tasto di scelta rapida di debug apre una nuova scheda del debugger. Dopo qualche istante la scheda **Sources (origini** ) Mostra un elenco degli assembly .NET nell'app. Espandere ogni assembly e trovare i file di origine *. cs* / *. Razor* disponibili per il debug. Impostare i punti di interruzione, tornare alla scheda dell'app e i punti di interruzione vengono raggiunti quando viene eseguito il codice. Quando viene raggiunto un punto di interruzione, l'esecuzione<kbd>F10</kbd>del codice in un singolo passaggio (F10<kbd>) viene</kbd>eseguito normalmente.

Blazorfornisce un proxy di debug che implementa il [protocollo devtools di Chrome](https://chromedevtools.github.io/devtools-protocol/) e potenzia il protocollo con. Informazioni specifiche del NET. Quando si preme il tasto di scelta rapida Blazor per il debug, punta il devtools di Chrome sul proxy. Il proxy si connette alla finestra del browser che si sta tentando di eseguire il debug, quindi è necessario abilitare il debug remoto.

## <a name="browser-source-maps"></a>Mappe di origine del browser

Le mappe di origine del browser consentono al browser di eseguire il mapping dei file compilati ai file di origine originali e vengono comunemente usati per il debug sul lato client. Tuttavia, Blazor attualmente non esegue il mapping di C# direttamente a JavaScript/WASM. Al contrario, Blazor l'interpretazione il nel browser, quindi le mappe di origine non sono rilevanti.

## <a name="troubleshoot"></a>Risolvere problemi

Se si verificano errori, è possibile che vengano visualizzati i suggerimenti seguenti:

* Nella scheda **debugger** aprire gli strumenti di sviluppo nel browser. Nella console eseguire `localStorage.clear()` per rimuovere tutti i punti di interruzione.
* Verificare di aver installato e considerato attendibile il certificato di sviluppo ASP.NET Core HTTPS. Per altre informazioni, vedere <xref:security/enforcing-ssl#troubleshoot-certificate-problems>.
