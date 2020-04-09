---
title: Chiamare funzioni JavaScript dai metodi .NET in ASP.NET CoreCallBlazor
author: guardrex
description: Scopri come richiamare funzioni JavaScript Blazor dai metodi .NET nelle app.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/07/2020
no-loc:
- Blazor
- SignalR
uid: blazor/call-javascript-from-dotnet
ms.openlocfilehash: 0c6b6a0a8f88fa912523e7772fcd84ef4ce3b4ff
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80977015"
---
# <a name="call-javascript-functions-from-net-methods-in-aspnet-core-opno-locblazor"></a><span data-ttu-id="fef9f-103">Chiamare funzioni JavaScript dai metodi .NET in ASP.NET CoreCallBlazor</span><span class="sxs-lookup"><span data-stu-id="fef9f-103">Call JavaScript functions from .NET methods in ASP.NET Core Blazor</span></span>

<span data-ttu-id="fef9f-104">Di [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="fef9f-104">By [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="fef9f-105">Un'app Blazor può richiamare funzioni JavaScript da metodi .NET e metodi .NET da funzioni JavaScript.</span><span class="sxs-lookup"><span data-stu-id="fef9f-105">A Blazor app can invoke JavaScript functions from .NET methods and .NET methods from JavaScript functions.</span></span> <span data-ttu-id="fef9f-106">Questi scenari sono denominati *interoperabilità JavaScript* (*interoperabilità JS*).</span><span class="sxs-lookup"><span data-stu-id="fef9f-106">These scenarios are called *JavaScript interoperability* (*JS interop*).</span></span>

<span data-ttu-id="fef9f-107">Questo articolo illustra la chiamata di funzioni JavaScript da .NET.</span><span class="sxs-lookup"><span data-stu-id="fef9f-107">This article covers invoking JavaScript functions from .NET.</span></span> <span data-ttu-id="fef9f-108">Per informazioni su come chiamare metodi .NET <xref:blazor/call-dotnet-from-javascript>da JavaScript, vedere .</span><span class="sxs-lookup"><span data-stu-id="fef9f-108">For information on how to call .NET methods from JavaScript, see <xref:blazor/call-dotnet-from-javascript>.</span></span>

<span data-ttu-id="fef9f-109">[Visualizzare o scaricare codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ( come[scaricare](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="fef9f-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="fef9f-110">Per chiamare JavaScript da .NET, usare l'astrazione. `IJSRuntime`</span><span class="sxs-lookup"><span data-stu-id="fef9f-110">To call into JavaScript from .NET, use the `IJSRuntime` abstraction.</span></span> <span data-ttu-id="fef9f-111">Per emettere chiamate di `IJSRuntime` interoperabilità JS, inserire l'astrazione nel componente.</span><span class="sxs-lookup"><span data-stu-id="fef9f-111">To issue JS interop calls, inject the `IJSRuntime` abstraction in your component.</span></span> <span data-ttu-id="fef9f-112">Il `InvokeAsync<T>` metodo accetta un identificatore per la funzione JavaScript che si desidera richiamare insieme a un numero qualsiasi di argomenti serializzabili JSON.</span><span class="sxs-lookup"><span data-stu-id="fef9f-112">The `InvokeAsync<T>` method takes an identifier for the JavaScript function that you wish to invoke along with any number of JSON-serializable arguments.</span></span> <span data-ttu-id="fef9f-113">L'identificatore di funzione è`window`relativo all'ambito globale ( ).</span><span class="sxs-lookup"><span data-stu-id="fef9f-113">The function identifier is relative to the global scope (`window`).</span></span> <span data-ttu-id="fef9f-114">Se si desidera `window.someScope.someFunction`chiamare , `someScope.someFunction`l'identificatore è .</span><span class="sxs-lookup"><span data-stu-id="fef9f-114">If you wish to call `window.someScope.someFunction`, the identifier is `someScope.someFunction`.</span></span> <span data-ttu-id="fef9f-115">Non è necessario registrare la funzione prima che venga chiamata.</span><span class="sxs-lookup"><span data-stu-id="fef9f-115">There's no need to register the function before it's called.</span></span> <span data-ttu-id="fef9f-116">Il tipo `T` restituito deve anche essere serializzabile JSON.</span><span class="sxs-lookup"><span data-stu-id="fef9f-116">The return type `T` must also be JSON serializable.</span></span> <span data-ttu-id="fef9f-117">`T`deve corrispondere al tipo .NET che meglio esegue il mapping al tipo JSON restituito.</span><span class="sxs-lookup"><span data-stu-id="fef9f-117">`T` should match the .NET type that best maps to the JSON type returned.</span></span>

<span data-ttu-id="fef9f-118">Per Blazor le app Server con il prerendering abilitato, la chiamata in JavaScript non è possibile durante il prerendering iniziale.</span><span class="sxs-lookup"><span data-stu-id="fef9f-118">For Blazor Server apps with prerendering enabled, calling into JavaScript isn't possible during the initial prerendering.</span></span> <span data-ttu-id="fef9f-119">Le chiamate di interoperabilità JavaScript devono essere rinviate fino a quando non viene stabilita la connessione con il browser.</span><span class="sxs-lookup"><span data-stu-id="fef9f-119">JavaScript interop calls must be deferred until after the connection with the browser is established.</span></span> <span data-ttu-id="fef9f-120">Per altre informazioni, vedere la sezione [Rilevare il Blazor prerendering di un'app server.](#detect-when-a-blazor-server-app-is-prerendering)</span><span class="sxs-lookup"><span data-stu-id="fef9f-120">For more information, see the [Detect when a Blazor Server app is prerendering](#detect-when-a-blazor-server-app-is-prerendering) section.</span></span>

<span data-ttu-id="fef9f-121">L'esempio seguente è basato su [TextDecoder](https://developer.mozilla.org/docs/Web/API/TextDecoder), un decodificatore basato su JavaScript.</span><span class="sxs-lookup"><span data-stu-id="fef9f-121">The following example is based on [TextDecoder](https://developer.mozilla.org/docs/Web/API/TextDecoder), a JavaScript-based decoder.</span></span> <span data-ttu-id="fef9f-122">Nell'esempio viene illustrato come richiamare una funzione JavaScript da un metodo c'è.</span><span class="sxs-lookup"><span data-stu-id="fef9f-122">The example demonstrates how to invoke a JavaScript function from a C# method.</span></span> <span data-ttu-id="fef9f-123">La funzione JavaScript accetta una matrice di byte da un metodo C , decodifica la matrice e restituisce il testo al componente per la visualizzazione.</span><span class="sxs-lookup"><span data-stu-id="fef9f-123">The JavaScript function accepts a byte array from a C# method, decodes the array, and returns the text to the component for display.</span></span>

<span data-ttu-id="fef9f-124">All'interno `<head>` dell'elemento *di wwwroot/index.html* (Blazor WebAssembly) o *Pages/_Host.cshtml* (Blazor Server), fornire una funzione JavaScript che utilizza `TextDecoder` per decodificare una matrice passata e restituire il valore decodificato:</span><span class="sxs-lookup"><span data-stu-id="fef9f-124">Inside the `<head>` element of *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server), provide a JavaScript function that uses `TextDecoder` to decode a passed array and return the decoded value:</span></span>

[!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-convertarray.html)]

<span data-ttu-id="fef9f-125">Il codice JavaScript, ad esempio il codice illustrato nell'esempio precedente, può anche essere caricato da un file JavaScript (*.js*) con un riferimento al file di script:</span><span class="sxs-lookup"><span data-stu-id="fef9f-125">JavaScript code, such as the code shown in the preceding example, can also be loaded from a JavaScript file (*.js*) with a reference to the script file:</span></span>

```html
<script src="exampleJsInterop.js"></script>
```

<span data-ttu-id="fef9f-126">Il seguente componente:</span><span class="sxs-lookup"><span data-stu-id="fef9f-126">The following component:</span></span>

* <span data-ttu-id="fef9f-127">Richiama la `convertArray` funzione JavaScript utilizzando `JSRuntime` quando viene selezionato un pulsante del componente ( Convert**Array**).</span><span class="sxs-lookup"><span data-stu-id="fef9f-127">Invokes the `convertArray` JavaScript function using `JSRuntime` when a component button (**Convert Array**) is selected.</span></span>
* <span data-ttu-id="fef9f-128">Dopo la chiamata alla funzione JavaScript, la matrice passata viene convertita in una stringa.</span><span class="sxs-lookup"><span data-stu-id="fef9f-128">After the JavaScript function is called, the passed array is converted into a string.</span></span> <span data-ttu-id="fef9f-129">La stringa viene restituita al componente per la visualizzazione.</span><span class="sxs-lookup"><span data-stu-id="fef9f-129">The string is returned to the component for display.</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/call-js-example.razor?highlight=2,34-35)]

## <a name="ijsruntime"></a><span data-ttu-id="fef9f-130">IJSRuntime</span><span class="sxs-lookup"><span data-stu-id="fef9f-130">IJSRuntime</span></span>

<span data-ttu-id="fef9f-131">Per utilizzare `IJSRuntime` l'astrazione, adottare uno dei seguenti approcci:</span><span class="sxs-lookup"><span data-stu-id="fef9f-131">To use the `IJSRuntime` abstraction, adopt any of the following approaches:</span></span>

* <span data-ttu-id="fef9f-132">Inserire `IJSRuntime` l'astrazione nel componente Razor (*.razor*):</span><span class="sxs-lookup"><span data-stu-id="fef9f-132">Inject the `IJSRuntime` abstraction into the Razor component (*.razor*):</span></span>

  [!code-razor[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction.razor?highlight=1)]

  <span data-ttu-id="fef9f-133">All'interno `<head>` dell'elemento di *wwwroot/index.html* (Blazor WebAssembly) o *Pages/_Host.cshtml* (Blazor Server), fornire una `handleTickerChanged` funzione JavaScript.</span><span class="sxs-lookup"><span data-stu-id="fef9f-133">Inside the `<head>` element of *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server), provide a `handleTickerChanged` JavaScript function.</span></span> <span data-ttu-id="fef9f-134">La funzione viene `IJSRuntime.InvokeVoidAsync` chiamata con e non restituisce un valore:The function is called with and doesn't return a value:</span><span class="sxs-lookup"><span data-stu-id="fef9f-134">The function is called with `IJSRuntime.InvokeVoidAsync` and doesn't return a value:</span></span>

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged1.html)]

* <span data-ttu-id="fef9f-135">Inserire `IJSRuntime` l'astrazione in una classe (*.cs*):</span><span class="sxs-lookup"><span data-stu-id="fef9f-135">Inject the `IJSRuntime` abstraction into a class (*.cs*):</span></span>

  [!code-csharp[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction-class.cs?highlight=5)]

  <span data-ttu-id="fef9f-136">All'interno `<head>` dell'elemento di *wwwroot/index.html* (Blazor WebAssembly) o *Pages/_Host.cshtml* (Blazor Server), fornire una `handleTickerChanged` funzione JavaScript.</span><span class="sxs-lookup"><span data-stu-id="fef9f-136">Inside the `<head>` element of *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server), provide a `handleTickerChanged` JavaScript function.</span></span> <span data-ttu-id="fef9f-137">La funzione viene `JSRuntime.InvokeAsync` chiamata con e restituisce un valore:The function is called with and returns a value:</span><span class="sxs-lookup"><span data-stu-id="fef9f-137">The function is called with `JSRuntime.InvokeAsync` and returns a value:</span></span>

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged2.html)]

* <span data-ttu-id="fef9f-138">Per la generazione di contenuto dinamico con [BuildRenderTree](xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic), utilizzare l'attributo: `[Inject]`</span><span class="sxs-lookup"><span data-stu-id="fef9f-138">For dynamic content generation with [BuildRenderTree](xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic), use the `[Inject]` attribute:</span></span>

  ```razor
  [Inject]
  IJSRuntime JSRuntime { get; set; }
  ```

<span data-ttu-id="fef9f-139">Nell'app di esempio lato client che accompagna questo argomento, sono disponibili due funzioni JavaScript per l'app che interagiscono con il DOM per ricevere l'input dell'utente e visualizzare un messaggio di benvenuto:In the client-side sample app that accompanies this topic, two JavaScript functions are available to the app that interact with the DOM to receive user input and display a welcome message:</span><span class="sxs-lookup"><span data-stu-id="fef9f-139">In the client-side sample app that accompanies this topic, two JavaScript functions are available to the app that interact with the DOM to receive user input and display a welcome message:</span></span>

* <span data-ttu-id="fef9f-140">`showPrompt`&ndash; Produce una richiesta di accettazione dell'input dell'utente (il nome dell'utente) e restituisce il nome al chiamante.</span><span class="sxs-lookup"><span data-stu-id="fef9f-140">`showPrompt` &ndash; Produces a prompt to accept user input (the user's name) and returns the name to the caller.</span></span>
* <span data-ttu-id="fef9f-141">`displayWelcome`&ndash; Assegna un messaggio di benvenuto dal chiamante `id` `welcome`a un oggetto DOM con un oggetto di .</span><span class="sxs-lookup"><span data-stu-id="fef9f-141">`displayWelcome` &ndash; Assigns a welcome message from the caller to a DOM object with an `id` of `welcome`.</span></span>

<span data-ttu-id="fef9f-142">*wwwroot/exampleJsInterop.js*:</span><span class="sxs-lookup"><span data-stu-id="fef9f-142">*wwwroot/exampleJsInterop.js*:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=2-7)]

<span data-ttu-id="fef9f-143">Inserire `<script>` il tag che fa riferimento alBlazor file JavaScript nel file di *wwwroot/index.html* ( WebAssembly) o nel file *Pages/_Host.cshtml* (Blazor Server).</span><span class="sxs-lookup"><span data-stu-id="fef9f-143">Place the `<script>` tag that references the JavaScript file in the *wwwroot/index.html* file (Blazor WebAssembly) or *Pages/_Host.cshtml* file (Blazor Server).</span></span>

<span data-ttu-id="fef9f-144">*wwwroot/index.html* Blazor ( WebAssembly):</span><span class="sxs-lookup"><span data-stu-id="fef9f-144">*wwwroot/index.html* (Blazor WebAssembly):</span></span>

[!code-html[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/index.html?highlight=22)]

<span data-ttu-id="fef9f-145">*Pages/_Host.cshtml* Blazor ( Server):</span><span class="sxs-lookup"><span data-stu-id="fef9f-145">*Pages/_Host.cshtml* (Blazor Server):</span></span>

[!code-cshtml[](./common/samples/3.x/BlazorServerSample/Pages/_Host.cshtml?highlight=35)]

<span data-ttu-id="fef9f-146">Non inserire un `<script>` tag in un `<script>` file componente perché il tag non può essere aggiornato dinamicamente.</span><span class="sxs-lookup"><span data-stu-id="fef9f-146">Don't place a `<script>` tag in a component file because the `<script>` tag can't be updated dynamically.</span></span>

<span data-ttu-id="fef9f-147">I metodi .NET interop con le funzioni JavaScript nel `IJSRuntime.InvokeAsync<T>`file *exampleJsInterop.js* chiamando .</span><span class="sxs-lookup"><span data-stu-id="fef9f-147">.NET methods interop with the JavaScript functions in the *exampleJsInterop.js* file by calling `IJSRuntime.InvokeAsync<T>`.</span></span>

<span data-ttu-id="fef9f-148">L'astrazione è asincrona per consentire scenari Server.The `IJSRuntime` abstraction is asynchronous to allow for Blazor Server scenarios.</span><span class="sxs-lookup"><span data-stu-id="fef9f-148">The `IJSRuntime` abstraction is asynchronous to allow for Blazor Server scenarios.</span></span> <span data-ttu-id="fef9f-149">Se l'app Blazor è un'app WebAssembly e si desidera richiamare `IJSInProcessRuntime` una `Invoke<T>` funzione JavaScript in modo sincrono, eseguire il downcast e chiamare invece.</span><span class="sxs-lookup"><span data-stu-id="fef9f-149">If the app is a Blazor WebAssembly app and you want to invoke a JavaScript function synchronously, downcast to `IJSInProcessRuntime` and call `Invoke<T>` instead.</span></span> <span data-ttu-id="fef9f-150">È consigliabile che la maggior parte delle librerie di interoperabilità JS utilizzi le API asincrone per assicurarsi che le librerie siano disponibili in tutti gli scenari.</span><span class="sxs-lookup"><span data-stu-id="fef9f-150">We recommend that most JS interop libraries use the async APIs to ensure that the libraries are available in all scenarios.</span></span>

<span data-ttu-id="fef9f-151">L'app di esempio include un componente per illustrare l'interoperabilità JS.</span><span class="sxs-lookup"><span data-stu-id="fef9f-151">The sample app includes a component to demonstrate JS interop.</span></span> <span data-ttu-id="fef9f-152">Il componente:</span><span class="sxs-lookup"><span data-stu-id="fef9f-152">The component:</span></span>

* <span data-ttu-id="fef9f-153">Riceve l'input dell'utente tramite un prompt JavaScript.</span><span class="sxs-lookup"><span data-stu-id="fef9f-153">Receives user input via a JavaScript prompt.</span></span>
* <span data-ttu-id="fef9f-154">Restituisce il testo al componente per l'elaborazione.</span><span class="sxs-lookup"><span data-stu-id="fef9f-154">Returns the text to the component for processing.</span></span>
* <span data-ttu-id="fef9f-155">Chiama una seconda funzione JavaScript che interagisce con il DOM per visualizzare un messaggio di benvenuto.</span><span class="sxs-lookup"><span data-stu-id="fef9f-155">Calls a second JavaScript function that interacts with the DOM to display a welcome message.</span></span>

<span data-ttu-id="fef9f-156">*Pages/JSInterop.razor*:</span><span class="sxs-lookup"><span data-stu-id="fef9f-156">*Pages/JSInterop.razor*:</span></span>

```razor
@page "/JSInterop"
@using BlazorSample.JsInteropClasses
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

1. <span data-ttu-id="fef9f-157">Quando `TriggerJsPrompt` viene eseguito selezionando il pulsante **Trigger JavaScript** `showPrompt` Prompt del componente, viene chiamata la funzione JavaScript fornita nel file *wwwroot/exampleJsInterop.js.*</span><span class="sxs-lookup"><span data-stu-id="fef9f-157">When `TriggerJsPrompt` is executed by selecting the component's **Trigger JavaScript Prompt** button, the JavaScript `showPrompt` function provided in the *wwwroot/exampleJsInterop.js* file is called.</span></span>
1. <span data-ttu-id="fef9f-158">La `showPrompt` funzione accetta l'input dell'utente (il nome dell'utente), che è codificato in HTML e restituito al componente.</span><span class="sxs-lookup"><span data-stu-id="fef9f-158">The `showPrompt` function accepts user input (the user's name), which is HTML-encoded and returned to the component.</span></span> <span data-ttu-id="fef9f-159">Il componente archivia il nome dell'utente in una variabile locale, `name`.</span><span class="sxs-lookup"><span data-stu-id="fef9f-159">The component stores the user's name in a local variable, `name`.</span></span>
1. <span data-ttu-id="fef9f-160">La stringa `name` archiviata in viene incorporata in un messaggio di `displayWelcome`benvenuto, che viene passato a una funzione JavaScript, che esegue il rendering del messaggio di benvenuto in un tag di intestazione.</span><span class="sxs-lookup"><span data-stu-id="fef9f-160">The string stored in `name` is incorporated into a welcome message, which is passed to a JavaScript function, `displayWelcome`, which renders the welcome message into a heading tag.</span></span>

## <a name="call-a-void-javascript-function"></a><span data-ttu-id="fef9f-161">Chiamare una funzione JavaScript voidCall a void JavaScript function</span><span class="sxs-lookup"><span data-stu-id="fef9f-161">Call a void JavaScript function</span></span>

<span data-ttu-id="fef9f-162">Le funzioni JavaScript che restituiscono [void(0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) o [undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined) vengono chiamate con `IJSRuntime.InvokeVoidAsync`.</span><span class="sxs-lookup"><span data-stu-id="fef9f-162">JavaScript functions that return [void(0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) or [undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined) are called with `IJSRuntime.InvokeVoidAsync`.</span></span>

## <a name="detect-when-a-opno-locblazor-server-app-is-prerendering"></a><span data-ttu-id="fef9f-163">Rilevare Blazor il prerendering di un'app serverDetect when a Server app is prerendering</span><span class="sxs-lookup"><span data-stu-id="fef9f-163">Detect when a Blazor Server app is prerendering</span></span>
 
[!INCLUDE[](~/includes/blazor-prerendering.md)]

## <a name="capture-references-to-elements"></a><span data-ttu-id="fef9f-164">Acquisire riferimenti agli elementi</span><span class="sxs-lookup"><span data-stu-id="fef9f-164">Capture references to elements</span></span>

<span data-ttu-id="fef9f-165">Alcuni scenari di interoperabilità JS richiedono riferimenti a elementi HTML.</span><span class="sxs-lookup"><span data-stu-id="fef9f-165">Some JS interop scenarios require references to HTML elements.</span></span> <span data-ttu-id="fef9f-166">Ad esempio, una libreria dell'interfaccia utente potrebbe richiedere un riferimento all'elemento per l'inizializzazione o potrebbe essere necessario chiamare API di tipo comando su un elemento, ad esempio `focus` o `play`.</span><span class="sxs-lookup"><span data-stu-id="fef9f-166">For example, a UI library may require an element reference for initialization, or you might need to call command-like APIs on an element, such as `focus` or `play`.</span></span>

<span data-ttu-id="fef9f-167">Acquisire riferimenti agli elementi HTML in un componente utilizzando il seguente approccio:</span><span class="sxs-lookup"><span data-stu-id="fef9f-167">Capture references to HTML elements in a component using the following approach:</span></span>

* <span data-ttu-id="fef9f-168">Aggiungere `@ref` un attributo all'elemento HTML.</span><span class="sxs-lookup"><span data-stu-id="fef9f-168">Add an `@ref` attribute to the HTML element.</span></span>
* <span data-ttu-id="fef9f-169">Definire un campo `ElementReference` di tipo il `@ref` cui nome corrisponde al valore dell'attributo.</span><span class="sxs-lookup"><span data-stu-id="fef9f-169">Define a field of type `ElementReference` whose name matches the value of the `@ref` attribute.</span></span>

<span data-ttu-id="fef9f-170">L'esempio seguente mostra l'acquisizione di un riferimento all'elemento:The following example shows capturing a reference to the `username` `<input>` element:</span><span class="sxs-lookup"><span data-stu-id="fef9f-170">The following example shows capturing a reference to the `username` `<input>` element:</span></span>

```razor
<input @ref="username" ... />

@code {
    ElementReference username;
}
```

> [!WARNING]
> <span data-ttu-id="fef9f-171">Utilizzare solo un riferimento all'elemento per modificare il contenuto di Blazorun elemento vuoto che non interagisce con .</span><span class="sxs-lookup"><span data-stu-id="fef9f-171">Only use an element reference to mutate the contents of an empty element that doesn't interact with Blazor.</span></span> <span data-ttu-id="fef9f-172">Questo scenario è utile quando un'API di terze parti fornisce contenuto all'elemento.</span><span class="sxs-lookup"><span data-stu-id="fef9f-172">This scenario is useful when a 3rd party API supplies content to the element.</span></span> <span data-ttu-id="fef9f-173">Poiché Blazor non interagisce con l'elemento, non c'è alcuna possibilità di un conflitto tra Blazorla rappresentazione dell'elemento e il DOM.</span><span class="sxs-lookup"><span data-stu-id="fef9f-173">Because Blazor doesn't interact with the element, there's no possibility of a conflict between Blazor's representation of the element and the DOM.</span></span>
>
> <span data-ttu-id="fef9f-174">Nell'esempio seguente, è *pericoloso* modificare il contenuto dell'elenco`ul`non Blazor ordinato ( ) perché interagisce con il`<li>`DOM per popolare gli elementi dell'elenco di questo elemento ( ):</span><span class="sxs-lookup"><span data-stu-id="fef9f-174">In the following example, it's *dangerous* to mutate the contents of the unordered list (`ul`) because Blazor interacts with the DOM to populate this element's list items (`<li>`):</span></span>
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
> <span data-ttu-id="fef9f-175">Se l'interoperabilità JS modifica `MyList` il Blazor contenuto dell'elemento e tenta di applicare differenze all'elemento, le differenze non corrisponderanno al DOM.</span><span class="sxs-lookup"><span data-stu-id="fef9f-175">If JS interop mutates the contents of element `MyList` and Blazor attempts to apply diffs to the element, the diffs won't match the DOM.</span></span>

<span data-ttu-id="fef9f-176">Per quanto riguarda il codice `ElementReference` .NET, un è un handle opaco.</span><span class="sxs-lookup"><span data-stu-id="fef9f-176">As far as .NET code is concerned, an `ElementReference` is an opaque handle.</span></span> <span data-ttu-id="fef9f-177">*L'unica* cosa che `ElementReference` si può fare con è passarlo attraverso al codice JavaScript tramite l'interoperabilità JS.</span><span class="sxs-lookup"><span data-stu-id="fef9f-177">The *only* thing you can do with `ElementReference` is pass it through to JavaScript code via JS interop.</span></span> <span data-ttu-id="fef9f-178">In questo caso, il codice lato `HTMLElement` JavaScript riceve un'istanza, che può essere usata con le normali API DOM.</span><span class="sxs-lookup"><span data-stu-id="fef9f-178">When you do so, the JavaScript-side code receives an `HTMLElement` instance, which it can use with normal DOM APIs.</span></span>

<span data-ttu-id="fef9f-179">Ad esempio, il codice seguente definisce un metodo di estensione .NET che consente di impostare lo stato attivo su un elemento:For example, the following code defines a .NET extension method that enables setting the focus on an element:</span><span class="sxs-lookup"><span data-stu-id="fef9f-179">For example, the following code defines a .NET extension method that enables setting the focus on an element:</span></span>

<span data-ttu-id="fef9f-180">*exampleJsInterop.js*:</span><span class="sxs-lookup"><span data-stu-id="fef9f-180">*exampleJsInterop.js*:</span></span>

```javascript
window.exampleJsFunctions = {
  focusElement : function (element) {
    element.focus();
  }
}
```

<span data-ttu-id="fef9f-181">Per chiamare una funzione JavaScript che non `IJSRuntime.InvokeVoidAsync`restituisce un valore, utilizzare .</span><span class="sxs-lookup"><span data-stu-id="fef9f-181">To call a JavaScript function that doesn't return a value, use `IJSRuntime.InvokeVoidAsync`.</span></span> <span data-ttu-id="fef9f-182">Il codice seguente imposta lo stato attivo sull'input del nome `ElementReference`utente chiamando la funzione JavaScript precedente con l'oggetto acquisito:</span><span class="sxs-lookup"><span data-stu-id="fef9f-182">The following code sets the focus on the username input by calling the preceding JavaScript function with the captured `ElementReference`:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component1.razor?highlight=1,3,11-12)]

<span data-ttu-id="fef9f-183">Per usare un metodo di estensione, creare `IJSRuntime` un metodo di estensione statico che riceve l'istanza:To use an extension method, create a static extension method that receives the instance:</span><span class="sxs-lookup"><span data-stu-id="fef9f-183">To use an extension method, create a static extension method that receives the `IJSRuntime` instance:</span></span>

```csharp
public static async Task Focus(this ElementReference elementRef, IJSRuntime jsRuntime)
{
    await jsRuntime.InvokeVoidAsync(
        "exampleJsFunctions.focusElement", elementRef);
}
```

<span data-ttu-id="fef9f-184">Il `Focus` metodo viene chiamato direttamente sull'oggetto.</span><span class="sxs-lookup"><span data-stu-id="fef9f-184">The `Focus` method is called directly on the object.</span></span> <span data-ttu-id="fef9f-185">Nell'esempio seguente si `Focus` presuppone che `JsInteropClasses` il metodo sia disponibile dallo spazio dei nomi:The following example assumes that the method is available from the namespace:</span><span class="sxs-lookup"><span data-stu-id="fef9f-185">The following example assumes that the `Focus` method is available from the `JsInteropClasses` namespace:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component2.razor?highlight=1-4,12)]

> [!IMPORTANT]
> <span data-ttu-id="fef9f-186">La `username` variabile viene popolata solo dopo il rendering del componente.</span><span class="sxs-lookup"><span data-stu-id="fef9f-186">The `username` variable is only populated after the component is rendered.</span></span> <span data-ttu-id="fef9f-187">Se un `ElementReference` oggetto non popolato viene passato al codice JavaScript, il codice JavaScript riceve un valore di `null`.</span><span class="sxs-lookup"><span data-stu-id="fef9f-187">If an unpopulated `ElementReference` is passed to JavaScript code, the JavaScript code receives a value of `null`.</span></span> <span data-ttu-id="fef9f-188">Per modificare i riferimenti agli elementi al termine del rendering del componente (per impostare lo stato attivo iniziale su un elemento) utilizzate i [metodi del ciclo di vita del componente OnAfterRenderAsync o OnAfterRender](xref:blazor/lifecycle#after-component-render).</span><span class="sxs-lookup"><span data-stu-id="fef9f-188">To manipulate element references after the component has finished rendering (to set the initial focus on an element) use the [OnAfterRenderAsync or OnAfterRender component lifecycle methods](xref:blazor/lifecycle#after-component-render).</span></span>

<span data-ttu-id="fef9f-189">Quando si utilizzano tipi generici e si restituisce un valore, usare [ValueTask\<T>](xref:System.Threading.Tasks.ValueTask`1):</span><span class="sxs-lookup"><span data-stu-id="fef9f-189">When working with generic types and returning a value, use [ValueTask\<T>](xref:System.Threading.Tasks.ValueTask`1):</span></span>

```csharp
public static ValueTask<T> GenericMethod<T>(this ElementReference elementRef, 
    IJSRuntime jsRuntime)
{
    return jsRuntime.InvokeAsync<T>(
        "exampleJsFunctions.doSomethingGeneric", elementRef);
}
```

<span data-ttu-id="fef9f-190">`GenericMethod`viene chiamato direttamente sull'oggetto con un tipo.</span><span class="sxs-lookup"><span data-stu-id="fef9f-190">`GenericMethod` is called directly on the object with a type.</span></span> <span data-ttu-id="fef9f-191">Nell'esempio seguente si `GenericMethod` presuppone che `JsInteropClasses` l'oggetto sia disponibile dallo spazio dei nomi:The following example assumes that the is available from the namespace:</span><span class="sxs-lookup"><span data-stu-id="fef9f-191">The following example assumes that the `GenericMethod` is available from the `JsInteropClasses` namespace:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component3.razor?highlight=17)]

## <a name="reference-elements-across-components"></a><span data-ttu-id="fef9f-192">Elementi di riferimento tra i componenti</span><span class="sxs-lookup"><span data-stu-id="fef9f-192">Reference elements across components</span></span>

<span data-ttu-id="fef9f-193">Un `ElementReference` è garantito valido solo `OnAfterRender` nel metodo di un `struct`componente (e un riferimento all'elemento è un ), pertanto non è possibile passare un riferimento a un elemento tra componenti.</span><span class="sxs-lookup"><span data-stu-id="fef9f-193">An `ElementReference` is only guaranteed valid in a component's `OnAfterRender` method (and an element reference is a `struct`), so an element reference can't be passed between components.</span></span>

<span data-ttu-id="fef9f-194">Affinché un componente padre renda un riferimento all'elemento disponibile ad altri componenti, il componente padre può:</span><span class="sxs-lookup"><span data-stu-id="fef9f-194">For a parent component to make an element reference available to other components, the parent component can:</span></span>

* <span data-ttu-id="fef9f-195">Consentire ai componenti figlio di registrare i callback.</span><span class="sxs-lookup"><span data-stu-id="fef9f-195">Allow child components to register callbacks.</span></span>
* <span data-ttu-id="fef9f-196">Richiamare i callback `OnAfterRender` registrati durante l'evento con il riferimento all'elemento passato.</span><span class="sxs-lookup"><span data-stu-id="fef9f-196">Invoke the registered callbacks during the `OnAfterRender` event with the passed element reference.</span></span> <span data-ttu-id="fef9f-197">Indirettamente, questo approccio consente ai componenti figlio di interagire con il riferimento all'elemento padre.</span><span class="sxs-lookup"><span data-stu-id="fef9f-197">Indirectly, this approach allows child components to interact with the parent's element reference.</span></span>

<span data-ttu-id="fef9f-198">Nell'esempio WebAssembly seguente Blazor viene illustrato l'approccio.</span><span class="sxs-lookup"><span data-stu-id="fef9f-198">The following Blazor WebAssembly example illustrates the approach.</span></span>

<span data-ttu-id="fef9f-199">Nella `<head>` wwwroot/index.html *wwwroot/index.html*:</span><span class="sxs-lookup"><span data-stu-id="fef9f-199">In the `<head>` of *wwwroot/index.html*:</span></span>

```html
<style>
    .red { color: red }
</style>
```

<span data-ttu-id="fef9f-200">Nella `<body>` wwwroot/index.html *wwwroot/index.html*:</span><span class="sxs-lookup"><span data-stu-id="fef9f-200">In the `<body>` of *wwwroot/index.html*:</span></span>

```html
<script>
    function setElementClass(element, className) {
        /** @type {HTMLElement} **/
        var myElement = element;
        myElement.classList.add(className);
    }
</script>
```

<span data-ttu-id="fef9f-201">*Pages/Index.razor* (componente padre):</span><span class="sxs-lookup"><span data-stu-id="fef9f-201">*Pages/Index.razor* (parent component):</span></span>

```razor
@page "/"

<h1 @ref="_title">Hello, world!</h1>

Welcome to your new app.

<SurveyPrompt Parent="this" Title="How is Blazor working for you?" />
```

<span data-ttu-id="fef9f-202">*Pagine/Index.razor.cs*:</span><span class="sxs-lookup"><span data-stu-id="fef9f-202">*Pages/Index.razor.cs*:</span></span>

```csharp
using System;
using System.Collections.Generic;
using Microsoft.AspNetCore.Components;

namespace BlazorSample.Pages
{
    public partial class Index : 
        ComponentBase, IObservable<ElementReference>, IDisposable
    {
        private bool _disposing;
        private IList<IObserver<ElementReference>> _subscriptions = 
            new List<IObserver<ElementReference>>();
        private ElementReference _title;

        protected override void OnAfterRender(bool firstRender)
        {
            base.OnAfterRender(firstRender);

            foreach (var subscription in _subscriptions)
            {
                try
                {
                    subscription.OnNext(_title);
                }
                catch (Exception)
                {
                    throw;
                }
            }
        }

        public void Dispose()
        {
            _disposing = true;

            foreach (var subscription in _subscriptions)
            {
                try
                {
                    subscription.OnCompleted();
                }
                catch (Exception)
                {
                }
            }

            _subscriptions.Clear();
        }

        public IDisposable Subscribe(IObserver<ElementReference> observer)
        {
            if (_disposing)
            {
                throw new InvalidOperationException("Parent being disposed");
            }

            _subscriptions.Add(observer);

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
                Self._subscriptions.Remove(Observer);
            }
        }
    }
}
```

<span data-ttu-id="fef9f-203">*Condiviso/SurveyPrompt.razor* (componente figlio):</span><span class="sxs-lookup"><span data-stu-id="fef9f-203">*Shared/SurveyPrompt.razor* (child component):</span></span>

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

<span data-ttu-id="fef9f-204">*Condiviso/SurveyPrompt.razor.cs*:</span><span class="sxs-lookup"><span data-stu-id="fef9f-204">*Shared/SurveyPrompt.razor.cs*:</span></span>

```csharp
using System;
using Microsoft.AspNetCore.Components;

namespace BlazorSample.Shared
{
    public partial class SurveyPrompt : 
        ComponentBase, IObserver<ElementReference>, IDisposable
    {
        private IDisposable _subscription = null;

        [Parameter]
        public IObservable<ElementReference> Parent { get; set; }

        protected override void OnParametersSet()
        {
            base.OnParametersSet();

            if (_subscription != null)
            {
                _subscription.Dispose();
            }

            _subscription = Parent.Subscribe(this);
        }

        public void OnCompleted()
        {
            _subscription = null;
        }

        public void OnError(Exception error)
        {
            _subscription = null;
        }

        public void OnNext(ElementReference value)
        {
            JS.InvokeAsync<object>(
                "setElementClass", new object[] { value, "red" });
        }

        public void Dispose()
        {
            _subscription?.Dispose();
        }
    }
}
```

## <a name="harden-js-interop-calls"></a><span data-ttu-id="fef9f-205">Chiamate di interoperabilità JS harden</span><span class="sxs-lookup"><span data-stu-id="fef9f-205">Harden JS interop calls</span></span>

<span data-ttu-id="fef9f-206">L'interoperabilità JS potrebbe non riuscire a causa di errori di rete e deve essere considerata inaffidabile.</span><span class="sxs-lookup"><span data-stu-id="fef9f-206">JS interop may fail due to networking errors and should be treated as unreliable.</span></span> <span data-ttu-id="fef9f-207">Per impostazione Blazor predefinita, un'app Server scade le chiamate di interoperabilità JS sul server dopo un minuto.</span><span class="sxs-lookup"><span data-stu-id="fef9f-207">By default, a Blazor Server app times out JS interop calls on the server after one minute.</span></span> <span data-ttu-id="fef9f-208">Se un'app è in grado di tollerare un timeout più aggressivo, ad esempio 10 secondi, imposta il timeout usando uno dei seguenti approcci:</span><span class="sxs-lookup"><span data-stu-id="fef9f-208">If an app can tolerate a more aggressive timeout, such as 10 seconds, set the timeout using one of the following approaches:</span></span>

* <span data-ttu-id="fef9f-209">Globalmente `Startup.ConfigureServices`in , specificare il timeout:</span><span class="sxs-lookup"><span data-stu-id="fef9f-209">Globally in `Startup.ConfigureServices`, specify the timeout:</span></span>

  ```csharp
  services.AddServerSideBlazor(
      options => options.JSInteropDefaultCallTimeout = TimeSpan.FromSeconds({SECONDS}));
  ```

* <span data-ttu-id="fef9f-210">Per la chiamata nel codice del componente, una singola chiamata può specificare il timeout:Per-invocation in component code, a single call can specify the timeout:</span><span class="sxs-lookup"><span data-stu-id="fef9f-210">Per-invocation in component code, a single call can specify the timeout:</span></span>

  ```csharp
  var result = await JSRuntime.InvokeAsync<string>("MyJSOperation", 
      TimeSpan.FromSeconds({SECONDS}), new[] { "Arg1" });
  ```

<span data-ttu-id="fef9f-211">Per ulteriori informazioni sull'esaurimento <xref:security/blazor/server>delle risorse, vedere .</span><span class="sxs-lookup"><span data-stu-id="fef9f-211">For more information on resource exhaustion, see <xref:security/blazor/server>.</span></span>

[!INCLUDE[Share interop code in a class library](~/includes/blazor-share-interop-code.md)]

## <a name="avoid-circular-object-references"></a><span data-ttu-id="fef9f-212">Evitare riferimenti a oggetti circolari</span><span class="sxs-lookup"><span data-stu-id="fef9f-212">Avoid circular object references</span></span>

<span data-ttu-id="fef9f-213">Gli oggetti che contengono riferimenti circolari non possono essere serializzati sul client per:</span><span class="sxs-lookup"><span data-stu-id="fef9f-213">Objects that contain circular references can't be serialized on the client for either:</span></span>

* <span data-ttu-id="fef9f-214">Chiamate al metodo .NET.</span><span class="sxs-lookup"><span data-stu-id="fef9f-214">.NET method calls.</span></span>
* <span data-ttu-id="fef9f-215">Le chiamate al metodo JavaScript da C, quando il tipo restituito ha riferimenti circolari.</span><span class="sxs-lookup"><span data-stu-id="fef9f-215">JavaScript method calls from C# when the return type has circular references.</span></span>

<span data-ttu-id="fef9f-216">Per ulteriori informazioni, vedere i seguenti problemi:</span><span class="sxs-lookup"><span data-stu-id="fef9f-216">For more information, see the following issues:</span></span>

* [<span data-ttu-id="fef9f-217">I riferimenti circolari non sono supportati, accettadue (#20525 dotnet/aspnetcore)</span><span class="sxs-lookup"><span data-stu-id="fef9f-217">Circular references are not supported, take two (dotnet/aspnetcore #20525)</span></span>](https://github.com/dotnet/aspnetcore/issues/20525)
* [<span data-ttu-id="fef9f-218">Proposta: aggiungere un meccanismo per gestire i riferimenti circolari durante la serializzazione (#30820 di patchnet/runtime)</span><span class="sxs-lookup"><span data-stu-id="fef9f-218">Proposal: Add mechanism to handle circular references when serializing (dotnet/runtime #30820)</span></span>](https://github.com/dotnet/runtime/issues/30820)

## <a name="additional-resources"></a><span data-ttu-id="fef9f-219">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="fef9f-219">Additional resources</span></span>

* <xref:blazor/call-dotnet-from-javascript>
* [<span data-ttu-id="fef9f-220">Esempio InteropComponent.razor (repository dotnet/AspNetCore GitHub, ramo di rilascio 3.1)</span><span class="sxs-lookup"><span data-stu-id="fef9f-220">InteropComponent.razor example (dotnet/AspNetCore GitHub repository, 3.1 release branch)</span></span>](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
* <span data-ttu-id="fef9f-221">[Eseguire trasferimenti Blazor di dati di grandi dimensioni nelle app serverPerform large data transfers in Server apps](xref:blazor/advanced-scenarios#perform-large-data-transfers-in-blazor-server-apps)</span><span class="sxs-lookup"><span data-stu-id="fef9f-221">[Perform large data transfers in Blazor Server apps](xref:blazor/advanced-scenarios#perform-large-data-transfers-in-blazor-server-apps)</span></span>
