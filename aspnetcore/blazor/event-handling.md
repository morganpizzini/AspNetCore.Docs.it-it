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
# <a name="aspnet-core-blazor-event-handling"></a><span data-ttu-id="a93a9-103">gestione degli eventi ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="a93a9-103">ASP.NET Core Blazor event handling</span></span>

<span data-ttu-id="a93a9-104">Di [Luke Latham](https://github.com/guardrex) e [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="a93a9-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="a93a9-105">I componenti Razor forniscono funzionalità di gestione degli eventi.</span><span class="sxs-lookup"><span data-stu-id="a93a9-105">Razor components provide event handling features.</span></span> <span data-ttu-id="a93a9-106">Per un attributo [`@on{EVENT}`](xref:mvc/views/razor#onevent) di elemento `@onclick`HTML denominato (ad esempio, ) con un valore tipizzato da delegato, un componente Razor considera il valore dell'attributo come un gestore eventi.</span><span class="sxs-lookup"><span data-stu-id="a93a9-106">For an HTML element attribute named [`@on{EVENT}`](xref:mvc/views/razor#onevent) (for example, `@onclick`) with a delegate-typed value, a Razor component treats the attribute's value as an event handler.</span></span>

<span data-ttu-id="a93a9-107">Il codice seguente `UpdateHeading` chiama il metodo quando il pulsante viene selezionato nell'interfaccia utente:The following code calls the method when the button is selected in the UI:</span><span class="sxs-lookup"><span data-stu-id="a93a9-107">The following code calls the `UpdateHeading` method when the button is selected in the UI:</span></span>

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

<span data-ttu-id="a93a9-108">Il codice seguente `CheckChanged` chiama il metodo quando la casella di controllo viene modificata nell'interfaccia utente:The following code calls the method when the check box is changed in the UI:</span><span class="sxs-lookup"><span data-stu-id="a93a9-108">The following code calls the `CheckChanged` method when the check box is changed in the UI:</span></span>

```razor
<input type="checkbox" class="form-check-input" @onchange="CheckChanged" />

@code {
    private void CheckChanged()
    {
        ...
    }
}
```

<span data-ttu-id="a93a9-109">I gestori eventi possono anche <xref:System.Threading.Tasks.Task>essere asincroni e restituire un oggetto .</span><span class="sxs-lookup"><span data-stu-id="a93a9-109">Event handlers can also be asynchronous and return a <xref:System.Threading.Tasks.Task>.</span></span> <span data-ttu-id="a93a9-110">Non è necessario chiamare manualmente [StateHasChanged](xref:blazor/lifecycle#state-changes).</span><span class="sxs-lookup"><span data-stu-id="a93a9-110">There's no need to manually call [StateHasChanged](xref:blazor/lifecycle#state-changes).</span></span> <span data-ttu-id="a93a9-111">Le eccezioni vengono registrate quando si verificano.</span><span class="sxs-lookup"><span data-stu-id="a93a9-111">Exceptions are logged when they occur.</span></span>

<span data-ttu-id="a93a9-112">Nell'esempio seguente, viene chiamato in modo asincrono quando viene selezionato il pulsante:In the following example, `UpdateHeading` is called asynchronously when the button is selected:</span><span class="sxs-lookup"><span data-stu-id="a93a9-112">In the following example, `UpdateHeading` is called asynchronously when the button is selected:</span></span>

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

## <a name="event-argument-types"></a><span data-ttu-id="a93a9-113">Tipi di argomenti di evento</span><span class="sxs-lookup"><span data-stu-id="a93a9-113">Event argument types</span></span>

<span data-ttu-id="a93a9-114">Per alcuni eventi, i tipi di argomento di evento sono consentiti.</span><span class="sxs-lookup"><span data-stu-id="a93a9-114">For some events, event argument types are permitted.</span></span> <span data-ttu-id="a93a9-115">La specifica di un tipo di evento nella chiamata al metodo è necessaria solo se il tipo di evento viene utilizzato nel metodo.</span><span class="sxs-lookup"><span data-stu-id="a93a9-115">Specifying an event type in the method call is only necessary if the event type is used in the method.</span></span>

<span data-ttu-id="a93a9-116">I `EventArgs` supportati sono illustrati nella tabella seguente.</span><span class="sxs-lookup"><span data-stu-id="a93a9-116">Supported `EventArgs` are shown in the following table.</span></span>

| <span data-ttu-id="a93a9-117">Event</span><span class="sxs-lookup"><span data-stu-id="a93a9-117">Event</span></span>            | <span data-ttu-id="a93a9-118">Classe</span><span class="sxs-lookup"><span data-stu-id="a93a9-118">Class</span></span>                | <span data-ttu-id="a93a9-119">Eventi e note DOM</span><span class="sxs-lookup"><span data-stu-id="a93a9-119">DOM events and notes</span></span> |
| ---------------- | -------------------- | -------------------- |
| <span data-ttu-id="a93a9-120">Appunti</span><span class="sxs-lookup"><span data-stu-id="a93a9-120">Clipboard</span></span>        | `ClipboardEventArgs` | <span data-ttu-id="a93a9-121">`oncut`, `oncopy`, `onpaste`</span><span class="sxs-lookup"><span data-stu-id="a93a9-121">`oncut`, `oncopy`, `onpaste`</span></span> |
| <span data-ttu-id="a93a9-122">Trascinamento</span><span class="sxs-lookup"><span data-stu-id="a93a9-122">Drag</span></span>             | `DragEventArgs`      | <span data-ttu-id="a93a9-123">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span><span class="sxs-lookup"><span data-stu-id="a93a9-123">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span></span><br><br><span data-ttu-id="a93a9-124">`DataTransfer`e `DataTransferItem` tenere premuti i dati dell'elemento trascinato.</span><span class="sxs-lookup"><span data-stu-id="a93a9-124">`DataTransfer` and `DataTransferItem` hold dragged item data.</span></span> |
| <span data-ttu-id="a93a9-125">Errore</span><span class="sxs-lookup"><span data-stu-id="a93a9-125">Error</span></span>            | `ErrorEventArgs`     | `onerror` |
| <span data-ttu-id="a93a9-126">Event</span><span class="sxs-lookup"><span data-stu-id="a93a9-126">Event</span></span>            | `EventArgs`          | <span data-ttu-id="a93a9-127">*Generale*</span><span class="sxs-lookup"><span data-stu-id="a93a9-127">*General*</span></span><br><span data-ttu-id="a93a9-128">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onended`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span><span class="sxs-lookup"><span data-stu-id="a93a9-128">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onended`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span></span><br><br><span data-ttu-id="a93a9-129">*Appunti*</span><span class="sxs-lookup"><span data-stu-id="a93a9-129">*Clipboard*</span></span><br><span data-ttu-id="a93a9-130">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span><span class="sxs-lookup"><span data-stu-id="a93a9-130">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span></span><br><br><span data-ttu-id="a93a9-131">*Input*</span><span class="sxs-lookup"><span data-stu-id="a93a9-131">*Input*</span></span><br><span data-ttu-id="a93a9-132">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`</span><span class="sxs-lookup"><span data-stu-id="a93a9-132">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`</span></span><br><br><span data-ttu-id="a93a9-133">*Supporti*</span><span class="sxs-lookup"><span data-stu-id="a93a9-133">*Media*</span></span><br><span data-ttu-id="a93a9-134">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`</span><span class="sxs-lookup"><span data-stu-id="a93a9-134">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`</span></span> |
| <span data-ttu-id="a93a9-135">Focus</span><span class="sxs-lookup"><span data-stu-id="a93a9-135">Focus</span></span>            | `FocusEventArgs`     | <span data-ttu-id="a93a9-136">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span><span class="sxs-lookup"><span data-stu-id="a93a9-136">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span></span><br><br><span data-ttu-id="a93a9-137">Non include il `relatedTarget`supporto per .</span><span class="sxs-lookup"><span data-stu-id="a93a9-137">Doesn't include support for `relatedTarget`.</span></span> |
| <span data-ttu-id="a93a9-138">Input</span><span class="sxs-lookup"><span data-stu-id="a93a9-138">Input</span></span>            | `ChangeEventArgs`    | <span data-ttu-id="a93a9-139">`onchange`, `oninput`</span><span class="sxs-lookup"><span data-stu-id="a93a9-139">`onchange`, `oninput`</span></span> |
| <span data-ttu-id="a93a9-140">Tastiera</span><span class="sxs-lookup"><span data-stu-id="a93a9-140">Keyboard</span></span>         | `KeyboardEventArgs`  | <span data-ttu-id="a93a9-141">`onkeydown`, `onkeypress`, `onkeyup`</span><span class="sxs-lookup"><span data-stu-id="a93a9-141">`onkeydown`, `onkeypress`, `onkeyup`</span></span> |
| <span data-ttu-id="a93a9-142">Mouse</span><span class="sxs-lookup"><span data-stu-id="a93a9-142">Mouse</span></span>            | `MouseEventArgs`     | <span data-ttu-id="a93a9-143">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span><span class="sxs-lookup"><span data-stu-id="a93a9-143">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span></span> |
| <span data-ttu-id="a93a9-144">Puntatore del mouse</span><span class="sxs-lookup"><span data-stu-id="a93a9-144">Mouse pointer</span></span>    | `PointerEventArgs`   | <span data-ttu-id="a93a9-145">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span><span class="sxs-lookup"><span data-stu-id="a93a9-145">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span></span> |
| <span data-ttu-id="a93a9-146">Rotellina del mouse</span><span class="sxs-lookup"><span data-stu-id="a93a9-146">Mouse wheel</span></span>      | `WheelEventArgs`     | <span data-ttu-id="a93a9-147">`onwheel`, `onmousewheel`</span><span class="sxs-lookup"><span data-stu-id="a93a9-147">`onwheel`, `onmousewheel`</span></span> |
| <span data-ttu-id="a93a9-148">Progress</span><span class="sxs-lookup"><span data-stu-id="a93a9-148">Progress</span></span>         | `ProgressEventArgs`  | <span data-ttu-id="a93a9-149">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span><span class="sxs-lookup"><span data-stu-id="a93a9-149">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span></span> |
| <span data-ttu-id="a93a9-150">Tocco</span><span class="sxs-lookup"><span data-stu-id="a93a9-150">Touch</span></span>            | `TouchEventArgs`     | <span data-ttu-id="a93a9-151">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span><span class="sxs-lookup"><span data-stu-id="a93a9-151">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span></span><br><br><span data-ttu-id="a93a9-152">`TouchPoint`rappresenta un singolo punto di contatto su un dispositivo sensibile al tocco.</span><span class="sxs-lookup"><span data-stu-id="a93a9-152">`TouchPoint` represents a single contact point on a touch-sensitive device.</span></span> |

<span data-ttu-id="a93a9-153">Per altre informazioni, vedere le seguenti risorse:</span><span class="sxs-lookup"><span data-stu-id="a93a9-153">For more information, see the following resources:</span></span>

* <span data-ttu-id="a93a9-154">[Classi EventArgs nell'origine di riferimento ASP.NET Core (ramo dotnet/aspnetcore release/3.1).](https://github.com/dotnet/aspnetcore/tree/release/3.1/src/Components/Web/src/Web)</span><span class="sxs-lookup"><span data-stu-id="a93a9-154">[EventArgs classes in the ASP.NET Core reference source (dotnet/aspnetcore release/3.1 branch)](https://github.com/dotnet/aspnetcore/tree/release/3.1/src/Components/Web/src/Web).</span></span>
* <span data-ttu-id="a93a9-155">[Documenti Web MDN: GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers) &ndash; Include informazioni su quali elementi HTML supportano ogni evento DOM.</span><span class="sxs-lookup"><span data-stu-id="a93a9-155">[MDN web docs: GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers) &ndash; Includes information on which HTML elements support each DOM event.</span></span>

## <a name="lambda-expressions"></a><span data-ttu-id="a93a9-156">Espressioni lambda</span><span class="sxs-lookup"><span data-stu-id="a93a9-156">Lambda expressions</span></span>

<span data-ttu-id="a93a9-157">[Le espressioni lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions) possono anche essere usate:Lambda expressions can also be used:</span><span class="sxs-lookup"><span data-stu-id="a93a9-157">[Lambda expressions](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions) can also be used:</span></span>

```razor
<button @onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

<span data-ttu-id="a93a9-158">È spesso conveniente chiudere su valori aggiuntivi, ad esempio quando si scorre un set di elementi.</span><span class="sxs-lookup"><span data-stu-id="a93a9-158">It's often convenient to close over additional values, such as when iterating over a set of elements.</span></span> <span data-ttu-id="a93a9-159">Nell'esempio seguente vengono creati tre `UpdateHeading` pulsanti,`MouseEventArgs`ognuno dei quali`buttonNumber`chiama il passaggio di un argomento dell'evento ( ) e il relativo numero di pulsante ( ) quando viene selezionato nell'interfaccia utente:</span><span class="sxs-lookup"><span data-stu-id="a93a9-159">The following example creates three buttons, each of which calls `UpdateHeading` passing an event argument (`MouseEventArgs`) and its button number (`buttonNumber`) when selected in the UI:</span></span>

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
> <span data-ttu-id="a93a9-160">**Non** usare la variabile`i`di `for` ciclo ( ) in un ciclo direttamente in un'espressione lambda.</span><span class="sxs-lookup"><span data-stu-id="a93a9-160">Do **not** use the loop variable (`i`) in a `for` loop directly in a lambda expression.</span></span> <span data-ttu-id="a93a9-161">In caso contrario, la stessa `i`variabile viene utilizzata da tutte le espressioni lambda facendo sì che il valore di 'sia lo stesso in tutte le espressioni lambda.</span><span class="sxs-lookup"><span data-stu-id="a93a9-161">Otherwise the same variable is used by all lambda expressions causing `i`'s value to be the same in all lambdas.</span></span> <span data-ttu-id="a93a9-162">Acquisire sempre il valore in`buttonNumber` una variabile locale (nell'esempio precedente) e quindi utilizzarlo.</span><span class="sxs-lookup"><span data-stu-id="a93a9-162">Always capture its value in a local variable (`buttonNumber` in the preceding example) and then use it.</span></span>

## <a name="eventcallback"></a><span data-ttu-id="a93a9-163">EventCallback</span><span class="sxs-lookup"><span data-stu-id="a93a9-163">EventCallback</span></span>

<span data-ttu-id="a93a9-164">Uno scenario comune con componenti annidati è il desiderio di eseguire&mdash;il metodo `onclick` di un componente padre quando si verifica un evento componente figlio, ad esempio, quando si verifica un evento nell'elemento figlio.</span><span class="sxs-lookup"><span data-stu-id="a93a9-164">A common scenario with nested components is the desire to run a parent component's method when a child component event occurs&mdash;for example, when an `onclick` event occurs in the child.</span></span> <span data-ttu-id="a93a9-165">Per esporre eventi tra `EventCallback`componenti, utilizzare un file .</span><span class="sxs-lookup"><span data-stu-id="a93a9-165">To expose events across components, use an `EventCallback`.</span></span> <span data-ttu-id="a93a9-166">Un componente padre può assegnare un metodo `EventCallback`di callback a .</span><span class="sxs-lookup"><span data-stu-id="a93a9-166">A parent component can assign a callback method to a child component's `EventCallback`.</span></span>

<span data-ttu-id="a93a9-167">Nell'app `ChildComponent` di esempio (*Components/ChildComponent.razor*) `onclick` viene illustrato come il `EventCallback` gestore di `ParentComponent`un pulsante è impostato per ricevere un delegato dall'esempio .</span><span class="sxs-lookup"><span data-stu-id="a93a9-167">The `ChildComponent` in the sample app (*Components/ChildComponent.razor*) demonstrates how a button's `onclick` handler is set up to receive an `EventCallback` delegate from the sample's `ParentComponent`.</span></span> <span data-ttu-id="a93a9-168">Viene `EventCallback` digitato `MouseEventArgs`con , appropriato `onclick` per un evento da una periferica:</span><span class="sxs-lookup"><span data-stu-id="a93a9-168">The `EventCallback` is typed with `MouseEventArgs`, which is appropriate for an `onclick` event from a peripheral device:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=5-7,17-18)]

<span data-ttu-id="a93a9-169">Il `ParentComponent` imposta il `EventCallback<T>` bambino`OnClickCallback`( `ShowMessage` ) sul relativo metodo.</span><span class="sxs-lookup"><span data-stu-id="a93a9-169">The `ParentComponent` sets the child's `EventCallback<T>` (`OnClickCallback`) to its `ShowMessage` method.</span></span>

<span data-ttu-id="a93a9-170">*Pages/ParentComponent.razor*:</span><span class="sxs-lookup"><span data-stu-id="a93a9-170">*Pages/ParentComponent.razor*:</span></span>

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

<span data-ttu-id="a93a9-171">Quando il pulsante `ChildComponent`è selezionato nella sezione :</span><span class="sxs-lookup"><span data-stu-id="a93a9-171">When the button is selected in the `ChildComponent`:</span></span>

* <span data-ttu-id="a93a9-172">Viene `ParentComponent`chiamato `ShowMessage` il metodo 's.</span><span class="sxs-lookup"><span data-stu-id="a93a9-172">The `ParentComponent`'s `ShowMessage` method is called.</span></span> <span data-ttu-id="a93a9-173">`_messageText`viene aggiornato e visualizzato `ParentComponent`nel file .</span><span class="sxs-lookup"><span data-stu-id="a93a9-173">`_messageText` is updated and displayed in the `ParentComponent`.</span></span>
* <span data-ttu-id="a93a9-174">Una chiamata a [StateHasChanged](xref:blazor/lifecycle#state-changes) non è necessaria nel`ShowMessage`metodo del callback ( ).</span><span class="sxs-lookup"><span data-stu-id="a93a9-174">A call to [StateHasChanged](xref:blazor/lifecycle#state-changes) isn't required in the callback's method (`ShowMessage`).</span></span> <span data-ttu-id="a93a9-175">`StateHasChanged`viene chiamato automaticamente per `ParentComponent`eseguire nuovamente il rendering di , proprio come gli eventi figlio attivano il rendering del componente nei gestori eventi eseguiti all'interno dell'elemento figlio.</span><span class="sxs-lookup"><span data-stu-id="a93a9-175">`StateHasChanged` is called automatically to rerender the `ParentComponent`, just as child events trigger component rerendering in event handlers that execute within the child.</span></span>

<span data-ttu-id="a93a9-176">`EventCallback`e `EventCallback<T>` consentire delegati asincroni.</span><span class="sxs-lookup"><span data-stu-id="a93a9-176">`EventCallback` and `EventCallback<T>` permit asynchronous delegates.</span></span> <span data-ttu-id="a93a9-177">`EventCallback<T>`è fortemente tipizzato e richiede un tipo di argomento specifico.</span><span class="sxs-lookup"><span data-stu-id="a93a9-177">`EventCallback<T>` is strongly typed and requires a specific argument type.</span></span> <span data-ttu-id="a93a9-178">`EventCallback`è debolmente tipizzato e consente qualsiasi tipo di argomento.</span><span class="sxs-lookup"><span data-stu-id="a93a9-178">`EventCallback` is weakly typed and allows any argument type.</span></span>

```razor
<ChildComponent 
    OnClickCallback="@(async () => { await Task.Yield(); _messageText = "Blaze It!"; })" />
```

<span data-ttu-id="a93a9-179">Richiamare `EventCallback` `EventCallback<T>` un `InvokeAsync` oggetto o <xref:System.Threading.Tasks.Task>con e attendere il :</span><span class="sxs-lookup"><span data-stu-id="a93a9-179">Invoke an `EventCallback` or `EventCallback<T>` with `InvokeAsync` and await the <xref:System.Threading.Tasks.Task>:</span></span>

```csharp
await callback.InvokeAsync(arg);
```

<span data-ttu-id="a93a9-180">Utilizzare `EventCallback` `EventCallback<T>` e per la gestione degli eventi e i parametri del componente di associazione.</span><span class="sxs-lookup"><span data-stu-id="a93a9-180">Use `EventCallback` and `EventCallback<T>` for event handling and binding component parameters.</span></span>

<span data-ttu-id="a93a9-181">Preferire il fortemente tipizzato `EventCallback<T>` su `EventCallback`.</span><span class="sxs-lookup"><span data-stu-id="a93a9-181">Prefer the strongly typed `EventCallback<T>` over `EventCallback`.</span></span> <span data-ttu-id="a93a9-182">`EventCallback<T>`fornisce un feedback di errore migliore agli utenti del componente.</span><span class="sxs-lookup"><span data-stu-id="a93a9-182">`EventCallback<T>` provides better error feedback to users of the component.</span></span> <span data-ttu-id="a93a9-183">Simile ad altri gestori eventi dell'interfaccia utente, specificare il parametro dell'evento è facoltativo.</span><span class="sxs-lookup"><span data-stu-id="a93a9-183">Similar to other UI event handlers, specifying the event parameter is optional.</span></span> <span data-ttu-id="a93a9-184">Utilizzare `EventCallback` quando non è presente alcun valore passato al callback.</span><span class="sxs-lookup"><span data-stu-id="a93a9-184">Use `EventCallback` when there's no value passed to the callback.</span></span>

## <a name="prevent-default-actions"></a><span data-ttu-id="a93a9-185">Impedisci azioni predefinite</span><span class="sxs-lookup"><span data-stu-id="a93a9-185">Prevent default actions</span></span>

<span data-ttu-id="a93a9-186">Utilizzare [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) l'attributo della direttiva per impedire l'azione predefinita per un evento.</span><span class="sxs-lookup"><span data-stu-id="a93a9-186">Use the [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) directive attribute to prevent the default action for an event.</span></span>

<span data-ttu-id="a93a9-187">Quando si seleziona un tasto su un dispositivo di input e lo stato attivo dell'elemento si trova su una casella di testo, un browser in genere visualizza il carattere del tasto nella casella di testo.</span><span class="sxs-lookup"><span data-stu-id="a93a9-187">When a key is selected on an input device and the element focus is on a text box, a browser normally displays the key's character in the text box.</span></span> <span data-ttu-id="a93a9-188">Nell'esempio seguente, il comportamento predefinito viene `@onkeypress:preventDefault` impedito specificando l'attributo della direttiva.</span><span class="sxs-lookup"><span data-stu-id="a93a9-188">In the following example, the default behavior is prevented by specifying the `@onkeypress:preventDefault` directive attribute.</span></span> <span data-ttu-id="a93a9-189">Il contatore viene **+** incrementato e la `<input>` chiave non viene acquisita nel valore dell'elemento:</span><span class="sxs-lookup"><span data-stu-id="a93a9-189">The counter increments, and the **+** key isn't captured into the `<input>` element's value:</span></span>

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

<span data-ttu-id="a93a9-190">Specificare `@on{EVENT}:preventDefault` l'attributo senza `@on{EVENT}:preventDefault="true"`un valore equivale a .</span><span class="sxs-lookup"><span data-stu-id="a93a9-190">Specifying the `@on{EVENT}:preventDefault` attribute without a value is equivalent to `@on{EVENT}:preventDefault="true"`.</span></span>

<span data-ttu-id="a93a9-191">Il valore dell'attributo può anche essere un'espressione.</span><span class="sxs-lookup"><span data-stu-id="a93a9-191">The value of the attribute can also be an expression.</span></span> <span data-ttu-id="a93a9-192">Nell'esempio `_shouldPreventDefault` seguente, `bool` è un `true` campo `false`impostato su o :</span><span class="sxs-lookup"><span data-stu-id="a93a9-192">In the following example, `_shouldPreventDefault` is a `bool` field set to either `true` or `false`:</span></span>

```razor
<input @onkeypress:preventDefault="_shouldPreventDefault" />
```

<span data-ttu-id="a93a9-193">Un gestore eventi non è necessario per impedire l'azione predefinita.</span><span class="sxs-lookup"><span data-stu-id="a93a9-193">An event handler isn't required to prevent the default action.</span></span> <span data-ttu-id="a93a9-194">Il gestore eventi e impedire scenari di azione predefiniti possono essere utilizzati in modo indipendente.</span><span class="sxs-lookup"><span data-stu-id="a93a9-194">The event handler and prevent default action scenarios can be used independently.</span></span>

## <a name="stop-event-propagation"></a><span data-ttu-id="a93a9-195">Interrompi propagazione eventi</span><span class="sxs-lookup"><span data-stu-id="a93a9-195">Stop event propagation</span></span>

<span data-ttu-id="a93a9-196">Utilizzare [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) l'attributo della direttiva per interrompere la propagazione degli eventi.</span><span class="sxs-lookup"><span data-stu-id="a93a9-196">Use the [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) directive attribute to stop event propagation.</span></span>

<span data-ttu-id="a93a9-197">Nell'esempio seguente, la selezione della casella di `<div>` controllo impedisce la `<div>`propagazione degli eventi Click dal secondo figlio all'elemento padre :</span><span class="sxs-lookup"><span data-stu-id="a93a9-197">In the following example, selecting the check box prevents click events from the second child `<div>` from propagating to the parent `<div>`:</span></span>

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
