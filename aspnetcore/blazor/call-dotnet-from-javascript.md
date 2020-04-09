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
# <a name="call-net-methods-from-javascript-functions-in-aspnet-core-opno-locblazor"></a>Chiamare metodi .NET dalle funzioni JavaScript in ASP.NET CoreCall methods from JavaScript functions in ASP.NET CoreBlazor

Di [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), [Shashikant Rudrawadi](http://wisne.co), e [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Un'app Blazor può richiamare funzioni JavaScript da metodi .NET e metodi .NET da funzioni JavaScript. Questi scenari sono denominati *interoperabilità JavaScript* (*interoperabilità JS*).

In questo articolo viene illustrata la chiamata di metodi .NET da JavaScript. Per informazioni su come chiamare funzioni JavaScript <xref:blazor/call-javascript-from-dotnet>da .NET, vedere .

[Visualizzare o scaricare codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ( come[scaricare](xref:index#how-to-download-a-sample))

## <a name="static-net-method-call"></a>Chiamata al metodo .NET staticoStatic .NET method call

Per richiamare un metodo .NET statico `DotNet.invokeMethod` `DotNet.invokeMethodAsync` da JavaScript, usare le funzioni o . Passare l'identificatore del metodo statico che si desidera chiamare, il nome dell'assembly contenente la funzione e gli eventuali argomenti. La versione asincrona Blazor è preferibile per supportare gli scenari Server.The asynchronous version is preferred to support Server scenarios. Il metodo .NET deve essere public, `[JSInvokable]` static e avere l'attributo . La chiamata a metodi generici aperti non è attualmente supportata.

L'app di esempio include un `int` metodo di C' per restituire una matrice. L'attributo `JSInvokable` viene applicato al metodo.

*Pages/JsInterop.razor*:

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

JavaScript servito al client richiama il metodo .NET di C.

*wwwroot/exampleJsInterop.js*:

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=8-14)]

Quando il **pulsante Trigger .NET static method ReturnArrayAsync** è selezionato, esaminare l'output della console negli strumenti di sviluppo Web del browser.

L'uscita della console è:

```console
Array(4) [ 1, 2, 3, 4 ]
```

Il quarto valore di matrice`data.push(4);`viene inserito `ReturnArrayAsync`nella matrice ( ) restituita da .

Per impostazione predefinita, l'identificatore del metodo è il `JSInvokableAttribute` nome del metodo, ma è possibile specificare un identificatore diverso utilizzando il costruttore:By default, the method identifier is the method name, but you can specify a different identifier using the constructor:

```csharp
@code {
    [JSInvokable("DifferentMethodName")]
    public static Task<int[]> ReturnArrayAsync()
    {
        return Task.FromResult(new int[] { 1, 2, 3 });
    }
}
```

Nel file JavaScript lato client:

```javascript
returnArrayAsyncJs: function () {
  DotNet.invokeMethodAsync('BlazorSample', 'DifferentMethodName')
    .then(data => {
      data.push(4);
      console.log(data);
    });
}
```

## <a name="instance-method-call"></a>Chiamata al metodo di istanza

È anche possibile chiamare i metodi di istanza .NET da JavaScript.You can also call .NET instance methods from JavaScript. Per richiamare un metodo di istanza .NET da JavaScript:

* Passare l'istanza .NET per riferimento a JavaScript:
  * Effettuare una `DotNetObjectReference.Create`chiamata statica a .
  * Eseguire il wrapping `DotNetObjectReference` dell'istanza in un'istanza e chiamare `Create` l'istanza. `DotNetObjectReference` Eliminare `DotNetObjectReference` gli oggetti (un esempio viene visualizzato più avanti in questa sezione).
* Richiamare i metodi di istanza `invokeMethod` `invokeMethodAsync` .NET sull'istanza utilizzando le funzioni o . L'istanza .NET può anche essere passata come argomento quando si richiamano altri metodi .NET da JavaScript.

> [!NOTE]
> L'app di esempio registra i messaggi nella console sul lato client. Per gli esempi seguenti illustrati dall'app di esempio, esaminare l'output della console del browser negli strumenti di sviluppo del browser.

Quando il metodo di **istanza Trigger .NET** è `ExampleJsInterop.CallHelloHelperSayHello` selezionato, viene chiamato `Blazor`e passa un nome, , , al metodo .

*Pages/JsInterop.razor*:

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

`CallHelloHelperSayHello`richiama la funzione `sayHello` JavaScript con `HelloHelper`una nuova istanza di .

*JsInteropClasses/ExampleJsInterop.cs*:

[!code-csharp[](./common/samples/3.x/BlazorWebAssemblySample/JsInteropClasses/ExampleJsInterop.cs?name=snippet1&highlight=11-18)]

*wwwroot/exampleJsInterop.js*:

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=15-18)]

Il nome viene `HelloHelper`passato al costruttore `HelloHelper.Name` di 's, che imposta la proprietà. Quando viene eseguita la `sayHello` `HelloHelper.SayHello` funzione `Hello, {Name}!` JavaScript, restituisce il messaggio, che viene scritto nella console dalla funzione JavaScript.

*JsInteropClasses/HelloHelper.cs*:

[!code-csharp[](./common/samples/3.x/BlazorWebAssemblySample/JsInteropClasses/HelloHelper.cs?name=snippet1&highlight=5,10-11)]

Output della console negli strumenti di sviluppo Web del browser:

```console
Hello, Blazor!
```

Per evitare una perdita di memoria e `DotNetObjectReference`consentire l'operazione di Garbage Collection su un componente che crea un oggetto , adottare uno dei seguenti approcci:

* Eliminare l'oggetto nella classe `DotNetObjectReference` che ha creato l'istanza:

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

  Il modello precedente illustrato `ExampleJsInterop` nella classe può essere implementato anche in un componente:The preceding pattern shown in the class can also be implemented in a component:

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

* Quando il componente o la classe `DotNetObjectReference`non elimina l'oggetto , `.dispose()`elimina l'oggetto sul client chiamando :

  ```javascript
  window.myFunction = (dotnetHelper) => {
    dotnetHelper.invokeMethod('BlazorSample', 'MyMethod');
    dotnetHelper.dispose();
  }
  ```

## <a name="component-instance-method-call"></a>Chiamata al metodo dell'istanza del componenteComponent instance method call

Per richiamare i metodi .NET di un componente:

* Utilizzare `invokeMethod` la `invokeMethodAsync` funzione o per effettuare una chiamata al metodo statico al componente.
* Il metodo statico del componente esegue il wrapping della `Action`chiamata al relativo metodo di istanza come metodo richiamato.

Nel JavaScript lato client:

```javascript
function updateMessageCallerJS() {
  DotNet.invokeMethod('BlazorSample', 'UpdateMessageCaller');
}
```

*Pages/JSInteropComponent.razor*:

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

Quando sono presenti più componenti, ognuno con metodi di istanza da `Action`chiamare, utilizzare una classe helper per richiamare i metodi di istanza (come s) di ogni componente.

Nell'esempio seguente:

* Il `JSInterop` componente `ListItem` contiene diversi componenti.
* Ogni `ListItem` componente è composto da un messaggio e da un pulsante.
* Quando `ListItem` viene selezionato un `ListItem`pulsante `UpdateMessage` del componente, il metodo 'modifica il testo dell'elemento dell'elenco e nasconde il pulsante.

*MessageUpdateInvokeHelper.cs*:

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

Nel JavaScript lato client:

```javascript
window.updateMessageCallerJS = (dotnetHelper) => {
    dotnetHelper.invokeMethod('BlazorSample', 'UpdateMessageCaller');
    dotnetHelper.dispose();
}
```

*Condiviso/ElencoItem.razor*:

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

*Pages/JSInterop.razor*:

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

## <a name="avoid-circular-object-references"></a>Evitare riferimenti a oggetti circolari

Gli oggetti che contengono riferimenti circolari non possono essere serializzati sul client per:

* Chiamate al metodo .NET.
* Le chiamate al metodo JavaScript da C, quando il tipo restituito ha riferimenti circolari.

Per ulteriori informazioni, vedere i seguenti problemi:

* [I riferimenti circolari non sono supportati, accettadue (#20525 dotnet/aspnetcore)](https://github.com/dotnet/aspnetcore/issues/20525)
* [Proposta: aggiungere un meccanismo per gestire i riferimenti circolari durante la serializzazione (#30820 di patchnet/runtime)](https://github.com/dotnet/runtime/issues/30820)

## <a name="additional-resources"></a>Risorse aggiuntive

* <xref:blazor/call-javascript-from-dotnet>
* [Esempio InteropComponent.razor (repository dotnet/AspNetCore GitHub, ramo di rilascio 3.1)](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
* [Eseguire trasferimenti Blazor di dati di grandi dimensioni nelle app serverPerform large data transfers in Server apps](xref:blazor/advanced-scenarios#perform-large-data-transfers-in-blazor-server-apps)
