---
<span data-ttu-id="f8d5a-101">title:' ASP.NET Core Blazor gestione eventi ' Author: guardrex Description:' informazioni sulle Blazor funzionalità di gestione degli eventi, inclusi i tipi di argomenti dell'evento, i callback degli eventi e la gestione degli eventi predefiniti del browser.</span><span class="sxs-lookup"><span data-stu-id="f8d5a-101">title: 'ASP.NET Core Blazor event handling' author: guardrex description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="f8d5a-102">monikerRange:' >= aspnetcore-3,1' ms. Author: Riande ms. Custom: MVC ms. Date: 06/04/2020 no-loc:</span><span class="sxs-lookup"><span data-stu-id="f8d5a-102">monikerRange: '>= aspnetcore-3.1' ms.author: riande ms.custom: mvc ms.date: 06/04/2020 no-loc:</span></span>
- <span data-ttu-id="f8d5a-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f8d5a-103">'Blazor'</span></span>
- <span data-ttu-id="f8d5a-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f8d5a-104">'Identity'</span></span>
- <span data-ttu-id="f8d5a-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f8d5a-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="f8d5a-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f8d5a-106">'Razor'</span></span>
- <span data-ttu-id="f8d5a-107">' SignalR ' UID: blazer/gestione eventi</span><span class="sxs-lookup"><span data-stu-id="f8d5a-107">'SignalR' uid: blazor/event-handling</span></span>

---
# <a name="aspnet-core-blazor-event-handling"></a><span data-ttu-id="f8d5a-108">BlazorGestione degli eventi ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f8d5a-108">ASP.NET Core Blazor event handling</span></span>

<span data-ttu-id="f8d5a-109">Di [Luke Latham](https://github.com/guardrex) e [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="f8d5a-109">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

Razor<span data-ttu-id="f8d5a-110">i componenti di forniscono funzionalità di gestione degli eventi.</span><span class="sxs-lookup"><span data-stu-id="f8d5a-110"> components provide event handling features.</span></span> <span data-ttu-id="f8d5a-111">Per un attributo dell'elemento HTML denominato, [`@on{EVENT}`](xref:mvc/views/razor#onevent) ad esempio, `@onclick` con un valore tipizzato come delegato, un Razor componente considera il valore dell'attributo come un gestore eventi.</span><span class="sxs-lookup"><span data-stu-id="f8d5a-111">For an HTML element attribute named [`@on{EVENT}`](xref:mvc/views/razor#onevent) (for example, `@onclick`) with a delegate-typed value, a Razor component treats the attribute's value as an event handler.</span></span>

<span data-ttu-id="f8d5a-112">Il codice seguente chiama il `UpdateHeading` metodo quando si seleziona il pulsante nell'interfaccia utente:</span><span class="sxs-lookup"><span data-stu-id="f8d5a-112">The following code calls the `UpdateHeading` method when the button is selected in the UI:</span></span>

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

<span data-ttu-id="f8d5a-113">Il codice seguente chiama il `CheckChanged` metodo quando la casella di controllo viene modificata nell'interfaccia utente:</span><span class="sxs-lookup"><span data-stu-id="f8d5a-113">The following code calls the `CheckChanged` method when the check box is changed in the UI:</span></span>

```razor
<input type="checkbox" class="form-check-input" @onchange="CheckChanged" />

@code {
    private void CheckChanged()
    {
        ...
    }
}
```

<span data-ttu-id="f8d5a-114">I gestori di eventi possono anche essere asincroni e restituire un oggetto <xref:System.Threading.Tasks.Task> .</span><span class="sxs-lookup"><span data-stu-id="f8d5a-114">Event handlers can also be asynchronous and return a <xref:System.Threading.Tasks.Task>.</span></span> <span data-ttu-id="f8d5a-115">Non è necessario chiamare manualmente [StateHasChanged](xref:blazor/lifecycle#state-changes).</span><span class="sxs-lookup"><span data-stu-id="f8d5a-115">There's no need to manually call [StateHasChanged](xref:blazor/lifecycle#state-changes).</span></span> <span data-ttu-id="f8d5a-116">Le eccezioni vengono registrate quando si verificano.</span><span class="sxs-lookup"><span data-stu-id="f8d5a-116">Exceptions are logged when they occur.</span></span>

<span data-ttu-id="f8d5a-117">Nell'esempio seguente, `UpdateHeading` viene chiamato in modo asincrono quando si seleziona il pulsante:</span><span class="sxs-lookup"><span data-stu-id="f8d5a-117">In the following example, `UpdateHeading` is called asynchronously when the button is selected:</span></span>

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

## <a name="event-argument-types"></a><span data-ttu-id="f8d5a-118">Tipi di argomenti dell'evento</span><span class="sxs-lookup"><span data-stu-id="f8d5a-118">Event argument types</span></span>

<span data-ttu-id="f8d5a-119">Per alcuni eventi, i tipi di argomento dell'evento sono consentiti.</span><span class="sxs-lookup"><span data-stu-id="f8d5a-119">For some events, event argument types are permitted.</span></span> <span data-ttu-id="f8d5a-120">La specifica di un tipo di evento nella chiamata al metodo è necessaria solo se il tipo di evento viene usato nel metodo.</span><span class="sxs-lookup"><span data-stu-id="f8d5a-120">Specifying an event type in the method call is only necessary if the event type is used in the method.</span></span>

<span data-ttu-id="f8d5a-121"><xref:System.EventArgs>I supportati sono riportati nella tabella seguente.</span><span class="sxs-lookup"><span data-stu-id="f8d5a-121">Supported <xref:System.EventArgs> are shown in the following table.</span></span>

| <span data-ttu-id="f8d5a-122">Event</span><span class="sxs-lookup"><span data-stu-id="f8d5a-122">Event</span></span>            | <span data-ttu-id="f8d5a-123">Class</span><span class="sxs-lookup"><span data-stu-id="f8d5a-123">Class</span></span>                | <span data-ttu-id="f8d5a-124">Eventi e note DOM</span><span class="sxs-lookup"><span data-stu-id="f8d5a-124">DOM events and notes</span></span> |
| ---------------- | -------------------- | -------------------- |
| <span data-ttu-id="f8d5a-125">Appunti</span><span class="sxs-lookup"><span data-stu-id="f8d5a-125">Clipboard</span></span>        | <xref:Microsoft.AspNetCore.Components.Web.ClipboardEventArgs> | <span data-ttu-id="f8d5a-126">`oncut`, `oncopy`, `onpaste`</span><span class="sxs-lookup"><span data-stu-id="f8d5a-126">`oncut`, `oncopy`, `onpaste`</span></span> |
| <span data-ttu-id="f8d5a-127">Trascinamento</span><span class="sxs-lookup"><span data-stu-id="f8d5a-127">Drag</span></span>             | <xref:Microsoft.AspNetCore.Components.Web.DragEventArgs> | <span data-ttu-id="f8d5a-128">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span><span class="sxs-lookup"><span data-stu-id="f8d5a-128">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span></span><br><br><span data-ttu-id="f8d5a-129"><xref:Microsoft.AspNetCore.Components.Web.DataTransfer>e <xref:Microsoft.AspNetCore.Components.Web.DataTransferItem> contengono dati di elementi trascinati.</span><span class="sxs-lookup"><span data-stu-id="f8d5a-129"><xref:Microsoft.AspNetCore.Components.Web.DataTransfer> and <xref:Microsoft.AspNetCore.Components.Web.DataTransferItem> hold dragged item data.</span></span> |
| <span data-ttu-id="f8d5a-130">Errore</span><span class="sxs-lookup"><span data-stu-id="f8d5a-130">Error</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.ErrorEventArgs> | `onerror` |
| <span data-ttu-id="f8d5a-131">Event</span><span class="sxs-lookup"><span data-stu-id="f8d5a-131">Event</span></span>            | <xref:System.EventArgs> | <span data-ttu-id="f8d5a-132">*Generalee*</span><span class="sxs-lookup"><span data-stu-id="f8d5a-132">*General*</span></span><br><span data-ttu-id="f8d5a-133">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onended`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span><span class="sxs-lookup"><span data-stu-id="f8d5a-133">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onended`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span></span><br><br><span data-ttu-id="f8d5a-134">*Appunti*</span><span class="sxs-lookup"><span data-stu-id="f8d5a-134">*Clipboard*</span></span><br><span data-ttu-id="f8d5a-135">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span><span class="sxs-lookup"><span data-stu-id="f8d5a-135">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span></span><br><br><span data-ttu-id="f8d5a-136">*Input*</span><span class="sxs-lookup"><span data-stu-id="f8d5a-136">*Input*</span></span><br><span data-ttu-id="f8d5a-137">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit></span><span class="sxs-lookup"><span data-stu-id="f8d5a-137">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit></span></span><br><br><span data-ttu-id="f8d5a-138">*Supporti*</span><span class="sxs-lookup"><span data-stu-id="f8d5a-138">*Media*</span></span><br><span data-ttu-id="f8d5a-139">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`</span><span class="sxs-lookup"><span data-stu-id="f8d5a-139">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`</span></span><br><br><span data-ttu-id="f8d5a-140"><xref:Microsoft.AspNetCore.Components.Web.EventHandlers>include gli attributi per configurare i mapping tra i nomi di evento e i tipi di argomenti dell'evento.</span><span class="sxs-lookup"><span data-stu-id="f8d5a-140"><xref:Microsoft.AspNetCore.Components.Web.EventHandlers> holds attributes to configure the mappings between event names and event argument types.</span></span> |
| <span data-ttu-id="f8d5a-141">Focus</span><span class="sxs-lookup"><span data-stu-id="f8d5a-141">Focus</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.FocusEventArgs> | <span data-ttu-id="f8d5a-142">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span><span class="sxs-lookup"><span data-stu-id="f8d5a-142">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span></span><br><br><span data-ttu-id="f8d5a-143">Non include il supporto per `relatedTarget` .</span><span class="sxs-lookup"><span data-stu-id="f8d5a-143">Doesn't include support for `relatedTarget`.</span></span> |
| <span data-ttu-id="f8d5a-144">Input</span><span class="sxs-lookup"><span data-stu-id="f8d5a-144">Input</span></span>            | <xref:Microsoft.AspNetCore.Components.ChangeEventArgs> | <span data-ttu-id="f8d5a-145">`onchange`, `oninput`</span><span class="sxs-lookup"><span data-stu-id="f8d5a-145">`onchange`, `oninput`</span></span> |
| <span data-ttu-id="f8d5a-146">Tastiera</span><span class="sxs-lookup"><span data-stu-id="f8d5a-146">Keyboard</span></span>         | <xref:Microsoft.AspNetCore.Components.Web.KeyboardEventArgs> | <span data-ttu-id="f8d5a-147">`onkeydown`, `onkeypress`, `onkeyup`</span><span class="sxs-lookup"><span data-stu-id="f8d5a-147">`onkeydown`, `onkeypress`, `onkeyup`</span></span> |
| <span data-ttu-id="f8d5a-148">Mouse</span><span class="sxs-lookup"><span data-stu-id="f8d5a-148">Mouse</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs> | <span data-ttu-id="f8d5a-149">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span><span class="sxs-lookup"><span data-stu-id="f8d5a-149">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span></span> |
| <span data-ttu-id="f8d5a-150">Puntatore del mouse</span><span class="sxs-lookup"><span data-stu-id="f8d5a-150">Mouse pointer</span></span>    | <xref:Microsoft.AspNetCore.Components.Web.PointerEventArgs> | <span data-ttu-id="f8d5a-151">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span><span class="sxs-lookup"><span data-stu-id="f8d5a-151">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span></span> |
| <span data-ttu-id="f8d5a-152">Rotellina del mouse</span><span class="sxs-lookup"><span data-stu-id="f8d5a-152">Mouse wheel</span></span>      | <xref:Microsoft.AspNetCore.Components.Web.WheelEventArgs> | <span data-ttu-id="f8d5a-153">`onwheel`, `onmousewheel`</span><span class="sxs-lookup"><span data-stu-id="f8d5a-153">`onwheel`, `onmousewheel`</span></span> |
| <span data-ttu-id="f8d5a-154">Avanzamento</span><span class="sxs-lookup"><span data-stu-id="f8d5a-154">Progress</span></span>         | <xref:Microsoft.AspNetCore.Components.Web.ProgressEventArgs> | <span data-ttu-id="f8d5a-155">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span><span class="sxs-lookup"><span data-stu-id="f8d5a-155">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span></span> |
| <span data-ttu-id="f8d5a-156">Tocco</span><span class="sxs-lookup"><span data-stu-id="f8d5a-156">Touch</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.TouchEventArgs> | <span data-ttu-id="f8d5a-157">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span><span class="sxs-lookup"><span data-stu-id="f8d5a-157">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span></span><br><br><span data-ttu-id="f8d5a-158"><xref:Microsoft.AspNetCore.Components.Web.TouchPoint>rappresenta un singolo punto di contatto su un dispositivo sensibile al tocco.</span><span class="sxs-lookup"><span data-stu-id="f8d5a-158"><xref:Microsoft.AspNetCore.Components.Web.TouchPoint> represents a single contact point on a touch-sensitive device.</span></span> |

<span data-ttu-id="f8d5a-159">Per altre informazioni, vedere le seguenti risorse:</span><span class="sxs-lookup"><span data-stu-id="f8d5a-159">For more information, see the following resources:</span></span>

* <span data-ttu-id="f8d5a-160">[Classi EventArgs nell'origine riferimento ASP.NET Core (ramo DotNet/aspnetcore Release/3.1)](https://github.com/dotnet/aspnetcore/tree/release/3.1/src/Components/Web/src/Web).</span><span class="sxs-lookup"><span data-stu-id="f8d5a-160">[EventArgs classes in the ASP.NET Core reference source (dotnet/aspnetcore release/3.1 branch)](https://github.com/dotnet/aspnetcore/tree/release/3.1/src/Components/Web/src/Web).</span></span>
* <span data-ttu-id="f8d5a-161">[MDN Web docs: GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers): include informazioni su quali elementi HTML supportano ogni evento DOM.</span><span class="sxs-lookup"><span data-stu-id="f8d5a-161">[MDN web docs: GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers): Includes information on which HTML elements support each DOM event.</span></span>

## <a name="lambda-expressions"></a><span data-ttu-id="f8d5a-162">Espressioni lambda</span><span class="sxs-lookup"><span data-stu-id="f8d5a-162">Lambda expressions</span></span>

<span data-ttu-id="f8d5a-163">È inoltre possibile utilizzare le [espressioni lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions) :</span><span class="sxs-lookup"><span data-stu-id="f8d5a-163">[Lambda expressions](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions) can also be used:</span></span>

```razor
<button @onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

<span data-ttu-id="f8d5a-164">Spesso è consigliabile chiudere i valori aggiuntivi, ad esempio quando si esegue l'iterazione su un set di elementi.</span><span class="sxs-lookup"><span data-stu-id="f8d5a-164">It's often convenient to close over additional values, such as when iterating over a set of elements.</span></span> <span data-ttu-id="f8d5a-165">Nell'esempio seguente vengono creati tre pulsanti, ciascuno dei quali chiama il `UpdateHeading` passaggio di un argomento di evento ( <xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs> ) e il relativo numero di pulsante ( `buttonNumber` ) quando vengono selezionati nell'interfaccia utente:</span><span class="sxs-lookup"><span data-stu-id="f8d5a-165">The following example creates three buttons, each of which calls `UpdateHeading` passing an event argument (<xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs>) and its button number (`buttonNumber`) when selected in the UI:</span></span>

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
> <span data-ttu-id="f8d5a-166">**Non** usare direttamente una variabile di ciclo in un'espressione lambda, ad `i` esempio nell'esempio di `for` ciclo precedente o in una variabile di riferimento in un `foreach` ciclo.</span><span class="sxs-lookup"><span data-stu-id="f8d5a-166">Do **not** use a loop variable directly in a lambda expression, such as `i` in the preceding `for` loop example or a reference variable in a `foreach` loop.</span></span> <span data-ttu-id="f8d5a-167">In caso contrario, la stessa variabile viene utilizzata da tutte le espressioni lambda, che comporta l'utilizzo dello stesso valore in tutte le espressioni lambda.</span><span class="sxs-lookup"><span data-stu-id="f8d5a-167">Otherwise, the same variable is used by all lambda expressions, which results in use of the same value in all lambdas.</span></span> <span data-ttu-id="f8d5a-168">Acquisire sempre il valore della variabile in una variabile locale e quindi usarlo.</span><span class="sxs-lookup"><span data-stu-id="f8d5a-168">Always capture the variable's value in a local variable and then use it.</span></span> <span data-ttu-id="f8d5a-169">Nell'esempio precedente, la variabile di ciclo `i` viene assegnata a `buttonNumber` .</span><span class="sxs-lookup"><span data-stu-id="f8d5a-169">In the preceding example, the loop variable `i` is assigned to `buttonNumber`.</span></span>

## <a name="eventcallback"></a><span data-ttu-id="f8d5a-170">EventCallback</span><span class="sxs-lookup"><span data-stu-id="f8d5a-170">EventCallback</span></span>

<span data-ttu-id="f8d5a-171">Uno scenario comune con i componenti annidati è la volontà di eseguire il metodo di un componente padre quando si verifica un evento del componente figlio.</span><span class="sxs-lookup"><span data-stu-id="f8d5a-171">A common scenario with nested components is the desire to run a parent component's method when a child component event occurs.</span></span> <span data-ttu-id="f8d5a-172">Un `onclick` evento che si verifica nel componente figlio è un caso d'uso comune.</span><span class="sxs-lookup"><span data-stu-id="f8d5a-172">An `onclick` event occurring in the child component is a common use case.</span></span> <span data-ttu-id="f8d5a-173">Per esporre gli eventi tra i componenti, usare un oggetto <xref:Microsoft.AspNetCore.Components.EventCallback> .</span><span class="sxs-lookup"><span data-stu-id="f8d5a-173">To expose events across components, use an <xref:Microsoft.AspNetCore.Components.EventCallback>.</span></span> <span data-ttu-id="f8d5a-174">Un componente padre può assegnare un metodo di callback a un componente figlio <xref:Microsoft.AspNetCore.Components.EventCallback> .</span><span class="sxs-lookup"><span data-stu-id="f8d5a-174">A parent component can assign a callback method to a child component's <xref:Microsoft.AspNetCore.Components.EventCallback>.</span></span>

<span data-ttu-id="f8d5a-175">`ChildComponent`Nell'app di esempio (*Components/ChildComponent. Razor*) viene illustrato il modo in cui il gestore di un pulsante `onclick` viene configurato per ricevere un <xref:Microsoft.AspNetCore.Components.EventCallback> delegato dall'oggetto dell'esempio `ParentComponent` .</span><span class="sxs-lookup"><span data-stu-id="f8d5a-175">The `ChildComponent` in the sample app (*Components/ChildComponent.razor*) demonstrates how a button's `onclick` handler is set up to receive an <xref:Microsoft.AspNetCore.Components.EventCallback> delegate from the sample's `ParentComponent`.</span></span> <span data-ttu-id="f8d5a-176"><xref:Microsoft.AspNetCore.Components.EventCallback>Viene digitato con `MouseEventArgs` , appropriato per un `onclick` evento da un dispositivo periferico:</span><span class="sxs-lookup"><span data-stu-id="f8d5a-176">The <xref:Microsoft.AspNetCore.Components.EventCallback> is typed with `MouseEventArgs`, which is appropriate for an `onclick` event from a peripheral device:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=5-7,17-18)]

<span data-ttu-id="f8d5a-177">`ParentComponent`Imposta l'oggetto () del figlio sul <xref:Microsoft.AspNetCore.Components.EventCallback%601> `OnClickCallback` relativo `ShowMessage` metodo.</span><span class="sxs-lookup"><span data-stu-id="f8d5a-177">The `ParentComponent` sets the child's <xref:Microsoft.AspNetCore.Components.EventCallback%601> (`OnClickCallback`) to its `ShowMessage` method.</span></span>

<span data-ttu-id="f8d5a-178">*Pages/ParentComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="f8d5a-178">*Pages/ParentComponent.razor*:</span></span>

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

<span data-ttu-id="f8d5a-179">Quando il pulsante è selezionato in `ChildComponent` :</span><span class="sxs-lookup"><span data-stu-id="f8d5a-179">When the button is selected in the `ChildComponent`:</span></span>

* <span data-ttu-id="f8d5a-180">`ParentComponent` `ShowMessage` Viene chiamato il metodo di.</span><span class="sxs-lookup"><span data-stu-id="f8d5a-180">The `ParentComponent`'s `ShowMessage` method is called.</span></span> <span data-ttu-id="f8d5a-181">`messageText`viene aggiornato e visualizzato nell'oggetto `ParentComponent` .</span><span class="sxs-lookup"><span data-stu-id="f8d5a-181">`messageText` is updated and displayed in the `ParentComponent`.</span></span>
* <span data-ttu-id="f8d5a-182">Una chiamata a [StateHasChanged](xref:blazor/lifecycle#state-changes) non è obbligatoria nel metodo del callback ( `ShowMessage` ).</span><span class="sxs-lookup"><span data-stu-id="f8d5a-182">A call to [StateHasChanged](xref:blazor/lifecycle#state-changes) isn't required in the callback's method (`ShowMessage`).</span></span> <span data-ttu-id="f8d5a-183"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>viene chiamato automaticamente per eseguire il rendering di `ParentComponent` , proprio come gli eventi figlio attivano il rendering dei componenti nei gestori eventi che vengono eseguiti all'interno dell'elemento figlio.</span><span class="sxs-lookup"><span data-stu-id="f8d5a-183"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called automatically to rerender the `ParentComponent`, just as child events trigger component rerendering in event handlers that execute within the child.</span></span>

<span data-ttu-id="f8d5a-184"><xref:Microsoft.AspNetCore.Components.EventCallback>e <xref:Microsoft.AspNetCore.Components.EventCallback%601> consentono delegati asincroni.</span><span class="sxs-lookup"><span data-stu-id="f8d5a-184"><xref:Microsoft.AspNetCore.Components.EventCallback> and <xref:Microsoft.AspNetCore.Components.EventCallback%601> permit asynchronous delegates.</span></span> <span data-ttu-id="f8d5a-185"><xref:Microsoft.AspNetCore.Components.EventCallback%601>è fortemente tipizzato e richiede un tipo di argomento specifico.</span><span class="sxs-lookup"><span data-stu-id="f8d5a-185"><xref:Microsoft.AspNetCore.Components.EventCallback%601> is strongly typed and requires a specific argument type.</span></span> <span data-ttu-id="f8d5a-186"><xref:Microsoft.AspNetCore.Components.EventCallback>è debolmente tipizzato e consente qualsiasi tipo di argomento.</span><span class="sxs-lookup"><span data-stu-id="f8d5a-186"><xref:Microsoft.AspNetCore.Components.EventCallback> is weakly typed and allows any argument type.</span></span>

```razor
<ChildComponent 
    OnClickCallback="@(async () => { await Task.Yield(); messageText = "Blaze It!"; })" />
```

<span data-ttu-id="f8d5a-187">Richiamare <xref:Microsoft.AspNetCore.Components.EventCallback> o <xref:Microsoft.AspNetCore.Components.EventCallback%601> con <xref:Microsoft.AspNetCore.Components.EventCallback.InvokeAsync%2A> e attendere <xref:System.Threading.Tasks.Task> :</span><span class="sxs-lookup"><span data-stu-id="f8d5a-187">Invoke an <xref:Microsoft.AspNetCore.Components.EventCallback> or <xref:Microsoft.AspNetCore.Components.EventCallback%601> with <xref:Microsoft.AspNetCore.Components.EventCallback.InvokeAsync%2A> and await the <xref:System.Threading.Tasks.Task>:</span></span>

```csharp
await callback.InvokeAsync(arg);
```

<span data-ttu-id="f8d5a-188">Usare <xref:Microsoft.AspNetCore.Components.EventCallback> e <xref:Microsoft.AspNetCore.Components.EventCallback%601> per la gestione degli eventi e i parametri del componente di associazione.</span><span class="sxs-lookup"><span data-stu-id="f8d5a-188">Use <xref:Microsoft.AspNetCore.Components.EventCallback> and <xref:Microsoft.AspNetCore.Components.EventCallback%601> for event handling and binding component parameters.</span></span>

<span data-ttu-id="f8d5a-189">Preferisci l'oggetto fortemente tipizzato <xref:Microsoft.AspNetCore.Components.EventCallback%601> <xref:Microsoft.AspNetCore.Components.EventCallback> .</span><span class="sxs-lookup"><span data-stu-id="f8d5a-189">Prefer the strongly typed <xref:Microsoft.AspNetCore.Components.EventCallback%601> over <xref:Microsoft.AspNetCore.Components.EventCallback>.</span></span> <span data-ttu-id="f8d5a-190"><xref:Microsoft.AspNetCore.Components.EventCallback%601>fornisce un migliore feedback sugli errori agli utenti del componente.</span><span class="sxs-lookup"><span data-stu-id="f8d5a-190"><xref:Microsoft.AspNetCore.Components.EventCallback%601> provides better error feedback to users of the component.</span></span> <span data-ttu-id="f8d5a-191">Analogamente ad altri gestori di eventi dell'interfaccia utente, la specifica del parametro dell'evento è facoltativa.</span><span class="sxs-lookup"><span data-stu-id="f8d5a-191">Similar to other UI event handlers, specifying the event parameter is optional.</span></span> <span data-ttu-id="f8d5a-192">Usare <xref:Microsoft.AspNetCore.Components.EventCallback> quando non è stato passato alcun valore al callback.</span><span class="sxs-lookup"><span data-stu-id="f8d5a-192">Use <xref:Microsoft.AspNetCore.Components.EventCallback> when there's no value passed to the callback.</span></span>

## <a name="prevent-default-actions"></a><span data-ttu-id="f8d5a-193">Impedisci azioni predefinite</span><span class="sxs-lookup"><span data-stu-id="f8d5a-193">Prevent default actions</span></span>

<span data-ttu-id="f8d5a-194">Utilizzare l' [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) attributo Directive per impedire l'azione predefinita per un evento.</span><span class="sxs-lookup"><span data-stu-id="f8d5a-194">Use the [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) directive attribute to prevent the default action for an event.</span></span>

<span data-ttu-id="f8d5a-195">Quando si seleziona un tasto in un dispositivo di input e lo stato attivo dell'elemento si trova in una casella di testo, in un browser viene in genere visualizzato il carattere della chiave nella casella di testo.</span><span class="sxs-lookup"><span data-stu-id="f8d5a-195">When a key is selected on an input device and the element focus is on a text box, a browser normally displays the key's character in the text box.</span></span> <span data-ttu-id="f8d5a-196">Nell'esempio seguente viene impedito il comportamento predefinito specificando l' `@onkeypress:preventDefault` attributo della direttiva.</span><span class="sxs-lookup"><span data-stu-id="f8d5a-196">In the following example, the default behavior is prevented by specifying the `@onkeypress:preventDefault` directive attribute.</span></span> <span data-ttu-id="f8d5a-197">Il contatore viene incrementato e la **+** chiave non viene acquisita nel `<input>` valore dell'elemento:</span><span class="sxs-lookup"><span data-stu-id="f8d5a-197">The counter increments, and the **+** key isn't captured into the `<input>` element's value:</span></span>

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

<span data-ttu-id="f8d5a-198">La specifica dell' `@on{EVENT}:preventDefault` attributo senza un valore equivale a `@on{EVENT}:preventDefault="true"` .</span><span class="sxs-lookup"><span data-stu-id="f8d5a-198">Specifying the `@on{EVENT}:preventDefault` attribute without a value is equivalent to `@on{EVENT}:preventDefault="true"`.</span></span>

<span data-ttu-id="f8d5a-199">Il valore dell'attributo può anche essere un'espressione.</span><span class="sxs-lookup"><span data-stu-id="f8d5a-199">The value of the attribute can also be an expression.</span></span> <span data-ttu-id="f8d5a-200">Nell'esempio seguente `shouldPreventDefault` è un `bool` campo impostato su `true` o `false` :</span><span class="sxs-lookup"><span data-stu-id="f8d5a-200">In the following example, `shouldPreventDefault` is a `bool` field set to either `true` or `false`:</span></span>

```razor
<input @onkeypress:preventDefault="shouldPreventDefault" />
```

<span data-ttu-id="f8d5a-201">Per impedire l'azione predefinita, non è necessario un gestore eventi.</span><span class="sxs-lookup"><span data-stu-id="f8d5a-201">An event handler isn't required to prevent the default action.</span></span> <span data-ttu-id="f8d5a-202">Il gestore eventi e impedire scenari di azione predefiniti possono essere usati in modo indipendente.</span><span class="sxs-lookup"><span data-stu-id="f8d5a-202">The event handler and prevent default action scenarios can be used independently.</span></span>

## <a name="stop-event-propagation"></a><span data-ttu-id="f8d5a-203">Arresta propagazione eventi</span><span class="sxs-lookup"><span data-stu-id="f8d5a-203">Stop event propagation</span></span>

<span data-ttu-id="f8d5a-204">Utilizzare l' [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) attributo Directive per arrestare la propagazione degli eventi.</span><span class="sxs-lookup"><span data-stu-id="f8d5a-204">Use the [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) directive attribute to stop event propagation.</span></span>

<span data-ttu-id="f8d5a-205">Nell'esempio seguente la selezione della casella di controllo impedisce la propagazione degli eventi click dal secondo figlio `<div>` all'elemento padre `<div>` :</span><span class="sxs-lookup"><span data-stu-id="f8d5a-205">In the following example, selecting the check box prevents click events from the second child `<div>` from propagating to the parent `<div>`:</span></span>

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
