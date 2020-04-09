---
title: Chiamare metodi .NET dalle funzioni JavaScript in ASP.NET CoreCall methods from JavaScript functions in ASP.NET CoreBlazor
author: guardrex
description: Scopri come richiamare metodi .NET Blazor dalle funzioni JavaScript nelle app.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/07/2020
no-loc:
- Blazor
- SignalR
uid: blazor/call-dotnet-from-javascript
ms.openlocfilehash: e2344dd15efd243a405373b6cf0362f28b48173a
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80976950"
---
# <a name="call-net-methods-from-javascript-functions-in-aspnet-core-opno-locblazor"></a><span data-ttu-id="0bb86-103">Chiamare metodi .NET dalle funzioni JavaScript in ASP.NET CoreCall methods from JavaScript functions in ASP.NET CoreBlazor</span><span class="sxs-lookup"><span data-stu-id="0bb86-103">Call .NET methods from JavaScript functions in ASP.NET Core Blazor</span></span>

<span data-ttu-id="0bb86-104">Di [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), [Shashikant Rudrawadi](http://wisne.co), e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="0bb86-104">By [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), [Shashikant Rudrawadi](http://wisne.co), and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="0bb86-105">Un'app Blazor può richiamare funzioni JavaScript da metodi .NET e metodi .NET da funzioni JavaScript.</span><span class="sxs-lookup"><span data-stu-id="0bb86-105">A Blazor app can invoke JavaScript functions from .NET methods and .NET methods from JavaScript functions.</span></span> <span data-ttu-id="0bb86-106">Questi scenari sono denominati *interoperabilità JavaScript* (*interoperabilità JS*).</span><span class="sxs-lookup"><span data-stu-id="0bb86-106">These scenarios are called *JavaScript interoperability* (*JS interop*).</span></span>

<span data-ttu-id="0bb86-107">In questo articolo viene illustrata la chiamata di metodi .NET da JavaScript.</span><span class="sxs-lookup"><span data-stu-id="0bb86-107">This article covers invoking .NET methods from JavaScript.</span></span> <span data-ttu-id="0bb86-108">Per informazioni su come chiamare funzioni JavaScript <xref:blazor/call-javascript-from-dotnet>da .NET, vedere .</span><span class="sxs-lookup"><span data-stu-id="0bb86-108">For information on how to call JavaScript functions from .NET, see <xref:blazor/call-javascript-from-dotnet>.</span></span>

<span data-ttu-id="0bb86-109">[Visualizzare o scaricare codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ( come[scaricare](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="0bb86-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="static-net-method-call"></a><span data-ttu-id="0bb86-110">Chiamata al metodo .NET staticoStatic .NET method call</span><span class="sxs-lookup"><span data-stu-id="0bb86-110">Static .NET method call</span></span>

<span data-ttu-id="0bb86-111">Per richiamare un metodo .NET statico `DotNet.invokeMethod` `DotNet.invokeMethodAsync` da JavaScript, usare le funzioni o .</span><span class="sxs-lookup"><span data-stu-id="0bb86-111">To invoke a static .NET method from JavaScript, use the `DotNet.invokeMethod` or `DotNet.invokeMethodAsync` functions.</span></span> <span data-ttu-id="0bb86-112">Passare l'identificatore del metodo statico che si desidera chiamare, il nome dell'assembly contenente la funzione e gli eventuali argomenti.</span><span class="sxs-lookup"><span data-stu-id="0bb86-112">Pass in the identifier of the static method you wish to call, the name of the assembly containing the function, and any arguments.</span></span> <span data-ttu-id="0bb86-113">La versione asincrona Blazor è preferibile per supportare gli scenari Server.The asynchronous version is preferred to support Server scenarios.</span><span class="sxs-lookup"><span data-stu-id="0bb86-113">The asynchronous version is preferred to support Blazor Server scenarios.</span></span> <span data-ttu-id="0bb86-114">Il metodo .NET deve essere public, `[JSInvokable]` static e avere l'attributo .</span><span class="sxs-lookup"><span data-stu-id="0bb86-114">The .NET method must be public, static, and have the `[JSInvokable]` attribute.</span></span> <span data-ttu-id="0bb86-115">La chiamata a metodi generici aperti non è attualmente supportata.</span><span class="sxs-lookup"><span data-stu-id="0bb86-115">Calling open generic methods isn't currently supported.</span></span>

<span data-ttu-id="0bb86-116">L'app di esempio include un `int` metodo di C' per restituire una matrice.</span><span class="sxs-lookup"><span data-stu-id="0bb86-116">The sample app includes a C# method to return an `int` array.</span></span> <span data-ttu-id="0bb86-117">L'attributo `JSInvokable` viene applicato al metodo.</span><span class="sxs-lookup"><span data-stu-id="0bb86-117">The `JSInvokable` attribute is applied to the method.</span></span>

<span data-ttu-id="0bb86-118">*Pages/JsInterop.razor*:</span><span class="sxs-lookup"><span data-stu-id="0bb86-118">*Pages/JsInterop.razor*:</span></span>

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

<span data-ttu-id="0bb86-119">JavaScript servito al client richiama il metodo .NET di C.</span><span class="sxs-lookup"><span data-stu-id="0bb86-119">JavaScript served to the client invokes the C# .NET method.</span></span>

<span data-ttu-id="0bb86-120">*wwwroot/exampleJsInterop.js*:</span><span class="sxs-lookup"><span data-stu-id="0bb86-120">*wwwroot/exampleJsInterop.js*:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=8-14)]

<span data-ttu-id="0bb86-121">Quando il **pulsante Trigger .NET static method ReturnArrayAsync** è selezionato, esaminare l'output della console negli strumenti di sviluppo Web del browser.</span><span class="sxs-lookup"><span data-stu-id="0bb86-121">When the **Trigger .NET static method ReturnArrayAsync** button is selected, examine the console output in the browser's web developer tools.</span></span>

<span data-ttu-id="0bb86-122">L'uscita della console è:</span><span class="sxs-lookup"><span data-stu-id="0bb86-122">The console output is:</span></span>

```console
Array(4) [ 1, 2, 3, 4 ]
```

<span data-ttu-id="0bb86-123">Il quarto valore di matrice`data.push(4);`viene inserito `ReturnArrayAsync`nella matrice ( ) restituita da .</span><span class="sxs-lookup"><span data-stu-id="0bb86-123">The fourth array value is pushed to the array (`data.push(4);`) returned by `ReturnArrayAsync`.</span></span>

<span data-ttu-id="0bb86-124">Per impostazione predefinita, l'identificatore del metodo è il `JSInvokableAttribute` nome del metodo, ma è possibile specificare un identificatore diverso utilizzando il costruttore:By default, the method identifier is the method name, but you can specify a different identifier using the constructor:</span><span class="sxs-lookup"><span data-stu-id="0bb86-124">By default, the method identifier is the method name, but you can specify a different identifier using the `JSInvokableAttribute` constructor:</span></span>

```csharp
@code {
    [JSInvokable("DifferentMethodName")]
    public static Task<int[]> ReturnArrayAsync()
    {
        return Task.FromResult(new int[] { 1, 2, 3 });
    }
}
```

<span data-ttu-id="0bb86-125">Nel file JavaScript lato client:</span><span class="sxs-lookup"><span data-stu-id="0bb86-125">In the client-side JavaScript file:</span></span>

```javascript
returnArrayAsyncJs: function () {
  DotNet.invokeMethodAsync('BlazorSample', 'DifferentMethodName')
    .then(data => {
      data.push(4);
      console.log(data);
    });
}
```

## <a name="instance-method-call"></a><span data-ttu-id="0bb86-126">Chiamata al metodo di istanza</span><span class="sxs-lookup"><span data-stu-id="0bb86-126">Instance method call</span></span>

<span data-ttu-id="0bb86-127">È anche possibile chiamare i metodi di istanza .NET da JavaScript.You can also call .NET instance methods from JavaScript.</span><span class="sxs-lookup"><span data-stu-id="0bb86-127">You can also call .NET instance methods from JavaScript.</span></span> <span data-ttu-id="0bb86-128">Per richiamare un metodo di istanza .NET da JavaScript:</span><span class="sxs-lookup"><span data-stu-id="0bb86-128">To invoke a .NET instance method from JavaScript:</span></span>

* <span data-ttu-id="0bb86-129">Passare l'istanza .NET per riferimento a JavaScript:</span><span class="sxs-lookup"><span data-stu-id="0bb86-129">Pass the .NET instance by reference to JavaScript:</span></span>
  * <span data-ttu-id="0bb86-130">Effettuare una `DotNetObjectReference.Create`chiamata statica a .</span><span class="sxs-lookup"><span data-stu-id="0bb86-130">Make a static call to `DotNetObjectReference.Create`.</span></span>
  * <span data-ttu-id="0bb86-131">Eseguire il wrapping `DotNetObjectReference` dell'istanza in un'istanza e chiamare `Create` l'istanza. `DotNetObjectReference`</span><span class="sxs-lookup"><span data-stu-id="0bb86-131">Wrap the instance in a `DotNetObjectReference` instance and call `Create` on the `DotNetObjectReference` instance.</span></span> <span data-ttu-id="0bb86-132">Eliminare `DotNetObjectReference` gli oggetti (un esempio viene visualizzato più avanti in questa sezione).</span><span class="sxs-lookup"><span data-stu-id="0bb86-132">Dispose of `DotNetObjectReference` objects (an example appears later in this section).</span></span>
* <span data-ttu-id="0bb86-133">Richiamare i metodi di istanza `invokeMethod` `invokeMethodAsync` .NET sull'istanza utilizzando le funzioni o .</span><span class="sxs-lookup"><span data-stu-id="0bb86-133">Invoke .NET instance methods on the instance using the `invokeMethod` or `invokeMethodAsync` functions.</span></span> <span data-ttu-id="0bb86-134">L'istanza .NET può anche essere passata come argomento quando si richiamano altri metodi .NET da JavaScript.</span><span class="sxs-lookup"><span data-stu-id="0bb86-134">The .NET instance can also be passed as an argument when invoking other .NET methods from JavaScript.</span></span>

> [!NOTE]
> <span data-ttu-id="0bb86-135">L'app di esempio registra i messaggi nella console sul lato client.</span><span class="sxs-lookup"><span data-stu-id="0bb86-135">The sample app logs messages to the client-side console.</span></span> <span data-ttu-id="0bb86-136">Per gli esempi seguenti illustrati dall'app di esempio, esaminare l'output della console del browser negli strumenti di sviluppo del browser.</span><span class="sxs-lookup"><span data-stu-id="0bb86-136">For the following examples demonstrated by the sample app, examine the browser's console output in the browser's developer tools.</span></span>

<span data-ttu-id="0bb86-137">Quando il metodo di **istanza Trigger .NET** è `ExampleJsInterop.CallHelloHelperSayHello` selezionato, viene chiamato `Blazor`e passa un nome, , , al metodo .</span><span class="sxs-lookup"><span data-stu-id="0bb86-137">When the **Trigger .NET instance method HelloHelper.SayHello** button is selected, `ExampleJsInterop.CallHelloHelperSayHello` is called and passes a name, `Blazor`, to the method.</span></span>

<span data-ttu-id="0bb86-138">*Pages/JsInterop.razor*:</span><span class="sxs-lookup"><span data-stu-id="0bb86-138">*Pages/JsInterop.razor*:</span></span>

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

<span data-ttu-id="0bb86-139">`CallHelloHelperSayHello`richiama la funzione `sayHello` JavaScript con `HelloHelper`una nuova istanza di .</span><span class="sxs-lookup"><span data-stu-id="0bb86-139">`CallHelloHelperSayHello` invokes the JavaScript function `sayHello` with a new instance of `HelloHelper`.</span></span>

<span data-ttu-id="0bb86-140">*JsInteropClasses/ExampleJsInterop.cs*:</span><span class="sxs-lookup"><span data-stu-id="0bb86-140">*JsInteropClasses/ExampleJsInterop.cs*:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorWebAssemblySample/JsInteropClasses/ExampleJsInterop.cs?name=snippet1&highlight=11-18)]

<span data-ttu-id="0bb86-141">*wwwroot/exampleJsInterop.js*:</span><span class="sxs-lookup"><span data-stu-id="0bb86-141">*wwwroot/exampleJsInterop.js*:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=15-18)]

<span data-ttu-id="0bb86-142">Il nome viene `HelloHelper`passato al costruttore `HelloHelper.Name` di 's, che imposta la proprietà.</span><span class="sxs-lookup"><span data-stu-id="0bb86-142">The name is passed to `HelloHelper`'s constructor, which sets the `HelloHelper.Name` property.</span></span> <span data-ttu-id="0bb86-143">Quando viene eseguita la `sayHello` `HelloHelper.SayHello` funzione `Hello, {Name}!` JavaScript, restituisce il messaggio, che viene scritto nella console dalla funzione JavaScript.</span><span class="sxs-lookup"><span data-stu-id="0bb86-143">When the JavaScript function `sayHello` is executed, `HelloHelper.SayHello` returns the `Hello, {Name}!` message, which is written to the console by the JavaScript function.</span></span>

<span data-ttu-id="0bb86-144">*JsInteropClasses/HelloHelper.cs*:</span><span class="sxs-lookup"><span data-stu-id="0bb86-144">*JsInteropClasses/HelloHelper.cs*:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorWebAssemblySample/JsInteropClasses/HelloHelper.cs?name=snippet1&highlight=5,10-11)]

<span data-ttu-id="0bb86-145">Output della console negli strumenti di sviluppo Web del browser:</span><span class="sxs-lookup"><span data-stu-id="0bb86-145">Console output in the browser's web developer tools:</span></span>

```console
Hello, Blazor!
```

<span data-ttu-id="0bb86-146">Per evitare una perdita di memoria e `DotNetObjectReference`consentire l'operazione di Garbage Collection su un componente che crea un oggetto , adottare uno dei seguenti approcci:</span><span class="sxs-lookup"><span data-stu-id="0bb86-146">To avoid a memory leak and allow garbage collection on a component that creates a `DotNetObjectReference`, adopt one of the following approaches:</span></span>

* <span data-ttu-id="0bb86-147">Eliminare l'oggetto nella classe `DotNetObjectReference` che ha creato l'istanza:</span><span class="sxs-lookup"><span data-stu-id="0bb86-147">Dispose of the object in the class that created the `DotNetObjectReference` instance:</span></span>

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

  <span data-ttu-id="0bb86-148">Il modello precedente illustrato `ExampleJsInterop` nella classe può essere implementato anche in un componente:The preceding pattern shown in the class can also be implemented in a component:</span><span class="sxs-lookup"><span data-stu-id="0bb86-148">The preceding pattern shown in the `ExampleJsInterop` class can also be implemented in a component:</span></span>

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

* <span data-ttu-id="0bb86-149">Quando il componente o la classe `DotNetObjectReference`non elimina l'oggetto , `.dispose()`elimina l'oggetto sul client chiamando :</span><span class="sxs-lookup"><span data-stu-id="0bb86-149">When the component or class doesn't dispose of the `DotNetObjectReference`, dispose of the object on the client by calling `.dispose()`:</span></span>

  ```javascript
  window.myFunction = (dotnetHelper) => {
    dotnetHelper.invokeMethod('BlazorSample', 'MyMethod');
    dotnetHelper.dispose();
  }
  ```

## <a name="component-instance-method-call"></a><span data-ttu-id="0bb86-150">Chiamata al metodo dell'istanza del componenteComponent instance method call</span><span class="sxs-lookup"><span data-stu-id="0bb86-150">Component instance method call</span></span>

<span data-ttu-id="0bb86-151">Per richiamare i metodi .NET di un componente:</span><span class="sxs-lookup"><span data-stu-id="0bb86-151">To invoke a component's .NET methods:</span></span>

* <span data-ttu-id="0bb86-152">Utilizzare `invokeMethod` la `invokeMethodAsync` funzione o per effettuare una chiamata al metodo statico al componente.</span><span class="sxs-lookup"><span data-stu-id="0bb86-152">Use the `invokeMethod` or `invokeMethodAsync` function to make a static method call to the component.</span></span>
* <span data-ttu-id="0bb86-153">Il metodo statico del componente esegue il wrapping della `Action`chiamata al relativo metodo di istanza come metodo richiamato.</span><span class="sxs-lookup"><span data-stu-id="0bb86-153">The component's static method wraps the call to its instance method as an invoked `Action`.</span></span>

<span data-ttu-id="0bb86-154">Nel JavaScript lato client:</span><span class="sxs-lookup"><span data-stu-id="0bb86-154">In the client-side JavaScript:</span></span>

```javascript
function updateMessageCallerJS() {
  DotNet.invokeMethod('BlazorSample', 'UpdateMessageCaller');
}
```

<span data-ttu-id="0bb86-155">*Pages/JSInteropComponent.razor*:</span><span class="sxs-lookup"><span data-stu-id="0bb86-155">*Pages/JSInteropComponent.razor*:</span></span>

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

<span data-ttu-id="0bb86-156">Quando sono presenti più componenti, ognuno con metodi di istanza da `Action`chiamare, utilizzare una classe helper per richiamare i metodi di istanza (come s) di ogni componente.</span><span class="sxs-lookup"><span data-stu-id="0bb86-156">When there are several components, each with instance methods to call, use a helper class to invoke the instance methods (as `Action`s) of each component.</span></span>

<span data-ttu-id="0bb86-157">Nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="0bb86-157">In the following example:</span></span>

* <span data-ttu-id="0bb86-158">Il `JSInterop` componente `ListItem` contiene diversi componenti.</span><span class="sxs-lookup"><span data-stu-id="0bb86-158">The `JSInterop` component contains several `ListItem` components.</span></span>
* <span data-ttu-id="0bb86-159">Ogni `ListItem` componente è composto da un messaggio e da un pulsante.</span><span class="sxs-lookup"><span data-stu-id="0bb86-159">Each `ListItem` component is composed of a message and a button.</span></span>
* <span data-ttu-id="0bb86-160">Quando `ListItem` viene selezionato un `ListItem`pulsante `UpdateMessage` del componente, il metodo 'modifica il testo dell'elemento dell'elenco e nasconde il pulsante.</span><span class="sxs-lookup"><span data-stu-id="0bb86-160">When a `ListItem` component button is selected, that `ListItem`'s `UpdateMessage` method changes the list item text and hides the button.</span></span>

<span data-ttu-id="0bb86-161">*MessageUpdateInvokeHelper.cs*:</span><span class="sxs-lookup"><span data-stu-id="0bb86-161">*MessageUpdateInvokeHelper.cs*:</span></span>

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

<span data-ttu-id="0bb86-162">Nel JavaScript lato client:</span><span class="sxs-lookup"><span data-stu-id="0bb86-162">In the client-side JavaScript:</span></span>

```javascript
window.updateMessageCallerJS = (dotnetHelper) => {
    dotnetHelper.invokeMethod('BlazorSample', 'UpdateMessageCaller');
    dotnetHelper.dispose();
}
```

<span data-ttu-id="0bb86-163">*Condiviso/ElencoItem.razor*:</span><span class="sxs-lookup"><span data-stu-id="0bb86-163">*Shared/ListItem.razor*:</span></span>

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

<span data-ttu-id="0bb86-164">*Pages/JSInterop.razor*:</span><span class="sxs-lookup"><span data-stu-id="0bb86-164">*Pages/JSInterop.razor*:</span></span>

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

## <a name="avoid-circular-object-references"></a><span data-ttu-id="0bb86-165">Evitare riferimenti a oggetti circolari</span><span class="sxs-lookup"><span data-stu-id="0bb86-165">Avoid circular object references</span></span>

<span data-ttu-id="0bb86-166">Gli oggetti che contengono riferimenti circolari non possono essere serializzati sul client per:</span><span class="sxs-lookup"><span data-stu-id="0bb86-166">Objects that contain circular references can't be serialized on the client for either:</span></span>

* <span data-ttu-id="0bb86-167">Chiamate al metodo .NET.</span><span class="sxs-lookup"><span data-stu-id="0bb86-167">.NET method calls.</span></span>
* <span data-ttu-id="0bb86-168">Le chiamate al metodo JavaScript da C, quando il tipo restituito ha riferimenti circolari.</span><span class="sxs-lookup"><span data-stu-id="0bb86-168">JavaScript method calls from C# when the return type has circular references.</span></span>

<span data-ttu-id="0bb86-169">Per ulteriori informazioni, vedere i seguenti problemi:</span><span class="sxs-lookup"><span data-stu-id="0bb86-169">For more information, see the following issues:</span></span>

* [<span data-ttu-id="0bb86-170">I riferimenti circolari non sono supportati, accettadue (#20525 dotnet/aspnetcore)</span><span class="sxs-lookup"><span data-stu-id="0bb86-170">Circular references are not supported, take two (dotnet/aspnetcore #20525)</span></span>](https://github.com/dotnet/aspnetcore/issues/20525)
* [<span data-ttu-id="0bb86-171">Proposta: aggiungere un meccanismo per gestire i riferimenti circolari durante la serializzazione (#30820 di patchnet/runtime)</span><span class="sxs-lookup"><span data-stu-id="0bb86-171">Proposal: Add mechanism to handle circular references when serializing (dotnet/runtime #30820)</span></span>](https://github.com/dotnet/runtime/issues/30820)

## <a name="additional-resources"></a><span data-ttu-id="0bb86-172">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="0bb86-172">Additional resources</span></span>

* <xref:blazor/call-javascript-from-dotnet>
* [<span data-ttu-id="0bb86-173">Esempio InteropComponent.razor (repository dotnet/AspNetCore GitHub, ramo di rilascio 3.1)</span><span class="sxs-lookup"><span data-stu-id="0bb86-173">InteropComponent.razor example (dotnet/AspNetCore GitHub repository, 3.1 release branch)</span></span>](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
* <span data-ttu-id="0bb86-174">[Eseguire trasferimenti Blazor di dati di grandi dimensioni nelle app serverPerform large data transfers in Server apps](xref:blazor/advanced-scenarios#perform-large-data-transfers-in-blazor-server-apps)</span><span class="sxs-lookup"><span data-stu-id="0bb86-174">[Perform large data transfers in Blazor Server apps](xref:blazor/advanced-scenarios#perform-large-data-transfers-in-blazor-server-apps)</span></span>
