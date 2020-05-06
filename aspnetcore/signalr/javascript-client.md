---
title: ASP.NET Core SignalR client JavaScript
author: bradygaster
description: Panoramica di ASP.NET Core SignalR client JavaScript.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/08/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/javascript-client
ms.openlocfilehash: 966e8e171752edb230f6da82203fd901b0fdeaab
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82768942"
---
# <a name="aspnet-core-signalr-javascript-client"></a>ASP.NET Core SignalR client JavaScript

Di [Rachel Appel](https://twitter.com/rachelappel)

La libreria SignalR client JavaScript ASP.NET Core consente agli sviluppatori di chiamare il codice dell'hub sul lato server.

[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/javascript-client/sample) ([procedura per il download](xref:index#how-to-download-a-sample))

## <a name="install-the-signalr-client-package"></a>Installare il SignalR pacchetto client

La SignalR libreria client JavaScript viene distribuita come pacchetto [NPM](https://www.npmjs.com/) . Le sezioni seguenti illustrano diversi modi per installare la libreria client.

### <a name="install-with-npm"></a>Eseguire l'installazione con NPM

Se si usa Visual Studio, eseguire i comandi seguenti dalla **console di gestione pacchetti** nella cartella radice. Per Visual Studio Code, eseguire i comandi seguenti dal **terminale integrato**.

::: moniker range=">= aspnetcore-3.0"

```bash
npm init -y
npm install @microsoft/signalr
```

NPM installa il contenuto del pacchetto nella *cartella\\ node_modules* . Creare una nuova cartella denominata *SignalR* nella cartella *wwwroot\\lib* . Copiare il file *SignalR. js* nella cartella *wwwroot\lib\signalr* .

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```bash
npm init -y
npm install @aspnet/signalr
```

NPM installa il contenuto del pacchetto nella *cartella\\ node_modules* . Creare una nuova cartella denominata *SignalR* nella cartella *wwwroot\\lib* . Copiare il file *SignalR. js* nella cartella *wwwroot\lib\signalr* .

::: moniker-end

Fare riferimento SignalR al client JavaScript nell' `<script>` elemento. Ad esempio:

```html
<script src="~/lib/signalr/signalr.js"></script>
```

### <a name="use-a-content-delivery-network-cdn"></a>Usare una rete per la distribuzione di contenuti (CDN)

Per usare la libreria client senza il prerequisito NPM, fare riferimento a una copia ospitata dalla rete CDN della libreria client. Ad esempio:

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/microsoft-signalr/3.1.3/signalr.min.js"></script>
```

La libreria client è disponibile nei seguenti CDNs:

::: moniker range=">= aspnetcore-3.0"

* [cdnjs](https://cdnjs.com/libraries/microsoft-signalr)
* [jsDelivr](https://www.jsdelivr.com/package/npm/@microsoft/signalr)
* [unpkg](https://unpkg.com/@microsoft/signalr@next/dist/browser/signalr.min.js)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* [cdnjs](https://cdnjs.com/libraries/aspnet-signalr)
* [jsDelivr](https://www.jsdelivr.com/package/npm/@aspnet/signalr)
* [unpkg](https://unpkg.com/@aspnet/signalr@next/dist/browser/signalr.min.js)

::: moniker-end

### <a name="install-with-libman"></a>Installare con LibMan

[LibMan](xref:client-side/libman/index) può essere usato per installare file di libreria client specifici dalla libreria client ospitata nella rete CDN. Ad esempio, aggiungere solo il file JavaScript minimizzati al progetto. Per informazioni dettagliate su questo approccio, vedere [aggiungere SignalR la libreria client](xref:tutorials/signalr#add-the-signalr-client-library).

## <a name="connect-to-a-hub"></a>Connettersi a un hub

Il codice seguente crea e avvia una connessione. Il nome dell'hub non fa distinzione tra maiuscole e minuscole.

[!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=9-13,43-45)]

### <a name="cross-origin-connections"></a>Connessioni tra le origini

In genere, i browser caricano le connessioni dallo stesso dominio della pagina richiesta. In alcuni casi, tuttavia, è necessaria una connessione a un altro dominio.

Per impedire a un sito dannoso di leggere dati sensibili da un altro sito, le [connessioni tra le origini](xref:security/cors) sono disabilitate per impostazione predefinita. Per consentire una richiesta tra origini, abilitarla nella `Startup` classe.

[!code-csharp[Cross-origin connections](javascript-client/sample/Startup.cs?highlight=29-35,56)]

## <a name="call-hub-methods-from-client"></a>Chiamare i metodi dell'hub dal client

I client JavaScript chiamano metodi pubblici sugli hub tramite il metodo [Invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke) di [HubConnection](/javascript/api/%40aspnet/signalr/hubconnection). Il `invoke` metodo accetta due argomenti:

* Nome del metodo dell'hub. Nell'esempio seguente il nome del metodo nell'hub è `SendMessage`.
* Qualsiasi argomento definito nel metodo dell'hub. Nell'esempio seguente il nome dell'argomento è `message`. Il codice di esempio usa la sintassi della funzione freccia supportata nelle versioni correnti di tutti i browser principali, ad eccezione di Internet Explorer.

  [!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=24)]

> [!NOTE]
> Se si usa il servizio SignalR di Azure in *modalità senza server*, non è possibile chiamare i metodi dell'hub da un client. Per ulteriori informazioni, vedere la [ SignalR documentazione del servizio](/azure/azure-signalr/signalr-concept-serverless-development-config).

Il `invoke` metodo restituisce una [promessa](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)JavaScript. `Promise` Viene risolto con il valore restituito (se presente) quando il metodo nel server restituisce. Se il metodo sul server genera un errore, `Promise` viene rifiutato con il messaggio di errore. Usare i `then` metodi `catch` e sull'oggetto `Promise` stesso per gestire questi casi (o `await` la sintassi).

Il `send` metodo restituisce un oggetto `Promise`JavaScript. Il `Promise` viene risolto quando il messaggio è stato inviato al server. Se si verifica un errore durante l'invio del messaggio `Promise` , viene rifiutato con il messaggio di errore. Usare i `then` metodi `catch` e sull'oggetto `Promise` stesso per gestire questi casi (o `await` la sintassi).

> [!NOTE]
> L' `send` utilizzo di non attende fino a quando il server non ha ricevuto il messaggio. Di conseguenza, non è possibile restituire dati o errori dal server.

## <a name="call-client-methods-from-hub"></a>Chiamare i metodi client dall'hub

Per ricevere messaggi dall'hub, definire un metodo usando il metodo [on](/javascript/api/%40aspnet/signalr/hubconnection#on) di `HubConnection`.

* Nome del metodo client JavaScript. Nell'esempio seguente il nome del metodo è `ReceiveMessage`.
* Argomenti passati dall'hub al metodo. Nell'esempio seguente il valore dell'argomento è `message`.

[!code-javascript[Receive calls from hub](javascript-client/sample/wwwroot/js/chat.js?range=14-19)]

Il codice precedente in `connection.on` viene eseguito quando il codice lato server lo chiama usando il metodo [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) .

[!code-csharp[Call client-side](javascript-client/sample/hubs/chathub.cs?range=8-11)]

SignalRdetermina il metodo client da chiamare mediante la corrispondenza tra il nome del metodo e `SendAsync` gli `connection.on`argomenti definiti in e.

> [!NOTE]
> Come procedura consigliata, chiamare il metodo [Start](/javascript/api/%40aspnet/signalr/hubconnection#start) sul valore `HubConnection` after `on`. In questo modo si garantisce che i gestori vengano registrati prima della ricezione dei messaggi.

## <a name="error-handling-and-logging"></a>Registrazione e gestione degli errori

Concatenare un `catch` metodo alla fine del `start` metodo per gestire gli errori sul lato client. Usare `console.error` per restituire gli errori alla console del browser.

[!code-javascript[Error handling](javascript-client/sample/wwwroot/js/chat.js?range=50)]

Configurare la traccia del log sul lato client passando un logger e un tipo di evento da registrare quando viene stabilita la connessione. I messaggi vengono registrati con il livello di registrazione specificato e un valore superiore. I livelli di log disponibili sono i seguenti:

* `signalR.LogLevel.Error`&ndash; Messaggi di errore. Registra `Error` solo i messaggi.
* `signalR.LogLevel.Warning`&ndash; Messaggi di avviso relativi a potenziali errori. Log `Warning`e `Error` messaggi.
* `signalR.LogLevel.Information`&ndash; Messaggi di stato senza errori. Registra `Information`i `Warning`messaggi, `Error` e.
* `signalR.LogLevel.Trace`&ndash; Messaggi di traccia. Registra tutti gli elementi, inclusi i dati trasportati tra l'hub e il client.

Usare il metodo [configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) in [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) per configurare il livello di registrazione. I messaggi vengono registrati nella console del browser.

[!code-javascript[Logging levels](javascript-client/sample/wwwroot/js/chat.js?range=9-12)]

## <a name="reconnect-clients"></a>Riconnettere i client

::: moniker range=">= aspnetcore-3.0"

### <a name="automatically-reconnect"></a>Riconnetti automaticamente

Il client JavaScript per SignalR può essere configurato in modo da riconnettersi automaticamente `withAutomaticReconnect` usando il metodo in [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder). Per impostazione predefinita, non verrà riconnessa automaticamente.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect()
    .build();
```

Senza parametri, `withAutomaticReconnect()` configura il client in modo che attenda rispettivamente 0, 2, 10 e 30 secondi prima di provare ogni tentativo di riconnessione, arrestandosi dopo quattro tentativi non riusciti.

Prima di iniziare tutti i tentativi di riconnessione, il `HubConnection` passerà allo `HubConnectionState.Reconnecting` stato e `onreconnecting` genererà i callback anziché eseguire la `Disconnected` transizione allo stato e attivare i `onclose` callback, ad esempio `HubConnection` senza riconnessione automatica configurata. Questo consente di avvisare gli utenti che la connessione è stata persa e di disabilitare gli elementi dell'interfaccia utente.

```javascript
connection.onreconnecting(error => {
    console.assert(connection.state === signalR.HubConnectionState.Reconnecting);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection lost due to error "${error}". Reconnecting.`;
    document.getElementById("messagesList").appendChild(li);
});
```

Se il client si riconnette correttamente entro i primi quattro tentativi, il `HubConnection` eseguirà la transizione allo `Connected` stato e attiverà i `onreconnected` callback. Questo consente di informare gli utenti che è stata ristabilita la connessione.

Poiché la connessione ha un aspetto completamente nuovo per il server, `connectionId` al `onreconnected` callback verrà fornito un nuovo.

> [!WARNING]
> Il `onreconnected` `connectionId` parametro del callback non sarà definito se è stato `HubConnection` configurato per [ignorare la negoziazione](xref:signalr/configuration#configure-client-options).

```javascript
connection.onreconnected(connectionId => {
    console.assert(connection.state === signalR.HubConnectionState.Connected);

    document.getElementById("messageInput").disabled = false;

    const li = document.createElement("li");
    li.textContent = `Connection reestablished. Connected with connectionId "${connectionId}".`;
    document.getElementById("messagesList").appendChild(li);
});
```

`withAutomaticReconnect()`non configurerà per ritentare gli errori iniziali iniziali, quindi gli `HubConnection` errori di avvio devono essere gestiti manualmente:

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

Se il client non si riconnette correttamente entro i primi quattro tentativi, `HubConnection` il passerà allo `Disconnected` stato e attiverà i callback [OnClose](/javascript/api/%40aspnet/signalr/hubconnection#onclose) . Questo consente di informare gli utenti che la connessione è stata persa definitivamente e si consiglia di aggiornare la pagina:

```javascript
connection.onclose(error => {
    console.assert(connection.state === signalR.HubConnectionState.Disconnected);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection closed due to error "${error}". Try refreshing this page to restart the connection.`;
    document.getElementById("messagesList").appendChild(li);
});
```

Per configurare un numero personalizzato di tentativi di riconnessione prima di disconnettere o modificare la temporizzazione di riconnessione `withAutomaticReconnect` , accetta una matrice di numeri che rappresenta il ritardo in millisecondi di attesa prima di avviare ogni tentativo di riconnessione.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect([0, 0, 10000])
    .build();

    // .withAutomaticReconnect([0, 2000, 10000, 30000]) yields the default behavior
```

Nell'esempio precedente viene configurato per `HubConnection` avviare il tentativo di riconnessione immediatamente dopo la perdita della connessione. Questo vale anche per la configurazione predefinita.

Se il primo tentativo di riconnessione ha esito negativo, anche il secondo tentativo di riconnessione verrà avviato immediatamente anziché attendere 2 secondi come nella configurazione predefinita.

Se il secondo tentativo di riconnessione ha esito negativo, il terzo tentativo di riconnessione si avvierà entro 10 secondi, che è simile alla configurazione predefinita.

Il comportamento personalizzato, quindi, differisce di nuovo dal comportamento predefinito arrestando il terzo tentativo di riconnessione anziché tentare un ulteriore tentativo di riconnessione in altri 30 secondi, come nella configurazione predefinita.

Se si desidera un maggiore controllo sull'intervallo e sul numero di tentativi di riconnessione automatici `withAutomaticReconnect` , accetta un oggetto che `IRetryPolicy` implementa l'interfaccia, che dispone di un `nextRetryDelayInMilliseconds`singolo metodo denominato.

`nextRetryDelayInMilliseconds`accetta un solo argomento con il tipo `RetryContext`. `RetryContext` Dispone di tre proprietà: `previousRetryCount`, `elapsedMilliseconds` e `retryReason` che sono `Error` rispettivamente `number`un oggetto `number` e un oggetto. Prima del primo tentativo di riconnessione, `previousRetryCount` sia `elapsedMilliseconds` che saranno zero e `retryReason` sarà l'errore che ha causato la perdita della connessione. Dopo ogni tentativo non riuscito, `previousRetryCount` verrà incrementato di uno, `elapsedMilliseconds` verrà aggiornato in modo da riflettere la quantità di tempo impiegato per la riconnessione fino a questo punto, in millisecondi, e `retryReason` sarà l'errore che ha causato l'esito negativo dell'ultimo tentativo di riconnessione.

`nextRetryDelayInMilliseconds`deve restituire un numero che rappresenta il numero di millisecondi di attesa prima del successivo tentativo di riconnessione `null` o se `HubConnection` l'oggetto deve arrestare la riconnessione.

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

In alternativa, è possibile scrivere codice per riconnettere manualmente il client, come illustrato in [riconnessione manuale](#manually-reconnect).

::: moniker-end

### <a name="manually-reconnect"></a>Riconnessione manuale

::: moniker range="< aspnetcore-3.0"

> [!WARNING]
> Prima del 3,0, il client JavaScript per SignalR non si riconnette automaticamente. È necessario scrivere codice che riconnetta il client manualmente.

::: moniker-end

Il codice seguente illustra un tipico approccio di riconnessione manuale:

1. Una funzione (in questo caso, la `start` funzione) viene creata per avviare la connessione.
1. Chiamare la `start` funzione nel gestore `onclose` eventi della connessione.

[!code-javascript[Reconnect the JavaScript client](javascript-client/sample/wwwroot/js/chat.js?range=28-40)]

Un'implementazione reale utilizzerebbe un backup esponenziale o ritenterà un numero di volte specificato prima di rinunciare.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Informazioni di riferimento sulle API JavaScript](/javascript/api/?view=signalr-js-latest)
* [Esercitazione su JavaScript](xref:tutorials/signalr)
* [Esercitazione su Webpack e TypeScript](xref:tutorials/signalr-typescript-webpack)
* [Hub](xref:signalr/hubs)
* [Client .NET](xref:signalr/dotnet-client)
* [Pubblicazione in Azure](xref:signalr/publish-to-azure-web-app)
* [Richieste tra le origini (CORS)](xref:security/cors)
* [Documentazione SignalR senza server per i servizi di Azure](/azure/azure-signalr/signalr-concept-serverless-development-config)
