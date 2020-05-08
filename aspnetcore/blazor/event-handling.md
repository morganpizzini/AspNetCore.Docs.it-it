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
# <a name="aspnet-core-blazor-event-handling"></a><span data-ttu-id="c2d44-103">Gestione degli eventi di ASP.NET Core Blazer</span><span class="sxs-lookup"><span data-stu-id="c2d44-103">ASP.NET Core Blazor event handling</span></span>

<span data-ttu-id="c2d44-104">Di [Luke Latham](https://github.com/guardrex) e [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="c2d44-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="c2d44-105">I componenti Razor forniscono funzionalità di gestione degli eventi.</span><span class="sxs-lookup"><span data-stu-id="c2d44-105">Razor components provide event handling features.</span></span> <span data-ttu-id="c2d44-106">Per un attributo dell'elemento HTML [`@on{EVENT}`](xref:mvc/views/razor#onevent) denominato, ad esempio `@onclick`, con un valore tipizzato da un delegato, un componente Razor considera il valore dell'attributo come un gestore eventi.</span><span class="sxs-lookup"><span data-stu-id="c2d44-106">For an HTML element attribute named [`@on{EVENT}`](xref:mvc/views/razor#onevent) (for example, `@onclick`) with a delegate-typed value, a Razor component treats the attribute's value as an event handler.</span></span>

<span data-ttu-id="c2d44-107">Il codice seguente chiama il `UpdateHeading` metodo quando si seleziona il pulsante nell'interfaccia utente:</span><span class="sxs-lookup"><span data-stu-id="c2d44-107">The following code calls the `UpdateHeading` method when the button is selected in the UI:</span></span>

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

<span data-ttu-id="c2d44-108">Il codice seguente chiama il `CheckChanged` metodo quando la casella di controllo viene modificata nell'interfaccia utente:</span><span class="sxs-lookup"><span data-stu-id="c2d44-108">The following code calls the `CheckChanged` method when the check box is changed in the UI:</span></span>

```razor
<input type="checkbox" class="form-check-input" @onchange="CheckChanged" />

@code {
    private void CheckChanged()
    {
        ...
    }
}
```

<span data-ttu-id="c2d44-109">I gestori di eventi possono anche essere asincroni e restituire <xref:System.Threading.Tasks.Task>un oggetto.</span><span class="sxs-lookup"><span data-stu-id="c2d44-109">Event handlers can also be asynchronous and return a <xref:System.Threading.Tasks.Task>.</span></span> <span data-ttu-id="c2d44-110">Non è necessario chiamare manualmente [StateHasChanged](xref:blazor/lifecycle#state-changes).</span><span class="sxs-lookup"><span data-stu-id="c2d44-110">There's no need to manually call [StateHasChanged](xref:blazor/lifecycle#state-changes).</span></span> <span data-ttu-id="c2d44-111">Le eccezioni vengono registrate quando si verificano.</span><span class="sxs-lookup"><span data-stu-id="c2d44-111">Exceptions are logged when they occur.</span></span>

<span data-ttu-id="c2d44-112">Nell'esempio seguente, `UpdateHeading` viene chiamato in modo asincrono quando si seleziona il pulsante:</span><span class="sxs-lookup"><span data-stu-id="c2d44-112">In the following example, `UpdateHeading` is called asynchronously when the button is selected:</span></span>

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

## <a name="event-argument-types"></a><span data-ttu-id="c2d44-113">Tipi di argomenti dell'evento</span><span class="sxs-lookup"><span data-stu-id="c2d44-113">Event argument types</span></span>

<span data-ttu-id="c2d44-114">Per alcuni eventi, i tipi di argomento dell'evento sono consentiti.</span><span class="sxs-lookup"><span data-stu-id="c2d44-114">For some events, event argument types are permitted.</span></span> <span data-ttu-id="c2d44-115">La specifica di un tipo di evento nella chiamata al metodo è necessaria solo se il tipo di evento viene usato nel metodo.</span><span class="sxs-lookup"><span data-stu-id="c2d44-115">Specifying an event type in the method call is only necessary if the event type is used in the method.</span></span>

<span data-ttu-id="c2d44-116">I `EventArgs` supportati sono riportati nella tabella seguente.</span><span class="sxs-lookup"><span data-stu-id="c2d44-116">Supported `EventArgs` are shown in the following table.</span></span>

| <span data-ttu-id="c2d44-117">event</span><span class="sxs-lookup"><span data-stu-id="c2d44-117">Event</span></span>            | <span data-ttu-id="c2d44-118">Classe</span><span class="sxs-lookup"><span data-stu-id="c2d44-118">Class</span></span>                | <span data-ttu-id="c2d44-119">Eventi e note DOM</span><span class="sxs-lookup"><span data-stu-id="c2d44-119">DOM events and notes</span></span> |
| ---------------- | -------------------- | -------------------- |
| <span data-ttu-id="c2d44-120">Appunti</span><span class="sxs-lookup"><span data-stu-id="c2d44-120">Clipboard</span></span>        | `ClipboardEventArgs` | <span data-ttu-id="c2d44-121">`oncut`, `oncopy`, `onpaste`</span><span class="sxs-lookup"><span data-stu-id="c2d44-121">`oncut`, `oncopy`, `onpaste`</span></span> |
| <span data-ttu-id="c2d44-122">Trascinamento</span><span class="sxs-lookup"><span data-stu-id="c2d44-122">Drag</span></span>             | `DragEventArgs`      | <span data-ttu-id="c2d44-123">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span><span class="sxs-lookup"><span data-stu-id="c2d44-123">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span></span><br><br><span data-ttu-id="c2d44-124">`DataTransfer`e `DataTransferItem` contengono dati di elementi trascinati.</span><span class="sxs-lookup"><span data-stu-id="c2d44-124">`DataTransfer` and `DataTransferItem` hold dragged item data.</span></span> |
| <span data-ttu-id="c2d44-125">Errore</span><span class="sxs-lookup"><span data-stu-id="c2d44-125">Error</span></span>            | `ErrorEventArgs`     | `onerror` |
| <span data-ttu-id="c2d44-126">event</span><span class="sxs-lookup"><span data-stu-id="c2d44-126">Event</span></span>            | `EventArgs`          | <span data-ttu-id="c2d44-127">*Generalee*</span><span class="sxs-lookup"><span data-stu-id="c2d44-127">*General*</span></span><br><span data-ttu-id="c2d44-128">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onended`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span><span class="sxs-lookup"><span data-stu-id="c2d44-128">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onended`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span></span><br><br><span data-ttu-id="c2d44-129">*Appunti*</span><span class="sxs-lookup"><span data-stu-id="c2d44-129">*Clipboard*</span></span><br><span data-ttu-id="c2d44-130">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span><span class="sxs-lookup"><span data-stu-id="c2d44-130">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span></span><br><br><span data-ttu-id="c2d44-131">*Input*</span><span class="sxs-lookup"><span data-stu-id="c2d44-131">*Input*</span></span><br><span data-ttu-id="c2d44-132">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`</span><span class="sxs-lookup"><span data-stu-id="c2d44-132">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`</span></span><br><br><span data-ttu-id="c2d44-133">*Supporti*</span><span class="sxs-lookup"><span data-stu-id="c2d44-133">*Media*</span></span><br><span data-ttu-id="c2d44-134">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`</span><span class="sxs-lookup"><span data-stu-id="c2d44-134">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`</span></span> |
| <span data-ttu-id="c2d44-135">Focus</span><span class="sxs-lookup"><span data-stu-id="c2d44-135">Focus</span></span>            | `FocusEventArgs`     | <span data-ttu-id="c2d44-136">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span><span class="sxs-lookup"><span data-stu-id="c2d44-136">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span></span><br><br><span data-ttu-id="c2d44-137">Non include il supporto `relatedTarget`per.</span><span class="sxs-lookup"><span data-stu-id="c2d44-137">Doesn't include support for `relatedTarget`.</span></span> |
| <span data-ttu-id="c2d44-138">Input</span><span class="sxs-lookup"><span data-stu-id="c2d44-138">Input</span></span>            | `ChangeEventArgs`    | <span data-ttu-id="c2d44-139">`onchange`, `oninput`</span><span class="sxs-lookup"><span data-stu-id="c2d44-139">`onchange`, `oninput`</span></span> |
| <span data-ttu-id="c2d44-140">Tastiera</span><span class="sxs-lookup"><span data-stu-id="c2d44-140">Keyboard</span></span>         | `KeyboardEventArgs`  | <span data-ttu-id="c2d44-141">`onkeydown`, `onkeypress`, `onkeyup`</span><span class="sxs-lookup"><span data-stu-id="c2d44-141">`onkeydown`, `onkeypress`, `onkeyup`</span></span> |
| <span data-ttu-id="c2d44-142">Mouse</span><span class="sxs-lookup"><span data-stu-id="c2d44-142">Mouse</span></span>            | `MouseEventArgs`     | <span data-ttu-id="c2d44-143">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span><span class="sxs-lookup"><span data-stu-id="c2d44-143">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span></span> |
| <span data-ttu-id="c2d44-144">Puntatore del mouse</span><span class="sxs-lookup"><span data-stu-id="c2d44-144">Mouse pointer</span></span>    | `PointerEventArgs`   | <span data-ttu-id="c2d44-145">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span><span class="sxs-lookup"><span data-stu-id="c2d44-145">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span></span> |
| <span data-ttu-id="c2d44-146">Rotellina del mouse</span><span class="sxs-lookup"><span data-stu-id="c2d44-146">Mouse wheel</span></span>      | `WheelEventArgs`     | <span data-ttu-id="c2d44-147">`onwheel`, `onmousewheel`</span><span class="sxs-lookup"><span data-stu-id="c2d44-147">`onwheel`, `onmousewheel`</span></span> |
| <span data-ttu-id="c2d44-148">Avanzamento</span><span class="sxs-lookup"><span data-stu-id="c2d44-148">Progress</span></span>         | `ProgressEventArgs`  | <span data-ttu-id="c2d44-149">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span><span class="sxs-lookup"><span data-stu-id="c2d44-149">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span></span> |
| <span data-ttu-id="c2d44-150">Tocco</span><span class="sxs-lookup"><span data-stu-id="c2d44-150">Touch</span></span>            | `TouchEventArgs`     | <span data-ttu-id="c2d44-151">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span><span class="sxs-lookup"><span data-stu-id="c2d44-151">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span></span><br><br><span data-ttu-id="c2d44-152">`TouchPoint`rappresenta un singolo punto di contatto su un dispositivo sensibile al tocco.</span><span class="sxs-lookup"><span data-stu-id="c2d44-152">`TouchPoint` represents a single contact point on a touch-sensitive device.</span></span> |

<span data-ttu-id="c2d44-153">Per altre informazioni, vedere le seguenti risorse:</span><span class="sxs-lookup"><span data-stu-id="c2d44-153">For more information, see the following resources:</span></span>

* <span data-ttu-id="c2d44-154">[Classi EventArgs nell'origine riferimento ASP.NET Core (ramo DotNet/aspnetcore Release/3.1)](https://github.com/dotnet/aspnetcore/tree/release/3.1/src/Components/Web/src/Web).</span><span class="sxs-lookup"><span data-stu-id="c2d44-154">[EventArgs classes in the ASP.NET Core reference source (dotnet/aspnetcore release/3.1 branch)](https://github.com/dotnet/aspnetcore/tree/release/3.1/src/Components/Web/src/Web).</span></span>
* <span data-ttu-id="c2d44-155">[MDN Web docs: GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers) &ndash; include informazioni su quali elementi HTML supportano ogni evento DOM.</span><span class="sxs-lookup"><span data-stu-id="c2d44-155">[MDN web docs: GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers) &ndash; Includes information on which HTML elements support each DOM event.</span></span>

## <a name="lambda-expressions"></a><span data-ttu-id="c2d44-156">Espressioni lambda</span><span class="sxs-lookup"><span data-stu-id="c2d44-156">Lambda expressions</span></span>

<span data-ttu-id="c2d44-157">È inoltre possibile utilizzare le [espressioni lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions) :</span><span class="sxs-lookup"><span data-stu-id="c2d44-157">[Lambda expressions](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions) can also be used:</span></span>

```razor
<button @onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

<span data-ttu-id="c2d44-158">Spesso è consigliabile chiudere i valori aggiuntivi, ad esempio quando si esegue l'iterazione su un set di elementi.</span><span class="sxs-lookup"><span data-stu-id="c2d44-158">It's often convenient to close over additional values, such as when iterating over a set of elements.</span></span> <span data-ttu-id="c2d44-159">Nell'esempio seguente vengono creati tre pulsanti, ciascuno dei quali `UpdateHeading` chiama il passaggio di un`MouseEventArgs`argomento di evento () e`buttonNumber`il relativo numero di pulsante () quando vengono selezionati nell'interfaccia utente:</span><span class="sxs-lookup"><span data-stu-id="c2d44-159">The following example creates three buttons, each of which calls `UpdateHeading` passing an event argument (`MouseEventArgs`) and its button number (`buttonNumber`) when selected in the UI:</span></span>

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
> <span data-ttu-id="c2d44-160">**Non** usare la variabile di ciclo (`i`) in un `for` ciclo direttamente in un'espressione lambda.</span><span class="sxs-lookup"><span data-stu-id="c2d44-160">Do **not** use the loop variable (`i`) in a `for` loop directly in a lambda expression.</span></span> <span data-ttu-id="c2d44-161">In caso contrario, la stessa variabile viene utilizzata da tutte `i`le espressioni lambda causando che il valore di sia lo stesso in tutte le espressioni lambda.</span><span class="sxs-lookup"><span data-stu-id="c2d44-161">Otherwise the same variable is used by all lambda expressions causing `i`'s value to be the same in all lambdas.</span></span> <span data-ttu-id="c2d44-162">Acquisire sempre il relativo valore in una variabile locale`buttonNumber` (nell'esempio precedente) e quindi usarlo.</span><span class="sxs-lookup"><span data-stu-id="c2d44-162">Always capture its value in a local variable (`buttonNumber` in the preceding example) and then use it.</span></span>

## <a name="eventcallback"></a><span data-ttu-id="c2d44-163">EventCallback</span><span class="sxs-lookup"><span data-stu-id="c2d44-163">EventCallback</span></span>

<span data-ttu-id="c2d44-164">Uno scenario comune con i componenti annidati è la volontà di eseguire il metodo di un componente padre quando si verifica&mdash;un evento del componente figlio `onclick` , ad esempio, quando si verifica un evento nell'elemento figlio.</span><span class="sxs-lookup"><span data-stu-id="c2d44-164">A common scenario with nested components is the desire to run a parent component's method when a child component event occurs&mdash;for example, when an `onclick` event occurs in the child.</span></span> <span data-ttu-id="c2d44-165">Per esporre gli eventi tra i componenti, `EventCallback`usare un oggetto.</span><span class="sxs-lookup"><span data-stu-id="c2d44-165">To expose events across components, use an `EventCallback`.</span></span> <span data-ttu-id="c2d44-166">Un componente padre può assegnare un metodo di callback a un componente figlio `EventCallback`.</span><span class="sxs-lookup"><span data-stu-id="c2d44-166">A parent component can assign a callback method to a child component's `EventCallback`.</span></span>

<span data-ttu-id="c2d44-167">`ChildComponent` Nell'app di esempio (*Components/ChildComponent. Razor*) viene illustrato il modo in `onclick` cui il gestore di un pulsante viene `EventCallback` configurato per ricevere un delegato `ParentComponent`dall'oggetto dell'esempio.</span><span class="sxs-lookup"><span data-stu-id="c2d44-167">The `ChildComponent` in the sample app (*Components/ChildComponent.razor*) demonstrates how a button's `onclick` handler is set up to receive an `EventCallback` delegate from the sample's `ParentComponent`.</span></span> <span data-ttu-id="c2d44-168">`EventCallback` Viene digitato `MouseEventArgs`con, appropriato per un `onclick` evento da un dispositivo periferico:</span><span class="sxs-lookup"><span data-stu-id="c2d44-168">The `EventCallback` is typed with `MouseEventArgs`, which is appropriate for an `onclick` event from a peripheral device:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=5-7,17-18)]

<span data-ttu-id="c2d44-169">`ParentComponent` Imposta l'oggetto `EventCallback<T>` (`OnClickCallback`) del figlio sul relativo `ShowMessage` metodo.</span><span class="sxs-lookup"><span data-stu-id="c2d44-169">The `ParentComponent` sets the child's `EventCallback<T>` (`OnClickCallback`) to its `ShowMessage` method.</span></span>

<span data-ttu-id="c2d44-170">*Pages/ParentComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="c2d44-170">*Pages/ParentComponent.razor*:</span></span>

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

<span data-ttu-id="c2d44-171">Quando il pulsante è selezionato in `ChildComponent`:</span><span class="sxs-lookup"><span data-stu-id="c2d44-171">When the button is selected in the `ChildComponent`:</span></span>

* <span data-ttu-id="c2d44-172">Viene `ParentComponent`chiamato `ShowMessage` il metodo di.</span><span class="sxs-lookup"><span data-stu-id="c2d44-172">The `ParentComponent`'s `ShowMessage` method is called.</span></span> <span data-ttu-id="c2d44-173">`messageText`viene aggiornato e visualizzato nell'oggetto `ParentComponent`.</span><span class="sxs-lookup"><span data-stu-id="c2d44-173">`messageText` is updated and displayed in the `ParentComponent`.</span></span>
* <span data-ttu-id="c2d44-174">Una chiamata a [StateHasChanged](xref:blazor/lifecycle#state-changes) non è obbligatoria nel metodo del callback (`ShowMessage`).</span><span class="sxs-lookup"><span data-stu-id="c2d44-174">A call to [StateHasChanged](xref:blazor/lifecycle#state-changes) isn't required in the callback's method (`ShowMessage`).</span></span> <span data-ttu-id="c2d44-175">`StateHasChanged`viene chiamato automaticamente per eseguire il rendering `ParentComponent`di, proprio come gli eventi figlio attivano il rendering dei componenti nei gestori eventi che vengono eseguiti all'interno dell'elemento figlio.</span><span class="sxs-lookup"><span data-stu-id="c2d44-175">`StateHasChanged` is called automatically to rerender the `ParentComponent`, just as child events trigger component rerendering in event handlers that execute within the child.</span></span>

<span data-ttu-id="c2d44-176">`EventCallback`e `EventCallback<T>` consentono delegati asincroni.</span><span class="sxs-lookup"><span data-stu-id="c2d44-176">`EventCallback` and `EventCallback<T>` permit asynchronous delegates.</span></span> <span data-ttu-id="c2d44-177">`EventCallback<T>`è fortemente tipizzato e richiede un tipo di argomento specifico.</span><span class="sxs-lookup"><span data-stu-id="c2d44-177">`EventCallback<T>` is strongly typed and requires a specific argument type.</span></span> <span data-ttu-id="c2d44-178">`EventCallback`è debolmente tipizzato e consente qualsiasi tipo di argomento.</span><span class="sxs-lookup"><span data-stu-id="c2d44-178">`EventCallback` is weakly typed and allows any argument type.</span></span>

```razor
<ChildComponent 
    OnClickCallback="@(async () => { await Task.Yield(); messageText = "Blaze It!"; })" />
```

<span data-ttu-id="c2d44-179">Richiamare `EventCallback` o `EventCallback<T>` con `InvokeAsync` e attendere: <xref:System.Threading.Tasks.Task></span><span class="sxs-lookup"><span data-stu-id="c2d44-179">Invoke an `EventCallback` or `EventCallback<T>` with `InvokeAsync` and await the <xref:System.Threading.Tasks.Task>:</span></span>

```csharp
await callback.InvokeAsync(arg);
```

<span data-ttu-id="c2d44-180">Usare `EventCallback` e `EventCallback<T>` per la gestione degli eventi e i parametri del componente di associazione.</span><span class="sxs-lookup"><span data-stu-id="c2d44-180">Use `EventCallback` and `EventCallback<T>` for event handling and binding component parameters.</span></span>

<span data-ttu-id="c2d44-181">Preferisci l'oggetto `EventCallback<T>` fortemente `EventCallback`tipizzato.</span><span class="sxs-lookup"><span data-stu-id="c2d44-181">Prefer the strongly typed `EventCallback<T>` over `EventCallback`.</span></span> <span data-ttu-id="c2d44-182">`EventCallback<T>`fornisce un migliore feedback sugli errori agli utenti del componente.</span><span class="sxs-lookup"><span data-stu-id="c2d44-182">`EventCallback<T>` provides better error feedback to users of the component.</span></span> <span data-ttu-id="c2d44-183">Analogamente ad altri gestori di eventi dell'interfaccia utente, la specifica del parametro dell'evento è facoltativa.</span><span class="sxs-lookup"><span data-stu-id="c2d44-183">Similar to other UI event handlers, specifying the event parameter is optional.</span></span> <span data-ttu-id="c2d44-184">Usare `EventCallback` quando non è stato passato alcun valore al callback.</span><span class="sxs-lookup"><span data-stu-id="c2d44-184">Use `EventCallback` when there's no value passed to the callback.</span></span>

## <a name="prevent-default-actions"></a><span data-ttu-id="c2d44-185">Impedisci azioni predefinite</span><span class="sxs-lookup"><span data-stu-id="c2d44-185">Prevent default actions</span></span>

<span data-ttu-id="c2d44-186">Utilizzare l' [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) attributo Directive per impedire l'azione predefinita per un evento.</span><span class="sxs-lookup"><span data-stu-id="c2d44-186">Use the [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) directive attribute to prevent the default action for an event.</span></span>

<span data-ttu-id="c2d44-187">Quando si seleziona un tasto in un dispositivo di input e lo stato attivo dell'elemento si trova in una casella di testo, in un browser viene in genere visualizzato il carattere della chiave nella casella di testo.</span><span class="sxs-lookup"><span data-stu-id="c2d44-187">When a key is selected on an input device and the element focus is on a text box, a browser normally displays the key's character in the text box.</span></span> <span data-ttu-id="c2d44-188">Nell'esempio seguente viene impedito il comportamento predefinito specificando l' `@onkeypress:preventDefault` attributo della direttiva.</span><span class="sxs-lookup"><span data-stu-id="c2d44-188">In the following example, the default behavior is prevented by specifying the `@onkeypress:preventDefault` directive attribute.</span></span> <span data-ttu-id="c2d44-189">Il contatore viene incrementato e la **+** chiave non viene acquisita `<input>` nel valore dell'elemento:</span><span class="sxs-lookup"><span data-stu-id="c2d44-189">The counter increments, and the **+** key isn't captured into the `<input>` element's value:</span></span>

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

<span data-ttu-id="c2d44-190">La specifica `@on{EVENT}:preventDefault` dell'attributo senza un valore equivale a `@on{EVENT}:preventDefault="true"`.</span><span class="sxs-lookup"><span data-stu-id="c2d44-190">Specifying the `@on{EVENT}:preventDefault` attribute without a value is equivalent to `@on{EVENT}:preventDefault="true"`.</span></span>

<span data-ttu-id="c2d44-191">Il valore dell'attributo può anche essere un'espressione.</span><span class="sxs-lookup"><span data-stu-id="c2d44-191">The value of the attribute can also be an expression.</span></span> <span data-ttu-id="c2d44-192">`shouldPreventDefault` Nell'esempio seguente è `bool` un campo impostato su `true` o: `false`</span><span class="sxs-lookup"><span data-stu-id="c2d44-192">In the following example, `shouldPreventDefault` is a `bool` field set to either `true` or `false`:</span></span>

```razor
<input @onkeypress:preventDefault="shouldPreventDefault" />
```

<span data-ttu-id="c2d44-193">Per impedire l'azione predefinita, non è necessario un gestore eventi.</span><span class="sxs-lookup"><span data-stu-id="c2d44-193">An event handler isn't required to prevent the default action.</span></span> <span data-ttu-id="c2d44-194">Il gestore eventi e impedire scenari di azione predefiniti possono essere usati in modo indipendente.</span><span class="sxs-lookup"><span data-stu-id="c2d44-194">The event handler and prevent default action scenarios can be used independently.</span></span>

## <a name="stop-event-propagation"></a><span data-ttu-id="c2d44-195">Arresta propagazione eventi</span><span class="sxs-lookup"><span data-stu-id="c2d44-195">Stop event propagation</span></span>

<span data-ttu-id="c2d44-196">Utilizzare l' [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) attributo Directive per arrestare la propagazione degli eventi.</span><span class="sxs-lookup"><span data-stu-id="c2d44-196">Use the [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) directive attribute to stop event propagation.</span></span>

<span data-ttu-id="c2d44-197">Nell'esempio seguente la selezione della casella di controllo impedisce la propagazione degli eventi `<div>` click dal secondo figlio all'elemento `<div>`padre:</span><span class="sxs-lookup"><span data-stu-id="c2d44-197">In the following example, selecting the check box prevents click events from the second child `<div>` from propagating to the parent `<div>`:</span></span>

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
