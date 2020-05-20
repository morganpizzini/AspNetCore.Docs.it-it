---
<span data-ttu-id="3a675-101">title:' Call JavaScript functions from .NET Methods in ASP.NET Core Blazor ' Author: Description:' Learn how to Invoke JavaScript functions from .NET Methods in Blazor Apps '.</span><span class="sxs-lookup"><span data-stu-id="3a675-101">title: 'Call JavaScript functions from .NET methods in ASP.NET Core Blazor' author: description: 'Learn how to invoke JavaScript functions from .NET methods in Blazor apps.'</span></span>
<span data-ttu-id="3a675-102">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3a675-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3a675-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3a675-103">'Blazor'</span></span>
- <span data-ttu-id="3a675-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3a675-104">'Identity'</span></span>
- <span data-ttu-id="3a675-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3a675-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="3a675-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3a675-106">'Razor'</span></span>
- <span data-ttu-id="3a675-107">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="3a675-107">'SignalR' uid:</span></span> 

---
# <a name="call-javascript-functions-from-net-methods-in-aspnet-core-blazor"></a><span data-ttu-id="3a675-108">Chiamare funzioni JavaScript da metodi .NET in ASP.NET CoreBlazor</span><span class="sxs-lookup"><span data-stu-id="3a675-108">Call JavaScript functions from .NET methods in ASP.NET Core Blazor</span></span>

<span data-ttu-id="3a675-109">Di [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27)e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="3a675-109">By [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="3a675-110">Un' Blazor app può richiamare funzioni JavaScript da metodi .NET e metodi .NET da funzioni JavaScript.</span><span class="sxs-lookup"><span data-stu-id="3a675-110">A Blazor app can invoke JavaScript functions from .NET methods and .NET methods from JavaScript functions.</span></span> <span data-ttu-id="3a675-111">Questi scenari sono detti *interoperabilità JavaScript (interoperabilità* *JS*).</span><span class="sxs-lookup"><span data-stu-id="3a675-111">These scenarios are called *JavaScript interoperability* (*JS interop*).</span></span>

<span data-ttu-id="3a675-112">Questo articolo illustra come richiamare funzioni JavaScript da .NET.</span><span class="sxs-lookup"><span data-stu-id="3a675-112">This article covers invoking JavaScript functions from .NET.</span></span> <span data-ttu-id="3a675-113">Per informazioni su come chiamare i metodi .NET da JavaScript, vedere <xref:blazor/call-dotnet-from-javascript> .</span><span class="sxs-lookup"><span data-stu-id="3a675-113">For information on how to call .NET methods from JavaScript, see <xref:blazor/call-dotnet-from-javascript>.</span></span>

<span data-ttu-id="3a675-114">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="3a675-114">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="3a675-115">Per chiamare JavaScript da .NET, usare l' `IJSRuntime` astrazione.</span><span class="sxs-lookup"><span data-stu-id="3a675-115">To call into JavaScript from .NET, use the `IJSRuntime` abstraction.</span></span> <span data-ttu-id="3a675-116">Per rilasciare chiamate di interoperabilità JS, inserire l' `IJSRuntime` astrazione nel componente.</span><span class="sxs-lookup"><span data-stu-id="3a675-116">To issue JS interop calls, inject the `IJSRuntime` abstraction in your component.</span></span> <span data-ttu-id="3a675-117">Il `InvokeAsync<T>` metodo accetta un identificatore per la funzione JavaScript che si vuole richiamare insieme a un numero qualsiasi di argomenti serializzabili in JSON.</span><span class="sxs-lookup"><span data-stu-id="3a675-117">The `InvokeAsync<T>` method takes an identifier for the JavaScript function that you wish to invoke along with any number of JSON-serializable arguments.</span></span> <span data-ttu-id="3a675-118">L'identificatore della funzione è relativo all'ambito globale ( `window` ).</span><span class="sxs-lookup"><span data-stu-id="3a675-118">The function identifier is relative to the global scope (`window`).</span></span> <span data-ttu-id="3a675-119">Se si desidera chiamare `window.someScope.someFunction` , l'identificatore è `someScope.someFunction` .</span><span class="sxs-lookup"><span data-stu-id="3a675-119">If you wish to call `window.someScope.someFunction`, the identifier is `someScope.someFunction`.</span></span> <span data-ttu-id="3a675-120">Non è necessario registrare la funzione prima che venga chiamata.</span><span class="sxs-lookup"><span data-stu-id="3a675-120">There's no need to register the function before it's called.</span></span> <span data-ttu-id="3a675-121">Il tipo restituito `T` deve anche essere serializzabile in JSON.</span><span class="sxs-lookup"><span data-stu-id="3a675-121">The return type `T` must also be JSON serializable.</span></span> <span data-ttu-id="3a675-122">`T`deve corrispondere al tipo .NET che meglio esegue il mapping al tipo JSON restituito.</span><span class="sxs-lookup"><span data-stu-id="3a675-122">`T` should match the .NET type that best maps to the JSON type returned.</span></span>

<span data-ttu-id="3a675-123">Per Blazor le app Server con il prerendering abilitato, non è possibile chiamare JavaScript durante il prerendering iniziale.</span><span class="sxs-lookup"><span data-stu-id="3a675-123">For Blazor Server apps with prerendering enabled, calling into JavaScript isn't possible during the initial prerendering.</span></span> <span data-ttu-id="3a675-124">È necessario rinviare le chiamate di interoperabilità JavaScript finché non viene stabilita la connessione con il browser.</span><span class="sxs-lookup"><span data-stu-id="3a675-124">JavaScript interop calls must be deferred until after the connection with the browser is established.</span></span> <span data-ttu-id="3a675-125">Per ulteriori informazioni, vedere la sezione [rilevare quando un' Blazor app Server è prerendering](#detect-when-a-blazor-server-app-is-prerendering) .</span><span class="sxs-lookup"><span data-stu-id="3a675-125">For more information, see the [Detect when a Blazor Server app is prerendering](#detect-when-a-blazor-server-app-is-prerendering) section.</span></span>

<span data-ttu-id="3a675-126">L'esempio seguente è basato su [TextDecoder](https://developer.mozilla.org/docs/Web/API/TextDecoder), un decodificatore basato su JavaScript.</span><span class="sxs-lookup"><span data-stu-id="3a675-126">The following example is based on [TextDecoder](https://developer.mozilla.org/docs/Web/API/TextDecoder), a JavaScript-based decoder.</span></span> <span data-ttu-id="3a675-127">Nell'esempio viene illustrato come richiamare una funzione JavaScript da un metodo C#.</span><span class="sxs-lookup"><span data-stu-id="3a675-127">The example demonstrates how to invoke a JavaScript function from a C# method.</span></span> <span data-ttu-id="3a675-128">La funzione JavaScript accetta una matrice di byte da un metodo C#, decodifica la matrice e restituisce il testo al componente per la visualizzazione.</span><span class="sxs-lookup"><span data-stu-id="3a675-128">The JavaScript function accepts a byte array from a C# method, decodes the array, and returns the text to the component for display.</span></span>

<span data-ttu-id="3a675-129">All'interno dell' `<head>` elemento di *wwwroot/index.html* ( Blazor Webassembly) o *pages/_Host. cshtml* ( Blazor Server), fornire una funzione JavaScript che usa `TextDecoder` per decodificare una matrice passata e restituire il valore decodificato:</span><span class="sxs-lookup"><span data-stu-id="3a675-129">Inside the `<head>` element of *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server), provide a JavaScript function that uses `TextDecoder` to decode a passed array and return the decoded value:</span></span>

[!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-convertarray.html)]

<span data-ttu-id="3a675-130">Il codice JavaScript, ad esempio il codice illustrato nell'esempio precedente, può anche essere caricato da un file JavaScript (con*estensione js*) con un riferimento al file script:</span><span class="sxs-lookup"><span data-stu-id="3a675-130">JavaScript code, such as the code shown in the preceding example, can also be loaded from a JavaScript file (*.js*) with a reference to the script file:</span></span>

```html
<script src="exampleJsInterop.js"></script>
```

<span data-ttu-id="3a675-131">Il componente seguente:</span><span class="sxs-lookup"><span data-stu-id="3a675-131">The following component:</span></span>

* <span data-ttu-id="3a675-132">Richiama la `convertArray` funzione JavaScript usando `JSRuntime` quando viene selezionato un pulsante Component (**Convert Array**).</span><span class="sxs-lookup"><span data-stu-id="3a675-132">Invokes the `convertArray` JavaScript function using `JSRuntime` when a component button (**Convert Array**) is selected.</span></span>
* <span data-ttu-id="3a675-133">Dopo la chiamata della funzione JavaScript, la matrice passata viene convertita in una stringa.</span><span class="sxs-lookup"><span data-stu-id="3a675-133">After the JavaScript function is called, the passed array is converted into a string.</span></span> <span data-ttu-id="3a675-134">La stringa viene restituita al componente per la visualizzazione.</span><span class="sxs-lookup"><span data-stu-id="3a675-134">The string is returned to the component for display.</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/call-js-example.razor?highlight=2,34-35)]

## <a name="ijsruntime"></a><span data-ttu-id="3a675-135">IJSRuntime</span><span class="sxs-lookup"><span data-stu-id="3a675-135">IJSRuntime</span></span>

<span data-ttu-id="3a675-136">Per usare l' `IJSRuntime` astrazione, adottare uno degli approcci seguenti:</span><span class="sxs-lookup"><span data-stu-id="3a675-136">To use the `IJSRuntime` abstraction, adopt any of the following approaches:</span></span>

* <span data-ttu-id="3a675-137">Inserire l' `IJSRuntime` astrazione nel Razor componente (*Razor*):</span><span class="sxs-lookup"><span data-stu-id="3a675-137">Inject the `IJSRuntime` abstraction into the Razor component (*.razor*):</span></span>

  [!code-razor[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction.razor?highlight=1)]

  <span data-ttu-id="3a675-138">All'interno dell' `<head>` elemento di *wwwroot/index.html* ( Blazor Webassembly) o *pages/_Host. cshtml* ( Blazor Server), fornire una `handleTickerChanged` funzione JavaScript.</span><span class="sxs-lookup"><span data-stu-id="3a675-138">Inside the `<head>` element of *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server), provide a `handleTickerChanged` JavaScript function.</span></span> <span data-ttu-id="3a675-139">La funzione viene chiamata con `IJSRuntime.InvokeVoidAsync` e non restituisce un valore:</span><span class="sxs-lookup"><span data-stu-id="3a675-139">The function is called with `IJSRuntime.InvokeVoidAsync` and doesn't return a value:</span></span>

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged1.html)]

* <span data-ttu-id="3a675-140">Inserire l' `IJSRuntime` astrazione in una classe (*. cs*):</span><span class="sxs-lookup"><span data-stu-id="3a675-140">Inject the `IJSRuntime` abstraction into a class (*.cs*):</span></span>

  [!code-csharp[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction-class.cs?highlight=5)]

  <span data-ttu-id="3a675-141">All'interno dell' `<head>` elemento di *wwwroot/index.html* ( Blazor Webassembly) o *pages/_Host. cshtml* ( Blazor Server), fornire una `handleTickerChanged` funzione JavaScript.</span><span class="sxs-lookup"><span data-stu-id="3a675-141">Inside the `<head>` element of *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server), provide a `handleTickerChanged` JavaScript function.</span></span> <span data-ttu-id="3a675-142">La funzione viene chiamata con `JSRuntime.InvokeAsync` e restituisce un valore:</span><span class="sxs-lookup"><span data-stu-id="3a675-142">The function is called with `JSRuntime.InvokeAsync` and returns a value:</span></span>

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged2.html)]

* <span data-ttu-id="3a675-143">Per la generazione di contenuto dinamico con [BuildRenderTree](xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic), usare l' `[Inject]` attributo:</span><span class="sxs-lookup"><span data-stu-id="3a675-143">For dynamic content generation with [BuildRenderTree](xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic), use the `[Inject]` attribute:</span></span>

  ```razor
  [Inject]
  IJSRuntime JSRuntime { get; set; }
  ```

<span data-ttu-id="3a675-144">Nell'app di esempio lato client che accompagna questo argomento sono disponibili due funzioni JavaScript per l'app che interagiscono con il DOM per ricevere l'input dell'utente e visualizzare un messaggio di benvenuto:</span><span class="sxs-lookup"><span data-stu-id="3a675-144">In the client-side sample app that accompanies this topic, two JavaScript functions are available to the app that interact with the DOM to receive user input and display a welcome message:</span></span>

* <span data-ttu-id="3a675-145">`showPrompt`&ndash;Genera un messaggio di richiesta per accettare l'input dell'utente (il nome dell'utente) e restituisce il nome al chiamante.</span><span class="sxs-lookup"><span data-stu-id="3a675-145">`showPrompt` &ndash; Produces a prompt to accept user input (the user's name) and returns the name to the caller.</span></span>
* <span data-ttu-id="3a675-146">`displayWelcome`&ndash;Assegna un messaggio di benvenuto dal chiamante a un oggetto DOM con un valore `id` di `welcome` .</span><span class="sxs-lookup"><span data-stu-id="3a675-146">`displayWelcome` &ndash; Assigns a welcome message from the caller to a DOM object with an `id` of `welcome`.</span></span>

<span data-ttu-id="3a675-147">*wwwroot/exampleJsInterop. js*:</span><span class="sxs-lookup"><span data-stu-id="3a675-147">*wwwroot/exampleJsInterop.js*:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=2-7)]

<span data-ttu-id="3a675-148">Inserire il `<script>` tag che fa riferimento al file JavaScript nel file *wwwroot/index.html* ( Blazor webassembly) o nel file *pages/_Host. cshtml* ( Blazor Server).</span><span class="sxs-lookup"><span data-stu-id="3a675-148">Place the `<script>` tag that references the JavaScript file in the *wwwroot/index.html* file (Blazor WebAssembly) or *Pages/_Host.cshtml* file (Blazor Server).</span></span>

<span data-ttu-id="3a675-149">*wwwroot/index.html* ( Blazor webassembly):</span><span class="sxs-lookup"><span data-stu-id="3a675-149">*wwwroot/index.html* (Blazor WebAssembly):</span></span>

[!code-html[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/index.html?highlight=22)]

<span data-ttu-id="3a675-150">*Pagine/_Host. cshtml* ( Blazor Server):</span><span class="sxs-lookup"><span data-stu-id="3a675-150">*Pages/_Host.cshtml* (Blazor Server):</span></span>

[!code-cshtml[](./common/samples/3.x/BlazorServerSample/Pages/_Host.cshtml?highlight=35)]

<span data-ttu-id="3a675-151">Non inserire un `<script>` tag in un file di componente perché il `<script>` tag non può essere aggiornato dinamicamente.</span><span class="sxs-lookup"><span data-stu-id="3a675-151">Don't place a `<script>` tag in a component file because the `<script>` tag can't be updated dynamically.</span></span>

<span data-ttu-id="3a675-152">I metodi .NET interoperano con le funzioni JavaScript nel file *exampleJsInterop. js* chiamando `IJSRuntime.InvokeAsync<T>` .</span><span class="sxs-lookup"><span data-stu-id="3a675-152">.NET methods interop with the JavaScript functions in the *exampleJsInterop.js* file by calling `IJSRuntime.InvokeAsync<T>`.</span></span>

<span data-ttu-id="3a675-153">L' `IJSRuntime` astrazione è asincrona per consentire Blazor scenari server.</span><span class="sxs-lookup"><span data-stu-id="3a675-153">The `IJSRuntime` abstraction is asynchronous to allow for Blazor Server scenarios.</span></span> <span data-ttu-id="3a675-154">Se l'app è un' Blazor app webassembly e si vuole richiamare una funzione JavaScript in modo sincrono, `IJSInProcessRuntime` abbandono e chiamano `Invoke<T>` invece.</span><span class="sxs-lookup"><span data-stu-id="3a675-154">If the app is a Blazor WebAssembly app and you want to invoke a JavaScript function synchronously, downcast to `IJSInProcessRuntime` and call `Invoke<T>` instead.</span></span> <span data-ttu-id="3a675-155">È consigliabile che la maggior parte delle librerie di interoperabilità JS usi le API asincrone per assicurarsi che le librerie siano disponibili in tutti gli scenari.</span><span class="sxs-lookup"><span data-stu-id="3a675-155">We recommend that most JS interop libraries use the async APIs to ensure that the libraries are available in all scenarios.</span></span>

<span data-ttu-id="3a675-156">L'app di esempio include un componente per dimostrare l'interoperabilità JS.</span><span class="sxs-lookup"><span data-stu-id="3a675-156">The sample app includes a component to demonstrate JS interop.</span></span> <span data-ttu-id="3a675-157">Il componente:</span><span class="sxs-lookup"><span data-stu-id="3a675-157">The component:</span></span>

* <span data-ttu-id="3a675-158">Riceve l'input dell'utente tramite un prompt di JavaScript.</span><span class="sxs-lookup"><span data-stu-id="3a675-158">Receives user input via a JavaScript prompt.</span></span>
* <span data-ttu-id="3a675-159">Restituisce il testo al componente per l'elaborazione.</span><span class="sxs-lookup"><span data-stu-id="3a675-159">Returns the text to the component for processing.</span></span>
* <span data-ttu-id="3a675-160">Chiama una seconda funzione JavaScript che interagisce con il DOM per visualizzare un messaggio di benvenuto.</span><span class="sxs-lookup"><span data-stu-id="3a675-160">Calls a second JavaScript function that interacts with the DOM to display a welcome message.</span></span>

<span data-ttu-id="3a675-161">*Pages/JSInterop. Razor*:</span><span class="sxs-lookup"><span data-stu-id="3a675-161">*Pages/JSInterop.razor*:</span></span>

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

1. <span data-ttu-id="3a675-162">Quando `TriggerJsPrompt` viene eseguito selezionando il pulsante di **richiesta trigger JavaScript** del componente, `showPrompt` viene chiamata la funzione JavaScript fornita nel file *wwwroot/exampleJsInterop. js* .</span><span class="sxs-lookup"><span data-stu-id="3a675-162">When `TriggerJsPrompt` is executed by selecting the component's **Trigger JavaScript Prompt** button, the JavaScript `showPrompt` function provided in the *wwwroot/exampleJsInterop.js* file is called.</span></span>
1. <span data-ttu-id="3a675-163">La `showPrompt` funzione accetta l'input dell'utente (il nome dell'utente), che è codificato in HTML e restituito al componente.</span><span class="sxs-lookup"><span data-stu-id="3a675-163">The `showPrompt` function accepts user input (the user's name), which is HTML-encoded and returned to the component.</span></span> <span data-ttu-id="3a675-164">Il componente archivia il nome dell'utente in una variabile locale, `name` .</span><span class="sxs-lookup"><span data-stu-id="3a675-164">The component stores the user's name in a local variable, `name`.</span></span>
1. <span data-ttu-id="3a675-165">La stringa archiviata in `name` viene incorporata in un messaggio di benvenuto, che viene passato a una funzione JavaScript, `displayWelcome` , che esegue il rendering del messaggio di benvenuto in un tag di intestazione.</span><span class="sxs-lookup"><span data-stu-id="3a675-165">The string stored in `name` is incorporated into a welcome message, which is passed to a JavaScript function, `displayWelcome`, which renders the welcome message into a heading tag.</span></span>

## <a name="call-a-void-javascript-function"></a><span data-ttu-id="3a675-166">Chiamare una funzione JavaScript void</span><span class="sxs-lookup"><span data-stu-id="3a675-166">Call a void JavaScript function</span></span>

<span data-ttu-id="3a675-167">Le funzioni JavaScript che restituiscono [void (0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) o [undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined) vengono chiamate con `IJSRuntime.InvokeVoidAsync` .</span><span class="sxs-lookup"><span data-stu-id="3a675-167">JavaScript functions that return [void(0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) or [undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined) are called with `IJSRuntime.InvokeVoidAsync`.</span></span>

## <a name="detect-when-a-blazor-server-app-is-prerendering"></a><span data-ttu-id="3a675-168">Rilevare il momento in cui viene eseguito il Blazor prerendering di un'app Server</span><span class="sxs-lookup"><span data-stu-id="3a675-168">Detect when a Blazor Server app is prerendering</span></span>
 
[!INCLUDE[](~/includes/blazor-prerendering.md)]

## <a name="capture-references-to-elements"></a><span data-ttu-id="3a675-169">Acquisisci riferimenti a elementi</span><span class="sxs-lookup"><span data-stu-id="3a675-169">Capture references to elements</span></span>

<span data-ttu-id="3a675-170">Alcuni scenari di interoperabilità JS richiedono riferimenti agli elementi HTML.</span><span class="sxs-lookup"><span data-stu-id="3a675-170">Some JS interop scenarios require references to HTML elements.</span></span> <span data-ttu-id="3a675-171">Ad esempio, una libreria dell'interfaccia utente può richiedere un riferimento a un elemento per l'inizializzazione o potrebbe essere necessario chiamare API simili a quelle di un elemento, ad esempio `focus` o `play` .</span><span class="sxs-lookup"><span data-stu-id="3a675-171">For example, a UI library may require an element reference for initialization, or you might need to call command-like APIs on an element, such as `focus` or `play`.</span></span>

<span data-ttu-id="3a675-172">Acquisire i riferimenti agli elementi HTML in un componente usando l'approccio seguente:</span><span class="sxs-lookup"><span data-stu-id="3a675-172">Capture references to HTML elements in a component using the following approach:</span></span>

* <span data-ttu-id="3a675-173">Aggiungere un `@ref` attributo all'elemento HTML.</span><span class="sxs-lookup"><span data-stu-id="3a675-173">Add an `@ref` attribute to the HTML element.</span></span>
* <span data-ttu-id="3a675-174">Definire un campo di tipo `ElementReference` il cui nome corrisponde al valore dell' `@ref` attributo.</span><span class="sxs-lookup"><span data-stu-id="3a675-174">Define a field of type `ElementReference` whose name matches the value of the `@ref` attribute.</span></span>

<span data-ttu-id="3a675-175">Nell'esempio seguente viene illustrata l'acquisizione di un riferimento all' `username` `<input>` elemento:</span><span class="sxs-lookup"><span data-stu-id="3a675-175">The following example shows capturing a reference to the `username` `<input>` element:</span></span>

```razor
<input @ref="username" ... />

@code {
    ElementReference username;
}
```

> [!WARNING]
> <span data-ttu-id="3a675-176">Usare solo un riferimento a un elemento per mutare il contenuto di un elemento vuoto che non interagisce con Blazor .</span><span class="sxs-lookup"><span data-stu-id="3a675-176">Only use an element reference to mutate the contents of an empty element that doesn't interact with Blazor.</span></span> <span data-ttu-id="3a675-177">Questo scenario è utile quando un'API di terze parti fornisce contenuto all'elemento.</span><span class="sxs-lookup"><span data-stu-id="3a675-177">This scenario is useful when a 3rd party API supplies content to the element.</span></span> <span data-ttu-id="3a675-178">Poiché Blazor non interagisce con l'elemento, non è possibile che si verifichi un conflitto tra Blazor la rappresentazione dell'elemento e il Dom.</span><span class="sxs-lookup"><span data-stu-id="3a675-178">Because Blazor doesn't interact with the element, there's no possibility of a conflict between Blazor's representation of the element and the DOM.</span></span>
>
> <span data-ttu-id="3a675-179">Nell'esempio seguente è *pericoloso* modificare il contenuto dell'elenco non ordinato ( `ul` ) perché Blazor interagisce con il Dom per popolare gli elementi elenco di questo elemento ( `<li>` ):</span><span class="sxs-lookup"><span data-stu-id="3a675-179">In the following example, it's *dangerous* to mutate the contents of the unordered list (`ul`) because Blazor interacts with the DOM to populate this element's list items (`<li>`):</span></span>
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
> <span data-ttu-id="3a675-180">Se l'interoperabilità JS modifica il contenuto dell'elemento `MyList` e Blazor tenta di applicare differenze all'elemento, le differenze non corrispondono al Dom.</span><span class="sxs-lookup"><span data-stu-id="3a675-180">If JS interop mutates the contents of element `MyList` and Blazor attempts to apply diffs to the element, the diffs won't match the DOM.</span></span>

<span data-ttu-id="3a675-181">Per quanto riguarda il codice .NET, un `ElementReference` è un handle opaco.</span><span class="sxs-lookup"><span data-stu-id="3a675-181">As far as .NET code is concerned, an `ElementReference` is an opaque handle.</span></span> <span data-ttu-id="3a675-182">L' *unica* cosa che è possibile fare con `ElementReference` è passarla al codice JavaScript tramite l'interoperabilità js.</span><span class="sxs-lookup"><span data-stu-id="3a675-182">The *only* thing you can do with `ElementReference` is pass it through to JavaScript code via JS interop.</span></span> <span data-ttu-id="3a675-183">Quando si esegue questa operazione, il codice sul lato JavaScript riceve un' `HTMLElement` istanza, che può essere usata con le API DOM normali.</span><span class="sxs-lookup"><span data-stu-id="3a675-183">When you do so, the JavaScript-side code receives an `HTMLElement` instance, which it can use with normal DOM APIs.</span></span>

<span data-ttu-id="3a675-184">Il codice seguente, ad esempio, definisce un metodo di estensione .NET che consente di impostare lo stato attivo su un elemento:</span><span class="sxs-lookup"><span data-stu-id="3a675-184">For example, the following code defines a .NET extension method that enables setting the focus on an element:</span></span>

<span data-ttu-id="3a675-185">*exampleJsInterop. js*:</span><span class="sxs-lookup"><span data-stu-id="3a675-185">*exampleJsInterop.js*:</span></span>

```javascript
window.exampleJsFunctions = {
  focusElement : function (element) {
    element.focus();
  }
}
```

<span data-ttu-id="3a675-186">Per chiamare una funzione JavaScript che non restituisce un valore, usare `IJSRuntime.InvokeVoidAsync` .</span><span class="sxs-lookup"><span data-stu-id="3a675-186">To call a JavaScript function that doesn't return a value, use `IJSRuntime.InvokeVoidAsync`.</span></span> <span data-ttu-id="3a675-187">Il codice seguente imposta lo stato attivo sull'input del nome utente chiamando la funzione JavaScript precedente con l'acquisizione `ElementReference` :</span><span class="sxs-lookup"><span data-stu-id="3a675-187">The following code sets the focus on the username input by calling the preceding JavaScript function with the captured `ElementReference`:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component1.razor?highlight=1,3,11-12)]

<span data-ttu-id="3a675-188">Per usare un metodo di estensione, creare un metodo di estensione statico che riceva l' `IJSRuntime` istanza:</span><span class="sxs-lookup"><span data-stu-id="3a675-188">To use an extension method, create a static extension method that receives the `IJSRuntime` instance:</span></span>

```csharp
public static async Task Focus(this ElementReference elementRef, IJSRuntime jsRuntime)
{
    await jsRuntime.InvokeVoidAsync(
        "exampleJsFunctions.focusElement", elementRef);
}
```

<span data-ttu-id="3a675-189">Il `Focus` metodo viene chiamato direttamente nell'oggetto.</span><span class="sxs-lookup"><span data-stu-id="3a675-189">The `Focus` method is called directly on the object.</span></span> <span data-ttu-id="3a675-190">Nell'esempio seguente si presuppone che il `Focus` metodo sia disponibile dallo `JsInteropClasses` spazio dei nomi:</span><span class="sxs-lookup"><span data-stu-id="3a675-190">The following example assumes that the `Focus` method is available from the `JsInteropClasses` namespace:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component2.razor?highlight=1-4,12)]

> [!IMPORTANT]
> <span data-ttu-id="3a675-191">La `username` variabile viene popolata solo dopo il rendering del componente.</span><span class="sxs-lookup"><span data-stu-id="3a675-191">The `username` variable is only populated after the component is rendered.</span></span> <span data-ttu-id="3a675-192">Se un oggetto non popolato `ElementReference` viene passato al codice JavaScript, il codice JavaScript riceve un valore di `null` .</span><span class="sxs-lookup"><span data-stu-id="3a675-192">If an unpopulated `ElementReference` is passed to JavaScript code, the JavaScript code receives a value of `null`.</span></span> <span data-ttu-id="3a675-193">Per modificare i riferimenti agli elementi dopo che il componente ha terminato il rendering (per impostare lo stato attivo iniziale su un elemento), usare i metodi del ciclo di vita dei [componenti OnAfterRenderAsync o OnAfterRender](xref:blazor/lifecycle#after-component-render).</span><span class="sxs-lookup"><span data-stu-id="3a675-193">To manipulate element references after the component has finished rendering (to set the initial focus on an element) use the [OnAfterRenderAsync or OnAfterRender component lifecycle methods](xref:blazor/lifecycle#after-component-render).</span></span>

<span data-ttu-id="3a675-194">Quando si utilizzano tipi generici e si restituisce un valore, utilizzare [ValueTask \< T>](xref:System.Threading.Tasks.ValueTask`1):</span><span class="sxs-lookup"><span data-stu-id="3a675-194">When working with generic types and returning a value, use [ValueTask\<T>](xref:System.Threading.Tasks.ValueTask`1):</span></span>

```csharp
public static ValueTask<T> GenericMethod<T>(this ElementReference elementRef, 
    IJSRuntime jsRuntime)
{
    return jsRuntime.InvokeAsync<T>(
        "exampleJsFunctions.doSomethingGeneric", elementRef);
}
```

<span data-ttu-id="3a675-195">`GenericMethod`viene chiamato direttamente sull'oggetto con un tipo.</span><span class="sxs-lookup"><span data-stu-id="3a675-195">`GenericMethod` is called directly on the object with a type.</span></span> <span data-ttu-id="3a675-196">Nell'esempio seguente si presuppone che l'oggetto `GenericMethod` sia disponibile dallo `JsInteropClasses` spazio dei nomi:</span><span class="sxs-lookup"><span data-stu-id="3a675-196">The following example assumes that the `GenericMethod` is available from the `JsInteropClasses` namespace:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component3.razor?highlight=17)]

## <a name="reference-elements-across-components"></a><span data-ttu-id="3a675-197">Elementi di riferimento tra i componenti</span><span class="sxs-lookup"><span data-stu-id="3a675-197">Reference elements across components</span></span>

<span data-ttu-id="3a675-198">Un oggetto `ElementReference` è garantito solo valido nel metodo di un componente `OnAfterRender` (e un riferimento a un elemento è `struct` ), pertanto non è possibile passare un riferimento a un elemento tra i componenti.</span><span class="sxs-lookup"><span data-stu-id="3a675-198">An `ElementReference` is only guaranteed valid in a component's `OnAfterRender` method (and an element reference is a `struct`), so an element reference can't be passed between components.</span></span>

<span data-ttu-id="3a675-199">Affinché un componente padre renda disponibile un riferimento a un elemento per altri componenti, il componente padre può:</span><span class="sxs-lookup"><span data-stu-id="3a675-199">For a parent component to make an element reference available to other components, the parent component can:</span></span>

* <span data-ttu-id="3a675-200">Consenti ai componenti figlio di registrare i callback.</span><span class="sxs-lookup"><span data-stu-id="3a675-200">Allow child components to register callbacks.</span></span>
* <span data-ttu-id="3a675-201">Richiamare i callback registrati durante l' `OnAfterRender` evento con il riferimento all'elemento passato.</span><span class="sxs-lookup"><span data-stu-id="3a675-201">Invoke the registered callbacks during the `OnAfterRender` event with the passed element reference.</span></span> <span data-ttu-id="3a675-202">Indirettamente, questo approccio consente ai componenti figlio di interagire con il riferimento all'elemento padre.</span><span class="sxs-lookup"><span data-stu-id="3a675-202">Indirectly, this approach allows child components to interact with the parent's element reference.</span></span>

<span data-ttu-id="3a675-203">Nell' Blazor esempio di webassembly seguente viene illustrato l'approccio.</span><span class="sxs-lookup"><span data-stu-id="3a675-203">The following Blazor WebAssembly example illustrates the approach.</span></span>

<span data-ttu-id="3a675-204">In `<head>` *wwwroot/index.html*:</span><span class="sxs-lookup"><span data-stu-id="3a675-204">In the `<head>` of *wwwroot/index.html*:</span></span>

```html
<style>
    .red { color: red }
</style>
```

<span data-ttu-id="3a675-205">In `<body>` *wwwroot/index.html*:</span><span class="sxs-lookup"><span data-stu-id="3a675-205">In the `<body>` of *wwwroot/index.html*:</span></span>

```html
<script>
    function setElementClass(element, className) {
        /** @type {HTMLElement} **/
        var myElement = element;
        myElement.classList.add(className);
    }
</script>
```

<span data-ttu-id="3a675-206">*Pages/index. Razor* (componente padre):</span><span class="sxs-lookup"><span data-stu-id="3a675-206">*Pages/Index.razor* (parent component):</span></span>

```razor
@page "/"

<h1 @ref="title">Hello, world!</h1>

Welcome to your new app.

<SurveyPrompt Parent="this" Title="How is Blazor working for you?" />
```

<span data-ttu-id="3a675-207">*Pages/index. Razor. cs*:</span><span class="sxs-lookup"><span data-stu-id="3a675-207">*Pages/Index.razor.cs*:</span></span>

```csharp
using System;
using System.Collections.Generic;
using Microsoft.AspNetCore.Components;

namespace BlazorSample.Pages
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

<span data-ttu-id="3a675-208">*Shared/SurveyPrompt. Razor* (componente figlio):</span><span class="sxs-lookup"><span data-stu-id="3a675-208">*Shared/SurveyPrompt.razor* (child component):</span></span>

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

<span data-ttu-id="3a675-209">*Shared/SurveyPrompt. Razor. cs*:</span><span class="sxs-lookup"><span data-stu-id="3a675-209">*Shared/SurveyPrompt.razor.cs*:</span></span>

```csharp
using System;
using Microsoft.AspNetCore.Components;

namespace BlazorSample.Shared
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

## <a name="harden-js-interop-calls"></a><span data-ttu-id="3a675-210">Chiamate di interoperabilità JS di Harden</span><span class="sxs-lookup"><span data-stu-id="3a675-210">Harden JS interop calls</span></span>

<span data-ttu-id="3a675-211">L'interoperabilità JS potrebbe non riuscire a causa di errori di rete e deve essere considerata inaffidabile.</span><span class="sxs-lookup"><span data-stu-id="3a675-211">JS interop may fail due to networking errors and should be treated as unreliable.</span></span> <span data-ttu-id="3a675-212">Per impostazione predefinita, un' Blazor app Server verifica il timeout delle chiamate di interoperabilità js nel server dopo un minuto.</span><span class="sxs-lookup"><span data-stu-id="3a675-212">By default, a Blazor Server app times out JS interop calls on the server after one minute.</span></span> <span data-ttu-id="3a675-213">Se un'app può tollerare un timeout più aggressivo, ad esempio 10 secondi, impostare il timeout usando uno degli approcci seguenti:</span><span class="sxs-lookup"><span data-stu-id="3a675-213">If an app can tolerate a more aggressive timeout, such as 10 seconds, set the timeout using one of the following approaches:</span></span>

* <span data-ttu-id="3a675-214">A livello globale in `Startup.ConfigureServices` specificare il timeout:</span><span class="sxs-lookup"><span data-stu-id="3a675-214">Globally in `Startup.ConfigureServices`, specify the timeout:</span></span>

  ```csharp
  services.AddServerSideBlazor(
      options => options.JSInteropDefaultCallTimeout = TimeSpan.FromSeconds({SECONDS}));
  ```

* <span data-ttu-id="3a675-215">Per chiamata nel codice componente, una singola chiamata può specificare il timeout:</span><span class="sxs-lookup"><span data-stu-id="3a675-215">Per-invocation in component code, a single call can specify the timeout:</span></span>

  ```csharp
  var result = await JSRuntime.InvokeAsync<string>("MyJSOperation", 
      TimeSpan.FromSeconds({SECONDS}), new[] { "Arg1" });
  ```

<span data-ttu-id="3a675-216">Per ulteriori informazioni sull'esaurimento delle risorse, vedere <xref:security/blazor/server/threat-mitigation> .</span><span class="sxs-lookup"><span data-stu-id="3a675-216">For more information on resource exhaustion, see <xref:security/blazor/server/threat-mitigation>.</span></span>

[!INCLUDE[Share interop code in a class library](~/includes/blazor-share-interop-code.md)]

## <a name="avoid-circular-object-references"></a><span data-ttu-id="3a675-217">Evitare riferimenti a oggetti circolari</span><span class="sxs-lookup"><span data-stu-id="3a675-217">Avoid circular object references</span></span>

<span data-ttu-id="3a675-218">Gli oggetti che contengono riferimenti circolari non possono essere serializzati sul client per uno dei seguenti:</span><span class="sxs-lookup"><span data-stu-id="3a675-218">Objects that contain circular references can't be serialized on the client for either:</span></span>

* <span data-ttu-id="3a675-219">Chiamate al metodo .NET.</span><span class="sxs-lookup"><span data-stu-id="3a675-219">.NET method calls.</span></span>
* <span data-ttu-id="3a675-220">Chiamate al metodo JavaScript da C# quando il tipo restituito contiene riferimenti circolari.</span><span class="sxs-lookup"><span data-stu-id="3a675-220">JavaScript method calls from C# when the return type has circular references.</span></span>

<span data-ttu-id="3a675-221">Per ulteriori informazioni, vedere i seguenti problemi:</span><span class="sxs-lookup"><span data-stu-id="3a675-221">For more information, see the following issues:</span></span>

* [<span data-ttu-id="3a675-222">I riferimenti circolari non sono supportati, accettano due (DotNet/aspnetcore #20525)</span><span class="sxs-lookup"><span data-stu-id="3a675-222">Circular references are not supported, take two (dotnet/aspnetcore #20525)</span></span>](https://github.com/dotnet/aspnetcore/issues/20525)
* [<span data-ttu-id="3a675-223">Proposta: aggiungere un meccanismo per gestire i riferimenti circolari durante la serializzazione (DotNet/Runtime #30820)</span><span class="sxs-lookup"><span data-stu-id="3a675-223">Proposal: Add mechanism to handle circular references when serializing (dotnet/runtime #30820)</span></span>](https://github.com/dotnet/runtime/issues/30820)

## <a name="additional-resources"></a><span data-ttu-id="3a675-224">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="3a675-224">Additional resources</span></span>

* <xref:blazor/call-dotnet-from-javascript>
* [<span data-ttu-id="3a675-225">Esempio di InteropComponent. Razor (repository GitHub DotNet/AspNetCore, Branch versione 3,1)</span><span class="sxs-lookup"><span data-stu-id="3a675-225">InteropComponent.razor example (dotnet/AspNetCore GitHub repository, 3.1 release branch)</span></span>](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
* <span data-ttu-id="3a675-226">[Eseguire trasferimenti di dati di grandi dimensioni nelle Blazor app Server](xref:blazor/advanced-scenarios#perform-large-data-transfers-in-blazor-server-apps)</span><span class="sxs-lookup"><span data-stu-id="3a675-226">[Perform large data transfers in Blazor Server apps](xref:blazor/advanced-scenarios#perform-large-data-transfers-in-blazor-server-apps)</span></span>
