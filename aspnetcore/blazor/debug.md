---
title: ASP.NET Core di debugBlazor WebAssembly
author: guardrex
description: Informazioni su come eseguire il debug delle Blazor app.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/15/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/debug
ms.openlocfilehash: 14943b9f7847ac9144addfdf16a003f6fc8c340c
ms.sourcegitcommit: cc845634a490c49ff869c89b6e422b6d65d0e886
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/24/2020
ms.locfileid: "87159702"
---
# <a name="debug-aspnet-core-no-locblazor-webassembly"></a>ASP.NET Core di debugBlazor WebAssembly

[Daniel Roth](https://github.com/danroth27)

Blazor WebAssemblyè possibile eseguire il debug delle app usando gli strumenti di sviluppo del browser nei browser basati su cromo (Edge/Chrome). In alternativa, è possibile eseguire il debug dell'app usando Visual Studio o Visual Studio Code.

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

* Google Chrome (versione 70 o successiva) (impostazione predefinita)
* Microsoft Edge (versione 80 o successiva)

## <a name="enable-debugging-for-visual-studio-and-visual-studio-code"></a>Abilitare il debug per Visual Studio e Visual Studio Code

Per abilitare il debug per un' Blazor WebAssembly app esistente, aggiornare il `launchSettings.json` file nel progetto di avvio per includere la `inspectUri` proprietà seguente in ogni profilo di avvio:

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/_framework/debug/ws-proxy?browser={browserInspectUri}"
```

Al termine dell'aggiornamento, il `launchSettings.json` file dovrebbe essere simile all'esempio seguente:

[!code-json[](debug/launchSettings.json?highlight=14,22)]

`inspectUri`Proprietà:

* Consente all'IDE di rilevare che l'app è un' Blazor WebAssembly app.
* Indica all'infrastruttura di debug degli script di connettersi al browser tramite il Blazor proxy di debug.

I valori segnaposto per i protocolli WebSockets ( `wsProtocol` ), host ( `url.hostname` ), Port ( `url.port` ) e Inspector URI nel browser avviato ( `browserInspectUri` ) sono forniti dal Framework.

## <a name="visual-studio"></a>Visual Studio

Per eseguire il debug di un' Blazor WebAssembly app in Visual Studio:

1. Creare una nuova app ospitata ASP.NET Core Blazor WebAssembly .
1. Premere <kbd>F5</kbd> per eseguire l'app nel debugger.
1. Impostare un punto di interruzione in `Pages/Counter.razor` nel `IncrementCount` metodo.
1. Passare alla **`Counter`** scheda e selezionare il pulsante per raggiungere il punto di interruzione:

   ![Contatore debug](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-counter.png)

1. Verificare il valore del `currentCount` campo nella finestra variabili locali:

   ![Visualizza variabili locali](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-locals.png)

1. Premere <kbd>F5</kbd> per continuare l'esecuzione.

Quando si esegue il debug dell' Blazor WebAssembly app, è anche possibile eseguire il debug del codice del server:

1. Impostare un punto di interruzione nella `Pages/FetchData.razor` pagina in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> .
1. Impostare un punto di interruzione in `WeatherForecastController` nel `Get` metodo di azione.
1. Passare alla **`Fetch Data`** scheda per raggiungere il primo punto di interruzione nel `FetchData` componente immediatamente prima di eseguire una richiesta HTTP al server:

   ![Eseguire il debug dei dati di recupero](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-fetch-data.png)

1. Premere <kbd>F5</kbd> per continuare l'esecuzione e quindi raggiungere il punto di interruzione sul server nell' `WeatherForecastController` :

   ![Server di debug](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-server.png)

1. Premere di nuovo <kbd>F5</kbd> per consentire l'esecuzione continua e visualizzare la tabella delle previsioni meteorologiche sottoposta a rendering.

<a id="vscode"></a>

## <a name="visual-studio-code"></a>Visual Studio Code

### <a name="debug-standalone-no-locblazor-webassembly"></a>Debug autonomoBlazor WebAssembly

1. Aprire l'app autonoma Blazor WebAssembly in vs code.

   Se viene visualizzata la notifica seguente, è necessaria un'installazione aggiuntiva per abilitare il debug:
   
   * Verificare che siano installate le estensioni corrette.
   * Verificare che l'anteprima JavaScript sia abilitata.
   * Ricaricare la finestra.

   ![Installazione aggiuntiva richiesta](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-additional-setup.png)

1. Avviare il debug usando il tasto di scelta rapida <kbd>F5</kbd> o la voce di menu.

1. Quando richiesto, selezionare l'opzione ** Blazor WebAssembly debug** per avviare il debug.

   ![Elenco delle opzioni di debug disponibili](index/_static/blazor-vscode-debugtypes.png)

1. Viene avviata l'app autonoma e viene aperto un browser di debug.

1. Impostare un punto di interruzione nel `IncrementCount` metodo nel `Counter` componente, quindi selezionare il pulsante per raggiungere il punto di interruzione:

   ![Esegui il debug del contatore in VS Code](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-debug-counter.png)

### <a name="debug-hosted-no-locblazor-webassembly"></a>Debug ospitatoBlazor WebAssembly

1. Aprire la cartella della soluzione dell'app ospitata Blazor WebAssembly in vs code.

1. Se non è impostata alcuna configurazione di avvio per il progetto, viene visualizzata la notifica seguente. Selezionare **Sì**.

   ![Aggiungi asset necessari](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-required-assets.png)

1. Nel riquadro comandi nella parte superiore della finestra selezionare il progetto *Server* all'interno della soluzione ospitata.

`launch.json`Viene generato un file con la configurazione di avvio per l'avvio del debugger.

### <a name="attach-to-an-existing-debugging-session"></a>Connetti a una sessione di debug esistente

Per connettersi a un'app in esecuzione Blazor , creare un `launch.json` file con la configurazione seguente:

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

Le seguenti opzioni di configurazione di avvio sono supportate per il `blazorwasm` tipo di debug ( `.vscode/launch.json` ).

| Opzione    | Description |
| --------- | ----------- |
| `request` | Usare `launch` per avviare e alleghi una sessione di debug a un' Blazor WebAssembly app o `attach` per alleghi una sessione di debug a un'app già in esecuzione. |
| `url`     | URL da aprire nel browser durante il debug. Il valore predefinito è `https://localhost:5001`. |
| `browser` | Browser da avviare per la sessione di debug. Impostare su `edge` o `chrome`. Il valore predefinito è `chrome`. |
| `trace`   | Usato per generare log dal debugger JS. Impostare su `true` per generare i log. |
| `hosted`  | Deve essere impostato su `true` se si avvia e si esegue il debug di un'app ospitata Blazor WebAssembly . |
| `webRoot` | Specifica il percorso assoluto del server Web. Deve essere impostato se un'app viene gestita da una route secondaria. |
| `timeout` | Numero di millisecondi di attesa per il fissaggio della sessione di debug. Il valore predefinito è 30.000 millisecondi (30 secondi). |
| `program` | Riferimento al file eseguibile per eseguire il server dell'app ospitata. Deve essere impostato se `hosted` è `true` . |
| `cwd`     | Directory di lavoro in cui avviare l'app. Deve essere impostato se `hosted` è `true` . |
| `env`     | Variabili di ambiente da fornire al processo avviato. Applicabile solo se `hosted` è impostato su `true` . |

### <a name="example-launch-configurations"></a>Configurazioni di avvio di esempio

#### <a name="launch-and-debug-a-standalone-no-locblazor-webassembly-app"></a>Avviare ed eseguire il debug di un'app autonoma Blazor WebAssembly

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

#### <a name="launch-and-debug-a-hosted-no-locblazor-webassembly-app-with-microsoft-edge"></a>Avviare ed eseguire il debug di un'app ospitata Blazor WebAssembly con Microsoft Edge

Per impostazione predefinita, la configurazione del browser è Google Chrome. Quando si usa Microsoft Edge per il debug, impostare `browser` su `edge` . Per usare Google Chrome, non impostare l' `browser` opzione o impostare il valore dell'opzione su `chrome` .

```json
{
  "name": "Launch and Debug Hosted Blazor WebAssembly App",
  "type": "blazorwasm",
  "request": "launch",
  "hosted": true,
  "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/MyHostedApp.Server.dll",
  "cwd": "${workspaceFolder}/Server",
  "browser": "edge"
}
```

Nell'esempio precedente, `MyHostedApp.Server.dll` è l'assembly dell'app *Server* . La `.vscode` cartella si trova nella cartella della soluzione accanto alle `Client` `Server` cartelle, e `Shared` .

## <a name="debug-in-the-browser"></a>Debug nel browser

1. Eseguire una build di debug dell'app nell'ambiente di sviluppo.

1. Avviare un browser e passare all'URL dell'app, ad esempio `https://localhost:5001` .

1. Nel browser provare ad avviare il debug remoto premendo <kbd>MAIUSC</kbd> + <kbd>ALT</kbd> + <kbd>D</kbd>.

   Il browser deve essere in esecuzione con il debug remoto abilitato, che non è il valore predefinito. Se il debug remoto è disabilitato, viene eseguito il rendering della pagina di errore della **scheda del browser di cui è possibile eseguire** il debug con le istruzioni per avviare il browser con la porta di debug aperta. Seguire le istruzioni per il browser, che consente di aprire una nuova finestra del browser. Chiudere la finestra del browser precedente.

1. Quando il browser è in esecuzione con il debug remoto abilitato, il tasto di scelta rapida per il debug (<kbd>MAIUSC</kbd> + <kbd>ALT</kbd> + <kbd>D</kbd>) apre una nuova scheda del debugger.

1. Dopo qualche istante, la scheda **origini** Mostra un elenco degli assembly .NET dell'app all'interno del `file://` nodo.

1. Nel codice componente ( `.razor` file) e nei file di codice C# ( `.cs` ), i punti di interruzione impostati vengono raggiunti durante l'esecuzione del codice. Quando viene raggiunto un punto di interruzione, l'esecuzione<kbd>F10</kbd>del codice in un singolo passaggio (F10<kbd>) viene</kbd>eseguito normalmente.

Blazorfornisce un proxy di debug che implementa il [protocollo devtools di Chrome](https://chromedevtools.github.io/devtools-protocol/) e potenzia il protocollo con. Informazioni specifiche del NET. Quando si preme il tasto di scelta rapida Blazor per il debug, punta il devtools di Chrome sul proxy. Il proxy si connette alla finestra del browser che si sta tentando di eseguire il debug, quindi è necessario abilitare il debug remoto.

## <a name="browser-source-maps"></a>Mappe di origine del browser

Le mappe di origine del browser consentono al browser di eseguire il mapping dei file compilati ai file di origine originali e vengono comunemente usati per il debug sul lato client. Tuttavia, Blazor attualmente non esegue il mapping di C# direttamente a JavaScript/WASM. Al contrario, Blazor l'interpretazione il nel browser, quindi le mappe di origine non sono rilevanti.

## <a name="troubleshoot"></a>Risolvere problemi

Se si verificano errori, è possibile che vengano visualizzati i suggerimenti seguenti:

* Nella scheda **debugger** aprire gli strumenti di sviluppo nel browser. Nella console eseguire `localStorage.clear()` per rimuovere tutti i punti di interruzione.
* Verificare di aver installato e considerato attendibile il certificato di sviluppo ASP.NET Core HTTPS. Per altre informazioni, vedere <xref:security/enforcing-ssl#troubleshoot-certificate-problems>.
* Visual Studio richiede l'opzione **Abilita debug JavaScript per ASP.NET (Chrome, Edge e IE)** in **strumenti**  >  **Opzioni**  >  **debug**  >  **generale**. Questa è l'impostazione predefinita per Visual Studio. Se il debug non funziona, verificare che sia selezionata l'opzione.
