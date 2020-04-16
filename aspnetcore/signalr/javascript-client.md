---
title: ASP.NET SignalR client JavaScript principale
author: bradygaster
description: Panoramica di SignalR ASP.NET client JavaScript di base.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/08/2020
no-loc:
- SignalR
uid: signalr/javascript-client
ms.openlocfilehash: 43b2cacf9f415ec422a00b28246f30c8ad74de29
ms.sourcegitcommit: 6c8cff2d6753415c4f5d2ffda88159a7f6f7431a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81440857"
---
# <a name="aspnet-core-opno-locsignalr-javascript-client"></a>ASP.NET SignalR client JavaScript principale

Di [Rachel Appel](https://twitter.com/rachelappel)

La libreria SignalR client JavaScript ASP.NET Core consente agli sviluppatori di chiamare il codice dell'hub sul lato server.

[Visualizzare o scaricare codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/javascript-client/sample) ( come[scaricare](xref:index#how-to-download-a-sample))

## <a name="install-the-opno-locsignalr-client-package"></a>Installare SignalR il pacchetto client

La SignalR libreria client JavaScript viene fornita come pacchetto [npm.](https://www.npmjs.com/) Nelle sezioni seguenti vengono descritti diversi modi per installare la libreria client.

### <a name="install-with-npm"></a>Installazione con npm

Se si usa Visual Studio, eseguire i comandi seguenti dalla console di **Gestione pacchetti** nella cartella radice. Per Visual Studio Code, eseguire i comandi seguenti dal **terminale integrato**.

::: moniker range=">= aspnetcore-3.0"

```bash
npm init -y
npm install @microsoft/signalr
```

npm installa il contenuto del pacchetto nella cartella *node_modules.\\ * Creare una nuova cartella denominata *signalr* nella cartella *wwwroot\\lib.* Copiare il file *signalr.js* nella cartella *wwwroot.*

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```bash
npm init -y
npm install @aspnet/signalr
```

npm installa il contenuto del pacchetto nella cartella *node_modules.\\ * Creare una nuova cartella denominata *signalr* nella cartella *wwwroot\\lib.* Copiare il file *signalr.js* nella cartella *wwwroot.*

::: moniker-end

Fare SignalR riferimento al client `<script>` JavaScript nell'elemento. Ad esempio:

```html
<script src="~/lib/signalr/signalr.js"></script>
```

### <a name="use-a-content-delivery-network-cdn"></a>Usare una rete per la distribuzione di contenuti (CDN)Use a Content Delivery Network (CDN)

Per utilizzare la libreria client senza il prerequisito npm, fare riferimento a una copia ospitata con CDN della libreria client. Ad esempio:

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/microsoft-signalr/3.1.3/signalr.min.js"></script>
```

La libreria client è disponibile nelle seguenti reti CDN:

::: moniker range=">= aspnetcore-3.0"

* [cdnjs (file cdnjs)](https://cdnjs.com/libraries/microsoft-signalr)
* [il comando (informazioni in stato di sorsia)](https://www.jsdelivr.com/package/npm/@microsoft/signalr)
* [unpkg](https://unpkg.com/@microsoft/signalr@next/dist/browser/signalr.min.js)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* [cdnjs (file cdnjs)](https://cdnjs.com/libraries/aspnet-signalr)
* [il comando (informazioni in stato di sorsia)](https://www.jsdelivr.com/package/npm/@aspnet/signalr)
* [unpkg](https://unpkg.com/@aspnet/signalr@next/dist/browser/signalr.min.js)

::: moniker-end

### <a name="install-with-libman"></a>Installa con LibMan

[LibMan](xref:client-side/libman/index) può essere utilizzato per installare file di libreria client specifici dalla libreria client ospitata da CDN. Ad esempio, aggiungere solo il file JavaScript minified al progetto. Per informazioni dettagliate su tale approccio, vedere [Aggiungere la SignalR libreria client](xref:tutorials/signalr#add-the-signalr-client-library).

## <a name="connect-to-a-hub"></a>Connettersi a un hub

Il codice seguente crea e avvia una connessione. Il nome dell'hub non fa distinzione tra maiuscole e minuscole.

[!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=9-13,43-45)]

### <a name="cross-origin-connections"></a>Connessioni tra origini

In genere, i browser caricano le connessioni dallo stesso dominio della pagina richiesta. Tuttavia, ci sono occasioni in cui è necessaria una connessione a un altro dominio.

Per impedire a un sito dannoso di leggere dati sensibili da un altro sito, le [connessioni tra origini](xref:security/cors) sono disabilitate per impostazione predefinita. Per consentire una richiesta tra origini, `Startup` abilitarla nella classe .

[!code-csharp[Cross-origin connections](javascript-client/sample/Startup.cs?highlight=29-35,56)]

## <a name="call-hub-methods-from-client"></a>Chiamare i metodi dell'hub dal clientCall hub methods from client

I client JavaScript chiamano metodi pubblici sugli hub tramite il metodo [invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke) di [HubConnection](/javascript/api/%40aspnet/signalr/hubconnection). Il `invoke` metodo accetta due argomenti:

* Nome del metodo dell'hub. Nell'esempio seguente, il nome del `SendMessage`metodo nell'hub è .
* Qualsiasi argomento definito nel metodo dell'hub. Nell'esempio seguente il nome `message`dell'argomento è . Il codice di esempio utilizza la sintassi della funzione freccia supportata nelle versioni correnti di tutti i browser principali, ad eccezione di Internet Explorer.

  [!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=24)]

> [!NOTE]
> Se si usa SignalR Servizio di Azure in *modalità senza server,* non è possibile chiamare i metodi hub da un client. Per ulteriori informazioni, vedere la [ SignalR documentazione del servizio](/azure/azure-signalr/signalr-concept-serverless-development-config).

Il `invoke` metodo restituisce una [promessa](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)JavaScript . L'oggetto `Promise` viene risolto con il valore restituito (se presente) quando il metodo sul server restituisce . Se il metodo sul server genera `Promise` un errore, viene rifiutato con il messaggio di errore. Utilizzare `then` i `catch` metodi `Promise` e su se stesso `await` per gestire questi casi (o sintassi).

Il `send` metodo restituisce `Promise`un oggetto JavaScript . L'operazione `Promise` viene risolta quando il messaggio è stato inviato al server. Se si verifica un errore `Promise` durante l'invio del messaggio, l'oggetto viene rifiutato con il messaggio di errore. Utilizzare `then` i `catch` metodi `Promise` e su se stesso `await` per gestire questi casi (o sintassi).

> [!NOTE]
> L'utilizzo `send` non attende che il server abbia ricevuto il messaggio. Di conseguenza, non è possibile restituire dati o errori dal server.

## <a name="call-client-methods-from-hub"></a>Chiamare i metodi client dall'hubCall client methods from hub

Per ricevere messaggi dall'hub, definire [on](/javascript/api/%40aspnet/signalr/hubconnection#on) un metodo `HubConnection`utilizzando il metodo on dell'oggetto .

* Nome del metodo client JavaScript. Nell'esempio seguente, il `ReceiveMessage`nome del metodo è .
* Argomenti passati dall'hub al metodo. Nell'esempio seguente il valore `message`dell'argomento è .

[!code-javascript[Receive calls from hub](javascript-client/sample/wwwroot/js/chat.js?range=14-19)]

Il codice precedente `connection.on` in viene eseguito quando il codice lato server lo chiama utilizzando il [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) metodo.

[!code-csharp[Call client-side](javascript-client/sample/hubs/chathub.cs?range=8-11)]

SignalRdetermina il metodo client da chiamare facendo corrispondere `SendAsync` `connection.on`il nome del metodo e gli argomenti definiti in e .

> [!NOTE]
> Come procedura consigliata, chiamare il `HubConnection` `on`metodo [start](/javascript/api/%40aspnet/signalr/hubconnection#start) su after . In questo modo si garantisce che i gestori vengano registrati prima della ricezione dei messaggi.

## <a name="error-handling-and-logging"></a>Registrazione e gestione degli errori

Concatenare un `catch` metodo `start` alla fine del metodo per gestire gli errori sul lato client. Consente `console.error` di generare errori nella console del browser.

[!code-javascript[Error handling](javascript-client/sample/wwwroot/js/chat.js?range=50)]

Impostare la traccia del log sul lato client passando un logger e il tipo di evento da registrare quando viene stabilita la connessione. I messaggi vengono registrati con il livello di log specificato e superiore. I livelli di registrazione disponibili sono i seguenti:

* `signalR.LogLevel.Error`&ndash; Messaggi di errore. Registra `Error` solo i messaggi.
* `signalR.LogLevel.Warning`&ndash; Messaggi di avviso relativi a potenziali errori. Registri `Warning`e `Error` messaggi.
* `signalR.LogLevel.Information`&ndash; Messaggi di stato senza errori. Registri `Information` `Warning`, `Error` e messaggi.
* `signalR.LogLevel.Trace`&ndash; Messaggi di traccia. Registra tutto, inclusi i dati trasportati tra hub e client.

Utilizzare il metodo [configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) in [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) per configurare il livello di registrazione. I messaggi vengono registrati nella console del browser.

[!code-javascript[Logging levels](javascript-client/sample/wwwroot/js/chat.js?range=9-12)]

## <a name="reconnect-clients"></a>Riconnettere i client

::: moniker range=">= aspnetcore-3.0"

### <a name="automatically-reconnect"></a>Riconnessione automatica

Il client JavaScript per SignalR può essere configurato `withAutomaticReconnect` per la riconnessione automatica utilizzando il metodo su [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder). Non si riconnette automaticamente per impostazione predefinita.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect()
    .build();
```

Senza parametri, `withAutomaticReconnect()` configura il client in modo che attenda 0, 2, 10 e 30 secondi rispettivamente prima di tentare ogni tentativo di riconnessione, interrompendosi dopo quattro tentativi non riusciti.

Prima di avviare qualsiasi `HubConnection` tentativo di `HubConnectionState.Reconnecting` riconnessione, `onreconnecting` il passerà allo `Disconnected` stato e genera `onclose` i callback `HubConnection` anziché passare allo stato e attivare i callback come un senza riconnessione automatica configurata. In questo modo è possibile avvisare gli utenti che la connessione è stata persa e disabilitare gli elementi dell'interfaccia utente.

```javascript
connection.onreconnecting(error => {
    console.assert(connection.state === signalR.HubConnectionState.Reconnecting);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection lost due to error "${error}". Reconnecting.`;
    document.getElementById("messagesList").appendChild(li);
});
```

Se il client si riconnette correttamente `HubConnection` entro i primi `Connected` quattro tentativi, il passerà allo stato e genera i callback. `onreconnected` Ciò offre l'opportunità di informare gli utenti che la connessione è stata ristabilita.

Poiché la connessione sembra completamente nuova `connectionId` al server, `onreconnected` verrà fornito un nuovo al callback.

> [!WARNING]
> Il `onreconnected` parametro `connectionId` del callback non `HubConnection` sarà definito se l'oggetto è stato configurato per ignorare la [negoziazione.](xref:signalr/configuration#configure-client-options)

```javascript
connection.onreconnected(connectionId => {
    console.assert(connection.state === signalR.HubConnectionState.Connected);

    document.getElementById("messageInput").disabled = false;

    const li = document.createElement("li");
    li.textContent = `Connection reestablished. Connected with connectionId "${connectionId}".`;
    document.getElementById("messagesList").appendChild(li);
});
```

`withAutomaticReconnect()`non configurerà `HubConnection` il per ritentare gli errori di avvio iniziale, pertanto gli errori di avvio devono essere gestiti manualmente:

```javascript
async function start() {
    try {
        await connection.start();
        console.assert(connection.state === signalR.HubConnectionState.Connected);
        console.log("connected");
    } catch (err) {
        console.assert(connection.state === signalR.HubConnectionState.Disconnected);
        console.log(err);
        setTimeout(() => start(), 5000);
    }
};
```

Se il client non si riconnette correttamente `HubConnection` entro i `Disconnected` primi quattro tentativi, la transizione allo stato e genera i callback [onclose.](/javascript/api/%40aspnet/signalr/hubconnection#onclose) Ciò offre l'opportunità di informare gli utenti che la connessione è stata persa definitivamente e consiglia di aggiornare la pagina:

```javascript
connection.onclose(error => {
    console.assert(connection.state === signalR.HubConnectionState.Disconnected);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection closed due to error "${error}". Try refreshing this page to restart the connection.`;
    document.getElementById("messagesList").appendChild(li);
});
```

Per configurare un numero personalizzato di tentativi di riconnessione `withAutomaticReconnect` prima di disconnettersi o modificare l'intervallo di riconnessione, accetta una matrice di numeri che rappresentano il ritardo in millisecondi di attesa prima di avviare ogni tentativo di riconnessione.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect([0, 0, 10000])
    .build();

    // .withAutomaticReconnect([0, 2000, 10000, 30000]) yields the default behavior
```

Nell'esempio precedente viene `HubConnection` configurato l'oggetto per avviare il tentativo di riconnessione immediatamente dopo la perdita della connessione. Questo vale anche per la configurazione predefinita.

Se il primo tentativo di riconnessione non riesce, anche il secondo tentativo di riconnessione verrà avviato immediatamente invece di attendere 2 secondi come nella configurazione predefinita.

Se il secondo tentativo di riconnessione non riesce, il terzo tentativo di riconnessione verrà avviato in 10 secondi, che è di nuovo simile alla configurazione predefinita.

Il comportamento personalizzato si discosta quindi nuovamente dal comportamento predefinito arrestandosi dopo il terzo tentativo di riconnessione anziché tentare un altro tentativo di riconnessione in altri 30 secondi come nella configurazione predefinita.

Se si desidera un maggiore controllo sulla temporizzazione `withAutomaticReconnect` e sul numero `IRetryPolicy` di tentativi di `nextRetryDelayInMilliseconds`riconnessione automatica, accetta un oggetto che implementa l'interfaccia, che dispone di un unico metodo denominato .

`nextRetryDelayInMilliseconds`accetta un singolo argomento `RetryContext`con il tipo . Il `RetryContext` ha tre `previousRetryCount` `elapsedMilliseconds` proprietà: `retryReason` , `number`e `number` che `Error` sono rispettivamente a , a e una . Prima del primo tentativo `previousRetryCount` di `elapsedMilliseconds` riconnessione, entrambi `retryReason` e saranno zero, e il sarà l'errore che ha causato la perdita della connessione. Dopo ogni tentativo non `previousRetryCount` riuscito, verrà incrementato di uno, `elapsedMilliseconds` verrà aggiornato per riflettere la quantità `retryReason` di tempo impiegato per la riconnessione finora in millisecondi e l'errore che ha causato l'errore dell'ultimo tentativo di riconnessione non riesce.

`nextRetryDelayInMilliseconds`deve restituire un numero che rappresenta il numero di millisecondi `null` di `HubConnection` attesa prima del successivo tentativo di riconnessione o se l'operazione deve interrompere la riconnessione.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect({
        nextRetryDelayInMilliseconds: retryContext => {
            if (retryContext.elapsedMilliseconds < 60000) {
                // If we've been reconnecting for less than 60 seconds so far,
                // wait between 0 and 10 seconds before the next reconnect attempt.
                return Math.random() * 10000;
            } else {
                // If we've been reconnecting for more than 60 seconds so far, stop reconnecting.
                return null;
            }
        }
    })
    .build();
```

In alternativa, è possibile scrivere codice che riconnetterà manualmente il client come illustrato in [Riconnessione manuale](#manually-reconnect).

::: moniker-end

### <a name="manually-reconnect"></a>Riconnessione manuale

::: moniker range="< aspnetcore-3.0"

> [!WARNING]
> Prima della 3.0, il SignalR client JavaScript per non si riconnette automaticamente. È necessario scrivere codice che riconnetterà il client manualmente.

::: moniker-end

Il codice seguente illustra un tipico approccio di riconnessione manuale:The following code demonstrates a typical manual reconnection approach:

1. Viene creata una funzione `start` (in questo caso, la funzione) per avviare la connessione.
1. Chiamare `start` la funzione nel `onclose` gestore eventi della connessione.

[!code-javascript[Reconnect the JavaScript client](javascript-client/sample/wwwroot/js/chat.js?range=28-40)]

Un'implementazione reale userebbe un back-off esponenziale o ritenterebbe un numero specificato di volte prima di rinunciare.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Informazioni di riferimento sull'API JavaScript](/javascript/api/?view=signalr-js-latest)
* [Esercitazione su JavaScript](xref:tutorials/signalr)
* [Esercitazione su WebPack e TypeScript](xref:tutorials/signalr-typescript-webpack)
* [Hub](xref:signalr/hubs)
* [Client .NET](xref:signalr/dotnet-client)
* [Pubblicazione in Azure](xref:signalr/publish-to-azure-web-app)
* [Richieste tra origini (CORS)](xref:security/cors)
* [Documentazione senza server di Servizio di AzureAzure SignalR Service serverless documentation](/azure/azure-signalr/signalr-concept-serverless-development-config)
