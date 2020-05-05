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
# <a name="aspnet-core-signalr-net-client"></a><span data-ttu-id="69a04-103">Client .NET di ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="69a04-103">ASP.NET Core SignalR .NET Client</span></span>

<span data-ttu-id="69a04-104">La libreria client di ASP.NET Core SignalR .NET consente di comunicare con gli hub SignalR dalle app .NET.</span><span class="sxs-lookup"><span data-stu-id="69a04-104">The ASP.NET Core SignalR .NET client library lets you communicate with SignalR hubs from .NET apps.</span></span>

<span data-ttu-id="69a04-105">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/dotnet-client/sample) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="69a04-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/dotnet-client/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="69a04-106">L'esempio di codice in questo articolo è un'app WPF che usa il client .NET di ASP.NET Core SignalR.</span><span class="sxs-lookup"><span data-stu-id="69a04-106">The code sample in this article is a WPF app that uses the ASP.NET Core SignalR .NET client.</span></span>

## <a name="install-the-signalr-net-client-package"></a><span data-ttu-id="69a04-107">Installare il pacchetto client .NET SignalR</span><span class="sxs-lookup"><span data-stu-id="69a04-107">Install the SignalR .NET client package</span></span>

<span data-ttu-id="69a04-108">Il pacchetto [Microsoft. AspNetCore. SignalR. client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) è necessario per consentire ai client .NET di connettersi agli hub SignalR.</span><span class="sxs-lookup"><span data-stu-id="69a04-108">The [Microsoft.AspNetCore.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package is required for .NET clients to connect to SignalR hubs.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="69a04-109">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="69a04-109">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="69a04-110">Per installare la libreria client, eseguire il comando seguente nella finestra della **console di gestione pacchetti** :</span><span class="sxs-lookup"><span data-stu-id="69a04-110">To install the client library, run the following command in the **Package Manager Console** window:</span></span>

```powershell
Install-Package Microsoft.AspNetCore.SignalR.Client
```

# <a name="net-core-cli"></a>[<span data-ttu-id="69a04-111">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="69a04-111">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="69a04-112">Per installare la libreria client, eseguire il comando seguente in una shell dei comandi:</span><span class="sxs-lookup"><span data-stu-id="69a04-112">To install the client library, run the following command in a command shell:</span></span>

```dotnetcli
dotnet add package Microsoft.AspNetCore.SignalR.Client
```

---

## <a name="connect-to-a-hub"></a><span data-ttu-id="69a04-113">Connettersi a un hub</span><span class="sxs-lookup"><span data-stu-id="69a04-113">Connect to a hub</span></span>

<span data-ttu-id="69a04-114">Per stabilire una connessione, creare un `HubConnectionBuilder` oggetto e `Build`chiamare.</span><span class="sxs-lookup"><span data-stu-id="69a04-114">To establish a connection, create a `HubConnectionBuilder` and call `Build`.</span></span> <span data-ttu-id="69a04-115">È possibile configurare l'URL dell'hub, il protocollo, il tipo di trasporto, il livello di registrazione, le intestazioni e altre opzioni durante la compilazione di una connessione.</span><span class="sxs-lookup"><span data-stu-id="69a04-115">The hub URL, protocol, transport type, log level, headers, and other options can be configured while building a connection.</span></span> <span data-ttu-id="69a04-116">Configurare le opzioni necessarie inserendo uno dei `HubConnectionBuilder` metodi in. `Build`</span><span class="sxs-lookup"><span data-stu-id="69a04-116">Configure any required options by inserting any of the `HubConnectionBuilder` methods into `Build`.</span></span> <span data-ttu-id="69a04-117">Avviare la connessione con `StartAsync`.</span><span class="sxs-lookup"><span data-stu-id="69a04-117">Start the connection with `StartAsync`.</span></span>

[!code-csharp[Build hub connection](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_MainWindowClass&highlight=15-17,39)]

## <a name="handle-lost-connection"></a><span data-ttu-id="69a04-118">Gestisci connessione persa</span><span class="sxs-lookup"><span data-stu-id="69a04-118">Handle lost connection</span></span>

::: moniker range=">= aspnetcore-3.0"

### <a name="automatically-reconnect"></a><span data-ttu-id="69a04-119">Riconnetti automaticamente</span><span class="sxs-lookup"><span data-stu-id="69a04-119">Automatically reconnect</span></span>

<span data-ttu-id="69a04-120">È <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection> possibile configurare per riconnettersi automaticamente utilizzando il `WithAutomaticReconnect` metodo su. <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder></span><span class="sxs-lookup"><span data-stu-id="69a04-120">The <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection> can be configured to automatically reconnect using the `WithAutomaticReconnect` method on the <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder>.</span></span> <span data-ttu-id="69a04-121">Per impostazione predefinita, non verrà riconnessa automaticamente.</span><span class="sxs-lookup"><span data-stu-id="69a04-121">It won't automatically reconnect by default.</span></span>

```csharp
HubConnection connection= new HubConnectionBuilder()
    .WithUrl(new Uri("http://127.0.0.1:5000/chatHub"))
    .WithAutomaticReconnect()
    .Build();
```

<span data-ttu-id="69a04-122">Senza parametri, `WithAutomaticReconnect()` configura il client in modo che attenda rispettivamente 0, 2, 10 e 30 secondi prima di provare ogni tentativo di riconnessione, arrestandosi dopo quattro tentativi non riusciti.</span><span class="sxs-lookup"><span data-stu-id="69a04-122">Without any parameters, `WithAutomaticReconnect()` configures the client to wait 0, 2, 10, and 30 seconds respectively before trying each reconnect attempt, stopping after four failed attempts.</span></span>

<span data-ttu-id="69a04-123">Prima di iniziare tutti i tentativi di riconnessione, il `HubConnection` passerà allo `HubConnectionState.Reconnecting` stato e `Reconnecting` genererà l'evento.</span><span class="sxs-lookup"><span data-stu-id="69a04-123">Before starting any reconnect attempts, the `HubConnection` will transition to the `HubConnectionState.Reconnecting` state and fire the `Reconnecting` event.</span></span>  <span data-ttu-id="69a04-124">Questo consente di avvisare gli utenti che la connessione è stata persa e di disabilitare gli elementi dell'interfaccia utente.</span><span class="sxs-lookup"><span data-stu-id="69a04-124">This provides an opportunity to warn users that the connection has been lost and to disable UI elements.</span></span> <span data-ttu-id="69a04-125">Le app non interattive possono avviare l'accodamento o l'eliminazione dei messaggi.</span><span class="sxs-lookup"><span data-stu-id="69a04-125">Non-interactive apps can start queuing or dropping messages.</span></span>

```csharp
connection.Reconnecting += error =>
{
    Debug.Assert(connection.State == HubConnectionState.Reconnecting);

    // Notify users the connection was lost and the client is reconnecting.
    // Start queuing or dropping messages.

    return Task.CompletedTask;
};
```

<span data-ttu-id="69a04-126">Se il client si riconnette correttamente entro i primi quattro tentativi, il `HubConnection` eseguirà la transizione allo `Connected` stato e attiverà l' `Reconnected` evento.</span><span class="sxs-lookup"><span data-stu-id="69a04-126">If the client successfully reconnects within its first four attempts, the `HubConnection` will transition back to the `Connected` state and fire the `Reconnected` event.</span></span> <span data-ttu-id="69a04-127">In questo modo è possibile informare gli utenti che la connessione è stata ristabilita e rimuovere dalla coda i messaggi in coda.</span><span class="sxs-lookup"><span data-stu-id="69a04-127">This provides an opportunity to inform users the connection has been reestablished and dequeue any queued messages.</span></span>

<span data-ttu-id="69a04-128">Poiché la connessione ha un aspetto completamente nuovo per il server, `ConnectionId` viene fornito un nuovo oggetto `Reconnected` ai gestori eventi.</span><span class="sxs-lookup"><span data-stu-id="69a04-128">Since the connection looks entirely new to the server, a new `ConnectionId` will be provided to the `Reconnected` event handlers.</span></span>

> [!WARNING]
> <span data-ttu-id="69a04-129">Il `Reconnected` `connectionId` parametro del gestore eventi sarà null se è stato `HubConnection` configurato per [ignorare la negoziazione](xref:signalr/configuration#configure-client-options).</span><span class="sxs-lookup"><span data-stu-id="69a04-129">The `Reconnected` event handler's `connectionId` parameter will be null if the `HubConnection` was configured to [skip negotiation](xref:signalr/configuration#configure-client-options).</span></span>

```csharp
connection.Reconnected += connectionId =>
{
    Debug.Assert(connection.State == HubConnectionState.Connected);

    // Notify users the connection was reestablished.
    // Start dequeuing messages queued while reconnecting if any.

    return Task.CompletedTask;
};
```

<span data-ttu-id="69a04-130">`WithAutomaticReconnect()`non configurerà per ritentare gli errori iniziali iniziali, quindi gli `HubConnection` errori di avvio devono essere gestiti manualmente:</span><span class="sxs-lookup"><span data-stu-id="69a04-130">`WithAutomaticReconnect()` won't configure the `HubConnection` to retry initial start failures, so start failures need to be handled manually:</span></span>

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

<span data-ttu-id="69a04-131">Se il client non si riconnette correttamente entro i primi quattro tentativi, `HubConnection` il passerà allo `Disconnected` stato e genererà <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection.Closed> l'evento.</span><span class="sxs-lookup"><span data-stu-id="69a04-131">If the client doesn't successfully reconnect within its first four attempts, the `HubConnection` will transition to the `Disconnected` state and fire the <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection.Closed> event.</span></span> <span data-ttu-id="69a04-132">Questo consente di provare a riavviare manualmente la connessione o informare gli utenti che la connessione è stata persa definitivamente.</span><span class="sxs-lookup"><span data-stu-id="69a04-132">This provides an opportunity to attempt to restart the connection manually or inform users the connection has been permanently lost.</span></span>

```csharp
connection.Closed += error =>
{
    Debug.Assert(connection.State == HubConnectionState.Disconnected);

    // Notify users the connection has been closed or manually try to restart the connection.

    return Task.CompletedTask;
};
```

<span data-ttu-id="69a04-133">Per configurare un numero personalizzato di tentativi di riconnessione prima di disconnettere o modificare la temporizzazione di riconnessione `WithAutomaticReconnect` , accetta una matrice di numeri che rappresenta il ritardo in millisecondi di attesa prima di avviare ogni tentativo di riconnessione.</span><span class="sxs-lookup"><span data-stu-id="69a04-133">In order to configure a custom number of reconnect attempts before disconnecting or change the reconnect timing, `WithAutomaticReconnect` accepts an array of numbers representing the delay in milliseconds to wait before starting each reconnect attempt.</span></span>

```csharp
HubConnection connection= new HubConnectionBuilder()
    .WithUrl(new Uri("http://127.0.0.1:5000/chatHub"))
    .WithAutomaticReconnect(new[] { TimeSpan.Zero, TimeSpan.Zero, TimeSpan.FromSeconds(10) })
    .Build();

    // .WithAutomaticReconnect(new[] { TimeSpan.Zero, TimeSpan.FromSeconds(2), TimeSpan.FromSeconds(10), TimeSpan.FromSeconds(30) }) yields the default behavior.
```

<span data-ttu-id="69a04-134">Nell'esempio precedente viene configurato per `HubConnection` avviare il tentativo di riconnessione immediatamente dopo la perdita della connessione.</span><span class="sxs-lookup"><span data-stu-id="69a04-134">The preceding example configures the `HubConnection` to start attempting reconnects immediately after the connection is lost.</span></span> <span data-ttu-id="69a04-135">Questo vale anche per la configurazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="69a04-135">This is also true for the default configuration.</span></span>

<span data-ttu-id="69a04-136">Se il primo tentativo di riconnessione ha esito negativo, anche il secondo tentativo di riconnessione verrà avviato immediatamente anziché attendere 2 secondi come nella configurazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="69a04-136">If the first reconnect attempt fails, the second reconnect attempt will also start immediately instead of waiting 2 seconds like it would in the default configuration.</span></span>

<span data-ttu-id="69a04-137">Se il secondo tentativo di riconnessione ha esito negativo, il terzo tentativo di riconnessione si avvierà entro 10 secondi, che è simile alla configurazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="69a04-137">If the second reconnect attempt fails, the third reconnect attempt will start in 10 seconds which is again like the default configuration.</span></span>

<span data-ttu-id="69a04-138">Il comportamento personalizzato, quindi, differisce di nuovo dal comportamento predefinito arrestando il terzo errore di tentativo di riconnessione.</span><span class="sxs-lookup"><span data-stu-id="69a04-138">The custom behavior then diverges again from the default behavior by stopping after the third reconnect attempt failure.</span></span> <span data-ttu-id="69a04-139">Nella configurazione predefinita verrà eseguito un altro tentativo di riconnessione in altri 30 secondi.</span><span class="sxs-lookup"><span data-stu-id="69a04-139">In the default configuration there would be one more reconnect attempt in another 30 seconds.</span></span>

<span data-ttu-id="69a04-140">Se si desidera un maggiore controllo sull'intervallo e sul numero di tentativi di riconnessione automatici `WithAutomaticReconnect` , accetta un oggetto che `IRetryPolicy` implementa l'interfaccia, che dispone di un `NextRetryDelay`singolo metodo denominato.</span><span class="sxs-lookup"><span data-stu-id="69a04-140">If you want even more control over the timing and number of automatic reconnect attempts, `WithAutomaticReconnect` accepts an object implementing the `IRetryPolicy` interface, which has a single method named `NextRetryDelay`.</span></span>

<span data-ttu-id="69a04-141">`NextRetryDelay`accetta un solo argomento con il tipo `RetryContext`.</span><span class="sxs-lookup"><span data-stu-id="69a04-141">`NextRetryDelay` takes a single argument with the type `RetryContext`.</span></span> <span data-ttu-id="69a04-142">`RetryContext` Dispone di tre proprietà: `PreviousRetryCount`, `ElapsedTime` e `RetryReason`, che sono `long` `Exception` rispettivamente un oggetto `TimeSpan` e un oggetto.</span><span class="sxs-lookup"><span data-stu-id="69a04-142">The `RetryContext` has three properties: `PreviousRetryCount`, `ElapsedTime` and `RetryReason`, which are a `long`, a `TimeSpan` and an `Exception` respectively.</span></span> <span data-ttu-id="69a04-143">Prima del primo tentativo di riconnessione, `PreviousRetryCount` sia `ElapsedTime` che saranno zero e `RetryReason` sarà l'eccezione che ha causato la perdita della connessione.</span><span class="sxs-lookup"><span data-stu-id="69a04-143">Before the first reconnect attempt, both `PreviousRetryCount` and `ElapsedTime` will be zero, and the `RetryReason` will be the Exception that caused the connection to be lost.</span></span> <span data-ttu-id="69a04-144">Dopo ogni tentativo non riuscito, `PreviousRetryCount` verrà incrementato di uno, `ElapsedTime` verrà aggiornato in modo da riflettere la quantità di tempo impiegato per la riconnessione fino a questo momento `RetryReason` e sarà l'eccezione che ha causato l'ultimo tentativo di riconnessione.</span><span class="sxs-lookup"><span data-stu-id="69a04-144">After each failed retry attempt, `PreviousRetryCount` will be incremented by one, `ElapsedTime` will be updated to reflect the amount of time spent reconnecting so far, and the `RetryReason` will be the Exception that caused the last reconnect attempt to fail.</span></span>

<span data-ttu-id="69a04-145">`NextRetryDelay`deve restituire un valore TimeSpan che rappresenta il tempo di attesa prima del successivo tentativo di riconnessione `null` o `HubConnection` se l'oggetto deve arrestare la riconnessione.</span><span class="sxs-lookup"><span data-stu-id="69a04-145">`NextRetryDelay` must return either a TimeSpan representing the time to wait before the next reconnect attempt or `null` if the `HubConnection` should stop reconnecting.</span></span>

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

<span data-ttu-id="69a04-146">In alternativa, è possibile scrivere codice per riconnettere manualmente il client, come illustrato in [riconnessione manuale](#manually-reconnect).</span><span class="sxs-lookup"><span data-stu-id="69a04-146">Alternatively, you can write code that will reconnect your client manually as demonstrated in [Manually reconnect](#manually-reconnect).</span></span>

::: moniker-end

### <a name="manually-reconnect"></a><span data-ttu-id="69a04-147">Riconnessione manuale</span><span class="sxs-lookup"><span data-stu-id="69a04-147">Manually reconnect</span></span>

::: moniker range="< aspnetcore-3.0"

> [!WARNING]
> <span data-ttu-id="69a04-148">Prima del 3,0, il client .NET per SignalR non si riconnette automaticamente.</span><span class="sxs-lookup"><span data-stu-id="69a04-148">Prior to 3.0, the .NET client for SignalR doesn't automatically reconnect.</span></span> <span data-ttu-id="69a04-149">È necessario scrivere codice che riconnetta il client manualmente.</span><span class="sxs-lookup"><span data-stu-id="69a04-149">You must write code that will reconnect your client manually.</span></span>

::: moniker-end

<span data-ttu-id="69a04-150">Usare l' <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection.Closed> evento per rispondere a una connessione persa.</span><span class="sxs-lookup"><span data-stu-id="69a04-150">Use the <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection.Closed> event to respond to a lost connection.</span></span> <span data-ttu-id="69a04-151">Ad esempio, potrebbe essere necessario automatizzare la riconnessione.</span><span class="sxs-lookup"><span data-stu-id="69a04-151">For example, you might want to automate reconnection.</span></span>

<span data-ttu-id="69a04-152">L' `Closed` evento richiede un delegato che restituisce un `Task`oggetto, che consente l'esecuzione del codice asincrono `async void`senza utilizzare.</span><span class="sxs-lookup"><span data-stu-id="69a04-152">The `Closed` event requires a delegate that returns a `Task`, which allows async code to run without using `async void`.</span></span> <span data-ttu-id="69a04-153">Per soddisfare la firma del delegato in `Closed` un gestore eventi che viene eseguito in modo `Task.CompletedTask`sincrono, restituire:</span><span class="sxs-lookup"><span data-stu-id="69a04-153">To satisfy the delegate signature in a `Closed` event handler that runs synchronously, return `Task.CompletedTask`:</span></span>

```csharp
connection.Closed += (error) => {
    // Do your close logic.
    return Task.CompletedTask;
};
```

<span data-ttu-id="69a04-154">Il motivo principale del supporto asincrono è che è quindi possibile riavviare la connessione.</span><span class="sxs-lookup"><span data-stu-id="69a04-154">The main reason for the async support is so you can restart the connection.</span></span> <span data-ttu-id="69a04-155">L'avvio di una connessione è un'azione asincrona.</span><span class="sxs-lookup"><span data-stu-id="69a04-155">Starting a connection is an async action.</span></span>

<span data-ttu-id="69a04-156">In un `Closed` gestore che riavvia la connessione, provare ad attendere un ritardo casuale per impedire l'overload del server, come illustrato nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="69a04-156">In a `Closed` handler that restarts the connection, consider waiting for some random delay to prevent overloading the server, as shown in the following example:</span></span>

[!code-csharp[Use Closed event handler to automate reconnection](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ClosedRestart)]

## <a name="call-hub-methods-from-client"></a><span data-ttu-id="69a04-157">Chiamare i metodi dell'hub dal client</span><span class="sxs-lookup"><span data-stu-id="69a04-157">Call hub methods from client</span></span>

<span data-ttu-id="69a04-158">`InvokeAsync`chiama i metodi sull'hub.</span><span class="sxs-lookup"><span data-stu-id="69a04-158">`InvokeAsync` calls methods on the hub.</span></span> <span data-ttu-id="69a04-159">Passare il nome del metodo dell'hub e gli eventuali argomenti definiti nel metodo `InvokeAsync`Hub a.</span><span class="sxs-lookup"><span data-stu-id="69a04-159">Pass the hub method name and any arguments defined in the hub method to `InvokeAsync`.</span></span> SignalR<span data-ttu-id="69a04-160">è asincrono, quindi usare `async` e `await` quando si effettuano le chiamate.</span><span class="sxs-lookup"><span data-stu-id="69a04-160"> is asynchronous, so use `async` and `await` when making the calls.</span></span>

[!code-csharp[InvokeAsync method](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_InvokeAsync)]

<span data-ttu-id="69a04-161">Il `InvokeAsync` metodo restituisce un `Task` oggetto che viene completato quando il metodo Server restituisce.</span><span class="sxs-lookup"><span data-stu-id="69a04-161">The `InvokeAsync` method returns a `Task` which completes when the server method returns.</span></span> <span data-ttu-id="69a04-162">Il valore restituito, se presente, viene fornito come risultato dell'oggetto `Task`.</span><span class="sxs-lookup"><span data-stu-id="69a04-162">The return value, if any, is provided as the result of the `Task`.</span></span> <span data-ttu-id="69a04-163">Eventuali eccezioni generate dal metodo nel server generano un errore `Task`.</span><span class="sxs-lookup"><span data-stu-id="69a04-163">Any exceptions thrown by the method on the server produce a faulted `Task`.</span></span> <span data-ttu-id="69a04-164">Utilizzare `await` la sintassi per attendere il completamento del metodo del server `try...catch` e la sintassi per la gestione degli errori.</span><span class="sxs-lookup"><span data-stu-id="69a04-164">Use `await` syntax to wait for the server method to complete and `try...catch` syntax to handle errors.</span></span>

<span data-ttu-id="69a04-165">Il `SendAsync` metodo restituisce un `Task` oggetto che viene completato quando il messaggio è stato inviato al server.</span><span class="sxs-lookup"><span data-stu-id="69a04-165">The `SendAsync` method returns a `Task` which completes when the message has been sent to the server.</span></span> <span data-ttu-id="69a04-166">Non viene fornito alcun valore restituito poiché `Task` questo non attende il completamento del metodo del server.</span><span class="sxs-lookup"><span data-stu-id="69a04-166">No return value is provided since this `Task` doesn't wait until the server method completes.</span></span> <span data-ttu-id="69a04-167">Tutte le eccezioni generate nel client durante l'invio del messaggio generano `Task`un errore.</span><span class="sxs-lookup"><span data-stu-id="69a04-167">Any exceptions thrown on the client while sending the message produce a faulted `Task`.</span></span> <span data-ttu-id="69a04-168">Utilizzare `await` la `try...catch` sintassi e per gestire gli errori di invio.</span><span class="sxs-lookup"><span data-stu-id="69a04-168">Use `await` and `try...catch` syntax to handle send errors.</span></span>

> [!NOTE]
> <span data-ttu-id="69a04-169">Se si usa il servizio SignalR di Azure in *modalità senza server*, non è possibile chiamare i metodi dell'hub da un client.</span><span class="sxs-lookup"><span data-stu-id="69a04-169">If you're using Azure SignalR Service in *Serverless mode*, you cannot call hub methods from a client.</span></span> <span data-ttu-id="69a04-170">Per ulteriori informazioni, vedere la [ SignalR documentazione del servizio](/azure/azure-signalr/signalr-concept-serverless-development-config).</span><span class="sxs-lookup"><span data-stu-id="69a04-170">For more information, see the [SignalR Service documentation](/azure/azure-signalr/signalr-concept-serverless-development-config).</span></span>

## <a name="call-client-methods-from-hub"></a><span data-ttu-id="69a04-171">Chiamare i metodi client dall'hub</span><span class="sxs-lookup"><span data-stu-id="69a04-171">Call client methods from hub</span></span>

<span data-ttu-id="69a04-172">Definire i metodi che l'hub `connection.On` chiama usando dopo la compilazione, ma prima di avviare la connessione.</span><span class="sxs-lookup"><span data-stu-id="69a04-172">Define methods the hub calls using `connection.On` after building, but before starting the connection.</span></span>

[!code-csharp[Define client methods](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ConnectionOn)]

<span data-ttu-id="69a04-173">Il codice precedente in `connection.On` viene eseguito quando il codice lato server lo chiama usando `SendAsync` il metodo.</span><span class="sxs-lookup"><span data-stu-id="69a04-173">The preceding code in `connection.On` runs when server-side code calls it using the `SendAsync` method.</span></span>

[!code-csharp[Call client method](dotnet-client/sample/signalrchat/hubs/chathub.cs?name=snippet_SendMessage)]

## <a name="error-handling-and-logging"></a><span data-ttu-id="69a04-174">Registrazione e gestione degli errori</span><span class="sxs-lookup"><span data-stu-id="69a04-174">Error handling and logging</span></span>

<span data-ttu-id="69a04-175">Gestire gli errori con un'istruzione try-catch.</span><span class="sxs-lookup"><span data-stu-id="69a04-175">Handle errors with a try-catch statement.</span></span> <span data-ttu-id="69a04-176">Controllare l' `Exception` oggetto per determinare l'azione appropriata da eseguire dopo che si è verificato un errore.</span><span class="sxs-lookup"><span data-stu-id="69a04-176">Inspect the `Exception` object to determine the proper action to take after an error occurs.</span></span>

[!code-csharp[Logging](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ErrorHandling)]

## <a name="additional-resources"></a><span data-ttu-id="69a04-177">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="69a04-177">Additional resources</span></span>

* [<span data-ttu-id="69a04-178">Hub</span><span class="sxs-lookup"><span data-stu-id="69a04-178">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="69a04-179">Client JavaScript</span><span class="sxs-lookup"><span data-stu-id="69a04-179">JavaScript client</span></span>](xref:signalr/javascript-client)
* [<span data-ttu-id="69a04-180">Pubblicazione in Azure</span><span class="sxs-lookup"><span data-stu-id="69a04-180">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
* <span data-ttu-id="69a04-181">[Documentazione SignalR senza server per i servizi di Azure](/azure/azure-signalr/signalr-concept-serverless-development-config)</span><span class="sxs-lookup"><span data-stu-id="69a04-181">[Azure SignalR Service serverless documentation](/azure/azure-signalr/signalr-concept-serverless-development-config)</span></span>
