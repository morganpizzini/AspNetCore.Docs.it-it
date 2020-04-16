---
title: Usare gRPC nelle app del browser
author: jamesnk
description: Informazioni su come configurare i servizi gRPC su ASP.NET Core come richiamabili dalle app browser tramite gRPC-Web.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 04/15/2020
uid: grpc/browser
ms.openlocfilehash: a20e604488b1fb919f18932599ba690bfa308f0c
ms.sourcegitcommit: 6c8cff2d6753415c4f5d2ffda88159a7f6f7431a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81440766"
---
# <a name="use-grpc-in-browser-apps"></a><span data-ttu-id="721a6-103">Usare gRPC nelle app del browser</span><span class="sxs-lookup"><span data-stu-id="721a6-103">Use gRPC in browser apps</span></span>

<span data-ttu-id="721a6-104">Di [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="721a6-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

> [!IMPORTANT]
> <span data-ttu-id="721a6-105">**Il supporto gRPC-Web in .NET è sperimentale**</span><span class="sxs-lookup"><span data-stu-id="721a6-105">**gRPC-Web support in .NET is experimental**</span></span>
>
> <span data-ttu-id="721a6-106">gRPC-Web per .NET è un progetto sperimentale, non un prodotto impegnato.</span><span class="sxs-lookup"><span data-stu-id="721a6-106">gRPC-Web for .NET is an experimental project, not a committed product.</span></span> <span data-ttu-id="721a6-107">Vogliamo:</span><span class="sxs-lookup"><span data-stu-id="721a6-107">We want to:</span></span>
>
> * <span data-ttu-id="721a6-108">Verificare che il nostro approccio all'implementazione di gRPC-Web funzioni.</span><span class="sxs-lookup"><span data-stu-id="721a6-108">Test that our approach to implementing gRPC-Web works.</span></span>
> * <span data-ttu-id="721a6-109">Ottenere commenti e suggerimenti su se questo approccio è utile per gli sviluppatori .NET rispetto al modo tradizionale di configurare gRPC-Web tramite un proxy.</span><span class="sxs-lookup"><span data-stu-id="721a6-109">Get feedback on if this approach is useful to .NET developers compared to the traditional way of setting up gRPC-Web via a proxy.</span></span>
>
> <span data-ttu-id="721a6-110">Si prega [https://github.com/grpc/grpc-dotnet](https://github.com/grpc/grpc-dotnet) di lasciare un feedback a per garantire che costruiamo qualcosa che gli sviluppatori piace e sono produttivi con.</span><span class="sxs-lookup"><span data-stu-id="721a6-110">Please leave feedback at [https://github.com/grpc/grpc-dotnet](https://github.com/grpc/grpc-dotnet) to ensure we build something that developers like and are productive with.</span></span>

<span data-ttu-id="721a6-111">Non è possibile chiamare un servizio gRPC HTTP/2 da un'app basata su browser.</span><span class="sxs-lookup"><span data-stu-id="721a6-111">It is not possible to call a HTTP/2 gRPC service from a browser-based app.</span></span> <span data-ttu-id="721a6-112">[gRPC-Web](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) è un protocollo che consente alle app JavaScript e Blazor del browser di chiamare i servizi gRPC.</span><span class="sxs-lookup"><span data-stu-id="721a6-112">[gRPC-Web](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) is a protocol that allows browser JavaScript and Blazor apps to call gRPC services.</span></span> <span data-ttu-id="721a6-113">In questo articolo viene illustrato come utilizzare gRPC-Web in .NET Core.</span><span class="sxs-lookup"><span data-stu-id="721a6-113">This article explains how to use gRPC-Web in .NET Core.</span></span>

## <a name="grpc-web-in-aspnet-core-vs-envoy"></a><span data-ttu-id="721a6-114">gRPC-Web in ASP.NET Core contro Envoy</span><span class="sxs-lookup"><span data-stu-id="721a6-114">gRPC-Web in ASP.NET Core vs. Envoy</span></span>

<span data-ttu-id="721a6-115">Sono disponibili due opzioni per l'aggiunta di gRPC-Web a un'app ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="721a6-115">There are two choices for how to add gRPC-Web to an ASP.NET Core app:</span></span>

* <span data-ttu-id="721a6-116">Supporta gRPC-Web insieme a gRPC HTTP/2 in ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="721a6-116">Support gRPC-Web alongside gRPC HTTP/2 in ASP.NET Core.</span></span> <span data-ttu-id="721a6-117">Questa opzione utilizza il `Grpc.AspNetCore.Web` middleware fornito dal pacchetto.</span><span class="sxs-lookup"><span data-stu-id="721a6-117">This option uses middleware provided by the `Grpc.AspNetCore.Web` package.</span></span>
* <span data-ttu-id="721a6-118">Utilizzare il supporto gRPC-Web [del proxy dell'inviato](https://www.envoyproxy.io/) per convertire gRPC-Web in gRPC HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="721a6-118">Use the [Envoy proxy's](https://www.envoyproxy.io/) gRPC-Web support to translate gRPC-Web to gRPC HTTP/2.</span></span> <span data-ttu-id="721a6-119">La chiamata tradotta viene quindi inoltrata nell'app ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="721a6-119">The translated call is then forwarded onto the ASP.NET Core app.</span></span>

<span data-ttu-id="721a6-120">Ogni approccio è possibile utilizzare pro e contro.</span><span class="sxs-lookup"><span data-stu-id="721a6-120">There are pros and cons to each approach.</span></span> <span data-ttu-id="721a6-121">Se stai già usando Envoy come proxy nell'ambiente della tua app, potrebbe essere utile usarlo anche per fornire il supporto gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="721a6-121">If you're already using Envoy as a proxy in your app's environment, it might make sense to also use it to provide gRPC-Web support.</span></span> <span data-ttu-id="721a6-122">Se si desidera una soluzione semplice per gRPC-Web che richiede solo ASP.NET Core, `Grpc.AspNetCore.Web` è una buona scelta.</span><span class="sxs-lookup"><span data-stu-id="721a6-122">If you want a simple solution for gRPC-Web that only requires ASP.NET Core, `Grpc.AspNetCore.Web` is a good choice.</span></span>

## <a name="configure-grpc-web-in-aspnet-core"></a><span data-ttu-id="721a6-123">Configurare gRPC-Web in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="721a6-123">Configure gRPC-Web in ASP.NET Core</span></span>

<span data-ttu-id="721a6-124">I servizi gRPC ospitati in ASP.NET Core possono essere configurati per supportare gRPC-Web insieme a HTTP/2 gRPC.</span><span class="sxs-lookup"><span data-stu-id="721a6-124">gRPC services hosted in ASP.NET Core can be configured to support gRPC-Web alongside HTTP/2 gRPC.</span></span> <span data-ttu-id="721a6-125">gRPC-Web non richiede alcuna modifica ai servizi.</span><span class="sxs-lookup"><span data-stu-id="721a6-125">gRPC-Web does not require any changes to services.</span></span> <span data-ttu-id="721a6-126">L'unica modifica è la configurazione di avvio.</span><span class="sxs-lookup"><span data-stu-id="721a6-126">The only modification is startup configuration.</span></span>

<span data-ttu-id="721a6-127">Per abilitare gRPC-Web con un servizio gRPC ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="721a6-127">To enable gRPC-Web with an ASP.NET Core gRPC service:</span></span>

* <span data-ttu-id="721a6-128">Aggiungere un riferimento al pacchetto [Grpc.AspNetCore.Web.](https://www.nuget.org/packages/Grpc.AspNetCore.Web)</span><span class="sxs-lookup"><span data-stu-id="721a6-128">Add a reference to the [Grpc.AspNetCore.Web](https://www.nuget.org/packages/Grpc.AspNetCore.Web) package.</span></span>
* <span data-ttu-id="721a6-129">Configura l'app per l'utilizzo `AddGrpcWeb` `UseGrpcWeb` di gRPC-Web aggiungendo e *Startup.cs:*</span><span class="sxs-lookup"><span data-stu-id="721a6-129">Configure the app to use gRPC-Web by adding `AddGrpcWeb` and `UseGrpcWeb` to *Startup.cs*:</span></span>

[!code-csharp[](~/grpc/browser/sample/Startup.cs?name=snippet_1&highlight=10,14)]

<span data-ttu-id="721a6-130">Il codice precedente:</span><span class="sxs-lookup"><span data-stu-id="721a6-130">The preceding code:</span></span>

* <span data-ttu-id="721a6-131">Aggiunge il middleware gRPC-Web, `UseGrpcWeb`, dopo il routing e prima degli endpoint.</span><span class="sxs-lookup"><span data-stu-id="721a6-131">Adds the gRPC-Web middleware, `UseGrpcWeb`, after routing and before endpoints.</span></span>
* <span data-ttu-id="721a6-132">Specifica che `endpoints.MapGrpcService<GreeterService>()` il metodo supporta gRPC-Web con `EnableGrpcWeb`.</span><span class="sxs-lookup"><span data-stu-id="721a6-132">Specifies the `endpoints.MapGrpcService<GreeterService>()` method supports gRPC-Web with `EnableGrpcWeb`.</span></span> 

<span data-ttu-id="721a6-133">In alternativa, configurare tutti i servizi `services.AddGrpcWeb(o => o.GrpcWebEnabled = true);` per supportare gRPC-Web aggiungendo a ConfigureServices.</span><span class="sxs-lookup"><span data-stu-id="721a6-133">Alternatively, configure all services to support gRPC-Web by adding `services.AddGrpcWeb(o => o.GrpcWebEnabled = true);` to ConfigureServices.</span></span>

[!code-csharp[](~/grpc/browser/sample/AllServicesSupportExample_Startup.cs?name=snippet_1&highlight=6,13)]

> [!NOTE]
> <span data-ttu-id="721a6-134">Esiste un problema noto che causa l'esito negativo di gRPC-Web quando [ospitato da Http.sys](xref:fundamentals/servers/httpsys) in .NET Core 3.x.</span><span class="sxs-lookup"><span data-stu-id="721a6-134">There is a known issue that causes gRPC-Web to fail when [hosted by Http.sys](xref:fundamentals/servers/httpsys) in .NET Core 3.x.</span></span>
>
> <span data-ttu-id="721a6-135">Una soluzione alternativa per ottenere gRPC-Web lavorando su Http.sys è disponibile [qui](https://github.com/grpc/grpc-dotnet/issues/853#issuecomment-610078202).</span><span class="sxs-lookup"><span data-stu-id="721a6-135">A workaround to get gRPC-Web working on Http.sys is available [here](https://github.com/grpc/grpc-dotnet/issues/853#issuecomment-610078202).</span></span>

### <a name="grpc-web-and-cors"></a><span data-ttu-id="721a6-136">gRPC-Web e CORS</span><span class="sxs-lookup"><span data-stu-id="721a6-136">gRPC-Web and CORS</span></span>

<span data-ttu-id="721a6-137">La sicurezza del browser impedisce a una pagina Web di effettuare richieste a un dominio diverso da quello che ha servito la pagina web.</span><span class="sxs-lookup"><span data-stu-id="721a6-137">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="721a6-138">Questa restrizione si applica all'esecuzione di chiamate gRPC-Web con le app browser.</span><span class="sxs-lookup"><span data-stu-id="721a6-138">This restriction applies to making gRPC-Web calls with browser apps.</span></span> <span data-ttu-id="721a6-139">Ad esempio, a un'app browser servita da `https://www.contoso.com` viene `https://services.contoso.com`impedito di chiamare servizi Web gRPC ospitati su .</span><span class="sxs-lookup"><span data-stu-id="721a6-139">For example, a browser app served by `https://www.contoso.com` is blocked from calling gRPC-Web services hosted on `https://services.contoso.com`.</span></span> <span data-ttu-id="721a6-140">Condivisione tra risorse tra origini (CORS, Cross Origin Resource Sharing) può essere utilizzato per allentare questa restrizione.</span><span class="sxs-lookup"><span data-stu-id="721a6-140">Cross Origin Resource Sharing (CORS) can be used to relax this restriction.</span></span>

<span data-ttu-id="721a6-141">Per consentire all'app browser di effettuare chiamate gRPC-Web di origine incrociata, configura [CORS in ASP.NET Core](xref:security/cors).</span><span class="sxs-lookup"><span data-stu-id="721a6-141">To allow your browser app to make cross-origin gRPC-Web calls, set up [CORS in ASP.NET Core](xref:security/cors).</span></span> <span data-ttu-id="721a6-142">Utilizzare il supporto CORS incorporato ed esporre intestazioni <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>specifiche di gRPC con .</span><span class="sxs-lookup"><span data-stu-id="721a6-142">Use the built-in CORS support, and expose gRPC-specific headers with <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>.</span></span>

[!code-csharp[](~/grpc/browser/sample/CORS_Startup.cs?name=snippet_1&highlight=5-11,19,24)]

<span data-ttu-id="721a6-143">Il codice precedente:</span><span class="sxs-lookup"><span data-stu-id="721a6-143">The preceding code:</span></span>

* <span data-ttu-id="721a6-144">Chiamate `AddCors` per aggiungere servizi CORS e configura un criterio CORS che espone intestazioni specifiche gRPC.</span><span class="sxs-lookup"><span data-stu-id="721a6-144">Calls `AddCors` to add CORS services and configures a CORS policy that exposes gRPC-specific headers.</span></span>
* <span data-ttu-id="721a6-145">Chiamate `UseCors` per aggiungere il middleware CORS dopo il routing e prima degli endpoint.</span><span class="sxs-lookup"><span data-stu-id="721a6-145">Calls `UseCors` to add the CORS middleware after routing and before endpoints.</span></span>
* <span data-ttu-id="721a6-146">Specifica che `endpoints.MapGrpcService<GreeterService>()` il metodo supporta `RequiresCors`CORS con .</span><span class="sxs-lookup"><span data-stu-id="721a6-146">Specifies the `endpoints.MapGrpcService<GreeterService>()` method supports CORS with `RequiresCors`.</span></span>

## <a name="call-grpc-web-from-the-browser"></a><span data-ttu-id="721a6-147">Chiamare gRPC-Web dal browser</span><span class="sxs-lookup"><span data-stu-id="721a6-147">Call gRPC-Web from the browser</span></span>

<span data-ttu-id="721a6-148">Le app browser possono utilizzare gRPC-Web per chiamare i servizi gRPC.</span><span class="sxs-lookup"><span data-stu-id="721a6-148">Browser apps can use gRPC-Web to call gRPC services.</span></span> <span data-ttu-id="721a6-149">Esistono alcuni requisiti e limitazioni quando si chiamano i servizi gRPC con gRPC-Web dal browser:</span><span class="sxs-lookup"><span data-stu-id="721a6-149">There are some requirements and limitations when calling gRPC services with gRPC-Web from the browser:</span></span>

* <span data-ttu-id="721a6-150">Il server deve essere stato configurato per supportare gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="721a6-150">The server must have been configured to support gRPC-Web.</span></span>
* <span data-ttu-id="721a6-151">Lo streaming client e le chiamate di streaming bidirezionale non sono supportati.</span><span class="sxs-lookup"><span data-stu-id="721a6-151">Client streaming and bidirectional streaming calls aren't supported.</span></span> <span data-ttu-id="721a6-152">Lo streaming del server è supportato.</span><span class="sxs-lookup"><span data-stu-id="721a6-152">Server streaming is supported.</span></span>
* <span data-ttu-id="721a6-153">La chiamata ai servizi gRPC su un dominio diverso richiede la configurazione di [CORS](xref:security/cors) sul server.</span><span class="sxs-lookup"><span data-stu-id="721a6-153">Calling gRPC services on a different domain requires [CORS](xref:security/cors) to be configured on the server.</span></span>

### <a name="javascript-grpc-web-client"></a><span data-ttu-id="721a6-154">Client JavaScript gRPC-Web</span><span class="sxs-lookup"><span data-stu-id="721a6-154">JavaScript gRPC-Web client</span></span>

<span data-ttu-id="721a6-155">È disponibile un client JavaScript gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="721a6-155">There is a JavaScript gRPC-Web client.</span></span> <span data-ttu-id="721a6-156">Per istruzioni su come utilizzare gRPC-Web da JavaScript, consultate [Scrivere codice client JavaScript con gRPC-Web](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code).</span><span class="sxs-lookup"><span data-stu-id="721a6-156">For instructions on how to use gRPC-Web from JavaScript, see [write JavaScript client code with gRPC-Web](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code).</span></span>

### <a name="configure-grpc-web-with-the-net-grpc-client"></a><span data-ttu-id="721a6-157">Configurare gRPC-Web con il client gRPC .NET</span><span class="sxs-lookup"><span data-stu-id="721a6-157">Configure gRPC-Web with the .NET gRPC client</span></span>

<span data-ttu-id="721a6-158">Il client gRPC .NET può essere configurato per effettuare chiamate gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="721a6-158">The .NET gRPC client can be configured to make gRPC-Web calls.</span></span> <span data-ttu-id="721a6-159">Ciò è utile per le applicazioni [Blazor WebAssembly,](xref:blazor/index#blazor-webassembly) che sono ospitate nel browser e hanno le stesse limitazioni HTTP del codice JavaScript.</span><span class="sxs-lookup"><span data-stu-id="721a6-159">This is useful for [Blazor WebAssembly](xref:blazor/index#blazor-webassembly) apps, which are hosted in the browser and have the same HTTP limitations of JavaScript code.</span></span> <span data-ttu-id="721a6-160">Chiamare gRPC-Web con un client .NET equivale [a HTTP/2 gRPC](xref:grpc/client).</span><span class="sxs-lookup"><span data-stu-id="721a6-160">Calling gRPC-Web with a .NET client is [the same as HTTP/2 gRPC](xref:grpc/client).</span></span> <span data-ttu-id="721a6-161">L'unica modifica è la modalità di creazione del canale.</span><span class="sxs-lookup"><span data-stu-id="721a6-161">The only modification is how the channel is created.</span></span>

<span data-ttu-id="721a6-162">Per utilizzare gRPC-Web:</span><span class="sxs-lookup"><span data-stu-id="721a6-162">To use gRPC-Web:</span></span>

* <span data-ttu-id="721a6-163">Aggiungere un riferimento al pacchetto [Grpc.Net.Client.Web.](https://www.nuget.org/packages/Grpc.Net.Client.Web)</span><span class="sxs-lookup"><span data-stu-id="721a6-163">Add a reference to the [Grpc.Net.Client.Web](https://www.nuget.org/packages/Grpc.Net.Client.Web) package.</span></span>
* <span data-ttu-id="721a6-164">Verificare che il riferimento al pacchetto [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) sia 2.27.0 o superiore.</span><span class="sxs-lookup"><span data-stu-id="721a6-164">Ensure the reference to [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) package is 2.27.0 or greater.</span></span>
* <span data-ttu-id="721a6-165">Configurare il canale `GrpcWebHandler`per l'utilizzo di :</span><span class="sxs-lookup"><span data-stu-id="721a6-165">Configure the channel to use the `GrpcWebHandler`:</span></span>

[!code-csharp[](~/grpc/browser/sample/Handler.cs?name=snippet_1)]

<span data-ttu-id="721a6-166">Il codice precedente:</span><span class="sxs-lookup"><span data-stu-id="721a6-166">The preceding code:</span></span>

* <span data-ttu-id="721a6-167">Configura un canale per l'utilizzo di gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="721a6-167">Configures a channel to use gRPC-Web.</span></span>
* <span data-ttu-id="721a6-168">Crea un client ed effettua una chiamata utilizzando il canale.</span><span class="sxs-lookup"><span data-stu-id="721a6-168">Creates a client and makes a call using the channel.</span></span>

<span data-ttu-id="721a6-169">L'utente `GrpcWebHandler` ha le seguenti opzioni di configurazione al momento della creazione:</span><span class="sxs-lookup"><span data-stu-id="721a6-169">The `GrpcWebHandler` has the following configuration options when created:</span></span>

* <span data-ttu-id="721a6-170">**InnerHandler:** sottostante <xref:System.Net.Http.HttpMessageHandler> che effettua la richiesta HTTP `HttpClientHandler`gRPC, ad esempio .</span><span class="sxs-lookup"><span data-stu-id="721a6-170">**InnerHandler**: The underlying <xref:System.Net.Http.HttpMessageHandler> that makes the gRPC HTTP request, for example, `HttpClientHandler`.</span></span>
* <span data-ttu-id="721a6-171">Mode ( **Modalità**) : Tipo di `Content-Type` `application/grpc-web` enumerazione che specifica se la richiesta HTTP gRPC è o . `application/grpc-web-text`</span><span class="sxs-lookup"><span data-stu-id="721a6-171">**Mode**: An enumeration type that specifies whether the gRPC HTTP request `Content-Type` is `application/grpc-web` or `application/grpc-web-text`.</span></span>
    * <span data-ttu-id="721a6-172">`GrpcWebMode.GrpcWeb`configura l'invio del contenuto senza codifica.</span><span class="sxs-lookup"><span data-stu-id="721a6-172">`GrpcWebMode.GrpcWeb` configures content to be sent without encoding.</span></span> <span data-ttu-id="721a6-173">Valore predefinito.</span><span class="sxs-lookup"><span data-stu-id="721a6-173">Default value.</span></span>
    * <span data-ttu-id="721a6-174">`GrpcWebMode.GrpcWebText`configura il contenuto in base 64 codificato.</span><span class="sxs-lookup"><span data-stu-id="721a6-174">`GrpcWebMode.GrpcWebText` configures content to be base64 encoded.</span></span> <span data-ttu-id="721a6-175">Obbligatorio per le chiamate di streaming server nei browser.</span><span class="sxs-lookup"><span data-stu-id="721a6-175">Required for server streaming calls in browsers.</span></span>
* <span data-ttu-id="721a6-176">**HttpVersion:** protocollo `Version` HTTP utilizzato per impostare [HttpRequestMessage.Version](xref:System.Net.Http.HttpRequestMessage.Version) nella richiesta HTTP gRPC sottostante.</span><span class="sxs-lookup"><span data-stu-id="721a6-176">**HttpVersion**: HTTP protocol `Version` used to set [HttpRequestMessage.Version](xref:System.Net.Http.HttpRequestMessage.Version) on the underlying gRPC HTTP request.</span></span> <span data-ttu-id="721a6-177">gRPC-Web non richiede una versione specifica e non sostituisce il valore predefinito a meno che non venga specificato.</span><span class="sxs-lookup"><span data-stu-id="721a6-177">gRPC-Web doesn't require a specific version and doesn't override the default unless specified.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="721a6-178">I client gRPC generati dispongono di metodi sincroni e asincroni per la chiamata di metodi unario.</span><span class="sxs-lookup"><span data-stu-id="721a6-178">Generated gRPC clients have sync and async methods for calling unary methods.</span></span> <span data-ttu-id="721a6-179">Ad esempio, `SayHello` è `SayHelloAsync` la sincronizzazione ed è asincrona.</span><span class="sxs-lookup"><span data-stu-id="721a6-179">For example, `SayHello` is sync and `SayHelloAsync` is async.</span></span> <span data-ttu-id="721a6-180">La chiamata di un metodo di sincronizzazione in un'applicazione Blazor WebAssembly causerà l'app non risponde.</span><span class="sxs-lookup"><span data-stu-id="721a6-180">Calling a sync method in a Blazor WebAssembly app will cause the app to become unresponsive.</span></span> <span data-ttu-id="721a6-181">I metodi asincroni devono essere sempre utilizzati in Blazor WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="721a6-181">Async methods must always be used in Blazor WebAssembly.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="721a6-182">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="721a6-182">Additional resources</span></span>

* [<span data-ttu-id="721a6-183">progetto gRPC per client Web GitHub</span><span class="sxs-lookup"><span data-stu-id="721a6-183">gRPC for Web Clients GitHub project</span></span>](https://github.com/grpc/grpc-web)
* <xref:security/cors>
