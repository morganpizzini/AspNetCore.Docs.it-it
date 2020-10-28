---
title: 'Chiamare i metodi .NET da funzioni JavaScript in ASP.NET Core :::no-loc(Blazor):::'
author: guardrex
description: 'Informazioni su come richiamare i metodi .NET dalle funzioni JavaScript nelle :::no-loc(Blazor)::: app.'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc, devx-track-js
ms.date: 08/12/2020
no-loc:
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: blazor/call-dotnet-from-javascript
ms.openlocfilehash: 4a7c06ed985c290eb4b3ffca6d5ed74c6bc4e031
ms.sourcegitcommit: 2e3a967331b2c69f585dd61e9ad5c09763615b44
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92690292"
---
# <a name="call-net-methods-from-javascript-functions-in-aspnet-core-no-locblazor"></a><span data-ttu-id="2aaff-103">Chiamare i metodi .NET da funzioni JavaScript in ASP.NET Core :::no-loc(Blazor):::</span><span class="sxs-lookup"><span data-stu-id="2aaff-103">Call .NET methods from JavaScript functions in ASP.NET Core :::no-loc(Blazor):::</span></span>

<span data-ttu-id="2aaff-104">Di [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), [Rudrawadi](http://wisne.co)e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="2aaff-104">By [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), [Shashikant Rudrawadi](http://wisne.co), and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="2aaff-105">Un' :::no-loc(Blazor)::: app può richiamare funzioni JavaScript da metodi .NET e metodi .NET da funzioni JavaScript.</span><span class="sxs-lookup"><span data-stu-id="2aaff-105">A :::no-loc(Blazor)::: app can invoke JavaScript functions from .NET methods and .NET methods from JavaScript functions.</span></span> <span data-ttu-id="2aaff-106">Questi scenari sono detti *interoperabilità JavaScript (interoperabilità* *JS* ).</span><span class="sxs-lookup"><span data-stu-id="2aaff-106">These scenarios are called *JavaScript interoperability* ( *JS interop* ).</span></span>

<span data-ttu-id="2aaff-107">Questo articolo descrive come richiamare i metodi .NET da JavaScript.</span><span class="sxs-lookup"><span data-stu-id="2aaff-107">This article covers invoking .NET methods from JavaScript.</span></span> <span data-ttu-id="2aaff-108">Per informazioni su come chiamare funzioni JavaScript da .NET, vedere <xref:blazor/call-javascript-from-dotnet> .</span><span class="sxs-lookup"><span data-stu-id="2aaff-108">For information on how to call JavaScript functions from .NET, see <xref:blazor/call-javascript-from-dotnet>.</span></span>

<span data-ttu-id="2aaff-109">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="2aaff-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="static-net-method-call"></a><span data-ttu-id="2aaff-110">Chiamata al metodo .NET statico</span><span class="sxs-lookup"><span data-stu-id="2aaff-110">Static .NET method call</span></span>

<span data-ttu-id="2aaff-111">Per richiamare un metodo .NET statico da JavaScript, usare le `DotNet.invokeMethod` `DotNet.invokeMethodAsync` funzioni o.</span><span class="sxs-lookup"><span data-stu-id="2aaff-111">To invoke a static .NET method from JavaScript, use the `DotNet.invokeMethod` or `DotNet.invokeMethodAsync` functions.</span></span> <span data-ttu-id="2aaff-112">Passare l'identificatore del metodo statico che si desidera chiamare, il nome dell'assembly che contiene la funzione e gli eventuali argomenti.</span><span class="sxs-lookup"><span data-stu-id="2aaff-112">Pass in the identifier of the static method you wish to call, the name of the assembly containing the function, and any arguments.</span></span> <span data-ttu-id="2aaff-113">Per supportare gli scenari è preferibile la versione asincrona :::no-loc(Blazor Server)::: .</span><span class="sxs-lookup"><span data-stu-id="2aaff-113">The asynchronous version is preferred to support :::no-loc(Blazor Server)::: scenarios.</span></span> <span data-ttu-id="2aaff-114">Il metodo .NET deve essere pubblico, statico e avere l' [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute) attributo.</span><span class="sxs-lookup"><span data-stu-id="2aaff-114">The .NET method must be public, static, and have the [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute) attribute.</span></span> <span data-ttu-id="2aaff-115">La chiamata ai metodi generici aperti non è attualmente supportata.</span><span class="sxs-lookup"><span data-stu-id="2aaff-115">Calling open generic methods isn't currently supported.</span></span>

<span data-ttu-id="2aaff-116">L'app di esempio include un metodo C# per restituire una `int` matrice.</span><span class="sxs-lookup"><span data-stu-id="2aaff-116">The sample app includes a C# method to return an `int` array.</span></span> <span data-ttu-id="2aaff-117">L' [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute) attributo viene applicato al metodo.</span><span class="sxs-lookup"><span data-stu-id="2aaff-117">The [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute) attribute is applied to the method.</span></span>

<span data-ttu-id="2aaff-118">`Pages/JsInterop.razor`:</span><span class="sxs-lookup"><span data-stu-id="2aaff-118">`Pages/JsInterop.razor`:</span></span>

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

<span data-ttu-id="2aaff-119">JavaScript servito al client richiama il metodo C# .NET.</span><span class="sxs-lookup"><span data-stu-id="2aaff-119">JavaScript served to the client invokes the C# .NET method.</span></span>

<span data-ttu-id="2aaff-120">`wwwroot/exampleJsInterop.js`:</span><span class="sxs-lookup"><span data-stu-id="2aaff-120">`wwwroot/exampleJsInterop.js`:</span></span>

[!code-javascript[](./common/samples/3.x/:::no-loc(Blazor):::WebAssemblySample/wwwroot/exampleJsInterop.js?highlight=8-14)]

<span data-ttu-id="2aaff-121">Quando **`Trigger .NET static method ReturnArrayAsync`** si seleziona il pulsante, esaminare l'output della console negli strumenti di sviluppo Web del browser.</span><span class="sxs-lookup"><span data-stu-id="2aaff-121">When the **`Trigger .NET static method ReturnArrayAsync`** button is selected, examine the console output in the browser's web developer tools.</span></span>

<span data-ttu-id="2aaff-122">L'output della console è:</span><span class="sxs-lookup"><span data-stu-id="2aaff-122">The console output is:</span></span>

```console
Array(4) [ 1, 2, 3, 4 ]
```

<span data-ttu-id="2aaff-123">Il quarto valore della matrice viene inserito nella matrice ( `data.push(4);` ) restituito da `ReturnArrayAsync` .</span><span class="sxs-lookup"><span data-stu-id="2aaff-123">The fourth array value is pushed to the array (`data.push(4);`) returned by `ReturnArrayAsync`.</span></span>

<span data-ttu-id="2aaff-124">Per impostazione predefinita, l'identificatore del metodo è il nome del metodo, ma è possibile specificare un identificatore diverso usando il [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute) costruttore dell'attributo:</span><span class="sxs-lookup"><span data-stu-id="2aaff-124">By default, the method identifier is the method name, but you can specify a different identifier using the [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute) attribute constructor:</span></span>

```csharp
@code {
    [JSInvokable("DifferentMethodName")]
    public static Task<int[]> ReturnArrayAsync()
    {
        return Task.FromResult(new int[] { 1, 2, 3 });
    }
}
```

<span data-ttu-id="2aaff-125">Nel file JavaScript sul lato client:</span><span class="sxs-lookup"><span data-stu-id="2aaff-125">In the client-side JavaScript file:</span></span>

```javascript
returnArrayAsyncJs: function () {
  DotNet.invokeMethodAsync('{APP ASSEMBLY}', 'DifferentMethodName')
    .then(data => {
      data.push(4);
      console.log(data);
    });
}
```

<span data-ttu-id="2aaff-126">Il segnaposto `{APP ASSEMBLY}` è il nome dell'assembly dell'app (ad esempio, `:::no-loc(Blazor):::Sample` ).</span><span class="sxs-lookup"><span data-stu-id="2aaff-126">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `:::no-loc(Blazor):::Sample`).</span></span>

## <a name="instance-method-call"></a><span data-ttu-id="2aaff-127">Chiamata al metodo di istanza</span><span class="sxs-lookup"><span data-stu-id="2aaff-127">Instance method call</span></span>

<span data-ttu-id="2aaff-128">È anche possibile chiamare i metodi di istanza .NET da JavaScript.</span><span class="sxs-lookup"><span data-stu-id="2aaff-128">You can also call .NET instance methods from JavaScript.</span></span> <span data-ttu-id="2aaff-129">Per richiamare un metodo di istanza .NET da JavaScript:</span><span class="sxs-lookup"><span data-stu-id="2aaff-129">To invoke a .NET instance method from JavaScript:</span></span>

* <span data-ttu-id="2aaff-130">Passa l'istanza .NET per riferimento a JavaScript:</span><span class="sxs-lookup"><span data-stu-id="2aaff-130">Pass the .NET instance by reference to JavaScript:</span></span>
  * <span data-ttu-id="2aaff-131">Eseguire una chiamata statica a <xref:Microsoft.JSInterop.DotNetObjectReference.Create%2A?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="2aaff-131">Make a static call to <xref:Microsoft.JSInterop.DotNetObjectReference.Create%2A?displayProperty=nameWithType>.</span></span>
  * <span data-ttu-id="2aaff-132">Eseguire il wrapping dell'istanza in un' <xref:Microsoft.JSInterop.DotNetObjectReference> istanza di e chiamare <xref:Microsoft.JSInterop.DotNetObjectReference.Create%2A> sull' <xref:Microsoft.JSInterop.DotNetObjectReference> istanza.</span><span class="sxs-lookup"><span data-stu-id="2aaff-132">Wrap the instance in a <xref:Microsoft.JSInterop.DotNetObjectReference> instance and call <xref:Microsoft.JSInterop.DotNetObjectReference.Create%2A> on the <xref:Microsoft.JSInterop.DotNetObjectReference> instance.</span></span> <span data-ttu-id="2aaff-133">Eliminazione di <xref:Microsoft.JSInterop.DotNetObjectReference> oggetti (un esempio viene visualizzato più avanti in questa sezione).</span><span class="sxs-lookup"><span data-stu-id="2aaff-133">Dispose of <xref:Microsoft.JSInterop.DotNetObjectReference> objects (an example appears later in this section).</span></span>
* <span data-ttu-id="2aaff-134">Richiamare i metodi di istanza .NET sull'istanza usando `invokeMethod` le `invokeMethodAsync` funzioni o.</span><span class="sxs-lookup"><span data-stu-id="2aaff-134">Invoke .NET instance methods on the instance using the `invokeMethod` or `invokeMethodAsync` functions.</span></span> <span data-ttu-id="2aaff-135">L'istanza di .NET può essere passata anche come argomento quando si richiamano altri metodi .NET da JavaScript.</span><span class="sxs-lookup"><span data-stu-id="2aaff-135">The .NET instance can also be passed as an argument when invoking other .NET methods from JavaScript.</span></span>

> [!NOTE]
> <span data-ttu-id="2aaff-136">L'app di esempio consente di registrare i messaggi nella console lato client.</span><span class="sxs-lookup"><span data-stu-id="2aaff-136">The sample app logs messages to the client-side console.</span></span> <span data-ttu-id="2aaff-137">Per gli esempi seguenti illustrati dall'app di esempio, esaminare l'output della console del browser negli strumenti di sviluppo del browser.</span><span class="sxs-lookup"><span data-stu-id="2aaff-137">For the following examples demonstrated by the sample app, examine the browser's console output in the browser's developer tools.</span></span>

<span data-ttu-id="2aaff-138">Quando il **`Trigger .NET instance method HelloHelper.SayHello`** pulsante è selezionato, `ExampleJsInterop.CallHelloHelperSayHello` viene chiamato e passa un nome `:::no-loc(Blazor):::` al metodo.</span><span class="sxs-lookup"><span data-stu-id="2aaff-138">When the **`Trigger .NET instance method HelloHelper.SayHello`** button is selected, `ExampleJsInterop.CallHelloHelperSayHello` is called and passes a name, `:::no-loc(Blazor):::`, to the method.</span></span>

<span data-ttu-id="2aaff-139">`Pages/JsInterop.razor`:</span><span class="sxs-lookup"><span data-stu-id="2aaff-139">`Pages/JsInterop.razor`:</span></span>

```razor
<button type="button" class="btn btn-primary" @onclick="TriggerNetInstanceMethod">
    Trigger .NET instance method HelloHelper.SayHello
</button>

@code {
    public async Task TriggerNetInstanceMethod()
    {
        var exampleJsInterop = new ExampleJsInterop(JSRuntime);
        await exampleJsInterop.CallHelloHelperSayHello(":::no-loc(Blazor):::");
    }
}
```

<span data-ttu-id="2aaff-140">`CallHelloHelperSayHello` richiama la funzione JavaScript `sayHello` con una nuova istanza di `HelloHelper` .</span><span class="sxs-lookup"><span data-stu-id="2aaff-140">`CallHelloHelperSayHello` invokes the JavaScript function `sayHello` with a new instance of `HelloHelper`.</span></span>

<span data-ttu-id="2aaff-141">`JsInteropClasses/ExampleJsInterop.cs`:</span><span class="sxs-lookup"><span data-stu-id="2aaff-141">`JsInteropClasses/ExampleJsInterop.cs`:</span></span>

[!code-csharp[](./common/samples/3.x/:::no-loc(Blazor):::WebAssemblySample/JsInteropClasses/ExampleJsInterop.cs?name=snippet1&highlight=11-18)]

<span data-ttu-id="2aaff-142">`wwwroot/exampleJsInterop.js`:</span><span class="sxs-lookup"><span data-stu-id="2aaff-142">`wwwroot/exampleJsInterop.js`:</span></span>

[!code-javascript[](./common/samples/3.x/:::no-loc(Blazor):::WebAssemblySample/wwwroot/exampleJsInterop.js?highlight=15-18)]

<span data-ttu-id="2aaff-143">Il nome viene passato al `HelloHelper` costruttore, che imposta la `HelloHelper.Name` Proprietà.</span><span class="sxs-lookup"><span data-stu-id="2aaff-143">The name is passed to `HelloHelper`'s constructor, which sets the `HelloHelper.Name` property.</span></span> <span data-ttu-id="2aaff-144">Quando viene eseguita la funzione JavaScript `sayHello` , `HelloHelper.SayHello` restituisce il `Hello, {Name}!` messaggio, che viene scritto nella console dalla funzione JavaScript.</span><span class="sxs-lookup"><span data-stu-id="2aaff-144">When the JavaScript function `sayHello` is executed, `HelloHelper.SayHello` returns the `Hello, {Name}!` message, which is written to the console by the JavaScript function.</span></span>

<span data-ttu-id="2aaff-145">`JsInteropClasses/HelloHelper.cs`:</span><span class="sxs-lookup"><span data-stu-id="2aaff-145">`JsInteropClasses/HelloHelper.cs`:</span></span>

[!code-csharp[](./common/samples/3.x/:::no-loc(Blazor):::WebAssemblySample/JsInteropClasses/HelloHelper.cs?name=snippet1&highlight=5,10-11)]

<span data-ttu-id="2aaff-146">Output della console negli strumenti di sviluppo Web del browser:</span><span class="sxs-lookup"><span data-stu-id="2aaff-146">Console output in the browser's web developer tools:</span></span>

```console
Hello, :::no-loc(Blazor):::!
```

<span data-ttu-id="2aaff-147">Per evitare una perdita di memoria e consentire Garbage Collection su un componente che crea un <xref:Microsoft.JSInterop.DotNetObjectReference> , adottare uno degli approcci seguenti:</span><span class="sxs-lookup"><span data-stu-id="2aaff-147">To avoid a memory leak and allow garbage collection on a component that creates a <xref:Microsoft.JSInterop.DotNetObjectReference>, adopt one of the following approaches:</span></span>

* <span data-ttu-id="2aaff-148">Eliminare l'oggetto nella classe che ha creato l' <xref:Microsoft.JSInterop.DotNetObjectReference> istanza:</span><span class="sxs-lookup"><span data-stu-id="2aaff-148">Dispose of the object in the class that created the <xref:Microsoft.JSInterop.DotNetObjectReference> instance:</span></span>

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

  <span data-ttu-id="2aaff-149">Il modello precedente illustrato nella `ExampleJsInterop` classe può anche essere implementato in un componente:</span><span class="sxs-lookup"><span data-stu-id="2aaff-149">The preceding pattern shown in the `ExampleJsInterop` class can also be implemented in a component:</span></span>

  ```razor
  @page "/JSInteropComponent"
  @using {APP ASSEMBLY}.JsInteropClasses
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
          objRef = DotNetObjectReference.Create(new HelloHelper(":::no-loc(Blazor):::"));

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
  
  <span data-ttu-id="2aaff-150">Il segnaposto `{APP ASSEMBLY}` è il nome dell'assembly dell'app (ad esempio, `:::no-loc(Blazor):::Sample` ).</span><span class="sxs-lookup"><span data-stu-id="2aaff-150">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `:::no-loc(Blazor):::Sample`).</span></span>

* <span data-ttu-id="2aaff-151">Quando il componente o la classe non Elimina <xref:Microsoft.JSInterop.DotNetObjectReference> , eliminare l'oggetto nel client chiamando `.dispose()` :</span><span class="sxs-lookup"><span data-stu-id="2aaff-151">When the component or class doesn't dispose of the <xref:Microsoft.JSInterop.DotNetObjectReference>, dispose of the object on the client by calling `.dispose()`:</span></span>

  ```javascript
  window.myFunction = (dotnetHelper) => {
    dotnetHelper.invokeMethodAsync('{APP ASSEMBLY}', 'MyMethod');
    dotnetHelper.dispose();
  }
  ```

## <a name="component-instance-method-call"></a><span data-ttu-id="2aaff-152">Chiamata al metodo dell'istanza del componente</span><span class="sxs-lookup"><span data-stu-id="2aaff-152">Component instance method call</span></span>

<span data-ttu-id="2aaff-153">Per richiamare i metodi .NET di un componente:</span><span class="sxs-lookup"><span data-stu-id="2aaff-153">To invoke a component's .NET methods:</span></span>

* <span data-ttu-id="2aaff-154">Utilizzare la `invokeMethod` `invokeMethodAsync` funzione o per eseguire una chiamata al metodo statico al componente.</span><span class="sxs-lookup"><span data-stu-id="2aaff-154">Use the `invokeMethod` or `invokeMethodAsync` function to make a static method call to the component.</span></span>
* <span data-ttu-id="2aaff-155">Il metodo statico del componente esegue il wrapping della chiamata al metodo di istanza come oggetto richiamato <xref:System.Action> .</span><span class="sxs-lookup"><span data-stu-id="2aaff-155">The component's static method wraps the call to its instance method as an invoked <xref:System.Action>.</span></span>

> [!NOTE]
> <span data-ttu-id="2aaff-156">Per :::no-loc(Blazor Server)::: le app, in cui più utenti possono usare contemporaneamente lo stesso componente, usare una classe helper per richiamare i metodi di istanza.</span><span class="sxs-lookup"><span data-stu-id="2aaff-156">For :::no-loc(Blazor Server)::: apps, where several users might be concurrently using the same component, use a helper class to invoke instance methods.</span></span>
>
> <span data-ttu-id="2aaff-157">Per ulteriori informazioni, vedere la sezione relativa alla [classe helper del metodo di istanza Component](#component-instance-method-helper-class) .</span><span class="sxs-lookup"><span data-stu-id="2aaff-157">For more information, see the [Component instance method helper class](#component-instance-method-helper-class) section.</span></span>

<span data-ttu-id="2aaff-158">Nel codice JavaScript lato client:</span><span class="sxs-lookup"><span data-stu-id="2aaff-158">In the client-side JavaScript:</span></span>

```javascript
function updateMessageCallerJS() {
  DotNet.invokeMethodAsync('{APP ASSEMBLY}', 'UpdateMessageCaller');
}
```

<span data-ttu-id="2aaff-159">Il segnaposto `{APP ASSEMBLY}` è il nome dell'assembly dell'app (ad esempio, `:::no-loc(Blazor):::Sample` ).</span><span class="sxs-lookup"><span data-stu-id="2aaff-159">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `:::no-loc(Blazor):::Sample`).</span></span>

<span data-ttu-id="2aaff-160">`Pages/JSInteropComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="2aaff-160">`Pages/JSInteropComponent.razor`:</span></span>

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

<span data-ttu-id="2aaff-161">Per passare argomenti al metodo di istanza:</span><span class="sxs-lookup"><span data-stu-id="2aaff-161">To pass arguments to the instance method:</span></span>

* <span data-ttu-id="2aaff-162">Aggiungere parametri alla chiamata del metodo JS.</span><span class="sxs-lookup"><span data-stu-id="2aaff-162">Add parameters to the JS method invocation.</span></span> <span data-ttu-id="2aaff-163">Nell'esempio seguente un nome viene passato al metodo.</span><span class="sxs-lookup"><span data-stu-id="2aaff-163">In the following example, a name is passed to the method.</span></span> <span data-ttu-id="2aaff-164">È possibile aggiungere altri parametri all'elenco in base alle esigenze.</span><span class="sxs-lookup"><span data-stu-id="2aaff-164">Additional parameters can be added to the list as needed.</span></span>

  ```javascript
  function updateMessageCallerJS(name) {
    DotNet.invokeMethodAsync('{APP ASSEMBLY}', 'UpdateMessageCaller', name);
  }
  ```
  
  <span data-ttu-id="2aaff-165">Il segnaposto `{APP ASSEMBLY}` è il nome dell'assembly dell'app (ad esempio, `:::no-loc(Blazor):::Sample` ).</span><span class="sxs-lookup"><span data-stu-id="2aaff-165">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `:::no-loc(Blazor):::Sample`).</span></span>

* <span data-ttu-id="2aaff-166">Fornire i tipi corretti a <xref:System.Action> per i parametri.</span><span class="sxs-lookup"><span data-stu-id="2aaff-166">Provide the correct types to the <xref:System.Action> for the parameters.</span></span> <span data-ttu-id="2aaff-167">Fornire l'elenco di parametri ai metodi di C#.</span><span class="sxs-lookup"><span data-stu-id="2aaff-167">Provide the parameter list to the C# methods.</span></span> <span data-ttu-id="2aaff-168">Richiamare <xref:System.Action> ( `UpdateMessage` ) con i parametri ( `action.Invoke(name)` ).</span><span class="sxs-lookup"><span data-stu-id="2aaff-168">Invoke the <xref:System.Action> (`UpdateMessage`) with the parameters (`action.Invoke(name)`).</span></span>

  <span data-ttu-id="2aaff-169">`Pages/JSInteropComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="2aaff-169">`Pages/JSInteropComponent.razor`:</span></span>

  ```razor
  @page "/JSInteropComponent"

  <p>
      Message: @message
  </p>

  <p>
      <button onclick="updateMessageCallerJS('Sarah Jane')">
          Call JS Method
      </button>
  </p>

  @code {
      private static Action<string> action;
      private string message = "Select the button.";

      protected override void OnInitialized()
      {
          action = UpdateMessage;
      }

      private void UpdateMessage(string name)
      {
          message = $"{name}, UpdateMessage Called!";
          StateHasChanged();
      }

      [JSInvokable]
      public static void UpdateMessageCaller(string name)
      {
          action.Invoke(name);
      }
  }
  ```

  <span data-ttu-id="2aaff-170">Output `message` quando viene selezionato il pulsante **chiama metodo JS** :</span><span class="sxs-lookup"><span data-stu-id="2aaff-170">Output `message` when the **Call JS Method** button is selected:</span></span>

  ```
  Sarah Jane, UpdateMessage Called!
  ```

## <a name="component-instance-method-helper-class"></a><span data-ttu-id="2aaff-171">Classe helper del metodo di istanza Component</span><span class="sxs-lookup"><span data-stu-id="2aaff-171">Component instance method helper class</span></span>

<span data-ttu-id="2aaff-172">La classe helper viene utilizzata per richiamare un metodo di istanza come <xref:System.Action> .</span><span class="sxs-lookup"><span data-stu-id="2aaff-172">The helper class is used to invoke an instance method as an <xref:System.Action>.</span></span> <span data-ttu-id="2aaff-173">Le classi helper sono utili nei casi seguenti:</span><span class="sxs-lookup"><span data-stu-id="2aaff-173">Helper classes are useful when:</span></span>

* <span data-ttu-id="2aaff-174">Viene eseguito il rendering di diversi componenti dello stesso tipo nella stessa pagina.</span><span class="sxs-lookup"><span data-stu-id="2aaff-174">Several components of the same type are rendered on the same page.</span></span>
* <span data-ttu-id="2aaff-175">:::no-loc(Blazor Server):::Viene usata un'app, in cui più utenti potrebbero usare un componente simultaneamente.</span><span class="sxs-lookup"><span data-stu-id="2aaff-175">A :::no-loc(Blazor Server)::: app is used, where multiple users might be using a component concurrently.</span></span>

<span data-ttu-id="2aaff-176">Nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="2aaff-176">In the following example:</span></span>

* <span data-ttu-id="2aaff-177">Il `JSInteropExample` componente contiene diversi `ListItem` componenti.</span><span class="sxs-lookup"><span data-stu-id="2aaff-177">The `JSInteropExample` component contains several `ListItem` components.</span></span>
* <span data-ttu-id="2aaff-178">Ogni `ListItem` componente è costituito da un messaggio e da un pulsante.</span><span class="sxs-lookup"><span data-stu-id="2aaff-178">Each `ListItem` component is composed of a message and a button.</span></span>
* <span data-ttu-id="2aaff-179">Quando `ListItem` viene selezionato un pulsante componente, `ListItem` `UpdateMessage` il metodo modifica il testo dell'elemento dell'elenco e lo nasconde.</span><span class="sxs-lookup"><span data-stu-id="2aaff-179">When a `ListItem` component button is selected, that `ListItem`'s `UpdateMessage` method changes the list item text and hides the button.</span></span>

<span data-ttu-id="2aaff-180">`MessageUpdateInvokeHelper.cs`:</span><span class="sxs-lookup"><span data-stu-id="2aaff-180">`MessageUpdateInvokeHelper.cs`:</span></span>

```csharp
using System;
using Microsoft.JSInterop;

public class MessageUpdateInvokeHelper
{
    private Action action;

    public MessageUpdateInvokeHelper(Action action)
    {
        this.action = action;
    }

    [JSInvokable("{APP ASSEMBLY}")]
    public void UpdateMessageCaller()
    {
        action.Invoke();
    }
}
```

<span data-ttu-id="2aaff-181">Il segnaposto `{APP ASSEMBLY}` è il nome dell'assembly dell'app (ad esempio, `:::no-loc(Blazor):::Sample` ).</span><span class="sxs-lookup"><span data-stu-id="2aaff-181">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `:::no-loc(Blazor):::Sample`).</span></span>

<span data-ttu-id="2aaff-182">Nel codice JavaScript lato client:</span><span class="sxs-lookup"><span data-stu-id="2aaff-182">In the client-side JavaScript:</span></span>

```javascript
window.updateMessageCallerJS = (dotnetHelper) => {
    dotnetHelper.invokeMethodAsync('{APP ASSEMBLY}', 'UpdateMessageCaller');
    dotnetHelper.dispose();
}
```

<span data-ttu-id="2aaff-183">Il segnaposto `{APP ASSEMBLY}` è il nome dell'assembly dell'app (ad esempio, `:::no-loc(Blazor):::Sample` ).</span><span class="sxs-lookup"><span data-stu-id="2aaff-183">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `:::no-loc(Blazor):::Sample`).</span></span>

<span data-ttu-id="2aaff-184">`Shared/ListItem.razor`:</span><span class="sxs-lookup"><span data-stu-id="2aaff-184">`Shared/ListItem.razor`:</span></span>

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

<span data-ttu-id="2aaff-185">`Pages/JSInteropExample.razor`:</span><span class="sxs-lookup"><span data-stu-id="2aaff-185">`Pages/JSInteropExample.razor`:</span></span>

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

## <a name="avoid-circular-object-references"></a><span data-ttu-id="2aaff-186">Evitare riferimenti a oggetti circolari</span><span class="sxs-lookup"><span data-stu-id="2aaff-186">Avoid circular object references</span></span>

<span data-ttu-id="2aaff-187">Gli oggetti che contengono riferimenti circolari non possono essere serializzati sul client per uno dei seguenti:</span><span class="sxs-lookup"><span data-stu-id="2aaff-187">Objects that contain circular references can't be serialized on the client for either:</span></span>

* <span data-ttu-id="2aaff-188">Chiamate al metodo .NET.</span><span class="sxs-lookup"><span data-stu-id="2aaff-188">.NET method calls.</span></span>
* <span data-ttu-id="2aaff-189">Chiamate al metodo JavaScript da C# quando il tipo restituito contiene riferimenti circolari.</span><span class="sxs-lookup"><span data-stu-id="2aaff-189">JavaScript method calls from C# when the return type has circular references.</span></span>

<span data-ttu-id="2aaff-190">Per ulteriori informazioni, vedere i seguenti problemi:</span><span class="sxs-lookup"><span data-stu-id="2aaff-190">For more information, see the following issues:</span></span>

* [<span data-ttu-id="2aaff-191">I riferimenti circolari non sono supportati, accettano due (DotNet/aspnetcore #20525)</span><span class="sxs-lookup"><span data-stu-id="2aaff-191">Circular references are not supported, take two (dotnet/aspnetcore #20525)</span></span>](https://github.com/dotnet/aspnetcore/issues/20525)
* [<span data-ttu-id="2aaff-192">Proposta: aggiungere un meccanismo per gestire i riferimenti circolari durante la serializzazione (DotNet/Runtime #30820)</span><span class="sxs-lookup"><span data-stu-id="2aaff-192">Proposal: Add mechanism to handle circular references when serializing (dotnet/runtime #30820)</span></span>](https://github.com/dotnet/runtime/issues/30820)

## <a name="additional-resources"></a><span data-ttu-id="2aaff-193">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="2aaff-193">Additional resources</span></span>

* <xref:blazor/call-javascript-from-dotnet>
* [<span data-ttu-id="2aaff-194">`InteropComponent.razor` esempio (repository GitHub DotNet/AspNetCore, ramo di versione 3,1)</span><span class="sxs-lookup"><span data-stu-id="2aaff-194">`InteropComponent.razor` example (dotnet/AspNetCore GitHub repository, 3.1 release branch)</span></span>](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
* [<span data-ttu-id="2aaff-195">Eseguire trasferimenti di dati di grandi dimensioni nelle :::no-loc(Blazor Server)::: app</span><span class="sxs-lookup"><span data-stu-id="2aaff-195">Perform large data transfers in :::no-loc(Blazor Server)::: apps</span></span>](xref:blazor/advanced-scenarios#perform-large-data-transfers-in-blazor-server-apps)
