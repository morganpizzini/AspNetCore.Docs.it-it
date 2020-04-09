---
title: Gestire gli errori Blazor nelle app di ASP.NET Core
author: guardrex
description: Scopri come Blazor ASP.NET Blazor Core come gestisce le eccezioni non gestite e come sviluppare app che rilevano e gestiscono gli errori.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/29/2020
no-loc:
- Blazor
- SignalR
uid: blazor/handle-errors
ms.openlocfilehash: 4fdaf7fb90d126b8f7f029aac3af49eec3b69e74
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80382275"
---
# <a name="handle-errors-in-aspnet-core-opno-locblazor-apps"></a>Gestire gli errori Blazor nelle app di ASP.NET Core

Di [Steve Sanderson](https://github.com/SteveSandersonMS)

Questo articolo descrive Blazor come gestisce le eccezioni non gestite e come sviluppare app che rilevano e gestiscono gli errori.

## <a name="detailed-errors-during-development"></a>Errori dettagliati durante lo sviluppo

Quando Blazor un'app non funziona correttamente durante lo sviluppo, la ricezione di informazioni dettagliate sull'errore dall'app consente di risolvere il problema. Quando si verifica Blazor un errore, le app visualizzano una barra d'oro nella parte inferiore dello schermo:

* Durante lo sviluppo, la barra d'oro ti indirizza alla console del browser, dove puoi vedere l'eccezione.
* In produzione, la barra d'oro notifica all'utente che si è verificato un errore e consiglia di aggiornare il browser.

L'interfaccia utente per questa esperienza Blazor di gestione degli errori fa parte dei modelli di progetto.

In Blazor un'app WebAssembly personalizzare l'esperienza nel file *di wwwroot/index.html:*

```html
<div id="blazor-error-ui">
    An unhandled error has occurred.
    <a href="" class="reload">Reload</a>
    <a class="dismiss">🗙</a>
</div>
```

In Blazor un'app Server, personalizza l'esperienza nel file *Pages/_Host.cshtml:*

```cshtml
<div id="blazor-error-ui">
    <environment include="Staging,Production">
        An error has occurred. This application may no longer respond until reloaded.
    </environment>
    <environment include="Development">
        An unhandled exception has occurred. See browser dev tools for details.
    </environment>
    <a href="" class="reload">Reload</a>
    <a class="dismiss">🗙</a>
</div>
```

L'elemento `blazor-error-ui` è nascosto dagli Blazor stili inclusi nei modelli (*wwwroot/css/site.css*) e quindi visualizzato quando si verifica un errore:

```css
#blazor-error-ui {
    background: lightyellow;
    bottom: 0;
    box-shadow: 0 -1px 2px rgba(0, 0, 0, 0.2);
    display: none;
    left: 0;
    padding: 0.6rem 1.25rem 0.7rem 1.25rem;
    position: fixed;
    width: 100%;
    z-index: 1000;
}

#blazor-error-ui .dismiss {
    cursor: pointer;
    position: absolute;
    right: 0.75rem;
    top: 0.5rem;
}
```

## <a name="how-a-opno-locblazor-server-app-reacts-to-unhandled-exceptions"></a>Reazione Blazor di un'app server alle eccezioni non gestiteHow a Server app reacts to unhandled exceptions

BlazorServer è un framework con stato. Mentre gli utenti interagiscono con un'app, mantengono una connessione al server noto come *circuito*. Il circuito contiene istanze del componente attivo, oltre a molti altri aspetti dello stato, ad esempio:

* L'output di rendering più recente dei componenti.
* Set corrente di delegati di gestione degli eventi che potrebbero essere attivati dagli eventi sul lato client.

Se un utente apre l'app in più schede del browser, dispone di più circuiti indipendenti.

Blazorconsidera la maggior parte delle eccezioni non gestite come irreversibili per il circuito in cui si verificano. Se un circuito viene terminato a causa di un'eccezione non gestita, l'utente può continuare a interagire con l'app solo ricaricando la pagina per creare un nuovo circuito. I circuiti al di fuori di quello che è terminato, che sono circuiti per altri utenti o altre schede del browser, non sono interessati. Questo scenario è simile a&mdash;un'app desktop che si arresta in modo anomalo l'app arrestata in modo anomalo deve essere riavviata, ma altre app non sono interessate.

Un circuito viene terminato quando si verifica un'eccezione non gestita per i motivi seguenti:A circuit is terminateed when an unhandled exception occurs for the following reasons:

* Un'eccezione non gestita spesso lascia il circuito in uno stato non definito.
* Il normale funzionamento dell'app non può essere garantito dopo un'eccezione non gestita.
* Le vulnerabilità di sicurezza possono essere visualizzate nell'app se il circuito continua.

## <a name="manage-unhandled-exceptions-in-developer-code"></a>Gestire le eccezioni non gestite nel codice per sviluppatoriManage unhandled exceptions in developer code

Affinché un'app continui dopo un errore, l'app deve disporre della logica di gestione degli errori. Nelle sezioni successive di questo articolo vengono descritte le potenziali origini di eccezioni non gestite.

Nell'ambiente di produzione, non eseguire il rendering dei messaggi di eccezione del framework o delle tracce dello stack nell'interfaccia utente. Il rendering dei messaggi di eccezione o delle tracce dello stack potrebbe:Rendering exception messages or stack traces could:

* Divulgare informazioni riservate agli utenti finali.
* Aiuta un utente malintenzionato a individuare i punti deboli in un'app che possono compromettere la sicurezza dell'app, del server o della rete.

## <a name="log-errors-with-a-persistent-provider"></a>Registrare gli errori con un provider persistenteLog errors with a persistent provider

Se si verifica un'eccezione non <xref:Microsoft.Extensions.Logging.ILogger> gestita, l'eccezione viene registrata nelle istanze configurate nel contenitore dei servizi. Per impostazione predefinita, Blazor le app registrano l'output della console con il provider di registrazione della console. Prendere in considerazione la registrazione in una posizione più permanente con un provider che gestisce le dimensioni del log e la rotazione del log. Per altre informazioni, vedere <xref:fundamentals/logging/index>.

Durante lo Blazor sviluppo, in genere invia i dettagli completi delle eccezioni alla console del browser per facilitare il debug. Nell'ambiente di produzione, gli errori dettagliati nella console del browser sono disabilitati per impostazione predefinita, il che significa che gli errori non vengono inviati ai client, ma i dettagli completi dell'eccezione sono ancora registrati sul lato server. Per altre informazioni, vedere <xref:fundamentals/error-handling>.

È necessario decidere quali eventi imprevisti registrare e il livello di gravità degli eventi imprevisti registrati. Gli utenti ostili potrebbero essere in grado di attivare gli errori deliberatamente. Ad esempio, non registrare un evento imprevisto da un errore in cui viene fornito un sconosciuto `ProductId` nell'URL di un componente che visualizza i dettagli del prodotto. Non tutti gli errori devono essere considerati come incidenti con gravità elevata per la registrazione.

## <a name="places-where-errors-may-occur"></a>Luoghi in cui possono verificarsi errori

Il codice del framework e dell'app può generare eccezioni non gestite in una delle posizioni seguenti:Framework and app code may trigger unhandled exceptions in any of the following locations:

* [Creazione di istanze dei componenti](#component-instantiation)
* [Metodi del ciclo di vita](#lifecycle-methods)
* [Logica di rendering](#rendering-logic)
* [Gestori eventi](#event-handlers)
* [Smaltimento dei componenti](#component-disposal)
* [Interoperabilità JavaScript](#javascript-interop)
* [BlazorRerendering del server](#blazor-server-prerendering)

Le eccezioni non gestite precedenti sono descritte nelle sezioni seguenti di questo articolo.

### <a name="component-instantiation"></a>Creazione di istanze dei componenti

Quando Blazor si crea un'istanza di un componente:

* Viene richiamato il costruttore del componente.
* Vengono richiamati i costruttori di qualsiasi servizio DI non singleton fornito al costruttore del componente tramite la [`@inject`](xref:blazor/dependency-injection#request-a-service-in-a-component) direttiva o l'attributo [`[Inject]`](xref:blazor/dependency-injection#request-a-service-in-a-component) .

Un Blazor circuito Server ha esito negativo quando `[Inject]` qualsiasi costruttore eseguito o un setter per qualsiasi proprietà genera un'eccezione non gestita. L'eccezione è irreversibile perché il framework non può creare un'istanza del componente. Se la logica del costruttore può generare eccezioni, l'app deve intercettare le eccezioni usando un'istruzione [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) con gestione e registrazione degli errori.

### <a name="lifecycle-methods"></a>Metodi del ciclo di vita

Durante la durata di Blazor un componente, richiama i seguenti [metodi del ciclo di vita:](xref:blazor/lifecycle)

* `OnInitialized` / `OnInitializedAsync`
* `OnParametersSet` / `OnParametersSetAsync`
* `ShouldRender` / `ShouldRenderAsync`
* `OnAfterRender` / `OnAfterRenderAsync`

Se un metodo del ciclo di vita genera un'eccezione, Blazor in modo sincrono o asincrono, l'eccezione viene irreversibile per un circuito Server.If any lifecycle method throws an exception, synchronously or asynchronously, the exception is fatal to a Server circuit. Per i componenti per gestire gli errori nei metodi del ciclo di vita, aggiungere la logica di gestione degli errori.

Nell'esempio seguente `OnParametersSetAsync` in cui chiama un metodo per ottenere un prodotto:In the following example where calls a method to obtain a product:

* Un'eccezione `ProductRepository.GetProductByIdAsync` generata nel metodo `try-catch` viene gestita da un'istruzione.
* Quando `catch` viene eseguito il blocco:
  * `loadFailed`è impostato `true`su , che viene utilizzato per visualizzare un messaggio di errore all'utente.
  * L'errore viene registrato.

[!code-razor[](handle-errors/samples_snapshot/3.x/product-details.razor?highlight=11,27-39)]

### <a name="rendering-logic"></a>Logica di rendering

Il markup dichiarativo in un `.razor` file di `BuildRenderTree`componente viene compilato in un metodo di C , denominato . Quando un componente `BuildRenderTree` esegue il rendering, l'esecuzione e la creazione di una struttura di dati che descrive gli elementi, il testo e i componenti figlio del componente sottoposto a rendering.

La logica di rendering può generare un'eccezione. Un esempio di questo `@someObject.PropertyName` scenario `@someObject` si `null`verifica quando viene valutato ma è . Un'eccezione non gestita generata Blazor dalla logica di rendering è irreversibile per un circuito Server.An unhandled exception thrown by rendering logic is fatal to a Server circuit.

Per evitare un'eccezione di riferimento `null` null nella logica di rendering, verificare la presenza di un oggetto prima di accedere ai relativi membri. Nell'esempio seguente, `person.Address` l'accesso `person.Address` alle `null`proprietà non è accessibile se è :

[!code-razor[](handle-errors/samples_snapshot/3.x/person-example.razor?highlight=1)]

Il codice precedente presuppone `person` che `null`non sia . Spesso, la struttura del codice garantisce l'esito di un oggetto nel momento in cui viene eseguito il rendering del componente. In questi casi, non è necessario `null` controllare la logica di rendering. Nell'esempio precedente, `person` potrebbe essere garantito che esista perché `person` viene creato quando viene creata un'istanza del componente.

### <a name="event-handlers"></a>Gestori eventi

Il codice lato client attiva le chiamate di codice C , quando i gestori eventi vengono creati usando:Client-side code triggers invocations of C ' code when event handlers are created using:

* `@onclick`
* `@onchange`
* Altri `@on...` attributi
* `@bind`

Il codice del gestore eventi potrebbe generare un'eccezione non gestita in questi scenari.

Se un gestore eventi genera un'eccezione non gestita (ad esempio, Blazor una query di database ha esito negativo), l'eccezione viene irreversibile per un circuito Server.If an event handler throws an unhandled exception (for example, a database query fails), the exception is fatal to a Server circuit. Se l'app chiama codice che potrebbe non riuscire per motivi esterni, intercettare le eccezioni usando un'istruzione [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) con la gestione e la registrazione degli errori.

Se il codice utente non intercetta e gestisce l'eccezione, il framework registra l'eccezione e termina il circuito.

### <a name="component-disposal"></a>Smaltimento dei componenti

Un componente può essere rimosso dall'interfaccia utente, ad esempio, perché l'utente è passato a un'altra pagina. Quando un componente <xref:System.IDisposable?displayProperty=fullName> che implementa viene rimosso dall'interfaccia <xref:System.IDisposable.Dispose*> utente, il framework chiama il metodo del componente.

Se il metodo `Dispose` del componente genera un'eccezione non gestita, l'eccezione viene irreversibile per un Blazor circuito Server.If the component's method throws an unhandled exception, the exception is fatal to a Server circuit. Se la logica di eliminazione può generare eccezioni, l'app deve intercettare le eccezioni usando un'istruzione [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) con gestione e registrazione degli errori.

Per ulteriori informazioni sullo <xref:blazor/lifecycle#component-disposal-with-idisposable>smaltimento dei componenti, vedere .

### <a name="javascript-interop"></a>Interoperabilità JavaScript

`IJSRuntime.InvokeAsync<T>`consente al codice .NET di effettuare chiamate asincrone al runtime JavaScript nel browser dell'utente.

Le seguenti condizioni si `InvokeAsync<T>`applicano alla gestione degli errori con:

* Se una `InvokeAsync<T>` chiamata a ha esito negativo in modo sincrono, si verifica un'eccezione .NET. Una chiamata `InvokeAsync<T>` a potrebbe non riuscire, ad esempio, perché gli argomenti forniti non possono essere serializzati. Il codice dello sviluppatore deve intercettare l'eccezione. Se il codice dell'app in un gestore eventi o nel metodo Blazor del ciclo di vita del componente non gestisce un'eccezione, l'eccezione risultante viene irreversibile per un circuito Server.If app code in an event handler or component lifecycle method doesn't handle an exception, the resulting exception is fatal to a Server circuit.
* Se una `InvokeAsync<T>` chiamata a ha esito negativo in modo asincrono, .NET <xref:System.Threading.Tasks.Task> ha esito negativo. Una chiamata `InvokeAsync<T>` a potrebbe non riuscire, ad esempio, perché il codice `Promise` lato `rejected`JavaScript genera un'eccezione o restituisce un oggetto che viene completato come . Il codice dello sviluppatore deve intercettare l'eccezione. Se si usa l'operatore [await,](/dotnet/csharp/language-reference/keywords/await) è consigliabile eseguire il wrapping della chiamata al metodo in un'istruzione [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) con la gestione e la registrazione degli errori. In caso contrario, il codice non riuscito genera Blazor un'eccezione non gestita irreversibile per un circuito Server.Otherwise, the failing code results in an unhandled exception that's fatal to a Server circuit.
* Per impostazione `InvokeAsync<T>` predefinita, le chiamate a devono essere completate entro un determinato periodo, altrimenti la chiamata scade. Il periodo di timeout predefinito è un minuto. Il timeout protegge il codice da una perdita di connettività di rete o codice JavaScript che non restituisce mai un messaggio di completamento. Se la chiamata scade, l'operazione risultante `Task` ha esito negativo con un file <xref:System.OperationCanceledException>. Trap ed elaborare l'eccezione con la registrazione.

Analogamente, il codice JavaScript può avviare [`[JSInvokable]`](xref:blazor/call-dotnet-from-javascript) chiamate ai metodi .NET indicati dall'attributo . Se questi metodi .NET generano un'eccezione non gestita:

* L'eccezione non viene considerata Blazor irreversibile per un circuito Server.
* Il lato `Promise` JavaScript viene rifiutato.

È possibile utilizzare il codice di gestione degli errori sul lato .NET o sul lato JavaScript della chiamata al metodo.

Per altre informazioni, vedere gli articoli seguenti:

* <xref:blazor/call-javascript-from-dotnet>
* <xref:blazor/call-dotnet-from-javascript>

### <a name="opno-locblazor-server-prerendering"></a>BlazorPrerendering del server

Blazori componenti possono essere sottoposti a prerendering utilizzando [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) in modo che il markup HTML di cui è stato eseguito il rendering venga restituito come parte della richiesta HTTP iniziale dell'utente. Questo funziona da:

* Creazione di un nuovo circuito per tutti i componenti prerenderizzati che fanno parte della stessa pagina.
* Generazione del codice HTML iniziale.
* Trattare il `disconnected` circuito come fino a quando SignalR il browser dell'utente stabilisce una connessione allo stesso server. Quando viene stabilita la connessione, l'interattività sul circuito viene ripresa e il markup HTML dei componenti viene aggiornato.

Se un componente genera un'eccezione non gestita durante il prerendering, ad esempio, durante un metodo del ciclo di vita o nella logica di rendering:If any component throws an unhandled exception during prerendering, for example, during a lifecycle method or in rendering logic:

* L'eccezione è irreversibile per il circuito.
* L'eccezione viene generata `Component` nello stack di chiamate dall'helper tag. Pertanto, l'intera richiesta HTTP ha esito negativo a meno che l'eccezione viene rilevata in modo esplicito dal codice dello sviluppatore.

In circostanze normali in cui il prerendering non riesce, continuare a compilare e eseguire il rendering del componente non ha senso perché non è possibile eseguire il rendering di un componente funzionante.

Per tollerare gli errori che possono verificarsi durante il prerendering, la logica di gestione degli errori deve essere inserita all'interno di un componente che può generare eccezioni. Usare le istruzioni [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) con la gestione e la registrazione degli errori. Anziché eseguire `Component` il wrapping `try-catch` di Tag Helper in un'istruzione, inserire la logica di gestione degli errori nel componente di cui è stato eseguito il `Component` rendering da Tag Helper.

## <a name="advanced-scenarios"></a>Scenari avanzati

### <a name="recursive-rendering"></a>Rendering ricorsivo

I componenti possono essere nidificati in modo ricorsivo. Ciò è utile per rappresentare strutture di dati ricorsive. Ad esempio, `TreeNode` un componente `TreeNode` può eseguire il rendering di più componenti per ognuno degli elementi figlio del nodo.

Quando si esegue il rendering in modo ricorsivo, evitare modelli di codifica che generano una ricorsione infinita:When rendering recursively, avoid coding patterns that result in infinite recursion:

* Non eseguire il rendering ricorsivo di una struttura di dati che contiene un ciclo. Ad esempio, non eseguire il rendering di un nodo della struttura ad albero i cui elementi figlio includono se stesso.
* Non creare una catena di layout che contengono un ciclo. Ad esempio, non creare un layout il cui layout è se stesso.
* Non consentire a un utente finale di violare le invarianti di ricorsione (regole) tramite l'immissione di dati dannosi o chiamate di interoperabilità JavaScript.

Cicli infiniti durante il rendering:

* Fa sì che il processo di rendering continui per sempre.
* Equivale alla creazione di un ciclo non terminato.

In questi scenari, Blazor un circuito Server interessato ha esito negativo e il thread tenta in genere di:In these scenarios, an affected Server circuit fails, and the thread usually attempts to:

* Consumare quanto tempo della CPU consentito dal sistema operativo, a tempo indeterminato.
* Consumare una quantità illimitata di memoria del server. L'utilizzo di memoria illimitata equivale allo scenario in cui un ciclo senza terminazione aggiunge voci a una raccolta a ogni iterazione.

Per evitare modelli di ricorsione infinita, assicurarsi che il codice di rendering ricorsivo contenga condizioni di arresto adatte.

### <a name="custom-render-tree-logic"></a>Logica dell'albero di rendering personalizzata

La Blazor maggior parte dei componenti vengono implementati come file con `RenderTreeBuilder` estensione *razor* e vengono compilati per produrre la logica che opera su un per eseguire il rendering dell'output. Uno sviluppatore `RenderTreeBuilder` può implementare manualmente la logica utilizzando il codice procedurale di C. Per altre informazioni, vedere <xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic>.

> [!WARNING]
> L'uso della logica del generatore di albero di rendering manuale è considerato uno scenario avanzato e non sicuro, non consigliato per lo sviluppo di componenti generali.

Se `RenderTreeBuilder` viene scritto codice, lo sviluppatore deve garantire la correttezza del codice. Ad esempio, lo sviluppatore deve assicurarsi che:For example, the developer must ensure that:

* Le `OpenElement` chiamate `CloseElement` a e sono bilanciate correttamente.
* Gli attributi vengono aggiunti solo nelle posizioni corrette.

Una logica non corretta del generatore di albero di rendering manuale può causare un comportamento arbitrario non definito, inclusi arresti anomali, blocchi del server e vulnerabilità della sicurezza.

Prendere in considerazione la logica del generatore di albero di rendering manuale sullo stesso livello di complessità e con lo stesso livello di *pericolo* della scrittura manuale del codice assembly o delle istruzioni MSIL.
