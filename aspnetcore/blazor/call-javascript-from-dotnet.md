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
# <a name="call-javascript-functions-from-net-methods-in-aspnet-core-opno-locblazor"></a>Chiamare funzioni JavaScript dai metodi .NET in ASP.NET CoreCallBlazor

Di [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), e [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Un'app Blazor può richiamare funzioni JavaScript da metodi .NET e metodi .NET da funzioni JavaScript. Questi scenari sono denominati *interoperabilità JavaScript* (*interoperabilità JS*).

Questo articolo illustra la chiamata di funzioni JavaScript da .NET. Per informazioni su come chiamare metodi .NET <xref:blazor/call-dotnet-from-javascript>da JavaScript, vedere .

[Visualizzare o scaricare codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ( come[scaricare](xref:index#how-to-download-a-sample))

Per chiamare JavaScript da .NET, usare l'astrazione. `IJSRuntime` Per emettere chiamate di `IJSRuntime` interoperabilità JS, inserire l'astrazione nel componente. Il `InvokeAsync<T>` metodo accetta un identificatore per la funzione JavaScript che si desidera richiamare insieme a un numero qualsiasi di argomenti serializzabili JSON. L'identificatore di funzione è`window`relativo all'ambito globale ( ). Se si desidera `window.someScope.someFunction`chiamare , `someScope.someFunction`l'identificatore è . Non è necessario registrare la funzione prima che venga chiamata. Il tipo `T` restituito deve anche essere serializzabile JSON. `T`deve corrispondere al tipo .NET che meglio esegue il mapping al tipo JSON restituito.

Per Blazor le app Server con il prerendering abilitato, la chiamata in JavaScript non è possibile durante il prerendering iniziale. Le chiamate di interoperabilità JavaScript devono essere rinviate fino a quando non viene stabilita la connessione con il browser. Per altre informazioni, vedere la sezione [Rilevare il Blazor prerendering di un'app server.](#detect-when-a-blazor-server-app-is-prerendering)

L'esempio seguente è basato su [TextDecoder](https://developer.mozilla.org/docs/Web/API/TextDecoder), un decodificatore basato su JavaScript. Nell'esempio viene illustrato come richiamare una funzione JavaScript da un metodo c'è. La funzione JavaScript accetta una matrice di byte da un metodo C , decodifica la matrice e restituisce il testo al componente per la visualizzazione.

All'interno `<head>` dell'elemento *di wwwroot/index.html* (Blazor WebAssembly) o *Pages/_Host.cshtml* (Blazor Server), fornire una funzione JavaScript che utilizza `TextDecoder` per decodificare una matrice passata e restituire il valore decodificato:

[!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-convertarray.html)]

Il codice JavaScript, ad esempio il codice illustrato nell'esempio precedente, può anche essere caricato da un file JavaScript (*.js*) con un riferimento al file di script:

```html
<script src="exampleJsInterop.js"></script>
```

Il seguente componente:

* Richiama la `convertArray` funzione JavaScript utilizzando `JSRuntime` quando viene selezionato un pulsante del componente ( Convert**Array**).
* Dopo la chiamata alla funzione JavaScript, la matrice passata viene convertita in una stringa. La stringa viene restituita al componente per la visualizzazione.

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/call-js-example.razor?highlight=2,34-35)]

## <a name="ijsruntime"></a>IJSRuntime

Per utilizzare `IJSRuntime` l'astrazione, adottare uno dei seguenti approcci:

* Inserire `IJSRuntime` l'astrazione nel componente Razor (*.razor*):

  [!code-razor[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction.razor?highlight=1)]

  All'interno `<head>` dell'elemento di *wwwroot/index.html* (Blazor WebAssembly) o *Pages/_Host.cshtml* (Blazor Server), fornire una `handleTickerChanged` funzione JavaScript. La funzione viene `IJSRuntime.InvokeVoidAsync` chiamata con e non restituisce un valore:The function is called with and doesn't return a value:

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged1.html)]

* Inserire `IJSRuntime` l'astrazione in una classe (*.cs*):

  [!code-csharp[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction-class.cs?highlight=5)]

  All'interno `<head>` dell'elemento di *wwwroot/index.html* (Blazor WebAssembly) o *Pages/_Host.cshtml* (Blazor Server), fornire una `handleTickerChanged` funzione JavaScript. La funzione viene `JSRuntime.InvokeAsync` chiamata con e restituisce un valore:The function is called with and returns a value:

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged2.html)]

* Per la generazione di contenuto dinamico con [BuildRenderTree](xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic), utilizzare l'attributo: `[Inject]`

  ```razor
  [Inject]
  IJSRuntime JSRuntime { get; set; }
  ```

Nell'app di esempio lato client che accompagna questo argomento, sono disponibili due funzioni JavaScript per l'app che interagiscono con il DOM per ricevere l'input dell'utente e visualizzare un messaggio di benvenuto:In the client-side sample app that accompanies this topic, two JavaScript functions are available to the app that interact with the DOM to receive user input and display a welcome message:

* `showPrompt`&ndash; Produce una richiesta di accettazione dell'input dell'utente (il nome dell'utente) e restituisce il nome al chiamante.
* `displayWelcome`&ndash; Assegna un messaggio di benvenuto dal chiamante `id` `welcome`a un oggetto DOM con un oggetto di .

*wwwroot/exampleJsInterop.js*:

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=2-7)]

Inserire `<script>` il tag che fa riferimento alBlazor file JavaScript nel file di *wwwroot/index.html* ( WebAssembly) o nel file *Pages/_Host.cshtml* (Blazor Server).

*wwwroot/index.html* Blazor ( WebAssembly):

[!code-html[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/index.html?highlight=22)]

*Pages/_Host.cshtml* Blazor ( Server):

[!code-cshtml[](./common/samples/3.x/BlazorServerSample/Pages/_Host.cshtml?highlight=35)]

Non inserire un `<script>` tag in un `<script>` file componente perché il tag non può essere aggiornato dinamicamente.

I metodi .NET interop con le funzioni JavaScript nel `IJSRuntime.InvokeAsync<T>`file *exampleJsInterop.js* chiamando .

L'astrazione è asincrona per consentire scenari Server.The `IJSRuntime` abstraction is asynchronous to allow for Blazor Server scenarios. Se l'app Blazor è un'app WebAssembly e si desidera richiamare `IJSInProcessRuntime` una `Invoke<T>` funzione JavaScript in modo sincrono, eseguire il downcast e chiamare invece. È consigliabile che la maggior parte delle librerie di interoperabilità JS utilizzi le API asincrone per assicurarsi che le librerie siano disponibili in tutti gli scenari.

L'app di esempio include un componente per illustrare l'interoperabilità JS. Il componente:

* Riceve l'input dell'utente tramite un prompt JavaScript.
* Restituisce il testo al componente per l'elaborazione.
* Chiama una seconda funzione JavaScript che interagisce con il DOM per visualizzare un messaggio di benvenuto.

*Pages/JSInterop.razor*:

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

1. Quando `TriggerJsPrompt` viene eseguito selezionando il pulsante **Trigger JavaScript** `showPrompt` Prompt del componente, viene chiamata la funzione JavaScript fornita nel file *wwwroot/exampleJsInterop.js.*
1. La `showPrompt` funzione accetta l'input dell'utente (il nome dell'utente), che è codificato in HTML e restituito al componente. Il componente archivia il nome dell'utente in una variabile locale, `name`.
1. La stringa `name` archiviata in viene incorporata in un messaggio di `displayWelcome`benvenuto, che viene passato a una funzione JavaScript, che esegue il rendering del messaggio di benvenuto in un tag di intestazione.

## <a name="call-a-void-javascript-function"></a>Chiamare una funzione JavaScript voidCall a void JavaScript function

Le funzioni JavaScript che restituiscono [void(0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) o [undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined) vengono chiamate con `IJSRuntime.InvokeVoidAsync`.

## <a name="detect-when-a-opno-locblazor-server-app-is-prerendering"></a>Rilevare Blazor il prerendering di un'app serverDetect when a Server app is prerendering
 
[!INCLUDE[](~/includes/blazor-prerendering.md)]

## <a name="capture-references-to-elements"></a>Acquisire riferimenti agli elementi

Alcuni scenari di interoperabilità JS richiedono riferimenti a elementi HTML. Ad esempio, una libreria dell'interfaccia utente potrebbe richiedere un riferimento all'elemento per l'inizializzazione o potrebbe essere necessario chiamare API di tipo comando su un elemento, ad esempio `focus` o `play`.

Acquisire riferimenti agli elementi HTML in un componente utilizzando il seguente approccio:

* Aggiungere `@ref` un attributo all'elemento HTML.
* Definire un campo `ElementReference` di tipo il `@ref` cui nome corrisponde al valore dell'attributo.

L'esempio seguente mostra l'acquisizione di un riferimento all'elemento:The following example shows capturing a reference to the `username` `<input>` element:

```razor
<input @ref="username" ... />

@code {
    ElementReference username;
}
```

> [!WARNING]
> Utilizzare solo un riferimento all'elemento per modificare il contenuto di Blazorun elemento vuoto che non interagisce con . Questo scenario è utile quando un'API di terze parti fornisce contenuto all'elemento. Poiché Blazor non interagisce con l'elemento, non c'è alcuna possibilità di un conflitto tra Blazorla rappresentazione dell'elemento e il DOM.
>
> Nell'esempio seguente, è *pericoloso* modificare il contenuto dell'elenco`ul`non Blazor ordinato ( ) perché interagisce con il`<li>`DOM per popolare gli elementi dell'elenco di questo elemento ( ):
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
> Se l'interoperabilità JS modifica `MyList` il Blazor contenuto dell'elemento e tenta di applicare differenze all'elemento, le differenze non corrisponderanno al DOM.

Per quanto riguarda il codice `ElementReference` .NET, un è un handle opaco. *L'unica* cosa che `ElementReference` si può fare con è passarlo attraverso al codice JavaScript tramite l'interoperabilità JS. In questo caso, il codice lato `HTMLElement` JavaScript riceve un'istanza, che può essere usata con le normali API DOM.

Ad esempio, il codice seguente definisce un metodo di estensione .NET che consente di impostare lo stato attivo su un elemento:For example, the following code defines a .NET extension method that enables setting the focus on an element:

*exampleJsInterop.js*:

```javascript
window.exampleJsFunctions = {
  focusElement : function (element) {
    element.focus();
  }
}
```

Per chiamare una funzione JavaScript che non `IJSRuntime.InvokeVoidAsync`restituisce un valore, utilizzare . Il codice seguente imposta lo stato attivo sull'input del nome `ElementReference`utente chiamando la funzione JavaScript precedente con l'oggetto acquisito:

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component1.razor?highlight=1,3,11-12)]

Per usare un metodo di estensione, creare `IJSRuntime` un metodo di estensione statico che riceve l'istanza:To use an extension method, create a static extension method that receives the instance:

```csharp
public static async Task Focus(this ElementReference elementRef, IJSRuntime jsRuntime)
{
    await jsRuntime.InvokeVoidAsync(
        "exampleJsFunctions.focusElement", elementRef);
}
```

Il `Focus` metodo viene chiamato direttamente sull'oggetto. Nell'esempio seguente si `Focus` presuppone che `JsInteropClasses` il metodo sia disponibile dallo spazio dei nomi:The following example assumes that the method is available from the namespace:

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component2.razor?highlight=1-4,12)]

> [!IMPORTANT]
> La `username` variabile viene popolata solo dopo il rendering del componente. Se un `ElementReference` oggetto non popolato viene passato al codice JavaScript, il codice JavaScript riceve un valore di `null`. Per modificare i riferimenti agli elementi al termine del rendering del componente (per impostare lo stato attivo iniziale su un elemento) utilizzate i [metodi del ciclo di vita del componente OnAfterRenderAsync o OnAfterRender](xref:blazor/lifecycle#after-component-render).

Quando si utilizzano tipi generici e si restituisce un valore, usare [ValueTask\<T>](xref:System.Threading.Tasks.ValueTask`1):

```csharp
public static ValueTask<T> GenericMethod<T>(this ElementReference elementRef, 
    IJSRuntime jsRuntime)
{
    return jsRuntime.InvokeAsync<T>(
        "exampleJsFunctions.doSomethingGeneric", elementRef);
}
```

`GenericMethod`viene chiamato direttamente sull'oggetto con un tipo. Nell'esempio seguente si `GenericMethod` presuppone che `JsInteropClasses` l'oggetto sia disponibile dallo spazio dei nomi:The following example assumes that the is available from the namespace:

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component3.razor?highlight=17)]

## <a name="reference-elements-across-components"></a>Elementi di riferimento tra i componenti

Un `ElementReference` è garantito valido solo `OnAfterRender` nel metodo di un `struct`componente (e un riferimento all'elemento è un ), pertanto non è possibile passare un riferimento a un elemento tra componenti.

Affinché un componente padre renda un riferimento all'elemento disponibile ad altri componenti, il componente padre può:

* Consentire ai componenti figlio di registrare i callback.
* Richiamare i callback `OnAfterRender` registrati durante l'evento con il riferimento all'elemento passato. Indirettamente, questo approccio consente ai componenti figlio di interagire con il riferimento all'elemento padre.

Nell'esempio WebAssembly seguente Blazor viene illustrato l'approccio.

Nella `<head>` wwwroot/index.html *wwwroot/index.html*:

```html
<style>
    .red { color: red }
</style>
```

Nella `<body>` wwwroot/index.html *wwwroot/index.html*:

```html
<script>
    function setElementClass(element, className) {
        /** @type {HTMLElement} **/
        var myElement = element;
        myElement.classList.add(className);
    }
</script>
```

*Pages/Index.razor* (componente padre):

```razor
@page "/"

<h1 @ref="_title">Hello, world!</h1>

Welcome to your new app.

<SurveyPrompt Parent="this" Title="How is Blazor working for you?" />
```

*Pagine/Index.razor.cs*:

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

*Condiviso/SurveyPrompt.razor* (componente figlio):

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

*Condiviso/SurveyPrompt.razor.cs*:

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

## <a name="harden-js-interop-calls"></a>Chiamate di interoperabilità JS harden

L'interoperabilità JS potrebbe non riuscire a causa di errori di rete e deve essere considerata inaffidabile. Per impostazione Blazor predefinita, un'app Server scade le chiamate di interoperabilità JS sul server dopo un minuto. Se un'app è in grado di tollerare un timeout più aggressivo, ad esempio 10 secondi, imposta il timeout usando uno dei seguenti approcci:

* Globalmente `Startup.ConfigureServices`in , specificare il timeout:

  ```csharp
  services.AddServerSideBlazor(
      options => options.JSInteropDefaultCallTimeout = TimeSpan.FromSeconds({SECONDS}));
  ```

* Per la chiamata nel codice del componente, una singola chiamata può specificare il timeout:Per-invocation in component code, a single call can specify the timeout:

  ```csharp
  var result = await JSRuntime.InvokeAsync<string>("MyJSOperation", 
      TimeSpan.FromSeconds({SECONDS}), new[] { "Arg1" });
  ```

Per ulteriori informazioni sull'esaurimento <xref:security/blazor/server>delle risorse, vedere .

[!INCLUDE[Share interop code in a class library](~/includes/blazor-share-interop-code.md)]

## <a name="avoid-circular-object-references"></a>Evitare riferimenti a oggetti circolari

Gli oggetti che contengono riferimenti circolari non possono essere serializzati sul client per:

* Chiamate al metodo .NET.
* Le chiamate al metodo JavaScript da C, quando il tipo restituito ha riferimenti circolari.

Per ulteriori informazioni, vedere i seguenti problemi:

* [I riferimenti circolari non sono supportati, accettadue (#20525 dotnet/aspnetcore)](https://github.com/dotnet/aspnetcore/issues/20525)
* [Proposta: aggiungere un meccanismo per gestire i riferimenti circolari durante la serializzazione (#30820 di patchnet/runtime)](https://github.com/dotnet/runtime/issues/30820)

## <a name="additional-resources"></a>Risorse aggiuntive

* <xref:blazor/call-dotnet-from-javascript>
* [Esempio InteropComponent.razor (repository dotnet/AspNetCore GitHub, ramo di rilascio 3.1)](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
* [Eseguire trasferimenti Blazor di dati di grandi dimensioni nelle app serverPerform large data transfers in Server apps](xref:blazor/advanced-scenarios#perform-large-data-transfers-in-blazor-server-apps)
