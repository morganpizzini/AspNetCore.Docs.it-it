---
title: Operazioni di richiesta e risposta in ASP.NET Core
author: jkotalik
description: Informazioni su come leggere il corpo della richiesta e scrivere il corpo della risposta in ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: jukotali
ms.custom: mvc
ms.date: 5/29/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/middleware/request-response
ms.openlocfilehash: b6fc7a115cb0f4696d10bf036eadb59028dfb605
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/26/2020
ms.locfileid: "85404132"
---
# <a name="request-and-response-operations-in-aspnet-core"></a><span data-ttu-id="0d2a9-103">Operazioni di richiesta e risposta in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0d2a9-103">Request and response operations in ASP.NET Core</span></span>

<span data-ttu-id="0d2a9-104">Di [Justin Kotalik](https://github.com/jkotalik)</span><span class="sxs-lookup"><span data-stu-id="0d2a9-104">By [Justin Kotalik](https://github.com/jkotalik)</span></span>

<span data-ttu-id="0d2a9-105">Questo articolo illustra come leggere dal corpo della richiesta e scrivere nel corpo della risposta.</span><span class="sxs-lookup"><span data-stu-id="0d2a9-105">This article explains how to read from the request body and write to the response body.</span></span> <span data-ttu-id="0d2a9-106">Il codice per queste operazioni potrebbe essere necessario quando si scrive il middleware.</span><span class="sxs-lookup"><span data-stu-id="0d2a9-106">Code for these operations might be required when writing middleware.</span></span> <span data-ttu-id="0d2a9-107">Al di fuori della scrittura del middleware, il codice personalizzato non è in genere necessario perché le operazioni vengono gestite da MVC e dalle Razor pagine.</span><span class="sxs-lookup"><span data-stu-id="0d2a9-107">Outside of writing middleware, custom code isn't generally required because the operations are handled by MVC and Razor Pages.</span></span>

<span data-ttu-id="0d2a9-108">Esistono due astrazioni per i corpi di richiesta e risposta: <xref:System.IO.Stream> e <xref:System.IO.Pipelines.Pipe> .</span><span class="sxs-lookup"><span data-stu-id="0d2a9-108">There are two abstractions for the request and response bodies: <xref:System.IO.Stream> and <xref:System.IO.Pipelines.Pipe>.</span></span> <span data-ttu-id="0d2a9-109">Per la lettura della richiesta, <xref:Microsoft.AspNetCore.Http.HttpRequest.Body?displayProperty=nameWithType> è un oggetto <xref:System.IO.Stream> e `HttpRequest.BodyReader` è un oggetto <xref:System.IO.Pipelines.PipeReader> .</span><span class="sxs-lookup"><span data-stu-id="0d2a9-109">For request reading, <xref:Microsoft.AspNetCore.Http.HttpRequest.Body?displayProperty=nameWithType> is a <xref:System.IO.Stream>, and `HttpRequest.BodyReader` is a <xref:System.IO.Pipelines.PipeReader>.</span></span> <span data-ttu-id="0d2a9-110">Per la scrittura della risposta, <xref:Microsoft.AspNetCore.Http.HttpResponse.Body?displayProperty=nameWithType> è un oggetto <xref:System.IO.Stream> e `HttpResponse.BodyWriter` è un oggetto <xref:System.IO.Pipelines.PipeWriter> .</span><span class="sxs-lookup"><span data-stu-id="0d2a9-110">For response writing, <xref:Microsoft.AspNetCore.Http.HttpResponse.Body?displayProperty=nameWithType> is a <xref:System.IO.Stream>, and `HttpResponse.BodyWriter` is a <xref:System.IO.Pipelines.PipeWriter>.</span></span>

<span data-ttu-id="0d2a9-111">Le [pipeline](/dotnet/standard/io/pipelines) sono consigliate per i flussi.</span><span class="sxs-lookup"><span data-stu-id="0d2a9-111">[Pipelines](/dotnet/standard/io/pipelines) are recommended over streams.</span></span> <span data-ttu-id="0d2a9-112">I flussi possono essere più facili da usare per alcune operazioni semplici, ma le pipeline hanno prestazioni migliori e sono più facili da usare nella maggior parte degli scenari.</span><span class="sxs-lookup"><span data-stu-id="0d2a9-112">Streams can be easier to use for some simple operations, but pipelines have a performance advantage and are easier to use in most scenarios.</span></span> <span data-ttu-id="0d2a9-113">ASP.NET Core sta iniziando a usare le pipeline anziché i flussi internamente.</span><span class="sxs-lookup"><span data-stu-id="0d2a9-113">ASP.NET Core is starting to use pipelines instead of streams internally.</span></span> <span data-ttu-id="0d2a9-114">Tra gli esempi sono inclusi:</span><span class="sxs-lookup"><span data-stu-id="0d2a9-114">Examples include:</span></span>

* `FormReader`
* `TextReader`
* `TextWriter`
* `HttpResponse.WriteAsync`

<span data-ttu-id="0d2a9-115">I flussi non vengono rimossi dal Framework.</span><span class="sxs-lookup"><span data-stu-id="0d2a9-115">Streams aren't being removed from the framework.</span></span> <span data-ttu-id="0d2a9-116">I flussi continuano a essere usati in .NET e molti tipi di flusso non hanno equivalenti di pipe, ad esempio `FileStreams` e `ResponseCompression` .</span><span class="sxs-lookup"><span data-stu-id="0d2a9-116">Streams continue to be used throughout .NET, and many stream types don't have pipe equivalents, such as `FileStreams` and `ResponseCompression`.</span></span>

## <a name="stream-examples"></a><span data-ttu-id="0d2a9-117">Esempi di flussi</span><span class="sxs-lookup"><span data-stu-id="0d2a9-117">Stream examples</span></span>

<span data-ttu-id="0d2a9-118">Si supponga che l'obiettivo sia quello di creare un middleware che legga l'intero corpo della richiesta come un elenco di stringhe, suddividendo le nuove righe.</span><span class="sxs-lookup"><span data-stu-id="0d2a9-118">Suppose the goal is to create a middleware that reads the entire request body as a list of strings, splitting on new lines.</span></span> <span data-ttu-id="0d2a9-119">Un'implementazione di flusso semplice potrebbe essere simile alla seguente:</span><span class="sxs-lookup"><span data-stu-id="0d2a9-119">A simple stream implementation might look like the following example:</span></span>

> [!WARNING]
> <span data-ttu-id="0d2a9-120">Il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="0d2a9-120">The following code:</span></span>
> * <span data-ttu-id="0d2a9-121">Viene utilizzato per illustrare i problemi relativi alla mancata utilizzo di una pipe per la lettura del corpo della richiesta.</span><span class="sxs-lookup"><span data-stu-id="0d2a9-121">Is used to demonstrate the problems with not using a pipe to read the request body.</span></span>
> * <span data-ttu-id="0d2a9-122">Non può essere usato nelle app di produzione.</span><span class="sxs-lookup"><span data-stu-id="0d2a9-122">Is not intended to be used in production apps.</span></span>

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringsFromStream)]

[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="0d2a9-123">Questo codice funziona, ma esistono alcuni problemi:</span><span class="sxs-lookup"><span data-stu-id="0d2a9-123">This code works, but there are some issues:</span></span>

* <span data-ttu-id="0d2a9-124">Prima dell'aggiunta a `StringBuilder`, l'esempio crea un'altra stringa (`encodedString`) che viene eliminata immediatamente.</span><span class="sxs-lookup"><span data-stu-id="0d2a9-124">Before appending to the `StringBuilder`, the example creates another string (`encodedString`) that is thrown away immediately.</span></span> <span data-ttu-id="0d2a9-125">Questo processo si verifica per tutti i byte nel flusso, pertanto il risultato è l'allocazione di memoria aggiuntiva rispetto alle dimensioni dell'intero corpo della richiesta.</span><span class="sxs-lookup"><span data-stu-id="0d2a9-125">This process occurs for all bytes in the stream, so the result is extra memory allocation the size of the entire request body.</span></span>
* <span data-ttu-id="0d2a9-126">L'esempio legge l'intera stringa prima della suddivisione in corrispondenza delle nuove righe.</span><span class="sxs-lookup"><span data-stu-id="0d2a9-126">The example reads the entire string before splitting on new lines.</span></span> <span data-ttu-id="0d2a9-127">È più efficiente verificare la presenza di nuove righe nella matrice di byte.</span><span class="sxs-lookup"><span data-stu-id="0d2a9-127">It's more efficient to check for new lines in the byte array.</span></span>

<span data-ttu-id="0d2a9-128">Di seguito è riportato un esempio in cui vengono corretti alcuni dei problemi precedenti:</span><span class="sxs-lookup"><span data-stu-id="0d2a9-128">Here's an example that fixes some of the preceding issues:</span></span>

> [!WARNING]
> <span data-ttu-id="0d2a9-129">Il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="0d2a9-129">The following code:</span></span>
> * <span data-ttu-id="0d2a9-130">Viene usato per illustrare le soluzioni ad alcuni problemi del codice precedente, senza risolvere tutti i problemi.</span><span class="sxs-lookup"><span data-stu-id="0d2a9-130">Is used to demonstrate the solutions to some problems in the preceding code while not solving all the problems.</span></span>
> * <span data-ttu-id="0d2a9-131">Non può essere usato nelle app di produzione.</span><span class="sxs-lookup"><span data-stu-id="0d2a9-131">Is not intended to be used in production apps.</span></span>

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringsFromStreamMoreEfficient)]

<span data-ttu-id="0d2a9-132">Questo esempio precedente:</span><span class="sxs-lookup"><span data-stu-id="0d2a9-132">This preceding example:</span></span>

* <span data-ttu-id="0d2a9-133">Non memorizza nel buffer l'intero corpo della richiesta in un `StringBuilder`, a meno che non siano presenti caratteri di nuova riga.</span><span class="sxs-lookup"><span data-stu-id="0d2a9-133">Doesn't buffer the entire request body in a `StringBuilder` unless there aren't any newline characters.</span></span>
* <span data-ttu-id="0d2a9-134">Non chiama `Split` sulla stringa.</span><span class="sxs-lookup"><span data-stu-id="0d2a9-134">Doesn't call `Split` on the string.</span></span>

<span data-ttu-id="0d2a9-135">Tuttavia, esistono ancora sono alcuni problemi:</span><span class="sxs-lookup"><span data-stu-id="0d2a9-135">However, there are still are a few issues:</span></span>

* <span data-ttu-id="0d2a9-136">Se i caratteri di nuova riga sono di tipo sparse, gran parte del corpo della richiesta viene memorizzato nel buffer nella stringa.</span><span class="sxs-lookup"><span data-stu-id="0d2a9-136">If newline characters are sparse, much of the request body is buffered in the string.</span></span>
* <span data-ttu-id="0d2a9-137">Il codice continua a creare stringhe ( `remainingString` ) e le aggiunge al buffer di stringa, il che comporta un'allocazione aggiuntiva.</span><span class="sxs-lookup"><span data-stu-id="0d2a9-137">The code continues to create strings (`remainingString`) and adds them to the string buffer, which results in an extra allocation.</span></span>

<span data-ttu-id="0d2a9-138">Questi problemi sono risolvibili, ma il codice sta diventando progressivamente più complicato con un lieve miglioramento.</span><span class="sxs-lookup"><span data-stu-id="0d2a9-138">These issues are fixable, but the code is becoming progressively more complicated with little improvement.</span></span> <span data-ttu-id="0d2a9-139">Le pipeline consentono di risolvere questi problemi con complicazioni minime per il codice.</span><span class="sxs-lookup"><span data-stu-id="0d2a9-139">Pipelines provide a way to solve these problems with minimal code complexity.</span></span>

## <a name="pipelines"></a><span data-ttu-id="0d2a9-140">Pipelines</span><span class="sxs-lookup"><span data-stu-id="0d2a9-140">Pipelines</span></span>

<span data-ttu-id="0d2a9-141">Nell'esempio seguente viene illustrato come è possibile gestire lo stesso scenario utilizzando un [PipeReader](/dotnet/standard/io/pipelines#pipe):</span><span class="sxs-lookup"><span data-stu-id="0d2a9-141">The following example shows how the same scenario can be handled using a [PipeReader](/dotnet/standard/io/pipelines#pipe):</span></span>

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringFromPipe)]

<span data-ttu-id="0d2a9-142">Questo esempio consente di risolvere molti problemi delle implementazioni dei flussi:</span><span class="sxs-lookup"><span data-stu-id="0d2a9-142">This example fixes many issues that the streams implementations had:</span></span>

* <span data-ttu-id="0d2a9-143">Non è necessario un buffer di stringa perché gestisce i `PipeReader` byte che non sono stati usati.</span><span class="sxs-lookup"><span data-stu-id="0d2a9-143">There's no need for a string buffer because the `PipeReader` handles bytes that haven't been used.</span></span>
* <span data-ttu-id="0d2a9-144">Le stringhe codificate vengono aggiunte direttamente all'elenco di stringhe restituite.</span><span class="sxs-lookup"><span data-stu-id="0d2a9-144">Encoded strings are directly added to the list of returned strings.</span></span>
* <span data-ttu-id="0d2a9-145">A parte la `ToArray` chiamata e la memoria usata dalla stringa, la creazione di stringhe è priva di allocazione.</span><span class="sxs-lookup"><span data-stu-id="0d2a9-145">Other than the `ToArray` call, and the memory used by the string, string creation is allocation free.</span></span>

## <a name="adapters"></a><span data-ttu-id="0d2a9-146">adapter</span><span class="sxs-lookup"><span data-stu-id="0d2a9-146">Adapters</span></span>

<span data-ttu-id="0d2a9-147">Le `Body` `BodyReader` proprietà, e `BodyWriter` sono disponibili per `HttpRequest` e `HttpResponse` .</span><span class="sxs-lookup"><span data-stu-id="0d2a9-147">The `Body`, `BodyReader`, and `BodyWriter` properties are available for `HttpRequest` and `HttpResponse`.</span></span> <span data-ttu-id="0d2a9-148">Quando si imposta `Body` su un flusso diverso, un nuovo set di adapter adatta automaticamente ogni tipo all'altro.</span><span class="sxs-lookup"><span data-stu-id="0d2a9-148">When you set `Body` to a different stream, a new set of adapters automatically adapt each type to the other.</span></span> <span data-ttu-id="0d2a9-149">Se si imposta `HttpRequest.Body` su un nuovo flusso, `HttpRequest.BodyReader` viene impostato automaticamente su un nuovo oggetto `PipeReader` che esegue il wrapping `HttpRequest.Body` .</span><span class="sxs-lookup"><span data-stu-id="0d2a9-149">If you set `HttpRequest.Body` to a new stream, `HttpRequest.BodyReader` is automatically set to a new `PipeReader` that wraps `HttpRequest.Body`.</span></span>

## <a name="startasync"></a><span data-ttu-id="0d2a9-150">StartAsync</span><span class="sxs-lookup"><span data-stu-id="0d2a9-150">StartAsync</span></span>

<span data-ttu-id="0d2a9-151">`HttpResponse.StartAsync`viene utilizzato per indicare che le intestazioni non sono modificabili e per eseguire i `OnStarting` callback.</span><span class="sxs-lookup"><span data-stu-id="0d2a9-151">`HttpResponse.StartAsync` is used to indicate that headers are unmodifiable and to run `OnStarting` callbacks.</span></span> <span data-ttu-id="0d2a9-152">Quando si usa gheppio come server, `StartAsync` la chiamata di prima di usare `PipeReader` garantisce che la memoria restituita da `GetMemory` appartenga a un buffer interno di Gheppio <xref:System.IO.Pipelines.Pipe> anziché a un buffer esterno.</span><span class="sxs-lookup"><span data-stu-id="0d2a9-152">When using Kestrel as a server, calling `StartAsync` before using the `PipeReader` guarantees that memory returned by `GetMemory` belongs to Kestrel's internal <xref:System.IO.Pipelines.Pipe> rather than an external buffer.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="0d2a9-153">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="0d2a9-153">Additional resources</span></span>

* [<span data-ttu-id="0d2a9-154">System. IO. Pipelines in .NET</span><span class="sxs-lookup"><span data-stu-id="0d2a9-154">System.IO.Pipelines in .NET</span></span>](/dotnet/standard/io/pipelines)
* <xref:fundamentals/middleware/write>

<!-- Test with Postman or other tool. See image in static directory. -->
