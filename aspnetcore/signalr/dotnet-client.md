---
title: Client SignalR .NET ASP.NET Core
author: bradygaster
description: Informazioni sul client di SignalR ASP.NET Core .NET
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 01/14/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/dotnet-client
ms.openlocfilehash: 77d7eb81abc4ec7a6f4f15bbe5d96cedc64cb330
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82767213"
---
# <a name="aspnet-core-signalr-net-client"></a>Client .NET di ASP.NET Core SignalR

La libreria client di ASP.NET Core SignalR .NET consente di comunicare con gli hub SignalR dalle app .NET.

[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/dotnet-client/sample) ([procedura per il download](xref:index#how-to-download-a-sample))

L'esempio di codice in questo articolo è un'app WPF che usa il client .NET di ASP.NET Core SignalR.

## <a name="install-the-signalr-net-client-package"></a>Installare il pacchetto client .NET SignalR

Il pacchetto [Microsoft. AspNetCore. SignalR. client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) è necessario per consentire ai client .NET di connettersi agli hub SignalR.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Per installare la libreria client, eseguire il comando seguente nella finestra della **console di gestione pacchetti** :

```powershell
Install-Package Microsoft.AspNetCore.SignalR.Client
```

# <a name="net-core-cli"></a>[Interfaccia della riga di comando di .NET Core](#tab/netcore-cli)

Per installare la libreria client, eseguire il comando seguente in una shell dei comandi:

```dotnetcli
dotnet add package Microsoft.AspNetCore.SignalR.Client
```

---

## <a name="connect-to-a-hub"></a>Connettersi a un hub

Per stabilire una connessione, creare un `HubConnectionBuilder` oggetto e `Build`chiamare. È possibile configurare l'URL dell'hub, il protocollo, il tipo di trasporto, il livello di registrazione, le intestazioni e altre opzioni durante la compilazione di una connessione. Configurare le opzioni necessarie inserendo uno dei `HubConnectionBuilder` metodi in. `Build` Avviare la connessione con `StartAsync`.

[!code-csharp[Build hub connection](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_MainWindowClass&highlight=15-17,39)]

## <a name="handle-lost-connection"></a>Gestisci connessione persa

::: moniker range=">= aspnetcore-3.0"

### <a name="automatically-reconnect"></a>Riconnetti automaticamente

È <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection> possibile configurare per riconnettersi automaticamente utilizzando il `WithAutomaticReconnect` metodo su. <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder> Per impostazione predefinita, non verrà riconnessa automaticamente.

```csharp
HubConnection connection= new HubConnectionBuilder()
    .WithUrl(new Uri("http://127.0.0.1:5000/chatHub"))
    .WithAutomaticReconnect()
    .Build();
```

Senza parametri, `WithAutomaticReconnect()` configura il client in modo che attenda rispettivamente 0, 2, 10 e 30 secondi prima di provare ogni tentativo di riconnessione, arrestandosi dopo quattro tentativi non riusciti.

Prima di iniziare tutti i tentativi di riconnessione, il `HubConnection` passerà allo `HubConnectionState.Reconnecting` stato e `Reconnecting` genererà l'evento.  Questo consente di avvisare gli utenti che la connessione è stata persa e di disabilitare gli elementi dell'interfaccia utente. Le app non interattive possono avviare l'accodamento o l'eliminazione dei messaggi.

```csharp
connection.Reconnecting += error =>
{
    Debug.Assert(connection.State == HubConnectionState.Reconnecting);

    // Notify users the connection was lost and the client is reconnecting.
    // Start queuing or dropping messages.

    return Task.CompletedTask;
};
```

Se il client si riconnette correttamente entro i primi quattro tentativi, il `HubConnection` eseguirà la transizione allo `Connected` stato e attiverà l' `Reconnected` evento. In questo modo è possibile informare gli utenti che la connessione è stata ristabilita e rimuovere dalla coda i messaggi in coda.

Poiché la connessione ha un aspetto completamente nuovo per il server, `ConnectionId` viene fornito un nuovo oggetto `Reconnected` ai gestori eventi.

> [!WARNING]
> Il `Reconnected` `connectionId` parametro del gestore eventi sarà null se è stato `HubConnection` configurato per [ignorare la negoziazione](xref:signalr/configuration#configure-client-options).

```csharp
connection.Reconnected += connectionId =>
{
    Debug.Assert(connection.State == HubConnectionState.Connected);

    // Notify users the connection was reestablished.
    // Start dequeuing messages queued while reconnecting if any.

    return Task.CompletedTask;
};
```

`WithAutomaticReconnect()`non configurerà per ritentare gli errori iniziali iniziali, quindi gli `HubConnection` errori di avvio devono essere gestiti manualmente:

```csharp
public static async Task<bool> ConnectWithRetryAsync(HubConnection connection, CancellationToken token)
{
    // Keep trying to until we can start or the token is canceled.
    while (true)
    {
        try
        {
            await connection.StartAsync(token);
            Debug.Assert(connection.State == HubConnectionState.Connected);
            return true;
        }
        catch when (token.IsCancellationRequested)
        {
            return false;
        }
        catch
        {
            // Failed to connect, trying again in 5000 ms.
            Debug.Assert(connection.State == HubConnectionState.Disconnected);
            await Task.Delay(5000);
        }
    }
}
```

Se il client non si riconnette correttamente entro i primi quattro tentativi, `HubConnection` il passerà allo `Disconnected` stato e genererà <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection.Closed> l'evento. Questo consente di provare a riavviare manualmente la connessione o informare gli utenti che la connessione è stata persa definitivamente.

```csharp
connection.Closed += error =>
{
    Debug.Assert(connection.State == HubConnectionState.Disconnected);

    // Notify users the connection has been closed or manually try to restart the connection.

    return Task.CompletedTask;
};
```

Per configurare un numero personalizzato di tentativi di riconnessione prima di disconnettere o modificare la temporizzazione di riconnessione `WithAutomaticReconnect` , accetta una matrice di numeri che rappresenta il ritardo in millisecondi di attesa prima di avviare ogni tentativo di riconnessione.

```csharp
HubConnection connection= new HubConnectionBuilder()
    .WithUrl(new Uri("http://127.0.0.1:5000/chatHub"))
    .WithAutomaticReconnect(new[] { TimeSpan.Zero, TimeSpan.Zero, TimeSpan.FromSeconds(10) })
    .Build();

    // .WithAutomaticReconnect(new[] { TimeSpan.Zero, TimeSpan.FromSeconds(2), TimeSpan.FromSeconds(10), TimeSpan.FromSeconds(30) }) yields the default behavior.
```

Nell'esempio precedente viene configurato per `HubConnection` avviare il tentativo di riconnessione immediatamente dopo la perdita della connessione. Questo vale anche per la configurazione predefinita.

Se il primo tentativo di riconnessione ha esito negativo, anche il secondo tentativo di riconnessione verrà avviato immediatamente anziché attendere 2 secondi come nella configurazione predefinita.

Se il secondo tentativo di riconnessione ha esito negativo, il terzo tentativo di riconnessione si avvierà entro 10 secondi, che è simile alla configurazione predefinita.

Il comportamento personalizzato, quindi, differisce di nuovo dal comportamento predefinito arrestando il terzo errore di tentativo di riconnessione. Nella configurazione predefinita verrà eseguito un altro tentativo di riconnessione in altri 30 secondi.

Se si desidera un maggiore controllo sull'intervallo e sul numero di tentativi di riconnessione automatici `WithAutomaticReconnect` , accetta un oggetto che `IRetryPolicy` implementa l'interfaccia, che dispone di un `NextRetryDelay`singolo metodo denominato.

`NextRetryDelay`accetta un solo argomento con il tipo `RetryContext`. `RetryContext` Dispone di tre proprietà: `PreviousRetryCount`, `ElapsedTime` e `RetryReason`, che sono `long` `Exception` rispettivamente un oggetto `TimeSpan` e un oggetto. Prima del primo tentativo di riconnessione, `PreviousRetryCount` sia `ElapsedTime` che saranno zero e `RetryReason` sarà l'eccezione che ha causato la perdita della connessione. Dopo ogni tentativo non riuscito, `PreviousRetryCount` verrà incrementato di uno, `ElapsedTime` verrà aggiornato in modo da riflettere la quantità di tempo impiegato per la riconnessione fino a questo momento `RetryReason` e sarà l'eccezione che ha causato l'ultimo tentativo di riconnessione.

`NextRetryDelay`deve restituire un valore TimeSpan che rappresenta il tempo di attesa prima del successivo tentativo di riconnessione `null` o `HubConnection` se l'oggetto deve arrestare la riconnessione.

```csharp
public class RandomRetryPolicy : IRetryPolicy
{
    private readonly Random _random = new Random();

    public TimeSpan? NextRetryDelay(RetryContext retryContext)
    {
        // If we've been reconnecting for less than 60 seconds so far,
        // wait between 0 and 10 seconds before the next reconnect attempt.
        if (retryContext.ElapsedTime < TimeSpan.FromSeconds(60))
        {
            return TimeSpan.FromSeconds(_random.NextDouble() * 10);
        }
        else
        {
            // If we've been reconnecting for more than 60 seconds so far, stop reconnecting.
            return null;
        }
    }
}
```

```csharp
HubConnection connection = new HubConnectionBuilder()
    .WithUrl(new Uri("http://127.0.0.1:5000/chatHub"))
    .WithAutomaticReconnect(new RandomRetryPolicy())
    .Build();
```

In alternativa, è possibile scrivere codice per riconnettere manualmente il client, come illustrato in [riconnessione manuale](#manually-reconnect).

::: moniker-end

### <a name="manually-reconnect"></a>Riconnessione manuale

::: moniker range="< aspnetcore-3.0"

> [!WARNING]
> Prima del 3,0, il client .NET per SignalR non si riconnette automaticamente. È necessario scrivere codice che riconnetta il client manualmente.

::: moniker-end

Usare l' <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection.Closed> evento per rispondere a una connessione persa. Ad esempio, potrebbe essere necessario automatizzare la riconnessione.

L' `Closed` evento richiede un delegato che restituisce un `Task`oggetto, che consente l'esecuzione del codice asincrono `async void`senza utilizzare. Per soddisfare la firma del delegato in `Closed` un gestore eventi che viene eseguito in modo `Task.CompletedTask`sincrono, restituire:

```csharp
connection.Closed += (error) => {
    // Do your close logic.
    return Task.CompletedTask;
};
```

Il motivo principale del supporto asincrono è che è quindi possibile riavviare la connessione. L'avvio di una connessione è un'azione asincrona.

In un `Closed` gestore che riavvia la connessione, provare ad attendere un ritardo casuale per impedire l'overload del server, come illustrato nell'esempio seguente:

[!code-csharp[Use Closed event handler to automate reconnection](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ClosedRestart)]

## <a name="call-hub-methods-from-client"></a>Chiamare i metodi dell'hub dal client

`InvokeAsync`chiama i metodi sull'hub. Passare il nome del metodo dell'hub e gli eventuali argomenti definiti nel metodo `InvokeAsync`Hub a. SignalRè asincrono, quindi usare `async` e `await` quando si effettuano le chiamate.

[!code-csharp[InvokeAsync method](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_InvokeAsync)]

Il `InvokeAsync` metodo restituisce un `Task` oggetto che viene completato quando il metodo Server restituisce. Il valore restituito, se presente, viene fornito come risultato dell'oggetto `Task`. Eventuali eccezioni generate dal metodo nel server generano un errore `Task`. Utilizzare `await` la sintassi per attendere il completamento del metodo del server `try...catch` e la sintassi per la gestione degli errori.

Il `SendAsync` metodo restituisce un `Task` oggetto che viene completato quando il messaggio è stato inviato al server. Non viene fornito alcun valore restituito poiché `Task` questo non attende il completamento del metodo del server. Tutte le eccezioni generate nel client durante l'invio del messaggio generano `Task`un errore. Utilizzare `await` la `try...catch` sintassi e per gestire gli errori di invio.

> [!NOTE]
> Se si usa il servizio SignalR di Azure in *modalità senza server*, non è possibile chiamare i metodi dell'hub da un client. Per ulteriori informazioni, vedere la [ SignalR documentazione del servizio](/azure/azure-signalr/signalr-concept-serverless-development-config).

## <a name="call-client-methods-from-hub"></a>Chiamare i metodi client dall'hub

Definire i metodi che l'hub `connection.On` chiama usando dopo la compilazione, ma prima di avviare la connessione.

[!code-csharp[Define client methods](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ConnectionOn)]

Il codice precedente in `connection.On` viene eseguito quando il codice lato server lo chiama usando `SendAsync` il metodo.

[!code-csharp[Call client method](dotnet-client/sample/signalrchat/hubs/chathub.cs?name=snippet_SendMessage)]

## <a name="error-handling-and-logging"></a>Registrazione e gestione degli errori

Gestire gli errori con un'istruzione try-catch. Controllare l' `Exception` oggetto per determinare l'azione appropriata da eseguire dopo che si è verificato un errore.

[!code-csharp[Logging](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ErrorHandling)]

## <a name="additional-resources"></a>Risorse aggiuntive

* [Hub](xref:signalr/hubs)
* [Client JavaScript](xref:signalr/javascript-client)
* [Pubblicazione in Azure](xref:signalr/publish-to-azure-web-app)
* [Documentazione SignalR senza server per i servizi di Azure](/azure/azure-signalr/signalr-concept-serverless-development-config)
