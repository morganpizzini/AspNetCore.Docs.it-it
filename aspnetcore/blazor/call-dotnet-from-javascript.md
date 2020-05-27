---
<span data-ttu-id="b5c5a-101">title:' Call .NET Methods from JavaScript Functions in ASP.NET Core Blazor ' Author: Description:' Learn how to Invoke .NET Methods from JavaScript Functions in Blazor Apps '.</span><span class="sxs-lookup"><span data-stu-id="b5c5a-101">title: 'Call .NET methods from JavaScript functions in ASP.NET Core Blazor' author: description: 'Learn how to invoke .NET methods from JavaScript functions in Blazor apps.'</span></span>
<span data-ttu-id="b5c5a-102">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="b5c5a-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b5c5a-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b5c5a-103">'Blazor'</span></span>
- <span data-ttu-id="b5c5a-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b5c5a-104">'Identity'</span></span>
- <span data-ttu-id="b5c5a-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b5c5a-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="b5c5a-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b5c5a-106">'Razor'</span></span>
- <span data-ttu-id="b5c5a-107">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="b5c5a-107">'SignalR' uid:</span></span> 

---
# <a name="call-net-methods-from-javascript-functions-in-aspnet-core-blazor"></a><span data-ttu-id="b5c5a-108">Chiamare i metodi .NET da funzioni JavaScript in ASP.NET CoreBlazor</span><span class="sxs-lookup"><span data-stu-id="b5c5a-108">Call .NET methods from JavaScript functions in ASP.NET Core Blazor</span></span>

<span data-ttu-id="b5c5a-109">Di [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), [Rudrawadi](http://wisne.co)e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="b5c5a-109">By [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), [Shashikant Rudrawadi](http://wisne.co), and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="b5c5a-110">Un' Blazor app può richiamare funzioni JavaScript da metodi .NET e metodi .NET da funzioni JavaScript.</span><span class="sxs-lookup"><span data-stu-id="b5c5a-110">A Blazor app can invoke JavaScript functions from .NET methods and .NET methods from JavaScript functions.</span></span> <span data-ttu-id="b5c5a-111">Questi scenari sono detti *interoperabilità JavaScript (interoperabilità* *JS*).</span><span class="sxs-lookup"><span data-stu-id="b5c5a-111">These scenarios are called *JavaScript interoperability* (*JS interop*).</span></span>

<span data-ttu-id="b5c5a-112">Questo articolo descrive come richiamare i metodi .NET da JavaScript.</span><span class="sxs-lookup"><span data-stu-id="b5c5a-112">This article covers invoking .NET methods from JavaScript.</span></span> <span data-ttu-id="b5c5a-113">Per informazioni su come chiamare funzioni JavaScript da .NET, vedere <xref:blazor/call-javascript-from-dotnet> .</span><span class="sxs-lookup"><span data-stu-id="b5c5a-113">For information on how to call JavaScript functions from .NET, see <xref:blazor/call-javascript-from-dotnet>.</span></span>

<span data-ttu-id="b5c5a-114">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="b5c5a-114">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="static-net-method-call"></a><span data-ttu-id="b5c5a-115">Chiamata al metodo .NET statico</span><span class="sxs-lookup"><span data-stu-id="b5c5a-115">Static .NET method call</span></span>

<span data-ttu-id="b5c5a-116">Per richiamare un metodo .NET statico da JavaScript, usare le `DotNet.invokeMethod` `DotNet.invokeMethodAsync` funzioni o.</span><span class="sxs-lookup"><span data-stu-id="b5c5a-116">To invoke a static .NET method from JavaScript, use the `DotNet.invokeMethod` or `DotNet.invokeMethodAsync` functions.</span></span> <span data-ttu-id="b5c5a-117">Passare l'identificatore del metodo statico che si desidera chiamare, il nome dell'assembly che contiene la funzione e gli eventuali argomenti.</span><span class="sxs-lookup"><span data-stu-id="b5c5a-117">Pass in the identifier of the static method you wish to call, the name of the assembly containing the function, and any arguments.</span></span> <span data-ttu-id="b5c5a-118">La versione asincrona è preferibile per supportare Blazor scenari server.</span><span class="sxs-lookup"><span data-stu-id="b5c5a-118">The asynchronous version is preferred to support Blazor Server scenarios.</span></span> <span data-ttu-id="b5c5a-119">Il metodo .NET deve essere pubblico, statico e avere l' [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute) attributo.</span><span class="sxs-lookup"><span data-stu-id="b5c5a-119">The .NET method must be public, static, and have the [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute) attribute.</span></span> <span data-ttu-id="b5c5a-120">La chiamata ai metodi generici aperti non è attualmente supportata.</span><span class="sxs-lookup"><span data-stu-id="b5c5a-120">Calling open generic methods isn't currently supported.</span></span>

<span data-ttu-id="b5c5a-121">L'app di esempio include un metodo C# per restituire una `int` matrice.</span><span class="sxs-lookup"><span data-stu-id="b5c5a-121">The sample app includes a C# method to return an `int` array.</span></span> <span data-ttu-id="b5c5a-122">L' [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute) attributo viene applicato al metodo.</span><span class="sxs-lookup"><span data-stu-id="b5c5a-122">The [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute) attribute is applied to the method.</span></span>

<span data-ttu-id="b5c5a-123">*Pages/JsInterop. Razor*:</span><span class="sxs-lookup"><span data-stu-id="b5c5a-123">*Pages/JsInterop.razor*:</span></span>

```razor
<button type="button" class="btn btn-primary"
        onclick="exampleJsFunctions.returnArrayAsyncJs()">
    Trigger .NET static method ReturnArrayAsync
</button>

@code {
    [JSInvokable]
    public static Task<int[]> ReturnArrayAsync()
    {
        return Task.FromResult(new int[] { 1, 2, 3 });
    }
}
```

<span data-ttu-id="b5c5a-124">JavaScript servito al client richiama il metodo C# .NET.</span><span class="sxs-lookup"><span data-stu-id="b5c5a-124">JavaScript served to the client invokes the C# .NET method.</span></span>

<span data-ttu-id="b5c5a-125">*wwwroot/exampleJsInterop. js*:</span><span class="sxs-lookup"><span data-stu-id="b5c5a-125">*wwwroot/exampleJsInterop.js*:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=8-14)]

<span data-ttu-id="b5c5a-126">Quando si seleziona il pulsante **trigger .net static method ReturnArrayAsync** , esaminare l'output della console negli strumenti di sviluppo Web del browser.</span><span class="sxs-lookup"><span data-stu-id="b5c5a-126">When the **Trigger .NET static method ReturnArrayAsync** button is selected, examine the console output in the browser's web developer tools.</span></span>

<span data-ttu-id="b5c5a-127">L'output della console è:</span><span class="sxs-lookup"><span data-stu-id="b5c5a-127">The console output is:</span></span>

```console
Array(4) [ 1, 2, 3, 4 ]
```

<span data-ttu-id="b5c5a-128">Il quarto valore della matrice viene inserito nella matrice ( `data.push(4);` ) restituito da `ReturnArrayAsync` .</span><span class="sxs-lookup"><span data-stu-id="b5c5a-128">The fourth array value is pushed to the array (`data.push(4);`) returned by `ReturnArrayAsync`.</span></span>

<span data-ttu-id="b5c5a-129">Per impostazione predefinita, l'identificatore del metodo è il nome del metodo, ma è possibile specificare un identificatore diverso usando il [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute) costruttore dell'attributo:</span><span class="sxs-lookup"><span data-stu-id="b5c5a-129">By default, the method identifier is the method name, but you can specify a different identifier using the [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute) attribute constructor:</span></span>

```csharp
@code {
    [JSInvokable("DifferentMethodName")]
    public static Task<int[]> ReturnArrayAsync()
    {
        return Task.FromResult(new int[] { 1, 2, 3 });
    }
}
```

<span data-ttu-id="b5c5a-130">Nel file JavaScript sul lato client:</span><span class="sxs-lookup"><span data-stu-id="b5c5a-130">In the client-side JavaScript file:</span></span>

```javascript
returnArrayAsyncJs: function () {
  DotNet.invokeMethodAsync('BlazorSample', 'DifferentMethodName')
    .then(data => {
      data.push(4);
      console.log(data);
    });
}
```

## <a name="instance-method-call"></a><span data-ttu-id="b5c5a-131">Chiamata al metodo di istanza</span><span class="sxs-lookup"><span data-stu-id="b5c5a-131">Instance method call</span></span>

<span data-ttu-id="b5c5a-132">È anche possibile chiamare i metodi di istanza .NET da JavaScript.</span><span class="sxs-lookup"><span data-stu-id="b5c5a-132">You can also call .NET instance methods from JavaScript.</span></span> <span data-ttu-id="b5c5a-133">Per richiamare un metodo di istanza .NET da JavaScript:</span><span class="sxs-lookup"><span data-stu-id="b5c5a-133">To invoke a .NET instance method from JavaScript:</span></span>

* <span data-ttu-id="b5c5a-134">Passa l'istanza .NET per riferimento a JavaScript:</span><span class="sxs-lookup"><span data-stu-id="b5c5a-134">Pass the .NET instance by reference to JavaScript:</span></span>
  * <span data-ttu-id="b5c5a-135">Eseguire una chiamata statica a <xref:Microsoft.JSInterop.DotNetObjectReference.Create%2A?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="b5c5a-135">Make a static call to <xref:Microsoft.JSInterop.DotNetObjectReference.Create%2A?displayProperty=nameWithType>.</span></span>
  * <span data-ttu-id="b5c5a-136">Eseguire il wrapping dell'istanza in un' <xref:Microsoft.JSInterop.DotNetObjectReference> istanza di e chiamare <xref:Microsoft.JSInterop.DotNetObjectReference.Create%2A> sull' <xref:Microsoft.JSInterop.DotNetObjectReference> istanza.</span><span class="sxs-lookup"><span data-stu-id="b5c5a-136">Wrap the instance in a <xref:Microsoft.JSInterop.DotNetObjectReference> instance and call <xref:Microsoft.JSInterop.DotNetObjectReference.Create%2A> on the <xref:Microsoft.JSInterop.DotNetObjectReference> instance.</span></span> <span data-ttu-id="b5c5a-137">Eliminazione di <xref:Microsoft.JSInterop.DotNetObjectReference> oggetti (un esempio viene visualizzato più avanti in questa sezione).</span><span class="sxs-lookup"><span data-stu-id="b5c5a-137">Dispose of <xref:Microsoft.JSInterop.DotNetObjectReference> objects (an example appears later in this section).</span></span>
* <span data-ttu-id="b5c5a-138">Richiamare i metodi di istanza .NET sull'istanza usando `invokeMethod` le `invokeMethodAsync` funzioni o.</span><span class="sxs-lookup"><span data-stu-id="b5c5a-138">Invoke .NET instance methods on the instance using the `invokeMethod` or `invokeMethodAsync` functions.</span></span> <span data-ttu-id="b5c5a-139">L'istanza di .NET può essere passata anche come argomento quando si richiamano altri metodi .NET da JavaScript.</span><span class="sxs-lookup"><span data-stu-id="b5c5a-139">The .NET instance can also be passed as an argument when invoking other .NET methods from JavaScript.</span></span>

> [!NOTE]
> <span data-ttu-id="b5c5a-140">L'app di esempio consente di registrare i messaggi nella console lato client.</span><span class="sxs-lookup"><span data-stu-id="b5c5a-140">The sample app logs messages to the client-side console.</span></span> <span data-ttu-id="b5c5a-141">Per gli esempi seguenti illustrati dall'app di esempio, esaminare l'output della console del browser negli strumenti di sviluppo del browser.</span><span class="sxs-lookup"><span data-stu-id="b5c5a-141">For the following examples demonstrated by the sample app, examine the browser's console output in the browser's developer tools.</span></span>

<span data-ttu-id="b5c5a-142">Quando il pulsante del **metodo di istanza .NET del trigger HelloHelper. sayHello** è selezionato, `ExampleJsInterop.CallHelloHelperSayHello` viene chiamato e passa un nome `Blazor` al metodo.</span><span class="sxs-lookup"><span data-stu-id="b5c5a-142">When the **Trigger .NET instance method HelloHelper.SayHello** button is selected, `ExampleJsInterop.CallHelloHelperSayHello` is called and passes a name, `Blazor`, to the method.</span></span>

<span data-ttu-id="b5c5a-143">*Pages/JsInterop. Razor*:</span><span class="sxs-lookup"><span data-stu-id="b5c5a-143">*Pages/JsInterop.razor*:</span></span>

```razor
<button type="button" class="btn btn-primary" @onclick="TriggerNetInstanceMethod">
    Trigger .NET instance method HelloHelper.SayHello
</button>

@code {
    public async Task TriggerNetInstanceMethod()
    {
        var exampleJsInterop = new ExampleJsInterop(JSRuntime);
        await exampleJsInterop.CallHelloHelperSayHello("Blazor");
    }
}
```

<span data-ttu-id="b5c5a-144">`CallHelloHelperSayHello`richiama la funzione JavaScript `sayHello` con una nuova istanza di `HelloHelper` .</span><span class="sxs-lookup"><span data-stu-id="b5c5a-144">`CallHelloHelperSayHello` invokes the JavaScript function `sayHello` with a new instance of `HelloHelper`.</span></span>

<span data-ttu-id="b5c5a-145">*JsInteropClasses/ExampleJsInterop. cs*:</span><span class="sxs-lookup"><span data-stu-id="b5c5a-145">*JsInteropClasses/ExampleJsInterop.cs*:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorWebAssemblySample/JsInteropClasses/ExampleJsInterop.cs?name=snippet1&highlight=11-18)]

<span data-ttu-id="b5c5a-146">*wwwroot/exampleJsInterop. js*:</span><span class="sxs-lookup"><span data-stu-id="b5c5a-146">*wwwroot/exampleJsInterop.js*:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=15-18)]

<span data-ttu-id="b5c5a-147">Il nome viene passato al `HelloHelper` costruttore, che imposta la `HelloHelper.Name` Proprietà.</span><span class="sxs-lookup"><span data-stu-id="b5c5a-147">The name is passed to `HelloHelper`'s constructor, which sets the `HelloHelper.Name` property.</span></span> <span data-ttu-id="b5c5a-148">Quando viene eseguita la funzione JavaScript `sayHello` , `HelloHelper.SayHello` restituisce il `Hello, {Name}!` messaggio, che viene scritto nella console dalla funzione JavaScript.</span><span class="sxs-lookup"><span data-stu-id="b5c5a-148">When the JavaScript function `sayHello` is executed, `HelloHelper.SayHello` returns the `Hello, {Name}!` message, which is written to the console by the JavaScript function.</span></span>

<span data-ttu-id="b5c5a-149">*JsInteropClasses/HelloHelper. cs*:</span><span class="sxs-lookup"><span data-stu-id="b5c5a-149">*JsInteropClasses/HelloHelper.cs*:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorWebAssemblySample/JsInteropClasses/HelloHelper.cs?name=snippet1&highlight=5,10-11)]

<span data-ttu-id="b5c5a-150">Output della console negli strumenti di sviluppo Web del browser:</span><span class="sxs-lookup"><span data-stu-id="b5c5a-150">Console output in the browser's web developer tools:</span></span>

```console
Hello, Blazor!
```

<span data-ttu-id="b5c5a-151">Per evitare una perdita di memoria e consentire Garbage Collection su un componente che crea un <xref:Microsoft.JSInterop.DotNetObjectReference> , adottare uno degli approcci seguenti:</span><span class="sxs-lookup"><span data-stu-id="b5c5a-151">To avoid a memory leak and allow garbage collection on a component that creates a <xref:Microsoft.JSInterop.DotNetObjectReference>, adopt one of the following approaches:</span></span>

* <span data-ttu-id="b5c5a-152">Eliminare l'oggetto nella classe che ha creato l' <xref:Microsoft.JSInterop.DotNetObjectReference> istanza:</span><span class="sxs-lookup"><span data-stu-id="b5c5a-152">Dispose of the object in the class that created the <xref:Microsoft.JSInterop.DotNetObjectReference> instance:</span></span>

  ```csharp
  public class ExampleJsInterop : IDisposable
  {
      private readonly IJSRuntime jsRuntime;
      private DotNetObjectReference<HelloHelper> objRef;

      public ExampleJsInterop(IJSRuntime jsRuntime)
      {
          this.jsRuntime = jsRuntime;
      }

      public ValueTask<string> CallHelloHelperSayHello(string name)
      {
          objRef = DotNetObjectReference.Create(new HelloHelper(name));

          return jsRuntime.InvokeAsync<string>(
              "exampleJsFunctions.sayHello",
              objRef);
      }

      public void Dispose()
      {
          objRef?.Dispose();
      }
  }
  ```

  <span data-ttu-id="b5c5a-153">Il modello precedente illustrato nella `ExampleJsInterop` classe può anche essere implementato in un componente:</span><span class="sxs-lookup"><span data-stu-id="b5c5a-153">The preceding pattern shown in the `ExampleJsInterop` class can also be implemented in a component:</span></span>

  ```razor
  @page "/JSInteropComponent"
  @using BlazorSample.JsInteropClasses
  @implements IDisposable
  @inject IJSRuntime JSRuntime

  <h1>JavaScript Interop</h1>

  <button type="button" class="btn btn-primary" @onclick="TriggerNetInstanceMethod">
      Trigger .NET instance method HelloHelper.SayHello
  </button>

  @code {
      private DotNetObjectReference<HelloHelper> objRef;

      public async Task TriggerNetInstanceMethod()
      {
          objRef = DotNetObjectReference.Create(new HelloHelper("Blazor"));

          await JSRuntime.InvokeAsync<string>(
              "exampleJsFunctions.sayHello",
              objRef);
      }

      public void Dispose()
      {
          objRef?.Dispose();
      }
  }
  ```

* <span data-ttu-id="b5c5a-154">Quando il componente o la classe non Elimina <xref:Microsoft.JSInterop.DotNetObjectReference> , eliminare l'oggetto nel client chiamando `.dispose()` :</span><span class="sxs-lookup"><span data-stu-id="b5c5a-154">When the component or class doesn't dispose of the <xref:Microsoft.JSInterop.DotNetObjectReference>, dispose of the object on the client by calling `.dispose()`:</span></span>

  ```javascript
  window.myFunction = (dotnetHelper) => {
    dotnetHelper.invokeMethod('BlazorSample', 'MyMethod');
    dotnetHelper.dispose();
  }
  ```

## <a name="component-instance-method-call"></a><span data-ttu-id="b5c5a-155">Chiamata al metodo dell'istanza del componente</span><span class="sxs-lookup"><span data-stu-id="b5c5a-155">Component instance method call</span></span>

<span data-ttu-id="b5c5a-156">Per richiamare i metodi .NET di un componente:</span><span class="sxs-lookup"><span data-stu-id="b5c5a-156">To invoke a component's .NET methods:</span></span>

* <span data-ttu-id="b5c5a-157">Utilizzare la `invokeMethod` `invokeMethodAsync` funzione o per eseguire una chiamata al metodo statico al componente.</span><span class="sxs-lookup"><span data-stu-id="b5c5a-157">Use the `invokeMethod` or `invokeMethodAsync` function to make a static method call to the component.</span></span>
* <span data-ttu-id="b5c5a-158">Il metodo statico del componente esegue il wrapping della chiamata al metodo di istanza come oggetto richiamato <xref:System.Action> .</span><span class="sxs-lookup"><span data-stu-id="b5c5a-158">The component's static method wraps the call to its instance method as an invoked <xref:System.Action>.</span></span>

<span data-ttu-id="b5c5a-159">Nel codice JavaScript lato client:</span><span class="sxs-lookup"><span data-stu-id="b5c5a-159">In the client-side JavaScript:</span></span>

```javascript
function updateMessageCallerJS() {
  DotNet.invokeMethod('BlazorSample', 'UpdateMessageCaller');
}
```

<span data-ttu-id="b5c5a-160">*Pages/JSInteropComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="b5c5a-160">*Pages/JSInteropComponent.razor*:</span></span>

```razor
@page "/JSInteropComponent"

<p>
    Message: @message
</p>

<p>
    <button onclick="updateMessageCallerJS()">Call JS Method</button>
</p>

@code {
    private static Action action;
    private string message = "Select the button.";

    protected override void OnInitialized()
    {
        action = UpdateMessage;
    }

    private void UpdateMessage()
    {
        message = "UpdateMessage Called!";
        StateHasChanged();
    }

    [JSInvokable]
    public static void UpdateMessageCaller()
    {
        action.Invoke();
    }
}
```

<span data-ttu-id="b5c5a-161">Quando sono presenti diversi componenti, ognuno con i metodi di istanza da chiamare, usare una classe helper per richiamare i metodi di istanza (come <xref:System.Action> s) di ogni componente.</span><span class="sxs-lookup"><span data-stu-id="b5c5a-161">When there are several components, each with instance methods to call, use a helper class to invoke the instance methods (as <xref:System.Action>s) of each component.</span></span>

<span data-ttu-id="b5c5a-162">Nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="b5c5a-162">In the following example:</span></span>

* <span data-ttu-id="b5c5a-163">Il `JSInteropExample` componente contiene diversi `ListItem` componenti.</span><span class="sxs-lookup"><span data-stu-id="b5c5a-163">The `JSInteropExample` component contains several `ListItem` components.</span></span>
* <span data-ttu-id="b5c5a-164">Ogni `ListItem` componente è costituito da un messaggio e da un pulsante.</span><span class="sxs-lookup"><span data-stu-id="b5c5a-164">Each `ListItem` component is composed of a message and a button.</span></span>
* <span data-ttu-id="b5c5a-165">Quando `ListItem` viene selezionato un pulsante componente, `ListItem` `UpdateMessage` il metodo modifica il testo dell'elemento dell'elenco e lo nasconde.</span><span class="sxs-lookup"><span data-stu-id="b5c5a-165">When a `ListItem` component button is selected, that `ListItem`'s `UpdateMessage` method changes the list item text and hides the button.</span></span>

<span data-ttu-id="b5c5a-166">*MessageUpdateInvokeHelper.cs*:</span><span class="sxs-lookup"><span data-stu-id="b5c5a-166">*MessageUpdateInvokeHelper.cs*:</span></span>

```csharp
using System;
using Microsoft.JSInterop;

public class MessageUpdateInvokeHelper
{
    private Action action;

    public MessageUpdateInvokeHelper(Action action)
    {
        action = action;
    }

    [JSInvokable("BlazorSample")]
    public void UpdateMessageCaller()
    {
        action.Invoke();
    }
}
```

<span data-ttu-id="b5c5a-167">Nel codice JavaScript lato client:</span><span class="sxs-lookup"><span data-stu-id="b5c5a-167">In the client-side JavaScript:</span></span>

```javascript
window.updateMessageCallerJS = (dotnetHelper) => {
    dotnetHelper.invokeMethod('BlazorSample', 'UpdateMessageCaller');
    dotnetHelper.dispose();
}
```

<span data-ttu-id="b5c5a-168">*Shared/ListItem. Razor*:</span><span class="sxs-lookup"><span data-stu-id="b5c5a-168">*Shared/ListItem.razor*:</span></span>

```razor
@inject IJSRuntime JsRuntime

<li>
    @message
    <button @onclick="InteropCall" style="display:@display">InteropCall</button>
</li>

@code {
    private string message = "Select one of these list item buttons.";
    private string display = "inline-block";
    private MessageUpdateInvokeHelper messageUpdateInvokeHelper;

    protected override void OnInitialized()
    {
        messageUpdateInvokeHelper = new MessageUpdateInvokeHelper(UpdateMessage);
    }

    protected async Task InteropCall()
    {
        await JsRuntime.InvokeVoidAsync("updateMessageCallerJS",
            DotNetObjectReference.Create(messageUpdateInvokeHelper));
    }

    private void UpdateMessage()
    {
        message = "UpdateMessage Called!";
        display = "none";
        StateHasChanged();
    }
}
```

<span data-ttu-id="b5c5a-169">*Pages/JSInteropExample. Razor*:</span><span class="sxs-lookup"><span data-stu-id="b5c5a-169">*Pages/JSInteropExample.razor*:</span></span>

```razor
@page "/JSInteropExample"

<h1>List of components</h1>

<ul>
    <ListItem />
    <ListItem />
    <ListItem />
    <ListItem />
</ul>
```

[!INCLUDE[Share interop code in a class library](~/includes/blazor-share-interop-code.md)]

## <a name="avoid-circular-object-references"></a><span data-ttu-id="b5c5a-170">Evitare riferimenti a oggetti circolari</span><span class="sxs-lookup"><span data-stu-id="b5c5a-170">Avoid circular object references</span></span>

<span data-ttu-id="b5c5a-171">Gli oggetti che contengono riferimenti circolari non possono essere serializzati sul client per uno dei seguenti:</span><span class="sxs-lookup"><span data-stu-id="b5c5a-171">Objects that contain circular references can't be serialized on the client for either:</span></span>

* <span data-ttu-id="b5c5a-172">Chiamate al metodo .NET.</span><span class="sxs-lookup"><span data-stu-id="b5c5a-172">.NET method calls.</span></span>
* <span data-ttu-id="b5c5a-173">Chiamate al metodo JavaScript da C# quando il tipo restituito contiene riferimenti circolari.</span><span class="sxs-lookup"><span data-stu-id="b5c5a-173">JavaScript method calls from C# when the return type has circular references.</span></span>

<span data-ttu-id="b5c5a-174">Per ulteriori informazioni, vedere i seguenti problemi:</span><span class="sxs-lookup"><span data-stu-id="b5c5a-174">For more information, see the following issues:</span></span>

* [<span data-ttu-id="b5c5a-175">I riferimenti circolari non sono supportati, accettano due (DotNet/aspnetcore #20525)</span><span class="sxs-lookup"><span data-stu-id="b5c5a-175">Circular references are not supported, take two (dotnet/aspnetcore #20525)</span></span>](https://github.com/dotnet/aspnetcore/issues/20525)
* [<span data-ttu-id="b5c5a-176">Proposta: aggiungere un meccanismo per gestire i riferimenti circolari durante la serializzazione (DotNet/Runtime #30820)</span><span class="sxs-lookup"><span data-stu-id="b5c5a-176">Proposal: Add mechanism to handle circular references when serializing (dotnet/runtime #30820)</span></span>](https://github.com/dotnet/runtime/issues/30820)

## <a name="additional-resources"></a><span data-ttu-id="b5c5a-177">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="b5c5a-177">Additional resources</span></span>

* <xref:blazor/call-javascript-from-dotnet>
* [<span data-ttu-id="b5c5a-178">Esempio di InteropComponent. Razor (repository GitHub DotNet/AspNetCore, Branch versione 3,1)</span><span class="sxs-lookup"><span data-stu-id="b5c5a-178">InteropComponent.razor example (dotnet/AspNetCore GitHub repository, 3.1 release branch)</span></span>](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
* <span data-ttu-id="b5c5a-179">[Eseguire trasferimenti di dati di grandi dimensioni nelle Blazor app Server](xref:blazor/advanced-scenarios#perform-large-data-transfers-in-blazor-server-apps)</span><span class="sxs-lookup"><span data-stu-id="b5c5a-179">[Perform large data transfers in Blazor Server apps](xref:blazor/advanced-scenarios#perform-large-data-transfers-in-blazor-server-apps)</span></span>
