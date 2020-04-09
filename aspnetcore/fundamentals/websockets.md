---
title: Supporto di WebSocket in ASP.NET Core
author: rick-anderson
description: Introduzione all'uso di oggetti WebSocket in ASP.NET Core.
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- SignalR
uid: fundamentals/websockets
ms.openlocfilehash: a8040003374906fd93e12c9fde44c4a5ccc2cf37
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "78655785"
---
# <a name="websockets-support-in-aspnet-core"></a>Supporto di WebSocket in ASP.NET Core

Di [Tom Dykstra](https://github.com/tdykstra) e [Andrew Stanton-Nurse](https://github.com/anurse)

L'articolo contiene l'introduzione all'uso di oggetti WebSocket in ASP.NET Core. [WebSocket](https://wikipedia.org/wiki/WebSocket) ([RFC 6455](https://tools.ietf.org/html/rfc6455)) è un protocollo che consente canali di comunicazione persistente bidirezionale su connessioni TCP. Viene usato nelle app che sfruttano comunicazioni veloci e in tempo reale, ad esempio le app di chat, i dashboard e le app di gioco.

[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/websockets/samples) ([procedura per il download](xref:index#how-to-download-a-sample)). [Come eseguire](#sample-app).

## <a name="signalr"></a>SignalR

[ASP.NET Core SignalR](xref:signalr/introduction) è una libreria che consente di aggiungere in modo più semplice funzionalità Web in tempo reale alle app. Laddove possibile, usa oggetti WebSocket.

Per la maggior parte delle applicazioni, è consigliabile preferire SignalR a oggetti WebSocket non elaborati. SignalR fornisce il fallback per il trasporto per gli ambienti in cui i WebSocket non sono disponibili. Fornisce inoltre un modello di app Remote Procedure Call semplice. Nella maggior parte degli scenari, inoltre, l'uso di SignalR non genera alcuno svantaggio significativo in termini di prestazioni rispetto all'uso di oggetti WebSocket non elaborati.

## <a name="prerequisites"></a>Prerequisiti

* ASP.NET Core 1.1 o versione successiva
* Qualsiasi sistema operativo che supporta ASP.NET Core:
  
  * Windows 7/Windows Server 2008 o versioni successive
  * Linux
  * macOS
  
* Se l'app viene eseguita su Windows con IIS:

  * Windows 8/Windows Server 2012 o versioni successive
  * IIS 8/IIS 8 Express
  * Gli oggetti WebSocket devono essere abilitati (vedere la sezione [Supporto di IIS/IIS Express](#iisiis-express-support)).
  
* Se l'app viene eseguita su [HTTP.sys](xref:fundamentals/servers/httpsys):

  * Windows 8/Windows Server 2012 o versioni successive

* Per i browser supportati, vedere https://caniuse.com/#feat=websockets.

::: moniker range="< aspnetcore-2.1"

## <a name="nuget-package"></a>Pacchetto NuGet

Installare il pacchetto [Microsoft.AspNetCore.WebSockets](https://www.nuget.org/packages/Microsoft.AspNetCore.WebSockets/).

::: moniker-end

## <a name="configure-the-middleware"></a>Configurare il middleware


Aggiungere il middleware degli oggetti WebSocket nel metodo `Configure` della classe `Startup`:

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=UseWebSockets)]

::: moniker range="< aspnetcore-2.2"

È possibile configurare le impostazioni seguenti:

* `KeepAliveInterval`: la frequenza di invio di frame "ping" al client per garantire che i proxy tengano aperta la connessione. Il valore predefinito è due minuti.
* `ReceiveBufferSize`: le dimensioni del buffer usato per ricevere dati. Gli utenti avanzati possono avere l'esigenza di modificare questa impostazione per ottimizzare le prestazioni in base alle dimensioni dei dati. L'impostazione predefinita è 4 KB.

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

È possibile configurare le impostazioni seguenti:

* `KeepAliveInterval`: la frequenza di invio di frame "ping" al client per garantire che i proxy tengano aperta la connessione. Il valore predefinito è due minuti.
* <xref:Microsoft.AspNetCore.Builder.WebSocketOptions.ReceiveBufferSize>: le dimensioni del buffer usato per ricevere dati. Gli utenti avanzati possono avere l'esigenza di modificare questa impostazione per ottimizzare le prestazioni in base alle dimensioni dei dati. L'impostazione predefinita è 4 KB.
* `AllowedOrigins`: elenco di valori dell'intestazione Origin consentiti per le richieste WebSocket. Per impostazione predefinita, tutte le origini sono consentite. Per informazioni dettagliate, vedere più avanti "Restrizione per le origini WebSocket".

::: moniker-end

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=UseWebSocketsOptions)]

## <a name="accept-websocket-requests"></a>Accettare le richieste WebSocket

In un momento successivo nel ciclo di vita della richiesta, più avanti nel metodo `Configure` o in un metodo di azione, ad esempio, verificare che si tratti di una richiesta WebSocket e accettarla.

L'esempio seguente è tratto da un punto successivo nel metodo `Configure`:

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=AcceptWebSocket&highlight=7)]

Una richiesta WebSocket può arrivare in qualsiasi URL, ma questo codice di esempio accetta solo le richieste per `/ws`.

Quando si usa un oggetto WebSocket, è **necessario** mantenere la pipeline middleware in esecuzione per la durata della connessione. Se si cerca di inviare o ricevere un messaggio WebSocket dopo che la pipeline middleware è terminata, si potrebbe ottenere un'eccezione simile alla seguente:

```
System.Net.WebSockets.WebSocketException (0x80004005): The remote party closed the WebSocket connection without completing the close handshake. ---> System.ObjectDisposedException: Cannot write to the response body, the response has completed.
Object name: 'HttpResponseStream'.
```

Se si sta usando un servizio in background per scrivere i dati in un oggetto WebSocket, assicurarsi di mantenere la pipeline middleware in esecuzione. A tale scopo, usare <xref:System.Threading.Tasks.TaskCompletionSource%601>. Passare `TaskCompletionSource` al servizio in background e fare in modo che chiami <xref:System.Threading.Tasks.TaskCompletionSource%601.TrySetResult%2A> al termine dell'uso dell'oggetto WebSocket. Quindi assegnare `await` alla proprietà <xref:System.Threading.Tasks.TaskCompletionSource%601.Task> durante la richiesta, come mostrato nell'esempio seguente:

```csharp
app.Use(async (context, next) => {
    var socket = await context.WebSockets.AcceptWebSocketAsync();
    var socketFinishedTcs = new TaskCompletionSource<object>();

    BackgroundSocketProcessor.AddSocket(socket, socketFinishedTcs); 

    await socketFinishedTcs.Task;
});
```
L'eccezione di chiusura WebSocket può verificarsi anche se il ritorno da un metodo di azione avviene troppo presto. Se si accetta un socket in un metodo di azione, attendere che il codice che usa il socket sia stato completato prima di tornare dal metodo dell'azione.

Per attendere il completamento del socket, non utilizzare mai `Task.Wait()`, `Task.Result` o chiamate di blocco simili perché ciò può provocare gravi problemi di threading. Usare sempre `await`.

## <a name="send-and-receive-messages"></a>Inviare e ricevere messaggi

Il metodo `AcceptWebSocketAsync` consente di aggiornare la connessione TCP a una connessione WebSocket e offre un oggetto [WebSocket](/dotnet/core/api/system.net.websockets.websocket). Usare l'oggetto `WebSocket` per inviare e ricevere messaggi.

Il codice illustrato in precedenza che accetta la richiesta WebSocket passa l'oggetto `WebSocket` a un metodo `Echo`. Il codice riceve un messaggio e lo reinvia immediatamente. I messaggi vengono inviati e ricevuti in un ciclo, fino a quando il client non chiude la connessione:

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=Echo)]

Quando si accetta la connessione WebSocket prima che inizi il ciclo, la pipeline del middleware termina. Dopo la chiusura del socket, la pipeline si arresta, In altri termini, quando viene accettato il WebSocket, lo spostamento in avanti della richiesta nella pipeline si interrompe. Quando il ciclo viene completato e il socket viene chiuso, la richiesta torna ad avanzare nella pipeline.

::: moniker range=">= aspnetcore-2.2"

## <a name="handle-client-disconnects"></a>Gestire le disconnessioni del client

Il server non viene informato automaticamente quando il client si disconnette a causa della perdita di connettività. Il server riceve un messaggio di disconnessione solo se inviato dal client, ma questo non è possibile in caso di interruzione della connessione Internet. Se si vuole intervenire quando si verifica una situazione di questo tipo, impostare un timeout per segnalare che non sono stati ricevuti messaggi dal client entro un determinato intervallo di tempo.

Se il client non invia messaggi con una certa frequenza e non si vuole impostare un timeout solo perché la connessione diventa inattiva, configurare un timer nel client in modo da inviare un messaggio ping ogni X secondi. Nel server, se un messaggio non è arrivato entro 2\*X secondi dal precedente, terminare la connessione e segnalare che il client si è disconnesso. Attendere il doppio del tempo previsto per tenere conto di eventuali ritardi della rete che potrebbero trattenere il messaggio ping.

## <a name="websocket-origin-restriction"></a>Restrizione per le origini WebSocket

La protezione fornita da CORS non si applica agli oggetti WebSocket. I browser **non**:

* Eseguono richieste CORS preventive.
* Rispettano le restrizioni specificate nelle intestazioni `Access-Control` quando eseguono richieste WebSocket.

I browser, tuttavia, inviano l'intestazione `Origin` quando rilasciano richieste WebSocket. Le applicazioni devono essere configurate per la convalida di queste intestazioni per assicurarsi che siano consentiti solo WebSocket provenienti dalle origini previste.

Se si ospita il server in "https://server.com" e il client in "https://client.com", aggiungere "https://client.com" all'elenco `AllowedOrigins` per consentire la verifica dei WebSocket.

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=UseWebSocketsOptionsAO&highlight=6-7)]

> [!NOTE]
> L'intestazione `Origin` viene controllata dal client e, come l'intestazione `Referer`, può essere falsificata. **Non** usare queste intestazioni come meccanismo di autenticazione.

::: moniker-end

## <a name="iisiis-express-support"></a>Supporto di IIS/IIS Express

Windows Server 2012 o versioni successive e Windows 8 o versioni successive con IIS/IIS Express 8 o versioni successive includono il supporto per il protocollo WebSocket.

> [!NOTE]
> Gli oggetti WebSocket sono sempre abilitati quando si usa IIS Express.

### <a name="enabling-websockets-on-iis"></a>Abilitazione di oggetti WebSocket in IIS

Per abilitare il supporto per il protocollo WebSocket in Windows Server 2012 o versioni successive:

> [!NOTE]
> Questi passaggi non sono necessari quando si usa IIS Express

1. Usare la procedura guidata **Aggiungi ruoli e funzionalità** accessibile tramite il menu **Gestisci** o il collegamento in **Server Manager**.
1. Selezionare **Installazione basata su ruoli o basata su funzionalità**. Selezionare **Avanti**.
1. Selezionare il server appropriato (il server locale è selezionato per impostazione predefinita). Selezionare **Avanti**.
1. Espandere **Server Web (IIS)** nella struttura **Ruoli**, espandere **Server Web**, quindi **Sviluppo applicazioni**.
1. Selezionare **Protocollo WebSocket**. Selezionare **Avanti**.
1. Se non sono necessarie le funzionalità aggiuntive, selezionare **Avanti**.
1. Selezionare **Installa**.
1. Al termine dell'installazione, selezionare **Chiudi** per chiudere la procedura guidata.

Per abilitare il supporto per il protocollo WebSocket in Windows 8 o versioni successive:

> [!NOTE]
> Questi passaggi non sono necessari quando si usa IIS Express

1. Passare a**Programmi** > **e funzionalità** >  **del Pannello di** > controllo**Attiva o disattiva le funzionalità di Windows** (lato sinistro dello schermo).
1. Aprire i nodi seguenti: **Internet Information Services** > **World Wide Web Services** > **Application Development Features**.
1. Selezionare la funzionalità **Protocollo WebSocket**. Selezionare **OK**.

### <a name="disable-websocket-when-using-socketio-on-nodejs"></a>Disabilitare WebSocket quando si usa socket.io su node.js

Se si utilizza il supporto WebSocket in [socket.io](https://socket.io/) su [Node.js](https://nodejs.org/) `webSocket` , disabilitare il modulo WebSocket IIS predefinito utilizzando l'elemento in *web.config* o *applicationHost.config*. Se questo passaggio non viene eseguito, il modulo IIS WebSocket tenta di gestire la comunicazione WebSocket anziché Node.js e l'app.

```xml
<system.webServer>
  <webSocket enabled="false" />
</system.webServer>
```

## <a name="sample-app"></a>App di esempio

L'[app di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/websockets/samples) inclusa in questo articolo è un'app echo. Ha una pagina Web che consente la connessione WebSocket e il server reinvia al client tutti i messaggi ricevuti. Eseguire l'app da un prompt dei comandi, in quanto non è configurata per l'esecuzione da Visual Studio con IIS Express, quindi passare a http://localhost:5000. Nella parte superiore sinistra della pagina Web viene visualizzato lo stato della connessione:

![Stato iniziale della pagina Web](websockets/_static/start.png)

Selezionare **Connect** (Connetti) per inviare una richiesta WebSocket per l'URL indicato. Immettere un messaggio di prova e selezionare **Send** (Invia). Al termine dell'operazione, selezionare **Close Socket** (Chiudi socket). La sezione **Comminication Log** (Registrazione comunicazione) riporta tutte le azioni di apertura, invio e chiusura nel momento in cui vengono eseguite.

![Stato iniziale della pagina Web](websockets/_static/end.png)

