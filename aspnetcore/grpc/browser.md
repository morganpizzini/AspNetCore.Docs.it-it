---
title: Usare gRPC nelle app del browser
author: jamesnk
description: Informazioni su come configurare i servizi gRPC in ASP.NET Core per essere richiamabili dalle app del browser usando gRPC-Web.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 05/26/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: grpc/browser
ms.openlocfilehash: 37932e755a0ef2149fb2336d2dcef87d3347d1a4
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/26/2020
ms.locfileid: "85404756"
---
# <a name="use-grpc-in-browser-apps"></a><span data-ttu-id="d41a6-103">Usare gRPC nelle app del browser</span><span class="sxs-lookup"><span data-stu-id="d41a6-103">Use gRPC in browser apps</span></span>

<span data-ttu-id="d41a6-104">Di [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="d41a6-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="d41a6-105">Non è possibile chiamare un servizio HTTP/2 gRPC da un'app basata su browser.</span><span class="sxs-lookup"><span data-stu-id="d41a6-105">It is not possible to call a HTTP/2 gRPC service from a browser-based app.</span></span> <span data-ttu-id="d41a6-106">[gRPC-Web](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) è un protocollo che consente alle app e JavaScript del browser Blazor di chiamare i servizi gRPC.</span><span class="sxs-lookup"><span data-stu-id="d41a6-106">[gRPC-Web](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) is a protocol that allows browser JavaScript and Blazor apps to call gRPC services.</span></span> <span data-ttu-id="d41a6-107">Questo articolo illustra come usare gRPC-Web in .NET Core.</span><span class="sxs-lookup"><span data-stu-id="d41a6-107">This article explains how to use gRPC-Web in .NET Core.</span></span>

## <a name="grpc-web-in-aspnet-core-vs-envoy"></a><span data-ttu-id="d41a6-108">gRPC-Web in ASP.NET Core e inviato</span><span class="sxs-lookup"><span data-stu-id="d41a6-108">gRPC-Web in ASP.NET Core vs. Envoy</span></span>

<span data-ttu-id="d41a6-109">Sono disponibili due opzioni per l'aggiunta di gRPC-Web a un'app ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="d41a6-109">There are two choices for how to add gRPC-Web to an ASP.NET Core app:</span></span>

* <span data-ttu-id="d41a6-110">Supportare gRPC-Web insieme a gRPC HTTP/2 in ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d41a6-110">Support gRPC-Web alongside gRPC HTTP/2 in ASP.NET Core.</span></span> <span data-ttu-id="d41a6-111">Questa opzione utilizza il middleware fornito dal `Grpc.AspNetCore.Web` pacchetto.</span><span class="sxs-lookup"><span data-stu-id="d41a6-111">This option uses middleware provided by the `Grpc.AspNetCore.Web` package.</span></span>
* <span data-ttu-id="d41a6-112">Usare il supporto gRPC-Web del [proxy inviato](https://www.envoyproxy.io/) per tradurre GRPC-Web in gRPC http/2.</span><span class="sxs-lookup"><span data-stu-id="d41a6-112">Use the [Envoy proxy's](https://www.envoyproxy.io/) gRPC-Web support to translate gRPC-Web to gRPC HTTP/2.</span></span> <span data-ttu-id="d41a6-113">La chiamata tradotta viene quindi trasmessa nell'app ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d41a6-113">The translated call is then forwarded onto the ASP.NET Core app.</span></span>

<span data-ttu-id="d41a6-114">Ogni approccio presenta vantaggi e svantaggi.</span><span class="sxs-lookup"><span data-stu-id="d41a6-114">There are pros and cons to each approach.</span></span> <span data-ttu-id="d41a6-115">Se si usa già l'inviato come proxy nell'ambiente dell'app, potrebbe essere utile usarlo anche per fornire il supporto gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="d41a6-115">If you're already using Envoy as a proxy in your app's environment, it might make sense to also use it to provide gRPC-Web support.</span></span> <span data-ttu-id="d41a6-116">Se si vuole una soluzione semplice per gRPC-Web che richiede solo ASP.NET Core, `Grpc.AspNetCore.Web` è una scelta ottimale.</span><span class="sxs-lookup"><span data-stu-id="d41a6-116">If you want a simple solution for gRPC-Web that only requires ASP.NET Core, `Grpc.AspNetCore.Web` is a good choice.</span></span>

## <a name="configure-grpc-web-in-aspnet-core"></a><span data-ttu-id="d41a6-117">Configurare gRPC-Web in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d41a6-117">Configure gRPC-Web in ASP.NET Core</span></span>

<span data-ttu-id="d41a6-118">i servizi gRPC ospitati in ASP.NET Core possono essere configurati per supportare gRPC-Web insieme a HTTP/2 gRPC.</span><span class="sxs-lookup"><span data-stu-id="d41a6-118">gRPC services hosted in ASP.NET Core can be configured to support gRPC-Web alongside HTTP/2 gRPC.</span></span> <span data-ttu-id="d41a6-119">gRPC-Web non richiede alcuna modifica ai servizi.</span><span class="sxs-lookup"><span data-stu-id="d41a6-119">gRPC-Web does not require any changes to services.</span></span> <span data-ttu-id="d41a6-120">L'unica modifica è la configurazione di avvio.</span><span class="sxs-lookup"><span data-stu-id="d41a6-120">The only modification is startup configuration.</span></span>

<span data-ttu-id="d41a6-121">Per abilitare gRPC-Web con un servizio gRPC ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="d41a6-121">To enable gRPC-Web with an ASP.NET Core gRPC service:</span></span>

* <span data-ttu-id="d41a6-122">Aggiungere un riferimento al pacchetto [Grpc. AspNetCore. Web](https://www.nuget.org/packages/Grpc.AspNetCore.Web) .</span><span class="sxs-lookup"><span data-stu-id="d41a6-122">Add a reference to the [Grpc.AspNetCore.Web](https://www.nuget.org/packages/Grpc.AspNetCore.Web) package.</span></span>
* <span data-ttu-id="d41a6-123">Configurare l'app per l'uso di gRPC-Web aggiungendo `UseGrpcWeb` e `EnableGrpcWeb` a *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="d41a6-123">Configure the app to use gRPC-Web by adding `UseGrpcWeb` and `EnableGrpcWeb` to *Startup.cs*:</span></span>

[!code-csharp[](~/grpc/browser/sample/Startup.cs?name=snippet_1&highlight=10,14)]

<span data-ttu-id="d41a6-124">Il codice precedente:</span><span class="sxs-lookup"><span data-stu-id="d41a6-124">The preceding code:</span></span>

* <span data-ttu-id="d41a6-125">Aggiunge il middleware gRPC-Web, `UseGrpcWeb` , dopo il routing e prima degli endpoint.</span><span class="sxs-lookup"><span data-stu-id="d41a6-125">Adds the gRPC-Web middleware, `UseGrpcWeb`, after routing and before endpoints.</span></span>
* <span data-ttu-id="d41a6-126">Specifica che il `endpoints.MapGrpcService<GreeterService>()` metodo supporta gRPC-Web con `EnableGrpcWeb` .</span><span class="sxs-lookup"><span data-stu-id="d41a6-126">Specifies the `endpoints.MapGrpcService<GreeterService>()` method supports gRPC-Web with `EnableGrpcWeb`.</span></span> 

<span data-ttu-id="d41a6-127">In alternativa, è possibile configurare il middleware gRPC-Web in modo che tutti i servizi supportino gRPC-Web per impostazione predefinita e `EnableGrpcWeb` non siano necessari.</span><span class="sxs-lookup"><span data-stu-id="d41a6-127">Alternatively, the gRPC-Web middleware can be configured so all services support gRPC-Web by default and `EnableGrpcWeb` isn't required.</span></span> <span data-ttu-id="d41a6-128">Specificare `new GrpcWebOptions { DefaultEnabled = true }` quando viene aggiunto il middleware.</span><span class="sxs-lookup"><span data-stu-id="d41a6-128">Specify `new GrpcWebOptions { DefaultEnabled = true }` when the middleware is added.</span></span>

[!code-csharp[](~/grpc/browser/sample/AllServicesSupportExample_Startup.cs?name=snippet_1&highlight=12)]

> [!NOTE]
> <span data-ttu-id="d41a6-129">Esiste un problema noto che causa l'esito negativo di gRPC-Web quando è [ospitato da Http.sys](xref:fundamentals/servers/httpsys) in .NET Core 3. x.</span><span class="sxs-lookup"><span data-stu-id="d41a6-129">There is a known issue that causes gRPC-Web to fail when [hosted by Http.sys](xref:fundamentals/servers/httpsys) in .NET Core 3.x.</span></span>
>
> <span data-ttu-id="d41a6-130">Una soluzione alternativa per l'uso di gRPC-Web in Http.sys è disponibile [qui](https://github.com/grpc/grpc-dotnet/issues/853#issuecomment-610078202).</span><span class="sxs-lookup"><span data-stu-id="d41a6-130">A workaround to get gRPC-Web working on Http.sys is available [here](https://github.com/grpc/grpc-dotnet/issues/853#issuecomment-610078202).</span></span>

### <a name="grpc-web-and-cors"></a><span data-ttu-id="d41a6-131">gRPC-Web e CORS</span><span class="sxs-lookup"><span data-stu-id="d41a6-131">gRPC-Web and CORS</span></span>

<span data-ttu-id="d41a6-132">La sicurezza del browser impedisce a una pagina Web di eseguire richieste a un dominio diverso da quello che ha gestito la pagina Web.</span><span class="sxs-lookup"><span data-stu-id="d41a6-132">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="d41a6-133">Questa restrizione si applica all'esecuzione di chiamate gRPC-Web con le app browser.</span><span class="sxs-lookup"><span data-stu-id="d41a6-133">This restriction applies to making gRPC-Web calls with browser apps.</span></span> <span data-ttu-id="d41a6-134">Ad esempio, un'app browser servita da `https://www.contoso.com` viene bloccata dalla chiamata a gRPC-Web Services ospitata in `https://services.contoso.com` .</span><span class="sxs-lookup"><span data-stu-id="d41a6-134">For example, a browser app served by `https://www.contoso.com` is blocked from calling gRPC-Web services hosted on `https://services.contoso.com`.</span></span> <span data-ttu-id="d41a6-135">La condivisione di risorse tra le origini (CORS) può essere usata per attenuare questa restrizione.</span><span class="sxs-lookup"><span data-stu-id="d41a6-135">Cross Origin Resource Sharing (CORS) can be used to relax this restriction.</span></span>

<span data-ttu-id="d41a6-136">Per consentire all'app browser di eseguire chiamate web gRPC tra le origini, configurare [CORS in ASP.NET Core](xref:security/cors).</span><span class="sxs-lookup"><span data-stu-id="d41a6-136">To allow your browser app to make cross-origin gRPC-Web calls, set up [CORS in ASP.NET Core](xref:security/cors).</span></span> <span data-ttu-id="d41a6-137">Usare il supporto CORS incorporato ed esporre intestazioni specifiche di gRPC con <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*> .</span><span class="sxs-lookup"><span data-stu-id="d41a6-137">Use the built-in CORS support, and expose gRPC-specific headers with <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>.</span></span>

[!code-csharp[](~/grpc/browser/sample/CORS_Startup.cs?name=snippet_1&highlight=5-11,19,24)]

<span data-ttu-id="d41a6-138">Il codice precedente:</span><span class="sxs-lookup"><span data-stu-id="d41a6-138">The preceding code:</span></span>

* <span data-ttu-id="d41a6-139">Chiama `AddCors` per aggiungere servizi CORS e configura un criterio CORS che espone intestazioni specifiche di gRPC.</span><span class="sxs-lookup"><span data-stu-id="d41a6-139">Calls `AddCors` to add CORS services and configures a CORS policy that exposes gRPC-specific headers.</span></span>
* <span data-ttu-id="d41a6-140">Chiama `UseCors` per aggiungere il middleware CORS dopo il routing e prima degli endpoint.</span><span class="sxs-lookup"><span data-stu-id="d41a6-140">Calls `UseCors` to add the CORS middleware after routing and before endpoints.</span></span>
* <span data-ttu-id="d41a6-141">Specifica che il `endpoints.MapGrpcService<GreeterService>()` metodo supporta cors con `RequiresCors` .</span><span class="sxs-lookup"><span data-stu-id="d41a6-141">Specifies the `endpoints.MapGrpcService<GreeterService>()` method supports CORS with `RequiresCors`.</span></span>

## <a name="call-grpc-web-from-the-browser"></a><span data-ttu-id="d41a6-142">Chiamare gRPC-Web dal browser</span><span class="sxs-lookup"><span data-stu-id="d41a6-142">Call gRPC-Web from the browser</span></span>

<span data-ttu-id="d41a6-143">Le app browser possono usare gRPC-Web per chiamare i servizi gRPC.</span><span class="sxs-lookup"><span data-stu-id="d41a6-143">Browser apps can use gRPC-Web to call gRPC services.</span></span> <span data-ttu-id="d41a6-144">Esistono alcuni requisiti e limitazioni quando si chiamano i servizi gRPC con gRPC-Web dal browser:</span><span class="sxs-lookup"><span data-stu-id="d41a6-144">There are some requirements and limitations when calling gRPC services with gRPC-Web from the browser:</span></span>

* <span data-ttu-id="d41a6-145">Il server deve essere stato configurato per supportare gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="d41a6-145">The server must have been configured to support gRPC-Web.</span></span>
* <span data-ttu-id="d41a6-146">Il flusso client e le chiamate di streaming bidirezionali non sono supportate.</span><span class="sxs-lookup"><span data-stu-id="d41a6-146">Client streaming and bidirectional streaming calls aren't supported.</span></span> <span data-ttu-id="d41a6-147">Il flusso del server è supportato.</span><span class="sxs-lookup"><span data-stu-id="d41a6-147">Server streaming is supported.</span></span>
* <span data-ttu-id="d41a6-148">Per chiamare i servizi gRPC in un dominio diverso è necessario configurare [CORS](xref:security/cors) nel server.</span><span class="sxs-lookup"><span data-stu-id="d41a6-148">Calling gRPC services on a different domain requires [CORS](xref:security/cors) to be configured on the server.</span></span>

### <a name="javascript-grpc-web-client"></a><span data-ttu-id="d41a6-149">JavaScript gRPC-client Web</span><span class="sxs-lookup"><span data-stu-id="d41a6-149">JavaScript gRPC-Web client</span></span>

<span data-ttu-id="d41a6-150">È disponibile un client JavaScript gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="d41a6-150">There is a JavaScript gRPC-Web client.</span></span> <span data-ttu-id="d41a6-151">Per istruzioni su come usare gRPC-Web da JavaScript, vedere [scrivere codice client JavaScript con gRPC-Web](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code).</span><span class="sxs-lookup"><span data-stu-id="d41a6-151">For instructions on how to use gRPC-Web from JavaScript, see [write JavaScript client code with gRPC-Web](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code).</span></span>

### <a name="configure-grpc-web-with-the-net-grpc-client"></a><span data-ttu-id="d41a6-152">Configurare gRPC-Web con il client gRPC .NET</span><span class="sxs-lookup"><span data-stu-id="d41a6-152">Configure gRPC-Web with the .NET gRPC client</span></span>

<span data-ttu-id="d41a6-153">Il client gRPC .NET può essere configurato per effettuare chiamate gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="d41a6-153">The .NET gRPC client can be configured to make gRPC-Web calls.</span></span> <span data-ttu-id="d41a6-154">Questa operazione è utile per [Blazor WebAssembly](xref:blazor/index#blazor-webassembly) le app, che sono ospitate nel browser e hanno le stesse limitazioni http del codice JavaScript.</span><span class="sxs-lookup"><span data-stu-id="d41a6-154">This is useful for [Blazor WebAssembly](xref:blazor/index#blazor-webassembly) apps, which are hosted in the browser and have the same HTTP limitations of JavaScript code.</span></span> <span data-ttu-id="d41a6-155">La chiamata di gRPC-Web con un client .NET equivale a [http/2 gRPC](xref:grpc/client).</span><span class="sxs-lookup"><span data-stu-id="d41a6-155">Calling gRPC-Web with a .NET client is [the same as HTTP/2 gRPC](xref:grpc/client).</span></span> <span data-ttu-id="d41a6-156">L'unica modifica è la modalità di creazione del canale.</span><span class="sxs-lookup"><span data-stu-id="d41a6-156">The only modification is how the channel is created.</span></span>

<span data-ttu-id="d41a6-157">Per usare gRPC-Web:</span><span class="sxs-lookup"><span data-stu-id="d41a6-157">To use gRPC-Web:</span></span>

* <span data-ttu-id="d41a6-158">Aggiungere un riferimento al pacchetto [Grpc .NET. client. Web](https://www.nuget.org/packages/Grpc.Net.Client.Web) .</span><span class="sxs-lookup"><span data-stu-id="d41a6-158">Add a reference to the [Grpc.Net.Client.Web](https://www.nuget.org/packages/Grpc.Net.Client.Web) package.</span></span>
* <span data-ttu-id="d41a6-159">Verificare che il riferimento a [Grpc .NET. client](https://www.nuget.org/packages/Grpc.Net.Client) Package sia 2.29.0 o versione successiva.</span><span class="sxs-lookup"><span data-stu-id="d41a6-159">Ensure the reference to [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) package is 2.29.0 or greater.</span></span>
* <span data-ttu-id="d41a6-160">Configurare il canale per l'utilizzo di `GrpcWebHandler` :</span><span class="sxs-lookup"><span data-stu-id="d41a6-160">Configure the channel to use the `GrpcWebHandler`:</span></span>

[!code-csharp[](~/grpc/browser/sample/Handler.cs?name=snippet_1)]

<span data-ttu-id="d41a6-161">Il codice precedente:</span><span class="sxs-lookup"><span data-stu-id="d41a6-161">The preceding code:</span></span>

* <span data-ttu-id="d41a6-162">Configura un canale per l'uso di gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="d41a6-162">Configures a channel to use gRPC-Web.</span></span>
* <span data-ttu-id="d41a6-163">Crea un client ed effettua una chiamata utilizzando il canale.</span><span class="sxs-lookup"><span data-stu-id="d41a6-163">Creates a client and makes a call using the channel.</span></span>

<span data-ttu-id="d41a6-164">`GrpcWebHandler`in sono disponibili le opzioni di configurazione seguenti:</span><span class="sxs-lookup"><span data-stu-id="d41a6-164">`GrpcWebHandler` has the following configuration options:</span></span>

* <span data-ttu-id="d41a6-165">**InnerHandler**: oggetto sottostante <xref:System.Net.Http.HttpMessageHandler> che esegue la richiesta http gRPC, ad esempio `HttpClientHandler` .</span><span class="sxs-lookup"><span data-stu-id="d41a6-165">**InnerHandler**: The underlying <xref:System.Net.Http.HttpMessageHandler> that makes the gRPC HTTP request, for example, `HttpClientHandler`.</span></span>
* <span data-ttu-id="d41a6-166">**GrpcWebMode**: tipo di enumerazione che specifica se la richiesta http gRPC `Content-Type` è `application/grpc-web` o `application/grpc-web-text` .</span><span class="sxs-lookup"><span data-stu-id="d41a6-166">**GrpcWebMode**: An enumeration type that specifies whether the gRPC HTTP request `Content-Type` is `application/grpc-web` or `application/grpc-web-text`.</span></span>
    * <span data-ttu-id="d41a6-167">`GrpcWebMode.GrpcWeb`Configura il contenuto da inviare senza codifica.</span><span class="sxs-lookup"><span data-stu-id="d41a6-167">`GrpcWebMode.GrpcWeb` configures content to be sent without encoding.</span></span> <span data-ttu-id="d41a6-168">Valore predefinito.</span><span class="sxs-lookup"><span data-stu-id="d41a6-168">Default value.</span></span>
    * <span data-ttu-id="d41a6-169">`GrpcWebMode.GrpcWebText`Configura il contenuto con codifica Base64.</span><span class="sxs-lookup"><span data-stu-id="d41a6-169">`GrpcWebMode.GrpcWebText` configures content to be base64 encoded.</span></span> <span data-ttu-id="d41a6-170">Obbligatorio per le chiamate di streaming del server nei browser.</span><span class="sxs-lookup"><span data-stu-id="d41a6-170">Required for server streaming calls in browsers.</span></span>
* <span data-ttu-id="d41a6-171">**HttpVersion**: protocollo http `Version` usato per impostare [HttpRequestMessage. Version](xref:System.Net.Http.HttpRequestMessage.Version) sulla richiesta http gRPC sottostante.</span><span class="sxs-lookup"><span data-stu-id="d41a6-171">**HttpVersion**: HTTP protocol `Version` used to set [HttpRequestMessage.Version](xref:System.Net.Http.HttpRequestMessage.Version) on the underlying gRPC HTTP request.</span></span> <span data-ttu-id="d41a6-172">gRPC-Web non richiede una versione specifica e non esegue l'override dell'impostazione predefinita, a meno che non sia specificato.</span><span class="sxs-lookup"><span data-stu-id="d41a6-172">gRPC-Web doesn't require a specific version and doesn't override the default unless specified.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="d41a6-173">I client gRPC generati hanno metodi Sync e Async per la chiamata di metodi unaria.</span><span class="sxs-lookup"><span data-stu-id="d41a6-173">Generated gRPC clients have sync and async methods for calling unary methods.</span></span> <span data-ttu-id="d41a6-174">Ad esempio, `SayHello` è Sync ed `SayHelloAsync` è Async.</span><span class="sxs-lookup"><span data-stu-id="d41a6-174">For example, `SayHello` is sync and `SayHelloAsync` is async.</span></span> <span data-ttu-id="d41a6-175">La chiamata di un metodo di sincronizzazione in un'app provocherà la mancata Blazor WebAssembly risposta dell'app.</span><span class="sxs-lookup"><span data-stu-id="d41a6-175">Calling a sync method in a Blazor WebAssembly app will cause the app to become unresponsive.</span></span> <span data-ttu-id="d41a6-176">I metodi asincroni devono sempre essere utilizzati in Blazor WebAssembly .</span><span class="sxs-lookup"><span data-stu-id="d41a6-176">Async methods must always be used in Blazor WebAssembly.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d41a6-177">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="d41a6-177">Additional resources</span></span>

* [<span data-ttu-id="d41a6-178">progetto GitHub di gRPC per client Web</span><span class="sxs-lookup"><span data-stu-id="d41a6-178">gRPC for Web Clients GitHub project</span></span>](https://github.com/grpc/grpc-web)
* <xref:security/cors>
