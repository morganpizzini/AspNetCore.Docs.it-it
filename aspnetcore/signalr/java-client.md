---
title: Client SignalR Java ASP.NET Core
author: mikaelm12
description: Informazioni su come usare il client SignalR Java di ASP.NET Core.
monikerRange: '>= aspnetcore-2.2'
ms.author: mimengis
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/java-client
ms.openlocfilehash: 33c1e3b9b2b8990c811f3b49a978cbc630294c81
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82777254"
---
# <a name="aspnet-core-signalr-java-client"></a><span data-ttu-id="4ceef-103">Client SignalR Java ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4ceef-103">ASP.NET Core SignalR Java client</span></span>

<span data-ttu-id="4ceef-104">Di [Mikael Mengistu](https://twitter.com/MikaelM_12)</span><span class="sxs-lookup"><span data-stu-id="4ceef-104">By [Mikael Mengistu](https://twitter.com/MikaelM_12)</span></span>

<span data-ttu-id="4ceef-105">Il client Java consente la connessione a un SignalR server di ASP.NET Core dal codice Java, incluse le app Android.</span><span class="sxs-lookup"><span data-stu-id="4ceef-105">The Java client enables connecting to an ASP.NET Core SignalR server from Java code, including Android apps.</span></span> <span data-ttu-id="4ceef-106">Come il client [JavaScript](xref:signalr/javascript-client) e il [client .NET](xref:signalr/dotnet-client), il client Java consente di ricevere e inviare messaggi a un hub in tempo reale.</span><span class="sxs-lookup"><span data-stu-id="4ceef-106">Like the [JavaScript client](xref:signalr/javascript-client) and the [.NET client](xref:signalr/dotnet-client), the Java client enables you to receive and send messages to a hub in real time.</span></span> <span data-ttu-id="4ceef-107">Il client Java è disponibile in ASP.NET Core 2,2 e versioni successive.</span><span class="sxs-lookup"><span data-stu-id="4ceef-107">The Java client is available in ASP.NET Core 2.2 and later.</span></span>

<span data-ttu-id="4ceef-108">L'app console Java di esempio a cui si fa riferimento in SignalR questo articolo usa il client Java.</span><span class="sxs-lookup"><span data-stu-id="4ceef-108">The sample Java console app referenced in this article uses the SignalR Java client.</span></span>

<span data-ttu-id="4ceef-109">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/java-client/sample) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="4ceef-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/java-client/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="install-the-signalr-java-client-package"></a><span data-ttu-id="4ceef-110">Installare il SignalR pacchetto client Java</span><span class="sxs-lookup"><span data-stu-id="4ceef-110">Install the SignalR Java client package</span></span>

<span data-ttu-id="4ceef-111">Il file jar *SignalR-1.0.0* consente ai client di connettersi SignalR agli hub.</span><span class="sxs-lookup"><span data-stu-id="4ceef-111">The *signalr-1.0.0* JAR file allows clients to connect to SignalR hubs.</span></span> <span data-ttu-id="4ceef-112">Per trovare il numero di versione del file JAR più recente, vedere i [Risultati della ricerca di Maven](https://search.maven.org/search?q=g:com.microsoft.signalr%20AND%20a:signalr).</span><span class="sxs-lookup"><span data-stu-id="4ceef-112">To find the latest JAR file version number, see the [Maven search results](https://search.maven.org/search?q=g:com.microsoft.signalr%20AND%20a:signalr).</span></span>

<span data-ttu-id="4ceef-113">Se si usa Gradle, aggiungere la riga seguente alla `dependencies` sezione del file *Build. Gradle* :</span><span class="sxs-lookup"><span data-stu-id="4ceef-113">If using Gradle, add the following line to the `dependencies` section of your *build.gradle* file:</span></span>

```gradle
implementation 'com.microsoft.signalr:signalr:1.0.0'
```

<span data-ttu-id="4ceef-114">Se si usa Maven, aggiungere le righe seguenti all' `<dependencies>` interno dell'elemento del file *POM. XML* :</span><span class="sxs-lookup"><span data-stu-id="4ceef-114">If using Maven, add the following lines inside the `<dependencies>` element of your *pom.xml* file:</span></span>

[!code-xml[pom.xml dependency element](java-client/sample/pom.xml?name=snippet_dependencyElement)]

## <a name="connect-to-a-hub"></a><span data-ttu-id="4ceef-115">Connettersi a un hub</span><span class="sxs-lookup"><span data-stu-id="4ceef-115">Connect to a hub</span></span>

<span data-ttu-id="4ceef-116">Per stabilire un `HubConnection`oggetto, `HubConnectionBuilder` deve essere utilizzato.</span><span class="sxs-lookup"><span data-stu-id="4ceef-116">To establish a `HubConnection`, the `HubConnectionBuilder` should be used.</span></span> <span data-ttu-id="4ceef-117">È possibile configurare l'URL dell'hub e il livello di registrazione durante la compilazione di una connessione.</span><span class="sxs-lookup"><span data-stu-id="4ceef-117">The hub URL and log level can be configured while building a connection.</span></span> <span data-ttu-id="4ceef-118">Configurare le opzioni necessarie chiamando uno dei `HubConnectionBuilder` metodi precedenti `build`a.</span><span class="sxs-lookup"><span data-stu-id="4ceef-118">Configure any required options by calling any of the `HubConnectionBuilder` methods before `build`.</span></span> <span data-ttu-id="4ceef-119">Avviare la connessione con `start`.</span><span class="sxs-lookup"><span data-stu-id="4ceef-119">Start the connection with `start`.</span></span>

[!code-java[Build hub connection](java-client/sample/src/main/java/Chat.java?range=16-17)]

## <a name="call-hub-methods-from-client"></a><span data-ttu-id="4ceef-120">Chiamare i metodi dell'hub dal client</span><span class="sxs-lookup"><span data-stu-id="4ceef-120">Call hub methods from client</span></span>

<span data-ttu-id="4ceef-121">Una chiamata a `send` richiama un metodo dell'hub.</span><span class="sxs-lookup"><span data-stu-id="4ceef-121">A call to `send` invokes a hub method.</span></span> <span data-ttu-id="4ceef-122">Passare il nome del metodo dell'hub e gli eventuali argomenti definiti nel metodo `send`Hub a.</span><span class="sxs-lookup"><span data-stu-id="4ceef-122">Pass the hub method name and any arguments defined in the hub method to `send`.</span></span>

[!code-java[send method](java-client/sample/src/main/java/Chat.java?range=28)]

> [!NOTE]
> <span data-ttu-id="4ceef-123">Se si usa il servizio SignalR di Azure in *modalità senza server*, non è possibile chiamare i metodi dell'hub da un client.</span><span class="sxs-lookup"><span data-stu-id="4ceef-123">If you're using Azure SignalR Service in *Serverless mode*, you cannot call hub methods from a client.</span></span> <span data-ttu-id="4ceef-124">Per ulteriori informazioni, vedere la [ SignalR documentazione del servizio](/azure/azure-signalr/signalr-concept-serverless-development-config).</span><span class="sxs-lookup"><span data-stu-id="4ceef-124">For more information, see the [SignalR Service documentation](/azure/azure-signalr/signalr-concept-serverless-development-config).</span></span>

## <a name="call-client-methods-from-hub"></a><span data-ttu-id="4ceef-125">Chiamare i metodi client dall'hub</span><span class="sxs-lookup"><span data-stu-id="4ceef-125">Call client methods from hub</span></span>

<span data-ttu-id="4ceef-126">Usare `hubConnection.on` per definire i metodi sul client che l'hub può chiamare.</span><span class="sxs-lookup"><span data-stu-id="4ceef-126">Use `hubConnection.on` to define methods on the client that the hub can call.</span></span> <span data-ttu-id="4ceef-127">Definire i metodi dopo la compilazione, ma prima di avviare la connessione.</span><span class="sxs-lookup"><span data-stu-id="4ceef-127">Define the methods after building but before starting the connection.</span></span>

[!code-java[Define client methods](java-client/sample/src/main/java/Chat.java?range=19-21)]

## <a name="add-logging"></a><span data-ttu-id="4ceef-128">Aggiungere la registrazione</span><span class="sxs-lookup"><span data-stu-id="4ceef-128">Add logging</span></span>

<span data-ttu-id="4ceef-129">Il SignalR client Java usa la libreria [SLF4J](https://www.slf4j.org/) per la registrazione.</span><span class="sxs-lookup"><span data-stu-id="4ceef-129">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="4ceef-130">Si tratta di un'API di registrazione di alto livello che consente agli utenti della libreria di scegliere una propria implementazione di registrazione specifica inserendo una dipendenza di registrazione specifica.</span><span class="sxs-lookup"><span data-stu-id="4ceef-130">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="4ceef-131">Il frammento di codice seguente illustra come `java.util.logging` usare con SignalR il client Java.</span><span class="sxs-lookup"><span data-stu-id="4ceef-131">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="4ceef-132">Se non si configura la registrazione nelle dipendenze, SLF4J carica un logger senza operazioni predefinito con il seguente messaggio di avviso:</span><span class="sxs-lookup"><span data-stu-id="4ceef-132">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="4ceef-133">Questa operazione può essere ignorata in modo sicuro.</span><span class="sxs-lookup"><span data-stu-id="4ceef-133">This can safely be ignored.</span></span>

## <a name="android-development-notes"></a><span data-ttu-id="4ceef-134">Note sullo sviluppo per Android</span><span class="sxs-lookup"><span data-stu-id="4ceef-134">Android development notes</span></span>

<span data-ttu-id="4ceef-135">Per quanto riguarda Android SDK compatibilità per le SignalR funzionalità client di, considerare gli elementi seguenti quando si specifica la versione di destinazione Android SDK:</span><span class="sxs-lookup"><span data-stu-id="4ceef-135">With regards to Android SDK compatibility for the SignalR client features, consider the following items when specifying your target Android SDK version:</span></span>

* <span data-ttu-id="4ceef-136">Il SignalR client Java viene eseguito sul livello 16 dell'API Android e versioni successive.</span><span class="sxs-lookup"><span data-stu-id="4ceef-136">The SignalR Java Client will run on Android API Level 16 and later.</span></span>
* <span data-ttu-id="4ceef-137">La connessione tramite il SignalR servizio di Azure richiede il livello 20 dell'API Android e versioni successive perché il [servizio di azure SignalR ](/azure/azure-signalr/signalr-overview) richiede TLS 1,2 e non supporta i pacchetti di crittografia basati su SHA-1.</span><span class="sxs-lookup"><span data-stu-id="4ceef-137">Connecting through the Azure SignalR Service will require Android API Level 20 and later because the [Azure SignalR Service](/azure/azure-signalr/signalr-overview) requires TLS 1.2 and doesn't support SHA-1-based cipher suites.</span></span> <span data-ttu-id="4ceef-138">Android ha [aggiunto il supporto per i pacchetti di crittografia SHA-256 (e versioni successive)](https://developer.android.com/reference/javax/net/ssl/SSLSocket) nel livello API 20.</span><span class="sxs-lookup"><span data-stu-id="4ceef-138">Android [added support for SHA-256 (and above) cipher suites](https://developer.android.com/reference/javax/net/ssl/SSLSocket) in API Level 20.</span></span>

## <a name="configure-bearer-token-authentication"></a><span data-ttu-id="4ceef-139">Configurare l'autenticazione bearer token</span><span class="sxs-lookup"><span data-stu-id="4ceef-139">Configure bearer token authentication</span></span>

<span data-ttu-id="4ceef-140">Nel client SignalR Java è possibile configurare un Bearer token da usare per l'autenticazione fornendo una "Factory di token di accesso" a [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="4ceef-140">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an "access token factory" to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="4ceef-141">Usare [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) per fornire una [RxJava](https://github.com/ReactiveX/RxJava) [stringa a\<>singola ](https://reactivex.io/documentation/single.html)RxJava.</span><span class="sxs-lookup"><span data-stu-id="4ceef-141">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="4ceef-142">Con una chiamata a [Single. rinvia](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), è possibile scrivere la logica per produrre i token di accesso per il client.</span><span class="sxs-lookup"><span data-stu-id="4ceef-142">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("YOUR HUB URL HERE")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

## <a name="known-limitations"></a><span data-ttu-id="4ceef-143">Limitazioni note</span><span class="sxs-lookup"><span data-stu-id="4ceef-143">Known limitations</span></span>

::: moniker range=">= aspnetcore-3.0"

* <span data-ttu-id="4ceef-144">È supportato solo il protocollo JSON.</span><span class="sxs-lookup"><span data-stu-id="4ceef-144">Only the JSON protocol is supported.</span></span>
* <span data-ttu-id="4ceef-145">Il trasporto del fallback del trasporto e degli eventi inviati dal server non è supportato.</span><span class="sxs-lookup"><span data-stu-id="4ceef-145">Transport fallback and the Server Sent Events transport aren't supported.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* <span data-ttu-id="4ceef-146">È supportato solo il protocollo JSON.</span><span class="sxs-lookup"><span data-stu-id="4ceef-146">Only the JSON protocol is supported.</span></span>
* <span data-ttu-id="4ceef-147">È supportato solo il trasporto WebSocket.</span><span class="sxs-lookup"><span data-stu-id="4ceef-147">Only the WebSockets transport is supported.</span></span>
* <span data-ttu-id="4ceef-148">Il flusso non è ancora supportato.</span><span class="sxs-lookup"><span data-stu-id="4ceef-148">Streaming isn't supported yet.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="4ceef-149">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="4ceef-149">Additional resources</span></span>

* [<span data-ttu-id="4ceef-150">Informazioni di riferimento sulle API Java</span><span class="sxs-lookup"><span data-stu-id="4ceef-150">Java API reference</span></span>](/java/api/com.microsoft.signalr?view=aspnet-signalr-java)
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/publish-to-azure-web-app>
* <span data-ttu-id="4ceef-151">[Documentazione SignalR senza server per i servizi di Azure](/azure/azure-signalr/signalr-concept-serverless-development-config)</span><span class="sxs-lookup"><span data-stu-id="4ceef-151">[Azure SignalR Service serverless documentation](/azure/azure-signalr/signalr-concept-serverless-development-config)</span></span>
