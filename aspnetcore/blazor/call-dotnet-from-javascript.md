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
# <a name="call-net-methods-from-javascript-functions-in-aspnet-core-blazor"></a>Chiamare i metodi .NET da funzioni JavaScript in ASP.NET CoreBlazor

Di [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), [Rudrawadi](http://wisne.co)e [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Un' Blazor app può richiamare funzioni JavaScript da metodi .NET e metodi .NET da funzioni JavaScript. Questi scenari sono detti *interoperabilità JavaScript (interoperabilità* *JS*).

Questo articolo descrive come richiamare i metodi .NET da JavaScript. Per informazioni su come chiamare funzioni JavaScript da .NET, vedere <xref:blazor/call-javascript-from-dotnet>.

[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([procedura per il download](xref:index#how-to-download-a-sample))

## <a name="static-net-method-call"></a>Chiamata al metodo .NET statico

Per richiamare un metodo .NET statico da JavaScript, usare le `DotNet.invokeMethod` funzioni `DotNet.invokeMethodAsync` o. Passare l'identificatore del metodo statico che si desidera chiamare, il nome dell'assembly che contiene la funzione e gli eventuali argomenti. La versione asincrona è preferibile Blazor per supportare scenari server. Il metodo .NET deve essere pubblico, statico e avere l' `[JSInvokable]` attributo. La chiamata ai metodi generici aperti non è attualmente supportata.

L'app di esempio include un metodo C# per restituire `int` una matrice. L' `JSInvokable` attributo viene applicato al metodo.

*Pages/JsInterop. Razor*:

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

JavaScript servito al client richiama il metodo C# .NET.

*wwwroot/exampleJsInterop. js*:

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=8-14)]

Quando si seleziona il pulsante **trigger .net static method ReturnArrayAsync** , esaminare l'output della console negli strumenti di sviluppo Web del browser.

L'output della console è:

```console
Array(4) [ 1, 2, 3, 4 ]
```

Il quarto valore della matrice viene inserito nella matrice (`data.push(4);`) restituito da `ReturnArrayAsync`.

Per impostazione predefinita, l'identificatore del metodo è il nome del metodo, ma è possibile specificare un identificatore `JSInvokableAttribute` diverso usando il costruttore:

```csharp
@code {
    [JSInvokable("DifferentMethodName")]
    public static Task<int[]> ReturnArrayAsync()
    {
        return Task.FromResult(new int[] { 1, 2, 3 });
    }
}
```

Nel file JavaScript sul lato client:

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

È anche possibile chiamare i metodi di istanza .NET da JavaScript. Per richiamare un metodo di istanza .NET da JavaScript:

* Passa l'istanza .NET per riferimento a JavaScript:
  * Eseguire una chiamata statica a `DotNetObjectReference.Create`.
  * Eseguire il wrapping dell'istanza `DotNetObjectReference` in un'istanza `Create` di e `DotNetObjectReference` chiamare sull'istanza. Eliminazione di `DotNetObjectReference` oggetti (un esempio viene visualizzato più avanti in questa sezione).
* Richiamare i metodi di istanza .NET sull'istanza usando `invokeMethod` le `invokeMethodAsync` funzioni o. L'istanza di .NET può essere passata anche come argomento quando si richiamano altri metodi .NET da JavaScript.

> [!NOTE]
> L'app di esempio consente di registrare i messaggi nella console lato client. Per gli esempi seguenti illustrati dall'app di esempio, esaminare l'output della console del browser negli strumenti di sviluppo del browser.

Quando il pulsante del **metodo di istanza .NET del trigger HelloHelper. sayHello** è selezionato, `ExampleJsInterop.CallHelloHelperSayHello` viene chiamato e passa `Blazor`un nome al metodo.

*Pages/JsInterop. Razor*:

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

`CallHelloHelperSayHello`richiama la funzione `sayHello` JavaScript con una nuova istanza di `HelloHelper`.

*JsInteropClasses/ExampleJsInterop. cs*:

[!code-csharp[](./common/samples/3.x/BlazorWebAssemblySample/JsInteropClasses/ExampleJsInterop.cs?name=snippet1&highlight=11-18)]

*wwwroot/exampleJsInterop. js*:

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=15-18)]

Il nome viene passato al `HelloHelper`costruttore, che imposta la `HelloHelper.Name` proprietà. Quando viene eseguita la `sayHello` funzione JavaScript, `HelloHelper.SayHello` restituisce il `Hello, {Name}!` messaggio, che viene scritto nella console dalla funzione JavaScript.

*JsInteropClasses/HelloHelper. cs*:

[!code-csharp[](./common/samples/3.x/BlazorWebAssemblySample/JsInteropClasses/HelloHelper.cs?name=snippet1&highlight=5,10-11)]

Output della console negli strumenti di sviluppo Web del browser:

```console
Hello, Blazor!
```

Per evitare una perdita di memoria e consentire Garbage Collection su un componente che crea `DotNetObjectReference`un, adottare uno degli approcci seguenti:

* Eliminare l'oggetto nella classe che ha creato l' `DotNetObjectReference` istanza:

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

  Il modello precedente illustrato nella `ExampleJsInterop` classe può anche essere implementato in un componente:

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

* Quando il componente o la classe non Elimina `DotNetObjectReference`, eliminare l'oggetto nel client chiamando: `.dispose()`

  ```javascript
  window.myFunction = (dotnetHelper) => {
    dotnetHelper.invokeMethod('BlazorSample', 'MyMethod');
    dotnetHelper.dispose();
  }
  ```

## <a name="component-instance-method-call"></a>Chiamata al metodo dell'istanza del componente

Per richiamare i metodi .NET di un componente:

* Utilizzare la `invokeMethod` funzione `invokeMethodAsync` o per eseguire una chiamata al metodo statico al componente.
* Il metodo statico del componente esegue il wrapping della chiamata al metodo di istanza come oggetto `Action`richiamato.

Nel codice JavaScript lato client:

```javascript
function updateMessageCallerJS() {
  DotNet.invokeMethod('BlazorSample', 'UpdateMessageCaller');
}
```

*Pages/JSInteropComponent. Razor*:

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

Quando sono presenti diversi componenti, ognuno con i metodi di istanza da chiamare, usare una classe helper per richiamare i metodi di istanza `Action`(come s) di ogni componente.

Nell'esempio seguente:

* Il `JSInterop` componente contiene diversi `ListItem` componenti.
* Ogni `ListItem` componente è costituito da un messaggio e da un pulsante.
* Quando viene `ListItem` selezionato un pulsante componente, `ListItem`il `UpdateMessage` metodo modifica il testo dell'elemento dell'elenco e lo nasconde.

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

Nel codice JavaScript lato client:

```javascript
window.updateMessageCallerJS = (dotnetHelper) => {
    dotnetHelper.invokeMethod('BlazorSample', 'UpdateMessageCaller');
    dotnetHelper.dispose();
}
```

*Shared/ListItem. Razor*:

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

*Pages/JSInterop. Razor*:

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

Gli oggetti che contengono riferimenti circolari non possono essere serializzati sul client per uno dei seguenti:

* Chiamate al metodo .NET.
* Chiamate al metodo JavaScript da C# quando il tipo restituito contiene riferimenti circolari.

Per ulteriori informazioni, vedere i seguenti problemi:

* [I riferimenti circolari non sono supportati, accettano due (DotNet/aspnetcore #20525)](https://github.com/dotnet/aspnetcore/issues/20525)
* [Proposta: aggiungere un meccanismo per gestire i riferimenti circolari durante la serializzazione (DotNet/Runtime #30820)](https://github.com/dotnet/runtime/issues/30820)

## <a name="additional-resources"></a>Risorse aggiuntive

* <xref:blazor/call-javascript-from-dotnet>
* [Esempio di InteropComponent. Razor (repository GitHub DotNet/AspNetCore, Branch versione 3,1)](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
* [Eseguire trasferimenti di dati di Blazor grandi dimensioni nelle app Server](xref:blazor/advanced-scenarios#perform-large-data-transfers-in-blazor-server-apps)
