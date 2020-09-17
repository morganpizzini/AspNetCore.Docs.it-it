---
title: Chiamare funzioni JavaScript da metodi .NET in ASP.NET Core Blazor
author: guardrex
description: Informazioni su come richiamare funzioni JavaScript da metodi .NET nelle Blazor app.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/17/2020
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
ms.openlocfilehash: a62462e3a0a2366a8662573ada5d2e7589c14c0d
ms.sourcegitcommit: 24106b7ffffc9fff410a679863e28aeb2bbe5b7e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/17/2020
ms.locfileid: "90722475"
---
# <a name="call-javascript-functions-from-net-methods-in-aspnet-core-no-locblazor"></a><span data-ttu-id="05075-103">Chiamare funzioni JavaScript da metodi .NET in ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="05075-103">Call JavaScript functions from .NET methods in ASP.NET Core Blazor</span></span>

<span data-ttu-id="05075-104">Di [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27)e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="05075-104">By [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="05075-105">Un' Blazor app può richiamare funzioni JavaScript da metodi .NET e metodi .NET da funzioni JavaScript.</span><span class="sxs-lookup"><span data-stu-id="05075-105">A Blazor app can invoke JavaScript functions from .NET methods and .NET methods from JavaScript functions.</span></span> <span data-ttu-id="05075-106">Questi scenari sono detti *interoperabilità JavaScript (interoperabilità* *JS*).</span><span class="sxs-lookup"><span data-stu-id="05075-106">These scenarios are called *JavaScript interoperability* (*JS interop*).</span></span>

<span data-ttu-id="05075-107">Questo articolo illustra come richiamare funzioni JavaScript da .NET.</span><span class="sxs-lookup"><span data-stu-id="05075-107">This article covers invoking JavaScript functions from .NET.</span></span> <span data-ttu-id="05075-108">Per informazioni su come chiamare i metodi .NET da JavaScript, vedere <xref:blazor/call-dotnet-from-javascript> .</span><span class="sxs-lookup"><span data-stu-id="05075-108">For information on how to call .NET methods from JavaScript, see <xref:blazor/call-dotnet-from-javascript>.</span></span>

<span data-ttu-id="05075-109">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="05075-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="05075-110">Per chiamare JavaScript da .NET, usare l' <xref:Microsoft.JSInterop.IJSRuntime> astrazione.</span><span class="sxs-lookup"><span data-stu-id="05075-110">To call into JavaScript from .NET, use the <xref:Microsoft.JSInterop.IJSRuntime> abstraction.</span></span> <span data-ttu-id="05075-111">Per rilasciare chiamate di interoperabilità JS, inserire l' <xref:Microsoft.JSInterop.IJSRuntime> astrazione nel componente.</span><span class="sxs-lookup"><span data-stu-id="05075-111">To issue JS interop calls, inject the <xref:Microsoft.JSInterop.IJSRuntime> abstraction in your component.</span></span> <span data-ttu-id="05075-112"><xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> accetta un identificatore per la funzione JavaScript che si vuole richiamare insieme a un numero qualsiasi di argomenti serializzabili in JSON.</span><span class="sxs-lookup"><span data-stu-id="05075-112"><xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> takes an identifier for the JavaScript function that you wish to invoke along with any number of JSON-serializable arguments.</span></span> <span data-ttu-id="05075-113">L'identificatore della funzione è relativo all'ambito globale ( `window` ).</span><span class="sxs-lookup"><span data-stu-id="05075-113">The function identifier is relative to the global scope (`window`).</span></span> <span data-ttu-id="05075-114">Se si desidera chiamare `window.someScope.someFunction` , l'identificatore è `someScope.someFunction` .</span><span class="sxs-lookup"><span data-stu-id="05075-114">If you wish to call `window.someScope.someFunction`, the identifier is `someScope.someFunction`.</span></span> <span data-ttu-id="05075-115">Non è necessario registrare la funzione prima che venga chiamata.</span><span class="sxs-lookup"><span data-stu-id="05075-115">There's no need to register the function before it's called.</span></span> <span data-ttu-id="05075-116">Il tipo restituito `T` deve anche essere serializzabile in JSON.</span><span class="sxs-lookup"><span data-stu-id="05075-116">The return type `T` must also be JSON serializable.</span></span> <span data-ttu-id="05075-117">`T` deve corrispondere al tipo .NET che meglio esegue il mapping al tipo JSON restituito.</span><span class="sxs-lookup"><span data-stu-id="05075-117">`T` should match the .NET type that best maps to the JSON type returned.</span></span>

<span data-ttu-id="05075-118">Per Blazor Server le app con il prerendering abilitato, non è possibile chiamare JavaScript durante il prerendering iniziale.</span><span class="sxs-lookup"><span data-stu-id="05075-118">For Blazor Server apps with prerendering enabled, calling into JavaScript isn't possible during the initial prerendering.</span></span> <span data-ttu-id="05075-119">È necessario rinviare le chiamate di interoperabilità JavaScript finché non viene stabilita la connessione con il browser.</span><span class="sxs-lookup"><span data-stu-id="05075-119">JavaScript interop calls must be deferred until after the connection with the browser is established.</span></span> <span data-ttu-id="05075-120">Per altre informazioni, vedere la sezione [rilevare quando un' Blazor Server app è prerendering](#detect-when-a-blazor-server-app-is-prerendering) .</span><span class="sxs-lookup"><span data-stu-id="05075-120">For more information, see the [Detect when a Blazor Server app is prerendering](#detect-when-a-blazor-server-app-is-prerendering) section.</span></span>

<span data-ttu-id="05075-121">L'esempio seguente è basato su [`TextDecoder`](https://developer.mozilla.org/docs/Web/API/TextDecoder) , un decodificatore basato su JavaScript.</span><span class="sxs-lookup"><span data-stu-id="05075-121">The following example is based on [`TextDecoder`](https://developer.mozilla.org/docs/Web/API/TextDecoder), a JavaScript-based decoder.</span></span> <span data-ttu-id="05075-122">Nell'esempio viene illustrato come richiamare una funzione JavaScript da un metodo C# che Scarica un requisito dal codice dello sviluppatore a un'API JavaScript esistente.</span><span class="sxs-lookup"><span data-stu-id="05075-122">The example demonstrates how to invoke a JavaScript function from a C# method that offloads a requirement from developer code to an existing JavaScript API.</span></span> <span data-ttu-id="05075-123">La funzione JavaScript accetta una matrice di byte da un metodo C#, decodifica la matrice e restituisce il testo al componente per la visualizzazione.</span><span class="sxs-lookup"><span data-stu-id="05075-123">The JavaScript function accepts a byte array from a C# method, decodes the array, and returns the text to the component for display.</span></span>

<span data-ttu-id="05075-124">All'interno dell' `<head>` elemento di `wwwroot/index.html` ( Blazor WebAssembly ) o `Pages/_Host.cshtml` ( Blazor Server ), fornire una funzione JavaScript che usi `TextDecoder` per decodificare una matrice passata e restituire il valore decodificato:</span><span class="sxs-lookup"><span data-stu-id="05075-124">Inside the `<head>` element of `wwwroot/index.html` (Blazor WebAssembly) or `Pages/_Host.cshtml` (Blazor Server), provide a JavaScript function that uses `TextDecoder` to decode a passed array and return the decoded value:</span></span>

[!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-convertarray.html)]

<span data-ttu-id="05075-125">Il codice JavaScript, ad esempio il codice illustrato nell'esempio precedente, può anche essere caricato da un file JavaScript ( `.js` ) con un riferimento al file script:</span><span class="sxs-lookup"><span data-stu-id="05075-125">JavaScript code, such as the code shown in the preceding example, can also be loaded from a JavaScript file (`.js`) with a reference to the script file:</span></span>

```html
<script src="exampleJsInterop.js"></script>
```

<span data-ttu-id="05075-126">Il componente seguente:</span><span class="sxs-lookup"><span data-stu-id="05075-126">The following component:</span></span>

* <span data-ttu-id="05075-127">Richiama la `convertArray` funzione JavaScript usando `JSRuntime` quando viene selezionato un pulsante del componente ( **`Convert Array`** ).</span><span class="sxs-lookup"><span data-stu-id="05075-127">Invokes the `convertArray` JavaScript function using `JSRuntime` when a component button (**`Convert Array`**) is selected.</span></span>
* <span data-ttu-id="05075-128">Dopo la chiamata della funzione JavaScript, la matrice passata viene convertita in una stringa.</span><span class="sxs-lookup"><span data-stu-id="05075-128">After the JavaScript function is called, the passed array is converted into a string.</span></span> <span data-ttu-id="05075-129">La stringa viene restituita al componente per la visualizzazione.</span><span class="sxs-lookup"><span data-stu-id="05075-129">The string is returned to the component for display.</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/call-js-example.razor?highlight=2,34-35)]

## <a name="ijsruntime"></a><span data-ttu-id="05075-130">IJSRuntime</span><span class="sxs-lookup"><span data-stu-id="05075-130">IJSRuntime</span></span>

<span data-ttu-id="05075-131">Per usare l' <xref:Microsoft.JSInterop.IJSRuntime> astrazione, adottare uno degli approcci seguenti:</span><span class="sxs-lookup"><span data-stu-id="05075-131">To use the <xref:Microsoft.JSInterop.IJSRuntime> abstraction, adopt any of the following approaches:</span></span>

* <span data-ttu-id="05075-132">Inserire l' <xref:Microsoft.JSInterop.IJSRuntime> astrazione nel Razor componente ( `.razor` ):</span><span class="sxs-lookup"><span data-stu-id="05075-132">Inject the <xref:Microsoft.JSInterop.IJSRuntime> abstraction into the Razor component (`.razor`):</span></span>

  [!code-razor[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction.razor?highlight=1)]

  <span data-ttu-id="05075-133">All'interno dell' `<head>` elemento di `wwwroot/index.html` ( Blazor WebAssembly ) o `Pages/_Host.cshtml` ( Blazor Server ) specificare una `handleTickerChanged` funzione JavaScript.</span><span class="sxs-lookup"><span data-stu-id="05075-133">Inside the `<head>` element of `wwwroot/index.html` (Blazor WebAssembly) or `Pages/_Host.cshtml` (Blazor Server), provide a `handleTickerChanged` JavaScript function.</span></span> <span data-ttu-id="05075-134">La funzione viene chiamata con <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> e non restituisce un valore:</span><span class="sxs-lookup"><span data-stu-id="05075-134">The function is called with <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> and doesn't return a value:</span></span>

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged1.html)]

* <span data-ttu-id="05075-135">Inserire l' <xref:Microsoft.JSInterop.IJSRuntime> astrazione in una classe ( `.cs` ):</span><span class="sxs-lookup"><span data-stu-id="05075-135">Inject the <xref:Microsoft.JSInterop.IJSRuntime> abstraction into a class (`.cs`):</span></span>

  [!code-csharp[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction-class.cs?highlight=5)]

  <span data-ttu-id="05075-136">All'interno dell' `<head>` elemento di `wwwroot/index.html` ( Blazor WebAssembly ) o `Pages/_Host.cshtml` ( Blazor Server ) specificare una `handleTickerChanged` funzione JavaScript.</span><span class="sxs-lookup"><span data-stu-id="05075-136">Inside the `<head>` element of `wwwroot/index.html` (Blazor WebAssembly) or `Pages/_Host.cshtml` (Blazor Server), provide a `handleTickerChanged` JavaScript function.</span></span> <span data-ttu-id="05075-137">La funzione viene chiamata con `JSRuntime.InvokeAsync` e restituisce un valore:</span><span class="sxs-lookup"><span data-stu-id="05075-137">The function is called with `JSRuntime.InvokeAsync` and returns a value:</span></span>

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged2.html)]

* <span data-ttu-id="05075-138">Per la generazione di contenuto dinamico con [BuildRenderTree](xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic), usare l' `[Inject]` attributo:</span><span class="sxs-lookup"><span data-stu-id="05075-138">For dynamic content generation with [BuildRenderTree](xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic), use the `[Inject]` attribute:</span></span>

  ```razor
  [Inject]
  IJSRuntime JSRuntime { get; set; }
  ```

<span data-ttu-id="05075-139">Nell'app di esempio lato client che accompagna questo argomento sono disponibili due funzioni JavaScript per l'app che interagiscono con il DOM per ricevere l'input dell'utente e visualizzare un messaggio di benvenuto:</span><span class="sxs-lookup"><span data-stu-id="05075-139">In the client-side sample app that accompanies this topic, two JavaScript functions are available to the app that interact with the DOM to receive user input and display a welcome message:</span></span>

* <span data-ttu-id="05075-140">`showPrompt`: Genera un messaggio di richiesta per accettare l'input dell'utente (il nome dell'utente) e restituisce il nome al chiamante.</span><span class="sxs-lookup"><span data-stu-id="05075-140">`showPrompt`: Produces a prompt to accept user input (the user's name) and returns the name to the caller.</span></span>
* <span data-ttu-id="05075-141">`displayWelcome`: Assegna un messaggio di benvenuto dal chiamante a un oggetto DOM con un valore `id` di `welcome` .</span><span class="sxs-lookup"><span data-stu-id="05075-141">`displayWelcome`: Assigns a welcome message from the caller to a DOM object with an `id` of `welcome`.</span></span>

<span data-ttu-id="05075-142">`wwwroot/exampleJsInterop.js`:</span><span class="sxs-lookup"><span data-stu-id="05075-142">`wwwroot/exampleJsInterop.js`:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=2-7)]

<span data-ttu-id="05075-143">Inserire il `<script>` tag che fa riferimento al file JavaScript nel file `wwwroot/index.html` ( Blazor WebAssembly ) o nel `Pages/_Host.cshtml` file ( Blazor Server ).</span><span class="sxs-lookup"><span data-stu-id="05075-143">Place the `<script>` tag that references the JavaScript file in the `wwwroot/index.html` file (Blazor WebAssembly) or `Pages/_Host.cshtml` file (Blazor Server).</span></span>

<span data-ttu-id="05075-144">`wwwroot/index.html` (Blazor WebAssembly):</span><span class="sxs-lookup"><span data-stu-id="05075-144">`wwwroot/index.html` (Blazor WebAssembly):</span></span>

[!code-html[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/index.html?highlight=22)]

<span data-ttu-id="05075-145">`Pages/_Host.cshtml` (Blazor Server):</span><span class="sxs-lookup"><span data-stu-id="05075-145">`Pages/_Host.cshtml` (Blazor Server):</span></span>

[!code-cshtml[](./common/samples/3.x/BlazorServerSample/Pages/_Host.cshtml?highlight=35)]

<span data-ttu-id="05075-146">Non inserire un `<script>` tag in un file di componente perché il `<script>` tag non può essere aggiornato dinamicamente.</span><span class="sxs-lookup"><span data-stu-id="05075-146">Don't place a `<script>` tag in a component file because the `<script>` tag can't be updated dynamically.</span></span>

<span data-ttu-id="05075-147">I metodi .NET interoperano con le funzioni JavaScript nel `exampleJsInterop.js` file chiamando <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="05075-147">.NET methods interop with the JavaScript functions in the `exampleJsInterop.js` file by calling <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType>.</span></span>

<span data-ttu-id="05075-148">L' <xref:Microsoft.JSInterop.IJSRuntime> astrazione è asincrona per consentire gli Blazor Server scenari.</span><span class="sxs-lookup"><span data-stu-id="05075-148">The <xref:Microsoft.JSInterop.IJSRuntime> abstraction is asynchronous to allow for Blazor Server scenarios.</span></span> <span data-ttu-id="05075-149">Se l'app è un' Blazor WebAssembly app e si vuole richiamare una funzione JavaScript in modo sincrono, <xref:Microsoft.JSInterop.IJSInProcessRuntime> abbandono e chiamano <xref:Microsoft.JSInterop.IJSInProcessRuntime.Invoke%2A> invece.</span><span class="sxs-lookup"><span data-stu-id="05075-149">If the app is a Blazor WebAssembly app and you want to invoke a JavaScript function synchronously, downcast to <xref:Microsoft.JSInterop.IJSInProcessRuntime> and call <xref:Microsoft.JSInterop.IJSInProcessRuntime.Invoke%2A> instead.</span></span> <span data-ttu-id="05075-150">È consigliabile che la maggior parte delle librerie di interoperabilità JS usi le API asincrone per assicurarsi che le librerie siano disponibili in tutti gli scenari.</span><span class="sxs-lookup"><span data-stu-id="05075-150">We recommend that most JS interop libraries use the async APIs to ensure that the libraries are available in all scenarios.</span></span>

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="05075-151">Per abilitare l'isolamento JavaScript nei [moduli JavaScript](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules)standard, vedere la sezione [ Blazor riferimenti a oggetti e isolamento JavaScript](#blazor-javascript-isolation-and-object-references) .</span><span class="sxs-lookup"><span data-stu-id="05075-151">To enable JavaScript isolation in standard [JavaScript modules](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules), see the [Blazor JavaScript isolation and object references](#blazor-javascript-isolation-and-object-references) section.</span></span>

::: moniker-end

<span data-ttu-id="05075-152">L'app di esempio include un componente per dimostrare l'interoperabilità JS.</span><span class="sxs-lookup"><span data-stu-id="05075-152">The sample app includes a component to demonstrate JS interop.</span></span> <span data-ttu-id="05075-153">Il componente:</span><span class="sxs-lookup"><span data-stu-id="05075-153">The component:</span></span>

* <span data-ttu-id="05075-154">Riceve l'input dell'utente tramite un prompt di JavaScript.</span><span class="sxs-lookup"><span data-stu-id="05075-154">Receives user input via a JavaScript prompt.</span></span>
* <span data-ttu-id="05075-155">Restituisce il testo al componente per l'elaborazione.</span><span class="sxs-lookup"><span data-stu-id="05075-155">Returns the text to the component for processing.</span></span>
* <span data-ttu-id="05075-156">Chiama una seconda funzione JavaScript che interagisce con il DOM per visualizzare un messaggio di benvenuto.</span><span class="sxs-lookup"><span data-stu-id="05075-156">Calls a second JavaScript function that interacts with the DOM to display a welcome message.</span></span>

<span data-ttu-id="05075-157">`Pages/JsInterop.razor`:</span><span class="sxs-lookup"><span data-stu-id="05075-157">`Pages/JsInterop.razor`:</span></span>

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

<span data-ttu-id="05075-158">Il segnaposto `{APP ASSEMBLY}` è il nome dell'assembly dell'app (ad esempio, `BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="05075-158">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

1. <span data-ttu-id="05075-159">Quando `TriggerJsPrompt` viene eseguito selezionando il pulsante del componente **`Trigger JavaScript Prompt`** , `showPrompt` viene chiamata la funzione JavaScript fornita nel `wwwroot/exampleJsInterop.js` file.</span><span class="sxs-lookup"><span data-stu-id="05075-159">When `TriggerJsPrompt` is executed by selecting the component's **`Trigger JavaScript Prompt`** button, the JavaScript `showPrompt` function provided in the `wwwroot/exampleJsInterop.js` file is called.</span></span>
1. <span data-ttu-id="05075-160">La `showPrompt` funzione accetta l'input dell'utente (il nome dell'utente), che è codificato in HTML e restituito al componente.</span><span class="sxs-lookup"><span data-stu-id="05075-160">The `showPrompt` function accepts user input (the user's name), which is HTML-encoded and returned to the component.</span></span> <span data-ttu-id="05075-161">Il componente archivia il nome dell'utente in una variabile locale, `name` .</span><span class="sxs-lookup"><span data-stu-id="05075-161">The component stores the user's name in a local variable, `name`.</span></span>
1. <span data-ttu-id="05075-162">La stringa archiviata in `name` viene incorporata in un messaggio di benvenuto, che viene passato a una funzione JavaScript, `displayWelcome` , che esegue il rendering del messaggio di benvenuto in un tag di intestazione.</span><span class="sxs-lookup"><span data-stu-id="05075-162">The string stored in `name` is incorporated into a welcome message, which is passed to a JavaScript function, `displayWelcome`, which renders the welcome message into a heading tag.</span></span>

## <a name="call-a-void-javascript-function"></a><span data-ttu-id="05075-163">Chiamare una funzione JavaScript void</span><span class="sxs-lookup"><span data-stu-id="05075-163">Call a void JavaScript function</span></span>

<span data-ttu-id="05075-164">Le funzioni JavaScript che restituiscono [void (0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) o [undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined) vengono chiamate con <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="05075-164">JavaScript functions that return [void(0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) or [undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined) are called with <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType>.</span></span>

## <a name="detect-when-a-no-locblazor-server-app-is-prerendering"></a><span data-ttu-id="05075-165">Rileva quando viene eseguito il Blazor Server prerendering di un'app</span><span class="sxs-lookup"><span data-stu-id="05075-165">Detect when a Blazor Server app is prerendering</span></span>
 
[!INCLUDE[](~/includes/blazor-prerendering.md)]

## <a name="capture-references-to-elements"></a><span data-ttu-id="05075-166">Acquisisci riferimenti a elementi</span><span class="sxs-lookup"><span data-stu-id="05075-166">Capture references to elements</span></span>

<span data-ttu-id="05075-167">Alcuni scenari di interoperabilità JS richiedono riferimenti agli elementi HTML.</span><span class="sxs-lookup"><span data-stu-id="05075-167">Some JS interop scenarios require references to HTML elements.</span></span> <span data-ttu-id="05075-168">Ad esempio, una libreria dell'interfaccia utente può richiedere un riferimento a un elemento per l'inizializzazione o potrebbe essere necessario chiamare API simili a quelle di un elemento, ad esempio `focus` o `play` .</span><span class="sxs-lookup"><span data-stu-id="05075-168">For example, a UI library may require an element reference for initialization, or you might need to call command-like APIs on an element, such as `focus` or `play`.</span></span>

<span data-ttu-id="05075-169">Acquisire i riferimenti agli elementi HTML in un componente usando l'approccio seguente:</span><span class="sxs-lookup"><span data-stu-id="05075-169">Capture references to HTML elements in a component using the following approach:</span></span>

* <span data-ttu-id="05075-170">Aggiungere un `@ref` attributo all'elemento HTML.</span><span class="sxs-lookup"><span data-stu-id="05075-170">Add an `@ref` attribute to the HTML element.</span></span>
* <span data-ttu-id="05075-171">Definire un campo di tipo <xref:Microsoft.AspNetCore.Components.ElementReference> il cui nome corrisponde al valore dell' `@ref` attributo.</span><span class="sxs-lookup"><span data-stu-id="05075-171">Define a field of type <xref:Microsoft.AspNetCore.Components.ElementReference> whose name matches the value of the `@ref` attribute.</span></span>

<span data-ttu-id="05075-172">Nell'esempio seguente viene illustrata l'acquisizione di un riferimento all' `username` `<input>` elemento:</span><span class="sxs-lookup"><span data-stu-id="05075-172">The following example shows capturing a reference to the `username` `<input>` element:</span></span>

```razor
<input @ref="username" ... />

@code {
    ElementReference username;
}
```

> [!WARNING]
> <span data-ttu-id="05075-173">Usare solo un riferimento a un elemento per mutare il contenuto di un elemento vuoto che non interagisce con Blazor .</span><span class="sxs-lookup"><span data-stu-id="05075-173">Only use an element reference to mutate the contents of an empty element that doesn't interact with Blazor.</span></span> <span data-ttu-id="05075-174">Questo scenario è utile quando un'API di terze parti fornisce contenuto all'elemento.</span><span class="sxs-lookup"><span data-stu-id="05075-174">This scenario is useful when a third-party API supplies content to the element.</span></span> <span data-ttu-id="05075-175">Poiché Blazor non interagisce con l'elemento, non è possibile che si verifichi un conflitto tra Blazor la rappresentazione dell'elemento e il Dom.</span><span class="sxs-lookup"><span data-stu-id="05075-175">Because Blazor doesn't interact with the element, there's no possibility of a conflict between Blazor's representation of the element and the DOM.</span></span>
>
> <span data-ttu-id="05075-176">Nell'esempio seguente è *pericoloso* modificare il contenuto dell'elenco non ordinato ( `ul` ) perché Blazor interagisce con il Dom per popolare gli elementi elenco di questo elemento ( `<li>` ):</span><span class="sxs-lookup"><span data-stu-id="05075-176">In the following example, it's *dangerous* to mutate the contents of the unordered list (`ul`) because Blazor interacts with the DOM to populate this element's list items (`<li>`):</span></span>
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
> <span data-ttu-id="05075-177">Se l'interoperabilità JS modifica il contenuto dell'elemento `MyList` e Blazor tenta di applicare differenze all'elemento, le differenze non corrispondono al Dom.</span><span class="sxs-lookup"><span data-stu-id="05075-177">If JS interop mutates the contents of element `MyList` and Blazor attempts to apply diffs to the element, the diffs won't match the DOM.</span></span>

<span data-ttu-id="05075-178">Per quanto riguarda il codice .NET, un <xref:Microsoft.AspNetCore.Components.ElementReference> è un handle opaco.</span><span class="sxs-lookup"><span data-stu-id="05075-178">As far as .NET code is concerned, an <xref:Microsoft.AspNetCore.Components.ElementReference> is an opaque handle.</span></span> <span data-ttu-id="05075-179">L' *unica* cosa che è possibile fare con <xref:Microsoft.AspNetCore.Components.ElementReference> è passarla al codice JavaScript tramite l'interoperabilità js.</span><span class="sxs-lookup"><span data-stu-id="05075-179">The *only* thing you can do with <xref:Microsoft.AspNetCore.Components.ElementReference> is pass it through to JavaScript code via JS interop.</span></span> <span data-ttu-id="05075-180">Quando si esegue questa operazione, il codice sul lato JavaScript riceve un' `HTMLElement` istanza, che può essere usata con le API DOM normali.</span><span class="sxs-lookup"><span data-stu-id="05075-180">When you do so, the JavaScript-side code receives an `HTMLElement` instance, which it can use with normal DOM APIs.</span></span>

<span data-ttu-id="05075-181">Il codice seguente, ad esempio, definisce un metodo di estensione .NET che consente di impostare lo stato attivo su un elemento:</span><span class="sxs-lookup"><span data-stu-id="05075-181">For example, the following code defines a .NET extension method that enables setting the focus on an element:</span></span>

<span data-ttu-id="05075-182">`exampleJsInterop.js`:</span><span class="sxs-lookup"><span data-stu-id="05075-182">`exampleJsInterop.js`:</span></span>

```javascript
window.exampleJsFunctions = {
  focusElement : function (element) {
    element.focus();
  }
}
```

<span data-ttu-id="05075-183">Per chiamare una funzione JavaScript che non restituisce un valore, usare <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="05075-183">To call a JavaScript function that doesn't return a value, use <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="05075-184">Il codice seguente imposta lo stato attivo sull'input del nome utente chiamando la funzione JavaScript precedente con l'acquisizione <xref:Microsoft.AspNetCore.Components.ElementReference> :</span><span class="sxs-lookup"><span data-stu-id="05075-184">The following code sets the focus on the username input by calling the preceding JavaScript function with the captured <xref:Microsoft.AspNetCore.Components.ElementReference>:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component1.razor?highlight=1,3,11-12)]

<span data-ttu-id="05075-185">Per usare un metodo di estensione, creare un metodo di estensione statico che riceva l' <xref:Microsoft.JSInterop.IJSRuntime> istanza:</span><span class="sxs-lookup"><span data-stu-id="05075-185">To use an extension method, create a static extension method that receives the <xref:Microsoft.JSInterop.IJSRuntime> instance:</span></span>

```csharp
public static async Task Focus(this ElementReference elementRef, IJSRuntime jsRuntime)
{
    await jsRuntime.InvokeVoidAsync(
        "exampleJsFunctions.focusElement", elementRef);
}
```

<span data-ttu-id="05075-186">Il `Focus` metodo viene chiamato direttamente nell'oggetto.</span><span class="sxs-lookup"><span data-stu-id="05075-186">The `Focus` method is called directly on the object.</span></span> <span data-ttu-id="05075-187">Nell'esempio seguente si presuppone che il `Focus` metodo sia disponibile dallo `JsInteropClasses` spazio dei nomi:</span><span class="sxs-lookup"><span data-stu-id="05075-187">The following example assumes that the `Focus` method is available from the `JsInteropClasses` namespace:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component2.razor?highlight=1-4,12)]

> [!IMPORTANT]
> <span data-ttu-id="05075-188">La `username` variabile viene popolata solo dopo il rendering del componente.</span><span class="sxs-lookup"><span data-stu-id="05075-188">The `username` variable is only populated after the component is rendered.</span></span> <span data-ttu-id="05075-189">Se un oggetto non popolato <xref:Microsoft.AspNetCore.Components.ElementReference> viene passato al codice JavaScript, il codice JavaScript riceve un valore di `null` .</span><span class="sxs-lookup"><span data-stu-id="05075-189">If an unpopulated <xref:Microsoft.AspNetCore.Components.ElementReference> is passed to JavaScript code, the JavaScript code receives a value of `null`.</span></span> <span data-ttu-id="05075-190">Per modificare i riferimenti agli elementi dopo che il componente ha terminato il rendering (per impostare lo stato attivo iniziale su un elemento), usare i metodi del ciclo di vita dei [ `OnAfterRenderAsync` `OnAfterRender` componenti o](xref:blazor/components/lifecycle#after-component-render).</span><span class="sxs-lookup"><span data-stu-id="05075-190">To manipulate element references after the component has finished rendering (to set the initial focus on an element) use the [`OnAfterRenderAsync` or `OnAfterRender` component lifecycle methods](xref:blazor/components/lifecycle#after-component-render).</span></span>

<span data-ttu-id="05075-191">Quando si utilizzano tipi generici e si restituisce un valore, utilizzare <xref:System.Threading.Tasks.ValueTask%601> :</span><span class="sxs-lookup"><span data-stu-id="05075-191">When working with generic types and returning a value, use <xref:System.Threading.Tasks.ValueTask%601>:</span></span>

```csharp
public static ValueTask<T> GenericMethod<T>(this ElementReference elementRef, 
    IJSRuntime jsRuntime)
{
    return jsRuntime.InvokeAsync<T>(
        "exampleJsFunctions.doSomethingGeneric", elementRef);
}
```

<span data-ttu-id="05075-192">`GenericMethod` viene chiamato direttamente sull'oggetto con un tipo.</span><span class="sxs-lookup"><span data-stu-id="05075-192">`GenericMethod` is called directly on the object with a type.</span></span> <span data-ttu-id="05075-193">Nell'esempio seguente si presuppone che l'oggetto `GenericMethod` sia disponibile dallo `JsInteropClasses` spazio dei nomi:</span><span class="sxs-lookup"><span data-stu-id="05075-193">The following example assumes that the `GenericMethod` is available from the `JsInteropClasses` namespace:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component3.razor?highlight=17)]

## <a name="reference-elements-across-components"></a><span data-ttu-id="05075-194">Elementi di riferimento tra i componenti</span><span class="sxs-lookup"><span data-stu-id="05075-194">Reference elements across components</span></span>

<span data-ttu-id="05075-195">Un oggetto <xref:Microsoft.AspNetCore.Components.ElementReference> è garantito solo valido nel metodo di un componente <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> (e un riferimento a un elemento è `struct` ), pertanto non è possibile passare un riferimento a un elemento tra i componenti.</span><span class="sxs-lookup"><span data-stu-id="05075-195">An <xref:Microsoft.AspNetCore.Components.ElementReference> is only guaranteed valid in a component's <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> method (and an element reference is a `struct`), so an element reference can't be passed between components.</span></span>

<span data-ttu-id="05075-196">Affinché un componente padre renda disponibile un riferimento a un elemento per altri componenti, il componente padre può:</span><span class="sxs-lookup"><span data-stu-id="05075-196">For a parent component to make an element reference available to other components, the parent component can:</span></span>

* <span data-ttu-id="05075-197">Consenti ai componenti figlio di registrare i callback.</span><span class="sxs-lookup"><span data-stu-id="05075-197">Allow child components to register callbacks.</span></span>
* <span data-ttu-id="05075-198">Richiamare i callback registrati durante l' <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> evento con il riferimento all'elemento passato.</span><span class="sxs-lookup"><span data-stu-id="05075-198">Invoke the registered callbacks during the <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> event with the passed element reference.</span></span> <span data-ttu-id="05075-199">Indirettamente, questo approccio consente ai componenti figlio di interagire con il riferimento all'elemento padre.</span><span class="sxs-lookup"><span data-stu-id="05075-199">Indirectly, this approach allows child components to interact with the parent's element reference.</span></span>

<span data-ttu-id="05075-200">Nell' Blazor WebAssembly esempio seguente viene illustrato l'approccio.</span><span class="sxs-lookup"><span data-stu-id="05075-200">The following Blazor WebAssembly example illustrates the approach.</span></span>

<span data-ttu-id="05075-201">Nella `<head>` di `wwwroot/index.html` :</span><span class="sxs-lookup"><span data-stu-id="05075-201">In the `<head>` of `wwwroot/index.html`:</span></span>

```html
<style>
    .red { color: red }
</style>
```

<span data-ttu-id="05075-202">Nella `<body>` di `wwwroot/index.html` :</span><span class="sxs-lookup"><span data-stu-id="05075-202">In the `<body>` of `wwwroot/index.html`:</span></span>

```html
<script>
    function setElementClass(element, className) {
        /** @type {HTMLElement} **/
        var myElement = element;
        myElement.classList.add(className);
    }
</script>
```

<span data-ttu-id="05075-203">`Pages/Index.razor` (componente padre):</span><span class="sxs-lookup"><span data-stu-id="05075-203">`Pages/Index.razor` (parent component):</span></span>

```razor
@page "/"

<h1 @ref="title">Hello, world!</h1>

Welcome to your new app.

<SurveyPrompt Parent="this" Title="How is Blazor working for you?" />
```

<span data-ttu-id="05075-204">`Pages/Index.razor.cs`:</span><span class="sxs-lookup"><span data-stu-id="05075-204">`Pages/Index.razor.cs`:</span></span>

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

<span data-ttu-id="05075-205">Il segnaposto `{APP ASSEMBLY}` è il nome dell'assembly dell'app (ad esempio, `BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="05075-205">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

<span data-ttu-id="05075-206">`Shared/SurveyPrompt.razor` (componente figlio):</span><span class="sxs-lookup"><span data-stu-id="05075-206">`Shared/SurveyPrompt.razor` (child component):</span></span>

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

<span data-ttu-id="05075-207">`Shared/SurveyPrompt.razor.cs`:</span><span class="sxs-lookup"><span data-stu-id="05075-207">`Shared/SurveyPrompt.razor.cs`:</span></span>

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

<span data-ttu-id="05075-208">Il segnaposto `{APP ASSEMBLY}` è il nome dell'assembly dell'app (ad esempio, `BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="05075-208">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

## <a name="harden-js-interop-calls"></a><span data-ttu-id="05075-209">Chiamate di interoperabilità JS di Harden</span><span class="sxs-lookup"><span data-stu-id="05075-209">Harden JS interop calls</span></span>

<span data-ttu-id="05075-210">L'interoperabilità JS potrebbe non riuscire a causa di errori di rete e deve essere considerata inaffidabile.</span><span class="sxs-lookup"><span data-stu-id="05075-210">JS interop may fail due to networking errors and should be treated as unreliable.</span></span> <span data-ttu-id="05075-211">Per impostazione predefinita, un' Blazor Server app timeout delle chiamate di interoperabilità js nel server dopo un minuto.</span><span class="sxs-lookup"><span data-stu-id="05075-211">By default, a Blazor Server app times out JS interop calls on the server after one minute.</span></span> <span data-ttu-id="05075-212">Se un'app può tollerare un timeout più aggressivo, impostare il timeout usando uno degli approcci seguenti:</span><span class="sxs-lookup"><span data-stu-id="05075-212">If an app can tolerate a more aggressive timeout, set the timeout using one of the following approaches:</span></span>

* <span data-ttu-id="05075-213">A livello globale in `Startup.ConfigureServices` specificare il timeout:</span><span class="sxs-lookup"><span data-stu-id="05075-213">Globally in `Startup.ConfigureServices`, specify the timeout:</span></span>

  ```csharp
  services.AddServerSideBlazor(
      options => options.JSInteropDefaultCallTimeout = TimeSpan.FromSeconds({SECONDS}));
  ```

* <span data-ttu-id="05075-214">Per chiamata nel codice componente, una singola chiamata può specificare il timeout:</span><span class="sxs-lookup"><span data-stu-id="05075-214">Per-invocation in component code, a single call can specify the timeout:</span></span>

  ```csharp
  var result = await JSRuntime.InvokeAsync<string>("MyJSOperation", 
      TimeSpan.FromSeconds({SECONDS}), new[] { "Arg1" });
  ```

<span data-ttu-id="05075-215">Per ulteriori informazioni sull'esaurimento delle risorse, vedere <xref:blazor/security/server/threat-mitigation> .</span><span class="sxs-lookup"><span data-stu-id="05075-215">For more information on resource exhaustion, see <xref:blazor/security/server/threat-mitigation>.</span></span>

[!INCLUDE[](~/includes/blazor-share-interop-code.md)]

## <a name="avoid-circular-object-references"></a><span data-ttu-id="05075-216">Evitare riferimenti a oggetti circolari</span><span class="sxs-lookup"><span data-stu-id="05075-216">Avoid circular object references</span></span>

<span data-ttu-id="05075-217">Gli oggetti che contengono riferimenti circolari non possono essere serializzati sul client per uno dei seguenti:</span><span class="sxs-lookup"><span data-stu-id="05075-217">Objects that contain circular references can't be serialized on the client for either:</span></span>

* <span data-ttu-id="05075-218">Chiamate al metodo .NET.</span><span class="sxs-lookup"><span data-stu-id="05075-218">.NET method calls.</span></span>
* <span data-ttu-id="05075-219">Chiamate al metodo JavaScript da C# quando il tipo restituito contiene riferimenti circolari.</span><span class="sxs-lookup"><span data-stu-id="05075-219">JavaScript method calls from C# when the return type has circular references.</span></span>

<span data-ttu-id="05075-220">Per ulteriori informazioni, vedere i seguenti problemi:</span><span class="sxs-lookup"><span data-stu-id="05075-220">For more information, see the following issues:</span></span>

* [<span data-ttu-id="05075-221">I riferimenti circolari non sono supportati, accettano due (DotNet/aspnetcore #20525)</span><span class="sxs-lookup"><span data-stu-id="05075-221">Circular references are not supported, take two (dotnet/aspnetcore #20525)</span></span>](https://github.com/dotnet/aspnetcore/issues/20525)
* [<span data-ttu-id="05075-222">Proposta: aggiungere un meccanismo per gestire i riferimenti circolari durante la serializzazione (DotNet/Runtime #30820)</span><span class="sxs-lookup"><span data-stu-id="05075-222">Proposal: Add mechanism to handle circular references when serializing (dotnet/runtime #30820)</span></span>](https://github.com/dotnet/runtime/issues/30820)

::: moniker range=">= aspnetcore-5.0"

## <a name="no-locblazor-javascript-isolation-and-object-references"></a><span data-ttu-id="05075-223">Blazor Isolamento JavaScript e riferimenti a oggetti</span><span class="sxs-lookup"><span data-stu-id="05075-223">Blazor JavaScript isolation and object references</span></span>

<span data-ttu-id="05075-224">Blazor Abilita l'isolamento JavaScript nei [moduli JavaScript](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules)standard.</span><span class="sxs-lookup"><span data-stu-id="05075-224">Blazor enables JavaScript isolation in standard [JavaScript modules](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules).</span></span> <span data-ttu-id="05075-225">L'isolamento JavaScript offre i vantaggi seguenti:</span><span class="sxs-lookup"><span data-stu-id="05075-225">JavaScript isolation provides the following benefits:</span></span>

* <span data-ttu-id="05075-226">Il codice JavaScript importato non inquina più lo spazio dei nomi globale.</span><span class="sxs-lookup"><span data-stu-id="05075-226">Imported JavaScript no longer pollutes the global namespace.</span></span>
* <span data-ttu-id="05075-227">I consumer di una libreria e i componenti non sono necessari per importare il codice JavaScript correlato.</span><span class="sxs-lookup"><span data-stu-id="05075-227">Consumers of a library and components aren't required to import the related JavaScript.</span></span>

<span data-ttu-id="05075-228">Il modulo JavaScript seguente, ad esempio, esporta una funzione JavaScript per visualizzare un prompt del browser:</span><span class="sxs-lookup"><span data-stu-id="05075-228">For example, the following JavaScript module exports a JavaScript function for showing a browser prompt:</span></span>

```javascript
export function showPrompt(message) {
  return prompt(message, 'Type anything here');
}
```

<span data-ttu-id="05075-229">Aggiungere il modulo JavaScript precedente a una libreria .NET come asset Web statico ( `wwwroot/exampleJsInterop.js` ) e quindi importare il modulo nel codice .NET usando il <xref:Microsoft.JSInterop.IJSRuntime> servizio.</span><span class="sxs-lookup"><span data-stu-id="05075-229">Add the preceding JavaScript module to a .NET library as a static web asset (`wwwroot/exampleJsInterop.js`) and then import the module into the .NET code using the <xref:Microsoft.JSInterop.IJSRuntime> service.</span></span> <span data-ttu-id="05075-230">Il servizio viene inserito come `jsRuntime` (non visualizzato) per l'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="05075-230">The service is injected as `jsRuntime` (not shown) for the following example:</span></span>

```csharp
var module = await jsRuntime.InvokeAsync<JSObjectReference>(
    "import", "./_content/MyComponents/exampleJsInterop.js");
```

<span data-ttu-id="05075-231">L' `import` identificatore nell'esempio precedente è un identificatore speciale usato in modo specifico per l'importazione di un modulo JavaScript.</span><span class="sxs-lookup"><span data-stu-id="05075-231">The `import` identifier in the preceding example is a special identifier used specifically for importing a JavaScript module.</span></span> <span data-ttu-id="05075-232">Specificare il modulo usando il percorso di asset Web statico stabile: `_content/{LIBRARY NAME}/{PATH UNDER WWWROOT}` .</span><span class="sxs-lookup"><span data-stu-id="05075-232">Specify the module using its stable static web asset path: `_content/{LIBRARY NAME}/{PATH UNDER WWWROOT}`.</span></span> <span data-ttu-id="05075-233">Il segnaposto `{LIBRARY NAME}` è il nome della libreria.</span><span class="sxs-lookup"><span data-stu-id="05075-233">The placeholder `{LIBRARY NAME}` is the library name.</span></span> <span data-ttu-id="05075-234">Il segnaposto `{PATH UNDER WWWROOT}` è il percorso dello script in `wwwroot` .</span><span class="sxs-lookup"><span data-stu-id="05075-234">The placeholder `{PATH UNDER WWWROOT}` is the path to the script under `wwwroot`.</span></span>

<span data-ttu-id="05075-235"><xref:Microsoft.JSInterop.IJSRuntime> importa il modulo come `JSObjectReference` , che rappresenta un riferimento a un oggetto JavaScript dal codice .NET.</span><span class="sxs-lookup"><span data-stu-id="05075-235"><xref:Microsoft.JSInterop.IJSRuntime> imports the module as a `JSObjectReference`, which represents a reference to a JavaScript object from .NET code.</span></span> <span data-ttu-id="05075-236">Usare `JSObjectReference` per richiamare le funzioni JavaScript esportate dal modulo:</span><span class="sxs-lookup"><span data-stu-id="05075-236">Use the `JSObjectReference` to invoke exported JavaScript functions from the module:</span></span>

```csharp
public async ValueTask<string> Prompt(string message)
{
    return await module.InvokeAsync<string>("showPrompt", message);
}
```

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="05075-237">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="05075-237">Additional resources</span></span>

* <xref:blazor/call-dotnet-from-javascript>
* [<span data-ttu-id="05075-238">Esempio di InteropComponent. Razor (repository GitHub DotNet/AspNetCore, Branch versione 3,1)</span><span class="sxs-lookup"><span data-stu-id="05075-238">InteropComponent.razor example (dotnet/AspNetCore GitHub repository, 3.1 release branch)</span></span>](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
* [<span data-ttu-id="05075-239">Eseguire trasferimenti di dati di grandi dimensioni nelle Blazor Server app</span><span class="sxs-lookup"><span data-stu-id="05075-239">Perform large data transfers in Blazor Server apps</span></span>](xref:blazor/advanced-scenarios#perform-large-data-transfers-in-blazor-server-apps)
