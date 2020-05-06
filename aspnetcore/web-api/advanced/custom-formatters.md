---
title: Formattatori personalizzati nell'API Web ASP.NET Core
author: rick-anderson
description: Informazioni su come creare e usare i formattatori personalizzati nelle API Web ASP.NET Core.
ms.author: riande
ms.date: 02/08/2017
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: web-api/advanced/custom-formatters
ms.openlocfilehash: 0836fc288a015adb9a6223c5a2b681b1b03bded4
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82777319"
---
# <a name="custom-formatters-in-aspnet-core-web-api"></a><span data-ttu-id="063aa-103">Formattatori personalizzati nell'API Web ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="063aa-103">Custom formatters in ASP.NET Core Web API</span></span>

<span data-ttu-id="063aa-104">[Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="063aa-104">By [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="063aa-105">ASP.NET Core MVC supporta lo scambio di dati in API Web mediante formattatori di input e di output.</span><span class="sxs-lookup"><span data-stu-id="063aa-105">ASP.NET Core MVC supports data exchange in Web APIs using input and output formatters.</span></span> <span data-ttu-id="063aa-106">I formattatori di input vengono utilizzati dall' [associazione di modelli](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="063aa-106">Input formatters are used by [Model Binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="063aa-107">I formattatori di output vengono utilizzati per [formattare le risposte](xref:web-api/advanced/formatting).</span><span class="sxs-lookup"><span data-stu-id="063aa-107">Output formatters are used to [format responses](xref:web-api/advanced/formatting).</span></span>

<span data-ttu-id="063aa-108">Il Framework fornisce formattatori di input e output predefiniti per JSON e XML.</span><span class="sxs-lookup"><span data-stu-id="063aa-108">The framework provides built-in input and output formatters for JSON and XML.</span></span> <span data-ttu-id="063aa-109">Fornisce un formattatore di output incorporato per il testo normale, ma non fornisce un formattatore di input per testo normale.</span><span class="sxs-lookup"><span data-stu-id="063aa-109">It provides a built-in output formatter for plain text, but doesn't provide an input formatter for plain text.</span></span>

<span data-ttu-id="063aa-110">In questo articolo viene illustrato come aggiungere supporto per altri formati creando formattatori personalizzati.</span><span class="sxs-lookup"><span data-stu-id="063aa-110">This article shows how to add support for additional formats by creating custom formatters.</span></span> <span data-ttu-id="063aa-111">Per un esempio di formattatore di input personalizzato per testo normale, vedere [TextPlainInputFormatter](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.Formatters/TextPlainInputFormatter.cs) su GitHub.</span><span class="sxs-lookup"><span data-stu-id="063aa-111">For an example of a custom input formatter for plain text, see [TextPlainInputFormatter](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.Formatters/TextPlainInputFormatter.cs) on GitHub.</span></span>

<span data-ttu-id="063aa-112">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="063aa-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-custom-formatters"></a><span data-ttu-id="063aa-113">Quando usare i formattatori personalizzati</span><span class="sxs-lookup"><span data-stu-id="063aa-113">When to use custom formatters</span></span>

<span data-ttu-id="063aa-114">Utilizzare un formattatore personalizzato quando si desidera che il processo di [negoziazione del contenuto](xref:web-api/advanced/formatting#content-negotiation) supporti un tipo di contenuto non supportato dai formattatori predefiniti.</span><span class="sxs-lookup"><span data-stu-id="063aa-114">Use a custom formatter when you want the [content negotiation](xref:web-api/advanced/formatting#content-negotiation) process to support a content type that isn't supported by the built-in formatters.</span></span>

<span data-ttu-id="063aa-115">Ad esempio, se alcuni dei client per l'API Web gestiscono il formato [Protobuf](https://github.com/google/protobuf), si potrebbe voler usare Protobuf con tali client per una maggiore efficienza.</span><span class="sxs-lookup"><span data-stu-id="063aa-115">For example, if some of the clients for your web API can handle the [Protobuf](https://github.com/google/protobuf) format, you might want to use Protobuf with those clients because it's more efficient.</span></span> <span data-ttu-id="063aa-116">Oppure potrebbe essere necessario che l'API Web invii nomi di contatto e indirizzi nel formato [vCard](https://wikipedia.org/wiki/VCard), un formato usato comunemente per lo scambio di dati di contatto.</span><span class="sxs-lookup"><span data-stu-id="063aa-116">Or you might want your web API to send contact names and addresses in [vCard](https://wikipedia.org/wiki/VCard) format, a commonly used format for exchanging contact data.</span></span> <span data-ttu-id="063aa-117">L'app di esempio disponibile in questo articolo implementa un semplice formattatore vCard.</span><span class="sxs-lookup"><span data-stu-id="063aa-117">The sample app provided with this article implements a simple vCard formatter.</span></span>

## <a name="overview-of-how-to-use-a-custom-formatter"></a><span data-ttu-id="063aa-118">Panoramica sull'uso di un formattatore personalizzato</span><span class="sxs-lookup"><span data-stu-id="063aa-118">Overview of how to use a custom formatter</span></span>

<span data-ttu-id="063aa-119">Di seguito sono elencati i passaggi per creare e usare un formattatore personalizzato:</span><span class="sxs-lookup"><span data-stu-id="063aa-119">Here are the steps to create and use a custom formatter:</span></span>

* <span data-ttu-id="063aa-120">Creare una classe formattatore di output se si vogliono serializzare i dati da inviare al client.</span><span class="sxs-lookup"><span data-stu-id="063aa-120">Create an output formatter class if you want to serialize data to send to the client.</span></span>
* <span data-ttu-id="063aa-121">Creare una classe formattatore di input se si vogliono deserializzare i dati ricevuti dal client.</span><span class="sxs-lookup"><span data-stu-id="063aa-121">Create an input formatter class if you want to deserialize data received from the client.</span></span>
* <span data-ttu-id="063aa-122">Aggiungere le istanze dei formattatori alle raccolte `InputFormatters` e `OutputFormatters` in [MvcOptions](/dotnet/api/microsoft.aspnetcore.mvc.mvcoptions).</span><span class="sxs-lookup"><span data-stu-id="063aa-122">Add instances of your formatters to the `InputFormatters` and `OutputFormatters` collections in [MvcOptions](/dotnet/api/microsoft.aspnetcore.mvc.mvcoptions).</span></span>

<span data-ttu-id="063aa-123">Nelle sezioni seguenti sono disponibili indicazioni ed esempi di codici per ognuno dei passaggi specificati.</span><span class="sxs-lookup"><span data-stu-id="063aa-123">The following sections provide guidance and code examples for each of these steps.</span></span>

## <a name="how-to-create-a-custom-formatter-class"></a><span data-ttu-id="063aa-124">Come creare una classe formattatore personalizzato</span><span class="sxs-lookup"><span data-stu-id="063aa-124">How to create a custom formatter class</span></span>

<span data-ttu-id="063aa-125">Per creare un formattatore:</span><span class="sxs-lookup"><span data-stu-id="063aa-125">To create a formatter:</span></span>

* <span data-ttu-id="063aa-126">Derivare la classe dalla classe di base appropriata.</span><span class="sxs-lookup"><span data-stu-id="063aa-126">Derive the class from the appropriate base class.</span></span>
* <span data-ttu-id="063aa-127">Specificare i formati di testo multimediali validi e le codifiche nel costruttore.</span><span class="sxs-lookup"><span data-stu-id="063aa-127">Specify valid media types and encodings in the constructor.</span></span>
* <span data-ttu-id="063aa-128">Eseguire l'override dei metodi `CanReadType`/`CanWriteType`</span><span class="sxs-lookup"><span data-stu-id="063aa-128">Override `CanReadType`/`CanWriteType` methods</span></span>
* <span data-ttu-id="063aa-129">Eseguire l'override dei metodi `ReadRequestBodyAsync`/`WriteResponseBodyAsync`</span><span class="sxs-lookup"><span data-stu-id="063aa-129">Override `ReadRequestBodyAsync`/`WriteResponseBodyAsync` methods</span></span>
  
### <a name="derive-from-the-appropriate-base-class"></a><span data-ttu-id="063aa-130">Derivare dalla classe di base appropriata</span><span class="sxs-lookup"><span data-stu-id="063aa-130">Derive from the appropriate base class</span></span>

<span data-ttu-id="063aa-131">Per i formati multimediali di testo (ad esempio, vCard), derivare dalla classe [TextInputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.textinputformatter) o [TextOutputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.textoutputformatter).</span><span class="sxs-lookup"><span data-stu-id="063aa-131">For text media types (for example, vCard), derive from the [TextInputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.textinputformatter) or [TextOutputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.textoutputformatter) base class.</span></span>

[!code-csharp[](custom-formatters/sample/Formatters/VcardOutputFormatter.cs?name=classdef)]

<span data-ttu-id="063aa-132">Per un esempio di formattatore di input, vedere l'[app di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample).</span><span class="sxs-lookup"><span data-stu-id="063aa-132">For an input formatter example, see the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample).</span></span>

<span data-ttu-id="063aa-133">Per i tipi binari, derivare dalla classe di base [InputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.inputformatter) o [OutputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.outputformatter).</span><span class="sxs-lookup"><span data-stu-id="063aa-133">For binary types, derive from the [InputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.inputformatter) or [OutputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.outputformatter) base class.</span></span>

### <a name="specify-valid-media-types-and-encodings"></a><span data-ttu-id="063aa-134">Specificare i formati multimediali validi e le codifiche</span><span class="sxs-lookup"><span data-stu-id="063aa-134">Specify valid media types and encodings</span></span>

<span data-ttu-id="063aa-135">Nel costruttore specificare i formati multimediali validi e le codifiche aggiungendo alle raccolte `SupportedMediaTypes` e `SupportedEncodings`.</span><span class="sxs-lookup"><span data-stu-id="063aa-135">In the constructor, specify valid media types and encodings by adding to the `SupportedMediaTypes` and `SupportedEncodings` collections.</span></span>

[!code-csharp[](custom-formatters/sample/Formatters/VcardOutputFormatter.cs?name=ctor&highlight=3,5-6)]

<span data-ttu-id="063aa-136">Per un esempio di formattatore di input, vedere l'[app di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample).</span><span class="sxs-lookup"><span data-stu-id="063aa-136">For an input formatter example, see the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample).</span></span>

> [!NOTE]
> <span data-ttu-id="063aa-137">In una classe formattatore non è possibile inserire le dipendenze del costruttore.</span><span class="sxs-lookup"><span data-stu-id="063aa-137">You can't do constructor dependency injection in a formatter class.</span></span> <span data-ttu-id="063aa-138">Ad esempio, non è possibile ottenere un logger aggiungendo un parametro di logger al costruttore.</span><span class="sxs-lookup"><span data-stu-id="063aa-138">For example, you can't get a logger by adding a logger parameter to the constructor.</span></span> <span data-ttu-id="063aa-139">Per accedere ai servizi, usare l'oggetto di contesto che viene passato ai metodi.</span><span class="sxs-lookup"><span data-stu-id="063aa-139">To access services, you have to use the context object that gets passed in to your methods.</span></span> <span data-ttu-id="063aa-140">Nell'esempio di codice riportato [di seguito](#read-write) viene illustrato come eseguire tale operazione.</span><span class="sxs-lookup"><span data-stu-id="063aa-140">A code example [below](#read-write) shows how to do this.</span></span>

### <a name="override-canreadtypecanwritetype"></a><span data-ttu-id="063aa-141">Eseguire l'override di CanReadType/CanWriteType</span><span class="sxs-lookup"><span data-stu-id="063aa-141">Override CanReadType/CanWriteType</span></span>

<span data-ttu-id="063aa-142">Eseguire l'override dei metodi `CanReadType` o `CanWriteType` per specificare il tipo in cui eseguire la deserializzazione o il tipo da cui eseguire la serializzazione.</span><span class="sxs-lookup"><span data-stu-id="063aa-142">Specify the type you can deserialize into or serialize from by overriding the `CanReadType` or `CanWriteType` methods.</span></span> <span data-ttu-id="063aa-143">Ad esempio, potrebbe essere possibile creare solo un testo vCard da un tipo `Contact` e viceversa.</span><span class="sxs-lookup"><span data-stu-id="063aa-143">For example, you might only be able to create vCard text from a `Contact` type and vice versa.</span></span>

[!code-csharp[](custom-formatters/sample/Formatters/VcardOutputFormatter.cs?name=canwritetype)]

<span data-ttu-id="063aa-144">Per un esempio di formattatore di input, vedere l'[app di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample).</span><span class="sxs-lookup"><span data-stu-id="063aa-144">For an input formatter example, see the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample).</span></span>

#### <a name="the-canwriteresult-method"></a><span data-ttu-id="063aa-145">Metodo CanWriteResult</span><span class="sxs-lookup"><span data-stu-id="063aa-145">The CanWriteResult method</span></span>

<span data-ttu-id="063aa-146">In alcuni scenari è necessario eseguire l'override di `CanWriteResult` anziché di `CanWriteType`.</span><span class="sxs-lookup"><span data-stu-id="063aa-146">In some scenarios you have to override `CanWriteResult` instead of `CanWriteType`.</span></span> <span data-ttu-id="063aa-147">Usare `CanWriteResult` se vengono soddisfatte le condizioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="063aa-147">Use `CanWriteResult` if the following conditions are true:</span></span>

* <span data-ttu-id="063aa-148">Il metodo di azione restituisce una classe modello.</span><span class="sxs-lookup"><span data-stu-id="063aa-148">Your action method returns a model class.</span></span>
* <span data-ttu-id="063aa-149">Sono disponibili classi derivate che possono essere restituite in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="063aa-149">There are derived classes which might be returned at runtime.</span></span>
* <span data-ttu-id="063aa-150">In fase di esecuzione è necessario conoscere quale classe derivata è stata restituita dall'azione.</span><span class="sxs-lookup"><span data-stu-id="063aa-150">You need to know at runtime which derived class was returned by the action.</span></span>

<span data-ttu-id="063aa-151">Ad esempio, si supponga che la firma del metodo di azione restituisca un tipo `Person`, ma può restituire un tipo `Student` o `Instructor` che deriva da `Person`.</span><span class="sxs-lookup"><span data-stu-id="063aa-151">For example, suppose your action method signature returns a `Person` type, but it may return a `Student` or `Instructor` type that derives from `Person`.</span></span> <span data-ttu-id="063aa-152">Se il formattatore deve gestire solo oggetti `Student`, controllare il tipo di [oggetto](/dotnet/api/microsoft.aspnetcore.mvc.formatters.outputformattercanwritecontext.object#Microsoft_AspNetCore_Mvc_Formatters_OutputFormatterCanWriteContext_Object) nell'oggetto di contesto specificato per il metodo `CanWriteResult`.</span><span class="sxs-lookup"><span data-stu-id="063aa-152">If you want your formatter to handle only `Student` objects, check the type of [Object](/dotnet/api/microsoft.aspnetcore.mvc.formatters.outputformattercanwritecontext.object#Microsoft_AspNetCore_Mvc_Formatters_OutputFormatterCanWriteContext_Object) in the context object provided to the `CanWriteResult` method.</span></span> <span data-ttu-id="063aa-153">Si noti che non è necessario usare `CanWriteResult` quando il metodo di azione restituisce `IActionResult`; in tal caso, il metodo `CanWriteType` riceve il tipo di runtime.</span><span class="sxs-lookup"><span data-stu-id="063aa-153">Note that it's not necessary to use `CanWriteResult` when the action method returns `IActionResult`; in that case, the `CanWriteType` method receives the runtime type.</span></span>

<a id="read-write"></a>

### <a name="override-readrequestbodyasyncwriteresponsebodyasync"></a><span data-ttu-id="063aa-154">Eseguire l'override di ReadRequestBodyAsync/WriteResponseBodyAsync</span><span class="sxs-lookup"><span data-stu-id="063aa-154">Override ReadRequestBodyAsync/WriteResponseBodyAsync</span></span>

<span data-ttu-id="063aa-155">Le effettive operazioni di deserializzazione o serializzazione vengono eseguite in `ReadRequestBodyAsync` o `WriteResponseBodyAsync`.</span><span class="sxs-lookup"><span data-stu-id="063aa-155">You do the actual work of deserializing or serializing in `ReadRequestBodyAsync` or `WriteResponseBodyAsync`.</span></span> <span data-ttu-id="063aa-156">Le righe evidenziate nell'esempio seguente indicano come ottenere i servizi dal contenitore dell'inserimento delle dipendenze. Non è possibile ottenerli dai parametri del costruttore.</span><span class="sxs-lookup"><span data-stu-id="063aa-156">The highlighted lines in the following example show how to get services from the dependency injection container (you can't get them from constructor parameters).</span></span>

[!code-csharp[](custom-formatters/sample/Formatters/VcardOutputFormatter.cs?name=writeresponse&highlight=3-4)]

<span data-ttu-id="063aa-157">Per un esempio di formattatore di input, vedere l'[app di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample).</span><span class="sxs-lookup"><span data-stu-id="063aa-157">For an input formatter example, see the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample).</span></span>

## <a name="how-to-configure-mvc-to-use-a-custom-formatter"></a><span data-ttu-id="063aa-158">Come configurare MVC per usare un formattatore personalizzato</span><span class="sxs-lookup"><span data-stu-id="063aa-158">How to configure MVC to use a custom formatter</span></span>

<span data-ttu-id="063aa-159">Per usare un formattatore personalizzato, aggiungere un'istanza della classe formattatore alla raccolta `InputFormatters` o `OutputFormatters`.</span><span class="sxs-lookup"><span data-stu-id="063aa-159">To use a custom formatter, add an instance of the formatter class to the `InputFormatters` or `OutputFormatters` collection.</span></span>

[!code-csharp[](custom-formatters/sample/Startup.cs?name=mvcoptions&highlight=3-4)]

<span data-ttu-id="063aa-160">I formattatori vengono valutati nell'ordine d'inserimento.</span><span class="sxs-lookup"><span data-stu-id="063aa-160">Formatters are evaluated in the order you insert them.</span></span> <span data-ttu-id="063aa-161">Il primo ha la precedenza.</span><span class="sxs-lookup"><span data-stu-id="063aa-161">The first one takes precedence.</span></span>

## <a name="next-steps"></a><span data-ttu-id="063aa-162">Passaggi successivi</span><span class="sxs-lookup"><span data-stu-id="063aa-162">Next steps</span></span>

* <span data-ttu-id="063aa-163">[App di esempio per questo documento](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample), che implementa semplici formattatori di input e output vCard.</span><span class="sxs-lookup"><span data-stu-id="063aa-163">[Sample app for this doc](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample), which implements simple vCard input and output formatters.</span></span> <span data-ttu-id="063aa-164">L'app legge e scrive vCard simili all'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="063aa-164">The apps reads and writes vCards that look like the following example:</span></span>

```
BEGIN:VCARD
VERSION:2.1
N:Davolio;Nancy
FN:Nancy Davolio
no-loc: [Blazor, "Identity", "Let's Encrypt", Razor, SignalR]
uid:20293482-9240-4d68-b475-325df4a83728
END:VCARD
```

<span data-ttu-id="063aa-165">Per visualizzare l'output vCard, eseguire l'applicazione, inviare una richiesta Get con intestazione Accept "text/vcard" a `http://localhost:63313/api/contacts/` (se l'applicazione è eseguita da Visual Studio) o a `http://localhost:5000/api/contacts/` (se l'applicazione è eseguita dalla riga di comando).</span><span class="sxs-lookup"><span data-stu-id="063aa-165">To see vCard output, run the application and send a Get request with Accept header "text/vcard" to `http://localhost:63313/api/contacts/` (when running from Visual Studio) or `http://localhost:5000/api/contacts/` (when running from the command line).</span></span>

<span data-ttu-id="063aa-166">Per aggiungere un file vCard alla raccolta di contatti in memoria, inviare una richiesta Post allo stesso URL, con intestazione Content-Type "text/vcard" e con testo vCard nel corpo, formattato come illustrato nell'esempio precedente.</span><span class="sxs-lookup"><span data-stu-id="063aa-166">To add a vCard to the in-memory collection of contacts, send a Post request to the same URL, with Content-Type header "text/vcard" and with vCard text in the body, formatted like the example above.</span></span>
