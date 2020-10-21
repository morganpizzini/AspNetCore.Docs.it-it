---
title: Chiamare funzioni JavaScript da metodi .NET in ASP.NET Core Blazor
author: guardrex
description: Informazioni su come richiamare funzioni JavaScript da metodi .NET nelle Blazor app.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/02/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/call-javascript-from-dotnet
ms.openlocfilehash: 3bd881b124e00b91ab0aa9d3eb7531f10ef895f2
ms.sourcegitcommit: b5ebaf42422205d212e3dade93fcefcf7f16db39
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92326502"
---
# <a name="call-javascript-functions-from-net-methods-in-aspnet-core-no-locblazor"></a>Chiamare funzioni JavaScript da metodi .NET in ASP.NET Core Blazor

Di [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27)e [Luke Latham](https://github.com/guardrex)

Un' Blazor app può richiamare funzioni JavaScript da metodi .NET e metodi .NET da funzioni JavaScript. Questi scenari sono detti *interoperabilità JavaScript (interoperabilità* *JS*).

Questo articolo illustra come richiamare funzioni JavaScript da .NET. Per informazioni su come chiamare i metodi .NET da JavaScript, vedere <xref:blazor/call-dotnet-from-javascript> .

[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([procedura per il download](xref:index#how-to-download-a-sample))

Per chiamare JavaScript da .NET, usare l' <xref:Microsoft.JSInterop.IJSRuntime> astrazione. Per rilasciare chiamate di interoperabilità JS, inserire l' <xref:Microsoft.JSInterop.IJSRuntime> astrazione nel componente. <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> accetta un identificatore per la funzione JavaScript che si vuole richiamare insieme a un numero qualsiasi di argomenti serializzabili in JSON. L'identificatore della funzione è relativo all'ambito globale ( `window` ). Se si desidera chiamare `window.someScope.someFunction` , l'identificatore è `someScope.someFunction` . Non è necessario registrare la funzione prima che venga chiamata. Il tipo restituito `T` deve anche essere serializzabile in JSON. `T` deve corrispondere al tipo .NET che meglio esegue il mapping al tipo JSON restituito.

Le funzioni JavaScript che restituiscono una [promessa](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) vengono chiamate con <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> . `InvokeAsync` rimuove il wrapping della promessa e restituisce il valore atteso dalla promessa.

Per Blazor Server le app con il prerendering abilitato, non è possibile chiamare JavaScript durante il prerendering iniziale. È necessario rinviare le chiamate di interoperabilità JavaScript finché non viene stabilita la connessione con il browser. Per altre informazioni, vedere la sezione [rilevare quando un' Blazor Server app è prerendering](#detect-when-a-blazor-server-app-is-prerendering) .

L'esempio seguente è basato su [`TextDecoder`](https://developer.mozilla.org/docs/Web/API/TextDecoder) , un decodificatore basato su JavaScript. Nell'esempio viene illustrato come richiamare una funzione JavaScript da un metodo C# che Scarica un requisito dal codice dello sviluppatore a un'API JavaScript esistente. La funzione JavaScript accetta una matrice di byte da un metodo C#, decodifica la matrice e restituisce il testo al componente per la visualizzazione.

All'interno dell' `<head>` elemento di `wwwroot/index.html` ( Blazor WebAssembly ) o `Pages/_Host.cshtml` ( Blazor Server ), fornire una funzione JavaScript che usi `TextDecoder` per decodificare una matrice passata e restituire il valore decodificato:

[!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-convertarray.html)]

Il codice JavaScript, ad esempio il codice illustrato nell'esempio precedente, può anche essere caricato da un file JavaScript ( `.js` ) con un riferimento al file script:

```html
<script src="exampleJsInterop.js"></script>
```

Il componente seguente:

* Richiama la `convertArray` funzione JavaScript usando `JSRuntime` quando viene selezionato un pulsante del componente ( **`Convert Array`** ).
* Dopo la chiamata della funzione JavaScript, la matrice passata viene convertita in una stringa. La stringa viene restituita al componente per la visualizzazione.

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/call-js-example.razor?highlight=2,34-35)]

## <a name="ijsruntime"></a>IJSRuntime

Per usare l' <xref:Microsoft.JSInterop.IJSRuntime> astrazione, adottare uno degli approcci seguenti:

* Inserire l' <xref:Microsoft.JSInterop.IJSRuntime> astrazione nel Razor componente ( `.razor` ):

  [!code-razor[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction.razor?highlight=1)]

  All'interno dell' `<head>` elemento di `wwwroot/index.html` ( Blazor WebAssembly ) o `Pages/_Host.cshtml` ( Blazor Server ) specificare una `handleTickerChanged` funzione JavaScript. La funzione viene chiamata con <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> e non restituisce un valore:

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged1.html)]

* Inserire l' <xref:Microsoft.JSInterop.IJSRuntime> astrazione in una classe ( `.cs` ):

  [!code-csharp[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction-class.cs?highlight=5)]

  All'interno dell' `<head>` elemento di `wwwroot/index.html` ( Blazor WebAssembly ) o `Pages/_Host.cshtml` ( Blazor Server ) specificare una `handleTickerChanged` funzione JavaScript. La funzione viene chiamata con `JSRuntime.InvokeAsync` e restituisce un valore:

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged2.html)]

* Per la generazione di contenuto dinamico con [BuildRenderTree](xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic), usare l' `[Inject]` attributo:

  ```razor
  [Inject]
  IJSRuntime JSRuntime { get; set; }
  ```

Nell'app di esempio lato client che accompagna questo argomento sono disponibili due funzioni JavaScript per l'app che interagiscono con il DOM per ricevere l'input dell'utente e visualizzare un messaggio di benvenuto:

* `showPrompt`: Genera un messaggio di richiesta per accettare l'input dell'utente (il nome dell'utente) e restituisce il nome al chiamante.
* `displayWelcome`: Assegna un messaggio di benvenuto dal chiamante a un oggetto DOM con un valore `id` di `welcome` .

`wwwroot/exampleJsInterop.js`:

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=2-7)]

Inserire il `<script>` tag che fa riferimento al file JavaScript nel file `wwwroot/index.html` ( Blazor WebAssembly ) o nel `Pages/_Host.cshtml` file ( Blazor Server ).

`wwwroot/index.html` (Blazor WebAssembly):

[!code-html[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/index.html?highlight=22)]

`Pages/_Host.cshtml` (Blazor Server):

[!code-cshtml[](./common/samples/3.x/BlazorServerSample/Pages/_Host.cshtml?highlight=35)]

Non inserire un `<script>` tag in un file di componente perché il `<script>` tag non può essere aggiornato dinamicamente.

I metodi .NET interoperano con le funzioni JavaScript nel `exampleJsInterop.js` file chiamando <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType> .

L' <xref:Microsoft.JSInterop.IJSRuntime> astrazione è asincrona per consentire gli Blazor Server scenari. Se l'app è un' Blazor WebAssembly app e si vuole richiamare una funzione JavaScript in modo sincrono, <xref:Microsoft.JSInterop.IJSInProcessRuntime> abbandono e chiamano <xref:Microsoft.JSInterop.IJSInProcessRuntime.Invoke%2A> invece. È consigliabile che la maggior parte delle librerie di interoperabilità JS usi le API asincrone per assicurarsi che le librerie siano disponibili in tutti gli scenari.

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> Per abilitare l'isolamento JavaScript nei [moduli JavaScript](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules)standard, vedere la sezione [ Blazor riferimenti a oggetti e isolamento JavaScript](#blazor-javascript-isolation-and-object-references) .

::: moniker-end

L'app di esempio include un componente per dimostrare l'interoperabilità JS. Il componente:

* Riceve l'input dell'utente tramite un prompt di JavaScript.
* Restituisce il testo al componente per l'elaborazione.
* Chiama una seconda funzione JavaScript che interagisce con il DOM per visualizzare un messaggio di benvenuto.

`Pages/JsInterop.razor`:

```razor
@page "/JSInterop"
@using {APP ASSEMBLY}.JsInteropClasses
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

Il segnaposto `{APP ASSEMBLY}` è il nome dell'assembly dell'app (ad esempio, `BlazorSample` ).

1. Quando `TriggerJsPrompt` viene eseguito selezionando il pulsante del componente **`Trigger JavaScript Prompt`** , `showPrompt` viene chiamata la funzione JavaScript fornita nel `wwwroot/exampleJsInterop.js` file.
1. La `showPrompt` funzione accetta l'input dell'utente (il nome dell'utente), che è codificato in HTML e restituito al componente. Il componente archivia il nome dell'utente in una variabile locale, `name` .
1. La stringa archiviata in `name` viene incorporata in un messaggio di benvenuto, che viene passato a una funzione JavaScript, `displayWelcome` , che esegue il rendering del messaggio di benvenuto in un tag di intestazione.

## <a name="call-a-void-javascript-function"></a>Chiamare una funzione JavaScript void

Utilizzare <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> per gli elementi seguenti:

* Funzioni JavaScript che restituiscono [void (0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) o [undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined).
* Se .NET non è necessario per leggere il risultato di una chiamata a JavaScript.

## <a name="detect-when-a-no-locblazor-server-app-is-prerendering"></a>Rileva quando viene eseguito il Blazor Server prerendering di un'app
 
[!INCLUDE[](~/includes/blazor-prerendering.md)]

## <a name="capture-references-to-elements"></a>Acquisisci riferimenti a elementi

Alcuni scenari di interoperabilità JS richiedono riferimenti agli elementi HTML. Ad esempio, una libreria dell'interfaccia utente può richiedere un riferimento a un elemento per l'inizializzazione o potrebbe essere necessario chiamare API simili a quelle di un elemento, ad esempio `focus` o `play` .

Acquisire i riferimenti agli elementi HTML in un componente usando l'approccio seguente:

* Aggiungere un `@ref` attributo all'elemento HTML.
* Definire un campo di tipo <xref:Microsoft.AspNetCore.Components.ElementReference> il cui nome corrisponde al valore dell' `@ref` attributo.

Nell'esempio seguente viene illustrata l'acquisizione di un riferimento all' `username` `<input>` elemento:

```razor
<input @ref="username" ... />

@code {
    ElementReference username;
}
```

> [!WARNING]
> Usare solo un riferimento a un elemento per mutare il contenuto di un elemento vuoto che non interagisce con Blazor . Questo scenario è utile quando un'API di terze parti fornisce contenuto all'elemento. Poiché Blazor non interagisce con l'elemento, non è possibile che si verifichi un conflitto tra Blazor la rappresentazione dell'elemento e il Dom.
>
> Nell'esempio seguente è *pericoloso* modificare il contenuto dell'elenco non ordinato ( `ul` ) perché Blazor interagisce con il Dom per popolare gli elementi elenco di questo elemento ( `<li>` ):
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
> Se l'interoperabilità JS modifica il contenuto dell'elemento `MyList` e Blazor tenta di applicare differenze all'elemento, le differenze non corrispondono al Dom.

Per quanto riguarda il codice .NET, un <xref:Microsoft.AspNetCore.Components.ElementReference> è un handle opaco. L' *unica* cosa che è possibile fare con <xref:Microsoft.AspNetCore.Components.ElementReference> è passarla al codice JavaScript tramite l'interoperabilità js. Quando si esegue questa operazione, il codice sul lato JavaScript riceve un' `HTMLElement` istanza, che può essere usata con le API DOM normali.

Il codice seguente, ad esempio, definisce un metodo di estensione .NET che consente di impostare lo stato attivo su un elemento:

`exampleJsInterop.js`:

```javascript
window.exampleJsFunctions = {
  focusElement : function (element) {
    element.focus();
  }
}
```

Per chiamare una funzione JavaScript che non restituisce un valore, usare <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> . Il codice seguente imposta lo stato attivo sull'input del nome utente chiamando la funzione JavaScript precedente con l'acquisizione <xref:Microsoft.AspNetCore.Components.ElementReference> :

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component1.razor?highlight=1,3,11-12)]

Per usare un metodo di estensione, creare un metodo di estensione statico che riceva l' <xref:Microsoft.JSInterop.IJSRuntime> istanza:

```csharp
public static async Task Focus(this ElementReference elementRef, IJSRuntime jsRuntime)
{
    await jsRuntime.InvokeVoidAsync(
        "exampleJsFunctions.focusElement", elementRef);
}
```

Il `Focus` metodo viene chiamato direttamente nell'oggetto. Nell'esempio seguente si presuppone che il `Focus` metodo sia disponibile dallo `JsInteropClasses` spazio dei nomi:

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component2.razor?highlight=1-4,12)]

> [!IMPORTANT]
> La `username` variabile viene popolata solo dopo il rendering del componente. Se un oggetto non popolato <xref:Microsoft.AspNetCore.Components.ElementReference> viene passato al codice JavaScript, il codice JavaScript riceve un valore di `null` . Per modificare i riferimenti agli elementi dopo che il componente ha terminato il rendering (per impostare lo stato attivo iniziale su un elemento), usare i metodi del ciclo di vita dei [ `OnAfterRenderAsync` `OnAfterRender` componenti o](xref:blazor/components/lifecycle#after-component-render).

Quando si utilizzano tipi generici e si restituisce un valore, utilizzare <xref:System.Threading.Tasks.ValueTask%601> :

```csharp
public static ValueTask<T> GenericMethod<T>(this ElementReference elementRef, 
    IJSRuntime jsRuntime)
{
    return jsRuntime.InvokeAsync<T>(
        "exampleJsFunctions.doSomethingGeneric", elementRef);
}
```

`GenericMethod` viene chiamato direttamente sull'oggetto con un tipo. Nell'esempio seguente si presuppone che l'oggetto `GenericMethod` sia disponibile dallo `JsInteropClasses` spazio dei nomi:

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component3.razor?highlight=17)]

## <a name="reference-elements-across-components"></a>Elementi di riferimento tra i componenti

Un' <xref:Microsoft.AspNetCore.Components.ElementReference> istanza è garantita solo come valida nel metodo di un componente <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> (e un riferimento a un elemento è `struct` ), pertanto non è possibile passare un riferimento a un elemento tra i componenti. Affinché un componente padre renda disponibile un riferimento a un elemento per altri componenti, il componente padre può:

* Consenti ai componenti figlio di registrare i callback.
* Richiamare i callback registrati durante l' <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> evento con il riferimento all'elemento passato. Indirettamente, questo approccio consente ai componenti figlio di interagire con il riferimento all'elemento padre.

Nell' Blazor WebAssembly esempio seguente viene illustrato l'approccio.

Nella `<head>` di `wwwroot/index.html` :

```html
<style>
    .red { color: red }
</style>
```

Nella `<body>` di `wwwroot/index.html` :

```html
<script>
    function setElementClass(element, className) {
        /** @type {HTMLElement} **/
        var myElement = element;
        myElement.classList.add(className);
    }
</script>
```

`Pages/Index.razor` (componente padre):

```razor
@page "/"

<h1 @ref="title">Hello, world!</h1>

Welcome to your new app.

<SurveyPrompt Parent="this" Title="How is Blazor working for you?" />
```

`Pages/Index.razor.cs`:

```csharp
using System;
using System.Collections.Generic;
using Microsoft.AspNetCore.Components;

namespace {APP ASSEMBLY}.Pages
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

Il segnaposto `{APP ASSEMBLY}` è il nome dell'assembly dell'app (ad esempio, `BlazorSample` ).

`Shared/SurveyPrompt.razor` (componente figlio):

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

`Shared/SurveyPrompt.razor.cs`:

```csharp
using System;
using Microsoft.AspNetCore.Components;

namespace {APP ASSEMBLY}.Shared
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

Il segnaposto `{APP ASSEMBLY}` è il nome dell'assembly dell'app (ad esempio, `BlazorSample` ).

## <a name="harden-js-interop-calls"></a>Chiamate di interoperabilità JS di Harden

L'interoperabilità JS potrebbe non riuscire a causa di errori di rete e deve essere considerata inaffidabile. Per impostazione predefinita, un' Blazor Server app timeout delle chiamate di interoperabilità js nel server dopo un minuto. Se un'app può tollerare un timeout più aggressivo, impostare il timeout usando uno degli approcci seguenti:

* A livello globale in `Startup.ConfigureServices` specificare il timeout:

  ```csharp
  services.AddServerSideBlazor(
      options => options.JSInteropDefaultCallTimeout = TimeSpan.FromSeconds({SECONDS}));
  ```

* Per chiamata nel codice componente, una singola chiamata può specificare il timeout:

  ```csharp
  var result = await JSRuntime.InvokeAsync<string>("MyJSOperation", 
      TimeSpan.FromSeconds({SECONDS}), new[] { "Arg1" });
  ```

Per ulteriori informazioni sull'esaurimento delle risorse, vedere <xref:blazor/security/server/threat-mitigation> .

[!INCLUDE[](~/includes/blazor-share-interop-code.md)]

## <a name="avoid-circular-object-references"></a>Evitare riferimenti a oggetti circolari

Gli oggetti che contengono riferimenti circolari non possono essere serializzati sul client per uno dei seguenti:

* Chiamate al metodo .NET.
* Chiamate al metodo JavaScript da C# quando il tipo restituito contiene riferimenti circolari.

Per ulteriori informazioni, vedere i seguenti problemi:

* [I riferimenti circolari non sono supportati, accettano due (DotNet/aspnetcore #20525)](https://github.com/dotnet/aspnetcore/issues/20525)
* [Proposta: aggiungere un meccanismo per gestire i riferimenti circolari durante la serializzazione (DotNet/Runtime #30820)](https://github.com/dotnet/runtime/issues/30820)

::: moniker range=">= aspnetcore-5.0"

## <a name="no-locblazor-javascript-isolation-and-object-references"></a>Blazor Isolamento JavaScript e riferimenti a oggetti

Blazor Abilita l'isolamento JavaScript nei [moduli JavaScript](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules)standard. L'isolamento JavaScript offre i vantaggi seguenti:

* Il codice JavaScript importato non inquina più lo spazio dei nomi globale.
* I consumer di una libreria e i componenti non sono necessari per importare il codice JavaScript correlato.

Il modulo JavaScript seguente, ad esempio, esporta una funzione JavaScript per visualizzare un prompt del browser:

```javascript
export function showPrompt(message) {
  return prompt(message, 'Type anything here');
}
```

Aggiungere il modulo JavaScript precedente a una libreria .NET come asset Web statico ( `wwwroot/exampleJsInterop.js` ) e quindi importare il modulo nel codice .NET usando il <xref:Microsoft.JSInterop.IJSRuntime> servizio. Il servizio viene inserito come `jsRuntime` (non visualizzato) per l'esempio seguente:

```csharp
var module = await jsRuntime.InvokeAsync<IJSObjectReference>(
    "import", "./_content/MyComponents/exampleJsInterop.js");
```

L' `import` identificatore nell'esempio precedente è un identificatore speciale usato in modo specifico per l'importazione di un modulo JavaScript. Specificare il modulo usando il percorso di asset Web statico stabile: `_content/{LIBRARY NAME}/{PATH UNDER WWWROOT}` . Il segnaposto `{LIBRARY NAME}` è il nome della libreria. Il segnaposto `{PATH UNDER WWWROOT}` è il percorso dello script in `wwwroot` .

<xref:Microsoft.JSInterop.IJSRuntime> importa il modulo come `IJSObjectReference` , che rappresenta un riferimento a un oggetto JavaScript dal codice .NET. Usare `IJSObjectReference` per richiamare le funzioni JavaScript esportate dal modulo:

```csharp
public async ValueTask<string> Prompt(string message)
{
    return await module.InvokeAsync<string>("showPrompt", message);
}
```

`IJSInProcessObjectReference` rappresenta un riferimento a un oggetto JavaScript le cui funzioni possono essere richiamate in modo sincrono.

`IJSUnmarshalledObjectReference` rappresenta un riferimento a un oggetto JavaScript le cui funzioni possono essere richiamate senza l'overhead della serializzazione dei dati .NET. Questa operazione può essere utilizzata in Blazor WebAssembly quando le prestazioni sono fondamentali:

```javascript
window.unmarshalledInstance = {
  helloWorld: function (personNamePointer) {
    const personName = Blazor.platform.readStringField(value, 0);
    return `Hello ${personName}`;
  }
};
```

```csharp
var unmarshalledRuntime = (IJSUnmarshalledRuntime)jsRuntime;
var jsUnmarshalledReference = unmarshalledRuntime
    .InvokeUnmarshalled<IJSUnmarshalledObjectReference>("unmarshalledInstance");

string helloWorldString = jsUnmarshalledReference.InvokeUnmarshalled<string, string>(
    "helloWorld");
```

## <a name="use-of-javascript-libraries-that-render-ui-dom-elements"></a>Uso di librerie JavaScript che eseguono il rendering dell'interfaccia utente (elementi DOM)

In alcuni casi può essere utile usare librerie JavaScript che producono elementi dell'interfaccia utente visibili all'interno del DOM del browser. A prima vista, questo potrebbe sembrare difficile perché il Blazor sistema di differenziazione si basa sul controllo della struttura ad albero di elementi DOM e si verificano errori se il codice esterno muta l'albero DOM e invalida il meccanismo per l'applicazione di diff. Questa limitazione non è Blazor specifica. Lo stesso problema si verifica con qualsiasi framework dell'interfaccia utente basato su diff.

Fortunatamente, è facile incorporare in modo affidabile l'interfaccia utente generata dall'esterno all'interno di un' Blazor interfaccia utente del componente. La tecnica consigliata consiste nel fare in modo che il codice del componente ( `.razor` file) produca un elemento vuoto. Per quanto Blazor riguarda il sistema diffing, l'elemento è sempre vuoto, quindi il renderer non esegue la ricorsione nell'elemento e lascia il proprio contenuto. In questo modo è possibile popolare l'elemento con contenuto arbitrario gestito esternamente.

Nell'esempio seguente viene illustrato il concetto. All'interno dell' `if` istruzione quando `firstRender` è `true` , eseguire un'operazione con `myElement` . Ad esempio, chiamare una libreria JavaScript esterna per popolarla. Blazor lascia solo il contenuto dell'elemento finché questo componente non viene rimosso. Quando il componente viene rimosso, viene rimosso anche l'intero sottoalbero DOM del componente.

```razor
<h1>Hello! This is a Blazor component rendered at @DateTime.Now</h1>

<div @ref="myElement"></div>

@code {
    HtmlElement myElement;
    
    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            ...
        }
    }
}
```

Per un esempio più dettagliato, si consideri il componente seguente che esegue il rendering di una mappa interattiva usando le [API MapBox Open Source](https://www.mapbox.com/):

```razor
@inject IJSRuntime JS
@implements IAsyncDisposable

<div @ref="mapElement" style='width: 400px; height: 300px;'></div>

<button @onclick="() => ShowAsync(51.454514, -2.587910)">Show Bristol, UK</button>
<button @onclick="() => ShowAsync(35.6762, 139.6503)">Show Tokyo, Japan</button>

@code
{
    ElementReference mapElement;
    IJSObjectReference mapModule;
    IJSObjectReference mapInstance;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            mapModule = await JS.InvokeAsync<IJSObjectReference>(
                "import", "./mapComponent.js");
            mapInstance = await mapModule.InvokeAsync<IJSObjectReference>(
                "addMapToElement", mapElement);
        }
    }

    Task ShowAsync(double latitude, double longitude)
        => mapModule.InvokeVoidAsync("setMapCenter", mapInstance, latitude, 
            longitude).AsTask();

    private async ValueTask IAsyncDisposable.DisposeAsync()
    {
        await mapInstance.DisposeAsync();
        await mapModule.DisposeAsync();
    }
}
```

Il modulo JavaScript corrispondente, che deve essere inserito in `wwwroot/mapComponent.js` , è il seguente:

```javascript
import 'https://api.mapbox.com/mapbox-gl-js/v1.12.0/mapbox-gl.js';

// TO MAKE THE MAP APPEAR YOU MUST ADD YOUR ACCESS TOKEN FROM 
// https://account.mapbox.com
mapboxgl.accessToken = '{ACCESS TOKEN}';

export function addMapToElement(element) {
  return new mapboxgl.Map({
    container: element,
    style: 'mapbox://styles/mapbox/streets-v11',
    center: [-74.5, 40],
    zoom: 9
  });
}

export function setMapCenter(map, latitude, longitude) {
  map.setCenter([longitude, latitude]);
}
```

Nell'esempio precedente, sostituire la stringa `{ACCESS TOKEN}` con un token di accesso valido che è possibile ottenere da https://account.mapbox.com .

Per produrre lo stile corretto, aggiungere il seguente tag del foglio di stile alla pagina HTML dell'host ( `index.html` o `_Host.cshtml` ):

```html
<link rel="stylesheet" href="https://api.mapbox.com/mapbox-gl-js/v1.12.0/mapbox-gl.css" />
```

Nell'esempio precedente viene prodotta un'interfaccia utente mappa interattiva, in cui l'utente:

* È possibile trascinare per scorrere o ingrandire.
* Fare clic sui pulsanti per passare a percorsi predefiniti.

![MapBox Street Map di Tokyo, Giappone con i pulsanti per selezionare Bristol, Regno Unito e Tokyo, Giappone](https://user-images.githubusercontent.com/1101362/94939821-92ef6700-04ca-11eb-858e-fff6df0053ae.png)

I punti chiave da comprendere sono:

 * Il `<div>` con `@ref="mapElement"` viene lasciato vuoto per quanto Blazor concerne. È pertanto sicuro per `mapbox-gl.js` compilarlo e modificarne il contenuto nel tempo. Questa tecnica può essere usata con qualsiasi libreria JavaScript che esegua il rendering dell'interfaccia utente. È anche possibile incorporare componenti da un Framework di JavaScript SPA di terze parti all'interno dei Blazor componenti, purché non tenti di raggiungere e modificare altre parti della pagina. *Non* è sicuro che il codice JavaScript esterno modifichi elementi che Blazor non sono considerati vuoti.
 * Quando si usa questo approccio, tenere presente le regole su come Blazor mantiene o Elimina gli elementi DOM. Nell'esempio precedente, il componente gestisce in modo sicuro gli eventi click del pulsante e aggiorna l'istanza della mappa esistente perché gli elementi DOM vengono conservati laddove possibile per impostazione predefinita. Se si esegue il rendering di un elenco di elementi della mappa dall'interno di un `@foreach` ciclo, si desidera utilizzare `@key` per garantire la conservazione delle istanze dei componenti. In caso contrario, le modifiche nei dati dell'elenco potrebbero causare la conservazione dello stato delle istanze precedenti in modo non auspicabile da parte delle istanze dei componenti. Per ulteriori informazioni, vedere [utilizzo @key di per mantenere elementi e componenti](xref:blazor/components/index#use-key-to-control-the-preservation-of-elements-and-components).

Inoltre, l'esempio precedente Mostra come è possibile incapsulare la logica JavaScript e le dipendenze all'interno di un modulo ES6 e caricarlo dinamicamente usando l' `import` identificatore. Per ulteriori informazioni, vedere [isolamento JavaScript e riferimenti a oggetti](#blazor-javascript-isolation-and-object-references).

::: moniker-end

## <a name="size-limits-on-js-interop-calls"></a>Limiti delle dimensioni per le chiamate di interoperabilità JS

In Blazor WebAssembly il Framework non impone limiti per le dimensioni degli input e degli output delle chiamate di interoperabilità js.

In Blazor Server il risultato di una chiamata di interoperabilità js è limitato dalla dimensione massima del payload applicata da SignalR ( <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize> ), il cui valore predefinito è 32 KB. Le applicazioni che tentano di rispondere a una chiamata di interoperabilità JS con un payload maggiore di <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize> generano un errore. È possibile configurare un limite maggiore modificando <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize> . Nell'esempio seguente viene impostata la dimensione massima del messaggio di ricezione su 64 KB (64 * 1024 * 1024):

```csharp
services.AddServerSideBlazor()
   .AddHubOptions(options => options.MaximumReceiveMessageSize = 64 * 1024 * 1024);
```

L'aumento del SignalR limite comporta il costo di richiedere l'utilizzo di più risorse del server ed espone il server a maggiori rischi da parte di utenti malintenzionati. Inoltre, la lettura di una grande quantità di contenuto in memoria come stringhe o matrici di byte può comportare anche l'utilizzo di allocazioni che funzionano in modo non corretto con la Garbage Collector, con conseguente penalizzazione delle prestazioni. Un'opzione per la lettura di payload di grandi dimensioni consiste nel considerare l'invio del contenuto in blocchi più piccoli e l'elaborazione del payload come <xref:System.IO.Stream> . Può essere usato durante la lettura di payload JSON di grandi dimensioni o se i dati sono disponibili in JavaScript come byte non elaborati. Per un esempio che illustra l'invio di payload binari di grandi dimensioni in Blazor Server che usa tecniche simili al `InputFile` componente, vedere l' [app di esempio binario Submit](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/BinarySubmit).

Quando si sviluppa codice che trasferisce una grande quantità di dati tra JavaScript e, tenere presenti le linee guida seguenti Blazor :

* Sezionare i dati in parti più piccole e inviare i segmenti di dati in sequenza fino a quando non vengono ricevuti tutti i dati dal server.
* Non allocare oggetti di grandi dimensioni in codice JavaScript e C#.
* Non bloccare il thread principale dell'interfaccia utente per periodi prolungati durante l'invio o la ricezione di dati.
* Liberare la memoria utilizzata quando il processo viene completato o annullato.
* Per motivi di sicurezza, applicare i seguenti requisiti aggiuntivi:
  * Dichiarare le dimensioni massime di file o dati che è possibile passare.
  * Dichiarare la velocità di caricamento minima dal client al server.
* Dopo la ricezione dei dati da parte del server, i dati possono essere:
  * Archiviati temporaneamente in un buffer di memoria fino a quando non vengono raccolti tutti i segmenti.
  * Utilizzato immediatamente. Ad esempio, i dati possono essere archiviati immediatamente in un database o scritti su disco durante la ricezione di ogni segmento.

## <a name="additional-resources"></a>Risorse aggiuntive

* <xref:blazor/call-dotnet-from-javascript>
* [Esempio di InteropComponent. Razor (repository GitHub DotNet/AspNetCore, Branch versione 3,1)](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
