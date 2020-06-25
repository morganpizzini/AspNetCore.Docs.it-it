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
ms.openlocfilehash: 89fbb9d52d99d0eff6656eb6a5a9b4e1c01bc65c
ms.sourcegitcommit: 1833870ad0845326fb764fef1b530a07b9b5b099
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/25/2020
ms.locfileid: "85347086"
---
# <a name="custom-formatters-in-aspnet-core-web-api"></a><span data-ttu-id="731b8-103">Formattatori personalizzati nell'API Web ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="731b8-103">Custom formatters in ASP.NET Core Web API</span></span>

<span data-ttu-id="731b8-104">Di [Kirk Larkin](https://twitter.com/serpent5) e [Tom Dykstra](https://github.com/tdykstra).</span><span class="sxs-lookup"><span data-stu-id="731b8-104">By [Kirk Larkin](https://twitter.com/serpent5) and [Tom Dykstra](https://github.com/tdykstra).</span></span>

<span data-ttu-id="731b8-105">ASP.NET Core MVC supporta lo scambio di dati in API Web mediante formattatori di input e di output.</span><span class="sxs-lookup"><span data-stu-id="731b8-105">ASP.NET Core MVC supports data exchange in Web APIs using input and output formatters.</span></span> <span data-ttu-id="731b8-106">I formattatori di input vengono utilizzati dall' [associazione di modelli](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="731b8-106">Input formatters are used by [Model Binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="731b8-107">I formattatori di output vengono utilizzati per [formattare le risposte](xref:web-api/advanced/formatting).</span><span class="sxs-lookup"><span data-stu-id="731b8-107">Output formatters are used to [format responses](xref:web-api/advanced/formatting).</span></span>

<span data-ttu-id="731b8-108">Il Framework fornisce formattatori di input e output predefiniti per JSON e XML.</span><span class="sxs-lookup"><span data-stu-id="731b8-108">The framework provides built-in input and output formatters for JSON and XML.</span></span> <span data-ttu-id="731b8-109">Fornisce un formattatore di output incorporato per il testo normale, ma non fornisce un formattatore di input per testo normale.</span><span class="sxs-lookup"><span data-stu-id="731b8-109">It provides a built-in output formatter for plain text, but doesn't provide an input formatter for plain text.</span></span>

<span data-ttu-id="731b8-110">In questo articolo viene illustrato come aggiungere supporto per altri formati creando formattatori personalizzati.</span><span class="sxs-lookup"><span data-stu-id="731b8-110">This article shows how to add support for additional formats by creating custom formatters.</span></span> <span data-ttu-id="731b8-111">Per un esempio di formattatore di input di testo normale personalizzato, vedere [TextPlainInputFormatter](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.Formatters/TextPlainInputFormatter.cs) su GitHub.</span><span class="sxs-lookup"><span data-stu-id="731b8-111">For an example of a custom plain text input formatter, see [TextPlainInputFormatter](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.Formatters/TextPlainInputFormatter.cs) on GitHub.</span></span>

<span data-ttu-id="731b8-112">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="731b8-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-custom-formatters"></a><span data-ttu-id="731b8-113">Quando usare i formattatori personalizzati</span><span class="sxs-lookup"><span data-stu-id="731b8-113">When to use custom formatters</span></span>

<span data-ttu-id="731b8-114">Usare un formattatore personalizzato per aggiungere il supporto per un tipo di contenuto non gestito dai formattatori Bult.</span><span class="sxs-lookup"><span data-stu-id="731b8-114">Use a custom formatter to add support for a content type that isn't handled by the bult-in formatters.</span></span>

## <a name="overview-of-how-to-use-a-custom-formatter"></a><span data-ttu-id="731b8-115">Panoramica sull'uso di un formattatore personalizzato</span><span class="sxs-lookup"><span data-stu-id="731b8-115">Overview of how to use a custom formatter</span></span>

<span data-ttu-id="731b8-116">Per creare un formattatore personalizzato:</span><span class="sxs-lookup"><span data-stu-id="731b8-116">To create a custom formatter:</span></span>

* <span data-ttu-id="731b8-117">Per la serializzazione dei dati inviati al client, creare una classe formattatore di output.</span><span class="sxs-lookup"><span data-stu-id="731b8-117">For serializing data sent to the client, create an output formatter class.</span></span>
* <span data-ttu-id="731b8-118">Per i dati deserialzing ricevuti dal client, creare una classe formattatore di input.</span><span class="sxs-lookup"><span data-stu-id="731b8-118">For deserialzing data received from the client, create an input formatter class.</span></span>
* <span data-ttu-id="731b8-119">Aggiungere istanze delle classi formatter alle `InputFormatters` raccolte e `OutputFormatters` in [MvcOptions](/dotnet/api/microsoft.aspnetcore.mvc.mvcoptions).</span><span class="sxs-lookup"><span data-stu-id="731b8-119">Add instances of formatter classes to the `InputFormatters` and `OutputFormatters` collections in [MvcOptions](/dotnet/api/microsoft.aspnetcore.mvc.mvcoptions).</span></span>

## <a name="how-to-create-a-custom-formatter-class"></a><span data-ttu-id="731b8-120">Come creare una classe formattatore personalizzato</span><span class="sxs-lookup"><span data-stu-id="731b8-120">How to create a custom formatter class</span></span>

<span data-ttu-id="731b8-121">Per creare un formattatore:</span><span class="sxs-lookup"><span data-stu-id="731b8-121">To create a formatter:</span></span>

* <span data-ttu-id="731b8-122">Derivare la classe dalla classe di base appropriata.</span><span class="sxs-lookup"><span data-stu-id="731b8-122">Derive the class from the appropriate base class.</span></span> <span data-ttu-id="731b8-123">L'app di esempio deriva da <xref:Microsoft.AspNetCore.Mvc.Formatters.TextOutputFormatter> e <xref:Microsoft.AspNetCore.Mvc.Formatters.TextInputFormatter> .</span><span class="sxs-lookup"><span data-stu-id="731b8-123">The sample app derives from <xref:Microsoft.AspNetCore.Mvc.Formatters.TextOutputFormatter> and <xref:Microsoft.AspNetCore.Mvc.Formatters.TextInputFormatter>.</span></span>
* <span data-ttu-id="731b8-124">Specificare i formati di testo multimediali validi e le codifiche nel costruttore.</span><span class="sxs-lookup"><span data-stu-id="731b8-124">Specify valid media types and encodings in the constructor.</span></span>
* <span data-ttu-id="731b8-125">Eseguire l'override dei metodi <xref:Microsoft.AspNetCore.Mvc.Formatters.InputFormatter.CanReadType%2A> e <xref:Microsoft.AspNetCore.Mvc.Formatters.OutputFormatter.CanWriteType%2A>.</span><span class="sxs-lookup"><span data-stu-id="731b8-125">Override the <xref:Microsoft.AspNetCore.Mvc.Formatters.InputFormatter.CanReadType%2A> and <xref:Microsoft.AspNetCore.Mvc.Formatters.OutputFormatter.CanWriteType%2A> methods.</span></span>
* <span data-ttu-id="731b8-126">Eseguire l'override dei metodi <xref:Microsoft.AspNetCore.Mvc.Formatters.InputFormatter.ReadRequestBodyAsync%2A> e `WriteResponseBodyAsync`.</span><span class="sxs-lookup"><span data-stu-id="731b8-126">Override the <xref:Microsoft.AspNetCore.Mvc.Formatters.InputFormatter.ReadRequestBodyAsync%2A> and `WriteResponseBodyAsync` methods.</span></span>

<span data-ttu-id="731b8-127">Nel codice seguente viene illustrata la `VcardOutputFormatter` classe dell' [esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/3.1sample):</span><span class="sxs-lookup"><span data-stu-id="731b8-127">The following code shows the `VcardOutputFormatter` class from the [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/3.1sample):</span></span>

[!code-csharp[](custom-formatters/3.1sample/Formatters/VcardOutputFormatter.cs?name=snippet)]
  
### <a name="derive-from-the-appropriate-base-class"></a><span data-ttu-id="731b8-128">Derivare dalla classe di base appropriata</span><span class="sxs-lookup"><span data-stu-id="731b8-128">Derive from the appropriate base class</span></span>

<span data-ttu-id="731b8-129">Per i formati multimediali di testo (ad esempio, vCard), derivare dalla classe [TextInputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.textinputformatter) o [TextOutputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.textoutputformatter).</span><span class="sxs-lookup"><span data-stu-id="731b8-129">For text media types (for example, vCard), derive from the [TextInputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.textinputformatter) or [TextOutputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.textoutputformatter) base class.</span></span>

[!code-csharp[](custom-formatters/3.1sample/Formatters/VcardOutputFormatter.cs?name=classdef)]

<span data-ttu-id="731b8-130">Per i tipi binari, derivare dalla classe di base [InputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.inputformatter) o [OutputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.outputformatter).</span><span class="sxs-lookup"><span data-stu-id="731b8-130">For binary types, derive from the [InputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.inputformatter) or [OutputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.outputformatter) base class.</span></span>

### <a name="specify-valid-media-types-and-encodings"></a><span data-ttu-id="731b8-131">Specificare i formati multimediali validi e le codifiche</span><span class="sxs-lookup"><span data-stu-id="731b8-131">Specify valid media types and encodings</span></span>

<span data-ttu-id="731b8-132">Nel costruttore specificare i formati multimediali validi e le codifiche aggiungendo alle raccolte `SupportedMediaTypes` e `SupportedEncodings`.</span><span class="sxs-lookup"><span data-stu-id="731b8-132">In the constructor, specify valid media types and encodings by adding to the `SupportedMediaTypes` and `SupportedEncodings` collections.</span></span>

[!code-csharp[](custom-formatters/3.1sample/Formatters/VcardOutputFormatter.cs?name=ctor)]

<span data-ttu-id="731b8-133">Una classe formatter **non** può utilizzare l'inserimento del costruttore per le relative dipendenze.</span><span class="sxs-lookup"><span data-stu-id="731b8-133">A formatter class can **not** use constructor injection for its dependencies.</span></span> <span data-ttu-id="731b8-134">Ad esempio, `ILogger<VcardOutputFormatter>` non può essere aggiunto come parametro al costruttore.</span><span class="sxs-lookup"><span data-stu-id="731b8-134">For example, `ILogger<VcardOutputFormatter>` cannot be added as a parameter to the constructor.</span></span> <span data-ttu-id="731b8-135">Per accedere ai servizi, usare l'oggetto di contesto che viene passato ai metodi.</span><span class="sxs-lookup"><span data-stu-id="731b8-135">To access services, use the context object that gets passed in to the methods.</span></span> <span data-ttu-id="731b8-136">Un esempio di codice in questo articolo e nell' [esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/3.1sample) illustra come eseguire questa operazione.</span><span class="sxs-lookup"><span data-stu-id="731b8-136">A code example in this article and the [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/3.1sample) show how to do this.</span></span>

### <a name="override-canreadtype-and-canwritetype"></a><span data-ttu-id="731b8-137">Eseguire l'override di CanReadType e CanWriteType</span><span class="sxs-lookup"><span data-stu-id="731b8-137">Override CanReadType and CanWriteType</span></span>

<span data-ttu-id="731b8-138">Specificare il tipo in cui eseguire la deserializzazione o serializzare eseguendo l'override dei `CanReadType` `CanWriteType` metodi o.</span><span class="sxs-lookup"><span data-stu-id="731b8-138">Specify the type to deserialize into or serialize from by overriding the `CanReadType` or `CanWriteType` methods.</span></span> <span data-ttu-id="731b8-139">Ad esempio, la creazione di testo vCard da un `Contact` tipo e viceversa.</span><span class="sxs-lookup"><span data-stu-id="731b8-139">For example, creating vCard text from a `Contact` type and vice versa.</span></span>

[!code-csharp[](custom-formatters/3.1sample/Formatters/VcardOutputFormatter.cs?name=canwritetype)]

#### <a name="the-canwriteresult-method"></a><span data-ttu-id="731b8-140">Metodo CanWriteResult</span><span class="sxs-lookup"><span data-stu-id="731b8-140">The CanWriteResult method</span></span>

<span data-ttu-id="731b8-141">In alcuni scenari `CanWriteResult` è necessario eseguire l'override di anziché `CanWriteType` .</span><span class="sxs-lookup"><span data-stu-id="731b8-141">In some scenarios, `CanWriteResult` must be overridden rather than `CanWriteType`.</span></span> <span data-ttu-id="731b8-142">Usare `CanWriteResult` se vengono soddisfatte le condizioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="731b8-142">Use `CanWriteResult` if the following conditions are true:</span></span>

* <span data-ttu-id="731b8-143">Il metodo di azione restituisce una classe di modello.</span><span class="sxs-lookup"><span data-stu-id="731b8-143">The action method returns a model class.</span></span>
* <span data-ttu-id="731b8-144">Sono disponibili classi derivate che possono essere restituite in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="731b8-144">There are derived classes which might be returned at runtime.</span></span>
* <span data-ttu-id="731b8-145">La classe derivata restituita dall'azione deve essere nota in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="731b8-145">The derived class returned by the action must be known at runtime.</span></span>

<span data-ttu-id="731b8-146">Si supponga, ad esempio, che il metodo di azione:</span><span class="sxs-lookup"><span data-stu-id="731b8-146">For example, suppose the action method:</span></span>

* <span data-ttu-id="731b8-147">La firma restituisce un `Person` tipo.</span><span class="sxs-lookup"><span data-stu-id="731b8-147">Signature returns a `Person` type.</span></span>
* <span data-ttu-id="731b8-148">Può restituire un `Student` `Instructor` tipo o che deriva da `Person` .</span><span class="sxs-lookup"><span data-stu-id="731b8-148">Can return a `Student` or `Instructor` type that derives from `Person`.</span></span> 

<span data-ttu-id="731b8-149">Affinché il formattatore gestisca solo `Student` oggetti, controllare il tipo di [oggetto](/dotnet/api/microsoft.aspnetcore.mvc.formatters.outputformattercanwritecontext.object#Microsoft_AspNetCore_Mvc_Formatters_OutputFormatterCanWriteContext_Object) nell'oggetto di contesto fornito al `CanWriteResult` metodo.</span><span class="sxs-lookup"><span data-stu-id="731b8-149">For the formatter to handle only `Student` objects, check the type of [Object](/dotnet/api/microsoft.aspnetcore.mvc.formatters.outputformattercanwritecontext.object#Microsoft_AspNetCore_Mvc_Formatters_OutputFormatterCanWriteContext_Object) in the context object provided to the `CanWriteResult` method.</span></span> <span data-ttu-id="731b8-150">Quando il metodo di azione restituisce `IActionResult` :</span><span class="sxs-lookup"><span data-stu-id="731b8-150">When the action method returns `IActionResult`:</span></span>

* <span data-ttu-id="731b8-151">Non è necessario usare `CanWriteResult` .</span><span class="sxs-lookup"><span data-stu-id="731b8-151">It's not necessary to use `CanWriteResult`.</span></span>
* <span data-ttu-id="731b8-152">Il `CanWriteType` metodo riceve il tipo di Runtime.</span><span class="sxs-lookup"><span data-stu-id="731b8-152">The `CanWriteType` method receives the runtime type.</span></span>

<a id="read-write"></a>

### <a name="override-readrequestbodyasync-and-writeresponsebodyasync"></a><span data-ttu-id="731b8-153">Eseguire l'override di ReadRequestBodyAsync e WriteResponseBodyAsync</span><span class="sxs-lookup"><span data-stu-id="731b8-153">Override ReadRequestBodyAsync and WriteResponseBodyAsync</span></span>

<span data-ttu-id="731b8-154">La deserializzazione o la serializzazione viene eseguita in `ReadRequestBodyAsync` o `WriteResponseBodyAsync` .</span><span class="sxs-lookup"><span data-stu-id="731b8-154">Deserialization or serialization is performed in `ReadRequestBodyAsync` or `WriteResponseBodyAsync`.</span></span> <span data-ttu-id="731b8-155">Nell'esempio seguente viene illustrato come ottenere servizi dal contenitore di inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="731b8-155">The following example shows how to get services from the dependency injection container.</span></span> <span data-ttu-id="731b8-156">Non è possibile ottenere i servizi dai parametri del costruttore.</span><span class="sxs-lookup"><span data-stu-id="731b8-156">Services can't be obtained from constructor parameters.</span></span>

[!code-csharp[](custom-formatters/3.1sample/Formatters/VcardOutputFormatter.cs?name=writeresponse)]

## <a name="how-to-configure-mvc-to-use-a-custom-formatter"></a><span data-ttu-id="731b8-157">Come configurare MVC per usare un formattatore personalizzato</span><span class="sxs-lookup"><span data-stu-id="731b8-157">How to configure MVC to use a custom formatter</span></span>

<span data-ttu-id="731b8-158">Per usare un formattatore personalizzato, aggiungere un'istanza della classe formattatore alla raccolta `InputFormatters` o `OutputFormatters`.</span><span class="sxs-lookup"><span data-stu-id="731b8-158">To use a custom formatter, add an instance of the formatter class to the `InputFormatters` or `OutputFormatters` collection.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](custom-formatters/3.1sample/Startup.cs?name=mvcoptions)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](custom-formatters/sample/Startup.cs?name=mvcoptions&highlight=3-4)]

::: moniker-end

<span data-ttu-id="731b8-159">I formattatori vengono valutati nell'ordine d'inserimento.</span><span class="sxs-lookup"><span data-stu-id="731b8-159">Formatters are evaluated in the order you insert them.</span></span> <span data-ttu-id="731b8-160">Il primo ha la precedenza.</span><span class="sxs-lookup"><span data-stu-id="731b8-160">The first one takes precedence.</span></span>

## <a name="the-completed-vcardinputformatter-class"></a><span data-ttu-id="731b8-161">Classe completata `VcardInputFormatter`</span><span class="sxs-lookup"><span data-stu-id="731b8-161">The completed `VcardInputFormatter` class</span></span>

<span data-ttu-id="731b8-162">Nel codice seguente viene illustrata la `VcardInputFormatter` classe dell' [esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/3.1sample):</span><span class="sxs-lookup"><span data-stu-id="731b8-162">The following code shows the `VcardInputFormatter` class from the [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/3.1sample):</span></span>

[!code-csharp[](custom-formatters/3.1sample/Formatters/VcardInputFormatter.cs?name=snippet)]

## <a name="test-the-app"></a><span data-ttu-id="731b8-163">Testare l'app</span><span class="sxs-lookup"><span data-stu-id="731b8-163">Test the app</span></span>

<span data-ttu-id="731b8-164">[Eseguire l'app di esempio per questo articolo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample), che implementa i formattatori di input e output vCard di base.</span><span class="sxs-lookup"><span data-stu-id="731b8-164">[Run the sample app for this article](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample), which implements basic vCard input and output formatters.</span></span> <span data-ttu-id="731b8-165">L'app legge e scrive le vCard simili alle seguenti:</span><span class="sxs-lookup"><span data-stu-id="731b8-165">The app reads and writes vCards similar to the following:</span></span>

```
BEGIN:VCARD
VERSION:2.1
N:Davolio;Nancy
FN:Nancy Davolio
END:VCARD
```

<span data-ttu-id="731b8-166">Per visualizzare l'output della vCard, eseguire l'app e inviare una richiesta GET con l'intestazione Accept `text/vcard` a `https://localhost:5001/api/contacts` .</span><span class="sxs-lookup"><span data-stu-id="731b8-166">To see vCard output, run the app and send a Get request with Accept header `text/vcard` to `https://localhost:5001/api/contacts`.</span></span>

<span data-ttu-id="731b8-167">Per aggiungere un vCard alla raccolta di contatti in memoria:</span><span class="sxs-lookup"><span data-stu-id="731b8-167">To add a vCard to the in-memory collection of contacts:</span></span>

* <span data-ttu-id="731b8-168">Inviare una `Post` richiesta a `/api/contacts` con uno strumento come il post.</span><span class="sxs-lookup"><span data-stu-id="731b8-168">Send a `Post` request to `/api/contacts` with a tool like Postman.</span></span>
* <span data-ttu-id="731b8-169">Impostare l'intestazione `Content-Type` su `text/vcard`.</span><span class="sxs-lookup"><span data-stu-id="731b8-169">Set the `Content-Type` header to `text/vcard`.</span></span>
* <span data-ttu-id="731b8-170">Imposta il `vCard` testo nel corpo, formattato come l'esempio precedente.</span><span class="sxs-lookup"><span data-stu-id="731b8-170">Set `vCard` text in the body, formatted like the preceding example.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="731b8-171">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="731b8-171">Additional resources</span></span>

* <xref:web-api/advanced/formatting>
* <xref:grpc/dotnet-grpc>
