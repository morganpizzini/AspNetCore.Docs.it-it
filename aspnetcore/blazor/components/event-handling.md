---
title: BlazorGestione degli eventi ASP.NET Core
author: guardrex
description: Informazioni sulle Blazor funzionalità di gestione degli eventi, inclusi i tipi di argomento degli eventi, i callback degli eventi e la gestione degli eventi predefiniti del browser.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/17/2020
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
uid: blazor/components/event-handling
ms.openlocfilehash: 0d832d98ac9d1364b5db2bf65f31cbc5442db7f6
ms.sourcegitcommit: 74f4a4ddbe3c2f11e2e09d05d2a979784d89d3f5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/27/2020
ms.locfileid: "91393782"
---
# <a name="aspnet-core-no-locblazor-event-handling"></a><span data-ttu-id="67916-103">BlazorGestione degli eventi ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="67916-103">ASP.NET Core Blazor event handling</span></span>

<span data-ttu-id="67916-104">Di [Luke Latham](https://github.com/guardrex) e [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="67916-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="67916-105">Razor i componenti di forniscono funzionalità di gestione degli eventi.</span><span class="sxs-lookup"><span data-stu-id="67916-105">Razor components provide event handling features.</span></span> <span data-ttu-id="67916-106">Per un attributo dell'elemento HTML denominato, [`@on{EVENT}`](xref:mvc/views/razor#onevent) ad esempio, `@onclick` con un valore tipizzato come delegato, un Razor componente considera il valore dell'attributo come un gestore eventi.</span><span class="sxs-lookup"><span data-stu-id="67916-106">For an HTML element attribute named [`@on{EVENT}`](xref:mvc/views/razor#onevent) (for example, `@onclick`) with a delegate-typed value, a Razor component treats the attribute's value as an event handler.</span></span>

<span data-ttu-id="67916-107">Il codice seguente chiama il `UpdateHeading` metodo quando si seleziona il pulsante nell'interfaccia utente:</span><span class="sxs-lookup"><span data-stu-id="67916-107">The following code calls the `UpdateHeading` method when the button is selected in the UI:</span></span>

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

<span data-ttu-id="67916-108">Il codice seguente chiama il `CheckChanged` metodo quando la casella di controllo viene modificata nell'interfaccia utente:</span><span class="sxs-lookup"><span data-stu-id="67916-108">The following code calls the `CheckChanged` method when the check box is changed in the UI:</span></span>

```razor
<input type="checkbox" class="form-check-input" @onchange="CheckChanged" />

@code {
    private void CheckChanged()
    {
        ...
    }
}
```

<span data-ttu-id="67916-109">I gestori di eventi possono anche essere asincroni e restituire un oggetto <xref:System.Threading.Tasks.Task> .</span><span class="sxs-lookup"><span data-stu-id="67916-109">Event handlers can also be asynchronous and return a <xref:System.Threading.Tasks.Task>.</span></span> <span data-ttu-id="67916-110">Non è necessario chiamare manualmente [StateHasChanged](xref:blazor/components/lifecycle#state-changes).</span><span class="sxs-lookup"><span data-stu-id="67916-110">There's no need to manually call [StateHasChanged](xref:blazor/components/lifecycle#state-changes).</span></span> <span data-ttu-id="67916-111">Le eccezioni vengono registrate quando si verificano.</span><span class="sxs-lookup"><span data-stu-id="67916-111">Exceptions are logged when they occur.</span></span>

<span data-ttu-id="67916-112">Nell'esempio seguente, `UpdateHeading` viene chiamato in modo asincrono quando si seleziona il pulsante:</span><span class="sxs-lookup"><span data-stu-id="67916-112">In the following example, `UpdateHeading` is called asynchronously when the button is selected:</span></span>

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

## <a name="event-argument-types"></a><span data-ttu-id="67916-113">Tipi di argomenti dell'evento</span><span class="sxs-lookup"><span data-stu-id="67916-113">Event argument types</span></span>

<span data-ttu-id="67916-114">Per alcuni eventi, i tipi di argomento dell'evento sono consentiti.</span><span class="sxs-lookup"><span data-stu-id="67916-114">For some events, event argument types are permitted.</span></span> <span data-ttu-id="67916-115">La specifica di un parametro di evento nella definizione di un metodo di evento è facoltativa ed è necessaria solo se il tipo di evento viene usato nel metodo.</span><span class="sxs-lookup"><span data-stu-id="67916-115">Specifying an event parameter in an event method definition is optional and only necessary if the event type is used in the method.</span></span> <span data-ttu-id="67916-116">Nell'esempio seguente, l' `MouseEventArgs` argomento dell'evento viene usato nel `ShowMessage` metodo per impostare il testo del messaggio:</span><span class="sxs-lookup"><span data-stu-id="67916-116">In the following example, the `MouseEventArgs` event argument is used in the `ShowMessage` method to set message text:</span></span>

```csharp
private void ShowMessage(MouseEventArgs e)
{
    messageText = $"The mouse is at coordinates: {e.ScreenX}:{e.ScreenY}";
}
```

<span data-ttu-id="67916-117"><xref:System.EventArgs>I supportati sono riportati nella tabella seguente.</span><span class="sxs-lookup"><span data-stu-id="67916-117">Supported <xref:System.EventArgs> are shown in the following table.</span></span>

::: moniker range=">= aspnetcore-5.0"

| <span data-ttu-id="67916-118">Evento</span><span class="sxs-lookup"><span data-stu-id="67916-118">Event</span></span>            | <span data-ttu-id="67916-119">Classe</span><span class="sxs-lookup"><span data-stu-id="67916-119">Class</span></span>  | <span data-ttu-id="67916-120">Eventi e note DOM</span><span class="sxs-lookup"><span data-stu-id="67916-120">DOM events and notes</span></span> |
| ---------------- | ------ | -------------------- |
| <span data-ttu-id="67916-121">Appunti</span><span class="sxs-lookup"><span data-stu-id="67916-121">Clipboard</span></span>        | <xref:Microsoft.AspNetCore.Components.Web.ClipboardEventArgs> | <span data-ttu-id="67916-122">`oncut`, `oncopy`, `onpaste`</span><span class="sxs-lookup"><span data-stu-id="67916-122">`oncut`, `oncopy`, `onpaste`</span></span> |
| <span data-ttu-id="67916-123">Trascinamento</span><span class="sxs-lookup"><span data-stu-id="67916-123">Drag</span></span>             | <xref:Microsoft.AspNetCore.Components.Web.DragEventArgs> | <span data-ttu-id="67916-124">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span><span class="sxs-lookup"><span data-stu-id="67916-124">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span></span><br><br><span data-ttu-id="67916-125"><xref:Microsoft.AspNetCore.Components.Web.DataTransfer> e <xref:Microsoft.AspNetCore.Components.Web.DataTransferItem> contengono dati di elementi trascinati.</span><span class="sxs-lookup"><span data-stu-id="67916-125"><xref:Microsoft.AspNetCore.Components.Web.DataTransfer> and <xref:Microsoft.AspNetCore.Components.Web.DataTransferItem> hold dragged item data.</span></span><br><br><span data-ttu-id="67916-126">Implementare il trascinamento della selezione nelle Blazor app usando l' [interoperabilità JS](xref:blazor/call-javascript-from-dotnet) con l' [API di trascinamento](https://developer.mozilla.org/docs/Web/API/HTML_Drag_and_Drop_API).</span><span class="sxs-lookup"><span data-stu-id="67916-126">Implement drag and drop in Blazor apps using [JS interop](xref:blazor/call-javascript-from-dotnet) with [HTML Drag and Drop API](https://developer.mozilla.org/docs/Web/API/HTML_Drag_and_Drop_API).</span></span> |
| <span data-ttu-id="67916-127">Errore</span><span class="sxs-lookup"><span data-stu-id="67916-127">Error</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.ErrorEventArgs> | `onerror` |
| <span data-ttu-id="67916-128">Evento</span><span class="sxs-lookup"><span data-stu-id="67916-128">Event</span></span>            | <xref:System.EventArgs> | <span data-ttu-id="67916-129">*Generale*</span><span class="sxs-lookup"><span data-stu-id="67916-129">*General*</span></span><br><span data-ttu-id="67916-130">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span><span class="sxs-lookup"><span data-stu-id="67916-130">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span></span><br><br><span data-ttu-id="67916-131">*Appunti*</span><span class="sxs-lookup"><span data-stu-id="67916-131">*Clipboard*</span></span><br><span data-ttu-id="67916-132">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span><span class="sxs-lookup"><span data-stu-id="67916-132">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span></span><br><br><span data-ttu-id="67916-133">*Input*</span><span class="sxs-lookup"><span data-stu-id="67916-133">*Input*</span></span><br><span data-ttu-id="67916-134">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`</span><span class="sxs-lookup"><span data-stu-id="67916-134">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`</span></span><br><br><span data-ttu-id="67916-135">*Supporti*</span><span class="sxs-lookup"><span data-stu-id="67916-135">*Media*</span></span><br><span data-ttu-id="67916-136">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onended`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `ontoggle`, `onvolumechange`, `onwaiting`</span><span class="sxs-lookup"><span data-stu-id="67916-136">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onended`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `ontoggle`, `onvolumechange`, `onwaiting`</span></span><br><br><span data-ttu-id="67916-137"><xref:Microsoft.AspNetCore.Components.Web.EventHandlers> include gli attributi per configurare i mapping tra i nomi di evento e i tipi di argomenti dell'evento.</span><span class="sxs-lookup"><span data-stu-id="67916-137"><xref:Microsoft.AspNetCore.Components.Web.EventHandlers> holds attributes to configure the mappings between event names and event argument types.</span></span> |
| <span data-ttu-id="67916-138">Focus</span><span class="sxs-lookup"><span data-stu-id="67916-138">Focus</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.FocusEventArgs> | <span data-ttu-id="67916-139">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span><span class="sxs-lookup"><span data-stu-id="67916-139">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span></span><br><br><span data-ttu-id="67916-140">Non include il supporto per `relatedTarget` .</span><span class="sxs-lookup"><span data-stu-id="67916-140">Doesn't include support for `relatedTarget`.</span></span> |
| <span data-ttu-id="67916-141">Input</span><span class="sxs-lookup"><span data-stu-id="67916-141">Input</span></span>            | <xref:Microsoft.AspNetCore.Components.ChangeEventArgs> | <span data-ttu-id="67916-142">`onchange`, `oninput`</span><span class="sxs-lookup"><span data-stu-id="67916-142">`onchange`, `oninput`</span></span> |
| <span data-ttu-id="67916-143">Tastiera</span><span class="sxs-lookup"><span data-stu-id="67916-143">Keyboard</span></span>         | <xref:Microsoft.AspNetCore.Components.Web.KeyboardEventArgs> | <span data-ttu-id="67916-144">`onkeydown`, `onkeypress`, `onkeyup`</span><span class="sxs-lookup"><span data-stu-id="67916-144">`onkeydown`, `onkeypress`, `onkeyup`</span></span> |
| <span data-ttu-id="67916-145">Mouse</span><span class="sxs-lookup"><span data-stu-id="67916-145">Mouse</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs> | <span data-ttu-id="67916-146">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span><span class="sxs-lookup"><span data-stu-id="67916-146">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span></span> |
| <span data-ttu-id="67916-147">Puntatore del mouse</span><span class="sxs-lookup"><span data-stu-id="67916-147">Mouse pointer</span></span>    | <xref:Microsoft.AspNetCore.Components.Web.PointerEventArgs> | <span data-ttu-id="67916-148">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span><span class="sxs-lookup"><span data-stu-id="67916-148">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span></span> |
| <span data-ttu-id="67916-149">Rotellina del mouse</span><span class="sxs-lookup"><span data-stu-id="67916-149">Mouse wheel</span></span>      | <xref:Microsoft.AspNetCore.Components.Web.WheelEventArgs> | <span data-ttu-id="67916-150">`onwheel`, `onmousewheel`</span><span class="sxs-lookup"><span data-stu-id="67916-150">`onwheel`, `onmousewheel`</span></span> |
| <span data-ttu-id="67916-151">Avanzamento</span><span class="sxs-lookup"><span data-stu-id="67916-151">Progress</span></span>         | <xref:Microsoft.AspNetCore.Components.Web.ProgressEventArgs> | <span data-ttu-id="67916-152">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span><span class="sxs-lookup"><span data-stu-id="67916-152">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span></span> |
| <span data-ttu-id="67916-153">Tocco</span><span class="sxs-lookup"><span data-stu-id="67916-153">Touch</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.TouchEventArgs> | <span data-ttu-id="67916-154">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span><span class="sxs-lookup"><span data-stu-id="67916-154">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span></span><br><br><span data-ttu-id="67916-155"><xref:Microsoft.AspNetCore.Components.Web.TouchPoint> rappresenta un singolo punto di contatto su un dispositivo sensibile al tocco.</span><span class="sxs-lookup"><span data-stu-id="67916-155"><xref:Microsoft.AspNetCore.Components.Web.TouchPoint> represents a single contact point on a touch-sensitive device.</span></span> |

::: moniker-end

::: moniker range="< aspnetcore-5.0"

| <span data-ttu-id="67916-156">Evento</span><span class="sxs-lookup"><span data-stu-id="67916-156">Event</span></span>            | <span data-ttu-id="67916-157">Classe</span><span class="sxs-lookup"><span data-stu-id="67916-157">Class</span></span> | <span data-ttu-id="67916-158">Eventi e note DOM</span><span class="sxs-lookup"><span data-stu-id="67916-158">DOM events and notes</span></span> |
| ---------------- | ----- | -------------------- |
| <span data-ttu-id="67916-159">Appunti</span><span class="sxs-lookup"><span data-stu-id="67916-159">Clipboard</span></span>        | <xref:Microsoft.AspNetCore.Components.Web.ClipboardEventArgs> | <span data-ttu-id="67916-160">`oncut`, `oncopy`, `onpaste`</span><span class="sxs-lookup"><span data-stu-id="67916-160">`oncut`, `oncopy`, `onpaste`</span></span> |
| <span data-ttu-id="67916-161">Trascinamento</span><span class="sxs-lookup"><span data-stu-id="67916-161">Drag</span></span>             | <xref:Microsoft.AspNetCore.Components.Web.DragEventArgs> | <span data-ttu-id="67916-162">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span><span class="sxs-lookup"><span data-stu-id="67916-162">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span></span><br><br><span data-ttu-id="67916-163"><xref:Microsoft.AspNetCore.Components.Web.DataTransfer> e <xref:Microsoft.AspNetCore.Components.Web.DataTransferItem> contengono dati di elementi trascinati.</span><span class="sxs-lookup"><span data-stu-id="67916-163"><xref:Microsoft.AspNetCore.Components.Web.DataTransfer> and <xref:Microsoft.AspNetCore.Components.Web.DataTransferItem> hold dragged item data.</span></span><br><br><span data-ttu-id="67916-164">Implementare il trascinamento della selezione nelle Blazor app usando l' [interoperabilità JS](xref:blazor/call-javascript-from-dotnet) con l' [API di trascinamento](https://developer.mozilla.org/docs/Web/API/HTML_Drag_and_Drop_API).</span><span class="sxs-lookup"><span data-stu-id="67916-164">Implement drag and drop in Blazor apps using [JS interop](xref:blazor/call-javascript-from-dotnet) with [HTML Drag and Drop API](https://developer.mozilla.org/docs/Web/API/HTML_Drag_and_Drop_API).</span></span> |
| <span data-ttu-id="67916-165">Errore</span><span class="sxs-lookup"><span data-stu-id="67916-165">Error</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.ErrorEventArgs> | `onerror` |
| <span data-ttu-id="67916-166">Evento</span><span class="sxs-lookup"><span data-stu-id="67916-166">Event</span></span>            | <xref:System.EventArgs> | <span data-ttu-id="67916-167">*Generale*</span><span class="sxs-lookup"><span data-stu-id="67916-167">*General*</span></span><br><span data-ttu-id="67916-168">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span><span class="sxs-lookup"><span data-stu-id="67916-168">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span></span><br><br><span data-ttu-id="67916-169">*Appunti*</span><span class="sxs-lookup"><span data-stu-id="67916-169">*Clipboard*</span></span><br><span data-ttu-id="67916-170">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span><span class="sxs-lookup"><span data-stu-id="67916-170">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span></span><br><br><span data-ttu-id="67916-171">*Input*</span><span class="sxs-lookup"><span data-stu-id="67916-171">*Input*</span></span><br><span data-ttu-id="67916-172">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`</span><span class="sxs-lookup"><span data-stu-id="67916-172">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`</span></span><br><br><span data-ttu-id="67916-173">*Supporti*</span><span class="sxs-lookup"><span data-stu-id="67916-173">*Media*</span></span><br><span data-ttu-id="67916-174">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onended`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`</span><span class="sxs-lookup"><span data-stu-id="67916-174">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onended`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`</span></span><br><br><span data-ttu-id="67916-175"><xref:Microsoft.AspNetCore.Components.Web.EventHandlers> include gli attributi per configurare i mapping tra i nomi di evento e i tipi di argomenti dell'evento.</span><span class="sxs-lookup"><span data-stu-id="67916-175"><xref:Microsoft.AspNetCore.Components.Web.EventHandlers> holds attributes to configure the mappings between event names and event argument types.</span></span> |
| <span data-ttu-id="67916-176">Focus</span><span class="sxs-lookup"><span data-stu-id="67916-176">Focus</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.FocusEventArgs> | <span data-ttu-id="67916-177">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span><span class="sxs-lookup"><span data-stu-id="67916-177">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span></span><br><br><span data-ttu-id="67916-178">Non include il supporto per `relatedTarget` .</span><span class="sxs-lookup"><span data-stu-id="67916-178">Doesn't include support for `relatedTarget`.</span></span> |
| <span data-ttu-id="67916-179">Input</span><span class="sxs-lookup"><span data-stu-id="67916-179">Input</span></span>            | <xref:Microsoft.AspNetCore.Components.ChangeEventArgs> | <span data-ttu-id="67916-180">`onchange`, `oninput`</span><span class="sxs-lookup"><span data-stu-id="67916-180">`onchange`, `oninput`</span></span> |
| <span data-ttu-id="67916-181">Tastiera</span><span class="sxs-lookup"><span data-stu-id="67916-181">Keyboard</span></span>         | <xref:Microsoft.AspNetCore.Components.Web.KeyboardEventArgs> | <span data-ttu-id="67916-182">`onkeydown`, `onkeypress`, `onkeyup`</span><span class="sxs-lookup"><span data-stu-id="67916-182">`onkeydown`, `onkeypress`, `onkeyup`</span></span> |
| <span data-ttu-id="67916-183">Mouse</span><span class="sxs-lookup"><span data-stu-id="67916-183">Mouse</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs> | <span data-ttu-id="67916-184">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span><span class="sxs-lookup"><span data-stu-id="67916-184">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span></span> |
| <span data-ttu-id="67916-185">Puntatore del mouse</span><span class="sxs-lookup"><span data-stu-id="67916-185">Mouse pointer</span></span>    | <xref:Microsoft.AspNetCore.Components.Web.PointerEventArgs> | <span data-ttu-id="67916-186">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span><span class="sxs-lookup"><span data-stu-id="67916-186">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span></span> |
| <span data-ttu-id="67916-187">Rotellina del mouse</span><span class="sxs-lookup"><span data-stu-id="67916-187">Mouse wheel</span></span>      | <xref:Microsoft.AspNetCore.Components.Web.WheelEventArgs> | <span data-ttu-id="67916-188">`onwheel`, `onmousewheel`</span><span class="sxs-lookup"><span data-stu-id="67916-188">`onwheel`, `onmousewheel`</span></span> |
| <span data-ttu-id="67916-189">Avanzamento</span><span class="sxs-lookup"><span data-stu-id="67916-189">Progress</span></span>         | <xref:Microsoft.AspNetCore.Components.Web.ProgressEventArgs> | <span data-ttu-id="67916-190">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span><span class="sxs-lookup"><span data-stu-id="67916-190">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span></span> |
| <span data-ttu-id="67916-191">Tocco</span><span class="sxs-lookup"><span data-stu-id="67916-191">Touch</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.TouchEventArgs> | <span data-ttu-id="67916-192">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span><span class="sxs-lookup"><span data-stu-id="67916-192">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span></span><br><br><span data-ttu-id="67916-193"><xref:Microsoft.AspNetCore.Components.Web.TouchPoint> rappresenta un singolo punto di contatto su un dispositivo sensibile al tocco.</span><span class="sxs-lookup"><span data-stu-id="67916-193"><xref:Microsoft.AspNetCore.Components.Web.TouchPoint> represents a single contact point on a touch-sensitive device.</span></span> |

::: moniker-end

<span data-ttu-id="67916-194">Per altre informazioni, vedere le seguenti risorse:</span><span class="sxs-lookup"><span data-stu-id="67916-194">For more information, see the following resources:</span></span>

* <span data-ttu-id="67916-195">[ `EventArgs` classi nell'origine riferimento ASP.NET Core (ramo DotNet/aspnetcore `master` )](https://github.com/dotnet/aspnetcore/tree/master/src/Components/Web/src/Web).</span><span class="sxs-lookup"><span data-stu-id="67916-195">[`EventArgs` classes in the ASP.NET Core reference source (dotnet/aspnetcore `master` branch)](https://github.com/dotnet/aspnetcore/tree/master/src/Components/Web/src/Web).</span></span> <span data-ttu-id="67916-196">Il `master` ramo rappresenta l'API in fase di sviluppo per la versione *successiva* del ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="67916-196">The `master` branch represents API under development for the *next* ASP.NET Core release.</span></span> <span data-ttu-id="67916-197">Per la versione corrente, selezionare il ramo repository GitHub appropriato (ad esempio, `release/3.1` ).</span><span class="sxs-lookup"><span data-stu-id="67916-197">For the current release, select the appropriate GitHub repository branch (for example, `release/3.1`).</span></span>
* <span data-ttu-id="67916-198">[MDN Web docs: GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers): include informazioni su quali elementi HTML supportano ogni evento DOM.</span><span class="sxs-lookup"><span data-stu-id="67916-198">[MDN web docs: GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers): Includes information on which HTML elements support each DOM event.</span></span>

## <a name="lambda-expressions"></a><span data-ttu-id="67916-199">Espressioni lambda</span><span class="sxs-lookup"><span data-stu-id="67916-199">Lambda expressions</span></span>

<span data-ttu-id="67916-200">È inoltre possibile utilizzare le [espressioni lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions) :</span><span class="sxs-lookup"><span data-stu-id="67916-200">[Lambda expressions](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions) can also be used:</span></span>

```razor
<button @onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

<span data-ttu-id="67916-201">Spesso è consigliabile chiudere i valori aggiuntivi, ad esempio quando si esegue l'iterazione su un set di elementi.</span><span class="sxs-lookup"><span data-stu-id="67916-201">It's often convenient to close over additional values, such as when iterating over a set of elements.</span></span> <span data-ttu-id="67916-202">Nell'esempio seguente vengono creati tre pulsanti, ciascuno dei quali chiama il `UpdateHeading` passaggio di un argomento di evento ( <xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs> ) e il relativo numero di pulsante ( `buttonNumber` ) quando vengono selezionati nell'interfaccia utente:</span><span class="sxs-lookup"><span data-stu-id="67916-202">The following example creates three buttons, each of which calls `UpdateHeading` passing an event argument (<xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs>) and its button number (`buttonNumber`) when selected in the UI:</span></span>

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
> <span data-ttu-id="67916-203">**Non** usare direttamente una variabile di ciclo in un'espressione lambda, come `i` nell' `for` esempio di ciclo precedente.</span><span class="sxs-lookup"><span data-stu-id="67916-203">Do **not** use a loop variable directly in a lambda expression, such as `i` in the preceding `for` loop example.</span></span> <span data-ttu-id="67916-204">In caso contrario, la stessa variabile viene utilizzata da tutte le espressioni lambda, che comporta l'utilizzo dello stesso valore in tutte le espressioni lambda.</span><span class="sxs-lookup"><span data-stu-id="67916-204">Otherwise, the same variable is used by all lambda expressions, which results in use of the same value in all lambdas.</span></span> <span data-ttu-id="67916-205">Acquisire sempre il valore della variabile in una variabile locale e quindi usarlo.</span><span class="sxs-lookup"><span data-stu-id="67916-205">Always capture the variable's value in a local variable and then use it.</span></span> <span data-ttu-id="67916-206">Nell'esempio precedente, la variabile di ciclo `i` viene assegnata a `buttonNumber` .</span><span class="sxs-lookup"><span data-stu-id="67916-206">In the preceding example, the loop variable `i` is assigned to `buttonNumber`.</span></span>

## <a name="eventcallback"></a><span data-ttu-id="67916-207">EventCallback</span><span class="sxs-lookup"><span data-stu-id="67916-207">EventCallback</span></span>

<span data-ttu-id="67916-208">Uno scenario comune con i componenti annidati è la volontà di eseguire il metodo di un componente padre quando si verifica un evento del componente figlio.</span><span class="sxs-lookup"><span data-stu-id="67916-208">A common scenario with nested components is the desire to run a parent component's method when a child component event occurs.</span></span> <span data-ttu-id="67916-209">Un `onclick` evento che si verifica nel componente figlio è un caso d'uso comune.</span><span class="sxs-lookup"><span data-stu-id="67916-209">An `onclick` event occurring in the child component is a common use case.</span></span> <span data-ttu-id="67916-210">Per esporre gli eventi tra i componenti, usare un oggetto <xref:Microsoft.AspNetCore.Components.EventCallback> .</span><span class="sxs-lookup"><span data-stu-id="67916-210">To expose events across components, use an <xref:Microsoft.AspNetCore.Components.EventCallback>.</span></span> <span data-ttu-id="67916-211">Un componente padre può assegnare un metodo di callback a un componente figlio <xref:Microsoft.AspNetCore.Components.EventCallback> .</span><span class="sxs-lookup"><span data-stu-id="67916-211">A parent component can assign a callback method to a child component's <xref:Microsoft.AspNetCore.Components.EventCallback>.</span></span>

<span data-ttu-id="67916-212">`ChildComponent`Nell'app di esempio ( `Components/ChildComponent.razor` ) viene illustrato come viene configurato il gestore di un pulsante `onclick` per ricevere un <xref:Microsoft.AspNetCore.Components.EventCallback> delegato dall'oggetto dell'esempio `ParentComponent` .</span><span class="sxs-lookup"><span data-stu-id="67916-212">The `ChildComponent` in the sample app (`Components/ChildComponent.razor`) demonstrates how a button's `onclick` handler is set up to receive an <xref:Microsoft.AspNetCore.Components.EventCallback> delegate from the sample's `ParentComponent`.</span></span> <span data-ttu-id="67916-213"><xref:Microsoft.AspNetCore.Components.EventCallback>Viene digitato con `MouseEventArgs` , appropriato per un `onclick` evento da un dispositivo periferico:</span><span class="sxs-lookup"><span data-stu-id="67916-213">The <xref:Microsoft.AspNetCore.Components.EventCallback> is typed with `MouseEventArgs`, which is appropriate for an `onclick` event from a peripheral device:</span></span>

[!code-razor[](../common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=5-7,17-18)]

<span data-ttu-id="67916-214">`ParentComponent`Imposta l'oggetto () del figlio sul <xref:Microsoft.AspNetCore.Components.EventCallback%601> `OnClickCallback` relativo `ShowMessage` metodo.</span><span class="sxs-lookup"><span data-stu-id="67916-214">The `ParentComponent` sets the child's <xref:Microsoft.AspNetCore.Components.EventCallback%601> (`OnClickCallback`) to its `ShowMessage` method.</span></span>

<span data-ttu-id="67916-215">`Pages/ParentComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="67916-215">`Pages/ParentComponent.razor`:</span></span>

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

<span data-ttu-id="67916-216">Quando il pulsante è selezionato in `ChildComponent` :</span><span class="sxs-lookup"><span data-stu-id="67916-216">When the button is selected in the `ChildComponent`:</span></span>

* <span data-ttu-id="67916-217">`ParentComponent` `ShowMessage` Viene chiamato il metodo di.</span><span class="sxs-lookup"><span data-stu-id="67916-217">The `ParentComponent`'s `ShowMessage` method is called.</span></span> <span data-ttu-id="67916-218">`messageText` viene aggiornato e visualizzato nell'oggetto `ParentComponent` .</span><span class="sxs-lookup"><span data-stu-id="67916-218">`messageText` is updated and displayed in the `ParentComponent`.</span></span>
* <span data-ttu-id="67916-219">Una chiamata a [`StateHasChanged`](xref:blazor/components/lifecycle#state-changes) non è obbligatoria nel metodo del callback ( `ShowMessage` ).</span><span class="sxs-lookup"><span data-stu-id="67916-219">A call to [`StateHasChanged`](xref:blazor/components/lifecycle#state-changes) isn't required in the callback's method (`ShowMessage`).</span></span> <span data-ttu-id="67916-220"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> viene chiamato automaticamente per eseguire il rendering di `ParentComponent` , proprio come gli eventi figlio attivano il rendering dei componenti nei gestori eventi che vengono eseguiti all'interno dell'elemento figlio.</span><span class="sxs-lookup"><span data-stu-id="67916-220"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called automatically to rerender the `ParentComponent`, just as child events trigger component rerendering in event handlers that execute within the child.</span></span>

<span data-ttu-id="67916-221"><xref:Microsoft.AspNetCore.Components.EventCallback> e <xref:Microsoft.AspNetCore.Components.EventCallback%601> consentono delegati asincroni.</span><span class="sxs-lookup"><span data-stu-id="67916-221"><xref:Microsoft.AspNetCore.Components.EventCallback> and <xref:Microsoft.AspNetCore.Components.EventCallback%601> permit asynchronous delegates.</span></span> <span data-ttu-id="67916-222"><xref:Microsoft.AspNetCore.Components.EventCallback> è debolmente tipizzato e consente di passare qualsiasi argomento di tipo in `InvokeAsync(Object)` .</span><span class="sxs-lookup"><span data-stu-id="67916-222"><xref:Microsoft.AspNetCore.Components.EventCallback> is weakly typed and allows passing any type argument in `InvokeAsync(Object)`.</span></span> <span data-ttu-id="67916-223"><xref:Microsoft.AspNetCore.Components.EventCallback%601> è fortemente tipizzato e richiede il passaggio di un `T` argomento in `InvokeAsync(T)` assegnabile a `TValue` .</span><span class="sxs-lookup"><span data-stu-id="67916-223"><xref:Microsoft.AspNetCore.Components.EventCallback%601> is strongly typed and requires passing a `T` argument in `InvokeAsync(T)` that's assignable to `TValue`.</span></span>

```razor
<ChildComponent 
    OnClickCallback="@(async () => { await Task.Yield(); messageText = "Blaze It!"; })" />
```

<span data-ttu-id="67916-224">Richiamare <xref:Microsoft.AspNetCore.Components.EventCallback> o <xref:Microsoft.AspNetCore.Components.EventCallback%601> con <xref:Microsoft.AspNetCore.Components.EventCallback.InvokeAsync%2A> e attendere <xref:System.Threading.Tasks.Task> :</span><span class="sxs-lookup"><span data-stu-id="67916-224">Invoke an <xref:Microsoft.AspNetCore.Components.EventCallback> or <xref:Microsoft.AspNetCore.Components.EventCallback%601> with <xref:Microsoft.AspNetCore.Components.EventCallback.InvokeAsync%2A> and await the <xref:System.Threading.Tasks.Task>:</span></span>

```csharp
await OnClickCallback.InvokeAsync(arg);
```

<span data-ttu-id="67916-225">Usare <xref:Microsoft.AspNetCore.Components.EventCallback> e <xref:Microsoft.AspNetCore.Components.EventCallback%601> per la gestione degli eventi e i parametri del componente di associazione.</span><span class="sxs-lookup"><span data-stu-id="67916-225">Use <xref:Microsoft.AspNetCore.Components.EventCallback> and <xref:Microsoft.AspNetCore.Components.EventCallback%601> for event handling and binding component parameters.</span></span>

<span data-ttu-id="67916-226">Preferisci l'oggetto fortemente tipizzato <xref:Microsoft.AspNetCore.Components.EventCallback%601> <xref:Microsoft.AspNetCore.Components.EventCallback> .</span><span class="sxs-lookup"><span data-stu-id="67916-226">Prefer the strongly typed <xref:Microsoft.AspNetCore.Components.EventCallback%601> over <xref:Microsoft.AspNetCore.Components.EventCallback>.</span></span> <span data-ttu-id="67916-227"><xref:Microsoft.AspNetCore.Components.EventCallback%601> fornisce un migliore feedback sugli errori agli utenti del componente.</span><span class="sxs-lookup"><span data-stu-id="67916-227"><xref:Microsoft.AspNetCore.Components.EventCallback%601> provides better error feedback to users of the component.</span></span> <span data-ttu-id="67916-228">Analogamente ad altri gestori di eventi dell'interfaccia utente, la specifica del parametro dell'evento è facoltativa.</span><span class="sxs-lookup"><span data-stu-id="67916-228">Similar to other UI event handlers, specifying the event parameter is optional.</span></span> <span data-ttu-id="67916-229">Usare <xref:Microsoft.AspNetCore.Components.EventCallback> quando non è stato passato alcun valore al callback.</span><span class="sxs-lookup"><span data-stu-id="67916-229">Use <xref:Microsoft.AspNetCore.Components.EventCallback> when there's no value passed to the callback.</span></span>

## <a name="prevent-default-actions"></a><span data-ttu-id="67916-230">Impedisci azioni predefinite</span><span class="sxs-lookup"><span data-stu-id="67916-230">Prevent default actions</span></span>

<span data-ttu-id="67916-231">Utilizzare l' [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) attributo Directive per impedire l'azione predefinita per un evento.</span><span class="sxs-lookup"><span data-stu-id="67916-231">Use the [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) directive attribute to prevent the default action for an event.</span></span>

<span data-ttu-id="67916-232">Quando si seleziona un tasto in un dispositivo di input e lo stato attivo dell'elemento si trova in una casella di testo, in un browser viene in genere visualizzato il carattere della chiave nella casella di testo.</span><span class="sxs-lookup"><span data-stu-id="67916-232">When a key is selected on an input device and the element focus is on a text box, a browser normally displays the key's character in the text box.</span></span> <span data-ttu-id="67916-233">Nell'esempio seguente viene impedito il comportamento predefinito specificando l' `@onkeypress:preventDefault` attributo della direttiva.</span><span class="sxs-lookup"><span data-stu-id="67916-233">In the following example, the default behavior is prevented by specifying the `@onkeypress:preventDefault` directive attribute.</span></span> <span data-ttu-id="67916-234">Il contatore viene incrementato e la **+** chiave non viene acquisita nel `<input>` valore dell'elemento:</span><span class="sxs-lookup"><span data-stu-id="67916-234">The counter increments, and the **+** key isn't captured into the `<input>` element's value:</span></span>

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

<span data-ttu-id="67916-235">La specifica dell' `@on{EVENT}:preventDefault` attributo senza un valore equivale a `@on{EVENT}:preventDefault="true"` .</span><span class="sxs-lookup"><span data-stu-id="67916-235">Specifying the `@on{EVENT}:preventDefault` attribute without a value is equivalent to `@on{EVENT}:preventDefault="true"`.</span></span>

<span data-ttu-id="67916-236">Il valore dell'attributo può anche essere un'espressione.</span><span class="sxs-lookup"><span data-stu-id="67916-236">The value of the attribute can also be an expression.</span></span> <span data-ttu-id="67916-237">Nell'esempio seguente `shouldPreventDefault` è un `bool` campo impostato su `true` o `false` :</span><span class="sxs-lookup"><span data-stu-id="67916-237">In the following example, `shouldPreventDefault` is a `bool` field set to either `true` or `false`:</span></span>

```razor
<input @onkeypress:preventDefault="shouldPreventDefault" />
```

## <a name="stop-event-propagation"></a><span data-ttu-id="67916-238">Arresta propagazione eventi</span><span class="sxs-lookup"><span data-stu-id="67916-238">Stop event propagation</span></span>

<span data-ttu-id="67916-239">Utilizzare l' [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) attributo Directive per arrestare la propagazione degli eventi.</span><span class="sxs-lookup"><span data-stu-id="67916-239">Use the [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) directive attribute to stop event propagation.</span></span>

<span data-ttu-id="67916-240">Nell'esempio seguente la selezione della casella di controllo impedisce la propagazione degli eventi click dal secondo figlio `<div>` all'elemento padre `<div>` :</span><span class="sxs-lookup"><span data-stu-id="67916-240">In the following example, selecting the check box prevents click events from the second child `<div>` from propagating to the parent `<div>`:</span></span>

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
