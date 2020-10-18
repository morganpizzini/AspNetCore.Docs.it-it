---
title: Chiamare funzioni JavaScript da metodi .NET in ASP.NET Core Blazor
author: guardrex
description: Informazioni su come richiamare funzioni JavaScript da metodi .NET nelle Blazor app.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/02/2020
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
uid: blazor/call-javascript-from-dotnet
ms.openlocfilehash: a7ba41501b856482c8fcf7efa8e1d78857020bf5
ms.sourcegitcommit: ecae2aa432628b9181d1fa11037c231c7dd56c9e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2020
ms.locfileid: "92113764"
---
# <a name="call-javascript-functions-from-net-methods-in-aspnet-core-no-locblazor"></a><span data-ttu-id="4a7ef-103">Chiamare funzioni JavaScript da metodi .NET in ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="4a7ef-103">Call JavaScript functions from .NET methods in ASP.NET Core Blazor</span></span>

<span data-ttu-id="4a7ef-104">Di [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27)e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="4a7ef-104">By [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="4a7ef-105">Un' Blazor app può richiamare funzioni JavaScript da metodi .NET e metodi .NET da funzioni JavaScript.</span><span class="sxs-lookup"><span data-stu-id="4a7ef-105">A Blazor app can invoke JavaScript functions from .NET methods and .NET methods from JavaScript functions.</span></span> <span data-ttu-id="4a7ef-106">Questi scenari sono detti *interoperabilità JavaScript (interoperabilità* *JS*).</span><span class="sxs-lookup"><span data-stu-id="4a7ef-106">These scenarios are called *JavaScript interoperability* (*JS interop*).</span></span>

<span data-ttu-id="4a7ef-107">Questo articolo illustra come richiamare funzioni JavaScript da .NET.</span><span class="sxs-lookup"><span data-stu-id="4a7ef-107">This article covers invoking JavaScript functions from .NET.</span></span> <span data-ttu-id="4a7ef-108">Per informazioni su come chiamare i metodi .NET da JavaScript, vedere <xref:blazor/call-dotnet-from-javascript> .</span><span class="sxs-lookup"><span data-stu-id="4a7ef-108">For information on how to call .NET methods from JavaScript, see <xref:blazor/call-dotnet-from-javascript>.</span></span>

<span data-ttu-id="4a7ef-109">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="4a7ef-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="4a7ef-110">Per chiamare JavaScript da .NET, usare l' <xref:Microsoft.JSInterop.IJSRuntime> astrazione.</span><span class="sxs-lookup"><span data-stu-id="4a7ef-110">To call into JavaScript from .NET, use the <xref:Microsoft.JSInterop.IJSRuntime> abstraction.</span></span> <span data-ttu-id="4a7ef-111">Per rilasciare chiamate di interoperabilità JS, inserire l' <xref:Microsoft.JSInterop.IJSRuntime> astrazione nel componente.</span><span class="sxs-lookup"><span data-stu-id="4a7ef-111">To issue JS interop calls, inject the <xref:Microsoft.JSInterop.IJSRuntime> abstraction in your component.</span></span> <span data-ttu-id="4a7ef-112"><xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> accetta un identificatore per la funzione JavaScript che si vuole richiamare insieme a un numero qualsiasi di argomenti serializzabili in JSON.</span><span class="sxs-lookup"><span data-stu-id="4a7ef-112"><xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> takes an identifier for the JavaScript function that you wish to invoke along with any number of JSON-serializable arguments.</span></span> <span data-ttu-id="4a7ef-113">L'identificatore della funzione è relativo all'ambito globale ( `window` ).</span><span class="sxs-lookup"><span data-stu-id="4a7ef-113">The function identifier is relative to the global scope (`window`).</span></span> <span data-ttu-id="4a7ef-114">Se si desidera chiamare `window.someScope.someFunction` , l'identificatore è `someScope.someFunction` .</span><span class="sxs-lookup"><span data-stu-id="4a7ef-114">If you wish to call `window.someScope.someFunction`, the identifier is `someScope.someFunction`.</span></span> <span data-ttu-id="4a7ef-115">Non è necessario registrare la funzione prima che venga chiamata.</span><span class="sxs-lookup"><span data-stu-id="4a7ef-115">There's no need to register the function before it's called.</span></span> <span data-ttu-id="4a7ef-116">Il tipo restituito `T` deve anche essere serializzabile in JSON.</span><span class="sxs-lookup"><span data-stu-id="4a7ef-116">The return type `T` must also be JSON serializable.</span></span> <span data-ttu-id="4a7ef-117">`T` deve corrispondere al tipo .NET che meglio esegue il mapping al tipo JSON restituito.</span><span class="sxs-lookup"><span data-stu-id="4a7ef-117">`T` should match the .NET type that best maps to the JSON type returned.</span></span>

<span data-ttu-id="4a7ef-118">Le funzioni JavaScript che restituiscono una [promessa](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) vengono chiamate con <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="4a7ef-118">JavaScript functions that return a [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) are called with <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A>.</span></span> <span data-ttu-id="4a7ef-119">`InvokeAsync` rimuove il wrapping della promessa e restituisce il valore atteso dalla promessa.</span><span class="sxs-lookup"><span data-stu-id="4a7ef-119">`InvokeAsync` unwraps the Promise and returns the value awaited by the Promise.</span></span>

<span data-ttu-id="4a7ef-120">Per Blazor Server le app con il prerendering abilitato, non è possibile chiamare JavaScript durante il prerendering iniziale.</span><span class="sxs-lookup"><span data-stu-id="4a7ef-120">For Blazor Server apps with prerendering enabled, calling into JavaScript isn't possible during the initial prerendering.</span></span> <span data-ttu-id="4a7ef-121">È necessario rinviare le chiamate di interoperabilità JavaScript finché non viene stabilita la connessione con il browser.</span><span class="sxs-lookup"><span data-stu-id="4a7ef-121">JavaScript interop calls must be deferred until after the connection with the browser is established.</span></span> <span data-ttu-id="4a7ef-122">Per altre informazioni, vedere la sezione [rilevare quando un' Blazor Server app è prerendering](#detect-when-a-blazor-server-app-is-prerendering) .</span><span class="sxs-lookup"><span data-stu-id="4a7ef-122">For more information, see the [Detect when a Blazor Server app is prerendering](#detect-when-a-blazor-server-app-is-prerendering) section.</span></span>

<span data-ttu-id="4a7ef-123">L'esempio seguente è basato su [`TextDecoder`](https://developer.mozilla.org/docs/Web/API/TextDecoder) , un decodificatore basato su JavaScript.</span><span class="sxs-lookup"><span data-stu-id="4a7ef-123">The following example is based on [`TextDecoder`](https://developer.mozilla.org/docs/Web/API/TextDecoder), a JavaScript-based decoder.</span></span> <span data-ttu-id="4a7ef-124">Nell'esempio viene illustrato come richiamare una funzione JavaScript da un metodo C# che Scarica un requisito dal codice dello sviluppatore a un'API JavaScript esistente.</span><span class="sxs-lookup"><span data-stu-id="4a7ef-124">The example demonstrates how to invoke a JavaScript function from a C# method that offloads a requirement from developer code to an existing JavaScript API.</span></span> <span data-ttu-id="4a7ef-125">La funzione JavaScript accetta una matrice di byte da un metodo C#, decodifica la matrice e restituisce il testo al componente per la visualizzazione.</span><span class="sxs-lookup"><span data-stu-id="4a7ef-125">The JavaScript function accepts a byte array from a C# method, decodes the array, and returns the text to the component for display.</span></span>

<span data-ttu-id="4a7ef-126">All'interno dell' `<head>` elemento di `wwwroot/index.html` ( Blazor WebAssembly ) o `Pages/_Host.cshtml` ( Blazor Server ), fornire una funzione JavaScript che usi `TextDecoder` per decodificare una matrice passata e restituire il valore decodificato:</span><span class="sxs-lookup"><span data-stu-id="4a7ef-126">Inside the `<head>` element of `wwwroot/index.html` (Blazor WebAssembly) or `Pages/_Host.cshtml` (Blazor Server), provide a JavaScript function that uses `TextDecoder` to decode a passed array and return the decoded value:</span></span>

[!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-convertarray.html)]

<span data-ttu-id="4a7ef-127">Il codice JavaScript, ad esempio il codice illustrato nell'esempio precedente, può anche essere caricato da un file JavaScript ( `.js` ) con un riferimento al file script:</span><span class="sxs-lookup"><span data-stu-id="4a7ef-127">JavaScript code, such as the code shown in the preceding example, can also be loaded from a JavaScript file (`.js`) with a reference to the script file:</span></span>

```html
<script src="exampleJsInterop.js"></script>
```

<span data-ttu-id="4a7ef-128">Il componente seguente:</span><span class="sxs-lookup"><span data-stu-id="4a7ef-128">The following component:</span></span>

* <span data-ttu-id="4a7ef-129">Richiama la `convertArray` funzione JavaScript usando `JSRuntime` quando viene selezionato un pulsante del componente ( **`Convert Array`** ).</span><span class="sxs-lookup"><span data-stu-id="4a7ef-129">Invokes the `convertArray` JavaScript function using `JSRuntime` when a component button (**`Convert Array`**) is selected.</span></span>
* <span data-ttu-id="4a7ef-130">Dopo la chiamata della funzione JavaScript, la matrice passata viene convertita in una stringa.</span><span class="sxs-lookup"><span data-stu-id="4a7ef-130">After the JavaScript function is called, the passed array is converted into a string.</span></span> <span data-ttu-id="4a7ef-131">La stringa viene restituita al componente per la visualizzazione.</span><span class="sxs-lookup"><span data-stu-id="4a7ef-131">The string is returned to the component for display.</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/call-js-example.razor?highlight=2,34-35)]

## <a name="ijsruntime"></a><span data-ttu-id="4a7ef-132">IJSRuntime</span><span class="sxs-lookup"><span data-stu-id="4a7ef-132">IJSRuntime</span></span>

<span data-ttu-id="4a7ef-133">Per usare l' <xref:Microsoft.JSInterop.IJSRuntime> astrazione, adottare uno degli approcci seguenti:</span><span class="sxs-lookup"><span data-stu-id="4a7ef-133">To use the <xref:Microsoft.JSInterop.IJSRuntime> abstraction, adopt any of the following approaches:</span></span>

* <span data-ttu-id="4a7ef-134">Inserire l' <xref:Microsoft.JSInterop.IJSRuntime> astrazione nel Razor componente ( `.razor` ):</span><span class="sxs-lookup"><span data-stu-id="4a7ef-134">Inject the <xref:Microsoft.JSInterop.IJSRuntime> abstraction into the Razor component (`.razor`):</span></span>

  [!code-razor[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction.razor?highlight=1)]

  <span data-ttu-id="4a7ef-135">All'interno dell' `<head>` elemento di `wwwroot/index.html` ( Blazor WebAssembly ) o `Pages/_Host.cshtml` ( Blazor Server ) specificare una `handleTickerChanged` funzione JavaScript.</span><span class="sxs-lookup"><span data-stu-id="4a7ef-135">Inside the `<head>` element of `wwwroot/index.html` (Blazor WebAssembly) or `Pages/_Host.cshtml` (Blazor Server), provide a `handleTickerChanged` JavaScript function.</span></span> <span data-ttu-id="4a7ef-136">La funzione viene chiamata con <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> e non restituisce un valore:</span><span class="sxs-lookup"><span data-stu-id="4a7ef-136">The function is called with <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> and doesn't return a value:</span></span>

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged1.html)]

* <span data-ttu-id="4a7ef-137">Inserire l' <xref:Microsoft.JSInterop.IJSRuntime> astrazione in una classe ( `.cs` ):</span><span class="sxs-lookup"><span data-stu-id="4a7ef-137">Inject the <xref:Microsoft.JSInterop.IJSRuntime> abstraction into a class (`.cs`):</span></span>

  [!code-csharp[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction-class.cs?highlight=5)]

  <span data-ttu-id="4a7ef-138">All'interno dell' `<head>` elemento di `wwwroot/index.html` ( Blazor WebAssembly ) o `Pages/_Host.cshtml` ( Blazor Server ) specificare una `handleTickerChanged` funzione JavaScript.</span><span class="sxs-lookup"><span data-stu-id="4a7ef-138">Inside the `<head>` element of `wwwroot/index.html` (Blazor WebAssembly) or `Pages/_Host.cshtml` (Blazor Server), provide a `handleTickerChanged` JavaScript function.</span></span> <span data-ttu-id="4a7ef-139">La funzione viene chiamata con `JSRuntime.InvokeAsync` e restituisce un valore:</span><span class="sxs-lookup"><span data-stu-id="4a7ef-139">The function is called with `JSRuntime.InvokeAsync` and returns a value:</span></span>

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged2.html)]

* <span data-ttu-id="4a7ef-140">Per la generazione di contenuto dinamico con [BuildRenderTree](xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic), usare l' `[Inject]` attributo:</span><span class="sxs-lookup"><span data-stu-id="4a7ef-140">For dynamic content generation with [BuildRenderTree](xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic), use the `[Inject]` attribute:</span></span>

  ```razor
  [Inject]
  IJSRuntime JSRuntime { get; set; }
  ```

<span data-ttu-id="4a7ef-141">Nell'app di esempio lato client che accompagna questo argomento sono disponibili due funzioni JavaScript per l'app che interagiscono con il DOM per ricevere l'input dell'utente e visualizzare un messaggio di benvenuto:</span><span class="sxs-lookup"><span data-stu-id="4a7ef-141">In the client-side sample app that accompanies this topic, two JavaScript functions are available to the app that interact with the DOM to receive user input and display a welcome message:</span></span>

* <span data-ttu-id="4a7ef-142">`showPrompt`: Genera un messaggio di richiesta per accettare l'input dell'utente (il nome dell'utente) e restituisce il nome al chiamante.</span><span class="sxs-lookup"><span data-stu-id="4a7ef-142">`showPrompt`: Produces a prompt to accept user input (the user's name) and returns the name to the caller.</span></span>
* <span data-ttu-id="4a7ef-143">`displayWelcome`: Assegna un messaggio di benvenuto dal chiamante a un oggetto DOM con un valore `id` di `welcome` .</span><span class="sxs-lookup"><span data-stu-id="4a7ef-143">`displayWelcome`: Assigns a welcome message from the caller to a DOM object with an `id` of `welcome`.</span></span>

<span data-ttu-id="4a7ef-144">`wwwroot/exampleJsInterop.js`:</span><span class="sxs-lookup"><span data-stu-id="4a7ef-144">`wwwroot/exampleJsInterop.js`:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=2-7)]

<span data-ttu-id="4a7ef-145">Inserire il `<script>` tag che fa riferimento al file JavaScript nel file `wwwroot/index.html` ( Blazor WebAssembly ) o nel `Pages/_Host.cshtml` file ( Blazor Server ).</span><span class="sxs-lookup"><span data-stu-id="4a7ef-145">Place the `<script>` tag that references the JavaScript file in the `wwwroot/index.html` file (Blazor WebAssembly) or `Pages/_Host.cshtml` file (Blazor Server).</span></span>

<span data-ttu-id="4a7ef-146">`wwwroot/index.html` (Blazor WebAssembly):</span><span class="sxs-lookup"><span data-stu-id="4a7ef-146">`wwwroot/index.html` (Blazor WebAssembly):</span></span>

[!code-html[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/index.html?highlight=22)]

<span data-ttu-id="4a7ef-147">`Pages/_Host.cshtml` (Blazor Server):</span><span class="sxs-lookup"><span data-stu-id="4a7ef-147">`Pages/_Host.cshtml` (Blazor Server):</span></span>

[!code-cshtml[](./common/samples/3.x/BlazorServerSample/Pages/_Host.cshtml?highlight=35)]

<span data-ttu-id="4a7ef-148">Non inserire un `<script>` tag in un file di componente perché il `<script>` tag non può essere aggiornato dinamicamente.</span><span class="sxs-lookup"><span data-stu-id="4a7ef-148">Don't place a `<script>` tag in a component file because the `<script>` tag can't be updated dynamically.</span></span>

<span data-ttu-id="4a7ef-149">I metodi .NET interoperano con le funzioni JavaScript nel `exampleJsInterop.js` file chiamando <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="4a7ef-149">.NET methods interop with the JavaScript functions in the `exampleJsInterop.js` file by calling <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType>.</span></span>

<span data-ttu-id="4a7ef-150">L' <xref:Microsoft.JSInterop.IJSRuntime> astrazione è asincrona per consentire gli Blazor Server scenari.</span><span class="sxs-lookup"><span data-stu-id="4a7ef-150">The <xref:Microsoft.JSInterop.IJSRuntime> abstraction is asynchronous to allow for Blazor Server scenarios.</span></span> <span data-ttu-id="4a7ef-151">Se l'app è un' Blazor WebAssembly app e si vuole richiamare una funzione JavaScript in modo sincrono, <xref:Microsoft.JSInterop.IJSInProcessRuntime> abbandono e chiamano <xref:Microsoft.JSInterop.IJSInProcessRuntime.Invoke%2A> invece.</span><span class="sxs-lookup"><span data-stu-id="4a7ef-151">If the app is a Blazor WebAssembly app and you want to invoke a JavaScript function synchronously, downcast to <xref:Microsoft.JSInterop.IJSInProcessRuntime> and call <xref:Microsoft.JSInterop.IJSInProcessRuntime.Invoke%2A> instead.</span></span> <span data-ttu-id="4a7ef-152">È consigliabile che la maggior parte delle librerie di interoperabilità JS usi le API asincrone per assicurarsi che le librerie siano disponibili in tutti gli scenari.</span><span class="sxs-lookup"><span data-stu-id="4a7ef-152">We recommend that most JS interop libraries use the async APIs to ensure that the libraries are available in all scenarios.</span></span>

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="4a7ef-153">Per abilitare l'isolamento JavaScript nei [moduli JavaScript](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules)standard, vedere la sezione [ Blazor riferimenti a oggetti e isolamento JavaScript](#blazor-javascript-isolation-and-object-references) .</span><span class="sxs-lookup"><span data-stu-id="4a7ef-153">To enable JavaScript isolation in standard [JavaScript modules](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules), see the [Blazor JavaScript isolation and object references](#blazor-javascript-isolation-and-object-references) section.</span></span>

::: moniker-end

<span data-ttu-id="4a7ef-154">L'app di esempio include un componente per dimostrare l'interoperabilità JS.</span><span class="sxs-lookup"><span data-stu-id="4a7ef-154">The sample app includes a component to demonstrate JS interop.</span></span> <span data-ttu-id="4a7ef-155">Il componente:</span><span class="sxs-lookup"><span data-stu-id="4a7ef-155">The component:</span></span>

* <span data-ttu-id="4a7ef-156">Riceve l'input dell'utente tramite un prompt di JavaScript.</span><span class="sxs-lookup"><span data-stu-id="4a7ef-156">Receives user input via a JavaScript prompt.</span></span>
* <span data-ttu-id="4a7ef-157">Restituisce il testo al componente per l'elaborazione.</span><span class="sxs-lookup"><span data-stu-id="4a7ef-157">Returns the text to the component for processing.</span></span>
* <span data-ttu-id="4a7ef-158">Chiama una seconda funzione JavaScript che interagisce con il DOM per visualizzare un messaggio di benvenuto.</span><span class="sxs-lookup"><span data-stu-id="4a7ef-158">Calls a second JavaScript function that interacts with the DOM to display a welcome message.</span></span>

<span data-ttu-id="4a7ef-159">`Pages/JsInterop.razor`:</span><span class="sxs-lookup"><span data-stu-id="4a7ef-159">`Pages/JsInterop.razor`:</span></span>

```razor
@page "/JSInterop"
@using {APP ASSEMBLY}.JsInteropClasses
@inject IJSRuntime JSRuntime

<h1>JavaScript Interop</h1>

<h2>Invoke JavaScript functions from .NET methods</h2>

<button type="button" class="btn btn-primary" @onclick="TriggerJsPrompt">
    Trigger JavaScript Prompt
</button>

<h3 id="welcome" style="color:green;font-style:italic"></h3>

@code {
    public async Task TriggerJsPrompt()
    {
        var name = await JSRuntime.InvokeAsync<string>(
                "exampleJsFunctions.showPrompt",
                "What's your name?");

        await JSRuntime.InvokeVoidAsync(
                "exampleJsFunctions.displayWelcome",
                $"Hello {name}! Welcome to Blazor!");
    }
}
```

<span data-ttu-id="4a7ef-160">Il segnaposto `{APP ASSEMBLY}` è il nome dell'assembly dell'app (ad esempio, `BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="4a7ef-160">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

1. <span data-ttu-id="4a7ef-161">Quando `TriggerJsPrompt` viene eseguito selezionando il pulsante del componente **`Trigger JavaScript Prompt`** , `showPrompt` viene chiamata la funzione JavaScript fornita nel `wwwroot/exampleJsInterop.js` file.</span><span class="sxs-lookup"><span data-stu-id="4a7ef-161">When `TriggerJsPrompt` is executed by selecting the component's **`Trigger JavaScript Prompt`** button, the JavaScript `showPrompt` function provided in the `wwwroot/exampleJsInterop.js` file is called.</span></span>
1. <span data-ttu-id="4a7ef-162">La `showPrompt` funzione accetta l'input dell'utente (il nome dell'utente), che è codificato in HTML e restituito al componente.</span><span class="sxs-lookup"><span data-stu-id="4a7ef-162">The `showPrompt` function accepts user input (the user's name), which is HTML-encoded and returned to the component.</span></span> <span data-ttu-id="4a7ef-163">Il componente archivia il nome dell'utente in una variabile locale, `name` .</span><span class="sxs-lookup"><span data-stu-id="4a7ef-163">The component stores the user's name in a local variable, `name`.</span></span>
1. <span data-ttu-id="4a7ef-164">La stringa archiviata in `name` viene incorporata in un messaggio di benvenuto, che viene passato a una funzione JavaScript, `displayWelcome` , che esegue il rendering del messaggio di benvenuto in un tag di intestazione.</span><span class="sxs-lookup"><span data-stu-id="4a7ef-164">The string stored in `name` is incorporated into a welcome message, which is passed to a JavaScript function, `displayWelcome`, which renders the welcome message into a heading tag.</span></span>

## <a name="call-a-void-javascript-function"></a><span data-ttu-id="4a7ef-165">Chiamare una funzione JavaScript void</span><span class="sxs-lookup"><span data-stu-id="4a7ef-165">Call a void JavaScript function</span></span>

<span data-ttu-id="4a7ef-166">Utilizzare <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> per gli elementi seguenti:</span><span class="sxs-lookup"><span data-stu-id="4a7ef-166">Use <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> for the following:</span></span>

* <span data-ttu-id="4a7ef-167">Funzioni JavaScript che restituiscono [void (0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) o [undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined).</span><span class="sxs-lookup"><span data-stu-id="4a7ef-167">JavaScript functions that return [void(0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) or [undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined).</span></span>
* <span data-ttu-id="4a7ef-168">Se .NET non è necessario per leggere il risultato di una chiamata a JavaScript.</span><span class="sxs-lookup"><span data-stu-id="4a7ef-168">If .NET isn't required to read the result of a JavaScript call.</span></span>

## <a name="detect-when-a-no-locblazor-server-app-is-prerendering"></a><span data-ttu-id="4a7ef-169">Rileva quando viene eseguito il Blazor Server prerendering di un'app</span><span class="sxs-lookup"><span data-stu-id="4a7ef-169">Detect when a Blazor Server app is prerendering</span></span>
 
[!INCLUDE[](~/includes/blazor-prerendering.md)]

## <a name="capture-references-to-elements"></a><span data-ttu-id="4a7ef-170">Acquisisci riferimenti a elementi</span><span class="sxs-lookup"><span data-stu-id="4a7ef-170">Capture references to elements</span></span>

<span data-ttu-id="4a7ef-171">Alcuni scenari di interoperabilità JS richiedono riferimenti agli elementi HTML.</span><span class="sxs-lookup"><span data-stu-id="4a7ef-171">Some JS interop scenarios require references to HTML elements.</span></span> <span data-ttu-id="4a7ef-172">Ad esempio, una libreria dell'interfaccia utente può richiedere un riferimento a un elemento per l'inizializzazione o potrebbe essere necessario chiamare API simili a quelle di un elemento, ad esempio `focus` o `play` .</span><span class="sxs-lookup"><span data-stu-id="4a7ef-172">For example, a UI library may require an element reference for initialization, or you might need to call command-like APIs on an element, such as `focus` or `play`.</span></span>

<span data-ttu-id="4a7ef-173">Acquisire i riferimenti agli elementi HTML in un componente usando l'approccio seguente:</span><span class="sxs-lookup"><span data-stu-id="4a7ef-173">Capture references to HTML elements in a component using the following approach:</span></span>

* <span data-ttu-id="4a7ef-174">Aggiungere un `@ref` attributo all'elemento HTML.</span><span class="sxs-lookup"><span data-stu-id="4a7ef-174">Add an `@ref` attribute to the HTML element.</span></span>
* <span data-ttu-id="4a7ef-175">Definire un campo di tipo <xref:Microsoft.AspNetCore.Components.ElementReference> il cui nome corrisponde al valore dell' `@ref` attributo.</span><span class="sxs-lookup"><span data-stu-id="4a7ef-175">Define a field of type <xref:Microsoft.AspNetCore.Components.ElementReference> whose name matches the value of the `@ref` attribute.</span></span>

<span data-ttu-id="4a7ef-176">Nell'esempio seguente viene illustrata l'acquisizione di un riferimento all' `username` `<input>` elemento:</span><span class="sxs-lookup"><span data-stu-id="4a7ef-176">The following example shows capturing a reference to the `username` `<input>` element:</span></span>

```razor
<input @ref="username" ... />

@code {
    ElementReference username;
}
```

> [!WARNING]
> <span data-ttu-id="4a7ef-177">Usare solo un riferimento a un elemento per mutare il contenuto di un elemento vuoto che non interagisce con Blazor .</span><span class="sxs-lookup"><span data-stu-id="4a7ef-177">Only use an element reference to mutate the contents of an empty element that doesn't interact with Blazor.</span></span> <span data-ttu-id="4a7ef-178">Questo scenario è utile quando un'API di terze parti fornisce contenuto all'elemento.</span><span class="sxs-lookup"><span data-stu-id="4a7ef-178">This scenario is useful when a third-party API supplies content to the element.</span></span> <span data-ttu-id="4a7ef-179">Poiché Blazor non interagisce con l'elemento, non è possibile che si verifichi un conflitto tra Blazor la rappresentazione dell'elemento e il Dom.</span><span class="sxs-lookup"><span data-stu-id="4a7ef-179">Because Blazor doesn't interact with the element, there's no possibility of a conflict between Blazor's representation of the element and the DOM.</span></span>
>
> <span data-ttu-id="4a7ef-180">Nell'esempio seguente è *pericoloso* modificare il contenuto dell'elenco non ordinato ( `ul` ) perché Blazor interagisce con il Dom per popolare gli elementi elenco di questo elemento ( `<li>` ):</span><span class="sxs-lookup"><span data-stu-id="4a7ef-180">In the following example, it's *dangerous* to mutate the contents of the unordered list (`ul`) because Blazor interacts with the DOM to populate this element's list items (`<li>`):</span></span>
>
> ```razor
> <ul ref="MyList">
>     @foreach (var item in Todos)
>     {
>         <li>@item.Text</li>
>     }
> </ul>
> ```
>
> <span data-ttu-id="4a7ef-181">Se l'interoperabilità JS modifica il contenuto dell'elemento `MyList` e Blazor tenta di applicare differenze all'elemento, le differenze non corrispondono al Dom.</span><span class="sxs-lookup"><span data-stu-id="4a7ef-181">If JS interop mutates the contents of element `MyList` and Blazor attempts to apply diffs to the element, the diffs won't match the DOM.</span></span>

<span data-ttu-id="4a7ef-182">Per quanto riguarda il codice .NET, un <xref:Microsoft.AspNetCore.Components.ElementReference> è un handle opaco.</span><span class="sxs-lookup"><span data-stu-id="4a7ef-182">As far as .NET code is concerned, an <xref:Microsoft.AspNetCore.Components.ElementReference> is an opaque handle.</span></span> <span data-ttu-id="4a7ef-183">L' *unica* cosa che è possibile fare con <xref:Microsoft.AspNetCore.Components.ElementReference> è passarla al codice JavaScript tramite l'interoperabilità js.</span><span class="sxs-lookup"><span data-stu-id="4a7ef-183">The *only* thing you can do with <xref:Microsoft.AspNetCore.Components.ElementReference> is pass it through to JavaScript code via JS interop.</span></span> <span data-ttu-id="4a7ef-184">Quando si esegue questa operazione, il codice sul lato JavaScript riceve un' `HTMLElement` istanza, che può essere usata con le API DOM normali.</span><span class="sxs-lookup"><span data-stu-id="4a7ef-184">When you do so, the JavaScript-side code receives an `HTMLElement` instance, which it can use with normal DOM APIs.</span></span>

<span data-ttu-id="4a7ef-185">Il codice seguente, ad esempio, definisce un metodo di estensione .NET che consente di impostare lo stato attivo su un elemento:</span><span class="sxs-lookup"><span data-stu-id="4a7ef-185">For example, the following code defines a .NET extension method that enables setting the focus on an element:</span></span>

<span data-ttu-id="4a7ef-186">`exampleJsInterop.js`:</span><span class="sxs-lookup"><span data-stu-id="4a7ef-186">`exampleJsInterop.js`:</span></span>

```javascript
window.exampleJsFunctions = {
  focusElement : function (element) {
    element.focus();
  }
}
```

<span data-ttu-id="4a7ef-187">Per chiamare una funzione JavaScript che non restituisce un valore, usare <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="4a7ef-187">To call a JavaScript function that doesn't return a value, use <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="4a7ef-188">Il codice seguente imposta lo stato attivo sull'input del nome utente chiamando la funzione JavaScript precedente con l'acquisizione <xref:Microsoft.AspNetCore.Components.ElementReference> :</span><span class="sxs-lookup"><span data-stu-id="4a7ef-188">The following code sets the focus on the username input by calling the preceding JavaScript function with the captured <xref:Microsoft.AspNetCore.Components.ElementReference>:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component1.razor?highlight=1,3,11-12)]

<span data-ttu-id="4a7ef-189">Per usare un metodo di estensione, creare un metodo di estensione statico che riceva l' <xref:Microsoft.JSInterop.IJSRuntime> istanza:</span><span class="sxs-lookup"><span data-stu-id="4a7ef-189">To use an extension method, create a static extension method that receives the <xref:Microsoft.JSInterop.IJSRuntime> instance:</span></span>

```csharp
public static async Task Focus(this ElementReference elementRef, IJSRuntime jsRuntime)
{
    await jsRuntime.InvokeVoidAsync(
        "exampleJsFunctions.focusElement", elementRef);
}
```

<span data-ttu-id="4a7ef-190">Il `Focus` metodo viene chiamato direttamente nell'oggetto.</span><span class="sxs-lookup"><span data-stu-id="4a7ef-190">The `Focus` method is called directly on the object.</span></span> <span data-ttu-id="4a7ef-191">Nell'esempio seguente si presuppone che il `Focus` metodo sia disponibile dallo `JsInteropClasses` spazio dei nomi:</span><span class="sxs-lookup"><span data-stu-id="4a7ef-191">The following example assumes that the `Focus` method is available from the `JsInteropClasses` namespace:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component2.razor?highlight=1-4,12)]

> [!IMPORTANT]
> <span data-ttu-id="4a7ef-192">La `username` variabile viene popolata solo dopo il rendering del componente.</span><span class="sxs-lookup"><span data-stu-id="4a7ef-192">The `username` variable is only populated after the component is rendered.</span></span> <span data-ttu-id="4a7ef-193">Se un oggetto non popolato <xref:Microsoft.AspNetCore.Components.ElementReference> viene passato al codice JavaScript, il codice JavaScript riceve un valore di `null` .</span><span class="sxs-lookup"><span data-stu-id="4a7ef-193">If an unpopulated <xref:Microsoft.AspNetCore.Components.ElementReference> is passed to JavaScript code, the JavaScript code receives a value of `null`.</span></span> <span data-ttu-id="4a7ef-194">Per modificare i riferimenti agli elementi dopo che il componente ha terminato il rendering (per impostare lo stato attivo iniziale su un elemento), usare i metodi del ciclo di vita dei [ `OnAfterRenderAsync` `OnAfterRender` componenti o](xref:blazor/components/lifecycle#after-component-render).</span><span class="sxs-lookup"><span data-stu-id="4a7ef-194">To manipulate element references after the component has finished rendering (to set the initial focus on an element) use the [`OnAfterRenderAsync` or `OnAfterRender` component lifecycle methods](xref:blazor/components/lifecycle#after-component-render).</span></span>

<span data-ttu-id="4a7ef-195">Quando si utilizzano tipi generici e si restituisce un valore, utilizzare <xref:System.Threading.Tasks.ValueTask%601> :</span><span class="sxs-lookup"><span data-stu-id="4a7ef-195">When working with generic types and returning a value, use <xref:System.Threading.Tasks.ValueTask%601>:</span></span>

```csharp
public static ValueTask<T> GenericMethod<T>(this ElementReference elementRef, 
    IJSRuntime jsRuntime)
{
    return jsRuntime.InvokeAsync<T>(
        "exampleJsFunctions.doSomethingGeneric", elementRef);
}
```

<span data-ttu-id="4a7ef-196">`GenericMethod` viene chiamato direttamente sull'oggetto con un tipo.</span><span class="sxs-lookup"><span data-stu-id="4a7ef-196">`GenericMethod` is called directly on the object with a type.</span></span> <span data-ttu-id="4a7ef-197">Nell'esempio seguente si presuppone che l'oggetto `GenericMethod` sia disponibile dallo `JsInteropClasses` spazio dei nomi:</span><span class="sxs-lookup"><span data-stu-id="4a7ef-197">The following example assumes that the `GenericMethod` is available from the `JsInteropClasses` namespace:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component3.razor?highlight=17)]

## <a name="reference-elements-across-components"></a><span data-ttu-id="4a7ef-198">Elementi di riferimento tra i componenti</span><span class="sxs-lookup"><span data-stu-id="4a7ef-198">Reference elements across components</span></span>

<span data-ttu-id="4a7ef-199">Un oggetto <xref:Microsoft.AspNetCore.Components.ElementReference> è garantito solo valido nel metodo di un componente <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> (e un riferimento a un elemento è `struct` ), pertanto non è possibile passare un riferimento a un elemento tra i componenti.</span><span class="sxs-lookup"><span data-stu-id="4a7ef-199">An <xref:Microsoft.AspNetCore.Components.ElementReference> is only guaranteed valid in a component's <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> method (and an element reference is a `struct`), so an element reference can't be passed between components.</span></span>

<span data-ttu-id="4a7ef-200">Affinché un componente padre renda disponibile un riferimento a un elemento per altri componenti, il componente padre può:</span><span class="sxs-lookup"><span data-stu-id="4a7ef-200">For a parent component to make an element reference available to other components, the parent component can:</span></span>

* <span data-ttu-id="4a7ef-201">Consenti ai componenti figlio di registrare i callback.</span><span class="sxs-lookup"><span data-stu-id="4a7ef-201">Allow child components to register callbacks.</span></span>
* <span data-ttu-id="4a7ef-202">Richiamare i callback registrati durante l' <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> evento con il riferimento all'elemento passato.</span><span class="sxs-lookup"><span data-stu-id="4a7ef-202">Invoke the registered callbacks during the <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> event with the passed element reference.</span></span> <span data-ttu-id="4a7ef-203">Indirettamente, questo approccio consente ai componenti figlio di interagire con il riferimento all'elemento padre.</span><span class="sxs-lookup"><span data-stu-id="4a7ef-203">Indirectly, this approach allows child components to interact with the parent's element reference.</span></span>

<span data-ttu-id="4a7ef-204">Nell' Blazor WebAssembly esempio seguente viene illustrato l'approccio.</span><span class="sxs-lookup"><span data-stu-id="4a7ef-204">The following Blazor WebAssembly example illustrates the approach.</span></span>

<span data-ttu-id="4a7ef-205">Nella `<head>` di `wwwroot/index.html` :</span><span class="sxs-lookup"><span data-stu-id="4a7ef-205">In the `<head>` of `wwwroot/index.html`:</span></span>

```html
<style>
    .red { color: red }
</style>
```

<span data-ttu-id="4a7ef-206">Nella `<body>` di `wwwroot/index.html` :</span><span class="sxs-lookup"><span data-stu-id="4a7ef-206">In the `<body>` of `wwwroot/index.html`:</span></span>

```html
<script>
    function setElementClass(element, className) {
        /** @type {HTMLElement} **/
        var myElement = element;
        myElement.classList.add(className);
    }
</script>
```

<span data-ttu-id="4a7ef-207">`Pages/Index.razor` (componente padre):</span><span class="sxs-lookup"><span data-stu-id="4a7ef-207">`Pages/Index.razor` (parent component):</span></span>

```razor
@page "/"

<h1 @ref="title">Hello, world!</h1>

Welcome to your new app.

<SurveyPrompt Parent="this" Title="How is Blazor working for you?" />
```

<span data-ttu-id="4a7ef-208">`Pages/Index.razor.cs`:</span><span class="sxs-lookup"><span data-stu-id="4a7ef-208">`Pages/Index.razor.cs`:</span></span>

```csharp
using System;
using System.Collections.Generic;
using Microsoft.AspNetCore.Components;

namespace {APP ASSEMBLY}.Pages
{
    public partial class Index : 
        ComponentBase, IObservable<ElementReference>, IDisposable
    {
        private bool disposing;
        private IList<IObserver<ElementReference>> subscriptions = 
            new List<IObserver<ElementReference>>();
        private ElementReference title;

        protected override void OnAfterRender(bool firstRender)
        {
            base.OnAfterRender(firstRender);

            foreach (var subscription in subscriptions)
            {
                try
                {
                    subscription.OnNext(title);
                }
                catch (Exception)
                {
                    throw;
                }
            }
        }

        public void Dispose()
        {
            disposing = true;

            foreach (var subscription in subscriptions)
            {
                try
                {
                    subscription.OnCompleted();
                }
                catch (Exception)
                {
                }
            }

            subscriptions.Clear();
        }

        public IDisposable Subscribe(IObserver<ElementReference> observer)
        {
            if (disposing)
            {
                throw new InvalidOperationException("Parent being disposed");
            }

            subscriptions.Add(observer);

            return new Subscription(observer, this);
        }

        private class Subscription : IDisposable
        {
            public Subscription(IObserver<ElementReference> observer, Index self)
            {
                Observer = observer;
                Self = self;
            }

            public IObserver<ElementReference> Observer { get; }
            public Index Self { get; }

            public void Dispose()
            {
                Self.subscriptions.Remove(Observer);
            }
        }
    }
}
```

<span data-ttu-id="4a7ef-209">Il segnaposto `{APP ASSEMBLY}` è il nome dell'assembly dell'app (ad esempio, `BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="4a7ef-209">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

<span data-ttu-id="4a7ef-210">`Shared/SurveyPrompt.razor` (componente figlio):</span><span class="sxs-lookup"><span data-stu-id="4a7ef-210">`Shared/SurveyPrompt.razor` (child component):</span></span>

```razor
@inject IJSRuntime JS

<div class="alert alert-secondary mt-4" role="alert">
    <span class="oi oi-pencil mr-2" aria-hidden="true"></span>
    <strong>@Title</strong>

    <span class="text-nowrap">
        Please take our
        <a target="_blank" class="font-weight-bold" 
            href="https://go.microsoft.com/fwlink/?linkid=2109206">brief survey</a>
    </span>
    and tell us what you think.
</div>

@code {
    [Parameter]
    public string Title { get; set; }
}
```

<span data-ttu-id="4a7ef-211">`Shared/SurveyPrompt.razor.cs`:</span><span class="sxs-lookup"><span data-stu-id="4a7ef-211">`Shared/SurveyPrompt.razor.cs`:</span></span>

```csharp
using System;
using Microsoft.AspNetCore.Components;

namespace {APP ASSEMBLY}.Shared
{
    public partial class SurveyPrompt : 
        ComponentBase, IObserver<ElementReference>, IDisposable
    {
        private IDisposable subscription = null;

        [Parameter]
        public IObservable<ElementReference> Parent { get; set; }

        protected override void OnParametersSet()
        {
            base.OnParametersSet();

            if (subscription != null)
            {
                subscription.Dispose();
            }

            subscription = Parent.Subscribe(this);
        }

        public void OnCompleted()
        {
            subscription = null;
        }

        public void OnError(Exception error)
        {
            subscription = null;
        }

        public void OnNext(ElementReference value)
        {
            JS.InvokeAsync<object>(
                "setElementClass", new object[] { value, "red" });
        }

        public void Dispose()
        {
            subscription?.Dispose();
        }
    }
}
```

<span data-ttu-id="4a7ef-212">Il segnaposto `{APP ASSEMBLY}` è il nome dell'assembly dell'app (ad esempio, `BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="4a7ef-212">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

## <a name="harden-js-interop-calls"></a><span data-ttu-id="4a7ef-213">Chiamate di interoperabilità JS di Harden</span><span class="sxs-lookup"><span data-stu-id="4a7ef-213">Harden JS interop calls</span></span>

<span data-ttu-id="4a7ef-214">L'interoperabilità JS potrebbe non riuscire a causa di errori di rete e deve essere considerata inaffidabile.</span><span class="sxs-lookup"><span data-stu-id="4a7ef-214">JS interop may fail due to networking errors and should be treated as unreliable.</span></span> <span data-ttu-id="4a7ef-215">Per impostazione predefinita, un' Blazor Server app timeout delle chiamate di interoperabilità js nel server dopo un minuto.</span><span class="sxs-lookup"><span data-stu-id="4a7ef-215">By default, a Blazor Server app times out JS interop calls on the server after one minute.</span></span> <span data-ttu-id="4a7ef-216">Se un'app può tollerare un timeout più aggressivo, impostare il timeout usando uno degli approcci seguenti:</span><span class="sxs-lookup"><span data-stu-id="4a7ef-216">If an app can tolerate a more aggressive timeout, set the timeout using one of the following approaches:</span></span>

* <span data-ttu-id="4a7ef-217">A livello globale in `Startup.ConfigureServices` specificare il timeout:</span><span class="sxs-lookup"><span data-stu-id="4a7ef-217">Globally in `Startup.ConfigureServices`, specify the timeout:</span></span>

  ```csharp
  services.AddServerSideBlazor(
      options => options.JSInteropDefaultCallTimeout = TimeSpan.FromSeconds({SECONDS}));
  ```

* <span data-ttu-id="4a7ef-218">Per chiamata nel codice componente, una singola chiamata può specificare il timeout:</span><span class="sxs-lookup"><span data-stu-id="4a7ef-218">Per-invocation in component code, a single call can specify the timeout:</span></span>

  ```csharp
  var result = await JSRuntime.InvokeAsync<string>("MyJSOperation", 
      TimeSpan.FromSeconds({SECONDS}), new[] { "Arg1" });
  ```

<span data-ttu-id="4a7ef-219">Per ulteriori informazioni sull'esaurimento delle risorse, vedere <xref:blazor/security/server/threat-mitigation> .</span><span class="sxs-lookup"><span data-stu-id="4a7ef-219">For more information on resource exhaustion, see <xref:blazor/security/server/threat-mitigation>.</span></span>

[!INCLUDE[](~/includes/blazor-share-interop-code.md)]

## <a name="avoid-circular-object-references"></a><span data-ttu-id="4a7ef-220">Evitare riferimenti a oggetti circolari</span><span class="sxs-lookup"><span data-stu-id="4a7ef-220">Avoid circular object references</span></span>

<span data-ttu-id="4a7ef-221">Gli oggetti che contengono riferimenti circolari non possono essere serializzati sul client per uno dei seguenti:</span><span class="sxs-lookup"><span data-stu-id="4a7ef-221">Objects that contain circular references can't be serialized on the client for either:</span></span>

* <span data-ttu-id="4a7ef-222">Chiamate al metodo .NET.</span><span class="sxs-lookup"><span data-stu-id="4a7ef-222">.NET method calls.</span></span>
* <span data-ttu-id="4a7ef-223">Chiamate al metodo JavaScript da C# quando il tipo restituito contiene riferimenti circolari.</span><span class="sxs-lookup"><span data-stu-id="4a7ef-223">JavaScript method calls from C# when the return type has circular references.</span></span>

<span data-ttu-id="4a7ef-224">Per ulteriori informazioni, vedere i seguenti problemi:</span><span class="sxs-lookup"><span data-stu-id="4a7ef-224">For more information, see the following issues:</span></span>

* [<span data-ttu-id="4a7ef-225">I riferimenti circolari non sono supportati, accettano due (DotNet/aspnetcore #20525)</span><span class="sxs-lookup"><span data-stu-id="4a7ef-225">Circular references are not supported, take two (dotnet/aspnetcore #20525)</span></span>](https://github.com/dotnet/aspnetcore/issues/20525)
* [<span data-ttu-id="4a7ef-226">Proposta: aggiungere un meccanismo per gestire i riferimenti circolari durante la serializzazione (DotNet/Runtime #30820)</span><span class="sxs-lookup"><span data-stu-id="4a7ef-226">Proposal: Add mechanism to handle circular references when serializing (dotnet/runtime #30820)</span></span>](https://github.com/dotnet/runtime/issues/30820)

::: moniker range=">= aspnetcore-5.0"

## <a name="no-locblazor-javascript-isolation-and-object-references"></a><span data-ttu-id="4a7ef-227">Blazor Isolamento JavaScript e riferimenti a oggetti</span><span class="sxs-lookup"><span data-stu-id="4a7ef-227">Blazor JavaScript isolation and object references</span></span>

<span data-ttu-id="4a7ef-228">Blazor Abilita l'isolamento JavaScript nei [moduli JavaScript](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules)standard.</span><span class="sxs-lookup"><span data-stu-id="4a7ef-228">Blazor enables JavaScript isolation in standard [JavaScript modules](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules).</span></span> <span data-ttu-id="4a7ef-229">L'isolamento JavaScript offre i vantaggi seguenti:</span><span class="sxs-lookup"><span data-stu-id="4a7ef-229">JavaScript isolation provides the following benefits:</span></span>

* <span data-ttu-id="4a7ef-230">Il codice JavaScript importato non inquina più lo spazio dei nomi globale.</span><span class="sxs-lookup"><span data-stu-id="4a7ef-230">Imported JavaScript no longer pollutes the global namespace.</span></span>
* <span data-ttu-id="4a7ef-231">I consumer di una libreria e i componenti non sono necessari per importare il codice JavaScript correlato.</span><span class="sxs-lookup"><span data-stu-id="4a7ef-231">Consumers of a library and components aren't required to import the related JavaScript.</span></span>

<span data-ttu-id="4a7ef-232">Il modulo JavaScript seguente, ad esempio, esporta una funzione JavaScript per visualizzare un prompt del browser:</span><span class="sxs-lookup"><span data-stu-id="4a7ef-232">For example, the following JavaScript module exports a JavaScript function for showing a browser prompt:</span></span>

```javascript
export function showPrompt(message) {
  return prompt(message, 'Type anything here');
}
```

<span data-ttu-id="4a7ef-233">Aggiungere il modulo JavaScript precedente a una libreria .NET come asset Web statico ( `wwwroot/exampleJsInterop.js` ) e quindi importare il modulo nel codice .NET usando il <xref:Microsoft.JSInterop.IJSRuntime> servizio.</span><span class="sxs-lookup"><span data-stu-id="4a7ef-233">Add the preceding JavaScript module to a .NET library as a static web asset (`wwwroot/exampleJsInterop.js`) and then import the module into the .NET code using the <xref:Microsoft.JSInterop.IJSRuntime> service.</span></span> <span data-ttu-id="4a7ef-234">Il servizio viene inserito come `jsRuntime` (non visualizzato) per l'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="4a7ef-234">The service is injected as `jsRuntime` (not shown) for the following example:</span></span>

```csharp
var module = await jsRuntime.InvokeAsync<IJSObjectReference>(
    "import", "./_content/MyComponents/exampleJsInterop.js");
```

<span data-ttu-id="4a7ef-235">L' `import` identificatore nell'esempio precedente è un identificatore speciale usato in modo specifico per l'importazione di un modulo JavaScript.</span><span class="sxs-lookup"><span data-stu-id="4a7ef-235">The `import` identifier in the preceding example is a special identifier used specifically for importing a JavaScript module.</span></span> <span data-ttu-id="4a7ef-236">Specificare il modulo usando il percorso di asset Web statico stabile: `_content/{LIBRARY NAME}/{PATH UNDER WWWROOT}` .</span><span class="sxs-lookup"><span data-stu-id="4a7ef-236">Specify the module using its stable static web asset path: `_content/{LIBRARY NAME}/{PATH UNDER WWWROOT}`.</span></span> <span data-ttu-id="4a7ef-237">Il segnaposto `{LIBRARY NAME}` è il nome della libreria.</span><span class="sxs-lookup"><span data-stu-id="4a7ef-237">The placeholder `{LIBRARY NAME}` is the library name.</span></span> <span data-ttu-id="4a7ef-238">Il segnaposto `{PATH UNDER WWWROOT}` è il percorso dello script in `wwwroot` .</span><span class="sxs-lookup"><span data-stu-id="4a7ef-238">The placeholder `{PATH UNDER WWWROOT}` is the path to the script under `wwwroot`.</span></span>

<span data-ttu-id="4a7ef-239"><xref:Microsoft.JSInterop.IJSRuntime> importa il modulo come `IJSObjectReference` , che rappresenta un riferimento a un oggetto JavaScript dal codice .NET.</span><span class="sxs-lookup"><span data-stu-id="4a7ef-239"><xref:Microsoft.JSInterop.IJSRuntime> imports the module as a `IJSObjectReference`, which represents a reference to a JavaScript object from .NET code.</span></span> <span data-ttu-id="4a7ef-240">Usare `IJSObjectReference` per richiamare le funzioni JavaScript esportate dal modulo:</span><span class="sxs-lookup"><span data-stu-id="4a7ef-240">Use the `IJSObjectReference` to invoke exported JavaScript functions from the module:</span></span>

```csharp
public async ValueTask<string> Prompt(string message)
{
    return await module.InvokeAsync<string>("showPrompt", message);
}
```

<span data-ttu-id="4a7ef-241">`IJSInProcessObjectReference` rappresenta un riferimento a un oggetto JavaScript le cui funzioni possono essere richiamate in modo sincrono.</span><span class="sxs-lookup"><span data-stu-id="4a7ef-241">`IJSInProcessObjectReference` represents a reference to a JavaScript object whose functions can be invoked synchronously.</span></span>

<span data-ttu-id="4a7ef-242">`IJSUnmarshalledObjectReference` rappresenta un riferimento a un oggetto JavaScript le cui funzioni possono essere richiamate senza l'overhead della serializzazione dei dati .NET.</span><span class="sxs-lookup"><span data-stu-id="4a7ef-242">`IJSUnmarshalledObjectReference` represents a reference to an JavaScript object whose functions can be invoked without the overhead of serializing .NET data.</span></span> <span data-ttu-id="4a7ef-243">Questa operazione può essere utilizzata in Blazor WebAssembly quando le prestazioni sono fondamentali:</span><span class="sxs-lookup"><span data-stu-id="4a7ef-243">This can be used in Blazor WebAssembly when performance is crucial:</span></span>

```javascript
window.unmarshalledInstance = {
  helloWorld: function (personNamePointer) {
    const personName = Blazor.platform.readStringField(value, 0);
    return `Hello ${personName}`;
  }
};
```

```csharp
var unmarshalledRuntime = (IJSUnmarshalledRuntime)jsRuntime;
var jsUnmarshalledReference = unmarshalledRuntime
    .InvokeUnmarshalled<IJSUnmarshalledObjectReference>("unmarshalledInstance");

string helloWorldString = jsUnmarshalledReference.InvokeUnmarshalled<string, string>(
    "helloWorld");
```

## <a name="use-of-javascript-libraries-that-render-ui-dom-elements"></a><span data-ttu-id="4a7ef-244">Uso di librerie JavaScript che eseguono il rendering dell'interfaccia utente (elementi DOM)</span><span class="sxs-lookup"><span data-stu-id="4a7ef-244">Use of JavaScript libraries that render UI (DOM elements)</span></span>

<span data-ttu-id="4a7ef-245">In alcuni casi può essere utile usare librerie JavaScript che producono elementi dell'interfaccia utente visibili all'interno del DOM del browser.</span><span class="sxs-lookup"><span data-stu-id="4a7ef-245">Sometimes you may wish to use JavaScript libraries that produce visible user interface elements within the browser DOM.</span></span> <span data-ttu-id="4a7ef-246">A prima vista, questo potrebbe sembrare difficile perché il Blazor sistema di differenziazione si basa sul controllo della struttura ad albero di elementi DOM e si verificano errori se il codice esterno muta l'albero DOM e invalida il meccanismo per l'applicazione di diff.</span><span class="sxs-lookup"><span data-stu-id="4a7ef-246">At first glance, this might seem difficult because Blazor's diffing system relies on having control over the tree of DOM elements and runs into errors if some external code mutates the DOM tree and invalidates its mechanism for applying diffs.</span></span> <span data-ttu-id="4a7ef-247">Questa limitazione non è Blazor specifica.</span><span class="sxs-lookup"><span data-stu-id="4a7ef-247">This isn't a Blazor-specific limitation.</span></span> <span data-ttu-id="4a7ef-248">Lo stesso problema si verifica con qualsiasi framework dell'interfaccia utente basato su diff.</span><span class="sxs-lookup"><span data-stu-id="4a7ef-248">The same challenge occurs with any diff-based UI framework.</span></span>

<span data-ttu-id="4a7ef-249">Fortunatamente, è facile incorporare in modo affidabile l'interfaccia utente generata dall'esterno all'interno di un' Blazor interfaccia utente del componente.</span><span class="sxs-lookup"><span data-stu-id="4a7ef-249">Fortunately, it's straightforward to embed externally-generated UI within a Blazor component UI reliably.</span></span> <span data-ttu-id="4a7ef-250">La tecnica consigliata consiste nel fare in modo che il codice del componente ( `.razor` file) produca un elemento vuoto.</span><span class="sxs-lookup"><span data-stu-id="4a7ef-250">The recommended technique is to have the component's code (`.razor` file) produce an empty element.</span></span> <span data-ttu-id="4a7ef-251">Per quanto Blazor riguarda il sistema diffing, l'elemento è sempre vuoto, quindi il renderer non esegue la ricorsione nell'elemento e lascia il proprio contenuto.</span><span class="sxs-lookup"><span data-stu-id="4a7ef-251">As far as Blazor's diffing system is concerned, the element is always empty, so the renderer does not recurse into the element and instead leaves its contents alone.</span></span> <span data-ttu-id="4a7ef-252">In questo modo è possibile popolare l'elemento con contenuto arbitrario gestito esternamente.</span><span class="sxs-lookup"><span data-stu-id="4a7ef-252">This makes it safe to populate the element with arbitrary externally-managed content.</span></span>

<span data-ttu-id="4a7ef-253">Nell'esempio seguente viene illustrato il concetto.</span><span class="sxs-lookup"><span data-stu-id="4a7ef-253">The following example demonstrates the concept.</span></span> <span data-ttu-id="4a7ef-254">All'interno dell' `if` istruzione quando `firstRender` è `true` , eseguire un'operazione con `myElement` .</span><span class="sxs-lookup"><span data-stu-id="4a7ef-254">Within the `if` statement when `firstRender` is `true`, do something with `myElement`.</span></span> <span data-ttu-id="4a7ef-255">Ad esempio, chiamare una libreria JavaScript esterna per popolarla.</span><span class="sxs-lookup"><span data-stu-id="4a7ef-255">For example, call an external JavaScript library to populate it.</span></span> <span data-ttu-id="4a7ef-256">Blazor lascia solo il contenuto dell'elemento finché questo componente non viene rimosso.</span><span class="sxs-lookup"><span data-stu-id="4a7ef-256">Blazor leaves the element's contents alone until this component itself is removed.</span></span> <span data-ttu-id="4a7ef-257">Quando il componente viene rimosso, viene rimosso anche l'intero sottoalbero DOM del componente.</span><span class="sxs-lookup"><span data-stu-id="4a7ef-257">When the component is removed, the component's entire DOM subtree is also removed.</span></span>

```razor
<h1>Hello! This is a Blazor component rendered at @DateTime.Now</h1>

<div @ref="myElement"></div>

@code {
    HtmlElement myElement;
    
    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            ...
        }
    }
}
```

<span data-ttu-id="4a7ef-258">Per un esempio più dettagliato, si consideri il componente seguente che esegue il rendering di una mappa interattiva usando le [API MapBox Open Source](https://www.mapbox.com/):</span><span class="sxs-lookup"><span data-stu-id="4a7ef-258">As a more detailed example, consider the following component that renders an interactive map using the [open-source Mapbox APIs](https://www.mapbox.com/):</span></span>

```razor
@inject IJSRuntime JS
@implements IAsyncDisposable

<div @ref="mapElement" style='width: 400px; height: 300px;'></div>

<button @onclick="() => ShowAsync(51.454514, -2.587910)">Show Bristol, UK</button>
<button @onclick="() => ShowAsync(35.6762, 139.6503)">Show Tokyo, Japan</button>

@code
{
    ElementReference mapElement;
    IJSObjectReference mapModule;
    IJSObjectReference mapInstance;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            mapModule = await JS.InvokeAsync<IJSObjectReference>(
                "import", "./mapComponent.js");
            mapInstance = await mapModule.InvokeAsync<IJSObjectReference>(
                "addMapToElement", mapElement);
        }
    }

    Task ShowAsync(double latitude, double longitude)
        => mapModule.InvokeVoidAsync("setMapCenter", mapInstance, latitude, 
            longitude).AsTask();

    private async ValueTask IAsyncDisposable.DisposeAsync()
    {
        await mapInstance.DisposeAsync();
        await mapModule.DisposeAsync();
    }
}
```

<span data-ttu-id="4a7ef-259">Il modulo JavaScript corrispondente, che deve essere inserito in `wwwroot/mapComponent.js` , è il seguente:</span><span class="sxs-lookup"><span data-stu-id="4a7ef-259">The corresponding JavaScript module, which should be placed at `wwwroot/mapComponent.js`, is as follows:</span></span>

```javascript
import 'https://api.mapbox.com/mapbox-gl-js/v1.12.0/mapbox-gl.js';

// TO MAKE THE MAP APPEAR YOU MUST ADD YOUR ACCESS TOKEN FROM 
// https://account.mapbox.com
mapboxgl.accessToken = '{ACCESS TOKEN}';

export function addMapToElement(element) {
  return new mapboxgl.Map({
    container: element,
    style: 'mapbox://styles/mapbox/streets-v11',
    center: [-74.5, 40],
    zoom: 9
  });
}

export function setMapCenter(map, latitude, longitude) {
  map.setCenter([longitude, latitude]);
}
```

<span data-ttu-id="4a7ef-260">Nell'esempio precedente, sostituire la stringa `{ACCESS TOKEN}` con un token di accesso valido che è possibile ottenere da https://account.mapbox.com .</span><span class="sxs-lookup"><span data-stu-id="4a7ef-260">In the preceding example, replace the string `{ACCESS TOKEN}` with a valid access token that you can get from https://account.mapbox.com.</span></span>

<span data-ttu-id="4a7ef-261">Per produrre lo stile corretto, aggiungere il seguente tag del foglio di stile alla pagina HTML dell'host ( `index.html` o `_Host.cshtml` ):</span><span class="sxs-lookup"><span data-stu-id="4a7ef-261">To produce correct styling, add the following stylesheet tag to the host HTML page (`index.html` or `_Host.cshtml`):</span></span>

```html
<link rel="stylesheet" href="https://api.mapbox.com/mapbox-gl-js/v1.12.0/mapbox-gl.css" />
```

<span data-ttu-id="4a7ef-262">Nell'esempio precedente viene prodotta un'interfaccia utente mappa interattiva, in cui l'utente:</span><span class="sxs-lookup"><span data-stu-id="4a7ef-262">The preceding example produces an interactive map UI, in which the user:</span></span>

* <span data-ttu-id="4a7ef-263">È possibile trascinare per scorrere o ingrandire.</span><span class="sxs-lookup"><span data-stu-id="4a7ef-263">Can drag to scroll or zoom.</span></span>
* <span data-ttu-id="4a7ef-264">Fare clic sui pulsanti per passare a percorsi predefiniti.</span><span class="sxs-lookup"><span data-stu-id="4a7ef-264">Click buttons to jump to predefined locations.</span></span>

![MapBox Street Map di Tokyo, Giappone con i pulsanti per selezionare Bristol, Regno Unito e Tokyo, Giappone](https://user-images.githubusercontent.com/1101362/94939821-92ef6700-04ca-11eb-858e-fff6df0053ae.png)

<span data-ttu-id="4a7ef-266">I punti chiave da comprendere sono:</span><span class="sxs-lookup"><span data-stu-id="4a7ef-266">The key points to understand are:</span></span>

 * <span data-ttu-id="4a7ef-267">Il `<div>` con `@ref="mapElement"` viene lasciato vuoto per quanto Blazor concerne.</span><span class="sxs-lookup"><span data-stu-id="4a7ef-267">The `<div>` with `@ref="mapElement"` is left empty as far as Blazor is concerned.</span></span> <span data-ttu-id="4a7ef-268">È pertanto sicuro per `mapbox-gl.js` compilarlo e modificarne il contenuto nel tempo.</span><span class="sxs-lookup"><span data-stu-id="4a7ef-268">It's therefore safe for `mapbox-gl.js` to populate it and modify its contents over time.</span></span> <span data-ttu-id="4a7ef-269">Questa tecnica può essere usata con qualsiasi libreria JavaScript che esegua il rendering dell'interfaccia utente.</span><span class="sxs-lookup"><span data-stu-id="4a7ef-269">You can use this technique with any JavaScript library that renders UI.</span></span> <span data-ttu-id="4a7ef-270">È anche possibile incorporare componenti da un Framework di JavaScript SPA di terze parti all'interno dei Blazor componenti, purché non tenti di raggiungere e modificare altre parti della pagina.</span><span class="sxs-lookup"><span data-stu-id="4a7ef-270">You could even embed components from a third-party JavaScript SPA framework inside Blazor components, as long as they don't try to reach out and modify other parts of the page.</span></span> <span data-ttu-id="4a7ef-271">*Non* è sicuro che il codice JavaScript esterno modifichi elementi che Blazor non sono considerati vuoti.</span><span class="sxs-lookup"><span data-stu-id="4a7ef-271">It is *not* safe for external JavaScript code to modify elements that Blazor does not regard as empty.</span></span>
 * <span data-ttu-id="4a7ef-272">Quando si usa questo approccio, tenere presente le regole su come Blazor mantiene o Elimina gli elementi DOM.</span><span class="sxs-lookup"><span data-stu-id="4a7ef-272">When using this approach, bear in mind the rules about how Blazor retains or destroys DOM elements.</span></span> <span data-ttu-id="4a7ef-273">Nell'esempio precedente, il componente gestisce in modo sicuro gli eventi click del pulsante e aggiorna l'istanza della mappa esistente perché gli elementi DOM vengono conservati laddove possibile per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="4a7ef-273">In the preceding example, the component safely handles button click events and updates the existing map instance because DOM elements are retained where possible by default.</span></span> <span data-ttu-id="4a7ef-274">Se si esegue il rendering di un elenco di elementi della mappa dall'interno di un `@foreach` ciclo, si desidera utilizzare `@key` per garantire la conservazione delle istanze dei componenti.</span><span class="sxs-lookup"><span data-stu-id="4a7ef-274">If you were rendering a list of map elements from inside a `@foreach` loop, you want to use `@key` to ensure the preservation of component instances.</span></span> <span data-ttu-id="4a7ef-275">In caso contrario, le modifiche nei dati dell'elenco potrebbero causare la conservazione dello stato delle istanze precedenti in modo non auspicabile da parte delle istanze dei componenti.</span><span class="sxs-lookup"><span data-stu-id="4a7ef-275">Otherwise, changes in the list data could cause component instances to retain the state of previous instances in an undesirable manner.</span></span> <span data-ttu-id="4a7ef-276">Per ulteriori informazioni, vedere [utilizzo @key di per mantenere elementi e componenti](xref:blazor/components/index#use-key-to-control-the-preservation-of-elements-and-components).</span><span class="sxs-lookup"><span data-stu-id="4a7ef-276">For more information, see [using @key to preserve elements and components](xref:blazor/components/index#use-key-to-control-the-preservation-of-elements-and-components).</span></span>

<span data-ttu-id="4a7ef-277">Inoltre, l'esempio precedente Mostra come è possibile incapsulare la logica JavaScript e le dipendenze all'interno di un modulo ES6 e caricarlo dinamicamente usando l' `import` identificatore.</span><span class="sxs-lookup"><span data-stu-id="4a7ef-277">Additionally, the preceding example shows how it's possible to encapsulate JavaScript logic and dependencies within an ES6 module and load it dynamically using the `import` identifier.</span></span> <span data-ttu-id="4a7ef-278">Per ulteriori informazioni, vedere [isolamento JavaScript e riferimenti a oggetti](#blazor-javascript-isolation-and-object-references).</span><span class="sxs-lookup"><span data-stu-id="4a7ef-278">For more information, see [JavaScript isolation and object references](#blazor-javascript-isolation-and-object-references).</span></span>

::: moniker-end

## <a name="size-limits-on-js-interop-calls"></a><span data-ttu-id="4a7ef-279">Limiti delle dimensioni per le chiamate di interoperabilità JS</span><span class="sxs-lookup"><span data-stu-id="4a7ef-279">Size limits on JS interop calls</span></span>

<span data-ttu-id="4a7ef-280">In Blazor WebAssembly il Framework non impone limiti per le dimensioni degli input e degli output delle chiamate di interoperabilità js.</span><span class="sxs-lookup"><span data-stu-id="4a7ef-280">In Blazor WebAssembly, the framework doesn't impose limits on the size of inputs and outputs of JS interop calls.</span></span>

<span data-ttu-id="4a7ef-281">In Blazor Server il risultato di una chiamata di interoperabilità js è limitato dalla dimensione massima del payload applicata da SignalR ( <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize> ), il cui valore predefinito è 32 KB.</span><span class="sxs-lookup"><span data-stu-id="4a7ef-281">In Blazor Server, the result of a JS interop call is limited by the maximum payload size enforced by SignalR (<xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize>), which defaults to 32 KB.</span></span> <span data-ttu-id="4a7ef-282">Le applicazioni che tentano di rispondere a una chiamata di interoperabilità JS con un payload maggiore di <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize> generano un errore.</span><span class="sxs-lookup"><span data-stu-id="4a7ef-282">Applications that attempt to respond to a JS interop call with a payload larger than <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize> throw an error.</span></span> <span data-ttu-id="4a7ef-283">È possibile configurare un limite maggiore modificando <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize> .</span><span class="sxs-lookup"><span data-stu-id="4a7ef-283">A larger limit can be configured by modifying <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize>.</span></span> <span data-ttu-id="4a7ef-284">Nell'esempio seguente viene impostata la dimensione massima del messaggio di ricezione su 64 KB (64 \* 1024 \* 1024):</span><span class="sxs-lookup"><span data-stu-id="4a7ef-284">The following example sets the maximum receive message size to 64 KB (64 \* 1024 \* 1024):</span></span>

```csharp
services.AddServerSideBlazor()
   .AddHubOptions(options => options.MaximumReceiveMessageSize = 64 * 1024 * 1024);
```

<span data-ttu-id="4a7ef-285">L'aumento del SignalR limite comporta il costo di richiedere l'utilizzo di più risorse del server ed espone il server a maggiori rischi da parte di utenti malintenzionati.</span><span class="sxs-lookup"><span data-stu-id="4a7ef-285">Increasing the SignalR limit comes at the cost of requiring the use of more server resources, and it exposes the server to increased risks from a malicious user.</span></span> <span data-ttu-id="4a7ef-286">Inoltre, la lettura di una grande quantità di contenuto in memoria come stringhe o matrici di byte può comportare anche l'utilizzo di allocazioni che funzionano in modo non corretto con la Garbage Collector, con conseguente penalizzazione delle prestazioni.</span><span class="sxs-lookup"><span data-stu-id="4a7ef-286">Additionally, reading a large amount of content in to memory as strings or byte arrays can also result in allocations that work poorly with the garbage collector, resulting in additional performance penalties.</span></span> <span data-ttu-id="4a7ef-287">Un'opzione per la lettura di payload di grandi dimensioni consiste nel considerare l'invio del contenuto in blocchi più piccoli e l'elaborazione del payload come <xref:System.IO.Stream> .</span><span class="sxs-lookup"><span data-stu-id="4a7ef-287">One option for reading large payloads is to consider sending the content in smaller chunks and processing the payload as a <xref:System.IO.Stream>.</span></span> <span data-ttu-id="4a7ef-288">Può essere usato durante la lettura di payload JSON di grandi dimensioni o se i dati sono disponibili in JavaScript come byte non elaborati.</span><span class="sxs-lookup"><span data-stu-id="4a7ef-288">This can be used when reading large JSON payloads or if data is available in JavaScript as raw bytes.</span></span> <span data-ttu-id="4a7ef-289">Per un esempio che illustra l'invio di payload binari di grandi dimensioni in Blazor Server che usa tecniche simili al `InputFile` componente, vedere l' [app di esempio binario Submit](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/BinarySubmit).</span><span class="sxs-lookup"><span data-stu-id="4a7ef-289">For an example that demonstrates sending large binary payloads in Blazor Server that uses techniques similar to the `InputFile` component, see the [Binary Submit sample app](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/BinarySubmit).</span></span>

<span data-ttu-id="4a7ef-290">Quando si sviluppa codice che trasferisce una grande quantità di dati tra JavaScript e, tenere presenti le linee guida seguenti Blazor :</span><span class="sxs-lookup"><span data-stu-id="4a7ef-290">Consider the following guidance when developing code that transfers a large amount of data between JavaScript and Blazor:</span></span>

* <span data-ttu-id="4a7ef-291">Sezionare i dati in parti più piccole e inviare i segmenti di dati in sequenza fino a quando non vengono ricevuti tutti i dati dal server.</span><span class="sxs-lookup"><span data-stu-id="4a7ef-291">Slice the data into smaller pieces, and send the data segments sequentially until all of the data is received by the server.</span></span>
* <span data-ttu-id="4a7ef-292">Non allocare oggetti di grandi dimensioni in codice JavaScript e C#.</span><span class="sxs-lookup"><span data-stu-id="4a7ef-292">Don't allocate large objects in JavaScript and C# code.</span></span>
* <span data-ttu-id="4a7ef-293">Non bloccare il thread principale dell'interfaccia utente per periodi prolungati durante l'invio o la ricezione di dati.</span><span class="sxs-lookup"><span data-stu-id="4a7ef-293">Don't block the main UI thread for long periods when sending or receiving data.</span></span>
* <span data-ttu-id="4a7ef-294">Liberare la memoria utilizzata quando il processo viene completato o annullato.</span><span class="sxs-lookup"><span data-stu-id="4a7ef-294">Free any memory consumed when the process is completed or cancelled.</span></span>
* <span data-ttu-id="4a7ef-295">Per motivi di sicurezza, applicare i seguenti requisiti aggiuntivi:</span><span class="sxs-lookup"><span data-stu-id="4a7ef-295">Enforce the following additional requirements for security purposes:</span></span>
  * <span data-ttu-id="4a7ef-296">Dichiarare le dimensioni massime di file o dati che è possibile passare.</span><span class="sxs-lookup"><span data-stu-id="4a7ef-296">Declare the maximum file or data size that can be passed.</span></span>
  * <span data-ttu-id="4a7ef-297">Dichiarare la velocità di caricamento minima dal client al server.</span><span class="sxs-lookup"><span data-stu-id="4a7ef-297">Declare the minimum upload rate from the client to the server.</span></span>
* <span data-ttu-id="4a7ef-298">Dopo la ricezione dei dati da parte del server, i dati possono essere:</span><span class="sxs-lookup"><span data-stu-id="4a7ef-298">After the data is received by the server, the data can be:</span></span>
  * <span data-ttu-id="4a7ef-299">Archiviati temporaneamente in un buffer di memoria fino a quando non vengono raccolti tutti i segmenti.</span><span class="sxs-lookup"><span data-stu-id="4a7ef-299">Temporarily stored in a memory buffer until all of the segments are collected.</span></span>
  * <span data-ttu-id="4a7ef-300">Utilizzato immediatamente.</span><span class="sxs-lookup"><span data-stu-id="4a7ef-300">Consumed immediately.</span></span> <span data-ttu-id="4a7ef-301">Ad esempio, i dati possono essere archiviati immediatamente in un database o scritti su disco durante la ricezione di ogni segmento.</span><span class="sxs-lookup"><span data-stu-id="4a7ef-301">For example, the data can be stored immediately in a database or written to disk as each segment is received.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4a7ef-302">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="4a7ef-302">Additional resources</span></span>

* <xref:blazor/call-dotnet-from-javascript>
* [<span data-ttu-id="4a7ef-303">Esempio di InteropComponent. Razor (repository GitHub DotNet/AspNetCore, Branch versione 3,1)</span><span class="sxs-lookup"><span data-stu-id="4a7ef-303">InteropComponent.razor example (dotnet/AspNetCore GitHub repository, 3.1 release branch)</span></span>](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
