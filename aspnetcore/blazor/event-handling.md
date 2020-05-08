---
title: Gestione Blazor degli eventi ASP.NET Core
author: guardrex
description: Informazioni sulle Blazorfunzionalità di gestione degli eventi, inclusi i tipi di argomento degli eventi, i callback degli eventi e la gestione degli eventi predefiniti del browser.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/16/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/event-handling
ms.openlocfilehash: aa338bbe61eec14bc1e1b3606e11e26bfb0e6a09
ms.sourcegitcommit: 84b46594f57608f6ac4f0570172c7051df507520
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/08/2020
ms.locfileid: "82967467"
---
# <a name="aspnet-core-blazor-event-handling"></a>Gestione degli eventi di ASP.NET Core Blazer

Di [Luke Latham](https://github.com/guardrex) e [Daniel Roth](https://github.com/danroth27)

I componenti Razor forniscono funzionalità di gestione degli eventi. Per un attributo dell'elemento HTML [`@on{EVENT}`](xref:mvc/views/razor#onevent) denominato, ad esempio `@onclick`, con un valore tipizzato da un delegato, un componente Razor considera il valore dell'attributo come un gestore eventi.

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

I gestori di eventi possono anche essere asincroni e restituire <xref:System.Threading.Tasks.Task>un oggetto. Non è necessario chiamare manualmente [StateHasChanged](xref:blazor/lifecycle#state-changes). Le eccezioni vengono registrate quando si verificano.

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

I `EventArgs` supportati sono riportati nella tabella seguente.

| event            | Classe                | Eventi e note DOM |
| ---------------- | -------------------- | -------------------- |
| Appunti        | `ClipboardEventArgs` | `oncut`, `oncopy`, `onpaste` |
| Trascinamento             | `DragEventArgs`      | `ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`<br><br>`DataTransfer`e `DataTransferItem` contengono dati di elementi trascinati. |
| Errore            | `ErrorEventArgs`     | `onerror` |
| event            | `EventArgs`          | *Generalee*<br>`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onended`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`<br><br>*Appunti*<br>`onbeforecut`, `onbeforecopy`, `onbeforepaste`<br><br>*Input*<br>`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`<br><br>*Supporti*<br>`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting` |
| Focus            | `FocusEventArgs`     | `onfocus`, `onblur`, `onfocusin`, `onfocusout`<br><br>Non include il supporto `relatedTarget`per. |
| Input            | `ChangeEventArgs`    | `onchange`, `oninput` |
| Tastiera         | `KeyboardEventArgs`  | `onkeydown`, `onkeypress`, `onkeyup` |
| Mouse            | `MouseEventArgs`     | `onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout` |
| Puntatore del mouse    | `PointerEventArgs`   | `onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture` |
| Rotellina del mouse      | `WheelEventArgs`     | `onwheel`, `onmousewheel` |
| Avanzamento         | `ProgressEventArgs`  | `onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout` |
| Tocco            | `TouchEventArgs`     | `ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`<br><br>`TouchPoint`rappresenta un singolo punto di contatto su un dispositivo sensibile al tocco. |

Per altre informazioni, vedere le seguenti risorse:

* [Classi EventArgs nell'origine riferimento ASP.NET Core (ramo DotNet/aspnetcore Release/3.1)](https://github.com/dotnet/aspnetcore/tree/release/3.1/src/Components/Web/src/Web).
* [MDN Web docs: GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers) &ndash; include informazioni su quali elementi HTML supportano ogni evento DOM.

## <a name="lambda-expressions"></a>Espressioni lambda

È inoltre possibile utilizzare le [espressioni lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions) :

```razor
<button @onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

Spesso è consigliabile chiudere i valori aggiuntivi, ad esempio quando si esegue l'iterazione su un set di elementi. Nell'esempio seguente vengono creati tre pulsanti, ciascuno dei quali `UpdateHeading` chiama il passaggio di un`MouseEventArgs`argomento di evento () e`buttonNumber`il relativo numero di pulsante () quando vengono selezionati nell'interfaccia utente:

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
> **Non** usare la variabile di ciclo (`i`) in un `for` ciclo direttamente in un'espressione lambda. In caso contrario, la stessa variabile viene utilizzata da tutte `i`le espressioni lambda causando che il valore di sia lo stesso in tutte le espressioni lambda. Acquisire sempre il relativo valore in una variabile locale`buttonNumber` (nell'esempio precedente) e quindi usarlo.

## <a name="eventcallback"></a>EventCallback

Uno scenario comune con i componenti annidati è la volontà di eseguire il metodo di un componente padre quando si verifica&mdash;un evento del componente figlio `onclick` , ad esempio, quando si verifica un evento nell'elemento figlio. Per esporre gli eventi tra i componenti, `EventCallback`usare un oggetto. Un componente padre può assegnare un metodo di callback a un componente figlio `EventCallback`.

`ChildComponent` Nell'app di esempio (*Components/ChildComponent. Razor*) viene illustrato il modo in `onclick` cui il gestore di un pulsante viene `EventCallback` configurato per ricevere un delegato `ParentComponent`dall'oggetto dell'esempio. `EventCallback` Viene digitato `MouseEventArgs`con, appropriato per un `onclick` evento da un dispositivo periferico:

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=5-7,17-18)]

`ParentComponent` Imposta l'oggetto `EventCallback<T>` (`OnClickCallback`) del figlio sul relativo `ShowMessage` metodo.

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

Quando il pulsante è selezionato in `ChildComponent`:

* Viene `ParentComponent`chiamato `ShowMessage` il metodo di. `messageText`viene aggiornato e visualizzato nell'oggetto `ParentComponent`.
* Una chiamata a [StateHasChanged](xref:blazor/lifecycle#state-changes) non è obbligatoria nel metodo del callback (`ShowMessage`). `StateHasChanged`viene chiamato automaticamente per eseguire il rendering `ParentComponent`di, proprio come gli eventi figlio attivano il rendering dei componenti nei gestori eventi che vengono eseguiti all'interno dell'elemento figlio.

`EventCallback`e `EventCallback<T>` consentono delegati asincroni. `EventCallback<T>`è fortemente tipizzato e richiede un tipo di argomento specifico. `EventCallback`è debolmente tipizzato e consente qualsiasi tipo di argomento.

```razor
<ChildComponent 
    OnClickCallback="@(async () => { await Task.Yield(); messageText = "Blaze It!"; })" />
```

Richiamare `EventCallback` o `EventCallback<T>` con `InvokeAsync` e attendere: <xref:System.Threading.Tasks.Task>

```csharp
await callback.InvokeAsync(arg);
```

Usare `EventCallback` e `EventCallback<T>` per la gestione degli eventi e i parametri del componente di associazione.

Preferisci l'oggetto `EventCallback<T>` fortemente `EventCallback`tipizzato. `EventCallback<T>`fornisce un migliore feedback sugli errori agli utenti del componente. Analogamente ad altri gestori di eventi dell'interfaccia utente, la specifica del parametro dell'evento è facoltativa. Usare `EventCallback` quando non è stato passato alcun valore al callback.

## <a name="prevent-default-actions"></a>Impedisci azioni predefinite

Utilizzare l' [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) attributo Directive per impedire l'azione predefinita per un evento.

Quando si seleziona un tasto in un dispositivo di input e lo stato attivo dell'elemento si trova in una casella di testo, in un browser viene in genere visualizzato il carattere della chiave nella casella di testo. Nell'esempio seguente viene impedito il comportamento predefinito specificando l' `@onkeypress:preventDefault` attributo della direttiva. Il contatore viene incrementato e la **+** chiave non viene acquisita `<input>` nel valore dell'elemento:

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

La specifica `@on{EVENT}:preventDefault` dell'attributo senza un valore equivale a `@on{EVENT}:preventDefault="true"`.

Il valore dell'attributo può anche essere un'espressione. `shouldPreventDefault` Nell'esempio seguente è `bool` un campo impostato su `true` o: `false`

```razor
<input @onkeypress:preventDefault="shouldPreventDefault" />
```

Per impedire l'azione predefinita, non è necessario un gestore eventi. Il gestore eventi e impedire scenari di azione predefiniti possono essere usati in modo indipendente.

## <a name="stop-event-propagation"></a>Arresta propagazione eventi

Utilizzare l' [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) attributo Directive per arrestare la propagazione degli eventi.

Nell'esempio seguente la selezione della casella di controllo impedisce la propagazione degli eventi `<div>` click dal secondo figlio all'elemento `<div>`padre:

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
