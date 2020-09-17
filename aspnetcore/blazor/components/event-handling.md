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
ms.openlocfilehash: cb7009946f56b3c8f1e5eeabb09b81075749c720
ms.sourcegitcommit: 24106b7ffffc9fff410a679863e28aeb2bbe5b7e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/17/2020
ms.locfileid: "90722449"
---
# <a name="aspnet-core-no-locblazor-event-handling"></a><span data-ttu-id="c1271-103">BlazorGestione degli eventi ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c1271-103">ASP.NET Core Blazor event handling</span></span>

<span data-ttu-id="c1271-104">Di [Luke Latham](https://github.com/guardrex) e [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="c1271-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="c1271-105">Razor i componenti di forniscono funzionalità di gestione degli eventi.</span><span class="sxs-lookup"><span data-stu-id="c1271-105">Razor components provide event handling features.</span></span> <span data-ttu-id="c1271-106">Per un attributo dell'elemento HTML denominato, [`@on{EVENT}`](xref:mvc/views/razor#onevent) ad esempio, `@onclick` con un valore tipizzato come delegato, un Razor componente considera il valore dell'attributo come un gestore eventi.</span><span class="sxs-lookup"><span data-stu-id="c1271-106">For an HTML element attribute named [`@on{EVENT}`](xref:mvc/views/razor#onevent) (for example, `@onclick`) with a delegate-typed value, a Razor component treats the attribute's value as an event handler.</span></span>

<span data-ttu-id="c1271-107">Il codice seguente chiama il `UpdateHeading` metodo quando si seleziona il pulsante nell'interfaccia utente:</span><span class="sxs-lookup"><span data-stu-id="c1271-107">The following code calls the `UpdateHeading` method when the button is selected in the UI:</span></span>

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

<span data-ttu-id="c1271-108">Il codice seguente chiama il `CheckChanged` metodo quando la casella di controllo viene modificata nell'interfaccia utente:</span><span class="sxs-lookup"><span data-stu-id="c1271-108">The following code calls the `CheckChanged` method when the check box is changed in the UI:</span></span>

```razor
<input type="checkbox" class="form-check-input" @onchange="CheckChanged" />

@code {
    private void CheckChanged()
    {
        ...
    }
}
```

<span data-ttu-id="c1271-109">I gestori di eventi possono anche essere asincroni e restituire un oggetto <xref:System.Threading.Tasks.Task> .</span><span class="sxs-lookup"><span data-stu-id="c1271-109">Event handlers can also be asynchronous and return a <xref:System.Threading.Tasks.Task>.</span></span> <span data-ttu-id="c1271-110">Non è necessario chiamare manualmente [StateHasChanged](xref:blazor/components/lifecycle#state-changes).</span><span class="sxs-lookup"><span data-stu-id="c1271-110">There's no need to manually call [StateHasChanged](xref:blazor/components/lifecycle#state-changes).</span></span> <span data-ttu-id="c1271-111">Le eccezioni vengono registrate quando si verificano.</span><span class="sxs-lookup"><span data-stu-id="c1271-111">Exceptions are logged when they occur.</span></span>

<span data-ttu-id="c1271-112">Nell'esempio seguente, `UpdateHeading` viene chiamato in modo asincrono quando si seleziona il pulsante:</span><span class="sxs-lookup"><span data-stu-id="c1271-112">In the following example, `UpdateHeading` is called asynchronously when the button is selected:</span></span>

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

## <a name="event-argument-types"></a><span data-ttu-id="c1271-113">Tipi di argomenti dell'evento</span><span class="sxs-lookup"><span data-stu-id="c1271-113">Event argument types</span></span>

<span data-ttu-id="c1271-114">Per alcuni eventi, i tipi di argomento dell'evento sono consentiti.</span><span class="sxs-lookup"><span data-stu-id="c1271-114">For some events, event argument types are permitted.</span></span> <span data-ttu-id="c1271-115">La specifica di un parametro di evento nella definizione di un metodo di evento è facoltativa ed è necessaria solo se il tipo di evento viene usato nel metodo.</span><span class="sxs-lookup"><span data-stu-id="c1271-115">Specifying an event parameter in an event method definition is optional and only necessary if the event type is used in the method.</span></span> <span data-ttu-id="c1271-116">Nell'esempio seguente, l' `MouseEventArgs` argomento dell'evento viene usato nel `ShowMessage` metodo per impostare il testo del messaggio:</span><span class="sxs-lookup"><span data-stu-id="c1271-116">In the following example, the `MouseEventArgs` event argument is used in the `ShowMessage` method to set message text:</span></span>

```csharp
private void ShowMessage(MouseEventArgs e)
{
    messageText = $"The mouse is at coordinates: {e.ScreenX}:{e.ScreenY}";
}
```

<span data-ttu-id="c1271-117"><xref:System.EventArgs>I supportati sono riportati nella tabella seguente.</span><span class="sxs-lookup"><span data-stu-id="c1271-117">Supported <xref:System.EventArgs> are shown in the following table.</span></span>

::: moniker range=">= aspnetcore-5.0"

| <span data-ttu-id="c1271-118">event</span><span class="sxs-lookup"><span data-stu-id="c1271-118">Event</span></span>            | <span data-ttu-id="c1271-119">Classe</span><span class="sxs-lookup"><span data-stu-id="c1271-119">Class</span></span>  | <span data-ttu-id="c1271-120">Eventi e note DOM</span><span class="sxs-lookup"><span data-stu-id="c1271-120">DOM events and notes</span></span> |
| ---------------- | ------ | -------------------- |
| <span data-ttu-id="c1271-121">Appunti</span><span class="sxs-lookup"><span data-stu-id="c1271-121">Clipboard</span></span>        | <xref:Microsoft.AspNetCore.Components.Web.ClipboardEventArgs> | <span data-ttu-id="c1271-122">`oncut`, `oncopy`, `onpaste`</span><span class="sxs-lookup"><span data-stu-id="c1271-122">`oncut`, `oncopy`, `onpaste`</span></span> |
| <span data-ttu-id="c1271-123">Trascinamento</span><span class="sxs-lookup"><span data-stu-id="c1271-123">Drag</span></span>             | <xref:Microsoft.AspNetCore.Components.Web.DragEventArgs> | <span data-ttu-id="c1271-124">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span><span class="sxs-lookup"><span data-stu-id="c1271-124">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span></span><br><br><span data-ttu-id="c1271-125"><xref:Microsoft.AspNetCore.Components.Web.DataTransfer> e <xref:Microsoft.AspNetCore.Components.Web.DataTransferItem> contengono dati di elementi trascinati.</span><span class="sxs-lookup"><span data-stu-id="c1271-125"><xref:Microsoft.AspNetCore.Components.Web.DataTransfer> and <xref:Microsoft.AspNetCore.Components.Web.DataTransferItem> hold dragged item data.</span></span> |
| <span data-ttu-id="c1271-126">Errore</span><span class="sxs-lookup"><span data-stu-id="c1271-126">Error</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.ErrorEventArgs> | `onerror` |
| <span data-ttu-id="c1271-127">event</span><span class="sxs-lookup"><span data-stu-id="c1271-127">Event</span></span>            | <xref:System.EventArgs> | <span data-ttu-id="c1271-128">*Generale*</span><span class="sxs-lookup"><span data-stu-id="c1271-128">*General*</span></span><br><span data-ttu-id="c1271-129">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span><span class="sxs-lookup"><span data-stu-id="c1271-129">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span></span><br><br><span data-ttu-id="c1271-130">*Appunti*</span><span class="sxs-lookup"><span data-stu-id="c1271-130">*Clipboard*</span></span><br><span data-ttu-id="c1271-131">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span><span class="sxs-lookup"><span data-stu-id="c1271-131">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span></span><br><br><span data-ttu-id="c1271-132">*Input*</span><span class="sxs-lookup"><span data-stu-id="c1271-132">*Input*</span></span><br><span data-ttu-id="c1271-133">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`</span><span class="sxs-lookup"><span data-stu-id="c1271-133">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`</span></span><br><br><span data-ttu-id="c1271-134">*Supporti*</span><span class="sxs-lookup"><span data-stu-id="c1271-134">*Media*</span></span><br><span data-ttu-id="c1271-135">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onended`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `ontoggle`, `onvolumechange`, `onwaiting`</span><span class="sxs-lookup"><span data-stu-id="c1271-135">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onended`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `ontoggle`, `onvolumechange`, `onwaiting`</span></span><br><br><span data-ttu-id="c1271-136"><xref:Microsoft.AspNetCore.Components.Web.EventHandlers> include gli attributi per configurare i mapping tra i nomi di evento e i tipi di argomenti dell'evento.</span><span class="sxs-lookup"><span data-stu-id="c1271-136"><xref:Microsoft.AspNetCore.Components.Web.EventHandlers> holds attributes to configure the mappings between event names and event argument types.</span></span> |
| <span data-ttu-id="c1271-137">Focus</span><span class="sxs-lookup"><span data-stu-id="c1271-137">Focus</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.FocusEventArgs> | <span data-ttu-id="c1271-138">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span><span class="sxs-lookup"><span data-stu-id="c1271-138">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span></span><br><br><span data-ttu-id="c1271-139">Non include il supporto per `relatedTarget` .</span><span class="sxs-lookup"><span data-stu-id="c1271-139">Doesn't include support for `relatedTarget`.</span></span> |
| <span data-ttu-id="c1271-140">Input</span><span class="sxs-lookup"><span data-stu-id="c1271-140">Input</span></span>            | <xref:Microsoft.AspNetCore.Components.ChangeEventArgs> | <span data-ttu-id="c1271-141">`onchange`, `oninput`</span><span class="sxs-lookup"><span data-stu-id="c1271-141">`onchange`, `oninput`</span></span> |
| <span data-ttu-id="c1271-142">Tastiera</span><span class="sxs-lookup"><span data-stu-id="c1271-142">Keyboard</span></span>         | <xref:Microsoft.AspNetCore.Components.Web.KeyboardEventArgs> | <span data-ttu-id="c1271-143">`onkeydown`, `onkeypress`, `onkeyup`</span><span class="sxs-lookup"><span data-stu-id="c1271-143">`onkeydown`, `onkeypress`, `onkeyup`</span></span> |
| <span data-ttu-id="c1271-144">Mouse</span><span class="sxs-lookup"><span data-stu-id="c1271-144">Mouse</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs> | <span data-ttu-id="c1271-145">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span><span class="sxs-lookup"><span data-stu-id="c1271-145">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span></span> |
| <span data-ttu-id="c1271-146">Puntatore del mouse</span><span class="sxs-lookup"><span data-stu-id="c1271-146">Mouse pointer</span></span>    | <xref:Microsoft.AspNetCore.Components.Web.PointerEventArgs> | <span data-ttu-id="c1271-147">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span><span class="sxs-lookup"><span data-stu-id="c1271-147">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span></span> |
| <span data-ttu-id="c1271-148">Rotellina del mouse</span><span class="sxs-lookup"><span data-stu-id="c1271-148">Mouse wheel</span></span>      | <xref:Microsoft.AspNetCore.Components.Web.WheelEventArgs> | <span data-ttu-id="c1271-149">`onwheel`, `onmousewheel`</span><span class="sxs-lookup"><span data-stu-id="c1271-149">`onwheel`, `onmousewheel`</span></span> |
| <span data-ttu-id="c1271-150">Avanzamento</span><span class="sxs-lookup"><span data-stu-id="c1271-150">Progress</span></span>         | <xref:Microsoft.AspNetCore.Components.Web.ProgressEventArgs> | <span data-ttu-id="c1271-151">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span><span class="sxs-lookup"><span data-stu-id="c1271-151">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span></span> |
| <span data-ttu-id="c1271-152">Tocco</span><span class="sxs-lookup"><span data-stu-id="c1271-152">Touch</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.TouchEventArgs> | <span data-ttu-id="c1271-153">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span><span class="sxs-lookup"><span data-stu-id="c1271-153">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span></span><br><br><span data-ttu-id="c1271-154"><xref:Microsoft.AspNetCore.Components.Web.TouchPoint> rappresenta un singolo punto di contatto su un dispositivo sensibile al tocco.</span><span class="sxs-lookup"><span data-stu-id="c1271-154"><xref:Microsoft.AspNetCore.Components.Web.TouchPoint> represents a single contact point on a touch-sensitive device.</span></span> |

::: moniker-end

::: moniker range="< aspnetcore-5.0"

| <span data-ttu-id="c1271-155">event</span><span class="sxs-lookup"><span data-stu-id="c1271-155">Event</span></span>            | <span data-ttu-id="c1271-156">Classe</span><span class="sxs-lookup"><span data-stu-id="c1271-156">Class</span></span> | <span data-ttu-id="c1271-157">Eventi e note DOM</span><span class="sxs-lookup"><span data-stu-id="c1271-157">DOM events and notes</span></span> |
| ---------------- | ----- | -------------------- |
| <span data-ttu-id="c1271-158">Appunti</span><span class="sxs-lookup"><span data-stu-id="c1271-158">Clipboard</span></span>        | <xref:Microsoft.AspNetCore.Components.Web.ClipboardEventArgs> | <span data-ttu-id="c1271-159">`oncut`, `oncopy`, `onpaste`</span><span class="sxs-lookup"><span data-stu-id="c1271-159">`oncut`, `oncopy`, `onpaste`</span></span> |
| <span data-ttu-id="c1271-160">Trascinamento</span><span class="sxs-lookup"><span data-stu-id="c1271-160">Drag</span></span>             | <xref:Microsoft.AspNetCore.Components.Web.DragEventArgs> | <span data-ttu-id="c1271-161">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span><span class="sxs-lookup"><span data-stu-id="c1271-161">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span></span><br><br><span data-ttu-id="c1271-162"><xref:Microsoft.AspNetCore.Components.Web.DataTransfer> e <xref:Microsoft.AspNetCore.Components.Web.DataTransferItem> contengono dati di elementi trascinati.</span><span class="sxs-lookup"><span data-stu-id="c1271-162"><xref:Microsoft.AspNetCore.Components.Web.DataTransfer> and <xref:Microsoft.AspNetCore.Components.Web.DataTransferItem> hold dragged item data.</span></span> |
| <span data-ttu-id="c1271-163">Errore</span><span class="sxs-lookup"><span data-stu-id="c1271-163">Error</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.ErrorEventArgs> | `onerror` |
| <span data-ttu-id="c1271-164">event</span><span class="sxs-lookup"><span data-stu-id="c1271-164">Event</span></span>            | <xref:System.EventArgs> | <span data-ttu-id="c1271-165">*Generale*</span><span class="sxs-lookup"><span data-stu-id="c1271-165">*General*</span></span><br><span data-ttu-id="c1271-166">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span><span class="sxs-lookup"><span data-stu-id="c1271-166">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span></span><br><br><span data-ttu-id="c1271-167">*Appunti*</span><span class="sxs-lookup"><span data-stu-id="c1271-167">*Clipboard*</span></span><br><span data-ttu-id="c1271-168">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span><span class="sxs-lookup"><span data-stu-id="c1271-168">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span></span><br><br><span data-ttu-id="c1271-169">*Input*</span><span class="sxs-lookup"><span data-stu-id="c1271-169">*Input*</span></span><br><span data-ttu-id="c1271-170">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`</span><span class="sxs-lookup"><span data-stu-id="c1271-170">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`</span></span><br><br><span data-ttu-id="c1271-171">*Supporti*</span><span class="sxs-lookup"><span data-stu-id="c1271-171">*Media*</span></span><br><span data-ttu-id="c1271-172">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onended`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`</span><span class="sxs-lookup"><span data-stu-id="c1271-172">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onended`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`</span></span><br><br><span data-ttu-id="c1271-173"><xref:Microsoft.AspNetCore.Components.Web.EventHandlers> include gli attributi per configurare i mapping tra i nomi di evento e i tipi di argomenti dell'evento.</span><span class="sxs-lookup"><span data-stu-id="c1271-173"><xref:Microsoft.AspNetCore.Components.Web.EventHandlers> holds attributes to configure the mappings between event names and event argument types.</span></span> |
| <span data-ttu-id="c1271-174">Focus</span><span class="sxs-lookup"><span data-stu-id="c1271-174">Focus</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.FocusEventArgs> | <span data-ttu-id="c1271-175">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span><span class="sxs-lookup"><span data-stu-id="c1271-175">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span></span><br><br><span data-ttu-id="c1271-176">Non include il supporto per `relatedTarget` .</span><span class="sxs-lookup"><span data-stu-id="c1271-176">Doesn't include support for `relatedTarget`.</span></span> |
| <span data-ttu-id="c1271-177">Input</span><span class="sxs-lookup"><span data-stu-id="c1271-177">Input</span></span>            | <xref:Microsoft.AspNetCore.Components.ChangeEventArgs> | <span data-ttu-id="c1271-178">`onchange`, `oninput`</span><span class="sxs-lookup"><span data-stu-id="c1271-178">`onchange`, `oninput`</span></span> |
| <span data-ttu-id="c1271-179">Tastiera</span><span class="sxs-lookup"><span data-stu-id="c1271-179">Keyboard</span></span>         | <xref:Microsoft.AspNetCore.Components.Web.KeyboardEventArgs> | <span data-ttu-id="c1271-180">`onkeydown`, `onkeypress`, `onkeyup`</span><span class="sxs-lookup"><span data-stu-id="c1271-180">`onkeydown`, `onkeypress`, `onkeyup`</span></span> |
| <span data-ttu-id="c1271-181">Mouse</span><span class="sxs-lookup"><span data-stu-id="c1271-181">Mouse</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs> | <span data-ttu-id="c1271-182">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span><span class="sxs-lookup"><span data-stu-id="c1271-182">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span></span> |
| <span data-ttu-id="c1271-183">Puntatore del mouse</span><span class="sxs-lookup"><span data-stu-id="c1271-183">Mouse pointer</span></span>    | <xref:Microsoft.AspNetCore.Components.Web.PointerEventArgs> | <span data-ttu-id="c1271-184">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span><span class="sxs-lookup"><span data-stu-id="c1271-184">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span></span> |
| <span data-ttu-id="c1271-185">Rotellina del mouse</span><span class="sxs-lookup"><span data-stu-id="c1271-185">Mouse wheel</span></span>      | <xref:Microsoft.AspNetCore.Components.Web.WheelEventArgs> | <span data-ttu-id="c1271-186">`onwheel`, `onmousewheel`</span><span class="sxs-lookup"><span data-stu-id="c1271-186">`onwheel`, `onmousewheel`</span></span> |
| <span data-ttu-id="c1271-187">Avanzamento</span><span class="sxs-lookup"><span data-stu-id="c1271-187">Progress</span></span>         | <xref:Microsoft.AspNetCore.Components.Web.ProgressEventArgs> | <span data-ttu-id="c1271-188">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span><span class="sxs-lookup"><span data-stu-id="c1271-188">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span></span> |
| <span data-ttu-id="c1271-189">Tocco</span><span class="sxs-lookup"><span data-stu-id="c1271-189">Touch</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.TouchEventArgs> | <span data-ttu-id="c1271-190">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span><span class="sxs-lookup"><span data-stu-id="c1271-190">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span></span><br><br><span data-ttu-id="c1271-191"><xref:Microsoft.AspNetCore.Components.Web.TouchPoint> rappresenta un singolo punto di contatto su un dispositivo sensibile al tocco.</span><span class="sxs-lookup"><span data-stu-id="c1271-191"><xref:Microsoft.AspNetCore.Components.Web.TouchPoint> represents a single contact point on a touch-sensitive device.</span></span> |

::: moniker-end

<span data-ttu-id="c1271-192">Per altre informazioni, vedere le seguenti risorse:</span><span class="sxs-lookup"><span data-stu-id="c1271-192">For more information, see the following resources:</span></span>

* <span data-ttu-id="c1271-193">[ `EventArgs` classi nell'origine riferimento ASP.NET Core (ramo DotNet/aspnetcore `master` )](https://github.com/dotnet/aspnetcore/tree/master/src/Components/Web/src/Web).</span><span class="sxs-lookup"><span data-stu-id="c1271-193">[`EventArgs` classes in the ASP.NET Core reference source (dotnet/aspnetcore `master` branch)](https://github.com/dotnet/aspnetcore/tree/master/src/Components/Web/src/Web).</span></span> <span data-ttu-id="c1271-194">Il `master` ramo rappresenta l'API in fase di sviluppo per la versione *successiva* del ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c1271-194">The `master` branch represents API under development for the *next* ASP.NET Core release.</span></span> <span data-ttu-id="c1271-195">Per la versione corrente, selezionare il ramo repository GitHub appropriato (ad esempio, `release/3.1` ).</span><span class="sxs-lookup"><span data-stu-id="c1271-195">For the current release, select the appropriate GitHub repository branch (for example, `release/3.1`).</span></span>
* <span data-ttu-id="c1271-196">[MDN Web docs: GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers): include informazioni su quali elementi HTML supportano ogni evento DOM.</span><span class="sxs-lookup"><span data-stu-id="c1271-196">[MDN web docs: GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers): Includes information on which HTML elements support each DOM event.</span></span>

## <a name="lambda-expressions"></a><span data-ttu-id="c1271-197">Espressioni lambda</span><span class="sxs-lookup"><span data-stu-id="c1271-197">Lambda expressions</span></span>

<span data-ttu-id="c1271-198">È inoltre possibile utilizzare le [espressioni lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions) :</span><span class="sxs-lookup"><span data-stu-id="c1271-198">[Lambda expressions](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions) can also be used:</span></span>

```razor
<button @onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

<span data-ttu-id="c1271-199">Spesso è consigliabile chiudere i valori aggiuntivi, ad esempio quando si esegue l'iterazione su un set di elementi.</span><span class="sxs-lookup"><span data-stu-id="c1271-199">It's often convenient to close over additional values, such as when iterating over a set of elements.</span></span> <span data-ttu-id="c1271-200">Nell'esempio seguente vengono creati tre pulsanti, ciascuno dei quali chiama il `UpdateHeading` passaggio di un argomento di evento ( <xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs> ) e il relativo numero di pulsante ( `buttonNumber` ) quando vengono selezionati nell'interfaccia utente:</span><span class="sxs-lookup"><span data-stu-id="c1271-200">The following example creates three buttons, each of which calls `UpdateHeading` passing an event argument (<xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs>) and its button number (`buttonNumber`) when selected in the UI:</span></span>

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
> <span data-ttu-id="c1271-201">**Non** usare direttamente una variabile di ciclo in un'espressione lambda, come `i` nell' `for` esempio di ciclo precedente.</span><span class="sxs-lookup"><span data-stu-id="c1271-201">Do **not** use a loop variable directly in a lambda expression, such as `i` in the preceding `for` loop example.</span></span> <span data-ttu-id="c1271-202">In caso contrario, la stessa variabile viene utilizzata da tutte le espressioni lambda, che comporta l'utilizzo dello stesso valore in tutte le espressioni lambda.</span><span class="sxs-lookup"><span data-stu-id="c1271-202">Otherwise, the same variable is used by all lambda expressions, which results in use of the same value in all lambdas.</span></span> <span data-ttu-id="c1271-203">Acquisire sempre il valore della variabile in una variabile locale e quindi usarlo.</span><span class="sxs-lookup"><span data-stu-id="c1271-203">Always capture the variable's value in a local variable and then use it.</span></span> <span data-ttu-id="c1271-204">Nell'esempio precedente, la variabile di ciclo `i` viene assegnata a `buttonNumber` .</span><span class="sxs-lookup"><span data-stu-id="c1271-204">In the preceding example, the loop variable `i` is assigned to `buttonNumber`.</span></span>

## <a name="eventcallback"></a><span data-ttu-id="c1271-205">EventCallback</span><span class="sxs-lookup"><span data-stu-id="c1271-205">EventCallback</span></span>

<span data-ttu-id="c1271-206">Uno scenario comune con i componenti annidati è la volontà di eseguire il metodo di un componente padre quando si verifica un evento del componente figlio.</span><span class="sxs-lookup"><span data-stu-id="c1271-206">A common scenario with nested components is the desire to run a parent component's method when a child component event occurs.</span></span> <span data-ttu-id="c1271-207">Un `onclick` evento che si verifica nel componente figlio è un caso d'uso comune.</span><span class="sxs-lookup"><span data-stu-id="c1271-207">An `onclick` event occurring in the child component is a common use case.</span></span> <span data-ttu-id="c1271-208">Per esporre gli eventi tra i componenti, usare un oggetto <xref:Microsoft.AspNetCore.Components.EventCallback> .</span><span class="sxs-lookup"><span data-stu-id="c1271-208">To expose events across components, use an <xref:Microsoft.AspNetCore.Components.EventCallback>.</span></span> <span data-ttu-id="c1271-209">Un componente padre può assegnare un metodo di callback a un componente figlio <xref:Microsoft.AspNetCore.Components.EventCallback> .</span><span class="sxs-lookup"><span data-stu-id="c1271-209">A parent component can assign a callback method to a child component's <xref:Microsoft.AspNetCore.Components.EventCallback>.</span></span>

<span data-ttu-id="c1271-210">`ChildComponent`Nell'app di esempio ( `Components/ChildComponent.razor` ) viene illustrato come viene configurato il gestore di un pulsante `onclick` per ricevere un <xref:Microsoft.AspNetCore.Components.EventCallback> delegato dall'oggetto dell'esempio `ParentComponent` .</span><span class="sxs-lookup"><span data-stu-id="c1271-210">The `ChildComponent` in the sample app (`Components/ChildComponent.razor`) demonstrates how a button's `onclick` handler is set up to receive an <xref:Microsoft.AspNetCore.Components.EventCallback> delegate from the sample's `ParentComponent`.</span></span> <span data-ttu-id="c1271-211"><xref:Microsoft.AspNetCore.Components.EventCallback>Viene digitato con `MouseEventArgs` , appropriato per un `onclick` evento da un dispositivo periferico:</span><span class="sxs-lookup"><span data-stu-id="c1271-211">The <xref:Microsoft.AspNetCore.Components.EventCallback> is typed with `MouseEventArgs`, which is appropriate for an `onclick` event from a peripheral device:</span></span>

[!code-razor[](../common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=5-7,17-18)]

<span data-ttu-id="c1271-212">`ParentComponent`Imposta l'oggetto () del figlio sul <xref:Microsoft.AspNetCore.Components.EventCallback%601> `OnClickCallback` relativo `ShowMessage` metodo.</span><span class="sxs-lookup"><span data-stu-id="c1271-212">The `ParentComponent` sets the child's <xref:Microsoft.AspNetCore.Components.EventCallback%601> (`OnClickCallback`) to its `ShowMessage` method.</span></span>

<span data-ttu-id="c1271-213">`Pages/ParentComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="c1271-213">`Pages/ParentComponent.razor`:</span></span>

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

<span data-ttu-id="c1271-214">Quando il pulsante è selezionato in `ChildComponent` :</span><span class="sxs-lookup"><span data-stu-id="c1271-214">When the button is selected in the `ChildComponent`:</span></span>

* <span data-ttu-id="c1271-215">`ParentComponent` `ShowMessage` Viene chiamato il metodo di.</span><span class="sxs-lookup"><span data-stu-id="c1271-215">The `ParentComponent`'s `ShowMessage` method is called.</span></span> <span data-ttu-id="c1271-216">`messageText` viene aggiornato e visualizzato nell'oggetto `ParentComponent` .</span><span class="sxs-lookup"><span data-stu-id="c1271-216">`messageText` is updated and displayed in the `ParentComponent`.</span></span>
* <span data-ttu-id="c1271-217">Una chiamata a [`StateHasChanged`](xref:blazor/components/lifecycle#state-changes) non è obbligatoria nel metodo del callback ( `ShowMessage` ).</span><span class="sxs-lookup"><span data-stu-id="c1271-217">A call to [`StateHasChanged`](xref:blazor/components/lifecycle#state-changes) isn't required in the callback's method (`ShowMessage`).</span></span> <span data-ttu-id="c1271-218"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> viene chiamato automaticamente per eseguire il rendering di `ParentComponent` , proprio come gli eventi figlio attivano il rendering dei componenti nei gestori eventi che vengono eseguiti all'interno dell'elemento figlio.</span><span class="sxs-lookup"><span data-stu-id="c1271-218"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called automatically to rerender the `ParentComponent`, just as child events trigger component rerendering in event handlers that execute within the child.</span></span>

<span data-ttu-id="c1271-219"><xref:Microsoft.AspNetCore.Components.EventCallback> e <xref:Microsoft.AspNetCore.Components.EventCallback%601> consentono delegati asincroni.</span><span class="sxs-lookup"><span data-stu-id="c1271-219"><xref:Microsoft.AspNetCore.Components.EventCallback> and <xref:Microsoft.AspNetCore.Components.EventCallback%601> permit asynchronous delegates.</span></span> <span data-ttu-id="c1271-220"><xref:Microsoft.AspNetCore.Components.EventCallback> è debolmente tipizzato e consente di passare qualsiasi argomento di tipo in `InvokeAsync(Object)` .</span><span class="sxs-lookup"><span data-stu-id="c1271-220"><xref:Microsoft.AspNetCore.Components.EventCallback> is weakly typed and allows passing any type argument in `InvokeAsync(Object)`.</span></span> <span data-ttu-id="c1271-221"><xref:Microsoft.AspNetCore.Components.EventCallback%601> è fortemente tipizzato e richiede il passaggio di un `T` argomento in `InvokeAsync(T)` assegnabile a `TValue` .</span><span class="sxs-lookup"><span data-stu-id="c1271-221"><xref:Microsoft.AspNetCore.Components.EventCallback%601> is strongly typed and requires passing a `T` argument in `InvokeAsync(T)` that's assignable to `TValue`.</span></span>

```razor
<ChildComponent 
    OnClickCallback="@(async () => { await Task.Yield(); messageText = "Blaze It!"; })" />
```

<span data-ttu-id="c1271-222">Richiamare <xref:Microsoft.AspNetCore.Components.EventCallback> o <xref:Microsoft.AspNetCore.Components.EventCallback%601> con <xref:Microsoft.AspNetCore.Components.EventCallback.InvokeAsync%2A> e attendere <xref:System.Threading.Tasks.Task> :</span><span class="sxs-lookup"><span data-stu-id="c1271-222">Invoke an <xref:Microsoft.AspNetCore.Components.EventCallback> or <xref:Microsoft.AspNetCore.Components.EventCallback%601> with <xref:Microsoft.AspNetCore.Components.EventCallback.InvokeAsync%2A> and await the <xref:System.Threading.Tasks.Task>:</span></span>

```csharp
await OnClickCallback.InvokeAsync(arg);
```

<span data-ttu-id="c1271-223">Usare <xref:Microsoft.AspNetCore.Components.EventCallback> e <xref:Microsoft.AspNetCore.Components.EventCallback%601> per la gestione degli eventi e i parametri del componente di associazione.</span><span class="sxs-lookup"><span data-stu-id="c1271-223">Use <xref:Microsoft.AspNetCore.Components.EventCallback> and <xref:Microsoft.AspNetCore.Components.EventCallback%601> for event handling and binding component parameters.</span></span>

<span data-ttu-id="c1271-224">Preferisci l'oggetto fortemente tipizzato <xref:Microsoft.AspNetCore.Components.EventCallback%601> <xref:Microsoft.AspNetCore.Components.EventCallback> .</span><span class="sxs-lookup"><span data-stu-id="c1271-224">Prefer the strongly typed <xref:Microsoft.AspNetCore.Components.EventCallback%601> over <xref:Microsoft.AspNetCore.Components.EventCallback>.</span></span> <span data-ttu-id="c1271-225"><xref:Microsoft.AspNetCore.Components.EventCallback%601> fornisce un migliore feedback sugli errori agli utenti del componente.</span><span class="sxs-lookup"><span data-stu-id="c1271-225"><xref:Microsoft.AspNetCore.Components.EventCallback%601> provides better error feedback to users of the component.</span></span> <span data-ttu-id="c1271-226">Analogamente ad altri gestori di eventi dell'interfaccia utente, la specifica del parametro dell'evento è facoltativa.</span><span class="sxs-lookup"><span data-stu-id="c1271-226">Similar to other UI event handlers, specifying the event parameter is optional.</span></span> <span data-ttu-id="c1271-227">Usare <xref:Microsoft.AspNetCore.Components.EventCallback> quando non è stato passato alcun valore al callback.</span><span class="sxs-lookup"><span data-stu-id="c1271-227">Use <xref:Microsoft.AspNetCore.Components.EventCallback> when there's no value passed to the callback.</span></span>

## <a name="prevent-default-actions"></a><span data-ttu-id="c1271-228">Impedisci azioni predefinite</span><span class="sxs-lookup"><span data-stu-id="c1271-228">Prevent default actions</span></span>

<span data-ttu-id="c1271-229">Utilizzare l' [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) attributo Directive per impedire l'azione predefinita per un evento.</span><span class="sxs-lookup"><span data-stu-id="c1271-229">Use the [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) directive attribute to prevent the default action for an event.</span></span>

<span data-ttu-id="c1271-230">Quando si seleziona un tasto in un dispositivo di input e lo stato attivo dell'elemento si trova in una casella di testo, in un browser viene in genere visualizzato il carattere della chiave nella casella di testo.</span><span class="sxs-lookup"><span data-stu-id="c1271-230">When a key is selected on an input device and the element focus is on a text box, a browser normally displays the key's character in the text box.</span></span> <span data-ttu-id="c1271-231">Nell'esempio seguente viene impedito il comportamento predefinito specificando l' `@onkeypress:preventDefault` attributo della direttiva.</span><span class="sxs-lookup"><span data-stu-id="c1271-231">In the following example, the default behavior is prevented by specifying the `@onkeypress:preventDefault` directive attribute.</span></span> <span data-ttu-id="c1271-232">Il contatore viene incrementato e la **+** chiave non viene acquisita nel `<input>` valore dell'elemento:</span><span class="sxs-lookup"><span data-stu-id="c1271-232">The counter increments, and the **+** key isn't captured into the `<input>` element's value:</span></span>

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

<span data-ttu-id="c1271-233">La specifica dell' `@on{EVENT}:preventDefault` attributo senza un valore equivale a `@on{EVENT}:preventDefault="true"` .</span><span class="sxs-lookup"><span data-stu-id="c1271-233">Specifying the `@on{EVENT}:preventDefault` attribute without a value is equivalent to `@on{EVENT}:preventDefault="true"`.</span></span>

<span data-ttu-id="c1271-234">Il valore dell'attributo può anche essere un'espressione.</span><span class="sxs-lookup"><span data-stu-id="c1271-234">The value of the attribute can also be an expression.</span></span> <span data-ttu-id="c1271-235">Nell'esempio seguente `shouldPreventDefault` è un `bool` campo impostato su `true` o `false` :</span><span class="sxs-lookup"><span data-stu-id="c1271-235">In the following example, `shouldPreventDefault` is a `bool` field set to either `true` or `false`:</span></span>

```razor
<input @onkeypress:preventDefault="shouldPreventDefault" />
```

## <a name="stop-event-propagation"></a><span data-ttu-id="c1271-236">Arresta propagazione eventi</span><span class="sxs-lookup"><span data-stu-id="c1271-236">Stop event propagation</span></span>

<span data-ttu-id="c1271-237">Utilizzare l' [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) attributo Directive per arrestare la propagazione degli eventi.</span><span class="sxs-lookup"><span data-stu-id="c1271-237">Use the [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) directive attribute to stop event propagation.</span></span>

<span data-ttu-id="c1271-238">Nell'esempio seguente la selezione della casella di controllo impedisce la propagazione degli eventi click dal secondo figlio `<div>` all'elemento padre `<div>` :</span><span class="sxs-lookup"><span data-stu-id="c1271-238">In the following example, selecting the check box prevents click events from the second child `<div>` from propagating to the parent `<div>`:</span></span>

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
