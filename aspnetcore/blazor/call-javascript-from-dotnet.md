---
title: Chiamare funzioni JavaScript da metodi .NET in ASP.NET CoreBlazor
author: guardrex
description: Informazioni su come richiamare funzioni JavaScript da metodi .NET nelle Blazor app.
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
uid: blazor/call-javascript-from-dotnet
ms.openlocfilehash: 2f12c2a05ef510c38f407ccd69f748aceafd55de
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82767168"
---
# <a name="call-javascript-functions-from-net-methods-in-aspnet-core-blazor"></a>Chiamare funzioni JavaScript da metodi .NET in ASP.NET CoreBlazor

Di [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27)e [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Un' Blazor app può richiamare funzioni JavaScript da metodi .NET e metodi .NET da funzioni JavaScript. Questi scenari sono detti *interoperabilità JavaScript (interoperabilità* *JS*).

Questo articolo illustra come richiamare funzioni JavaScript da .NET. Per informazioni su come chiamare i metodi .NET da JavaScript, vedere <xref:blazor/call-dotnet-from-javascript>.

[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([procedura per il download](xref:index#how-to-download-a-sample))

Per chiamare JavaScript da .NET, usare l' `IJSRuntime` astrazione. Per rilasciare chiamate di interoperabilità JS `IJSRuntime` , inserire l'astrazione nel componente. Il `InvokeAsync<T>` metodo accetta un identificatore per la funzione JavaScript che si vuole richiamare insieme a un numero qualsiasi di argomenti serializzabili in JSON. L'identificatore della funzione è relativo all'ambito globale (`window`). Se si desidera chiamare `window.someScope.someFunction`, l'identificatore è. `someScope.someFunction` Non è necessario registrare la funzione prima che venga chiamata. Il tipo `T` restituito deve anche essere serializzabile in JSON. `T`deve corrispondere al tipo .NET che meglio esegue il mapping al tipo JSON restituito.

Per Blazor le app Server con il prerendering abilitato, non è possibile chiamare JavaScript durante il prerendering iniziale. È necessario rinviare le chiamate di interoperabilità JavaScript finché non viene stabilita la connessione con il browser. Per ulteriori informazioni, vedere la sezione [rilevare quando Blazor un'app Server è prerendering](#detect-when-a-blazor-server-app-is-prerendering) .

L'esempio seguente è basato su [TextDecoder](https://developer.mozilla.org/docs/Web/API/TextDecoder), un decodificatore basato su JavaScript. Nell'esempio viene illustrato come richiamare una funzione JavaScript da un metodo C#. La funzione JavaScript accetta una matrice di byte da un metodo C#, decodifica la matrice e restituisce il testo al componente per la visualizzazione.

All'interno `<head>` dell'elemento di *wwwroot/index.html* (Blazor webassembly) o *pages/_Host. cshtml* (Blazor Server), fornire una funzione JavaScript `TextDecoder` che usa per decodificare una matrice passata e restituire il valore decodificato:

[!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-convertarray.html)]

Il codice JavaScript, ad esempio il codice illustrato nell'esempio precedente, può anche essere caricato da un file JavaScript (con*estensione js*) con un riferimento al file script:

```html
<script src="exampleJsInterop.js"></script>
```

Il componente seguente:

* Richiama la `convertArray` funzione JavaScript usando `JSRuntime` quando viene selezionato un pulsante Component (**Convert Array**).
* Dopo la chiamata della funzione JavaScript, la matrice passata viene convertita in una stringa. La stringa viene restituita al componente per la visualizzazione.

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/call-js-example.razor?highlight=2,34-35)]

## <a name="ijsruntime"></a>IJSRuntime

Per usare l' `IJSRuntime` astrazione, adottare uno degli approcci seguenti:

* Inserire l' `IJSRuntime` astrazione nel Razor componente (*Razor*):

  [!code-razor[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction.razor?highlight=1)]

  All'interno `<head>` dell'elemento di *wwwroot/index.html* (Blazor webassembly) o *pages/_Host. cshtml* (Blazor Server), `handleTickerChanged` fornire una funzione JavaScript. La funzione viene chiamata con `IJSRuntime.InvokeVoidAsync` e non restituisce un valore:

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged1.html)]

* Inserire l' `IJSRuntime` astrazione in una classe (*. cs*):

  [!code-csharp[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction-class.cs?highlight=5)]

  All'interno `<head>` dell'elemento di *wwwroot/index.html* (Blazor webassembly) o *pages/_Host. cshtml* (Blazor Server), `handleTickerChanged` fornire una funzione JavaScript. La funzione viene chiamata con `JSRuntime.InvokeAsync` e restituisce un valore:

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged2.html)]

* Per la generazione di contenuto dinamico con [BuildRenderTree](xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic), `[Inject]` usare l'attributo:

  ```razor
  [Inject]
  IJSRuntime JSRuntime { get; set; }
  ```

Nell'app di esempio lato client che accompagna questo argomento sono disponibili due funzioni JavaScript per l'app che interagiscono con il DOM per ricevere l'input dell'utente e visualizzare un messaggio di benvenuto:

* `showPrompt`&ndash; Genera un messaggio di richiesta per accettare l'input dell'utente (il nome dell'utente) e restituisce il nome al chiamante.
* `displayWelcome`&ndash; Assegna un messaggio di benvenuto dal chiamante a un oggetto DOM con un `id` valore di `welcome`.

*wwwroot/exampleJsInterop. js*:

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=2-7)]

Inserire il `<script>` Tag che fa riferimento al file JavaScript nel file *wwwroot/index.html* (Blazor Webassembly) o nel file *pages/_Host. cshtml* (Blazor Server).

*wwwroot/index.html* (Blazor webassembly):

[!code-html[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/index.html?highlight=22)]

*Pagine/_Host. cshtml* (Blazor Server):

[!code-cshtml[](./common/samples/3.x/BlazorServerSample/Pages/_Host.cshtml?highlight=35)]

Non inserire un `<script>` tag in un file di componente perché `<script>` il tag non può essere aggiornato dinamicamente.

I metodi .NET interoperano con le funzioni JavaScript nel file *exampleJsInterop. js* chiamando `IJSRuntime.InvokeAsync<T>`.

L' `IJSRuntime` astrazione è asincrona per consentire Blazor scenari server. Se l'app è un' Blazor app webassembly e si vuole richiamare una funzione JavaScript in modo sincrono, `IJSInProcessRuntime` abbandono `Invoke<T>` e chiamano invece. È consigliabile che la maggior parte delle librerie di interoperabilità JS usi le API asincrone per assicurarsi che le librerie siano disponibili in tutti gli scenari.

L'app di esempio include un componente per dimostrare l'interoperabilità JS. Il componente:

* Riceve l'input dell'utente tramite un prompt di JavaScript.
* Restituisce il testo al componente per l'elaborazione.
* Chiama una seconda funzione JavaScript che interagisce con il DOM per visualizzare un messaggio di benvenuto.

*Pages/JSInterop. Razor*:

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

1. Quando `TriggerJsPrompt` viene eseguito selezionando il pulsante di **richiesta trigger JavaScript** del componente, viene `showPrompt` chiamata la funzione JavaScript fornita nel file *wwwroot/exampleJsInterop. js* .
1. La `showPrompt` funzione accetta l'input dell'utente (il nome dell'utente), che è codificato in HTML e restituito al componente. Il componente archivia il nome dell'utente in una variabile locale, `name`.
1. La stringa archiviata `name` in viene incorporata in un messaggio di benvenuto, che viene passato a una `displayWelcome`funzione JavaScript,, che esegue il rendering del messaggio di benvenuto in un tag di intestazione.

## <a name="call-a-void-javascript-function"></a>Chiamare una funzione JavaScript void

Le funzioni JavaScript che restituiscono [void (0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) o [undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined) vengono chiamate con `IJSRuntime.InvokeVoidAsync`.

## <a name="detect-when-a-blazor-server-app-is-prerendering"></a>Rilevare il momento Blazor in cui viene eseguito il prerendering di un'app Server
 
[!INCLUDE[](~/includes/blazor-prerendering.md)]

## <a name="capture-references-to-elements"></a>Acquisisci riferimenti a elementi

Alcuni scenari di interoperabilità JS richiedono riferimenti agli elementi HTML. Ad esempio, una libreria dell'interfaccia utente può richiedere un riferimento a un elemento per l'inizializzazione o potrebbe essere necessario chiamare API simili a quelle di un `focus` elemento `play`, ad esempio o.

Acquisire i riferimenti agli elementi HTML in un componente usando l'approccio seguente:

* Aggiungere un `@ref` attributo all'elemento HTML.
* Definire un campo di tipo `ElementReference` il cui nome corrisponde al valore dell' `@ref` attributo.

Nell'esempio seguente viene illustrata l'acquisizione di `username` `<input>` un riferimento all'elemento:

```razor
<input @ref="username" ... />

@code {
    ElementReference username;
}
```

> [!WARNING]
> Usare solo un riferimento a un elemento per mutare il contenuto di un elemento vuoto che non Blazorinteragisce con. Questo scenario è utile quando un'API di terze parti fornisce contenuto all'elemento. Poiché Blazor non interagisce con l'elemento, non è possibile che si verifichi un Blazorconflitto tra la rappresentazione dell'elemento e il Dom.
>
> Nell'esempio seguente è *pericoloso* modificare il contenuto dell'elenco non ordinato (`ul`) perché Blazor interagisce con il Dom per popolare gli elementi elenco di questo elemento (`<li>`):
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
> Se l'interoperabilità JS modifica il contenuto `MyList` dell' Blazor elemento e tenta di applicare differenze all'elemento, le differenze non corrispondono al Dom.

Per quanto riguarda il codice .NET, un `ElementReference` è un handle opaco. L' *unica* cosa che è possibile fare `ElementReference` con è passarla al codice JavaScript tramite l'interoperabilità js. Quando si esegue questa operazione, il codice sul lato JavaScript riceve `HTMLElement` un'istanza, che può essere usata con le API DOM normali.

Il codice seguente, ad esempio, definisce un metodo di estensione .NET che consente di impostare lo stato attivo su un elemento:

*exampleJsInterop. js*:

```javascript
window.exampleJsFunctions = {
  focusElement : function (element) {
    element.focus();
  }
}
```

Per chiamare una funzione JavaScript che non restituisce un valore, usare `IJSRuntime.InvokeVoidAsync`. Il codice seguente imposta lo stato attivo sull'input del nome utente chiamando la funzione JavaScript precedente con l' `ElementReference`acquisizione:

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component1.razor?highlight=1,3,11-12)]

Per usare un metodo di estensione, creare un metodo di estensione statico che `IJSRuntime` riceva l'istanza:

```csharp
public static async Task Focus(this ElementReference elementRef, IJSRuntime jsRuntime)
{
    await jsRuntime.InvokeVoidAsync(
        "exampleJsFunctions.focusElement", elementRef);
}
```

Il `Focus` metodo viene chiamato direttamente nell'oggetto. Nell'esempio seguente si presuppone che `Focus` il metodo sia disponibile dallo `JsInteropClasses` spazio dei nomi:

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component2.razor?highlight=1-4,12)]

> [!IMPORTANT]
> La `username` variabile viene popolata solo dopo il rendering del componente. Se un oggetto non `ElementReference` popolato viene passato al codice JavaScript, il codice JavaScript riceve un `null`valore di. Per modificare i riferimenti agli elementi dopo che il componente ha terminato il rendering (per impostare lo stato attivo iniziale su un elemento), usare i metodi del ciclo di vita dei [componenti OnAfterRenderAsync o OnAfterRender](xref:blazor/lifecycle#after-component-render).

Quando si utilizzano tipi generici e si restituisce un valore, utilizzare [\<ValueTask T>](xref:System.Threading.Tasks.ValueTask`1):

```csharp
public static ValueTask<T> GenericMethod<T>(this ElementReference elementRef, 
    IJSRuntime jsRuntime)
{
    return jsRuntime.InvokeAsync<T>(
        "exampleJsFunctions.doSomethingGeneric", elementRef);
}
```

`GenericMethod`viene chiamato direttamente sull'oggetto con un tipo. Nell'esempio seguente si presuppone che `GenericMethod` l'oggetto sia disponibile `JsInteropClasses` dallo spazio dei nomi:

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component3.razor?highlight=17)]

## <a name="reference-elements-across-components"></a>Elementi di riferimento tra i componenti

Un `ElementReference` oggetto è garantito solo valido nel `OnAfterRender` metodo di un componente (e un riferimento a un `struct`elemento è), pertanto non è possibile passare un riferimento a un elemento tra i componenti.

Affinché un componente padre renda disponibile un riferimento a un elemento per altri componenti, il componente padre può:

* Consenti ai componenti figlio di registrare i callback.
* Richiamare i callback registrati durante l' `OnAfterRender` evento con il riferimento all'elemento passato. Indirettamente, questo approccio consente ai componenti figlio di interagire con il riferimento all'elemento padre.

Nell'esempio Blazor di webassembly seguente viene illustrato l'approccio.

`<head>` In *wwwroot/index.html*:

```html
<style>
    .red { color: red }
</style>
```

`<body>` In *wwwroot/index.html*:

```html
<script>
    function setElementClass(element, className) {
        /** @type {HTMLElement} **/
        var myElement = element;
        myElement.classList.add(className);
    }
</script>
```

*Pages/index. Razor* (componente padre):

```razor
@page "/"

<h1 @ref="_title">Hello, world!</h1>

Welcome to your new app.

<SurveyPrompt Parent="this" Title="How is Blazor working for you?" />
```

*Pages/index. Razor. cs*:

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

*Shared/SurveyPrompt. Razor* (componente figlio):

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

*Shared/SurveyPrompt. Razor. cs*:

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

## <a name="harden-js-interop-calls"></a>Chiamate di interoperabilità JS di Harden

L'interoperabilità JS potrebbe non riuscire a causa di errori di rete e deve essere considerata inaffidabile. Per impostazione predefinita, Blazor un'app Server verifica il timeout delle chiamate di interoperabilità js nel server dopo un minuto. Se un'app può tollerare un timeout più aggressivo, ad esempio 10 secondi, impostare il timeout usando uno degli approcci seguenti:

* A livello globale `Startup.ConfigureServices`in specificare il timeout:

  ```csharp
  services.AddServerSideBlazor(
      options => options.JSInteropDefaultCallTimeout = TimeSpan.FromSeconds({SECONDS}));
  ```

* Per chiamata nel codice componente, una singola chiamata può specificare il timeout:

  ```csharp
  var result = await JSRuntime.InvokeAsync<string>("MyJSOperation", 
      TimeSpan.FromSeconds({SECONDS}), new[] { "Arg1" });
  ```

Per ulteriori informazioni sull'esaurimento delle risorse, <xref:security/blazor/server/threat-mitigation>vedere.

[!INCLUDE[Share interop code in a class library](~/includes/blazor-share-interop-code.md)]

## <a name="avoid-circular-object-references"></a>Evitare riferimenti a oggetti circolari

Gli oggetti che contengono riferimenti circolari non possono essere serializzati sul client per uno dei seguenti:

* Chiamate al metodo .NET.
* Chiamate al metodo JavaScript da C# quando il tipo restituito contiene riferimenti circolari.

Per ulteriori informazioni, vedere i seguenti problemi:

* [I riferimenti circolari non sono supportati, accettano due (DotNet/aspnetcore #20525)](https://github.com/dotnet/aspnetcore/issues/20525)
* [Proposta: aggiungere un meccanismo per gestire i riferimenti circolari durante la serializzazione (DotNet/Runtime #30820)](https://github.com/dotnet/runtime/issues/30820)

## <a name="additional-resources"></a>Risorse aggiuntive

* <xref:blazor/call-dotnet-from-javascript>
* [Esempio di InteropComponent. Razor (repository GitHub DotNet/AspNetCore, Branch versione 3,1)](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
* [Eseguire trasferimenti di dati di Blazor grandi dimensioni nelle app Server](xref:blazor/advanced-scenarios#perform-large-data-transfers-in-blazor-server-apps)
