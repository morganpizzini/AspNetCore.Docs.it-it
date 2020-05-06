---
title: Usare gRPC nelle app del browser
author: jamesnk
description: Informazioni su come configurare i servizi gRPC in ASP.NET Core per essere richiamabili dalle app del browser usando gRPC-Web.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 04/15/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: grpc/browser
ms.openlocfilehash: a74f7acb54b4601a0c30ff1a39dc30231e2b5a78
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774743"
---
# <a name="use-grpc-in-browser-apps"></a><span data-ttu-id="2bb05-103">Usare gRPC nelle app del browser</span><span class="sxs-lookup"><span data-stu-id="2bb05-103">Use gRPC in browser apps</span></span>

<span data-ttu-id="2bb05-104">Di [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="2bb05-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

> [!IMPORTANT]
> <span data-ttu-id="2bb05-105">**gRPC-supporto Web in .NET è sperimentale**</span><span class="sxs-lookup"><span data-stu-id="2bb05-105">**gRPC-Web support in .NET is experimental**</span></span>
>
> <span data-ttu-id="2bb05-106">gRPC-Web per .NET è un progetto sperimentale, non un prodotto di cui è stato eseguito il commit.</span><span class="sxs-lookup"><span data-stu-id="2bb05-106">gRPC-Web for .NET is an experimental project, not a committed product.</span></span> <span data-ttu-id="2bb05-107">Si desidera:</span><span class="sxs-lookup"><span data-stu-id="2bb05-107">We want to:</span></span>
>
> * <span data-ttu-id="2bb05-108">Testare l'approccio all'implementazione di gRPC-Web Works.</span><span class="sxs-lookup"><span data-stu-id="2bb05-108">Test that our approach to implementing gRPC-Web works.</span></span>
> * <span data-ttu-id="2bb05-109">Ottenere commenti e suggerimenti su se questo approccio è utile per gli sviluppatori .NET rispetto al metodo tradizionale di configurazione di gRPC-Web tramite un proxy.</span><span class="sxs-lookup"><span data-stu-id="2bb05-109">Get feedback on if this approach is useful to .NET developers compared to the traditional way of setting up gRPC-Web via a proxy.</span></span>
>
> <span data-ttu-id="2bb05-110">Invia commenti e suggerimenti [https://github.com/grpc/grpc-dotnet](https://github.com/grpc/grpc-dotnet) per assicurarsi di creare qualcosa che gli sviluppatori desiderano e siano produttivi.</span><span class="sxs-lookup"><span data-stu-id="2bb05-110">Please leave feedback at [https://github.com/grpc/grpc-dotnet](https://github.com/grpc/grpc-dotnet) to ensure we build something that developers like and are productive with.</span></span>

<span data-ttu-id="2bb05-111">Non è possibile chiamare un servizio HTTP/2 gRPC da un'app basata su browser.</span><span class="sxs-lookup"><span data-stu-id="2bb05-111">It is not possible to call a HTTP/2 gRPC service from a browser-based app.</span></span> <span data-ttu-id="2bb05-112">[gRPC-Web](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) è un protocollo che consente alle app e Blazor JavaScript del browser di chiamare i servizi gRPC.</span><span class="sxs-lookup"><span data-stu-id="2bb05-112">[gRPC-Web](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) is a protocol that allows browser JavaScript and Blazor apps to call gRPC services.</span></span> <span data-ttu-id="2bb05-113">Questo articolo illustra come usare gRPC-Web in .NET Core.</span><span class="sxs-lookup"><span data-stu-id="2bb05-113">This article explains how to use gRPC-Web in .NET Core.</span></span>

## <a name="grpc-web-in-aspnet-core-vs-envoy"></a><span data-ttu-id="2bb05-114">gRPC-Web in ASP.NET Core e inviato</span><span class="sxs-lookup"><span data-stu-id="2bb05-114">gRPC-Web in ASP.NET Core vs. Envoy</span></span>

<span data-ttu-id="2bb05-115">Sono disponibili due opzioni per l'aggiunta di gRPC-Web a un'app ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="2bb05-115">There are two choices for how to add gRPC-Web to an ASP.NET Core app:</span></span>

* <span data-ttu-id="2bb05-116">Supportare gRPC-Web insieme a gRPC HTTP/2 in ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="2bb05-116">Support gRPC-Web alongside gRPC HTTP/2 in ASP.NET Core.</span></span> <span data-ttu-id="2bb05-117">Questa opzione utilizza il `Grpc.AspNetCore.Web` middleware fornito dal pacchetto.</span><span class="sxs-lookup"><span data-stu-id="2bb05-117">This option uses middleware provided by the `Grpc.AspNetCore.Web` package.</span></span>
* <span data-ttu-id="2bb05-118">Usare il supporto gRPC-Web del [proxy inviato](https://www.envoyproxy.io/) per tradurre GRPC-Web in gRPC http/2.</span><span class="sxs-lookup"><span data-stu-id="2bb05-118">Use the [Envoy proxy's](https://www.envoyproxy.io/) gRPC-Web support to translate gRPC-Web to gRPC HTTP/2.</span></span> <span data-ttu-id="2bb05-119">La chiamata tradotta viene quindi trasmessa nell'app ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="2bb05-119">The translated call is then forwarded onto the ASP.NET Core app.</span></span>

<span data-ttu-id="2bb05-120">Ogni approccio presenta vantaggi e svantaggi.</span><span class="sxs-lookup"><span data-stu-id="2bb05-120">There are pros and cons to each approach.</span></span> <span data-ttu-id="2bb05-121">Se si usa già l'inviato come proxy nell'ambiente dell'app, potrebbe essere utile usarlo anche per fornire il supporto gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="2bb05-121">If you're already using Envoy as a proxy in your app's environment, it might make sense to also use it to provide gRPC-Web support.</span></span> <span data-ttu-id="2bb05-122">Se si vuole una soluzione semplice per gRPC-Web che richiede solo ASP.NET Core, `Grpc.AspNetCore.Web` è una scelta ottimale.</span><span class="sxs-lookup"><span data-stu-id="2bb05-122">If you want a simple solution for gRPC-Web that only requires ASP.NET Core, `Grpc.AspNetCore.Web` is a good choice.</span></span>

## <a name="configure-grpc-web-in-aspnet-core"></a><span data-ttu-id="2bb05-123">Configurare gRPC-Web in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2bb05-123">Configure gRPC-Web in ASP.NET Core</span></span>

<span data-ttu-id="2bb05-124">i servizi gRPC ospitati in ASP.NET Core possono essere configurati per supportare gRPC-Web insieme a HTTP/2 gRPC.</span><span class="sxs-lookup"><span data-stu-id="2bb05-124">gRPC services hosted in ASP.NET Core can be configured to support gRPC-Web alongside HTTP/2 gRPC.</span></span> <span data-ttu-id="2bb05-125">gRPC-Web non richiede alcuna modifica ai servizi.</span><span class="sxs-lookup"><span data-stu-id="2bb05-125">gRPC-Web does not require any changes to services.</span></span> <span data-ttu-id="2bb05-126">L'unica modifica è la configurazione di avvio.</span><span class="sxs-lookup"><span data-stu-id="2bb05-126">The only modification is startup configuration.</span></span>

<span data-ttu-id="2bb05-127">Per abilitare gRPC-Web con un servizio gRPC ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="2bb05-127">To enable gRPC-Web with an ASP.NET Core gRPC service:</span></span>

* <span data-ttu-id="2bb05-128">Aggiungere un riferimento al pacchetto [Grpc. AspNetCore. Web](https://www.nuget.org/packages/Grpc.AspNetCore.Web) .</span><span class="sxs-lookup"><span data-stu-id="2bb05-128">Add a reference to the [Grpc.AspNetCore.Web](https://www.nuget.org/packages/Grpc.AspNetCore.Web) package.</span></span>
* <span data-ttu-id="2bb05-129">Configurare l'app per l'uso di gRPC-Web `AddGrpcWeb` aggiungendo `UseGrpcWeb` e a *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="2bb05-129">Configure the app to use gRPC-Web by adding `AddGrpcWeb` and `UseGrpcWeb` to *Startup.cs*:</span></span>

[!code-csharp[](~/grpc/browser/sample/Startup.cs?name=snippet_1&highlight=10,14)]

<span data-ttu-id="2bb05-130">Il codice precedente:</span><span class="sxs-lookup"><span data-stu-id="2bb05-130">The preceding code:</span></span>

* <span data-ttu-id="2bb05-131">Aggiunge il middleware gRPC-Web, `UseGrpcWeb`, dopo il routing e prima degli endpoint.</span><span class="sxs-lookup"><span data-stu-id="2bb05-131">Adds the gRPC-Web middleware, `UseGrpcWeb`, after routing and before endpoints.</span></span>
* <span data-ttu-id="2bb05-132">Specifica che `endpoints.MapGrpcService<GreeterService>()` il metodo supporta GRPC-Web `EnableGrpcWeb`con.</span><span class="sxs-lookup"><span data-stu-id="2bb05-132">Specifies the `endpoints.MapGrpcService<GreeterService>()` method supports gRPC-Web with `EnableGrpcWeb`.</span></span> 

<span data-ttu-id="2bb05-133">In alternativa, configurare tutti i servizi per il supporto di gRPC- `services.AddGrpcWeb(o => o.GrpcWebEnabled = true);` Web mediante l'aggiunta a ConfigureServices.</span><span class="sxs-lookup"><span data-stu-id="2bb05-133">Alternatively, configure all services to support gRPC-Web by adding `services.AddGrpcWeb(o => o.GrpcWebEnabled = true);` to ConfigureServices.</span></span>

[!code-csharp[](~/grpc/browser/sample/AllServicesSupportExample_Startup.cs?name=snippet_1&highlight=6,13)]

> [!NOTE]
> <span data-ttu-id="2bb05-134">Esiste un problema noto che causa l'esito negativo di gRPC-Web quando è [ospitato da http. sys](xref:fundamentals/servers/httpsys) in .NET Core 3. x.</span><span class="sxs-lookup"><span data-stu-id="2bb05-134">There is a known issue that causes gRPC-Web to fail when [hosted by Http.sys](xref:fundamentals/servers/httpsys) in .NET Core 3.x.</span></span>
>
> <span data-ttu-id="2bb05-135">Una soluzione alternativa per ottenere gRPC-Web che lavora su http. sys è disponibile [qui](https://github.com/grpc/grpc-dotnet/issues/853#issuecomment-610078202).</span><span class="sxs-lookup"><span data-stu-id="2bb05-135">A workaround to get gRPC-Web working on Http.sys is available [here](https://github.com/grpc/grpc-dotnet/issues/853#issuecomment-610078202).</span></span>

### <a name="grpc-web-and-cors"></a><span data-ttu-id="2bb05-136">gRPC-Web e CORS</span><span class="sxs-lookup"><span data-stu-id="2bb05-136">gRPC-Web and CORS</span></span>

<span data-ttu-id="2bb05-137">La sicurezza del browser impedisce a una pagina Web di eseguire richieste a un dominio diverso da quello che ha gestito la pagina Web.</span><span class="sxs-lookup"><span data-stu-id="2bb05-137">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="2bb05-138">Questa restrizione si applica all'esecuzione di chiamate gRPC-Web con le app browser.</span><span class="sxs-lookup"><span data-stu-id="2bb05-138">This restriction applies to making gRPC-Web calls with browser apps.</span></span> <span data-ttu-id="2bb05-139">Ad esempio, un'app browser servita da `https://www.contoso.com` viene bloccata dalla chiamata a GRPC-Web Services ospitata in `https://services.contoso.com`.</span><span class="sxs-lookup"><span data-stu-id="2bb05-139">For example, a browser app served by `https://www.contoso.com` is blocked from calling gRPC-Web services hosted on `https://services.contoso.com`.</span></span> <span data-ttu-id="2bb05-140">La condivisione di risorse tra le origini (CORS) può essere usata per attenuare questa restrizione.</span><span class="sxs-lookup"><span data-stu-id="2bb05-140">Cross Origin Resource Sharing (CORS) can be used to relax this restriction.</span></span>

<span data-ttu-id="2bb05-141">Per consentire all'app browser di eseguire chiamate web gRPC tra le origini, configurare [CORS in ASP.NET Core](xref:security/cors).</span><span class="sxs-lookup"><span data-stu-id="2bb05-141">To allow your browser app to make cross-origin gRPC-Web calls, set up [CORS in ASP.NET Core](xref:security/cors).</span></span> <span data-ttu-id="2bb05-142">Usare il supporto CORS incorporato ed esporre intestazioni specifiche di gRPC con <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>.</span><span class="sxs-lookup"><span data-stu-id="2bb05-142">Use the built-in CORS support, and expose gRPC-specific headers with <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>.</span></span>

[!code-csharp[](~/grpc/browser/sample/CORS_Startup.cs?name=snippet_1&highlight=5-11,19,24)]

<span data-ttu-id="2bb05-143">Il codice precedente:</span><span class="sxs-lookup"><span data-stu-id="2bb05-143">The preceding code:</span></span>

* <span data-ttu-id="2bb05-144">Chiama `AddCors` per aggiungere servizi CORS e configura un criterio CORS che espone intestazioni specifiche di gRPC.</span><span class="sxs-lookup"><span data-stu-id="2bb05-144">Calls `AddCors` to add CORS services and configures a CORS policy that exposes gRPC-specific headers.</span></span>
* <span data-ttu-id="2bb05-145">Chiama `UseCors` per aggiungere il middleware CORS dopo il routing e prima degli endpoint.</span><span class="sxs-lookup"><span data-stu-id="2bb05-145">Calls `UseCors` to add the CORS middleware after routing and before endpoints.</span></span>
* <span data-ttu-id="2bb05-146">Specifica che `endpoints.MapGrpcService<GreeterService>()` il metodo supporta CORS `RequiresCors`con.</span><span class="sxs-lookup"><span data-stu-id="2bb05-146">Specifies the `endpoints.MapGrpcService<GreeterService>()` method supports CORS with `RequiresCors`.</span></span>

## <a name="call-grpc-web-from-the-browser"></a><span data-ttu-id="2bb05-147">Chiamare gRPC-Web dal browser</span><span class="sxs-lookup"><span data-stu-id="2bb05-147">Call gRPC-Web from the browser</span></span>

<span data-ttu-id="2bb05-148">Le app browser possono usare gRPC-Web per chiamare i servizi gRPC.</span><span class="sxs-lookup"><span data-stu-id="2bb05-148">Browser apps can use gRPC-Web to call gRPC services.</span></span> <span data-ttu-id="2bb05-149">Esistono alcuni requisiti e limitazioni quando si chiamano i servizi gRPC con gRPC-Web dal browser:</span><span class="sxs-lookup"><span data-stu-id="2bb05-149">There are some requirements and limitations when calling gRPC services with gRPC-Web from the browser:</span></span>

* <span data-ttu-id="2bb05-150">Il server deve essere stato configurato per supportare gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="2bb05-150">The server must have been configured to support gRPC-Web.</span></span>
* <span data-ttu-id="2bb05-151">Il flusso client e le chiamate di streaming bidirezionali non sono supportate.</span><span class="sxs-lookup"><span data-stu-id="2bb05-151">Client streaming and bidirectional streaming calls aren't supported.</span></span> <span data-ttu-id="2bb05-152">Il flusso del server è supportato.</span><span class="sxs-lookup"><span data-stu-id="2bb05-152">Server streaming is supported.</span></span>
* <span data-ttu-id="2bb05-153">Per chiamare i servizi gRPC in un dominio diverso è necessario configurare [CORS](xref:security/cors) nel server.</span><span class="sxs-lookup"><span data-stu-id="2bb05-153">Calling gRPC services on a different domain requires [CORS](xref:security/cors) to be configured on the server.</span></span>

### <a name="javascript-grpc-web-client"></a><span data-ttu-id="2bb05-154">JavaScript gRPC-client Web</span><span class="sxs-lookup"><span data-stu-id="2bb05-154">JavaScript gRPC-Web client</span></span>

<span data-ttu-id="2bb05-155">È disponibile un client JavaScript gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="2bb05-155">There is a JavaScript gRPC-Web client.</span></span> <span data-ttu-id="2bb05-156">Per istruzioni su come usare gRPC-Web da JavaScript, vedere [scrivere codice client JavaScript con gRPC-Web](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code).</span><span class="sxs-lookup"><span data-stu-id="2bb05-156">For instructions on how to use gRPC-Web from JavaScript, see [write JavaScript client code with gRPC-Web](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code).</span></span>

### <a name="configure-grpc-web-with-the-net-grpc-client"></a><span data-ttu-id="2bb05-157">Configurare gRPC-Web con il client gRPC .NET</span><span class="sxs-lookup"><span data-stu-id="2bb05-157">Configure gRPC-Web with the .NET gRPC client</span></span>

<span data-ttu-id="2bb05-158">Il client gRPC .NET può essere configurato per effettuare chiamate gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="2bb05-158">The .NET gRPC client can be configured to make gRPC-Web calls.</span></span> <span data-ttu-id="2bb05-159">Questa operazione è utile [ Blazor ](xref:blazor/index#blazor-webassembly) per le app webassembly, che sono ospitate nel browser e hanno le stesse limitazioni http del codice JavaScript.</span><span class="sxs-lookup"><span data-stu-id="2bb05-159">This is useful for [Blazor WebAssembly](xref:blazor/index#blazor-webassembly) apps, which are hosted in the browser and have the same HTTP limitations of JavaScript code.</span></span> <span data-ttu-id="2bb05-160">La chiamata di gRPC-Web con un client .NET equivale a [http/2 gRPC](xref:grpc/client).</span><span class="sxs-lookup"><span data-stu-id="2bb05-160">Calling gRPC-Web with a .NET client is [the same as HTTP/2 gRPC](xref:grpc/client).</span></span> <span data-ttu-id="2bb05-161">L'unica modifica è la modalità di creazione del canale.</span><span class="sxs-lookup"><span data-stu-id="2bb05-161">The only modification is how the channel is created.</span></span>

<span data-ttu-id="2bb05-162">Per usare gRPC-Web:</span><span class="sxs-lookup"><span data-stu-id="2bb05-162">To use gRPC-Web:</span></span>

* <span data-ttu-id="2bb05-163">Aggiungere un riferimento al pacchetto [Grpc .NET. client. Web](https://www.nuget.org/packages/Grpc.Net.Client.Web) .</span><span class="sxs-lookup"><span data-stu-id="2bb05-163">Add a reference to the [Grpc.Net.Client.Web](https://www.nuget.org/packages/Grpc.Net.Client.Web) package.</span></span>
* <span data-ttu-id="2bb05-164">Verificare che il riferimento a [Grpc .NET. client](https://www.nuget.org/packages/Grpc.Net.Client) Package sia 2.27.0 o versione successiva.</span><span class="sxs-lookup"><span data-stu-id="2bb05-164">Ensure the reference to [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) package is 2.27.0 or greater.</span></span>
* <span data-ttu-id="2bb05-165">Configurare il canale per l' `GrpcWebHandler`utilizzo di:</span><span class="sxs-lookup"><span data-stu-id="2bb05-165">Configure the channel to use the `GrpcWebHandler`:</span></span>

[!code-csharp[](~/grpc/browser/sample/Handler.cs?name=snippet_1)]

<span data-ttu-id="2bb05-166">Il codice precedente:</span><span class="sxs-lookup"><span data-stu-id="2bb05-166">The preceding code:</span></span>

* <span data-ttu-id="2bb05-167">Configura un canale per l'uso di gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="2bb05-167">Configures a channel to use gRPC-Web.</span></span>
* <span data-ttu-id="2bb05-168">Crea un client ed effettua una chiamata utilizzando il canale.</span><span class="sxs-lookup"><span data-stu-id="2bb05-168">Creates a client and makes a call using the channel.</span></span>

<span data-ttu-id="2bb05-169">Al `GrpcWebHandler` momento della creazione di sono disponibili le opzioni di configurazione seguenti:</span><span class="sxs-lookup"><span data-stu-id="2bb05-169">The `GrpcWebHandler` has the following configuration options when created:</span></span>

* <span data-ttu-id="2bb05-170">**InnerHandler**: oggetto sottostante <xref:System.Net.Http.HttpMessageHandler> che esegue la richiesta http gRPC, ad esempio `HttpClientHandler`.</span><span class="sxs-lookup"><span data-stu-id="2bb05-170">**InnerHandler**: The underlying <xref:System.Net.Http.HttpMessageHandler> that makes the gRPC HTTP request, for example, `HttpClientHandler`.</span></span>
* <span data-ttu-id="2bb05-171">**Mode**: tipo di enumerazione che specifica se la richiesta `Content-Type` http gRPC è `application/grpc-web` o `application/grpc-web-text`.</span><span class="sxs-lookup"><span data-stu-id="2bb05-171">**Mode**: An enumeration type that specifies whether the gRPC HTTP request `Content-Type` is `application/grpc-web` or `application/grpc-web-text`.</span></span>
    * <span data-ttu-id="2bb05-172">`GrpcWebMode.GrpcWeb`Configura il contenuto da inviare senza codifica.</span><span class="sxs-lookup"><span data-stu-id="2bb05-172">`GrpcWebMode.GrpcWeb` configures content to be sent without encoding.</span></span> <span data-ttu-id="2bb05-173">Valore predefinito.</span><span class="sxs-lookup"><span data-stu-id="2bb05-173">Default value.</span></span>
    * <span data-ttu-id="2bb05-174">`GrpcWebMode.GrpcWebText`Configura il contenuto con codifica Base64.</span><span class="sxs-lookup"><span data-stu-id="2bb05-174">`GrpcWebMode.GrpcWebText` configures content to be base64 encoded.</span></span> <span data-ttu-id="2bb05-175">Obbligatorio per le chiamate di streaming del server nei browser.</span><span class="sxs-lookup"><span data-stu-id="2bb05-175">Required for server streaming calls in browsers.</span></span>
* <span data-ttu-id="2bb05-176">**HttpVersion**: protocollo `Version` http usato per impostare [HTTPREQUESTMESSAGE. Version](xref:System.Net.Http.HttpRequestMessage.Version) sulla richiesta http gRPC sottostante.</span><span class="sxs-lookup"><span data-stu-id="2bb05-176">**HttpVersion**: HTTP protocol `Version` used to set [HttpRequestMessage.Version](xref:System.Net.Http.HttpRequestMessage.Version) on the underlying gRPC HTTP request.</span></span> <span data-ttu-id="2bb05-177">gRPC-Web non richiede una versione specifica e non esegue l'override dell'impostazione predefinita, a meno che non sia specificato.</span><span class="sxs-lookup"><span data-stu-id="2bb05-177">gRPC-Web doesn't require a specific version and doesn't override the default unless specified.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="2bb05-178">I client gRPC generati hanno metodi Sync e Async per la chiamata di metodi unaria.</span><span class="sxs-lookup"><span data-stu-id="2bb05-178">Generated gRPC clients have sync and async methods for calling unary methods.</span></span> <span data-ttu-id="2bb05-179">Ad esempio, `SayHello` è Sync ed `SayHelloAsync` è Async.</span><span class="sxs-lookup"><span data-stu-id="2bb05-179">For example, `SayHello` is sync and `SayHelloAsync` is async.</span></span> <span data-ttu-id="2bb05-180">La chiamata di un metodo di Blazor sincronizzazione in un'app webassembly provocherà la mancata risposta dell'app.</span><span class="sxs-lookup"><span data-stu-id="2bb05-180">Calling a sync method in a Blazor WebAssembly app will cause the app to become unresponsive.</span></span> <span data-ttu-id="2bb05-181">I metodi asincroni devono essere sempre usati Blazor in webassembly.</span><span class="sxs-lookup"><span data-stu-id="2bb05-181">Async methods must always be used in Blazor WebAssembly.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2bb05-182">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="2bb05-182">Additional resources</span></span>

* [<span data-ttu-id="2bb05-183">progetto GitHub di gRPC per client Web</span><span class="sxs-lookup"><span data-stu-id="2bb05-183">gRPC for Web Clients GitHub project</span></span>](https://github.com/grpc/grpc-web)
* <xref:security/cors>
