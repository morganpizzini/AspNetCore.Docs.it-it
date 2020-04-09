---
title: Usare gRPC nelle app del browser
author: jamesnk
description: Informazioni su come configurare i servizi gRPC su ASP.NET Core come richiamabili dalle app browser tramite gRPC-Web.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 02/16/2020
uid: grpc/browser
ms.openlocfilehash: 0bb8157525ccd32991d8925816c1b599c3d21a92
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80977145"
---
# <a name="use-grpc-in-browser-apps"></a><span data-ttu-id="1a38b-103">Usare gRPC nelle app del browser</span><span class="sxs-lookup"><span data-stu-id="1a38b-103">Use gRPC in browser apps</span></span>

<span data-ttu-id="1a38b-104">Di [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="1a38b-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

> [!IMPORTANT]
> <span data-ttu-id="1a38b-105">**Il supporto gRPC-Web in .NET è sperimentale**</span><span class="sxs-lookup"><span data-stu-id="1a38b-105">**gRPC-Web support in .NET is experimental**</span></span>
>
> <span data-ttu-id="1a38b-106">gRPC-Web per .NET è un progetto sperimentale, non un prodotto impegnato.</span><span class="sxs-lookup"><span data-stu-id="1a38b-106">gRPC-Web for .NET is an experimental project, not a committed product.</span></span> <span data-ttu-id="1a38b-107">Vogliamo:</span><span class="sxs-lookup"><span data-stu-id="1a38b-107">We want to:</span></span>
>
> * <span data-ttu-id="1a38b-108">Verificare che il nostro approccio all'implementazione di gRPC-Web funzioni.</span><span class="sxs-lookup"><span data-stu-id="1a38b-108">Test that our approach to implementing gRPC-Web works.</span></span>
> * <span data-ttu-id="1a38b-109">Ottenere commenti e suggerimenti su se questo approccio è utile per gli sviluppatori .NET rispetto al modo tradizionale di configurare gRPC-Web tramite un proxy.</span><span class="sxs-lookup"><span data-stu-id="1a38b-109">Get feedback on if this approach is useful to .NET developers compared to the traditional way of setting up gRPC-Web via a proxy.</span></span>
>
> <span data-ttu-id="1a38b-110">Si prega [https://github.com/grpc/grpc-dotnet](https://github.com/grpc/grpc-dotnet) di lasciare un feedback a per garantire che costruiamo qualcosa che gli sviluppatori piace e sono produttivi con.</span><span class="sxs-lookup"><span data-stu-id="1a38b-110">Please leave feedback at [https://github.com/grpc/grpc-dotnet](https://github.com/grpc/grpc-dotnet) to ensure we build something that developers like and are productive with.</span></span>

<span data-ttu-id="1a38b-111">Non è possibile chiamare un servizio gRPC HTTP/2 da un'app basata su browser.</span><span class="sxs-lookup"><span data-stu-id="1a38b-111">It is not possible to call a HTTP/2 gRPC service from a browser-based app.</span></span> <span data-ttu-id="1a38b-112">[gRPC-Web](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) è un protocollo che consente alle app JavaScript e Blazor del browser di chiamare i servizi gRPC.</span><span class="sxs-lookup"><span data-stu-id="1a38b-112">[gRPC-Web](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) is a protocol that allows browser JavaScript and Blazor apps to call gRPC services.</span></span> <span data-ttu-id="1a38b-113">In questo articolo viene illustrato come utilizzare gRPC-Web in .NET Core.</span><span class="sxs-lookup"><span data-stu-id="1a38b-113">This article explains how to use gRPC-Web in .NET Core.</span></span>

## <a name="configure-grpc-web-in-aspnet-core"></a><span data-ttu-id="1a38b-114">Configurare gRPC-Web in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1a38b-114">Configure gRPC-Web in ASP.NET Core</span></span>

<span data-ttu-id="1a38b-115">I servizi gRPC ospitati in ASP.NET Core possono essere configurati per supportare gRPC-Web insieme a HTTP/2 gRPC.</span><span class="sxs-lookup"><span data-stu-id="1a38b-115">gRPC services hosted in ASP.NET Core can be configured to support gRPC-Web alongside HTTP/2 gRPC.</span></span> <span data-ttu-id="1a38b-116">gRPC-Web non richiede alcuna modifica ai servizi.</span><span class="sxs-lookup"><span data-stu-id="1a38b-116">gRPC-Web does not require any changes to services.</span></span> <span data-ttu-id="1a38b-117">L'unica modifica è la configurazione di avvio.</span><span class="sxs-lookup"><span data-stu-id="1a38b-117">The only modification is startup configuration.</span></span>

<span data-ttu-id="1a38b-118">Per abilitare gRPC-Web con un servizio gRPC ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="1a38b-118">To enable gRPC-Web with an ASP.NET Core gRPC service:</span></span>

* <span data-ttu-id="1a38b-119">Aggiungere un riferimento al pacchetto [Grpc.AspNetCore.Web.](https://www.nuget.org/packages/Grpc.AspNetCore.Web)</span><span class="sxs-lookup"><span data-stu-id="1a38b-119">Add a reference to the [Grpc.AspNetCore.Web](https://www.nuget.org/packages/Grpc.AspNetCore.Web) package.</span></span>
* <span data-ttu-id="1a38b-120">Configura l'app per l'utilizzo `AddGrpcWeb` `UseGrpcWeb` di gRPC-Web aggiungendo e *Startup.cs:*</span><span class="sxs-lookup"><span data-stu-id="1a38b-120">Configure the app to use gRPC-Web by adding `AddGrpcWeb` and `UseGrpcWeb` to *Startup.cs*:</span></span>

[!code-csharp[](~/grpc/browser/sample/Startup.cs?name=snippet_1&highlight=10,14)]

<span data-ttu-id="1a38b-121">Il codice precedente:</span><span class="sxs-lookup"><span data-stu-id="1a38b-121">The preceding code:</span></span>

* <span data-ttu-id="1a38b-122">Aggiunge il middleware gRPC-Web, `UseGrpcWeb`, dopo il routing e prima degli endpoint.</span><span class="sxs-lookup"><span data-stu-id="1a38b-122">Adds the gRPC-Web middleware, `UseGrpcWeb`, after routing and before endpoints.</span></span>
* <span data-ttu-id="1a38b-123">Specifica che `endpoints.MapGrpcService<GreeterService>()` il metodo supporta gRPC-Web con `EnableGrpcWeb`.</span><span class="sxs-lookup"><span data-stu-id="1a38b-123">Specifies the `endpoints.MapGrpcService<GreeterService>()` method supports gRPC-Web with `EnableGrpcWeb`.</span></span> 

<span data-ttu-id="1a38b-124">In alternativa, configurare tutti i servizi `services.AddGrpcWeb(o => o.GrpcWebEnabled = true);` per supportare gRPC-Web aggiungendo a ConfigureServices.</span><span class="sxs-lookup"><span data-stu-id="1a38b-124">Alternatively, configure all services to support gRPC-Web by adding `services.AddGrpcWeb(o => o.GrpcWebEnabled = true);` to ConfigureServices.</span></span>

[!code-csharp[](~/grpc/browser/sample/AllServicesSupportExample_Startup.cs?name=snippet_1&highlight=6,13)]

> [!NOTE]
> <span data-ttu-id="1a38b-125">Esiste un problema noto che causa l'esito negativo di gRPC-Web quando [ospitato da Http.sys](xref:fundamentals/servers/httpsys) in .NET Core 3.x.</span><span class="sxs-lookup"><span data-stu-id="1a38b-125">There is a known issue that causes gRPC-Web to fail when [hosted by Http.sys](xref:fundamentals/servers/httpsys) in .NET Core 3.x.</span></span>
>
> <span data-ttu-id="1a38b-126">Una soluzione alternativa per ottenere gRPC-Web lavorando su Http.sys è disponibile [qui](https://github.com/grpc/grpc-dotnet/issues/853#issuecomment-610078202).</span><span class="sxs-lookup"><span data-stu-id="1a38b-126">A workaround to get gRPC-Web working on Http.sys is available [here](https://github.com/grpc/grpc-dotnet/issues/853#issuecomment-610078202).</span></span>

### <a name="grpc-web-and-cors"></a><span data-ttu-id="1a38b-127">gRPC-Web e CORS</span><span class="sxs-lookup"><span data-stu-id="1a38b-127">gRPC-Web and CORS</span></span>

<span data-ttu-id="1a38b-128">La sicurezza del browser impedisce a una pagina Web di effettuare richieste a un dominio diverso da quello che ha servito la pagina web.</span><span class="sxs-lookup"><span data-stu-id="1a38b-128">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="1a38b-129">Questa restrizione si applica all'esecuzione di chiamate gRPC-Web con le app browser.</span><span class="sxs-lookup"><span data-stu-id="1a38b-129">This restriction applies to making gRPC-Web calls with browser apps.</span></span> <span data-ttu-id="1a38b-130">Ad esempio, a un'app browser servita da `https://www.contoso.com` viene `https://services.contoso.com`impedito di chiamare servizi Web gRPC ospitati su .</span><span class="sxs-lookup"><span data-stu-id="1a38b-130">For example, a browser app served by `https://www.contoso.com` is blocked from calling gRPC-Web services hosted on `https://services.contoso.com`.</span></span> <span data-ttu-id="1a38b-131">Condivisione tra risorse tra origini (CORS, Cross Origin Resource Sharing) può essere utilizzato per allentare questa restrizione.</span><span class="sxs-lookup"><span data-stu-id="1a38b-131">Cross Origin Resource Sharing (CORS) can be used to relax this restriction.</span></span>

<span data-ttu-id="1a38b-132">Per consentire all'app browser di effettuare chiamate gRPC-Web di origine incrociata, configura [CORS in ASP.NET Core](xref:security/cors).</span><span class="sxs-lookup"><span data-stu-id="1a38b-132">To allow your browser app to make cross-origin gRPC-Web calls, set up [CORS in ASP.NET Core](xref:security/cors).</span></span> <span data-ttu-id="1a38b-133">Utilizzare il supporto CORS incorporato ed esporre intestazioni <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>specifiche di gRPC con .</span><span class="sxs-lookup"><span data-stu-id="1a38b-133">Use the built-in CORS support, and expose gRPC-specific headers with <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>.</span></span>

[!code-csharp[](~/grpc/browser/sample/CORS_Startup.cs?name=snippet_1&highlight=5-11,19,24)]

<span data-ttu-id="1a38b-134">Il codice precedente:</span><span class="sxs-lookup"><span data-stu-id="1a38b-134">The preceding code:</span></span>

* <span data-ttu-id="1a38b-135">Chiamate `AddCors` per aggiungere servizi CORS e configura un criterio CORS che espone intestazioni specifiche gRPC.</span><span class="sxs-lookup"><span data-stu-id="1a38b-135">Calls `AddCors` to add CORS services and configures a CORS policy that exposes gRPC-specific headers.</span></span>
* <span data-ttu-id="1a38b-136">Chiamate `UseCors` per aggiungere il middleware CORS dopo il routing e prima degli endpoint.</span><span class="sxs-lookup"><span data-stu-id="1a38b-136">Calls `UseCors` to add the CORS middleware after routing and before endpoints.</span></span>
* <span data-ttu-id="1a38b-137">Specifica che `endpoints.MapGrpcService<GreeterService>()` il metodo supporta `RequiresCors`CORS con .</span><span class="sxs-lookup"><span data-stu-id="1a38b-137">Specifies the `endpoints.MapGrpcService<GreeterService>()` method supports CORS with `RequiresCors`.</span></span>

## <a name="call-grpc-web-from-the-browser"></a><span data-ttu-id="1a38b-138">Chiamare gRPC-Web dal browser</span><span class="sxs-lookup"><span data-stu-id="1a38b-138">Call gRPC-Web from the browser</span></span>

<span data-ttu-id="1a38b-139">Le app browser possono utilizzare gRPC-Web per chiamare i servizi gRPC.</span><span class="sxs-lookup"><span data-stu-id="1a38b-139">Browser apps can use gRPC-Web to call gRPC services.</span></span> <span data-ttu-id="1a38b-140">Esistono alcuni requisiti e limitazioni quando si chiamano i servizi gRPC con gRPC-Web dal browser:</span><span class="sxs-lookup"><span data-stu-id="1a38b-140">There are some requirements and limitations when calling gRPC services with gRPC-Web from the browser:</span></span>

* <span data-ttu-id="1a38b-141">Il server deve essere stato configurato per supportare gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="1a38b-141">The server must have been configured to support gRPC-Web.</span></span>
* <span data-ttu-id="1a38b-142">Lo streaming client e le chiamate di streaming bidirezionale non sono supportati.</span><span class="sxs-lookup"><span data-stu-id="1a38b-142">Client streaming and bidirectional streaming calls aren't supported.</span></span> <span data-ttu-id="1a38b-143">Lo streaming del server è supportato.</span><span class="sxs-lookup"><span data-stu-id="1a38b-143">Server streaming is supported.</span></span>
* <span data-ttu-id="1a38b-144">La chiamata ai servizi gRPC su un dominio diverso richiede la configurazione di [CORS](xref:security/cors) sul server.</span><span class="sxs-lookup"><span data-stu-id="1a38b-144">Calling gRPC services on a different domain requires [CORS](xref:security/cors) to be configured on the server.</span></span>

### <a name="javascript-grpc-web-client"></a><span data-ttu-id="1a38b-145">Client JavaScript gRPC-Web</span><span class="sxs-lookup"><span data-stu-id="1a38b-145">JavaScript gRPC-Web client</span></span>

<span data-ttu-id="1a38b-146">È disponibile un client JavaScript gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="1a38b-146">There is a JavaScript gRPC-Web client.</span></span> <span data-ttu-id="1a38b-147">Per istruzioni su come utilizzare gRPC-Web da JavaScript, consultate [Scrivere codice client JavaScript con gRPC-Web](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code).</span><span class="sxs-lookup"><span data-stu-id="1a38b-147">For instructions on how to use gRPC-Web from JavaScript, see [write JavaScript client code with gRPC-Web](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code).</span></span>

### <a name="configure-grpc-web-with-the-net-grpc-client"></a><span data-ttu-id="1a38b-148">Configurare gRPC-Web con il client gRPC .NET</span><span class="sxs-lookup"><span data-stu-id="1a38b-148">Configure gRPC-Web with the .NET gRPC client</span></span>

<span data-ttu-id="1a38b-149">Il client gRPC .NET può essere configurato per effettuare chiamate gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="1a38b-149">The .NET gRPC client can be configured to make gRPC-Web calls.</span></span> <span data-ttu-id="1a38b-150">Ciò è utile per le applicazioni [Blazor WebAssembly,](xref:blazor/index#blazor-webassembly) che sono ospitate nel browser e hanno le stesse limitazioni HTTP del codice JavaScript.</span><span class="sxs-lookup"><span data-stu-id="1a38b-150">This is useful for [Blazor WebAssembly](xref:blazor/index#blazor-webassembly) apps, which are hosted in the browser and have the same HTTP limitations of JavaScript code.</span></span> <span data-ttu-id="1a38b-151">Chiamare gRPC-Web con un client .NET equivale [a HTTP/2 gRPC](xref:grpc/client).</span><span class="sxs-lookup"><span data-stu-id="1a38b-151">Calling gRPC-Web with a .NET client is [the same as HTTP/2 gRPC](xref:grpc/client).</span></span> <span data-ttu-id="1a38b-152">L'unica modifica è la modalità di creazione del canale.</span><span class="sxs-lookup"><span data-stu-id="1a38b-152">The only modification is how the channel is created.</span></span>

<span data-ttu-id="1a38b-153">Per utilizzare gRPC-Web:</span><span class="sxs-lookup"><span data-stu-id="1a38b-153">To use gRPC-Web:</span></span>

* <span data-ttu-id="1a38b-154">Aggiungere un riferimento al pacchetto [Grpc.Net.Client.Web.](https://www.nuget.org/packages/Grpc.Net.Client.Web)</span><span class="sxs-lookup"><span data-stu-id="1a38b-154">Add a reference to the [Grpc.Net.Client.Web](https://www.nuget.org/packages/Grpc.Net.Client.Web) package.</span></span>
* <span data-ttu-id="1a38b-155">Verificare che il riferimento al pacchetto [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) sia 2.27.0 o superiore.</span><span class="sxs-lookup"><span data-stu-id="1a38b-155">Ensure the reference to [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) package is 2.27.0 or greater.</span></span>
* <span data-ttu-id="1a38b-156">Configurare il canale `GrpcWebHandler`per l'utilizzo di :</span><span class="sxs-lookup"><span data-stu-id="1a38b-156">Configure the channel to use the `GrpcWebHandler`:</span></span>

[!code-csharp[](~/grpc/browser/sample/Handler.cs?name=snippet_1)]

<span data-ttu-id="1a38b-157">Il codice precedente:</span><span class="sxs-lookup"><span data-stu-id="1a38b-157">The preceding code:</span></span>

* <span data-ttu-id="1a38b-158">Configura un canale per l'utilizzo di gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="1a38b-158">Configures a channel to use gRPC-Web.</span></span>
* <span data-ttu-id="1a38b-159">Crea un client ed effettua una chiamata utilizzando il canale.</span><span class="sxs-lookup"><span data-stu-id="1a38b-159">Creates a client and makes a call using the channel.</span></span>

<span data-ttu-id="1a38b-160">L'utente `GrpcWebHandler` ha le seguenti opzioni di configurazione al momento della creazione:</span><span class="sxs-lookup"><span data-stu-id="1a38b-160">The `GrpcWebHandler` has the following configuration options when created:</span></span>

* <span data-ttu-id="1a38b-161">**InnerHandler:** sottostante <xref:System.Net.Http.HttpMessageHandler> che effettua la richiesta HTTP `HttpClientHandler`gRPC, ad esempio .</span><span class="sxs-lookup"><span data-stu-id="1a38b-161">**InnerHandler**: The underlying <xref:System.Net.Http.HttpMessageHandler> that makes the gRPC HTTP request, for example, `HttpClientHandler`.</span></span>
* <span data-ttu-id="1a38b-162">Mode ( **Modalità**) : Tipo di `Content-Type` enumerazione che specifica se la richiesta di richiesta HTTP gRPC è `application/grpc-web` o . `application/grpc-web-text`</span><span class="sxs-lookup"><span data-stu-id="1a38b-162">**Mode**: An enumeration type that specifies whether the gRPC HTTP request request `Content-Type` is `application/grpc-web` or `application/grpc-web-text`.</span></span>
    * <span data-ttu-id="1a38b-163">`GrpcWebMode.GrpcWeb`configura l'invio del contenuto senza codifica.</span><span class="sxs-lookup"><span data-stu-id="1a38b-163">`GrpcWebMode.GrpcWeb` configures content to be sent without encoding.</span></span> <span data-ttu-id="1a38b-164">Valore predefinito.</span><span class="sxs-lookup"><span data-stu-id="1a38b-164">Default value.</span></span>
    * <span data-ttu-id="1a38b-165">`GrpcWebMode.GrpcWebText`configura il contenuto in base 64 codificato.</span><span class="sxs-lookup"><span data-stu-id="1a38b-165">`GrpcWebMode.GrpcWebText` configures content to be base64 encoded.</span></span> <span data-ttu-id="1a38b-166">Obbligatorio per le chiamate di streaming server nei browser.</span><span class="sxs-lookup"><span data-stu-id="1a38b-166">Required for server streaming calls in browsers.</span></span>
* <span data-ttu-id="1a38b-167">**HttpVersion:** protocollo `Version` HTTP utilizzato per impostare [HttpRequestMessage.Version](xref:System.Net.Http.HttpRequestMessage.Version) nella richiesta HTTP gRPC sottostante.</span><span class="sxs-lookup"><span data-stu-id="1a38b-167">**HttpVersion**: HTTP protocol `Version` used to set [HttpRequestMessage.Version](xref:System.Net.Http.HttpRequestMessage.Version) on the underlying gRPC HTTP request.</span></span> <span data-ttu-id="1a38b-168">gRPC-Web non richiede una versione specifica e non sostituisce il valore predefinito a meno che non venga specificato.</span><span class="sxs-lookup"><span data-stu-id="1a38b-168">gRPC-Web doesn't require a specific version and doesn't override the default unless specified.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="1a38b-169">I client gRPC generati dispongono di metodi sincroni e asincroni per la chiamata di metodi unario.</span><span class="sxs-lookup"><span data-stu-id="1a38b-169">Generated gRPC clients have sync and async methods for calling unary methods.</span></span> <span data-ttu-id="1a38b-170">Ad esempio, `SayHello` è `SayHelloAsync` la sincronizzazione ed è asincrona.</span><span class="sxs-lookup"><span data-stu-id="1a38b-170">For example, `SayHello` is sync and `SayHelloAsync` is async.</span></span> <span data-ttu-id="1a38b-171">La chiamata di un metodo di sincronizzazione in un'applicazione Blazor WebAssembly causerà l'app non risponde.</span><span class="sxs-lookup"><span data-stu-id="1a38b-171">Calling a sync method in a Blazor WebAssembly app will cause the app to become unresponsive.</span></span> <span data-ttu-id="1a38b-172">I metodi asincroni devono essere sempre utilizzati in Blazor WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="1a38b-172">Async methods must always be used in Blazor WebAssembly.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1a38b-173">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="1a38b-173">Additional resources</span></span>

* [<span data-ttu-id="1a38b-174">progetto gRPC per client Web GitHub</span><span class="sxs-lookup"><span data-stu-id="1a38b-174">gRPC for Web Clients GitHub project</span></span>](https://github.com/grpc/grpc-web)
* <xref:security/cors>
