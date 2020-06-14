---
title: Creazione e utilizzo di Razor componenti ASP.NET Core
author: guardrex
description: Informazioni su come creare e usare Razor i componenti, tra cui la modalità di associazione ai dati, la gestione degli eventi e la gestione dei cicli di vita dei componenti.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/11/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/components
ms.openlocfilehash: 2a6de1a39737f98cb151a0556f36c223d86f9752
ms.sourcegitcommit: d243fadeda20ad4f142ea60301ae5f5e0d41ed60
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/12/2020
ms.locfileid: "84723951"
---
# <a name="create-and-use-aspnet-core-razor-components"></a>Creazione e utilizzo di Razor componenti ASP.NET Core

Di [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27)e [Tobias Bartsch](https://www.aveo-solutions.com/)

[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([procedura per il download](xref:index#how-to-download-a-sample))

Blazorle app vengono compilate usando i *componenti*. Un componente è un blocco di interfaccia utente (UI) autonomo, ad esempio una pagina, una finestra di dialogo o un form. Un componente include il markup HTML e la logica di elaborazione necessaria per inserire i dati o rispondere agli eventi dell'interfaccia utente. I componenti sono flessibili e leggeri. Possono essere annidati, riutilizzati e condivisi tra i progetti.

## <a name="component-classes"></a>Classi di componenti

I componenti vengono implementati in [Razor](xref:mvc/views/razor) file componente (*Razor*) utilizzando una combinazione di markup C# e HTML. Un componente in Blazor viene definito formalmente come * Razor componente*.

Il nome di un componente deve iniziare con un carattere maiuscolo. Ad esempio, *MyCoolComponent. Razor* è valido e *MyCoolComponent. Razor* non è valido.

L'interfaccia utente per un componente viene definita utilizzando HTML. La logica di rendering dinamica (ad esempio, cicli, condizionali, espressioni) viene aggiunta usando una sintassi C# incorporata denominata *Razor* . Quando viene compilata un'app, il markup HTML e la logica di rendering C# vengono convertiti in una classe Component. Il nome della classe generata corrisponde al nome del file.

I membri della classe Component sono definiti in un [`@code`][1] blocco. Nel [`@code`][1] blocco, lo stato del componente (proprietà, campi) viene specificato con i metodi per la gestione degli eventi o per la definizione di altre logiche dei componenti. È consentito più di un [`@code`][1] blocco.

I membri dei componenti possono essere usati come parte della logica di rendering del componente usando espressioni C# che iniziano con `@` . Ad esempio, viene eseguito il rendering di un campo C# anteponendo `@` il nome del campo. Nell'esempio seguente viene valutato ed eseguito il rendering:

* `headingFontStyle`al valore della proprietà CSS per `font-style` .
* `headingText`al contenuto dell' `<h1>` elemento.

```razor
<h1 style="font-style:@headingFontStyle">@headingText</h1>

@code {
    private string headingFontStyle = "italic";
    private string headingText = "Put on your new Blazor!";
}
```

Una volta eseguito il rendering iniziale del componente, il componente rigenera l'albero di rendering in risposta agli eventi. BlazorConfronta quindi il nuovo albero di rendering con quello precedente e applica le modifiche apportate al Document Object Model (DOM) del browser.

I componenti sono classi C# ordinarie e possono essere inseriti in qualsiasi punto all'interno di un progetto. I componenti che producono pagine Web in genere risiedono nella cartella *pages* . I componenti non di pagina vengono spesso inseriti nella cartella *condivisa* o in una cartella personalizzata aggiunta al progetto.

Lo spazio dei nomi di un componente viene in genere derivato dallo spazio dei nomi radice dell'app e dal percorso (cartella) del componente all'interno dell'app. Se lo spazio dei nomi radice dell'app è `BlazorApp` e il componente si trova `Counter` nella cartella *pages* :

* Lo `Counter` spazio dei nomi del componente è `BlazorApp.Pages` .
* Il nome completo del tipo del componente è `BlazorApp.Pages.Counter` .

Per le cartelle personalizzate che contengono componenti, aggiungere un' `using` istruzione al componente padre o al file *_Imports. Razor* dell'app. Nell'esempio seguente vengono resi disponibili i componenti nella cartella dei *componenti* :

```razor
@using BlazorApp.Components
```

In alternativa, è possibile fare riferimento direttamente a un componente:

```razor
<BlazorApp.Components.MyCoolComponent />
```

Per ulteriori informazioni, vedere la sezione [Import Components](#import-components) .

## <a name="razor-syntax"></a>Sintassi Razor

Razori componenti nelle Blazor app utilizzano ampiamente la Razor sintassi. Se non si ha familiarità con il Razor linguaggio di markup, è consigliabile leggere <xref:mvc/views/razor> prima di procedere.

Quando si accede al contenuto sulla Razor sintassi, prestare particolare attenzione alle sezioni seguenti:

* [Direttive](xref:mvc/views/razor#directives): `@` -parole chiave riservate con prefisso che in genere modificano il modo in cui il markup del componente viene analizzato o funzione.
* [Attributi di direttiva](xref:mvc/views/razor#directive-attributes): `@` parole chiave riservate con prefisso che in genere modificano il modo in cui gli elementi componente vengono analizzati o funzionano.

## <a name="static-assets"></a>Asset statici

Blazorsegue la convenzione di ASP.NET Core app che collocano asset statici nella [cartella radice Web del progetto (wwwroot)](xref:fundamentals/index#web-root).

Usare un percorso relativo di base ( `/` ) per fare riferimento alla radice Web per un asset statico. Nell'esempio seguente *logo.png* si trova fisicamente nella cartella *{Project root}/wwwroot/images* :

```razor
<img alt="Company logo" src="/images/logo.png" />
```

Razori componenti **non** supportano la notazione tilde-barra ( `~/` ).

Per informazioni sull'impostazione del percorso di base di un'app, vedere <xref:host-and-deploy/blazor/index#app-base-path> .

## <a name="tag-helpers-arent-supported-in-components"></a>Gli helper tag non sono supportati nei componenti

Gli [Helper Tag](xref:mvc/views/tag-helpers/intro) non sono supportati nei Razor componenti (file*Razor* ). Per fornire funzionalità di tipo Helper tag in Blazor , creare un componente con la stessa funzionalità dell'helper tag e utilizzare il componente.

## <a name="use-components"></a>Usare i componenti

I componenti possono includere altri componenti dichiarando questi ultimi usando la sintassi degli elementi HTML. Il markup per l'uso di un componente è simile a un tag HTML, in cui il nome del tag è il tipo di componente.

Il markup seguente in *index. Razor* esegue il rendering di un' `HeadingComponent` istanza:

```razor
<HeadingComponent />
```

*Components/HeadingComponent. Razor*:

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/HeadingComponent.razor)]

Se un componente contiene un elemento HTML con una prima lettera maiuscola che non corrisponde a un nome di componente, viene emesso un avviso che indica che l'elemento ha un nome imprevisto. L'aggiunta di una [`@using`][2] direttiva per lo spazio dei nomi del componente rende disponibile il componente, che risolve l'avviso.

## <a name="routing"></a>Routing.

Il routing in Blazor viene effettuato fornendo un modello di route a ogni componente accessibile nell'app.

Quando Razor viene compilato un file con una [`@page`][9] direttiva, alla classe generata viene assegnato un oggetto che <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> specifica il modello di route. In fase di esecuzione, il router cerca le classi di componenti con un oggetto <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> ed esegue il rendering di qualsiasi componente con un modello di route corrispondente all'URL richiesto.

```razor
@page "/ParentComponent"

...
```

Per altre informazioni, vedere <xref:blazor/routing>.

## <a name="parameters"></a>Parametri

### <a name="route-parameters"></a>Parametri di route

I componenti possono ricevere parametri di route dal modello di route fornito nella [`@page`][9] direttiva. Il router usa parametri di route per popolare i parametri del componente corrispondente.

*Pages/RouteParameter. Razor*:

[!code-razor[](components/samples_snapshot/RouteParameter.razor?highlight=2,7-8)]

I parametri facoltativi non sono supportati, quindi [`@page`][9] vengono applicate due direttive nell'esempio precedente. Il primo consente la navigazione al componente senza un parametro. La seconda [`@page`][9] direttiva riceve il `{text}` parametro di route e assegna il valore alla `Text` Proprietà.

La sintassi dei parametri *catch-all* ( `*` / `**` ), che acquisisce il percorso tra più limiti di cartella, **non** è supportata nei Razor componenti (*Razor*).

### <a name="component-parameters"></a>Parametri del componente

I componenti possono avere *parametri del componente*, che vengono definiti usando proprietà pubbliche nella classe Component con l' [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) attributo. Usare gli attributi per specificare gli argomenti per un componente nel markup.

*Components/ChildComponent. Razor*:

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=2,11-12)]

Nell'esempio seguente dall'app di esempio, `ParentComponent` imposta il valore della `Title` proprietà dell'oggetto `ChildComponent` .

*Pages/ParentComponent. Razor*:

[!code-razor[](components/samples_snapshot/ParentComponent.razor?highlight=5-6)]

> [!WARNING]
> Non creare componenti che scrivono nei propri *parametri del componente*, usare invece un campo privato. Per ulteriori informazioni, vedere la sezione [non creare componenti che scrivono nella relativa proprietà dei parametri](#dont-create-components-that-write-to-their-own-parameter-properties) .

## <a name="child-content"></a>Contenuto figlio

I componenti possono impostare il contenuto di un altro componente. Il componente di assegnazione fornisce il contenuto tra i tag che specificano il componente di destinazione.

Nell'esempio seguente, `ChildComponent` ha una `ChildContent` proprietà che rappresenta un oggetto <xref:Microsoft.AspNetCore.Components.RenderFragment> , che rappresenta un segmento di interfaccia utente di cui eseguire il rendering. Il valore di `ChildContent` viene posizionato nel markup del componente in cui deve essere eseguito il rendering del contenuto. Il valore di `ChildContent` viene ricevuto dal componente padre e ne viene eseguito il rendering all'interno del pannello bootstrap `panel-body` .

*Components/ChildComponent. Razor*:

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=3,14-15)]

> [!NOTE]
> La proprietà che riceve il <xref:Microsoft.AspNetCore.Components.RenderFragment> contenuto deve essere denominata `ChildContent` per convenzione.

`ParentComponent`Nell'app di esempio può fornire contenuto per il rendering `ChildComponent` di inserendo il contenuto all'interno dei `<ChildComponent>` tag.

*Pages/ParentComponent. Razor*:

[!code-razor[](components/samples_snapshot/ParentComponent.razor?highlight=7-8)]

## <a name="attribute-splatting-and-arbitrary-parameters"></a>Attributo splatting e parametri arbitrari

I componenti possono acquisire ed eseguire il rendering di attributi aggiuntivi oltre ai parametri dichiarati del componente. È possibile acquisire attributi aggiuntivi in un dizionario e quindi *Splatted* su un elemento quando il componente viene sottoposto a rendering usando la [`@attributes`][3] Razor direttiva. Questo scenario è utile quando si definisce un componente che produce un elemento di markup che supporta un'ampia gamma di personalizzazioni. Ad esempio, può essere noioso definire gli attributi separatamente per un `<input>` che supporta molti parametri.

Nell'esempio seguente, il primo `<input>` elemento ( `id="useIndividualParams"` ) USA parametri dei singoli componenti, mentre il secondo `<input>` elemento ( `id="useAttributesDict"` ) usa l'attributo splatting:

```razor
<input id="useIndividualParams"
       maxlength="@Maxlength"
       placeholder="@Placeholder"
       required="@Required"
       size="@Size" />

<input id="useAttributesDict"
       @attributes="InputAttributes" />

@code {
    [Parameter]
    public string Maxlength { get; set; } = "10";

    [Parameter]
    public string Placeholder { get; set; } = "Input placeholder text";

    [Parameter]
    public string Required { get; set; } = "required";

    [Parameter]
    public string Size { get; set; } = "50";

    [Parameter]
    public Dictionary<string, object> InputAttributes { get; set; } =
        new Dictionary<string, object>()
        {
            { "maxlength", "10" },
            { "placeholder", "Input placeholder text" },
            { "required", "required" },
            { "size", "50" }
        };
}
```

Il tipo del parametro deve implementare `IEnumerable<KeyValuePair<string, object>>` con chiavi di stringa. L'uso `IReadOnlyDictionary<string, object>` di è anche un'opzione in questo scenario.

Gli elementi sottoposti a rendering `<input>` usando entrambi gli approcci sono identici:

```html
<input id="useIndividualParams"
       maxlength="10"
       placeholder="Input placeholder text"
       required="required"
       size="50">

<input id="useAttributesDict"
       maxlength="10"
       placeholder="Input placeholder text"
       required="required"
       size="50">
```

Per accettare attributi arbitrari, definire un parametro component usando l' [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) attributo con la <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> proprietà impostata su `true` :

```razor
@code {
    [Parameter(CaptureUnmatchedValues = true)]
    public Dictionary<string, object> InputAttributes { get; set; }
}
```

La <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> proprietà su [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) consente al parametro di trovare la corrispondenza con tutti gli attributi che non corrispondono ad altri parametri. Un componente può definire solo un singolo parametro con <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> . Il tipo di proprietà utilizzato con <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> deve essere assegnabile da `Dictionary<string, object>` con chiavi di stringa. `IEnumerable<KeyValuePair<string, object>>`o `IReadOnlyDictionary<string, object>` sono anche opzioni in questo scenario.

La posizione di [`@attributes`][3] relativa alla posizione degli attributi degli elementi è importante. Quando [`@attributes`][3] Splatted sull'elemento, gli attributi vengono elaborati da destra a sinistra (da ultimo a primo). Si consideri l'esempio seguente di un componente che utilizza un `Child` componente:

*ParentComponent. Razor*:

```razor
<ChildComponent extra="10" />
```

*ChildComponent. Razor*:

```razor
<div @attributes="AdditionalAttributes" extra="5" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

L' `Child` attributo del componente `extra` viene impostato a destra di [`@attributes`][3] . Il `Parent` rendering del componente `<div>` contiene `extra="5"` quando viene passato attraverso l'attributo aggiuntivo perché gli attributi vengono elaborati da destra a sinistra (da ultimo a primo):

```html
<div extra="5" />
```

Nell'esempio seguente, l'ordine di `extra` e [`@attributes`][3] viene invertito nell'oggetto del `Child` componente `<div>` :

*ParentComponent. Razor*:

```razor
<ChildComponent extra="10" />
```

*ChildComponent. Razor*:

```razor
<div extra="5" @attributes="AdditionalAttributes" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

Il rendering `<div>` nel `Parent` componente contiene `extra="10"` quando viene passato tramite l'attributo aggiuntivo:

```html
<div extra="10" />
```

## <a name="capture-references-to-components"></a>Acquisisci riferimenti ai componenti

I riferimenti ai componenti forniscono un modo per fare riferimento a un'istanza del componente in modo da poter emettere comandi per tale istanza, ad esempio `Show` o `Reset` . Per acquisire un riferimento a un componente:

* Aggiungere un [`@ref`][4] attributo al componente figlio.
* Definire un campo con lo stesso tipo del componente figlio.

```razor
<MyLoginDialog @ref="loginDialog" ... />

@code {
    private MyLoginDialog loginDialog;

    private void OnSomething()
    {
        loginDialog.Show();
    }
}
```

Quando viene eseguito il rendering del componente, il `loginDialog` campo viene popolato con l' `MyLoginDialog` istanza del componente figlio. È quindi possibile richiamare i metodi .NET nell'istanza del componente.

> [!IMPORTANT]
> La `loginDialog` variabile viene popolata solo dopo il rendering del componente e l'output include `MyLoginDialog` l'elemento. Fino a quel momento, non c'è niente a cui fare riferimento. Per modificare i riferimenti ai componenti dopo che il componente ha terminato il rendering, usare i [Metodi OnAfterRenderAsync o OnAfterRender](xref:blazor/lifecycle#after-component-render).

Per fare riferimento ai componenti in un ciclo, vedere [Capture References to multiple analogous Child-Components (DotNet/aspnetcore #13358)](https://github.com/dotnet/aspnetcore/issues/13358).

Mentre l'acquisizione di riferimenti ai componenti usa una sintassi simile per l' [acquisizione di riferimenti a elementi](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements), non è una funzionalità di interoperabilità di JavaScript. I riferimenti ai componenti non vengono passati al codice JavaScript. I riferimenti ai componenti vengono usati solo nel codice .NET.

> [!NOTE]
> **Non** usare i riferimenti ai componenti per mutare lo stato dei componenti figlio. Usare invece i normali parametri dichiarativi per passare i dati ai componenti figlio. L'utilizzo di normali parametri dichiarativi restituisce automaticamente i componenti figlio che eseguono il rendering alle ore corrette.

## <a name="invoke-component-methods-externally-to-update-state"></a>Richiama i metodi del componente esternamente per aggiornare lo stato

BlazorUsa un contesto di sincronizzazione ( <xref:System.Threading.SynchronizationContext> ) per applicare un singolo thread logico di esecuzione. I metodi del [ciclo](xref:blazor/lifecycle) di vita di un componente e tutti i callback di evento generati da Blazor vengono eseguiti nel contesto di sincronizzazione.

BlazorIl contesto di sincronizzazione del server tenta di emulare un ambiente a thread singolo in modo che corrisponda strettamente al modello webassembly nel browser, ovvero a thread singolo. In un determinato momento, il lavoro viene eseguito su un solo thread, con l'impressione di un singolo thread logico. Non vengono eseguite contemporaneamente due operazioni.

Nel caso in cui un componente deve essere aggiornato in base a un evento esterno, ad esempio un timer o altre notifiche, usare il `InvokeAsync` metodo, che invia il Blazor contesto di sincronizzazione di. Si consideri ad esempio un *servizio Notifier* che può inviare notifiche a qualsiasi componente in ascolto dello stato aggiornato:

```csharp
public class NotifierService
{
    // Can be called from anywhere
    public async Task Update(string key, int value)
    {
        if (Notify != null)
        {
            await Notify.Invoke(key, value);
        }
    }

    public event Func<string, int, Task> Notify;
}
```

Registrare il `NotifierService` come singletion:

* In Blazor webassembly registrare il servizio in `Program.Main` :

  ```csharp
  builder.Services.AddSingleton<NotifierService>();
  ```

* In Blazor server registrare il servizio in `Startup.ConfigureServices` :

  ```csharp
  services.AddScoped<NotifierService>();
  ```

Usare `NotifierService` per aggiornare un componente:

```razor
@page "/"
@inject NotifierService Notifier
@implements IDisposable

<p>Last update: @lastNotification.key = @lastNotification.value</p>

@code {
    private (string key, int value) lastNotification;

    protected override void OnInitialized()
    {
        Notifier.Notify += OnNotify;
    }

    public async Task OnNotify(string key, int value)
    {
        await InvokeAsync(() =>
        {
            lastNotification = (key, value);
            StateHasChanged();
        });
    }

    public void Dispose()
    {
        Notifier.Notify -= OnNotify;
    }
}
```

Nell'esempio precedente `NotifierService` richiama il metodo del componente `OnNotify` all'esterno del contesto di Blazor sincronizzazione di. `InvokeAsync`viene usato per passare al contesto corretto e accodare un rendering.

## <a name="use-key-to-control-the-preservation-of-elements-and-components"></a>Usare \@ la chiave per controllare la conservazione di elementi e componenti

Quando si esegue il rendering di un elenco di elementi o componenti e gli elementi o i componenti cambiano successivamente, l' Blazor algoritmo diffing deve decidere quali elementi o componenti precedenti possono essere conservati e come eseguire il mapping degli oggetti modello. In genere, questo processo è automatico e può essere ignorato, ma in alcuni casi potrebbe essere necessario controllare il processo.

Prendere in considerazione gli esempi seguenti:

```csharp
@foreach (var person in People)
{
    <DetailsEditor Details="@person.Details" />
}

@code {
    [Parameter]
    public IEnumerable<Person> People { get; set; }
}
```

Il contenuto della `People` raccolta può cambiare con le voci inserite, eliminate o riordinate. Quando viene eseguito il rendering del componente, è `<DetailsEditor>` possibile che il componente venga modificato in modo da ricevere `Details` valori di parametro diversi. Questo può causare un rirendering più complesso del previsto. In alcuni casi, il rirendering può comportare differenze di comportamento visibili, ad esempio lo stato attivo degli elementi persi.

È possibile controllare il processo di mapping con l' [`@key`][5] attributo della direttiva. [`@key`][5]fa in modo che l'algoritmo di diffing garantisca la conservazione di elementi o componenti in base al valore della chiave:

```csharp
@foreach (var person in People)
{
    <DetailsEditor @key="person" Details="@person.Details" />
}

@code {
    [Parameter]
    public IEnumerable<Person> People { get; set; }
}
```

Quando la `People` raccolta viene modificata, l'algoritmo diffing mantiene l'associazione tra `<DetailsEditor>` istanze e `person` istanze:

* Se un oggetto `Person` viene eliminato dall' `People` elenco, solo l' `<DetailsEditor>` istanza corrispondente viene rimossa dall'interfaccia utente. Altre istanze rimangono invariate.
* Se un oggetto viene inserito in una determinata `Person` posizione nell'elenco, viene inserita una nuova istanza in corrispondenza della `<DetailsEditor>` posizione corrispondente. Altre istanze rimangono invariate.
* Se `Person` le voci vengono riordinate, le `<DetailsEditor>` istanze corrispondenti vengono mantenute e nuovamente ordinate nell'interfaccia utente.

In alcuni scenari, l'utilizzo di [`@key`][5] riduce al minimo la complessità del rendering ed evita potenziali problemi con le parti con stato del DOM che cambiano, ad esempio la posizione dello stato attivo.

> [!IMPORTANT]
> Le chiavi sono locali per ogni elemento contenitore o componente. Le chiavi non vengono confrontate globalmente nell'intero documento.

### <a name="when-to-use-key"></a>Quando usare la \@ chiave

In genere, è opportuno usare [`@key`][5] ogni volta che viene eseguito il rendering di un elenco (ad esempio in un blocco [foreach](/dotnet/csharp/language-reference/keywords/foreach-in) ) e un valore appropriato per definire [`@key`][5] .

È anche possibile usare [`@key`][5] per impedire a Blazor di mantenere un sottoalbero di un elemento o di un componente quando un oggetto viene modificato:

```razor
<div @key="currentPerson">
    ... content that depends on currentPerson ...
</div>
```

Se `@currentPerson` viene modificata, la [`@key`][5] direttiva attribute impone Blazor di rimuovere l'intero oggetto `<div>` e i relativi discendenti e di ricompilare il sottoalbero all'interno dell'interfaccia utente con nuovi elementi e componenti. Questo può essere utile se è necessario garantire che lo stato dell'interfaccia utente non venga mantenuto quando viene `@currentPerson` modificato.

### <a name="when-not-to-use-key"></a>Quando non usare la \@ chiave

Si verifica un costo in termini di prestazioni quando si verificano differenze con [`@key`][5] . Il costo delle prestazioni non è elevato, ma è sufficiente specificare solo [`@key`][5] se il controllo delle regole di conservazione degli elementi o dei componenti avvantaggia l'app.

Anche se [`@key`][5] non viene usato, Blazor conserva le istanze di elementi e componenti figlio il più possibile. L'unico vantaggio dell'utilizzo di [`@key`][5] è il controllo sulla *modalità* di mapping delle istanze del modello alle istanze dei componenti mantenute, anziché sull'algoritmo di diffing che seleziona il mapping.

### <a name="what-values-to-use-for-key"></a>Valori da usare per la \@ chiave

In genere, è opportuno fornire uno dei seguenti tipi di valore per [`@key`][5] :

* Istanze di oggetti modello (ad esempio, un' `Person` istanza come nell'esempio precedente). In questo modo si garantisce la conservazione in base all'uguaglianza del riferimento all'oggetto.
* Identificatori univoci, ad esempio valori di chiave primaria di tipo `int` , `string` o `Guid` .

Verificare che i valori usati per non siano in [`@key`][5] conflitto. Se vengono rilevati valori di conflitto nello stesso elemento padre, Blazor genera un'eccezione perché non è in grado di eseguire il mapping deterministico di elementi o componenti precedenti a nuovi elementi o componenti. Utilizzare solo valori distinti, ad esempio istanze di oggetti o valori di chiave primaria.

## <a name="dont-create-components-that-write-to-their-own-parameter-properties"></a>Non creare componenti che scrivono nelle proprie proprietà dei parametri

I parametri vengono sovrascritti nei casi seguenti:

* Viene eseguito il rendering del contenuto di un componente figlio con un oggetto <xref:Microsoft.AspNetCore.Components.RenderFragment> .
* <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>viene chiamato nel componente padre.

I parametri vengono reimpostati perché il componente padre esegue nuovamente il rendering quando <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> viene chiamato il metodo e vengono specificati nuovi valori di parametro per il componente figlio.

Si consideri il `Expander` componente seguente:

* Esegue il rendering del contenuto figlio.
* Consente di visualizzare o disabilitare il contenuto figlio con un parametro component.

```razor
<div @onclick="@Toggle">
    Toggle (Expanded = @Expanded)

    @if (Expanded)
    {
        @ChildContent
    }
</div>

@code {
    [Parameter]
    public bool Expanded { get; set; }

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    private void Toggle()
    {
        Expanded = !Expanded;
    }
}
```

Il `Expander` componente viene aggiunto a un componente padre che può chiamare <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> :

```razor
<Expander Expanded="true">
    <h1>Hello, world!</h1>
</Expander>

<Expander Expanded="true" />

<button @onclick="@(() => StateHasChanged())">
    Call StateHasChanged
</button>
```

Inizialmente, i `Expander` componenti si comportano in modo indipendente quando le proprietà vengono disposte `Expanded` . I componenti figlio gestiscono gli Stati come previsto. Quando <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> viene chiamato nell'elemento padre, il `Expanded` parametro del primo componente figlio viene reimpostato sul valore iniziale ( `true` ). Il valore del secondo `Expander` componente `Expanded` non viene reimpostato perché nel secondo componente non viene eseguito il rendering del contenuto figlio.

Per mantenere lo stato nello scenario precedente, usare un *campo privato* nel `Expander` componente per mantenerne lo stato attivato/disattivato.

Il `Expander` componente seguente:

* Accetta il `Expanded` valore del parametro component dall'elemento padre.
* Assegna il valore del parametro component a un *campo privato* ( `expanded` ) nell' [evento OnInitialized](xref:blazor/lifecycle#component-initialization-methods).
* Usa il campo privato per mantenere lo stato di attivazione/disattivo interno.

```razor
<div @onclick="@Toggle">
    Toggle (Expanded = @expanded)

    @if (expanded)
    {
        @ChildContent
    }
</div>

@code {
    [Parameter]
    public bool Expanded { get; set; }

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    private bool expanded;

    protected override void OnInitialized()
    {
        expanded = Expanded;
    }

    private void Toggle()
    {
        expanded = !expanded;
    }
}
```

## <a name="partial-class-support"></a>Supporto di classi parziali

Razori componenti vengono generati come classi parziali. Razori componenti vengono creati utilizzando uno degli approcci seguenti:

* Il codice C# è definito in un [`@code`][1] blocco con markup HTML e Razor codice in un unico file. Blazori modelli definiscono i Razor componenti usando questo approccio.
* Il codice C# viene inserito in un file code-behind definito come classe parziale.

Nell'esempio seguente viene illustrato il `Counter` componente predefinito con un [`@code`][1] blocco in un'app generata da un Blazor modello. Il markup HTML, Razor il codice e il codice C# si trovano nello stesso file:

*Counter. Razor*:

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;

    void IncrementCount()
    {
        currentCount++;
    }
}
```

Il `Counter` componente può essere creato anche usando un file code-behind con una classe parziale:

*Counter. Razor*:

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>
```

*Counter.Razor.cs*:

```csharp
namespace BlazorApp.Pages
{
    public partial class Counter
    {
        private int currentCount = 0;

        void IncrementCount()
        {
            currentCount++;
        }
    }
}
```

Aggiungere gli spazi dei nomi necessari al file di classe parziale in base alle esigenze. Gli spazi dei nomi tipici usati dai Razor componenti includono:

```csharp
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.Authorization;
using Microsoft.AspNetCore.Components.Forms;
using Microsoft.AspNetCore.Components.Routing;
using Microsoft.AspNetCore.Components.Web;
```

## <a name="specify-a-base-class"></a>Specificare una classe di base

La [`@inherits`][6] direttiva può essere usata per specificare una classe di base per un componente. Nell'esempio seguente viene illustrato come un componente può ereditare una classe base, `BlazorRocksBase` , per fornire le proprietà e i metodi del componente. La classe base deve derivare da <xref:Microsoft.AspNetCore.Components.ComponentBase> .

*Pages/BlazorRocks. Razor*:

```razor
@page "/BlazorRocks"
@inherits BlazorRocksBase

<h1>@BlazorRocksText</h1>
```

*BlazorRocksBase.cs*:

```csharp
using Microsoft.AspNetCore.Components;

namespace BlazorSample
{
    public class BlazorRocksBase : ComponentBase
    {
        public string BlazorRocksText { get; set; } = 
            "Blazor rocks the browser!";
    }
}
```

## <a name="specify-an-attribute"></a>Specificare un attributo

Gli attributi possono essere specificati in Razor componenti con la [`@attribute`][7] direttiva. Nell'esempio seguente viene applicato l' [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attributo alla classe Component:

```razor
@page "/"
@attribute [Authorize]
```

## <a name="import-components"></a>Importa componenti

Lo spazio dei nomi di un componente creato con Razor è basato su (in ordine di priorità):

* [`@namespace`][8]designazione nel Razor markup file (*. Razor*) ( `@namespace BlazorSample.MyNamespace` ).
* Il progetto `RootNamespace` nel file di progetto ( `<RootNamespace>BlazorSample</RootNamespace>` ).
* Il nome del progetto, tratto dal nome file del file di progetto (con*estensione csproj*), e il percorso dalla radice del progetto al componente. Ad esempio, il Framework risolve *{Project root}/pages/index.Razor* (*BlazorSample. csproj*) nello spazio dei nomi `BlazorSample.Pages` . I componenti seguono le regole di associazione dei nomi in C#. Per il `Index` componente in questo esempio, i componenti nell'ambito sono tutti componenti:
  * Nella stessa cartella, *pagine*.
  * Componenti nella radice del progetto che non specificano in modo esplicito uno spazio dei nomi diverso.

I componenti definiti in uno spazio dei nomi diverso vengono introdotti nell'ambito usando Razor la [`@using`][2] direttiva.

Se un altro componente, `NavMenu.razor` , è presente nella cartella *BlazorSample/Shared/* , il componente può essere utilizzato in `Index.razor` con l' [`@using`][2] istruzione seguente:

```razor
@using BlazorSample.Shared

This is the Index page.

<NavMenu></NavMenu>
```

È anche possibile fare riferimento ai componenti usando i relativi nomi completi, che non richiedono la [`@using`][2] direttiva:

```razor
This is the Index page.

<BlazorSample.Shared.NavMenu></BlazorSample.Shared.NavMenu>
```

> [!NOTE]
> La `global::` qualificazione non è supportata.
>
> L'importazione di componenti con istruzioni [using](/dotnet/csharp/language-reference/keywords/using-statement) con alias (ad esempio, `@using Foo = Bar` ) non è supportata.
>
> I nomi parzialmente qualificati non sono supportati. Ad esempio, `@using BlazorSample` l'aggiunta e il riferimento al `NavMenu` componente ( `NavMenu.razor` ) con `<Shared.NavMenu></Shared.NavMenu>` non è supportato.

## <a name="conditional-html-element-attributes"></a>Attributi dell'elemento HTML condizionale

Gli attributi degli elementi HTML vengono sottoposti a rendering in modo condizionale in base al valore .NET. Se il valore è `false` o `null` , l'attributo non viene sottoposto a rendering. Se il valore è `true` , l'attributo viene sottoposto a rendering ridotto a icona.

Nell'esempio seguente, `IsCompleted` determina se `checked` viene eseguito il rendering nel markup dell'elemento:

```razor
<input type="checkbox" checked="@IsCompleted" />

@code {
    [Parameter]
    public bool IsCompleted { get; set; }
}
```

Se `IsCompleted` è `true` , viene eseguito il rendering della casella di controllo come segue:

```html
<input type="checkbox" checked />
```

Se `IsCompleted` è `false` , viene eseguito il rendering della casella di controllo come segue:

```html
<input type="checkbox" />
```

Per altre informazioni, vedere <xref:mvc/views/razor>.

> [!WARNING]
> Alcuni attributi HTML, ad esempio [aria-Pressed](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons), non funzionano correttamente quando il tipo .NET è `bool` . In questi casi, usare un `string` tipo anziché un `bool` .

## <a name="raw-html"></a>HTML non elaborato

Le stringhe vengono in genere sottoposte a rendering usando i nodi di testo DOM, il che significa che qualsiasi markup che può contenere viene ignorato e considerato come testo letterale. Per eseguire il rendering del codice HTML non elaborato, eseguire il wrapping del contenuto HTML in un `MarkupString` valore. Il valore viene analizzato in formato HTML o SVG e inserito nel DOM.

> [!WARNING]
> Il rendering di codice HTML non elaborato costruito da un'origine non attendibile costituisce un rischio per la **sicurezza** e deve essere evitato.

Nell'esempio seguente viene illustrato l'utilizzo del `MarkupString` tipo per aggiungere un blocco di contenuto HTML statico all'output sottoposto a rendering di un componente:

```html
@((MarkupString)myMarkup)

@code {
    private string myMarkup = 
        "<p class='markup'>This is a <em>markup string</em>.</p>";
}
```

## <a name="cascading-values-and-parameters"></a>Parametri e valori di propagazione

In alcuni scenari, non è pratico eseguire il flusso dei dati da un componente predecessore a un componente discendente usando i [parametri del componente](#component-parameters), soprattutto quando sono presenti diversi livelli di componenti. I valori e i parametri di propagazione consentono di risolvere questo problema fornendo un modo pratico per un componente predecessore per fornire un valore a tutti i relativi componenti discendenti. I parametri e i valori di propagazione forniscono anche un approccio per la coordinazione dei componenti.

### <a name="theme-example"></a>Esempio di tema

Nell'esempio seguente dall'app di esempio, la `ThemeInfo` classe specifica le informazioni sul tema per scorrere la gerarchia dei componenti in modo che tutti i pulsanti all'interno di una determinata parte dell'app condividono lo stesso stile.

*UIThemeClasses/themeinfo. cs*:

```csharp
public class ThemeInfo
{
    public string ButtonClass { get; set; }
}
```

Un componente predecessore può fornire un valore di propagazione utilizzando il componente valore di propagazione. Il <xref:Microsoft.AspNetCore.Components.CascadingValue%601> componente esegue il wrapping di un sottoalbero della gerarchia dei componenti e fornisce un singolo valore a tutti i componenti all'interno di tale sottoalbero.

Ad esempio, l'app di esempio specifica le informazioni sul tema ( `ThemeInfo` ) in uno dei layout dell'app come parametro di propagazione per tutti i componenti che costituiscono il corpo del layout della `@Body` Proprietà. `ButtonClass`viene assegnato un valore di `btn-success` nel componente layout. Qualsiasi componente discendente può utilizzare questa proprietà tramite l' `ThemeInfo` oggetto a cascata.

`CascadingValuesParametersLayout`componente

```razor
@inherits LayoutComponentBase
@using BlazorSample.UIThemeClasses

<div class="container-fluid">
    <div class="row">
        <div class="col-sm-3">
            <NavMenu />
        </div>
        <div class="col-sm-9">
            <CascadingValue Value="theme">
                <div class="content px-4">
                    @Body
                </div>
            </CascadingValue>
        </div>
    </div>
</div>

@code {
    private ThemeInfo theme = new ThemeInfo { ButtonClass = "btn-success" };
}
```

Per utilizzare i valori di propagazione, i componenti dichiarano i parametri di propagazione utilizzando l' [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) attributo. I valori a cascata vengono associati ai parametri di propagazione per tipo.

Nell'app di esempio, il `CascadingValuesParametersTheme` componente associa il `ThemeInfo` valore a cascata a un parametro di propagazione. Il parametro viene usato per impostare la classe CSS per uno dei pulsanti visualizzati dal componente.

`CascadingValuesParametersTheme`componente

```razor
@page "/cascadingvaluesparameterstheme"
@layout CascadingValuesParametersLayout
@using BlazorSample.UIThemeClasses

<h1>Cascading Values & Parameters</h1>

<p>Current count: @currentCount</p>

<p>
    <button class="btn" @onclick="IncrementCount">
        Increment Counter (Unthemed)
    </button>
</p>

<p>
    <button class="btn @ThemeInfo.ButtonClass" @onclick="IncrementCount">
        Increment Counter (Themed)
    </button>
</p>

@code {
    private int currentCount = 0;

    [CascadingParameter]
    protected ThemeInfo ThemeInfo { get; set; }

    private void IncrementCount()
    {
        currentCount++;
    }
}
```

Per eseguire il propagazione di più valori dello stesso tipo all'interno dello stesso sottoalbero, fornire una stringa univoca <xref:Microsoft.AspNetCore.Components.CascadingValue%601.Name%2A> a ogni <xref:Microsoft.AspNetCore.Components.CascadingValue%601> componente e l' [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) attributo corrispondente. Nell'esempio seguente due componenti propagano <xref:Microsoft.AspNetCore.Components.CascadingValue%601> istanze diverse di `MyCascadingType` per nome:

```razor
<CascadingValue Value=@parentCascadeParameter1 Name="CascadeParam1">
    <CascadingValue Value=@ParentCascadeParameter2 Name="CascadeParam2">
        ...
    </CascadingValue>
</CascadingValue>

@code {
    private MyCascadingType parentCascadeParameter1;

    [Parameter]
    public MyCascadingType ParentCascadeParameter2 { get; set; }

    ...
}
```

In un componente discendente i parametri a cascata ricevono i rispettivi valori dai valori a cascata corrispondenti nel componente predecessore per nome:

```razor
...

@code {
    [CascadingParameter(Name = "CascadeParam1")]
    protected MyCascadingType ChildCascadeParameter1 { get; set; }
    
    [CascadingParameter(Name = "CascadeParam2")]
    protected MyCascadingType ChildCascadeParameter2 { get; set; }
}
```

### <a name="tabset-example"></a>Esempio di tabulazione

I parametri di propagazione consentono inoltre ai componenti di collaborare attraverso la gerarchia dei componenti. Si consideri, ad esempio, l'esempio di *tabulazione* seguente nell'app di esempio.

L'app di esempio ha un' `ITab` interfaccia implementata dalle schede:

[!code-csharp[](common/samples/3.x/BlazorWebAssemblySample/UIInterfaces/ITab.cs)]

Il `CascadingValuesParametersTabSet` componente usa il `TabSet` componente, che contiene diversi `Tab` componenti:

```razor
<TabSet>
    <Tab Title="First tab">
        <h4>Greetings from the first tab!</h4>

        <label>
            <input type="checkbox" @bind="showThirdTab" />
            Toggle third tab
        </label>
    </Tab>
    <Tab Title="Second tab">
        <h4>The second tab says Hello World!</h4>
    </Tab>

    @if (showThirdTab)
    {
        <Tab Title="Third tab">
            <h4>Welcome to the disappearing third tab!</h4>
            <p>Toggle this tab from the first tab.</p>
        </Tab>
    }
</TabSet>
```

I `Tab` componenti figlio non vengono passati in modo esplicito come parametri a `TabSet` . Al contrario, i `Tab` componenti figlio fanno parte del contenuto figlio di `TabSet` . Tuttavia, `TabSet` è comunque necessario conoscere ogni componente in `Tab` modo che sia in grado di eseguire il rendering delle intestazioni e della scheda attiva. Per abilitare questo coordinamento senza richiedere codice aggiuntivo, il `TabSet` componente *può fornire se stesso come valore* di propagazione che viene quindi prelevato dai `Tab` componenti discendenti.

`TabSet`componente

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/TabSet.razor)]

I componenti discendenti `Tab` acquisiscono l'oggetto che contiene `TabSet` come parametro di propagazione, in modo `Tab` che i componenti si aggiungano alla `TabSet` coordinata e della scheda attiva.

`Tab`componente

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/Tab.razor)]

## <a name="razor-templates"></a>Razormodelli

I frammenti di rendering possono essere definiti usando la Razor sintassi del modello. Razori modelli sono un modo per definire un frammento di interfaccia utente e presupporre il formato seguente:

```razor
@<{HTML tag}>...</{HTML tag}>
```

Nell'esempio seguente viene illustrato come specificare <xref:Microsoft.AspNetCore.Components.RenderFragment> <xref:Microsoft.AspNetCore.Components.RenderFragment%601> i valori e ed eseguire il rendering dei modelli direttamente in un componente. I frammenti di rendering possono anche essere passati come argomenti ai [componenti basati su modelli](xref:blazor/templated-components).

```razor
@timeTemplate

@petTemplate(new Pet { Name = "Rex" })

@code {
    private RenderFragment timeTemplate = @<p>The time is @DateTime.Now.</p>;
    private RenderFragment<Pet> petTemplate = (pet) => @<p>Pet: @pet.Name</p>;

    private class Pet
    {
        public string Name { get; set; }
    }
}
```

Output del codice precedente sottoposto a rendering:

```html
<p>The time is 10/04/2018 01:26:52.</p>

<p>Pet: Rex</p>
```

## <a name="scalable-vector-graphics-svg-images"></a>Immagini SVG (Scalable Vector Graphics)

Poiché Blazor esegue il rendering del codice HTML, le immagini supportate dal browser, incluse le immagini SVG (Scalable Vector Graphics) (con*estensione SVG*), sono supportate tramite il `<img>` Tag:

```html
<img alt="Example image" src="some-image.svg" />
```

Analogamente, le immagini SVG sono supportate nelle regole CSS di un file di foglio di stile (*CSS*):

```css
.my-element {
    background-image: url("some-image.svg");
}
```

Tuttavia, il markup SVG inline non è supportato in tutti gli scenari. Se si inserisce un `<svg>` tag direttamente in un file di componente (*Razor*), il rendering delle immagini di base è supportato, ma molti scenari avanzati non sono ancora supportati. Ad esempio, i `<use>` tag non sono attualmente rispettati e [`@bind`][10] non possono essere usati con alcuni tag svg. Per ulteriori informazioni, vedere [supporto per SVG in Blazor (#18271 DotNet/aspnetcore)](https://github.com/dotnet/aspnetcore/issues/18271).

## <a name="additional-resources"></a>Risorse aggiuntive

* <xref:security/blazor/server/threat-mitigation>: Include informazioni aggiuntive sulla creazione di Blazor app server che devono essere confrontate con l'esaurimento delle risorse.

<!--Reference links in article-->
[1]: <xref:mvc/views/razor#code>
[2]: <xref:mvc/views/razor#using>
[3]: <xref:mvc/views/razor#attributes>
[4]: <xref:mvc/views/razor#ref>
[5]: <xref:mvc/views/razor#key>
[6]: <xref:mvc/views/razor#inherits>
[7]: <xref:mvc/views/razor#attribute>
[8]: <xref:mvc/views/razor#namespace>
[9]: <xref:mvc/views/razor#page>
[10]: <xref:mvc/views/razor#bind>
