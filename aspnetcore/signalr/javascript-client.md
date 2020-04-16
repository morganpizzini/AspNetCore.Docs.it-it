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
# <a name="aspnet-core-opno-locsignalr-javascript-client"></a><span data-ttu-id="80f7b-103">ASP.NET SignalR client JavaScript principale</span><span class="sxs-lookup"><span data-stu-id="80f7b-103">ASP.NET Core SignalR JavaScript client</span></span>

<span data-ttu-id="80f7b-104">Di [Rachel Appel](https://twitter.com/rachelappel)</span><span class="sxs-lookup"><span data-stu-id="80f7b-104">By [Rachel Appel](https://twitter.com/rachelappel)</span></span>

<span data-ttu-id="80f7b-105">La libreria SignalR client JavaScript ASP.NET Core consente agli sviluppatori di chiamare il codice dell'hub sul lato server.</span><span class="sxs-lookup"><span data-stu-id="80f7b-105">The ASP.NET Core SignalR JavaScript client library enables developers to call server-side hub code.</span></span>

<span data-ttu-id="80f7b-106">[Visualizzare o scaricare codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/javascript-client/sample) ( come[scaricare](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="80f7b-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/javascript-client/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="install-the-opno-locsignalr-client-package"></a><span data-ttu-id="80f7b-107">Installare SignalR il pacchetto client</span><span class="sxs-lookup"><span data-stu-id="80f7b-107">Install the SignalR client package</span></span>

<span data-ttu-id="80f7b-108">La SignalR libreria client JavaScript viene fornita come pacchetto [npm.](https://www.npmjs.com/)</span><span class="sxs-lookup"><span data-stu-id="80f7b-108">The SignalR JavaScript client library is delivered as an [npm](https://www.npmjs.com/) package.</span></span> <span data-ttu-id="80f7b-109">Nelle sezioni seguenti vengono descritti diversi modi per installare la libreria client.</span><span class="sxs-lookup"><span data-stu-id="80f7b-109">The following sections outline different ways to install the client library.</span></span>

### <a name="install-with-npm"></a><span data-ttu-id="80f7b-110">Installazione con npm</span><span class="sxs-lookup"><span data-stu-id="80f7b-110">Install with npm</span></span>

<span data-ttu-id="80f7b-111">Se si usa Visual Studio, eseguire i comandi seguenti dalla console di **Gestione pacchetti** nella cartella radice.</span><span class="sxs-lookup"><span data-stu-id="80f7b-111">If using Visual Studio, run the following commands from **Package Manager Console** while in the root folder.</span></span> <span data-ttu-id="80f7b-112">Per Visual Studio Code, eseguire i comandi seguenti dal **terminale integrato**.</span><span class="sxs-lookup"><span data-stu-id="80f7b-112">For Visual Studio Code, run the following commands from the **Integrated Terminal**.</span></span>

::: moniker range=">= aspnetcore-3.0"

```bash
npm init -y
npm install @microsoft/signalr
```

<span data-ttu-id="80f7b-113">npm installa il contenuto del pacchetto nella cartella \*node_modules.\\ \*</span><span class="sxs-lookup"><span data-stu-id="80f7b-113">npm installs the package contents in the *node_modules\\@microsoft\signalr\dist\browser* folder.</span></span> <span data-ttu-id="80f7b-114">Creare una nuova cartella denominata *signalr* nella cartella *wwwroot\\lib.*</span><span class="sxs-lookup"><span data-stu-id="80f7b-114">Create a new folder named *signalr* under the *wwwroot\\lib* folder.</span></span> <span data-ttu-id="80f7b-115">Copiare il file *signalr.js* nella cartella *wwwroot.*</span><span class="sxs-lookup"><span data-stu-id="80f7b-115">Copy the *signalr.js* file to the *wwwroot\lib\signalr* folder.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```bash
npm init -y
npm install @aspnet/signalr
```

<span data-ttu-id="80f7b-116">npm installa il contenuto del pacchetto nella cartella \*node_modules.\\ \*</span><span class="sxs-lookup"><span data-stu-id="80f7b-116">npm installs the package contents in the *node_modules\\@aspnet\signalr\dist\browser* folder.</span></span> <span data-ttu-id="80f7b-117">Creare una nuova cartella denominata *signalr* nella cartella *wwwroot\\lib.*</span><span class="sxs-lookup"><span data-stu-id="80f7b-117">Create a new folder named *signalr* under the *wwwroot\\lib* folder.</span></span> <span data-ttu-id="80f7b-118">Copiare il file *signalr.js* nella cartella *wwwroot.*</span><span class="sxs-lookup"><span data-stu-id="80f7b-118">Copy the *signalr.js* file to the *wwwroot\lib\signalr* folder.</span></span>

::: moniker-end

<span data-ttu-id="80f7b-119">Fare SignalR riferimento al client `<script>` JavaScript nell'elemento.</span><span class="sxs-lookup"><span data-stu-id="80f7b-119">Reference the SignalR JavaScript client in the `<script>` element.</span></span> <span data-ttu-id="80f7b-120">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="80f7b-120">For example:</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
```

### <a name="use-a-content-delivery-network-cdn"></a><span data-ttu-id="80f7b-121">Usare una rete per la distribuzione di contenuti (CDN)Use a Content Delivery Network (CDN)</span><span class="sxs-lookup"><span data-stu-id="80f7b-121">Use a Content Delivery Network (CDN)</span></span>

<span data-ttu-id="80f7b-122">Per utilizzare la libreria client senza il prerequisito npm, fare riferimento a una copia ospitata con CDN della libreria client.</span><span class="sxs-lookup"><span data-stu-id="80f7b-122">To use the client library without the npm prerequisite, reference a CDN-hosted copy of the client library.</span></span> <span data-ttu-id="80f7b-123">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="80f7b-123">For example:</span></span>

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/microsoft-signalr/3.1.3/signalr.min.js"></script>
```

<span data-ttu-id="80f7b-124">La libreria client è disponibile nelle seguenti reti CDN:</span><span class="sxs-lookup"><span data-stu-id="80f7b-124">The client library is available on the following CDNs:</span></span>

::: moniker range=">= aspnetcore-3.0"

* [<span data-ttu-id="80f7b-125">cdnjs (file cdnjs)</span><span class="sxs-lookup"><span data-stu-id="80f7b-125">cdnjs</span></span>](https://cdnjs.com/libraries/microsoft-signalr)
* [<span data-ttu-id="80f7b-126">il comando (informazioni in stato di sorsia)</span><span class="sxs-lookup"><span data-stu-id="80f7b-126">jsDelivr</span></span>](https://www.jsdelivr.com/package/npm/@microsoft/signalr)
* [<span data-ttu-id="80f7b-127">unpkg</span><span class="sxs-lookup"><span data-stu-id="80f7b-127">unpkg</span></span>](https://unpkg.com/@microsoft/signalr@next/dist/browser/signalr.min.js)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* [<span data-ttu-id="80f7b-128">cdnjs (file cdnjs)</span><span class="sxs-lookup"><span data-stu-id="80f7b-128">cdnjs</span></span>](https://cdnjs.com/libraries/aspnet-signalr)
* [<span data-ttu-id="80f7b-129">il comando (informazioni in stato di sorsia)</span><span class="sxs-lookup"><span data-stu-id="80f7b-129">jsDelivr</span></span>](https://www.jsdelivr.com/package/npm/@aspnet/signalr)
* [<span data-ttu-id="80f7b-130">unpkg</span><span class="sxs-lookup"><span data-stu-id="80f7b-130">unpkg</span></span>](https://unpkg.com/@aspnet/signalr@next/dist/browser/signalr.min.js)

::: moniker-end

### <a name="install-with-libman"></a><span data-ttu-id="80f7b-131">Installa con LibMan</span><span class="sxs-lookup"><span data-stu-id="80f7b-131">Install with LibMan</span></span>

<span data-ttu-id="80f7b-132">[LibMan](xref:client-side/libman/index) può essere utilizzato per installare file di libreria client specifici dalla libreria client ospitata da CDN.</span><span class="sxs-lookup"><span data-stu-id="80f7b-132">[LibMan](xref:client-side/libman/index) can be used to install specific client library files from the CDN-hosted client library.</span></span> <span data-ttu-id="80f7b-133">Ad esempio, aggiungere solo il file JavaScript minified al progetto.</span><span class="sxs-lookup"><span data-stu-id="80f7b-133">For example, only add the minified JavaScript file to the project.</span></span> <span data-ttu-id="80f7b-134">Per informazioni dettagliate su tale approccio, vedere [Aggiungere la SignalR libreria client](xref:tutorials/signalr#add-the-signalr-client-library).</span><span class="sxs-lookup"><span data-stu-id="80f7b-134">For details on that approach, see [Add the SignalR client library](xref:tutorials/signalr#add-the-signalr-client-library).</span></span>

## <a name="connect-to-a-hub"></a><span data-ttu-id="80f7b-135">Connettersi a un hub</span><span class="sxs-lookup"><span data-stu-id="80f7b-135">Connect to a hub</span></span>

<span data-ttu-id="80f7b-136">Il codice seguente crea e avvia una connessione.</span><span class="sxs-lookup"><span data-stu-id="80f7b-136">The following code creates and starts a connection.</span></span> <span data-ttu-id="80f7b-137">Il nome dell'hub non fa distinzione tra maiuscole e minuscole.</span><span class="sxs-lookup"><span data-stu-id="80f7b-137">The hub's name is case insensitive.</span></span>

[!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=9-13,43-45)]

### <a name="cross-origin-connections"></a><span data-ttu-id="80f7b-138">Connessioni tra origini</span><span class="sxs-lookup"><span data-stu-id="80f7b-138">Cross-origin connections</span></span>

<span data-ttu-id="80f7b-139">In genere, i browser caricano le connessioni dallo stesso dominio della pagina richiesta.</span><span class="sxs-lookup"><span data-stu-id="80f7b-139">Typically, browsers load connections from the same domain as the requested page.</span></span> <span data-ttu-id="80f7b-140">Tuttavia, ci sono occasioni in cui è necessaria una connessione a un altro dominio.</span><span class="sxs-lookup"><span data-stu-id="80f7b-140">However, there are occasions when a connection to another domain is required.</span></span>

<span data-ttu-id="80f7b-141">Per impedire a un sito dannoso di leggere dati sensibili da un altro sito, le [connessioni tra origini](xref:security/cors) sono disabilitate per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="80f7b-141">To prevent a malicious site from reading sensitive data from another site, [cross-origin connections](xref:security/cors) are disabled by default.</span></span> <span data-ttu-id="80f7b-142">Per consentire una richiesta tra origini, `Startup` abilitarla nella classe .</span><span class="sxs-lookup"><span data-stu-id="80f7b-142">To allow a cross-origin request, enable it in the `Startup` class.</span></span>

[!code-csharp[Cross-origin connections](javascript-client/sample/Startup.cs?highlight=29-35,56)]

## <a name="call-hub-methods-from-client"></a><span data-ttu-id="80f7b-143">Chiamare i metodi dell'hub dal clientCall hub methods from client</span><span class="sxs-lookup"><span data-stu-id="80f7b-143">Call hub methods from client</span></span>

<span data-ttu-id="80f7b-144">I client JavaScript chiamano metodi pubblici sugli hub tramite il metodo [invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke) di [HubConnection](/javascript/api/%40aspnet/signalr/hubconnection).</span><span class="sxs-lookup"><span data-stu-id="80f7b-144">JavaScript clients call public methods on hubs via the [invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke) method of the [HubConnection](/javascript/api/%40aspnet/signalr/hubconnection).</span></span> <span data-ttu-id="80f7b-145">Il `invoke` metodo accetta due argomenti:</span><span class="sxs-lookup"><span data-stu-id="80f7b-145">The `invoke` method accepts two arguments:</span></span>

* <span data-ttu-id="80f7b-146">Nome del metodo dell'hub.</span><span class="sxs-lookup"><span data-stu-id="80f7b-146">The name of the hub method.</span></span> <span data-ttu-id="80f7b-147">Nell'esempio seguente, il nome del `SendMessage`metodo nell'hub è .</span><span class="sxs-lookup"><span data-stu-id="80f7b-147">In the following example, the method name on the hub is `SendMessage`.</span></span>
* <span data-ttu-id="80f7b-148">Qualsiasi argomento definito nel metodo dell'hub.</span><span class="sxs-lookup"><span data-stu-id="80f7b-148">Any arguments defined in the hub method.</span></span> <span data-ttu-id="80f7b-149">Nell'esempio seguente il nome `message`dell'argomento è .</span><span class="sxs-lookup"><span data-stu-id="80f7b-149">In the following example, the argument name is `message`.</span></span> <span data-ttu-id="80f7b-150">Il codice di esempio utilizza la sintassi della funzione freccia supportata nelle versioni correnti di tutti i browser principali, ad eccezione di Internet Explorer.</span><span class="sxs-lookup"><span data-stu-id="80f7b-150">The example code uses arrow function syntax that is supported in current versions of all major browsers except Internet Explorer.</span></span>

  [!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=24)]

> [!NOTE]
> <span data-ttu-id="80f7b-151">Se si usa SignalR Servizio di Azure in *modalità senza server,* non è possibile chiamare i metodi hub da un client.</span><span class="sxs-lookup"><span data-stu-id="80f7b-151">If you're using Azure SignalR Service in *Serverless mode*, you cannot call hub methods from a client.</span></span> <span data-ttu-id="80f7b-152">Per ulteriori informazioni, vedere la [ SignalR documentazione del servizio](/azure/azure-signalr/signalr-concept-serverless-development-config).</span><span class="sxs-lookup"><span data-stu-id="80f7b-152">For more information, see the [SignalR Service documentation](/azure/azure-signalr/signalr-concept-serverless-development-config).</span></span>

<span data-ttu-id="80f7b-153">Il `invoke` metodo restituisce una [promessa](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)JavaScript .</span><span class="sxs-lookup"><span data-stu-id="80f7b-153">The `invoke` method returns a JavaScript [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise).</span></span> <span data-ttu-id="80f7b-154">L'oggetto `Promise` viene risolto con il valore restituito (se presente) quando il metodo sul server restituisce .</span><span class="sxs-lookup"><span data-stu-id="80f7b-154">The `Promise` is resolved with the return value (if any) when the method on the server returns.</span></span> <span data-ttu-id="80f7b-155">Se il metodo sul server genera `Promise` un errore, viene rifiutato con il messaggio di errore.</span><span class="sxs-lookup"><span data-stu-id="80f7b-155">If the method on the server throws an error, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="80f7b-156">Utilizzare `then` i `catch` metodi `Promise` e su se stesso `await` per gestire questi casi (o sintassi).</span><span class="sxs-lookup"><span data-stu-id="80f7b-156">Use the `then` and `catch` methods on the `Promise` itself to handle these cases (or `await` syntax).</span></span>

<span data-ttu-id="80f7b-157">Il `send` metodo restituisce `Promise`un oggetto JavaScript .</span><span class="sxs-lookup"><span data-stu-id="80f7b-157">The `send` method returns a JavaScript `Promise`.</span></span> <span data-ttu-id="80f7b-158">L'operazione `Promise` viene risolta quando il messaggio è stato inviato al server.</span><span class="sxs-lookup"><span data-stu-id="80f7b-158">The `Promise` is resolved when the message has been sent to the server.</span></span> <span data-ttu-id="80f7b-159">Se si verifica un errore `Promise` durante l'invio del messaggio, l'oggetto viene rifiutato con il messaggio di errore.</span><span class="sxs-lookup"><span data-stu-id="80f7b-159">If there is an error sending the message, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="80f7b-160">Utilizzare `then` i `catch` metodi `Promise` e su se stesso `await` per gestire questi casi (o sintassi).</span><span class="sxs-lookup"><span data-stu-id="80f7b-160">Use the `then` and `catch` methods on the `Promise` itself to handle these cases (or `await` syntax).</span></span>

> [!NOTE]
> <span data-ttu-id="80f7b-161">L'utilizzo `send` non attende che il server abbia ricevuto il messaggio.</span><span class="sxs-lookup"><span data-stu-id="80f7b-161">Using `send` doesn't wait until the server has received the message.</span></span> <span data-ttu-id="80f7b-162">Di conseguenza, non è possibile restituire dati o errori dal server.</span><span class="sxs-lookup"><span data-stu-id="80f7b-162">Consequently, it's not possible to return data or errors from the server.</span></span>

## <a name="call-client-methods-from-hub"></a><span data-ttu-id="80f7b-163">Chiamare i metodi client dall'hubCall client methods from hub</span><span class="sxs-lookup"><span data-stu-id="80f7b-163">Call client methods from hub</span></span>

<span data-ttu-id="80f7b-164">Per ricevere messaggi dall'hub, definire [on](/javascript/api/%40aspnet/signalr/hubconnection#on) un metodo `HubConnection`utilizzando il metodo on dell'oggetto .</span><span class="sxs-lookup"><span data-stu-id="80f7b-164">To receive messages from the hub, define a method using the [on](/javascript/api/%40aspnet/signalr/hubconnection#on) method of the `HubConnection`.</span></span>

* <span data-ttu-id="80f7b-165">Nome del metodo client JavaScript.</span><span class="sxs-lookup"><span data-stu-id="80f7b-165">The name of the JavaScript client method.</span></span> <span data-ttu-id="80f7b-166">Nell'esempio seguente, il `ReceiveMessage`nome del metodo è .</span><span class="sxs-lookup"><span data-stu-id="80f7b-166">In the following example, the method name is `ReceiveMessage`.</span></span>
* <span data-ttu-id="80f7b-167">Argomenti passati dall'hub al metodo.</span><span class="sxs-lookup"><span data-stu-id="80f7b-167">Arguments the hub passes to the method.</span></span> <span data-ttu-id="80f7b-168">Nell'esempio seguente il valore `message`dell'argomento è .</span><span class="sxs-lookup"><span data-stu-id="80f7b-168">In the following example, the argument value is `message`.</span></span>

[!code-javascript[Receive calls from hub](javascript-client/sample/wwwroot/js/chat.js?range=14-19)]

<span data-ttu-id="80f7b-169">Il codice precedente `connection.on` in viene eseguito quando il codice lato server lo chiama utilizzando il [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) metodo.</span><span class="sxs-lookup"><span data-stu-id="80f7b-169">The preceding code in `connection.on` runs when server-side code calls it using the [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) method.</span></span>

[!code-csharp[Call client-side](javascript-client/sample/hubs/chathub.cs?range=8-11)]

SignalR<span data-ttu-id="80f7b-170">determina il metodo client da chiamare facendo corrispondere `SendAsync` `connection.on`il nome del metodo e gli argomenti definiti in e .</span><span class="sxs-lookup"><span data-stu-id="80f7b-170"> determines which client method to call by matching the method name and arguments defined in `SendAsync` and `connection.on`.</span></span>

> [!NOTE]
> <span data-ttu-id="80f7b-171">Come procedura consigliata, chiamare il `HubConnection` `on`metodo [start](/javascript/api/%40aspnet/signalr/hubconnection#start) su after .</span><span class="sxs-lookup"><span data-stu-id="80f7b-171">As a best practice, call the [start](/javascript/api/%40aspnet/signalr/hubconnection#start) method on the `HubConnection` after `on`.</span></span> <span data-ttu-id="80f7b-172">In questo modo si garantisce che i gestori vengano registrati prima della ricezione dei messaggi.</span><span class="sxs-lookup"><span data-stu-id="80f7b-172">Doing so ensures your handlers are registered before any messages are received.</span></span>

## <a name="error-handling-and-logging"></a><span data-ttu-id="80f7b-173">Registrazione e gestione degli errori</span><span class="sxs-lookup"><span data-stu-id="80f7b-173">Error handling and logging</span></span>

<span data-ttu-id="80f7b-174">Concatenare un `catch` metodo `start` alla fine del metodo per gestire gli errori sul lato client.</span><span class="sxs-lookup"><span data-stu-id="80f7b-174">Chain a `catch` method to the end of the `start` method to handle client-side errors.</span></span> <span data-ttu-id="80f7b-175">Consente `console.error` di generare errori nella console del browser.</span><span class="sxs-lookup"><span data-stu-id="80f7b-175">Use `console.error` to output errors to the browser's console.</span></span>

[!code-javascript[Error handling](javascript-client/sample/wwwroot/js/chat.js?range=50)]

<span data-ttu-id="80f7b-176">Impostare la traccia del log sul lato client passando un logger e il tipo di evento da registrare quando viene stabilita la connessione.</span><span class="sxs-lookup"><span data-stu-id="80f7b-176">Set up client-side log tracing by passing a logger and type of event to log when the connection is made.</span></span> <span data-ttu-id="80f7b-177">I messaggi vengono registrati con il livello di log specificato e superiore.</span><span class="sxs-lookup"><span data-stu-id="80f7b-177">Messages are logged with the specified log level and higher.</span></span> <span data-ttu-id="80f7b-178">I livelli di registrazione disponibili sono i seguenti:</span><span class="sxs-lookup"><span data-stu-id="80f7b-178">Available log levels are as follows:</span></span>

* <span data-ttu-id="80f7b-179">`signalR.LogLevel.Error`&ndash; Messaggi di errore.</span><span class="sxs-lookup"><span data-stu-id="80f7b-179">`signalR.LogLevel.Error` &ndash; Error messages.</span></span> <span data-ttu-id="80f7b-180">Registra `Error` solo i messaggi.</span><span class="sxs-lookup"><span data-stu-id="80f7b-180">Logs `Error` messages only.</span></span>
* <span data-ttu-id="80f7b-181">`signalR.LogLevel.Warning`&ndash; Messaggi di avviso relativi a potenziali errori.</span><span class="sxs-lookup"><span data-stu-id="80f7b-181">`signalR.LogLevel.Warning` &ndash; Warning messages about potential errors.</span></span> <span data-ttu-id="80f7b-182">Registri `Warning`e `Error` messaggi.</span><span class="sxs-lookup"><span data-stu-id="80f7b-182">Logs `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="80f7b-183">`signalR.LogLevel.Information`&ndash; Messaggi di stato senza errori.</span><span class="sxs-lookup"><span data-stu-id="80f7b-183">`signalR.LogLevel.Information` &ndash; Status messages without errors.</span></span> <span data-ttu-id="80f7b-184">Registri `Information` `Warning`, `Error` e messaggi.</span><span class="sxs-lookup"><span data-stu-id="80f7b-184">Logs `Information`, `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="80f7b-185">`signalR.LogLevel.Trace`&ndash; Messaggi di traccia.</span><span class="sxs-lookup"><span data-stu-id="80f7b-185">`signalR.LogLevel.Trace` &ndash; Trace messages.</span></span> <span data-ttu-id="80f7b-186">Registra tutto, inclusi i dati trasportati tra hub e client.</span><span class="sxs-lookup"><span data-stu-id="80f7b-186">Logs everything, including data transported between hub and client.</span></span>

<span data-ttu-id="80f7b-187">Utilizzare il metodo [configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) in [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) per configurare il livello di registrazione.</span><span class="sxs-lookup"><span data-stu-id="80f7b-187">Use the [configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) method on [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) to configure the log level.</span></span> <span data-ttu-id="80f7b-188">I messaggi vengono registrati nella console del browser.</span><span class="sxs-lookup"><span data-stu-id="80f7b-188">Messages are logged to the browser console.</span></span>

[!code-javascript[Logging levels](javascript-client/sample/wwwroot/js/chat.js?range=9-12)]

## <a name="reconnect-clients"></a><span data-ttu-id="80f7b-189">Riconnettere i client</span><span class="sxs-lookup"><span data-stu-id="80f7b-189">Reconnect clients</span></span>

::: moniker range=">= aspnetcore-3.0"

### <a name="automatically-reconnect"></a><span data-ttu-id="80f7b-190">Riconnessione automatica</span><span class="sxs-lookup"><span data-stu-id="80f7b-190">Automatically reconnect</span></span>

<span data-ttu-id="80f7b-191">Il client JavaScript per SignalR può essere configurato `withAutomaticReconnect` per la riconnessione automatica utilizzando il metodo su [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="80f7b-191">The JavaScript client for SignalR can be configured to automatically reconnect using the `withAutomaticReconnect` method on [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder).</span></span> <span data-ttu-id="80f7b-192">Non si riconnette automaticamente per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="80f7b-192">It won't automatically reconnect by default.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect()
    .build();
```

<span data-ttu-id="80f7b-193">Senza parametri, `withAutomaticReconnect()` configura il client in modo che attenda 0, 2, 10 e 30 secondi rispettivamente prima di tentare ogni tentativo di riconnessione, interrompendosi dopo quattro tentativi non riusciti.</span><span class="sxs-lookup"><span data-stu-id="80f7b-193">Without any parameters, `withAutomaticReconnect()` configures the client to wait 0, 2, 10, and 30 seconds respectively before trying each reconnect attempt, stopping after four failed attempts.</span></span>

<span data-ttu-id="80f7b-194">Prima di avviare qualsiasi `HubConnection` tentativo di `HubConnectionState.Reconnecting` riconnessione, `onreconnecting` il passerà allo `Disconnected` stato e genera `onclose` i callback `HubConnection` anziché passare allo stato e attivare i callback come un senza riconnessione automatica configurata.</span><span class="sxs-lookup"><span data-stu-id="80f7b-194">Before starting any reconnect attempts, the `HubConnection` will transition to the `HubConnectionState.Reconnecting` state and fire its `onreconnecting` callbacks instead of transitioning to the `Disconnected` state and triggering its `onclose` callbacks like a `HubConnection` without automatic reconnect configured.</span></span> <span data-ttu-id="80f7b-195">In questo modo è possibile avvisare gli utenti che la connessione è stata persa e disabilitare gli elementi dell'interfaccia utente.</span><span class="sxs-lookup"><span data-stu-id="80f7b-195">This provides an opportunity to warn users that the connection has been lost and to disable UI elements.</span></span>

```javascript
connection.onreconnecting(error => {
    console.assert(connection.state === signalR.HubConnectionState.Reconnecting);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection lost due to error "${error}". Reconnecting.`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="80f7b-196">Se il client si riconnette correttamente `HubConnection` entro i primi `Connected` quattro tentativi, il passerà allo stato e genera i callback. `onreconnected`</span><span class="sxs-lookup"><span data-stu-id="80f7b-196">If the client successfully reconnects within its first four attempts, the `HubConnection` will transition back to the `Connected` state and fire its `onreconnected` callbacks.</span></span> <span data-ttu-id="80f7b-197">Ciò offre l'opportunità di informare gli utenti che la connessione è stata ristabilita.</span><span class="sxs-lookup"><span data-stu-id="80f7b-197">This provides an opportunity to inform users the connection has been reestablished.</span></span>

<span data-ttu-id="80f7b-198">Poiché la connessione sembra completamente nuova `connectionId` al server, `onreconnected` verrà fornito un nuovo al callback.</span><span class="sxs-lookup"><span data-stu-id="80f7b-198">Since the connection looks entirely new to the server, a new `connectionId` will be provided to the `onreconnected` callback.</span></span>

> [!WARNING]
> <span data-ttu-id="80f7b-199">Il `onreconnected` parametro `connectionId` del callback non `HubConnection` sarà definito se l'oggetto è stato configurato per ignorare la [negoziazione.](xref:signalr/configuration#configure-client-options)</span><span class="sxs-lookup"><span data-stu-id="80f7b-199">The `onreconnected` callback's `connectionId` parameter will be undefined if the `HubConnection` was configured to [skip negotiation](xref:signalr/configuration#configure-client-options).</span></span>

```javascript
connection.onreconnected(connectionId => {
    console.assert(connection.state === signalR.HubConnectionState.Connected);

    document.getElementById("messageInput").disabled = false;

    const li = document.createElement("li");
    li.textContent = `Connection reestablished. Connected with connectionId "${connectionId}".`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="80f7b-200">`withAutomaticReconnect()`non configurerà `HubConnection` il per ritentare gli errori di avvio iniziale, pertanto gli errori di avvio devono essere gestiti manualmente:</span><span class="sxs-lookup"><span data-stu-id="80f7b-200">`withAutomaticReconnect()` won't configure the `HubConnection` to retry initial start failures, so start failures need to be handled manually:</span></span>

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

<span data-ttu-id="80f7b-201">Se il client non si riconnette correttamente `HubConnection` entro i `Disconnected` primi quattro tentativi, la transizione allo stato e genera i callback [onclose.](/javascript/api/%40aspnet/signalr/hubconnection#onclose)</span><span class="sxs-lookup"><span data-stu-id="80f7b-201">If the client doesn't successfully reconnect within its first four attempts, the `HubConnection` will transition to the `Disconnected` state and fire its [onclose](/javascript/api/%40aspnet/signalr/hubconnection#onclose) callbacks.</span></span> <span data-ttu-id="80f7b-202">Ciò offre l'opportunità di informare gli utenti che la connessione è stata persa definitivamente e consiglia di aggiornare la pagina:</span><span class="sxs-lookup"><span data-stu-id="80f7b-202">This provides an opportunity to inform users the connection has been permanently lost and recommend refreshing the page:</span></span>

```javascript
connection.onclose(error => {
    console.assert(connection.state === signalR.HubConnectionState.Disconnected);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection closed due to error "${error}". Try refreshing this page to restart the connection.`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="80f7b-203">Per configurare un numero personalizzato di tentativi di riconnessione `withAutomaticReconnect` prima di disconnettersi o modificare l'intervallo di riconnessione, accetta una matrice di numeri che rappresentano il ritardo in millisecondi di attesa prima di avviare ogni tentativo di riconnessione.</span><span class="sxs-lookup"><span data-stu-id="80f7b-203">In order to configure a custom number of reconnect attempts before disconnecting or change the reconnect timing, `withAutomaticReconnect` accepts an array of numbers representing the delay in milliseconds to wait before starting each reconnect attempt.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect([0, 0, 10000])
    .build();

    // .withAutomaticReconnect([0, 2000, 10000, 30000]) yields the default behavior
```

<span data-ttu-id="80f7b-204">Nell'esempio precedente viene `HubConnection` configurato l'oggetto per avviare il tentativo di riconnessione immediatamente dopo la perdita della connessione.</span><span class="sxs-lookup"><span data-stu-id="80f7b-204">The preceding example configures the `HubConnection` to start attempting reconnects immediately after the connection is lost.</span></span> <span data-ttu-id="80f7b-205">Questo vale anche per la configurazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="80f7b-205">This is also true for the default configuration.</span></span>

<span data-ttu-id="80f7b-206">Se il primo tentativo di riconnessione non riesce, anche il secondo tentativo di riconnessione verrà avviato immediatamente invece di attendere 2 secondi come nella configurazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="80f7b-206">If the first reconnect attempt fails, the second reconnect attempt will also start immediately instead of waiting 2 seconds like it would in the default configuration.</span></span>

<span data-ttu-id="80f7b-207">Se il secondo tentativo di riconnessione non riesce, il terzo tentativo di riconnessione verrà avviato in 10 secondi, che è di nuovo simile alla configurazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="80f7b-207">If the second reconnect attempt fails, the third reconnect attempt will start in 10 seconds which is again like the default configuration.</span></span>

<span data-ttu-id="80f7b-208">Il comportamento personalizzato si discosta quindi nuovamente dal comportamento predefinito arrestandosi dopo il terzo tentativo di riconnessione anziché tentare un altro tentativo di riconnessione in altri 30 secondi come nella configurazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="80f7b-208">The custom behavior then diverges again from the default behavior by stopping after the third reconnect attempt failure instead of trying one more reconnect attempt in another 30 seconds like it would in the default configuration.</span></span>

<span data-ttu-id="80f7b-209">Se si desidera un maggiore controllo sulla temporizzazione `withAutomaticReconnect` e sul numero `IRetryPolicy` di tentativi di `nextRetryDelayInMilliseconds`riconnessione automatica, accetta un oggetto che implementa l'interfaccia, che dispone di un unico metodo denominato .</span><span class="sxs-lookup"><span data-stu-id="80f7b-209">If you want even more control over the timing and number of automatic reconnect attempts, `withAutomaticReconnect` accepts an object implementing the `IRetryPolicy` interface, which has a single method named `nextRetryDelayInMilliseconds`.</span></span>

<span data-ttu-id="80f7b-210">`nextRetryDelayInMilliseconds`accetta un singolo argomento `RetryContext`con il tipo .</span><span class="sxs-lookup"><span data-stu-id="80f7b-210">`nextRetryDelayInMilliseconds` takes a single argument with the type `RetryContext`.</span></span> <span data-ttu-id="80f7b-211">Il `RetryContext` ha tre `previousRetryCount` `elapsedMilliseconds` proprietà: `retryReason` , `number`e `number` che `Error` sono rispettivamente a , a e una .</span><span class="sxs-lookup"><span data-stu-id="80f7b-211">The `RetryContext` has three properties: `previousRetryCount`, `elapsedMilliseconds` and `retryReason` which are a `number`, a `number` and an `Error` respectively.</span></span> <span data-ttu-id="80f7b-212">Prima del primo tentativo `previousRetryCount` di `elapsedMilliseconds` riconnessione, entrambi `retryReason` e saranno zero, e il sarà l'errore che ha causato la perdita della connessione.</span><span class="sxs-lookup"><span data-stu-id="80f7b-212">Before the first reconnect attempt, both `previousRetryCount` and `elapsedMilliseconds` will be zero, and the `retryReason` will be the Error that caused the connection to be lost.</span></span> <span data-ttu-id="80f7b-213">Dopo ogni tentativo non `previousRetryCount` riuscito, verrà incrementato di uno, `elapsedMilliseconds` verrà aggiornato per riflettere la quantità `retryReason` di tempo impiegato per la riconnessione finora in millisecondi e l'errore che ha causato l'errore dell'ultimo tentativo di riconnessione non riesce.</span><span class="sxs-lookup"><span data-stu-id="80f7b-213">After each failed retry attempt, `previousRetryCount` will be incremented by one, `elapsedMilliseconds` will be updated to reflect the amount of time spent reconnecting so far in milliseconds, and the `retryReason` will be the Error that caused the last reconnect attempt to fail.</span></span>

<span data-ttu-id="80f7b-214">`nextRetryDelayInMilliseconds`deve restituire un numero che rappresenta il numero di millisecondi `null` di `HubConnection` attesa prima del successivo tentativo di riconnessione o se l'operazione deve interrompere la riconnessione.</span><span class="sxs-lookup"><span data-stu-id="80f7b-214">`nextRetryDelayInMilliseconds` must return either a number representing the number of milliseconds to wait before the next reconnect attempt or `null` if the `HubConnection` should stop reconnecting.</span></span>

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

<span data-ttu-id="80f7b-215">In alternativa, è possibile scrivere codice che riconnetterà manualmente il client come illustrato in [Riconnessione manuale](#manually-reconnect).</span><span class="sxs-lookup"><span data-stu-id="80f7b-215">Alternatively, you can write code that will reconnect your client manually as demonstrated in [Manually reconnect](#manually-reconnect).</span></span>

::: moniker-end

### <a name="manually-reconnect"></a><span data-ttu-id="80f7b-216">Riconnessione manuale</span><span class="sxs-lookup"><span data-stu-id="80f7b-216">Manually reconnect</span></span>

::: moniker range="< aspnetcore-3.0"

> [!WARNING]
> <span data-ttu-id="80f7b-217">Prima della 3.0, il SignalR client JavaScript per non si riconnette automaticamente.</span><span class="sxs-lookup"><span data-stu-id="80f7b-217">Prior to 3.0, the JavaScript client for SignalR doesn't automatically reconnect.</span></span> <span data-ttu-id="80f7b-218">È necessario scrivere codice che riconnetterà il client manualmente.</span><span class="sxs-lookup"><span data-stu-id="80f7b-218">You must write code that will reconnect your client manually.</span></span>

::: moniker-end

<span data-ttu-id="80f7b-219">Il codice seguente illustra un tipico approccio di riconnessione manuale:The following code demonstrates a typical manual reconnection approach:</span><span class="sxs-lookup"><span data-stu-id="80f7b-219">The following code demonstrates a typical manual reconnection approach:</span></span>

1. <span data-ttu-id="80f7b-220">Viene creata una funzione `start` (in questo caso, la funzione) per avviare la connessione.</span><span class="sxs-lookup"><span data-stu-id="80f7b-220">A function (in this case, the `start` function) is created to start the connection.</span></span>
1. <span data-ttu-id="80f7b-221">Chiamare `start` la funzione nel `onclose` gestore eventi della connessione.</span><span class="sxs-lookup"><span data-stu-id="80f7b-221">Call the `start` function in the connection's `onclose` event handler.</span></span>

[!code-javascript[Reconnect the JavaScript client](javascript-client/sample/wwwroot/js/chat.js?range=28-40)]

<span data-ttu-id="80f7b-222">Un'implementazione reale userebbe un back-off esponenziale o ritenterebbe un numero specificato di volte prima di rinunciare.</span><span class="sxs-lookup"><span data-stu-id="80f7b-222">A real-world implementation would use an exponential back-off or retry a specified number of times before giving up.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="80f7b-223">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="80f7b-223">Additional resources</span></span>

* [<span data-ttu-id="80f7b-224">Informazioni di riferimento sull'API JavaScript</span><span class="sxs-lookup"><span data-stu-id="80f7b-224">JavaScript API reference</span></span>](/javascript/api/?view=signalr-js-latest)
* [<span data-ttu-id="80f7b-225">Esercitazione su JavaScript</span><span class="sxs-lookup"><span data-stu-id="80f7b-225">JavaScript tutorial</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="80f7b-226">Esercitazione su WebPack e TypeScript</span><span class="sxs-lookup"><span data-stu-id="80f7b-226">WebPack and TypeScript tutorial</span></span>](xref:tutorials/signalr-typescript-webpack)
* [<span data-ttu-id="80f7b-227">Hub</span><span class="sxs-lookup"><span data-stu-id="80f7b-227">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="80f7b-228">Client .NET</span><span class="sxs-lookup"><span data-stu-id="80f7b-228">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="80f7b-229">Pubblicazione in Azure</span><span class="sxs-lookup"><span data-stu-id="80f7b-229">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
* [<span data-ttu-id="80f7b-230">Richieste tra origini (CORS)</span><span class="sxs-lookup"><span data-stu-id="80f7b-230">Cross-Origin Requests (CORS)</span></span>](xref:security/cors)
* <span data-ttu-id="80f7b-231">[Documentazione senza server di Servizio di AzureAzure SignalR Service serverless documentation](/azure/azure-signalr/signalr-concept-serverless-development-config)</span><span class="sxs-lookup"><span data-stu-id="80f7b-231">[Azure SignalR Service serverless documentation](/azure/azure-signalr/signalr-concept-serverless-development-config)</span></span>
