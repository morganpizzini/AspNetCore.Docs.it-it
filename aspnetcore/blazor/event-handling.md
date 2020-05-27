---
title:' ASP.NET Core Blazor gestione eventi ' Author: Description:' informazioni sulle Blazor funzionalità di gestione degli eventi, inclusi i tipi di argomenti dell'evento, i callback degli eventi e la gestione degli eventi predefiniti del browser.
monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID '': 

---
# <a name="aspnet-core-blazor-event-handling"></a>BlazorGestione degli eventi ASP.NET Core

Di [Luke Latham](https://github.com/guardrex) e [Daniel Roth](https://github.com/danroth27)

Razori componenti di forniscono funzionalità di gestione degli eventi. Per un attributo dell'elemento HTML denominato, [`@on{EVENT}`](xref:mvc/views/razor#onevent) ad esempio, `@onclick` con un valore tipizzato come delegato, un Razor componente considera il valore dell'attributo come un gestore eventi.

Il codice seguente chiama il `UpdateHeading` metodo quando si seleziona il pulsante nell'interfaccia utente:

```razor
<button class="btn btn-primary" @onclick="UpdateHeading">
    Update heading
</button>

@code {
    private void UpdateHeading(MouseEventArgs e)
    {
        ...
    }
}
```

Il codice seguente chiama il `CheckChanged` metodo quando la casella di controllo viene modificata nell'interfaccia utente:

```razor
<input type="checkbox" class="form-check-input" @onchange="CheckChanged" />

@code {
    private void CheckChanged()
    {
        ...
    }
}
```

I gestori di eventi possono anche essere asincroni e restituire un oggetto <xref:System.Threading.Tasks.Task> . Non è necessario chiamare manualmente [StateHasChanged](xref:blazor/lifecycle#state-changes). Le eccezioni vengono registrate quando si verificano.

Nell'esempio seguente, `UpdateHeading` viene chiamato in modo asincrono quando si seleziona il pulsante:

```razor
<button class="btn btn-primary" @onclick="UpdateHeading">
    Update heading
</button>

@code {
    private async Task UpdateHeading(MouseEventArgs e)
    {
        ...
    }
}
```

## <a name="event-argument-types"></a>Tipi di argomenti dell'evento

Per alcuni eventi, i tipi di argomento dell'evento sono consentiti. La specifica di un tipo di evento nella chiamata al metodo è necessaria solo se il tipo di evento viene usato nel metodo.

<xref:System.EventArgs>I supportati sono riportati nella tabella seguente.

| Event            | Classe                | Eventi e note DOM |
| ---
title:' ASP.NET Core Blazor gestione eventi ' Author: Description:' informazioni sulle Blazor funzionalità di gestione degli eventi, inclusi i tipi di argomenti dell'evento, i callback degli eventi e la gestione degli eventi predefiniti del browser.
monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID '': 

-
title:' ASP.NET Core Blazor gestione eventi ' Author: Description:' informazioni sulle Blazor funzionalità di gestione degli eventi, inclusi i tipi di argomenti dell'evento, i callback degli eventi e la gestione degli eventi predefiniti del browser.
monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID '': 

-
title:' ASP.NET Core Blazor gestione eventi ' Author: Description:' informazioni sulle Blazor funzionalità di gestione degli eventi, inclusi i tipi di argomenti dell'evento, i callback degli eventi e la gestione degli eventi predefiniti del browser.
monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID '': 

-
title:' ASP.NET Core Blazor gestione eventi ' Author: Description:' informazioni sulle Blazor funzionalità di gestione degli eventi, inclusi i tipi di argomenti dell'evento, i callback degli eventi e la gestione degli eventi predefiniti del browser.
monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID '': 

-
title:' ASP.NET Core Blazor gestione eventi ' Author: Description:' informazioni sulle Blazor funzionalità di gestione degli eventi, inclusi i tipi di argomenti dell'evento, i callback degli eventi e la gestione degli eventi predefiniti del browser.
monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID '': 

-
title:' ASP.NET Core Blazor gestione eventi ' Author: Description:' informazioni sulle Blazor funzionalità di gestione degli eventi, inclusi i tipi di argomenti dell'evento, i callback degli eventi e la gestione degli eventi predefiniti del browser.
monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID '': 

-------- | Titolo---: "ASP.NET Core Blazor gestione eventi" autore: Descrizione: "informazioni sulle Blazor funzionalità di gestione degli eventi, inclusi i tipi di argomenti degli eventi, callback di eventi e gestione degli eventi del browser predefiniti".
monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID '': 

-
title:' ASP.NET Core Blazor gestione eventi ' Author: Description:' informazioni sulle Blazor funzionalità di gestione degli eventi, inclusi i tipi di argomenti dell'evento, i callback degli eventi e la gestione degli eventi predefiniti del browser.
monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID '': 

-
title:' ASP.NET Core Blazor gestione eventi ' Author: Description:' informazioni sulle Blazor funzionalità di gestione degli eventi, inclusi i tipi di argomenti dell'evento, i callback degli eventi e la gestione degli eventi predefiniti del browser.
monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID '': 

-
title:' ASP.NET Core Blazor gestione eventi ' Author: Description:' informazioni sulle Blazor funzionalità di gestione degli eventi, inclusi i tipi di argomenti dell'evento, i callback degli eventi e la gestione degli eventi predefiniti del browser.
monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID '': 

-
title:' ASP.NET Core Blazor gestione eventi ' Author: Description:' informazioni sulle Blazor funzionalità di gestione degli eventi, inclusi i tipi di argomenti dell'evento, i callback degli eventi e la gestione degli eventi predefiniti del browser.
monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID '': 

-
title:' ASP.NET Core Blazor gestione eventi ' Author: Description:' informazioni sulle Blazor funzionalità di gestione degli eventi, inclusi i tipi di argomenti dell'evento, i callback degli eventi e la gestione degli eventi predefiniti del browser.
monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID '': 

-
title:' ASP.NET Core Blazor gestione eventi ' Author: Description:' informazioni sulle Blazor funzionalità di gestione degli eventi, inclusi i tipi di argomenti dell'evento, i callback degli eventi e la gestione degli eventi predefiniti del browser.
monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID '': 

-
title:' ASP.NET Core Blazor gestione eventi ' Author: Description:' informazioni sulle Blazor funzionalità di gestione degli eventi, inclusi i tipi di argomenti dell'evento, i callback degli eventi e la gestione degli eventi predefiniti del browser.
monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID '': 

---------- | Titolo---: "ASP.NET Core Blazor gestione eventi" autore: Descrizione: "informazioni sulle Blazor funzionalità di gestione degli eventi, inclusi i tipi di argomenti degli eventi, callback di eventi e gestione degli eventi del browser predefiniti".
monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID '': 

-
title:' ASP.NET Core Blazor gestione eventi ' Author: Description:' informazioni sulle Blazor funzionalità di gestione degli eventi, inclusi i tipi di argomenti dell'evento, i callback degli eventi e la gestione degli eventi predefiniti del browser.
monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID '': 

-
title:' ASP.NET Core Blazor gestione eventi ' Author: Description:' informazioni sulle Blazor funzionalità di gestione degli eventi, inclusi i tipi di argomenti dell'evento, i callback degli eventi e la gestione degli eventi predefiniti del browser.
monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID '': 

-
title:' ASP.NET Core Blazor gestione eventi ' Author: Description:' informazioni sulle Blazor funzionalità di gestione degli eventi, inclusi i tipi di argomenti dell'evento, i callback degli eventi e la gestione degli eventi predefiniti del browser.
monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID '': 

-
title:' ASP.NET Core Blazor gestione eventi ' Author: Description:' informazioni sulle Blazor funzionalità di gestione degli eventi, inclusi i tipi di argomenti dell'evento, i callback degli eventi e la gestione degli eventi predefiniti del browser.
monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID '': 

-
title:' ASP.NET Core Blazor gestione eventi ' Author: Description:' informazioni sulle Blazor funzionalità di gestione degli eventi, inclusi i tipi di argomenti dell'evento, i callback degli eventi e la gestione degli eventi predefiniti del browser.
monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID '': 

-
title:' ASP.NET Core Blazor gestione eventi ' Author: Description:' informazioni sulle Blazor funzionalità di gestione degli eventi, inclusi i tipi di argomenti dell'evento, i callback degli eventi e la gestione degli eventi predefiniti del browser.
monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID '': 

-
title:' ASP.NET Core Blazor gestione eventi ' Author: Description:' informazioni sulle Blazor funzionalità di gestione degli eventi, inclusi i tipi di argomenti dell'evento, i callback degli eventi e la gestione degli eventi predefiniti del browser.
monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID '': 

---------- | | Appunti | <xref:Microsoft.AspNetCore.Components.Web.ClipboardEventArgs> | `oncut`, `oncopy`, `onpaste` | | Trascina | <xref:Microsoft.AspNetCore.Components.Web.DragEventArgs> | `ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`,`ondragend`<br><br><xref:Microsoft.AspNetCore.Components.Web.DataTransfer>e <xref:Microsoft.AspNetCore.Components.Web.DataTransferItem> contengono dati di elementi trascinati. | | Errore | <xref:Microsoft.AspNetCore.Components.Web.ErrorEventArgs> | `onerror` | | Evento | <xref:System.EventArgs>  |  *Informazioni generali*<br>`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onended`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`<br><br>*Appunti*<br>`onbeforecut`, `onbeforecopy`, `onbeforepaste`<br><br>*Input*<br>`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit><br><br>*Supporti*<br>`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`<br><br><xref:Microsoft.AspNetCore.Components.Web.EventHandlers>include gli attributi per configurare i mapping tra i nomi di evento e i tipi di argomenti dell'evento. | | Stato attivo | <xref:Microsoft.AspNetCore.Components.Web.FocusEventArgs> | `onfocus`, `onblur`, `onfocusin`,`onfocusout`<br><br>Non include il supporto per `relatedTarget` . | | Input | <xref:Microsoft.AspNetCore.Components.ChangeEventArgs> | `onchange`, `oninput` | | Tastiera | <xref:Microsoft.AspNetCore.Components.Web.KeyboardEventArgs> | `onkeydown`, `onkeypress`, `onkeyup` | | Mouse | <xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs> | `onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout` | | Puntatore del mouse | <xref:Microsoft.AspNetCore.Components.Web.PointerEventArgs> | `onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture` | | Rotellina del mouse | <xref:Microsoft.AspNetCore.Components.Web.WheelEventArgs> | `onwheel`, `onmousewheel` | | Stato di avanzamento | <xref:Microsoft.AspNetCore.Components.Web.ProgressEventArgs> | `onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout` | | Tocco | <xref:Microsoft.AspNetCore.Components.Web.TouchEventArgs> | `ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`,`ontouchcancel`<br><br><xref:Microsoft.AspNetCore.Components.Web.TouchPoint>rappresenta un singolo punto di contatto su un dispositivo sensibile al tocco. |

Per altre informazioni, vedere le seguenti risorse:

* [Classi EventArgs nell'origine riferimento ASP.NET Core (ramo DotNet/aspnetcore Release/3.1)](https://github.com/dotnet/aspnetcore/tree/release/3.1/src/Components/Web/src/Web).
* [Documentazione Web MDN: GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers) &ndash; Include informazioni su quali elementi HTML supportano ogni evento DOM.

## <a name="lambda-expressions"></a>Espressioni lambda

È inoltre possibile utilizzare le [espressioni lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions) :

```razor
<button @onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

Spesso è consigliabile chiudere i valori aggiuntivi, ad esempio quando si esegue l'iterazione su un set di elementi. Nell'esempio seguente vengono creati tre pulsanti, ciascuno dei quali chiama il `UpdateHeading` passaggio di un argomento di evento ( <xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs> ) e il relativo numero di pulsante ( `buttonNumber` ) quando vengono selezionati nell'interfaccia utente:

```razor
<h2>@message</h2>

@for (var i = 1; i < 4; i++)
{
    var buttonNumber = i;

    <button class="btn btn-primary"
            @onclick="@(e => UpdateHeading(e, buttonNumber))">
        Button #@i
    </button>
}

@code {
    private string message = "Select a button to learn its position.";

    private void UpdateHeading(MouseEventArgs e, int buttonNumber)
    {
        message = $"You selected Button #{buttonNumber} at " +
            $"mouse position: {e.ClientX} X {e.ClientY}.";
    }
}
```

> [!NOTE]
> **Non** usare la variabile di ciclo ( `i` ) in un `for` ciclo direttamente in un'espressione lambda. In caso contrario, la stessa variabile viene utilizzata da tutte le espressioni lambda causando `i` che il valore di sia lo stesso in tutte le espressioni lambda. Acquisire sempre il relativo valore in una variabile locale ( `buttonNumber` nell'esempio precedente) e quindi usarlo.

## <a name="eventcallback"></a>EventCallback

Uno scenario comune con i componenti annidati è la volontà di eseguire il metodo di un componente padre quando si verifica un evento del componente figlio. Un `onclick` evento che si verifica nel componente figlio è un caso d'uso comune. Per esporre gli eventi tra i componenti, usare un oggetto <xref:Microsoft.AspNetCore.Components.EventCallback> . Un componente padre può assegnare un metodo di callback a un componente figlio <xref:Microsoft.AspNetCore.Components.EventCallback> .

`ChildComponent`Nell'app di esempio (*Components/ChildComponent. Razor*) viene illustrato il modo in cui il gestore di un pulsante `onclick` viene configurato per ricevere un <xref:Microsoft.AspNetCore.Components.EventCallback> delegato dall'oggetto dell'esempio `ParentComponent` . <xref:Microsoft.AspNetCore.Components.EventCallback>Viene digitato con `MouseEventArgs` , appropriato per un `onclick` evento da un dispositivo periferico:

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=5-7,17-18)]

`ParentComponent`Imposta l'oggetto () del figlio sul <xref:Microsoft.AspNetCore.Components.EventCallback%601> `OnClickCallback` relativo `ShowMessage` metodo.

*Pages/ParentComponent. Razor*:

```razor
@page "/ParentComponent"

<h1>Parent-child example</h1>

<ChildComponent Title="Panel Title from Parent"
                OnClickCallback="@ShowMessage">
    Content of the child component is supplied
    by the parent component.
</ChildComponent>

<p><b>@messageText</b></p>

@code {
    private string messageText;

    private void ShowMessage(MouseEventArgs e)
    {
        messageText = $"Blaze a new trail with Blazor! ({e.ScreenX}, {e.ScreenY})";
    }
}
```

Quando il pulsante è selezionato in `ChildComponent` :

* `ParentComponent` `ShowMessage` Viene chiamato il metodo di. `messageText`viene aggiornato e visualizzato nell'oggetto `ParentComponent` .
* Una chiamata a [StateHasChanged](xref:blazor/lifecycle#state-changes) non è obbligatoria nel metodo del callback ( `ShowMessage` ). <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>viene chiamato automaticamente per eseguire il rendering di `ParentComponent` , proprio come gli eventi figlio attivano il rendering dei componenti nei gestori eventi che vengono eseguiti all'interno dell'elemento figlio.

<xref:Microsoft.AspNetCore.Components.EventCallback>e <xref:Microsoft.AspNetCore.Components.EventCallback%601> consentono delegati asincroni. <xref:Microsoft.AspNetCore.Components.EventCallback%601>è fortemente tipizzato e richiede un tipo di argomento specifico. <xref:Microsoft.AspNetCore.Components.EventCallback>è debolmente tipizzato e consente qualsiasi tipo di argomento.

```razor
<ChildComponent 
    OnClickCallback="@(async () => { await Task.Yield(); messageText = "Blaze It!"; })" />
```

Richiamare <xref:Microsoft.AspNetCore.Components.EventCallback> o <xref:Microsoft.AspNetCore.Components.EventCallback%601> con <xref:Microsoft.AspNetCore.Components.EventCallback.InvokeAsync%2A> e attendere <xref:System.Threading.Tasks.Task> :

```csharp
await callback.InvokeAsync(arg);
```

Usare <xref:Microsoft.AspNetCore.Components.EventCallback> e <xref:Microsoft.AspNetCore.Components.EventCallback%601> per la gestione degli eventi e i parametri del componente di associazione.

Preferisci l'oggetto fortemente tipizzato <xref:Microsoft.AspNetCore.Components.EventCallback%601> <xref:Microsoft.AspNetCore.Components.EventCallback> . <xref:Microsoft.AspNetCore.Components.EventCallback%601>fornisce un migliore feedback sugli errori agli utenti del componente. Analogamente ad altri gestori di eventi dell'interfaccia utente, la specifica del parametro dell'evento è facoltativa. Usare <xref:Microsoft.AspNetCore.Components.EventCallback> quando non è stato passato alcun valore al callback.

## <a name="prevent-default-actions"></a>Impedisci azioni predefinite

Utilizzare l' [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) attributo Directive per impedire l'azione predefinita per un evento.

Quando si seleziona un tasto in un dispositivo di input e lo stato attivo dell'elemento si trova in una casella di testo, in un browser viene in genere visualizzato il carattere della chiave nella casella di testo. Nell'esempio seguente viene impedito il comportamento predefinito specificando l' `@onkeypress:preventDefault` attributo della direttiva. Il contatore viene incrementato e la **+** chiave non viene acquisita nel `<input>` valore dell'elemento:

```razor
<input value="@count" @onkeypress="KeyHandler" @onkeypress:preventDefault />

@code {
    private int count = 0;

    private void KeyHandler(KeyboardEventArgs e)
    {
        if (e.Key == "+")
        {
            count++;
        }
    }
}
```

La specifica dell' `@on{EVENT}:preventDefault` attributo senza un valore equivale a `@on{EVENT}:preventDefault="true"` .

Il valore dell'attributo può anche essere un'espressione. Nell'esempio seguente `shouldPreventDefault` è un `bool` campo impostato su `true` o `false` :

```razor
<input @onkeypress:preventDefault="shouldPreventDefault" />
```

Per impedire l'azione predefinita, non è necessario un gestore eventi. Il gestore eventi e impedire scenari di azione predefiniti possono essere usati in modo indipendente.

## <a name="stop-event-propagation"></a>Arresta propagazione eventi

Utilizzare l' [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) attributo Directive per arrestare la propagazione degli eventi.

Nell'esempio seguente la selezione della casella di controllo impedisce la propagazione degli eventi click dal secondo figlio `<div>` all'elemento padre `<div>` :

```razor
<label>
    <input @bind="stopPropagation" type="checkbox" />
    Stop Propagation
</label>

<div @onclick="OnSelectParentDiv">
    <h3>Parent div</h3>

    <div @onclick="OnSelectChildDiv">
        Child div that doesn't stop propagation when selected.
    </div>

    <div @onclick="OnSelectChildDiv" @onclick:stopPropagation="stopPropagation">
        Child div that stops propagation when selected.
    </div>
</div>

@code {
    private bool stopPropagation = false;

    private void OnSelectParentDiv() => 
        Console.WriteLine($"The parent div was selected. {DateTime.Now}");
    private void OnSelectChildDiv() => 
        Console.WriteLine($"A child div was selected. {DateTime.Now}");
}
```
