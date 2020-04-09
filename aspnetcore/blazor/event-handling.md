---
title: gestione Blazor degli eventi di ASP.NET Core
author: guardrex
description: Informazioni Blazorsulle funzionalità di gestione degli eventi, inclusi i tipi di argomenti di evento, i callback degli eventi e la gestione degli eventi predefiniti del browser.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/16/2020
no-loc:
- Blazor
- SignalR
uid: blazor/event-handling
ms.openlocfilehash: c144841805e07a136f153c25a78c7f9af7c5801b
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "79511366"
---
# <a name="aspnet-core-blazor-event-handling"></a>gestione degli eventi ASP.NET Core Blazor

Di [Luke Latham](https://github.com/guardrex) e [Daniel Roth](https://github.com/danroth27)

I componenti Razor forniscono funzionalità di gestione degli eventi. Per un attributo [`@on{EVENT}`](xref:mvc/views/razor#onevent) di elemento `@onclick`HTML denominato (ad esempio, ) con un valore tipizzato da delegato, un componente Razor considera il valore dell'attributo come un gestore eventi.

Il codice seguente `UpdateHeading` chiama il metodo quando il pulsante viene selezionato nell'interfaccia utente:The following code calls the method when the button is selected in the UI:

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

Il codice seguente `CheckChanged` chiama il metodo quando la casella di controllo viene modificata nell'interfaccia utente:The following code calls the method when the check box is changed in the UI:

```razor
<input type="checkbox" class="form-check-input" @onchange="CheckChanged" />

@code {
    private void CheckChanged()
    {
        ...
    }
}
```

I gestori eventi possono anche <xref:System.Threading.Tasks.Task>essere asincroni e restituire un oggetto . Non è necessario chiamare manualmente [StateHasChanged](xref:blazor/lifecycle#state-changes). Le eccezioni vengono registrate quando si verificano.

Nell'esempio seguente, viene chiamato in modo asincrono quando viene selezionato il pulsante:In the following example, `UpdateHeading` is called asynchronously when the button is selected:

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

## <a name="event-argument-types"></a>Tipi di argomenti di evento

Per alcuni eventi, i tipi di argomento di evento sono consentiti. La specifica di un tipo di evento nella chiamata al metodo è necessaria solo se il tipo di evento viene utilizzato nel metodo.

I `EventArgs` supportati sono illustrati nella tabella seguente.

| Event            | Classe                | Eventi e note DOM |
| ---------------- | -------------------- | -------------------- |
| Appunti        | `ClipboardEventArgs` | `oncut`, `oncopy`, `onpaste` |
| Trascinamento             | `DragEventArgs`      | `ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`<br><br>`DataTransfer`e `DataTransferItem` tenere premuti i dati dell'elemento trascinato. |
| Errore            | `ErrorEventArgs`     | `onerror` |
| Event            | `EventArgs`          | *Generale*<br>`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onended`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`<br><br>*Appunti*<br>`onbeforecut`, `onbeforecopy`, `onbeforepaste`<br><br>*Input*<br>`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`<br><br>*Supporti*<br>`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting` |
| Focus            | `FocusEventArgs`     | `onfocus`, `onblur`, `onfocusin`, `onfocusout`<br><br>Non include il `relatedTarget`supporto per . |
| Input            | `ChangeEventArgs`    | `onchange`, `oninput` |
| Tastiera         | `KeyboardEventArgs`  | `onkeydown`, `onkeypress`, `onkeyup` |
| Mouse            | `MouseEventArgs`     | `onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout` |
| Puntatore del mouse    | `PointerEventArgs`   | `onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture` |
| Rotellina del mouse      | `WheelEventArgs`     | `onwheel`, `onmousewheel` |
| Progress         | `ProgressEventArgs`  | `onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout` |
| Tocco            | `TouchEventArgs`     | `ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`<br><br>`TouchPoint`rappresenta un singolo punto di contatto su un dispositivo sensibile al tocco. |

Per altre informazioni, vedere le seguenti risorse:

* [Classi EventArgs nell'origine di riferimento ASP.NET Core (ramo dotnet/aspnetcore release/3.1).](https://github.com/dotnet/aspnetcore/tree/release/3.1/src/Components/Web/src/Web)
* [Documenti Web MDN: GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers) &ndash; Include informazioni su quali elementi HTML supportano ogni evento DOM.

## <a name="lambda-expressions"></a>Espressioni lambda

[Le espressioni lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions) possono anche essere usate:Lambda expressions can also be used:

```razor
<button @onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

È spesso conveniente chiudere su valori aggiuntivi, ad esempio quando si scorre un set di elementi. Nell'esempio seguente vengono creati tre `UpdateHeading` pulsanti,`MouseEventArgs`ognuno dei quali`buttonNumber`chiama il passaggio di un argomento dell'evento ( ) e il relativo numero di pulsante ( ) quando viene selezionato nell'interfaccia utente:

```razor
<h2>@_message</h2>

@for (var i = 1; i < 4; i++)
{
    var buttonNumber = i;

    <button class="btn btn-primary"
            @onclick="@(e => UpdateHeading(e, buttonNumber))">
        Button #@i
    </button>
}

@code {
    private string _message = "Select a button to learn its position.";

    private void UpdateHeading(MouseEventArgs e, int buttonNumber)
    {
        _message = $"You selected Button #{buttonNumber} at " +
            $"mouse position: {e.ClientX} X {e.ClientY}.";
    }
}
```

> [!NOTE]
> **Non** usare la variabile`i`di `for` ciclo ( ) in un ciclo direttamente in un'espressione lambda. In caso contrario, la stessa `i`variabile viene utilizzata da tutte le espressioni lambda facendo sì che il valore di 'sia lo stesso in tutte le espressioni lambda. Acquisire sempre il valore in`buttonNumber` una variabile locale (nell'esempio precedente) e quindi utilizzarlo.

## <a name="eventcallback"></a>EventCallback

Uno scenario comune con componenti annidati è il desiderio di eseguire&mdash;il metodo `onclick` di un componente padre quando si verifica un evento componente figlio, ad esempio, quando si verifica un evento nell'elemento figlio. Per esporre eventi tra `EventCallback`componenti, utilizzare un file . Un componente padre può assegnare un metodo `EventCallback`di callback a .

Nell'app `ChildComponent` di esempio (*Components/ChildComponent.razor*) `onclick` viene illustrato come il `EventCallback` gestore di `ParentComponent`un pulsante è impostato per ricevere un delegato dall'esempio . Viene `EventCallback` digitato `MouseEventArgs`con , appropriato `onclick` per un evento da una periferica:

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=5-7,17-18)]

Il `ParentComponent` imposta il `EventCallback<T>` bambino`OnClickCallback`( `ShowMessage` ) sul relativo metodo.

*Pages/ParentComponent.razor*:

```razor
@page "/ParentComponent"

<h1>Parent-child example</h1>

<ChildComponent Title="Panel Title from Parent"
                OnClickCallback="@ShowMessage">
    Content of the child component is supplied
    by the parent component.
</ChildComponent>

<p><b>@_messageText</b></p>

@code {
    private string _messageText;

    private void ShowMessage(MouseEventArgs e)
    {
        _messageText = $"Blaze a new trail with Blazor! ({e.ScreenX}, {e.ScreenY})";
    }
}
```

Quando il pulsante `ChildComponent`è selezionato nella sezione :

* Viene `ParentComponent`chiamato `ShowMessage` il metodo 's. `_messageText`viene aggiornato e visualizzato `ParentComponent`nel file .
* Una chiamata a [StateHasChanged](xref:blazor/lifecycle#state-changes) non è necessaria nel`ShowMessage`metodo del callback ( ). `StateHasChanged`viene chiamato automaticamente per `ParentComponent`eseguire nuovamente il rendering di , proprio come gli eventi figlio attivano il rendering del componente nei gestori eventi eseguiti all'interno dell'elemento figlio.

`EventCallback`e `EventCallback<T>` consentire delegati asincroni. `EventCallback<T>`è fortemente tipizzato e richiede un tipo di argomento specifico. `EventCallback`è debolmente tipizzato e consente qualsiasi tipo di argomento.

```razor
<ChildComponent 
    OnClickCallback="@(async () => { await Task.Yield(); _messageText = "Blaze It!"; })" />
```

Richiamare `EventCallback` `EventCallback<T>` un `InvokeAsync` oggetto o <xref:System.Threading.Tasks.Task>con e attendere il :

```csharp
await callback.InvokeAsync(arg);
```

Utilizzare `EventCallback` `EventCallback<T>` e per la gestione degli eventi e i parametri del componente di associazione.

Preferire il fortemente tipizzato `EventCallback<T>` su `EventCallback`. `EventCallback<T>`fornisce un feedback di errore migliore agli utenti del componente. Simile ad altri gestori eventi dell'interfaccia utente, specificare il parametro dell'evento è facoltativo. Utilizzare `EventCallback` quando non è presente alcun valore passato al callback.

## <a name="prevent-default-actions"></a>Impedisci azioni predefinite

Utilizzare [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) l'attributo della direttiva per impedire l'azione predefinita per un evento.

Quando si seleziona un tasto su un dispositivo di input e lo stato attivo dell'elemento si trova su una casella di testo, un browser in genere visualizza il carattere del tasto nella casella di testo. Nell'esempio seguente, il comportamento predefinito viene `@onkeypress:preventDefault` impedito specificando l'attributo della direttiva. Il contatore viene **+** incrementato e la `<input>` chiave non viene acquisita nel valore dell'elemento:

```razor
<input value="@_count" @onkeypress="KeyHandler" @onkeypress:preventDefault />

@code {
    private int _count = 0;

    private void KeyHandler(KeyboardEventArgs e)
    {
        if (e.Key == "+")
        {
            _count++;
        }
    }
}
```

Specificare `@on{EVENT}:preventDefault` l'attributo senza `@on{EVENT}:preventDefault="true"`un valore equivale a .

Il valore dell'attributo può anche essere un'espressione. Nell'esempio `_shouldPreventDefault` seguente, `bool` è un `true` campo `false`impostato su o :

```razor
<input @onkeypress:preventDefault="_shouldPreventDefault" />
```

Un gestore eventi non è necessario per impedire l'azione predefinita. Il gestore eventi e impedire scenari di azione predefiniti possono essere utilizzati in modo indipendente.

## <a name="stop-event-propagation"></a>Interrompi propagazione eventi

Utilizzare [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) l'attributo della direttiva per interrompere la propagazione degli eventi.

Nell'esempio seguente, la selezione della casella di `<div>` controllo impedisce la `<div>`propagazione degli eventi Click dal secondo figlio all'elemento padre :

```razor
<label>
    <input @bind="_stopPropagation" type="checkbox" />
    Stop Propagation
</label>

<div @onclick="OnSelectParentDiv">
    <h3>Parent div</h3>

    <div @onclick="OnSelectChildDiv">
        Child div that doesn't stop propagation when selected.
    </div>

    <div @onclick="OnSelectChildDiv" @onclick:stopPropagation="_stopPropagation">
        Child div that stops propagation when selected.
    </div>
</div>

@code {
    private bool _stopPropagation = false;

    private void OnSelectParentDiv() => 
        Console.WriteLine($"The parent div was selected. {DateTime.Now}");
    private void OnSelectChildDiv() => 
        Console.WriteLine($"A child div was selected. {DateTime.Now}");
}
```
