---
title: Chiamare i metodi .NET da funzioni JavaScript in ASP.NET CoreBlazor
author: guardrex
description: Informazioni su come richiamare i metodi .NET dalle funzioni JavaScript Blazor nelle app.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/07/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/call-dotnet-from-javascript
ms.openlocfilehash: 1bc75f0825b114a24def287bb7ccb11c27514f01
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82767187"
---
# <a name="call-net-methods-from-javascript-functions-in-aspnet-core-blazor"></a><span data-ttu-id="0c975-103">Chiamare i metodi .NET da funzioni JavaScript in ASP.NET CoreBlazor</span><span class="sxs-lookup"><span data-stu-id="0c975-103">Call .NET methods from JavaScript functions in ASP.NET Core Blazor</span></span>

<span data-ttu-id="0c975-104">Di [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), [Rudrawadi](http://wisne.co)e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="0c975-104">By [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), [Shashikant Rudrawadi](http://wisne.co), and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="0c975-105">Un' Blazor app può richiamare funzioni JavaScript da metodi .NET e metodi .NET da funzioni JavaScript.</span><span class="sxs-lookup"><span data-stu-id="0c975-105">A Blazor app can invoke JavaScript functions from .NET methods and .NET methods from JavaScript functions.</span></span> <span data-ttu-id="0c975-106">Questi scenari sono detti *interoperabilità JavaScript (interoperabilità* *JS*).</span><span class="sxs-lookup"><span data-stu-id="0c975-106">These scenarios are called *JavaScript interoperability* (*JS interop*).</span></span>

<span data-ttu-id="0c975-107">Questo articolo descrive come richiamare i metodi .NET da JavaScript.</span><span class="sxs-lookup"><span data-stu-id="0c975-107">This article covers invoking .NET methods from JavaScript.</span></span> <span data-ttu-id="0c975-108">Per informazioni su come chiamare funzioni JavaScript da .NET, vedere <xref:blazor/call-javascript-from-dotnet>.</span><span class="sxs-lookup"><span data-stu-id="0c975-108">For information on how to call JavaScript functions from .NET, see <xref:blazor/call-javascript-from-dotnet>.</span></span>

<span data-ttu-id="0c975-109">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="0c975-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="static-net-method-call"></a><span data-ttu-id="0c975-110">Chiamata al metodo .NET statico</span><span class="sxs-lookup"><span data-stu-id="0c975-110">Static .NET method call</span></span>

<span data-ttu-id="0c975-111">Per richiamare un metodo .NET statico da JavaScript, usare le `DotNet.invokeMethod` funzioni `DotNet.invokeMethodAsync` o.</span><span class="sxs-lookup"><span data-stu-id="0c975-111">To invoke a static .NET method from JavaScript, use the `DotNet.invokeMethod` or `DotNet.invokeMethodAsync` functions.</span></span> <span data-ttu-id="0c975-112">Passare l'identificatore del metodo statico che si desidera chiamare, il nome dell'assembly che contiene la funzione e gli eventuali argomenti.</span><span class="sxs-lookup"><span data-stu-id="0c975-112">Pass in the identifier of the static method you wish to call, the name of the assembly containing the function, and any arguments.</span></span> <span data-ttu-id="0c975-113">La versione asincrona è preferibile Blazor per supportare scenari server.</span><span class="sxs-lookup"><span data-stu-id="0c975-113">The asynchronous version is preferred to support Blazor Server scenarios.</span></span> <span data-ttu-id="0c975-114">Il metodo .NET deve essere pubblico, statico e avere l' `[JSInvokable]` attributo.</span><span class="sxs-lookup"><span data-stu-id="0c975-114">The .NET method must be public, static, and have the `[JSInvokable]` attribute.</span></span> <span data-ttu-id="0c975-115">La chiamata ai metodi generici aperti non è attualmente supportata.</span><span class="sxs-lookup"><span data-stu-id="0c975-115">Calling open generic methods isn't currently supported.</span></span>

<span data-ttu-id="0c975-116">L'app di esempio include un metodo C# per restituire `int` una matrice.</span><span class="sxs-lookup"><span data-stu-id="0c975-116">The sample app includes a C# method to return an `int` array.</span></span> <span data-ttu-id="0c975-117">L' `JSInvokable` attributo viene applicato al metodo.</span><span class="sxs-lookup"><span data-stu-id="0c975-117">The `JSInvokable` attribute is applied to the method.</span></span>

<span data-ttu-id="0c975-118">*Pages/JsInterop. Razor*:</span><span class="sxs-lookup"><span data-stu-id="0c975-118">*Pages/JsInterop.razor*:</span></span>

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

<span data-ttu-id="0c975-119">JavaScript servito al client richiama il metodo C# .NET.</span><span class="sxs-lookup"><span data-stu-id="0c975-119">JavaScript served to the client invokes the C# .NET method.</span></span>

<span data-ttu-id="0c975-120">*wwwroot/exampleJsInterop. js*:</span><span class="sxs-lookup"><span data-stu-id="0c975-120">*wwwroot/exampleJsInterop.js*:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=8-14)]

<span data-ttu-id="0c975-121">Quando si seleziona il pulsante **trigger .net static method ReturnArrayAsync** , esaminare l'output della console negli strumenti di sviluppo Web del browser.</span><span class="sxs-lookup"><span data-stu-id="0c975-121">When the **Trigger .NET static method ReturnArrayAsync** button is selected, examine the console output in the browser's web developer tools.</span></span>

<span data-ttu-id="0c975-122">L'output della console è:</span><span class="sxs-lookup"><span data-stu-id="0c975-122">The console output is:</span></span>

```console
Array(4) [ 1, 2, 3, 4 ]
```

<span data-ttu-id="0c975-123">Il quarto valore della matrice viene inserito nella matrice (`data.push(4);`) restituito da `ReturnArrayAsync`.</span><span class="sxs-lookup"><span data-stu-id="0c975-123">The fourth array value is pushed to the array (`data.push(4);`) returned by `ReturnArrayAsync`.</span></span>

<span data-ttu-id="0c975-124">Per impostazione predefinita, l'identificatore del metodo è il nome del metodo, ma è possibile specificare un identificatore `JSInvokableAttribute` diverso usando il costruttore:</span><span class="sxs-lookup"><span data-stu-id="0c975-124">By default, the method identifier is the method name, but you can specify a different identifier using the `JSInvokableAttribute` constructor:</span></span>

```csharp
@code {
    [JSInvokable("DifferentMethodName")]
    public static Task<int[]> ReturnArrayAsync()
    {
        return Task.FromResult(new int[] { 1, 2, 3 });
    }
}
```

<span data-ttu-id="0c975-125">Nel file JavaScript sul lato client:</span><span class="sxs-lookup"><span data-stu-id="0c975-125">In the client-side JavaScript file:</span></span>

```javascript
returnArrayAsyncJs: function () {
  DotNet.invokeMethodAsync('BlazorSample', 'DifferentMethodName')
    .then(data => {
      data.push(4);
      console.log(data);
    });
}
```

## <a name="instance-method-call"></a><span data-ttu-id="0c975-126">Chiamata al metodo di istanza</span><span class="sxs-lookup"><span data-stu-id="0c975-126">Instance method call</span></span>

<span data-ttu-id="0c975-127">È anche possibile chiamare i metodi di istanza .NET da JavaScript.</span><span class="sxs-lookup"><span data-stu-id="0c975-127">You can also call .NET instance methods from JavaScript.</span></span> <span data-ttu-id="0c975-128">Per richiamare un metodo di istanza .NET da JavaScript:</span><span class="sxs-lookup"><span data-stu-id="0c975-128">To invoke a .NET instance method from JavaScript:</span></span>

* <span data-ttu-id="0c975-129">Passa l'istanza .NET per riferimento a JavaScript:</span><span class="sxs-lookup"><span data-stu-id="0c975-129">Pass the .NET instance by reference to JavaScript:</span></span>
  * <span data-ttu-id="0c975-130">Eseguire una chiamata statica a `DotNetObjectReference.Create`.</span><span class="sxs-lookup"><span data-stu-id="0c975-130">Make a static call to `DotNetObjectReference.Create`.</span></span>
  * <span data-ttu-id="0c975-131">Eseguire il wrapping dell'istanza `DotNetObjectReference` in un'istanza `Create` di e `DotNetObjectReference` chiamare sull'istanza.</span><span class="sxs-lookup"><span data-stu-id="0c975-131">Wrap the instance in a `DotNetObjectReference` instance and call `Create` on the `DotNetObjectReference` instance.</span></span> <span data-ttu-id="0c975-132">Eliminazione di `DotNetObjectReference` oggetti (un esempio viene visualizzato più avanti in questa sezione).</span><span class="sxs-lookup"><span data-stu-id="0c975-132">Dispose of `DotNetObjectReference` objects (an example appears later in this section).</span></span>
* <span data-ttu-id="0c975-133">Richiamare i metodi di istanza .NET sull'istanza usando `invokeMethod` le `invokeMethodAsync` funzioni o.</span><span class="sxs-lookup"><span data-stu-id="0c975-133">Invoke .NET instance methods on the instance using the `invokeMethod` or `invokeMethodAsync` functions.</span></span> <span data-ttu-id="0c975-134">L'istanza di .NET può essere passata anche come argomento quando si richiamano altri metodi .NET da JavaScript.</span><span class="sxs-lookup"><span data-stu-id="0c975-134">The .NET instance can also be passed as an argument when invoking other .NET methods from JavaScript.</span></span>

> [!NOTE]
> <span data-ttu-id="0c975-135">L'app di esempio consente di registrare i messaggi nella console lato client.</span><span class="sxs-lookup"><span data-stu-id="0c975-135">The sample app logs messages to the client-side console.</span></span> <span data-ttu-id="0c975-136">Per gli esempi seguenti illustrati dall'app di esempio, esaminare l'output della console del browser negli strumenti di sviluppo del browser.</span><span class="sxs-lookup"><span data-stu-id="0c975-136">For the following examples demonstrated by the sample app, examine the browser's console output in the browser's developer tools.</span></span>

<span data-ttu-id="0c975-137">Quando il pulsante del **metodo di istanza .NET del trigger HelloHelper. sayHello** è selezionato, `ExampleJsInterop.CallHelloHelperSayHello` viene chiamato e passa `Blazor`un nome al metodo.</span><span class="sxs-lookup"><span data-stu-id="0c975-137">When the **Trigger .NET instance method HelloHelper.SayHello** button is selected, `ExampleJsInterop.CallHelloHelperSayHello` is called and passes a name, `Blazor`, to the method.</span></span>

<span data-ttu-id="0c975-138">*Pages/JsInterop. Razor*:</span><span class="sxs-lookup"><span data-stu-id="0c975-138">*Pages/JsInterop.razor*:</span></span>

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

<span data-ttu-id="0c975-139">`CallHelloHelperSayHello`richiama la funzione `sayHello` JavaScript con una nuova istanza di `HelloHelper`.</span><span class="sxs-lookup"><span data-stu-id="0c975-139">`CallHelloHelperSayHello` invokes the JavaScript function `sayHello` with a new instance of `HelloHelper`.</span></span>

<span data-ttu-id="0c975-140">*JsInteropClasses/ExampleJsInterop. cs*:</span><span class="sxs-lookup"><span data-stu-id="0c975-140">*JsInteropClasses/ExampleJsInterop.cs*:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorWebAssemblySample/JsInteropClasses/ExampleJsInterop.cs?name=snippet1&highlight=11-18)]

<span data-ttu-id="0c975-141">*wwwroot/exampleJsInterop. js*:</span><span class="sxs-lookup"><span data-stu-id="0c975-141">*wwwroot/exampleJsInterop.js*:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=15-18)]

<span data-ttu-id="0c975-142">Il nome viene passato al `HelloHelper`costruttore, che imposta la `HelloHelper.Name` proprietà.</span><span class="sxs-lookup"><span data-stu-id="0c975-142">The name is passed to `HelloHelper`'s constructor, which sets the `HelloHelper.Name` property.</span></span> <span data-ttu-id="0c975-143">Quando viene eseguita la `sayHello` funzione JavaScript, `HelloHelper.SayHello` restituisce il `Hello, {Name}!` messaggio, che viene scritto nella console dalla funzione JavaScript.</span><span class="sxs-lookup"><span data-stu-id="0c975-143">When the JavaScript function `sayHello` is executed, `HelloHelper.SayHello` returns the `Hello, {Name}!` message, which is written to the console by the JavaScript function.</span></span>

<span data-ttu-id="0c975-144">*JsInteropClasses/HelloHelper. cs*:</span><span class="sxs-lookup"><span data-stu-id="0c975-144">*JsInteropClasses/HelloHelper.cs*:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorWebAssemblySample/JsInteropClasses/HelloHelper.cs?name=snippet1&highlight=5,10-11)]

<span data-ttu-id="0c975-145">Output della console negli strumenti di sviluppo Web del browser:</span><span class="sxs-lookup"><span data-stu-id="0c975-145">Console output in the browser's web developer tools:</span></span>

```console
Hello, Blazor!
```

<span data-ttu-id="0c975-146">Per evitare una perdita di memoria e consentire Garbage Collection su un componente che crea `DotNetObjectReference`un, adottare uno degli approcci seguenti:</span><span class="sxs-lookup"><span data-stu-id="0c975-146">To avoid a memory leak and allow garbage collection on a component that creates a `DotNetObjectReference`, adopt one of the following approaches:</span></span>

* <span data-ttu-id="0c975-147">Eliminare l'oggetto nella classe che ha creato l' `DotNetObjectReference` istanza:</span><span class="sxs-lookup"><span data-stu-id="0c975-147">Dispose of the object in the class that created the `DotNetObjectReference` instance:</span></span>

  ```csharp
  public class ExampleJsInterop : IDisposable
  {
      private readonly IJSRuntime _jsRuntime;
      private DotNetObjectReference<HelloHelper> _objRef;

      public ExampleJsInterop(IJSRuntime jsRuntime)
      {
          _jsRuntime = jsRuntime;
      }

      public ValueTask<string> CallHelloHelperSayHello(string name)
      {
          _objRef = DotNetObjectReference.Create(new HelloHelper(name));

          return _jsRuntime.InvokeAsync<string>(
              "exampleJsFunctions.sayHello",
              _objRef);
      }

      public void Dispose()
      {
          _objRef?.Dispose();
      }
  }
  ```

  <span data-ttu-id="0c975-148">Il modello precedente illustrato nella `ExampleJsInterop` classe può anche essere implementato in un componente:</span><span class="sxs-lookup"><span data-stu-id="0c975-148">The preceding pattern shown in the `ExampleJsInterop` class can also be implemented in a component:</span></span>

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
      private DotNetObjectReference<HelloHelper> _objRef;

      public async Task TriggerNetInstanceMethod()
      {
          _objRef = DotNetObjectReference.Create(new HelloHelper("Blazor"));

          await JSRuntime.InvokeAsync<string>(
              "exampleJsFunctions.sayHello",
              _objRef);
      }

      public void Dispose()
      {
          _objRef?.Dispose();
      }
  }
  ```

* <span data-ttu-id="0c975-149">Quando il componente o la classe non Elimina `DotNetObjectReference`, eliminare l'oggetto nel client chiamando: `.dispose()`</span><span class="sxs-lookup"><span data-stu-id="0c975-149">When the component or class doesn't dispose of the `DotNetObjectReference`, dispose of the object on the client by calling `.dispose()`:</span></span>

  ```javascript
  window.myFunction = (dotnetHelper) => {
    dotnetHelper.invokeMethod('BlazorSample', 'MyMethod');
    dotnetHelper.dispose();
  }
  ```

## <a name="component-instance-method-call"></a><span data-ttu-id="0c975-150">Chiamata al metodo dell'istanza del componente</span><span class="sxs-lookup"><span data-stu-id="0c975-150">Component instance method call</span></span>

<span data-ttu-id="0c975-151">Per richiamare i metodi .NET di un componente:</span><span class="sxs-lookup"><span data-stu-id="0c975-151">To invoke a component's .NET methods:</span></span>

* <span data-ttu-id="0c975-152">Utilizzare la `invokeMethod` funzione `invokeMethodAsync` o per eseguire una chiamata al metodo statico al componente.</span><span class="sxs-lookup"><span data-stu-id="0c975-152">Use the `invokeMethod` or `invokeMethodAsync` function to make a static method call to the component.</span></span>
* <span data-ttu-id="0c975-153">Il metodo statico del componente esegue il wrapping della chiamata al metodo di istanza come oggetto `Action`richiamato.</span><span class="sxs-lookup"><span data-stu-id="0c975-153">The component's static method wraps the call to its instance method as an invoked `Action`.</span></span>

<span data-ttu-id="0c975-154">Nel codice JavaScript lato client:</span><span class="sxs-lookup"><span data-stu-id="0c975-154">In the client-side JavaScript:</span></span>

```javascript
function updateMessageCallerJS() {
  DotNet.invokeMethod('BlazorSample', 'UpdateMessageCaller');
}
```

<span data-ttu-id="0c975-155">*Pages/JSInteropComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="0c975-155">*Pages/JSInteropComponent.razor*:</span></span>

```razor
@page "/JSInteropComponent"

<p>
    Message: @_message
</p>

<p>
    <button onclick="updateMessageCallerJS()">Call JS Method</button>
</p>

@code {
    private static Action _action;
    private string _message = "Select the button.";

    protected override void OnInitialized()
    {
        _action = UpdateMessage;
    }

    private void UpdateMessage()
    {
        _message = "UpdateMessage Called!";
        StateHasChanged();
    }

    [JSInvokable]
    public static void UpdateMessageCaller()
    {
        _action.Invoke();
    }
}
```

<span data-ttu-id="0c975-156">Quando sono presenti diversi componenti, ognuno con i metodi di istanza da chiamare, usare una classe helper per richiamare i metodi di istanza `Action`(come s) di ogni componente.</span><span class="sxs-lookup"><span data-stu-id="0c975-156">When there are several components, each with instance methods to call, use a helper class to invoke the instance methods (as `Action`s) of each component.</span></span>

<span data-ttu-id="0c975-157">Nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="0c975-157">In the following example:</span></span>

* <span data-ttu-id="0c975-158">Il `JSInterop` componente contiene diversi `ListItem` componenti.</span><span class="sxs-lookup"><span data-stu-id="0c975-158">The `JSInterop` component contains several `ListItem` components.</span></span>
* <span data-ttu-id="0c975-159">Ogni `ListItem` componente è costituito da un messaggio e da un pulsante.</span><span class="sxs-lookup"><span data-stu-id="0c975-159">Each `ListItem` component is composed of a message and a button.</span></span>
* <span data-ttu-id="0c975-160">Quando viene `ListItem` selezionato un pulsante componente, `ListItem`il `UpdateMessage` metodo modifica il testo dell'elemento dell'elenco e lo nasconde.</span><span class="sxs-lookup"><span data-stu-id="0c975-160">When a `ListItem` component button is selected, that `ListItem`'s `UpdateMessage` method changes the list item text and hides the button.</span></span>

<span data-ttu-id="0c975-161">*MessageUpdateInvokeHelper.cs*:</span><span class="sxs-lookup"><span data-stu-id="0c975-161">*MessageUpdateInvokeHelper.cs*:</span></span>

```csharp
using System;
using Microsoft.JSInterop;

public class MessageUpdateInvokeHelper
{
    private Action _action;

    public MessageUpdateInvokeHelper(Action action)
    {
        _action = action;
    }

    [JSInvokable("BlazorSample")]
    public void UpdateMessageCaller()
    {
        _action.Invoke();
    }
}
```

<span data-ttu-id="0c975-162">Nel codice JavaScript lato client:</span><span class="sxs-lookup"><span data-stu-id="0c975-162">In the client-side JavaScript:</span></span>

```javascript
window.updateMessageCallerJS = (dotnetHelper) => {
    dotnetHelper.invokeMethod('BlazorSample', 'UpdateMessageCaller');
    dotnetHelper.dispose();
}
```

<span data-ttu-id="0c975-163">*Shared/ListItem. Razor*:</span><span class="sxs-lookup"><span data-stu-id="0c975-163">*Shared/ListItem.razor*:</span></span>

```razor
@inject IJSRuntime JsRuntime

<li>
    @_message
    <button @onclick="InteropCall" style="display:@_display">InteropCall</button>
</li>

@code {
    private string _message = "Select one of these list item buttons.";
    private string _display = "inline-block";
    private MessageUpdateInvokeHelper _messageUpdateInvokeHelper;

    protected override void OnInitialized()
    {
        _messageUpdateInvokeHelper = new MessageUpdateInvokeHelper(UpdateMessage);
    }

    protected async Task InteropCall()
    {
        await JsRuntime.InvokeVoidAsync("updateMessageCallerJS",
            DotNetObjectReference.Create(_messageUpdateInvokeHelper));
    }

    private void UpdateMessage()
    {
        _message = "UpdateMessage Called!";
        _display = "none";
        StateHasChanged();
    }
}
```

<span data-ttu-id="0c975-164">*Pages/JSInterop. Razor*:</span><span class="sxs-lookup"><span data-stu-id="0c975-164">*Pages/JSInterop.razor*:</span></span>

```razor
@page "/JSInterop"

<h1>List of components</h1>

<ul>
    <ListItem />
    <ListItem />
    <ListItem />
    <ListItem />
</ul>
```

[!INCLUDE[Share interop code in a class library](~/includes/blazor-share-interop-code.md)]

## <a name="avoid-circular-object-references"></a><span data-ttu-id="0c975-165">Evitare riferimenti a oggetti circolari</span><span class="sxs-lookup"><span data-stu-id="0c975-165">Avoid circular object references</span></span>

<span data-ttu-id="0c975-166">Gli oggetti che contengono riferimenti circolari non possono essere serializzati sul client per uno dei seguenti:</span><span class="sxs-lookup"><span data-stu-id="0c975-166">Objects that contain circular references can't be serialized on the client for either:</span></span>

* <span data-ttu-id="0c975-167">Chiamate al metodo .NET.</span><span class="sxs-lookup"><span data-stu-id="0c975-167">.NET method calls.</span></span>
* <span data-ttu-id="0c975-168">Chiamate al metodo JavaScript da C# quando il tipo restituito contiene riferimenti circolari.</span><span class="sxs-lookup"><span data-stu-id="0c975-168">JavaScript method calls from C# when the return type has circular references.</span></span>

<span data-ttu-id="0c975-169">Per ulteriori informazioni, vedere i seguenti problemi:</span><span class="sxs-lookup"><span data-stu-id="0c975-169">For more information, see the following issues:</span></span>

* [<span data-ttu-id="0c975-170">I riferimenti circolari non sono supportati, accettano due (DotNet/aspnetcore #20525)</span><span class="sxs-lookup"><span data-stu-id="0c975-170">Circular references are not supported, take two (dotnet/aspnetcore #20525)</span></span>](https://github.com/dotnet/aspnetcore/issues/20525)
* [<span data-ttu-id="0c975-171">Proposta: aggiungere un meccanismo per gestire i riferimenti circolari durante la serializzazione (DotNet/Runtime #30820)</span><span class="sxs-lookup"><span data-stu-id="0c975-171">Proposal: Add mechanism to handle circular references when serializing (dotnet/runtime #30820)</span></span>](https://github.com/dotnet/runtime/issues/30820)

## <a name="additional-resources"></a><span data-ttu-id="0c975-172">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="0c975-172">Additional resources</span></span>

* <xref:blazor/call-javascript-from-dotnet>
* [<span data-ttu-id="0c975-173">Esempio di InteropComponent. Razor (repository GitHub DotNet/AspNetCore, Branch versione 3,1)</span><span class="sxs-lookup"><span data-stu-id="0c975-173">InteropComponent.razor example (dotnet/AspNetCore GitHub repository, 3.1 release branch)</span></span>](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
* <span data-ttu-id="0c975-174">[Eseguire trasferimenti di dati di Blazor grandi dimensioni nelle app Server](xref:blazor/advanced-scenarios#perform-large-data-transfers-in-blazor-server-apps)</span><span class="sxs-lookup"><span data-stu-id="0c975-174">[Perform large data transfers in Blazor Server apps](xref:blazor/advanced-scenarios#perform-large-data-transfers-in-blazor-server-apps)</span></span>
