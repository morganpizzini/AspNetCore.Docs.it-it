---
title: ASP.NET Core SignalR client JavaScript
author: bradygaster
description: Panoramica di ASP.NET Core SignalR client JavaScript.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/08/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/javascript-client
ms.openlocfilehash: 6f611e56ec62ad7aea8a93e4761e1f67d0f76574
ms.sourcegitcommit: fad0cd264c9d07a48a8c6ba1690807e0f8728898
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92379459"
---
# <a name="aspnet-core-no-locsignalr-javascript-client"></a><span data-ttu-id="637f0-103">ASP.NET Core SignalR client JavaScript</span><span class="sxs-lookup"><span data-stu-id="637f0-103">ASP.NET Core SignalR JavaScript client</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="637f0-104">Di [Rachel Appel](https://twitter.com/rachelappel)</span><span class="sxs-lookup"><span data-stu-id="637f0-104">By [Rachel Appel](https://twitter.com/rachelappel)</span></span>

<span data-ttu-id="637f0-105">La SignalR libreria client JavaScript ASP.NET Core consente agli sviluppatori di chiamare il codice dell'hub sul lato server.</span><span class="sxs-lookup"><span data-stu-id="637f0-105">The ASP.NET Core SignalR JavaScript client library enables developers to call server-side hub code.</span></span>

<span data-ttu-id="637f0-106">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/javascript-client/samples) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="637f0-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/javascript-client/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="install-the-no-locsignalr-client-package"></a><span data-ttu-id="637f0-107">Installare il SignalR pacchetto client</span><span class="sxs-lookup"><span data-stu-id="637f0-107">Install the SignalR client package</span></span>

<span data-ttu-id="637f0-108">La SignalR libreria client JavaScript viene distribuita come pacchetto [NPM](https://www.npmjs.com/) .</span><span class="sxs-lookup"><span data-stu-id="637f0-108">The SignalR JavaScript client library is delivered as an [npm](https://www.npmjs.com/) package.</span></span> <span data-ttu-id="637f0-109">Le sezioni seguenti illustrano diversi modi per installare la libreria client.</span><span class="sxs-lookup"><span data-stu-id="637f0-109">The following sections outline different ways to install the client library.</span></span>

### <a name="install-with-npm"></a><span data-ttu-id="637f0-110">Eseguire l'installazione con NPM</span><span class="sxs-lookup"><span data-stu-id="637f0-110">Install with npm</span></span>

<span data-ttu-id="637f0-111">Per Visual Studio, eseguire i comandi seguenti dalla **console di gestione pacchetti** nella cartella radice.</span><span class="sxs-lookup"><span data-stu-id="637f0-111">For Visual Studio, run the following commands from **Package Manager Console** while in the root folder.</span></span> <span data-ttu-id="637f0-112">Per Visual Studio Code, eseguire i comandi seguenti dal **terminale integrato**.</span><span class="sxs-lookup"><span data-stu-id="637f0-112">For Visual Studio Code, run the following commands from the **Integrated Terminal**.</span></span>

```bash
npm init -y
npm install @microsoft/signalr
```

<span data-ttu-id="637f0-113">NPM installa il contenuto del pacchetto nella *cartella \\ @microsoft\signalr\dist\browser node_modules* .</span><span class="sxs-lookup"><span data-stu-id="637f0-113">npm installs the package contents in the *node_modules\\@microsoft\signalr\dist\browser* folder.</span></span> <span data-ttu-id="637f0-114">Creare una nuova cartella denominata *SignalR* nella cartella *wwwroot \\ lib* .</span><span class="sxs-lookup"><span data-stu-id="637f0-114">Create a new folder named *signalr* under the *wwwroot\\lib* folder.</span></span> <span data-ttu-id="637f0-115">Copiare il file *signalr.js* nella cartella *wwwroot\lib\signalr* .</span><span class="sxs-lookup"><span data-stu-id="637f0-115">Copy the *signalr.js* file to the *wwwroot\lib\signalr* folder.</span></span>

<span data-ttu-id="637f0-116">Fare riferimento al SignalR client JavaScript nell' `<script>` elemento.</span><span class="sxs-lookup"><span data-stu-id="637f0-116">Reference the SignalR JavaScript client in the `<script>` element.</span></span> <span data-ttu-id="637f0-117">Esempio:</span><span class="sxs-lookup"><span data-stu-id="637f0-117">For example:</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
```

### <a name="use-a-content-delivery-network-cdn"></a><span data-ttu-id="637f0-118">Usare una rete per la distribuzione di contenuti (CDN)</span><span class="sxs-lookup"><span data-stu-id="637f0-118">Use a Content Delivery Network (CDN)</span></span>

<span data-ttu-id="637f0-119">Per usare la libreria client senza il prerequisito NPM, fare riferimento a una copia ospitata dalla rete CDN della libreria client.</span><span class="sxs-lookup"><span data-stu-id="637f0-119">To use the client library without the npm prerequisite, reference a CDN-hosted copy of the client library.</span></span> <span data-ttu-id="637f0-120">Esempio:</span><span class="sxs-lookup"><span data-stu-id="637f0-120">For example:</span></span>

[!code-html[](javascript-client/samples/3.x/SignalRChat/Pages/Index.cshtml?name=snippet_CDN)]

<span data-ttu-id="637f0-121">La libreria client è disponibile nei seguenti CDNs:</span><span class="sxs-lookup"><span data-stu-id="637f0-121">The client library is available on the following CDNs:</span></span>

* [<span data-ttu-id="637f0-122">cdnjs</span><span class="sxs-lookup"><span data-stu-id="637f0-122">cdnjs</span></span>](https://cdnjs.com/libraries/microsoft-signalr)
* [<span data-ttu-id="637f0-123">jsDelivr</span><span class="sxs-lookup"><span data-stu-id="637f0-123">jsDelivr</span></span>](https://www.jsdelivr.com/package/npm/@microsoft/signalr)
* [<span data-ttu-id="637f0-124">unpkg</span><span class="sxs-lookup"><span data-stu-id="637f0-124">unpkg</span></span>](https://unpkg.com/@microsoft/signalr@next/dist/browser/signalr.min.js)

### <a name="install-with-libman"></a><span data-ttu-id="637f0-125">Installare con LibMan</span><span class="sxs-lookup"><span data-stu-id="637f0-125">Install with LibMan</span></span>

<span data-ttu-id="637f0-126">[LibMan](xref:client-side/libman/index) può essere usato per installare file di libreria client specifici dalla libreria client ospitata nella rete CDN.</span><span class="sxs-lookup"><span data-stu-id="637f0-126">[LibMan](xref:client-side/libman/index) can be used to install specific client library files from the CDN-hosted client library.</span></span> <span data-ttu-id="637f0-127">Ad esempio, aggiungere solo il file JavaScript minimizzati al progetto.</span><span class="sxs-lookup"><span data-stu-id="637f0-127">For example, only add the minified JavaScript file to the project.</span></span> <span data-ttu-id="637f0-128">Per informazioni dettagliate su questo approccio, vedere [aggiungere la SignalR libreria client](xref:tutorials/signalr#add-the-signalr-client-library).</span><span class="sxs-lookup"><span data-stu-id="637f0-128">For details on that approach, see [Add the SignalR client library](xref:tutorials/signalr#add-the-signalr-client-library).</span></span>

## <a name="connect-to-a-hub"></a><span data-ttu-id="637f0-129">Connettersi a un hub</span><span class="sxs-lookup"><span data-stu-id="637f0-129">Connect to a hub</span></span>

<span data-ttu-id="637f0-130">Il codice seguente crea e avvia una connessione.</span><span class="sxs-lookup"><span data-stu-id="637f0-130">The following code creates and starts a connection.</span></span> <span data-ttu-id="637f0-131">Il nome dell'hub non fa distinzione tra maiuscole e minuscole:</span><span class="sxs-lookup"><span data-stu-id="637f0-131">The hub's name is case insensitive:</span></span>

[!code-javascript[](javascript-client/samples/3.x/SignalRChat/wwwroot/chat.js?range=3-6,29-43)]

### <a name="cross-origin-connections"></a><span data-ttu-id="637f0-132">Connessioni tra le origini</span><span class="sxs-lookup"><span data-stu-id="637f0-132">Cross-origin connections</span></span>

<span data-ttu-id="637f0-133">In genere, i browser caricano le connessioni dallo stesso dominio della pagina richiesta.</span><span class="sxs-lookup"><span data-stu-id="637f0-133">Typically, browsers load connections from the same domain as the requested page.</span></span> <span data-ttu-id="637f0-134">In alcuni casi, tuttavia, è necessaria una connessione a un altro dominio.</span><span class="sxs-lookup"><span data-stu-id="637f0-134">However, there are occasions when a connection to another domain is required.</span></span>

<span data-ttu-id="637f0-135">Per impedire a un sito dannoso di leggere dati sensibili da un altro sito, le [connessioni tra le origini](xref:security/cors) sono disabilitate per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="637f0-135">To prevent a malicious site from reading sensitive data from another site, [cross-origin connections](xref:security/cors) are disabled by default.</span></span> <span data-ttu-id="637f0-136">Per consentire una richiesta tra origini, abilitarla nella `Startup` classe:</span><span class="sxs-lookup"><span data-stu-id="637f0-136">To allow a cross-origin request, enable it in the `Startup` class:</span></span>

[!code-csharp[](javascript-client/samples/3.x/SignalRChat/Startup.cs?highlight=16-23,40)]

## <a name="call-hub-methods-from-the-client"></a><span data-ttu-id="637f0-137">Chiamare i metodi dell'hub dal client</span><span class="sxs-lookup"><span data-stu-id="637f0-137">Call hub methods from the client</span></span>

<span data-ttu-id="637f0-138">I client JavaScript chiamano metodi pubblici sugli hub tramite il metodo [Invoke](/javascript/api/%40microsoft/signalr/hubconnection#invoke-string--any---) di [HubConnection](/javascript/api/%40microsoft/signalr/hubconnection).</span><span class="sxs-lookup"><span data-stu-id="637f0-138">JavaScript clients call public methods on hubs via the [invoke](/javascript/api/%40microsoft/signalr/hubconnection#invoke-string--any---) method of the [HubConnection](/javascript/api/%40microsoft/signalr/hubconnection).</span></span> <span data-ttu-id="637f0-139">Il `invoke` metodo accetta:</span><span class="sxs-lookup"><span data-stu-id="637f0-139">The `invoke` method accepts:</span></span>

* <span data-ttu-id="637f0-140">Nome del metodo dell'hub.</span><span class="sxs-lookup"><span data-stu-id="637f0-140">The name of the hub method.</span></span>
* <span data-ttu-id="637f0-141">Qualsiasi argomento definito nel metodo dell'hub.</span><span class="sxs-lookup"><span data-stu-id="637f0-141">Any arguments defined in the hub method.</span></span>

<span data-ttu-id="637f0-142">Nell'esempio seguente il nome del metodo nell'hub è `SendMessage` .</span><span class="sxs-lookup"><span data-stu-id="637f0-142">In the following example, the method name on the hub is `SendMessage`.</span></span> <span data-ttu-id="637f0-143">Il secondo e il terzo argomento passati per eseguire il `invoke` mapping agli argomenti e del metodo Hub `user` `message` :</span><span class="sxs-lookup"><span data-stu-id="637f0-143">The second and third arguments passed to `invoke` map to the hub method's `user` and `message` arguments:</span></span>

[!code-javascript[](javascript-client/samples/3.x/SignalRChat/wwwroot/chat.js?name=snippet_Invoke&highlight=2)]

> [!NOTE]
> <span data-ttu-id="637f0-144">La chiamata di metodi dell'hub da un client è supportata solo quando si usa il SignalR servizio di Azure in modalità *predefinita* .</span><span class="sxs-lookup"><span data-stu-id="637f0-144">Calling hub methods from a client is only supported when using the Azure SignalR Service in *Default* mode.</span></span> <span data-ttu-id="637f0-145">Per altre informazioni, vedere [domande frequenti (repository GitHub di Azure-SignalR)](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose).</span><span class="sxs-lookup"><span data-stu-id="637f0-145">For more information, see [Frequently Asked Questions (azure-signalr GitHub repository)](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose).</span></span>

<span data-ttu-id="637f0-146">Il `invoke` metodo restituisce una [promessa](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)JavaScript.</span><span class="sxs-lookup"><span data-stu-id="637f0-146">The `invoke` method returns a JavaScript [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise).</span></span> <span data-ttu-id="637f0-147">`Promise`Viene risolto con il valore restituito (se presente) quando il metodo nel server restituisce.</span><span class="sxs-lookup"><span data-stu-id="637f0-147">The `Promise` is resolved with the return value (if any) when the method on the server returns.</span></span> <span data-ttu-id="637f0-148">Se il metodo sul server genera un errore, `Promise` viene rifiutato con il messaggio di errore.</span><span class="sxs-lookup"><span data-stu-id="637f0-148">If the method on the server throws an error, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="637f0-149">Usare `async` e `await` o i `Promise` `then` metodi e `catch` per gestire questi casi.</span><span class="sxs-lookup"><span data-stu-id="637f0-149">Use `async` and `await` or the `Promise`'s `then` and `catch` methods to handle these cases.</span></span>

<span data-ttu-id="637f0-150">I client JavaScript possono anche chiamare metodi pubblici sugli hub tramite il metodo [Send](/javascript/api/%40microsoft/signalr/hubconnection#send-string--any---) di `HubConnection` .</span><span class="sxs-lookup"><span data-stu-id="637f0-150">JavaScript clients can also call public methods on hubs via the the [send](/javascript/api/%40microsoft/signalr/hubconnection#send-string--any---) method of the `HubConnection`.</span></span> <span data-ttu-id="637f0-151">A differenza del `invoke` metodo, il `send` metodo non attende una risposta dal server.</span><span class="sxs-lookup"><span data-stu-id="637f0-151">Unlike the `invoke` method, the `send` method doesn't wait for a response from the server.</span></span> <span data-ttu-id="637f0-152">Il `send` metodo restituisce un oggetto JavaScript `Promise` .</span><span class="sxs-lookup"><span data-stu-id="637f0-152">The `send` method returns a JavaScript `Promise`.</span></span> <span data-ttu-id="637f0-153">Il `Promise` viene risolto quando il messaggio è stato inviato al server.</span><span class="sxs-lookup"><span data-stu-id="637f0-153">The `Promise` is resolved when the message has been sent to the server.</span></span> <span data-ttu-id="637f0-154">Se si verifica un errore durante l'invio del messaggio, `Promise` viene rifiutato con il messaggio di errore.</span><span class="sxs-lookup"><span data-stu-id="637f0-154">If there is an error sending the message, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="637f0-155">Usare `async` e `await` o i `Promise` `then` metodi e `catch` per gestire questi casi.</span><span class="sxs-lookup"><span data-stu-id="637f0-155">Use `async` and `await` or the `Promise`'s `then` and `catch` methods to handle these cases.</span></span>

> [!NOTE]
> <span data-ttu-id="637f0-156">L'utilizzo di `send` non attende fino a quando il server non ha ricevuto il messaggio.</span><span class="sxs-lookup"><span data-stu-id="637f0-156">Using `send` doesn't wait until the server has received the message.</span></span> <span data-ttu-id="637f0-157">Di conseguenza, non è possibile restituire dati o errori dal server.</span><span class="sxs-lookup"><span data-stu-id="637f0-157">Consequently, it's not possible to return data or errors from the server.</span></span>

## <a name="call-client-methods-from-the-hub"></a><span data-ttu-id="637f0-158">Chiamare i metodi client dall'hub</span><span class="sxs-lookup"><span data-stu-id="637f0-158">Call client methods from the hub</span></span>

<span data-ttu-id="637f0-159">Per ricevere messaggi dall'hub, definire un metodo usando il metodo [on](/javascript/api/%40microsoft/signalr/hubconnection#on-string---args--any-------void-) di `HubConnection` .</span><span class="sxs-lookup"><span data-stu-id="637f0-159">To receive messages from the hub, define a method using the [on](/javascript/api/%40microsoft/signalr/hubconnection#on-string---args--any-------void-) method of the `HubConnection`.</span></span>

* <span data-ttu-id="637f0-160">Nome del metodo client JavaScript.</span><span class="sxs-lookup"><span data-stu-id="637f0-160">The name of the JavaScript client method.</span></span>
* <span data-ttu-id="637f0-161">Argomenti passati dall'hub al metodo.</span><span class="sxs-lookup"><span data-stu-id="637f0-161">Arguments the hub passes to the method.</span></span>

<span data-ttu-id="637f0-162">Nell'esempio seguente il nome del metodo è `ReceiveMessage` .</span><span class="sxs-lookup"><span data-stu-id="637f0-162">In the following example, the method name is `ReceiveMessage`.</span></span> <span data-ttu-id="637f0-163">I nomi degli argomenti sono `user` e `message` :</span><span class="sxs-lookup"><span data-stu-id="637f0-163">The argument names are `user` and `message`:</span></span>

[!code-javascript[](javascript-client/samples/3.x/SignalRChat/wwwroot/chat.js?name=snippet_ReceiveMessage)]

<span data-ttu-id="637f0-164">Il codice precedente in `connection.on` viene eseguito quando il codice lato server lo chiama usando il <xref:Microsoft.AspNetCore.SignalR.ClientProxyExtensions.SendAsync%2A> Metodo:</span><span class="sxs-lookup"><span data-stu-id="637f0-164">The preceding code in `connection.on` runs when server-side code calls it using the <xref:Microsoft.AspNetCore.SignalR.ClientProxyExtensions.SendAsync%2A> method:</span></span>

[!code-csharp[Call client-side](javascript-client/samples/3.x/SignalRChat/Hubs/ChatHub.cs?name=snippet_SendMessage)]

<span data-ttu-id="637f0-165">SignalR determina il metodo client da chiamare mediante la corrispondenza tra il nome del metodo e gli argomenti definiti in `SendAsync` e `connection.on` .</span><span class="sxs-lookup"><span data-stu-id="637f0-165">SignalR determines which client method to call by matching the method name and arguments defined in `SendAsync` and `connection.on`.</span></span>

> [!NOTE]
> <span data-ttu-id="637f0-166">Come procedura consigliata, chiamare il metodo [Start](/javascript/api/%40aspnet/signalr/hubconnection#start) sul valore `HubConnection` after `on` .</span><span class="sxs-lookup"><span data-stu-id="637f0-166">As a best practice, call the [start](/javascript/api/%40aspnet/signalr/hubconnection#start) method on the `HubConnection` after `on`.</span></span> <span data-ttu-id="637f0-167">In questo modo si garantisce che i gestori vengano registrati prima della ricezione dei messaggi.</span><span class="sxs-lookup"><span data-stu-id="637f0-167">Doing so ensures your handlers are registered before any messages are received.</span></span>

## <a name="error-handling-and-logging"></a><span data-ttu-id="637f0-168">Registrazione e gestione degli errori</span><span class="sxs-lookup"><span data-stu-id="637f0-168">Error handling and logging</span></span>

<span data-ttu-id="637f0-169">Usare `try` e `catch` con `async` e `await` o il `Promise` `catch` metodo per gestire gli errori sul lato client.</span><span class="sxs-lookup"><span data-stu-id="637f0-169">Use `try` and `catch` with `async` and `await` or the `Promise`'s `catch` method to handle client-side errors.</span></span> <span data-ttu-id="637f0-170">Usare `console.error` per restituire gli errori alla console del browser:</span><span class="sxs-lookup"><span data-stu-id="637f0-170">Use `console.error` to output errors to the browser's console:</span></span>

[!code-javascript[](javascript-client/samples/3.x/SignalRChat/wwwroot/chat.js?name=snippet_Invoke&highlight=1,3-5)]

<span data-ttu-id="637f0-171">Configurare la traccia del log sul lato client passando un logger e un tipo di evento da registrare quando viene stabilita la connessione.</span><span class="sxs-lookup"><span data-stu-id="637f0-171">Set up client-side log tracing by passing a logger and type of event to log when the connection is made.</span></span> <span data-ttu-id="637f0-172">I messaggi vengono registrati con il livello di registrazione specificato e un valore superiore.</span><span class="sxs-lookup"><span data-stu-id="637f0-172">Messages are logged with the specified log level and higher.</span></span> <span data-ttu-id="637f0-173">I livelli di log disponibili sono i seguenti:</span><span class="sxs-lookup"><span data-stu-id="637f0-173">Available log levels are as follows:</span></span>

* <span data-ttu-id="637f0-174">`signalR.LogLevel.Error`: Messaggi di errore.</span><span class="sxs-lookup"><span data-stu-id="637f0-174">`signalR.LogLevel.Error`: Error messages.</span></span> <span data-ttu-id="637f0-175">Registra `Error` solo i messaggi.</span><span class="sxs-lookup"><span data-stu-id="637f0-175">Logs `Error` messages only.</span></span>
* <span data-ttu-id="637f0-176">`signalR.LogLevel.Warning`: Messaggi di avviso relativi a potenziali errori.</span><span class="sxs-lookup"><span data-stu-id="637f0-176">`signalR.LogLevel.Warning`: Warning messages about potential errors.</span></span> <span data-ttu-id="637f0-177">Log `Warning` e `Error` messaggi.</span><span class="sxs-lookup"><span data-stu-id="637f0-177">Logs `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="637f0-178">`signalR.LogLevel.Information`: Messaggi di stato senza errori.</span><span class="sxs-lookup"><span data-stu-id="637f0-178">`signalR.LogLevel.Information`: Status messages without errors.</span></span> <span data-ttu-id="637f0-179">Registra `Information` `Warning` `Error` i messaggi, e.</span><span class="sxs-lookup"><span data-stu-id="637f0-179">Logs `Information`, `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="637f0-180">`signalR.LogLevel.Trace`: Messaggi di traccia.</span><span class="sxs-lookup"><span data-stu-id="637f0-180">`signalR.LogLevel.Trace`: Trace messages.</span></span> <span data-ttu-id="637f0-181">Registra tutti gli elementi, inclusi i dati trasportati tra l'hub e il client.</span><span class="sxs-lookup"><span data-stu-id="637f0-181">Logs everything, including data transported between hub and client.</span></span>

<span data-ttu-id="637f0-182">Usare il metodo [configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) in [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) per configurare il livello di registrazione.</span><span class="sxs-lookup"><span data-stu-id="637f0-182">Use the [configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) method on [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) to configure the log level.</span></span> <span data-ttu-id="637f0-183">I messaggi vengono registrati nella console del browser:</span><span class="sxs-lookup"><span data-stu-id="637f0-183">Messages are logged to the browser console:</span></span>

[!code-javascript[](javascript-client/samples/3.x/SignalRChat/wwwroot/chat.js?name=snippet_Connection&highlight=3)]

## <a name="reconnect-clients"></a><span data-ttu-id="637f0-184">Riconnettere i client</span><span class="sxs-lookup"><span data-stu-id="637f0-184">Reconnect clients</span></span>

### <a name="automatically-reconnect"></a><span data-ttu-id="637f0-185">Riconnetti automaticamente</span><span class="sxs-lookup"><span data-stu-id="637f0-185">Automatically reconnect</span></span>

<span data-ttu-id="637f0-186">Il client JavaScript per SignalR può essere configurato in modo da riconnettersi automaticamente usando il `withAutomaticReconnect` metodo in [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="637f0-186">The JavaScript client for SignalR can be configured to automatically reconnect using the `withAutomaticReconnect` method on [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder).</span></span> <span data-ttu-id="637f0-187">Per impostazione predefinita, non verrà riconnessa automaticamente.</span><span class="sxs-lookup"><span data-stu-id="637f0-187">It won't automatically reconnect by default.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withAutomaticReconnect()
    .build();
```

<span data-ttu-id="637f0-188">Senza parametri, `withAutomaticReconnect()` Configura il client in modo che attenda rispettivamente 0, 2, 10 e 30 secondi prima di provare ogni tentativo di riconnessione, arrestandosi dopo quattro tentativi non riusciti.</span><span class="sxs-lookup"><span data-stu-id="637f0-188">Without any parameters, `withAutomaticReconnect()` configures the client to wait 0, 2, 10, and 30 seconds respectively before trying each reconnect attempt, stopping after four failed attempts.</span></span>

<span data-ttu-id="637f0-189">Prima di iniziare tutti i tentativi di riconnessione, il `HubConnection` passerà allo stato e genererà i `HubConnectionState.Reconnecting` `onreconnecting` callback anziché eseguire la transizione allo `Disconnected` stato e attivare i callback, ad `onclose` esempio `HubConnection` senza riconnessione automatica configurata.</span><span class="sxs-lookup"><span data-stu-id="637f0-189">Before starting any reconnect attempts, the `HubConnection` will transition to the `HubConnectionState.Reconnecting` state and fire its `onreconnecting` callbacks instead of transitioning to the `Disconnected` state and triggering its `onclose` callbacks like a `HubConnection` without automatic reconnect configured.</span></span> <span data-ttu-id="637f0-190">Questo consente di avvisare gli utenti che la connessione è stata persa e di disabilitare gli elementi dell'interfaccia utente.</span><span class="sxs-lookup"><span data-stu-id="637f0-190">This provides an opportunity to warn users that the connection has been lost and to disable UI elements.</span></span>

```javascript
connection.onreconnecting(error => {
    console.assert(connection.state === signalR.HubConnectionState.Reconnecting);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection lost due to error "${error}". Reconnecting.`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="637f0-191">Se il client si riconnette correttamente entro i primi quattro tentativi, il `HubConnection` eseguirà la transizione allo `Connected` stato e attiverà i `onreconnected` callback.</span><span class="sxs-lookup"><span data-stu-id="637f0-191">If the client successfully reconnects within its first four attempts, the `HubConnection` will transition back to the `Connected` state and fire its `onreconnected` callbacks.</span></span> <span data-ttu-id="637f0-192">Questo consente di informare gli utenti che è stata ristabilita la connessione.</span><span class="sxs-lookup"><span data-stu-id="637f0-192">This provides an opportunity to inform users the connection has been reestablished.</span></span>

<span data-ttu-id="637f0-193">Poiché la connessione ha un aspetto completamente nuovo per il server, `connectionId` al callback verrà fornito un nuovo `onreconnected` .</span><span class="sxs-lookup"><span data-stu-id="637f0-193">Since the connection looks entirely new to the server, a new `connectionId` will be provided to the `onreconnected` callback.</span></span>

> [!WARNING]
> <span data-ttu-id="637f0-194">Il `onreconnected` parametro del callback non sarà `connectionId` definito se `HubConnection` è stato configurato per [ignorare la negoziazione](xref:signalr/configuration#configure-client-options).</span><span class="sxs-lookup"><span data-stu-id="637f0-194">The `onreconnected` callback's `connectionId` parameter will be undefined if the `HubConnection` was configured to [skip negotiation](xref:signalr/configuration#configure-client-options).</span></span>

```javascript
connection.onreconnected(connectionId => {
    console.assert(connection.state === signalR.HubConnectionState.Connected);

    document.getElementById("messageInput").disabled = false;

    const li = document.createElement("li");
    li.textContent = `Connection reestablished. Connected with connectionId "${connectionId}".`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="637f0-195">`withAutomaticReconnect()` non configurerà `HubConnection` per ritentare gli errori iniziali iniziali, quindi gli errori di avvio devono essere gestiti manualmente:</span><span class="sxs-lookup"><span data-stu-id="637f0-195">`withAutomaticReconnect()` won't configure the `HubConnection` to retry initial start failures, so start failures need to be handled manually:</span></span>

```javascript
async function start() {
    try {
        await connection.start();
        console.assert(connection.state === signalR.HubConnectionState.Connected);
        console.log("SignalR Connected.");
    } catch (err) {
        console.assert(connection.state === signalR.HubConnectionState.Disconnected);
        console.log(err);
        setTimeout(() => start(), 5000);
    }
};
```

<span data-ttu-id="637f0-196">Se il client non si riconnette correttamente entro i primi quattro tentativi, il `HubConnection` passerà allo `Disconnected` stato e attiverà i callback [OnClose](/javascript/api/%40aspnet/signalr/hubconnection#onclose) .</span><span class="sxs-lookup"><span data-stu-id="637f0-196">If the client doesn't successfully reconnect within its first four attempts, the `HubConnection` will transition to the `Disconnected` state and fire its [onclose](/javascript/api/%40aspnet/signalr/hubconnection#onclose) callbacks.</span></span> <span data-ttu-id="637f0-197">Questo consente di informare gli utenti che la connessione è stata persa definitivamente e si consiglia di aggiornare la pagina:</span><span class="sxs-lookup"><span data-stu-id="637f0-197">This provides an opportunity to inform users the connection has been permanently lost and recommend refreshing the page:</span></span>

```javascript
connection.onclose(error => {
    console.assert(connection.state === signalR.HubConnectionState.Disconnected);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection closed due to error "${error}". Try refreshing this page to restart the connection.`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="637f0-198">Per configurare un numero personalizzato di tentativi di riconnessione prima di disconnettere o modificare la temporizzazione di riconnessione, `withAutomaticReconnect` accetta una matrice di numeri che rappresenta il ritardo in millisecondi di attesa prima di avviare ogni tentativo di riconnessione.</span><span class="sxs-lookup"><span data-stu-id="637f0-198">In order to configure a custom number of reconnect attempts before disconnecting or change the reconnect timing, `withAutomaticReconnect` accepts an array of numbers representing the delay in milliseconds to wait before starting each reconnect attempt.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withAutomaticReconnect([0, 0, 10000])
    .build();

    // .withAutomaticReconnect([0, 2000, 10000, 30000]) yields the default behavior
```

<span data-ttu-id="637f0-199">Nell'esempio precedente `HubConnection` viene configurato per avviare il tentativo di riconnessione immediatamente dopo la perdita della connessione.</span><span class="sxs-lookup"><span data-stu-id="637f0-199">The preceding example configures the `HubConnection` to start attempting reconnects immediately after the connection is lost.</span></span> <span data-ttu-id="637f0-200">Questo vale anche per la configurazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="637f0-200">This is also true for the default configuration.</span></span>

<span data-ttu-id="637f0-201">Se il primo tentativo di riconnessione ha esito negativo, anche il secondo tentativo di riconnessione verrà avviato immediatamente anziché attendere 2 secondi come nella configurazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="637f0-201">If the first reconnect attempt fails, the second reconnect attempt will also start immediately instead of waiting 2 seconds like it would in the default configuration.</span></span>

<span data-ttu-id="637f0-202">Se il secondo tentativo di riconnessione ha esito negativo, il terzo tentativo di riconnessione si avvierà entro 10 secondi, che è simile alla configurazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="637f0-202">If the second reconnect attempt fails, the third reconnect attempt will start in 10 seconds which is again like the default configuration.</span></span>

<span data-ttu-id="637f0-203">Il comportamento personalizzato, quindi, differisce di nuovo dal comportamento predefinito arrestando il terzo tentativo di riconnessione anziché tentare un ulteriore tentativo di riconnessione in altri 30 secondi, come nella configurazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="637f0-203">The custom behavior then diverges again from the default behavior by stopping after the third reconnect attempt failure instead of trying one more reconnect attempt in another 30 seconds like it would in the default configuration.</span></span>

<span data-ttu-id="637f0-204">Se si desidera un maggiore controllo sull'intervallo e sul numero di tentativi di riconnessione automatici, `withAutomaticReconnect` accetta un oggetto che implementa l' `IRetryPolicy` interfaccia, che dispone di un singolo metodo denominato `nextRetryDelayInMilliseconds` .</span><span class="sxs-lookup"><span data-stu-id="637f0-204">If you want even more control over the timing and number of automatic reconnect attempts, `withAutomaticReconnect` accepts an object implementing the `IRetryPolicy` interface, which has a single method named `nextRetryDelayInMilliseconds`.</span></span>

<span data-ttu-id="637f0-205">`nextRetryDelayInMilliseconds` accetta un solo argomento con il tipo `RetryContext` .</span><span class="sxs-lookup"><span data-stu-id="637f0-205">`nextRetryDelayInMilliseconds` takes a single argument with the type `RetryContext`.</span></span> <span data-ttu-id="637f0-206">`RetryContext`Dispone di tre proprietà: `previousRetryCount` , `elapsedMilliseconds` e `retryReason` che sono rispettivamente un oggetto `number` `number` e `Error` un oggetto.</span><span class="sxs-lookup"><span data-stu-id="637f0-206">The `RetryContext` has three properties: `previousRetryCount`, `elapsedMilliseconds` and `retryReason` which are a `number`, a `number` and an `Error` respectively.</span></span> <span data-ttu-id="637f0-207">Prima del primo tentativo di riconnessione, sia che `previousRetryCount` `elapsedMilliseconds` saranno zero e sarà `retryReason` l'errore che ha causato la perdita della connessione.</span><span class="sxs-lookup"><span data-stu-id="637f0-207">Before the first reconnect attempt, both `previousRetryCount` and `elapsedMilliseconds` will be zero, and the `retryReason` will be the Error that caused the connection to be lost.</span></span> <span data-ttu-id="637f0-208">Dopo ogni tentativo non riuscito, verrà `previousRetryCount` incrementato di uno, `elapsedMilliseconds` verrà aggiornato in modo da riflettere la quantità di tempo impiegato per la riconnessione fino a questo punto, in millisecondi, e `retryReason` sarà l'errore che ha causato l'esito negativo dell'ultimo tentativo di riconnessione.</span><span class="sxs-lookup"><span data-stu-id="637f0-208">After each failed retry attempt, `previousRetryCount` will be incremented by one, `elapsedMilliseconds` will be updated to reflect the amount of time spent reconnecting so far in milliseconds, and the `retryReason` will be the Error that caused the last reconnect attempt to fail.</span></span>

<span data-ttu-id="637f0-209">`nextRetryDelayInMilliseconds` deve restituire un numero che rappresenta il numero di millisecondi di attesa prima del successivo tentativo di riconnessione o `null` se l'oggetto `HubConnection` deve arrestare la riconnessione.</span><span class="sxs-lookup"><span data-stu-id="637f0-209">`nextRetryDelayInMilliseconds` must return either a number representing the number of milliseconds to wait before the next reconnect attempt or `null` if the `HubConnection` should stop reconnecting.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
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

<span data-ttu-id="637f0-210">In alternativa, è possibile scrivere codice per riconnettere manualmente il client, come illustrato in [riconnessione manuale](#manually-reconnect).</span><span class="sxs-lookup"><span data-stu-id="637f0-210">Alternatively, you can write code that will reconnect your client manually as demonstrated in [Manually reconnect](#manually-reconnect).</span></span>

### <a name="manually-reconnect"></a><span data-ttu-id="637f0-211">Riconnessione manuale</span><span class="sxs-lookup"><span data-stu-id="637f0-211">Manually reconnect</span></span>

<span data-ttu-id="637f0-212">Il codice seguente illustra un tipico approccio di riconnessione manuale:</span><span class="sxs-lookup"><span data-stu-id="637f0-212">The following code demonstrates a typical manual reconnection approach:</span></span>

1. <span data-ttu-id="637f0-213">Una funzione (in questo caso, la `start` funzione) viene creata per avviare la connessione.</span><span class="sxs-lookup"><span data-stu-id="637f0-213">A function (in this case, the `start` function) is created to start the connection.</span></span>
1. <span data-ttu-id="637f0-214">Chiamare la `start` funzione nel `onclose` gestore eventi della connessione.</span><span class="sxs-lookup"><span data-stu-id="637f0-214">Call the `start` function in the connection's `onclose` event handler.</span></span>

[!code-javascript[](javascript-client/samples/3.x/SignalRChat/wwwroot/chat.js?range=30-40)]

<span data-ttu-id="637f0-215">Un'implementazione reale utilizzerebbe un backup esponenziale o ritenterà un numero di volte specificato prima di rinunciare.</span><span class="sxs-lookup"><span data-stu-id="637f0-215">A real-world implementation would use an exponential back-off or retry a specified number of times before giving up.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="637f0-216">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="637f0-216">Additional resources</span></span>

* [<span data-ttu-id="637f0-217">Informazioni di riferimento sulle API JavaScript</span><span class="sxs-lookup"><span data-stu-id="637f0-217">JavaScript API reference</span></span>](/javascript/api/?view=signalr-js-latest&preserve-view=true )
* [<span data-ttu-id="637f0-218">Esercitazione su JavaScript</span><span class="sxs-lookup"><span data-stu-id="637f0-218">JavaScript tutorial</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="637f0-219">Esercitazione su Webpack e TypeScript</span><span class="sxs-lookup"><span data-stu-id="637f0-219">WebPack and TypeScript tutorial</span></span>](xref:tutorials/signalr-typescript-webpack)
* [<span data-ttu-id="637f0-220">Hub</span><span class="sxs-lookup"><span data-stu-id="637f0-220">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="637f0-221">Client .NET</span><span class="sxs-lookup"><span data-stu-id="637f0-221">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="637f0-222">Pubblicazione in Azure</span><span class="sxs-lookup"><span data-stu-id="637f0-222">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
* [<span data-ttu-id="637f0-223">Richieste tra le origini (CORS)</span><span class="sxs-lookup"><span data-stu-id="637f0-223">Cross-Origin Requests (CORS)</span></span>](xref:security/cors)
* [<span data-ttu-id="637f0-224">Documentazione senza server per i servizi di Azure SignalR</span><span class="sxs-lookup"><span data-stu-id="637f0-224">Azure SignalR Service serverless documentation</span></span>](/azure/azure-signalr/signalr-concept-serverless-development-config)
* [<span data-ttu-id="637f0-225">Risolvere gli errori di connessione</span><span class="sxs-lookup"><span data-stu-id="637f0-225">Troubleshoot connection errors</span></span>](xref:signalr/troubleshoot)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="637f0-226">Di [Rachel Appel](https://twitter.com/rachelappel)</span><span class="sxs-lookup"><span data-stu-id="637f0-226">By [Rachel Appel](https://twitter.com/rachelappel)</span></span>

<span data-ttu-id="637f0-227">La SignalR libreria client JavaScript ASP.NET Core consente agli sviluppatori di chiamare il codice dell'hub sul lato server.</span><span class="sxs-lookup"><span data-stu-id="637f0-227">The ASP.NET Core SignalR JavaScript client library enables developers to call server-side hub code.</span></span>

<span data-ttu-id="637f0-228">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/javascript-client/samples) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="637f0-228">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/javascript-client/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="install-the-no-locsignalr-client-package"></a><span data-ttu-id="637f0-229">Installare il SignalR pacchetto client</span><span class="sxs-lookup"><span data-stu-id="637f0-229">Install the SignalR client package</span></span>

<span data-ttu-id="637f0-230">La SignalR libreria client JavaScript viene distribuita come pacchetto [NPM](https://www.npmjs.com/) .</span><span class="sxs-lookup"><span data-stu-id="637f0-230">The SignalR JavaScript client library is delivered as an [npm](https://www.npmjs.com/) package.</span></span> <span data-ttu-id="637f0-231">Le sezioni seguenti illustrano diversi modi per installare la libreria client.</span><span class="sxs-lookup"><span data-stu-id="637f0-231">The following sections outline different ways to install the client library.</span></span>

### <a name="install-with-npm"></a><span data-ttu-id="637f0-232">Eseguire l'installazione con NPM</span><span class="sxs-lookup"><span data-stu-id="637f0-232">Install with npm</span></span>

<span data-ttu-id="637f0-233">Se si usa Visual Studio, eseguire i comandi seguenti dalla **console di gestione pacchetti** nella cartella radice.</span><span class="sxs-lookup"><span data-stu-id="637f0-233">If using Visual Studio, run the following commands from **Package Manager Console** while in the root folder.</span></span> <span data-ttu-id="637f0-234">Per Visual Studio Code, eseguire i comandi seguenti dal **terminale integrato**.</span><span class="sxs-lookup"><span data-stu-id="637f0-234">For Visual Studio Code, run the following commands from the **Integrated Terminal**.</span></span>

```bash
npm init -y
npm install @aspnet/signalr
```

<span data-ttu-id="637f0-235">NPM installa il contenuto del pacchetto nella *cartella \\ @aspnet\signalr\dist\browser node_modules* .</span><span class="sxs-lookup"><span data-stu-id="637f0-235">npm installs the package contents in the *node_modules\\@aspnet\signalr\dist\browser* folder.</span></span> <span data-ttu-id="637f0-236">Creare una nuova cartella denominata *SignalR* nella cartella *wwwroot \\ lib* .</span><span class="sxs-lookup"><span data-stu-id="637f0-236">Create a new folder named *signalr* under the *wwwroot\\lib* folder.</span></span> <span data-ttu-id="637f0-237">Copiare il file *signalr.js* nella cartella *wwwroot\lib\signalr* .</span><span class="sxs-lookup"><span data-stu-id="637f0-237">Copy the *signalr.js* file to the *wwwroot\lib\signalr* folder.</span></span>

<span data-ttu-id="637f0-238">Fare riferimento al SignalR client JavaScript nell' `<script>` elemento.</span><span class="sxs-lookup"><span data-stu-id="637f0-238">Reference the SignalR JavaScript client in the `<script>` element.</span></span> <span data-ttu-id="637f0-239">Esempio:</span><span class="sxs-lookup"><span data-stu-id="637f0-239">For example:</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
```

### <a name="use-a-content-delivery-network-cdn"></a><span data-ttu-id="637f0-240">Usare una rete per la distribuzione di contenuti (CDN)</span><span class="sxs-lookup"><span data-stu-id="637f0-240">Use a Content Delivery Network (CDN)</span></span>

<span data-ttu-id="637f0-241">Per usare la libreria client senza il prerequisito NPM, fare riferimento a una copia ospitata dalla rete CDN della libreria client.</span><span class="sxs-lookup"><span data-stu-id="637f0-241">To use the client library without the npm prerequisite, reference a CDN-hosted copy of the client library.</span></span> <span data-ttu-id="637f0-242">Esempio:</span><span class="sxs-lookup"><span data-stu-id="637f0-242">For example:</span></span>

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/microsoft-signalr/3.1.3/signalr.min.js"></script>
```

<span data-ttu-id="637f0-243">La libreria client è disponibile nei seguenti CDNs:</span><span class="sxs-lookup"><span data-stu-id="637f0-243">The client library is available on the following CDNs:</span></span>

* [<span data-ttu-id="637f0-244">cdnjs</span><span class="sxs-lookup"><span data-stu-id="637f0-244">cdnjs</span></span>](https://cdnjs.com/libraries/aspnet-signalr)
* [<span data-ttu-id="637f0-245">jsDelivr</span><span class="sxs-lookup"><span data-stu-id="637f0-245">jsDelivr</span></span>](https://www.jsdelivr.com/package/npm/@aspnet/signalr)
* [<span data-ttu-id="637f0-246">unpkg</span><span class="sxs-lookup"><span data-stu-id="637f0-246">unpkg</span></span>](https://unpkg.com/@aspnet/signalr@next/dist/browser/signalr.min.js)

### <a name="install-with-libman"></a><span data-ttu-id="637f0-247">Installare con LibMan</span><span class="sxs-lookup"><span data-stu-id="637f0-247">Install with LibMan</span></span>

<span data-ttu-id="637f0-248">[LibMan](xref:client-side/libman/index) può essere usato per installare file di libreria client specifici dalla libreria client ospitata nella rete CDN.</span><span class="sxs-lookup"><span data-stu-id="637f0-248">[LibMan](xref:client-side/libman/index) can be used to install specific client library files from the CDN-hosted client library.</span></span> <span data-ttu-id="637f0-249">Ad esempio, aggiungere solo il file JavaScript minimizzati al progetto.</span><span class="sxs-lookup"><span data-stu-id="637f0-249">For example, only add the minified JavaScript file to the project.</span></span> <span data-ttu-id="637f0-250">Per informazioni dettagliate su questo approccio, vedere [aggiungere la SignalR libreria client](xref:tutorials/signalr#add-the-signalr-client-library).</span><span class="sxs-lookup"><span data-stu-id="637f0-250">For details on that approach, see [Add the SignalR client library](xref:tutorials/signalr#add-the-signalr-client-library).</span></span>

## <a name="connect-to-a-hub"></a><span data-ttu-id="637f0-251">Connettersi a un hub</span><span class="sxs-lookup"><span data-stu-id="637f0-251">Connect to a hub</span></span>

<span data-ttu-id="637f0-252">Il codice seguente crea e avvia una connessione.</span><span class="sxs-lookup"><span data-stu-id="637f0-252">The following code creates and starts a connection.</span></span> <span data-ttu-id="637f0-253">Il nome dell'hub non fa distinzione tra maiuscole e minuscole.</span><span class="sxs-lookup"><span data-stu-id="637f0-253">The hub's name is case insensitive.</span></span>

[!code-javascript[Call hub methods](javascript-client/samples/2.x/SignalRChat/wwwroot/js/chat.js?range=9-13,28-51)]

### <a name="cross-origin-connections"></a><span data-ttu-id="637f0-254">Connessioni tra le origini</span><span class="sxs-lookup"><span data-stu-id="637f0-254">Cross-origin connections</span></span>

<span data-ttu-id="637f0-255">In genere, i browser caricano le connessioni dallo stesso dominio della pagina richiesta.</span><span class="sxs-lookup"><span data-stu-id="637f0-255">Typically, browsers load connections from the same domain as the requested page.</span></span> <span data-ttu-id="637f0-256">In alcuni casi, tuttavia, è necessaria una connessione a un altro dominio.</span><span class="sxs-lookup"><span data-stu-id="637f0-256">However, there are occasions when a connection to another domain is required.</span></span>

<span data-ttu-id="637f0-257">Per impedire a un sito dannoso di leggere dati sensibili da un altro sito, le [connessioni tra le origini](xref:security/cors) sono disabilitate per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="637f0-257">To prevent a malicious site from reading sensitive data from another site, [cross-origin connections](xref:security/cors) are disabled by default.</span></span> <span data-ttu-id="637f0-258">Per consentire una richiesta tra origini, abilitarla nella `Startup` classe.</span><span class="sxs-lookup"><span data-stu-id="637f0-258">To allow a cross-origin request, enable it in the `Startup` class.</span></span>

[!code-csharp[Cross-origin connections](javascript-client/samples/2.x/SignalRChat/Startup.cs?highlight=29-35,56)]

## <a name="call-hub-methods-from-client"></a><span data-ttu-id="637f0-259">Chiamare i metodi dell'hub dal client</span><span class="sxs-lookup"><span data-stu-id="637f0-259">Call hub methods from client</span></span>

<span data-ttu-id="637f0-260">I client JavaScript chiamano metodi pubblici sugli hub tramite il metodo [Invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke) di [HubConnection](/javascript/api/%40aspnet/signalr/hubconnection).</span><span class="sxs-lookup"><span data-stu-id="637f0-260">JavaScript clients call public methods on hubs via the [invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke) method of the [HubConnection](/javascript/api/%40aspnet/signalr/hubconnection).</span></span> <span data-ttu-id="637f0-261">Il `invoke` metodo accetta due argomenti:</span><span class="sxs-lookup"><span data-stu-id="637f0-261">The `invoke` method accepts two arguments:</span></span>

* <span data-ttu-id="637f0-262">Nome del metodo dell'hub.</span><span class="sxs-lookup"><span data-stu-id="637f0-262">The name of the hub method.</span></span> <span data-ttu-id="637f0-263">Nell'esempio seguente il nome del metodo nell'hub è `SendMessage` .</span><span class="sxs-lookup"><span data-stu-id="637f0-263">In the following example, the method name on the hub is `SendMessage`.</span></span>
* <span data-ttu-id="637f0-264">Qualsiasi argomento definito nel metodo dell'hub.</span><span class="sxs-lookup"><span data-stu-id="637f0-264">Any arguments defined in the hub method.</span></span> <span data-ttu-id="637f0-265">Nell'esempio seguente il nome dell'argomento è `message` .</span><span class="sxs-lookup"><span data-stu-id="637f0-265">In the following example, the argument name is `message`.</span></span> <span data-ttu-id="637f0-266">Il codice di esempio usa la sintassi della funzione freccia supportata nelle versioni correnti di tutti i browser principali, ad eccezione di Internet Explorer.</span><span class="sxs-lookup"><span data-stu-id="637f0-266">The example code uses arrow function syntax that is supported in current versions of all major browsers except Internet Explorer.</span></span>

  [!code-javascript[Call hub methods](javascript-client/samples/2.x/SignalRChat/wwwroot/js/chat.js?range=24)]

> [!NOTE]
> <span data-ttu-id="637f0-267">La chiamata di metodi dell'hub da un client è supportata solo quando si usa il SignalR servizio di Azure in modalità *predefinita* .</span><span class="sxs-lookup"><span data-stu-id="637f0-267">Calling hub methods from a client is only supported when using the Azure SignalR Service in *Default* mode.</span></span> <span data-ttu-id="637f0-268">Per altre informazioni, vedere [domande frequenti (repository GitHub di Azure-SignalR)](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose).</span><span class="sxs-lookup"><span data-stu-id="637f0-268">For more information, see [Frequently Asked Questions (azure-signalr GitHub repository)](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose).</span></span>

<span data-ttu-id="637f0-269">Il `invoke` metodo restituisce una [promessa](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)JavaScript.</span><span class="sxs-lookup"><span data-stu-id="637f0-269">The `invoke` method returns a JavaScript [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise).</span></span> <span data-ttu-id="637f0-270">`Promise`Viene risolto con il valore restituito (se presente) quando il metodo nel server restituisce.</span><span class="sxs-lookup"><span data-stu-id="637f0-270">The `Promise` is resolved with the return value (if any) when the method on the server returns.</span></span> <span data-ttu-id="637f0-271">Se il metodo sul server genera un errore, `Promise` viene rifiutato con il messaggio di errore.</span><span class="sxs-lookup"><span data-stu-id="637f0-271">If the method on the server throws an error, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="637f0-272">Usare i `then` `catch` metodi e sull'oggetto `Promise` stesso per gestire questi casi (o la `await` sintassi).</span><span class="sxs-lookup"><span data-stu-id="637f0-272">Use the `then` and `catch` methods on the `Promise` itself to handle these cases (or `await` syntax).</span></span>

<span data-ttu-id="637f0-273">Il `send` metodo restituisce un oggetto JavaScript `Promise` .</span><span class="sxs-lookup"><span data-stu-id="637f0-273">The `send` method returns a JavaScript `Promise`.</span></span> <span data-ttu-id="637f0-274">Il `Promise` viene risolto quando il messaggio è stato inviato al server.</span><span class="sxs-lookup"><span data-stu-id="637f0-274">The `Promise` is resolved when the message has been sent to the server.</span></span> <span data-ttu-id="637f0-275">Se si verifica un errore durante l'invio del messaggio, `Promise` viene rifiutato con il messaggio di errore.</span><span class="sxs-lookup"><span data-stu-id="637f0-275">If there is an error sending the message, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="637f0-276">Usare i `then` `catch` metodi e sull'oggetto `Promise` stesso per gestire questi casi (o la `await` sintassi).</span><span class="sxs-lookup"><span data-stu-id="637f0-276">Use the `then` and `catch` methods on the `Promise` itself to handle these cases (or `await` syntax).</span></span>

> [!NOTE]
> <span data-ttu-id="637f0-277">L'utilizzo di `send` non attende fino a quando il server non ha ricevuto il messaggio.</span><span class="sxs-lookup"><span data-stu-id="637f0-277">Using `send` doesn't wait until the server has received the message.</span></span> <span data-ttu-id="637f0-278">Di conseguenza, non è possibile restituire dati o errori dal server.</span><span class="sxs-lookup"><span data-stu-id="637f0-278">Consequently, it's not possible to return data or errors from the server.</span></span>

## <a name="call-client-methods-from-hub"></a><span data-ttu-id="637f0-279">Chiamare i metodi client dall'hub</span><span class="sxs-lookup"><span data-stu-id="637f0-279">Call client methods from hub</span></span>

<span data-ttu-id="637f0-280">Per ricevere messaggi dall'hub, definire un metodo usando il metodo [on](/javascript/api/%40aspnet/signalr/hubconnection#on) di `HubConnection` .</span><span class="sxs-lookup"><span data-stu-id="637f0-280">To receive messages from the hub, define a method using the [on](/javascript/api/%40aspnet/signalr/hubconnection#on) method of the `HubConnection`.</span></span>

* <span data-ttu-id="637f0-281">Nome del metodo client JavaScript.</span><span class="sxs-lookup"><span data-stu-id="637f0-281">The name of the JavaScript client method.</span></span> <span data-ttu-id="637f0-282">Nell'esempio seguente il nome del metodo è `ReceiveMessage` .</span><span class="sxs-lookup"><span data-stu-id="637f0-282">In the following example, the method name is `ReceiveMessage`.</span></span>
* <span data-ttu-id="637f0-283">Argomenti passati dall'hub al metodo.</span><span class="sxs-lookup"><span data-stu-id="637f0-283">Arguments the hub passes to the method.</span></span> <span data-ttu-id="637f0-284">Nell'esempio seguente il valore dell'argomento è `message` .</span><span class="sxs-lookup"><span data-stu-id="637f0-284">In the following example, the argument value is `message`.</span></span>

[!code-javascript[Receive calls from hub](javascript-client/samples/2.x/SignalRChat/wwwroot/js/chat.js?range=14-19)]

<span data-ttu-id="637f0-285">Il codice precedente in `connection.on` viene eseguito quando il codice lato server lo chiama usando il <xref:Microsoft.AspNetCore.SignalR.ClientProxyExtensions.SendAsync%2A> metodo.</span><span class="sxs-lookup"><span data-stu-id="637f0-285">The preceding code in `connection.on` runs when server-side code calls it using the <xref:Microsoft.AspNetCore.SignalR.ClientProxyExtensions.SendAsync%2A> method.</span></span>

[!code-csharp[Call client-side](javascript-client/samples/2.x/SignalRChat/hubs/chathub.cs?range=8-11)]

<span data-ttu-id="637f0-286">SignalR determina il metodo client da chiamare mediante la corrispondenza tra il nome del metodo e gli argomenti definiti in `SendAsync` e `connection.on` .</span><span class="sxs-lookup"><span data-stu-id="637f0-286">SignalR determines which client method to call by matching the method name and arguments defined in `SendAsync` and `connection.on`.</span></span>

> [!NOTE]
> <span data-ttu-id="637f0-287">Come procedura consigliata, chiamare il metodo [Start](/javascript/api/%40aspnet/signalr/hubconnection#start) sul valore `HubConnection` after `on` .</span><span class="sxs-lookup"><span data-stu-id="637f0-287">As a best practice, call the [start](/javascript/api/%40aspnet/signalr/hubconnection#start) method on the `HubConnection` after `on`.</span></span> <span data-ttu-id="637f0-288">In questo modo si garantisce che i gestori vengano registrati prima della ricezione dei messaggi.</span><span class="sxs-lookup"><span data-stu-id="637f0-288">Doing so ensures your handlers are registered before any messages are received.</span></span>

## <a name="error-handling-and-logging"></a><span data-ttu-id="637f0-289">Registrazione e gestione degli errori</span><span class="sxs-lookup"><span data-stu-id="637f0-289">Error handling and logging</span></span>

<span data-ttu-id="637f0-290">Concatenare un `catch` metodo alla fine del `start` metodo per gestire gli errori sul lato client.</span><span class="sxs-lookup"><span data-stu-id="637f0-290">Chain a `catch` method to the end of the `start` method to handle client-side errors.</span></span> <span data-ttu-id="637f0-291">Usare `console.error` per restituire gli errori alla console del browser.</span><span class="sxs-lookup"><span data-stu-id="637f0-291">Use `console.error` to output errors to the browser's console.</span></span>

[!code-javascript[Error handling](javascript-client/samples/2.x/SignalRChat/wwwroot/js/chat.js?range=50)]

<span data-ttu-id="637f0-292">Configurare la traccia del log sul lato client passando un logger e un tipo di evento da registrare quando viene stabilita la connessione.</span><span class="sxs-lookup"><span data-stu-id="637f0-292">Set up client-side log tracing by passing a logger and type of event to log when the connection is made.</span></span> <span data-ttu-id="637f0-293">I messaggi vengono registrati con il livello di registrazione specificato e un valore superiore.</span><span class="sxs-lookup"><span data-stu-id="637f0-293">Messages are logged with the specified log level and higher.</span></span> <span data-ttu-id="637f0-294">I livelli di log disponibili sono i seguenti:</span><span class="sxs-lookup"><span data-stu-id="637f0-294">Available log levels are as follows:</span></span>

* <span data-ttu-id="637f0-295">`signalR.LogLevel.Error`: Messaggi di errore.</span><span class="sxs-lookup"><span data-stu-id="637f0-295">`signalR.LogLevel.Error`: Error messages.</span></span> <span data-ttu-id="637f0-296">Registra `Error` solo i messaggi.</span><span class="sxs-lookup"><span data-stu-id="637f0-296">Logs `Error` messages only.</span></span>
* <span data-ttu-id="637f0-297">`signalR.LogLevel.Warning`: Messaggi di avviso relativi a potenziali errori.</span><span class="sxs-lookup"><span data-stu-id="637f0-297">`signalR.LogLevel.Warning`: Warning messages about potential errors.</span></span> <span data-ttu-id="637f0-298">Log `Warning` e `Error` messaggi.</span><span class="sxs-lookup"><span data-stu-id="637f0-298">Logs `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="637f0-299">`signalR.LogLevel.Information`: Messaggi di stato senza errori.</span><span class="sxs-lookup"><span data-stu-id="637f0-299">`signalR.LogLevel.Information`: Status messages without errors.</span></span> <span data-ttu-id="637f0-300">Registra `Information` `Warning` `Error` i messaggi, e.</span><span class="sxs-lookup"><span data-stu-id="637f0-300">Logs `Information`, `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="637f0-301">`signalR.LogLevel.Trace`: Messaggi di traccia.</span><span class="sxs-lookup"><span data-stu-id="637f0-301">`signalR.LogLevel.Trace`: Trace messages.</span></span> <span data-ttu-id="637f0-302">Registra tutti gli elementi, inclusi i dati trasportati tra l'hub e il client.</span><span class="sxs-lookup"><span data-stu-id="637f0-302">Logs everything, including data transported between hub and client.</span></span>

<span data-ttu-id="637f0-303">Usare il metodo [configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) in [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) per configurare il livello di registrazione.</span><span class="sxs-lookup"><span data-stu-id="637f0-303">Use the [configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) method on [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) to configure the log level.</span></span> <span data-ttu-id="637f0-304">I messaggi vengono registrati nella console del browser.</span><span class="sxs-lookup"><span data-stu-id="637f0-304">Messages are logged to the browser console.</span></span>

[!code-javascript[Logging levels](javascript-client/samples/2.x/SignalRChat/wwwroot/js/chat.js?range=9-12)]

## <a name="reconnect-clients"></a><span data-ttu-id="637f0-305">Riconnettere i client</span><span class="sxs-lookup"><span data-stu-id="637f0-305">Reconnect clients</span></span>

### <a name="manually-reconnect"></a><span data-ttu-id="637f0-306">Riconnessione manuale</span><span class="sxs-lookup"><span data-stu-id="637f0-306">Manually reconnect</span></span>

> [!WARNING]
> <span data-ttu-id="637f0-307">Prima del 3,0, il client JavaScript per SignalR non si riconnette automaticamente.</span><span class="sxs-lookup"><span data-stu-id="637f0-307">Prior to 3.0, the JavaScript client for SignalR doesn't automatically reconnect.</span></span> <span data-ttu-id="637f0-308">È necessario scrivere codice che riconnetta il client manualmente.</span><span class="sxs-lookup"><span data-stu-id="637f0-308">You must write code that will reconnect your client manually.</span></span>

<span data-ttu-id="637f0-309">Il codice seguente illustra un tipico approccio di riconnessione manuale:</span><span class="sxs-lookup"><span data-stu-id="637f0-309">The following code demonstrates a typical manual reconnection approach:</span></span>

1. <span data-ttu-id="637f0-310">Una funzione (in questo caso, la `start` funzione) viene creata per avviare la connessione.</span><span class="sxs-lookup"><span data-stu-id="637f0-310">A function (in this case, the `start` function) is created to start the connection.</span></span>
1. <span data-ttu-id="637f0-311">Chiamare la `start` funzione nel `onclose` gestore eventi della connessione.</span><span class="sxs-lookup"><span data-stu-id="637f0-311">Call the `start` function in the connection's `onclose` event handler.</span></span>

[!code-javascript[Reconnect the JavaScript client](javascript-client/samples/2.x/SignalRChat/wwwroot/js/chat.js?range=28-40)]

<span data-ttu-id="637f0-312">Un'implementazione reale utilizzerebbe un backup esponenziale o ritenterà un numero di volte specificato prima di rinunciare.</span><span class="sxs-lookup"><span data-stu-id="637f0-312">A real-world implementation would use an exponential back-off or retry a specified number of times before giving up.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="637f0-313">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="637f0-313">Additional resources</span></span>

* [<span data-ttu-id="637f0-314">Informazioni di riferimento sulle API JavaScript</span><span class="sxs-lookup"><span data-stu-id="637f0-314">JavaScript API reference</span></span>](/javascript/api/?view=signalr-js-latest)
* [<span data-ttu-id="637f0-315">Esercitazione su JavaScript</span><span class="sxs-lookup"><span data-stu-id="637f0-315">JavaScript tutorial</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="637f0-316">Esercitazione su Webpack e TypeScript</span><span class="sxs-lookup"><span data-stu-id="637f0-316">WebPack and TypeScript tutorial</span></span>](xref:tutorials/signalr-typescript-webpack)
* [<span data-ttu-id="637f0-317">Hub</span><span class="sxs-lookup"><span data-stu-id="637f0-317">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="637f0-318">Client .NET</span><span class="sxs-lookup"><span data-stu-id="637f0-318">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="637f0-319">Pubblicazione in Azure</span><span class="sxs-lookup"><span data-stu-id="637f0-319">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
* [<span data-ttu-id="637f0-320">Richieste tra le origini (CORS)</span><span class="sxs-lookup"><span data-stu-id="637f0-320">Cross-Origin Requests (CORS)</span></span>](xref:security/cors)
* [<span data-ttu-id="637f0-321">Documentazione senza server per i servizi di Azure SignalR</span><span class="sxs-lookup"><span data-stu-id="637f0-321">Azure SignalR Service serverless documentation</span></span>](/azure/azure-signalr/signalr-concept-serverless-development-config)

::: moniker-end
