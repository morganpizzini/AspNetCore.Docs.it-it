---
<span data-ttu-id="68cd4-101">title:' ASP.NET Core Blazor gestione eventi ' Author: Description:' informazioni sulle Blazor funzionalità di gestione degli eventi, inclusi i tipi di argomenti dell'evento, i callback degli eventi e la gestione degli eventi predefiniti del browser.</span><span class="sxs-lookup"><span data-stu-id="68cd4-101">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="68cd4-102">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="68cd4-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68cd4-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68cd4-103">'Blazor'</span></span>
- <span data-ttu-id="68cd4-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68cd4-104">'Identity'</span></span>
- <span data-ttu-id="68cd4-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68cd4-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="68cd4-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68cd4-106">'Razor'</span></span>
- <span data-ttu-id="68cd4-107">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="68cd4-107">'SignalR' uid:</span></span> 

---
# <a name="aspnet-core-blazor-event-handling"></a><span data-ttu-id="68cd4-108">BlazorGestione degli eventi ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="68cd4-108">ASP.NET Core Blazor event handling</span></span>

<span data-ttu-id="68cd4-109">Di [Luke Latham](https://github.com/guardrex) e [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="68cd4-109">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

Razor<span data-ttu-id="68cd4-110">i componenti di forniscono funzionalità di gestione degli eventi.</span><span class="sxs-lookup"><span data-stu-id="68cd4-110"> components provide event handling features.</span></span> <span data-ttu-id="68cd4-111">Per un attributo dell'elemento HTML denominato, [`@on{EVENT}`](xref:mvc/views/razor#onevent) ad esempio, `@onclick` con un valore tipizzato come delegato, un Razor componente considera il valore dell'attributo come un gestore eventi.</span><span class="sxs-lookup"><span data-stu-id="68cd4-111">For an HTML element attribute named [`@on{EVENT}`](xref:mvc/views/razor#onevent) (for example, `@onclick`) with a delegate-typed value, a Razor component treats the attribute's value as an event handler.</span></span>

<span data-ttu-id="68cd4-112">Il codice seguente chiama il `UpdateHeading` metodo quando si seleziona il pulsante nell'interfaccia utente:</span><span class="sxs-lookup"><span data-stu-id="68cd4-112">The following code calls the `UpdateHeading` method when the button is selected in the UI:</span></span>

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

<span data-ttu-id="68cd4-113">Il codice seguente chiama il `CheckChanged` metodo quando la casella di controllo viene modificata nell'interfaccia utente:</span><span class="sxs-lookup"><span data-stu-id="68cd4-113">The following code calls the `CheckChanged` method when the check box is changed in the UI:</span></span>

```razor
<input type="checkbox" class="form-check-input" @onchange="CheckChanged" />

@code {
    private void CheckChanged()
    {
        ...
    }
}
```

<span data-ttu-id="68cd4-114">I gestori di eventi possono anche essere asincroni e restituire un oggetto <xref:System.Threading.Tasks.Task> .</span><span class="sxs-lookup"><span data-stu-id="68cd4-114">Event handlers can also be asynchronous and return a <xref:System.Threading.Tasks.Task>.</span></span> <span data-ttu-id="68cd4-115">Non è necessario chiamare manualmente [StateHasChanged](xref:blazor/lifecycle#state-changes).</span><span class="sxs-lookup"><span data-stu-id="68cd4-115">There's no need to manually call [StateHasChanged](xref:blazor/lifecycle#state-changes).</span></span> <span data-ttu-id="68cd4-116">Le eccezioni vengono registrate quando si verificano.</span><span class="sxs-lookup"><span data-stu-id="68cd4-116">Exceptions are logged when they occur.</span></span>

<span data-ttu-id="68cd4-117">Nell'esempio seguente, `UpdateHeading` viene chiamato in modo asincrono quando si seleziona il pulsante:</span><span class="sxs-lookup"><span data-stu-id="68cd4-117">In the following example, `UpdateHeading` is called asynchronously when the button is selected:</span></span>

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

## <a name="event-argument-types"></a><span data-ttu-id="68cd4-118">Tipi di argomenti dell'evento</span><span class="sxs-lookup"><span data-stu-id="68cd4-118">Event argument types</span></span>

<span data-ttu-id="68cd4-119">Per alcuni eventi, i tipi di argomento dell'evento sono consentiti.</span><span class="sxs-lookup"><span data-stu-id="68cd4-119">For some events, event argument types are permitted.</span></span> <span data-ttu-id="68cd4-120">La specifica di un tipo di evento nella chiamata al metodo è necessaria solo se il tipo di evento viene usato nel metodo.</span><span class="sxs-lookup"><span data-stu-id="68cd4-120">Specifying an event type in the method call is only necessary if the event type is used in the method.</span></span>

<span data-ttu-id="68cd4-121"><xref:System.EventArgs>I supportati sono riportati nella tabella seguente.</span><span class="sxs-lookup"><span data-stu-id="68cd4-121">Supported <xref:System.EventArgs> are shown in the following table.</span></span>

| <span data-ttu-id="68cd4-122">Evento</span><span class="sxs-lookup"><span data-stu-id="68cd4-122">Event</span></span>            | <span data-ttu-id="68cd4-123">Classe</span><span class="sxs-lookup"><span data-stu-id="68cd4-123">Class</span></span>                | <span data-ttu-id="68cd4-124">Eventi e note DOM</span><span class="sxs-lookup"><span data-stu-id="68cd4-124">DOM events and notes</span></span> |
| ---
<span data-ttu-id="68cd4-125">title:' ASP.NET Core Blazor gestione eventi ' Author: Description:' informazioni sulle Blazor funzionalità di gestione degli eventi, inclusi i tipi di argomenti dell'evento, i callback degli eventi e la gestione degli eventi predefiniti del browser.</span><span class="sxs-lookup"><span data-stu-id="68cd4-125">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="68cd4-126">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="68cd4-126">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68cd4-127">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68cd4-127">'Blazor'</span></span>
- <span data-ttu-id="68cd4-128">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68cd4-128">'Identity'</span></span>
- <span data-ttu-id="68cd4-129">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68cd4-129">'Let's Encrypt'</span></span>
- <span data-ttu-id="68cd4-130">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68cd4-130">'Razor'</span></span>
- <span data-ttu-id="68cd4-131">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="68cd4-131">'SignalR' uid:</span></span> 

-
<span data-ttu-id="68cd4-132">title:' ASP.NET Core Blazor gestione eventi ' Author: Description:' informazioni sulle Blazor funzionalità di gestione degli eventi, inclusi i tipi di argomenti dell'evento, i callback degli eventi e la gestione degli eventi predefiniti del browser.</span><span class="sxs-lookup"><span data-stu-id="68cd4-132">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="68cd4-133">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="68cd4-133">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68cd4-134">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68cd4-134">'Blazor'</span></span>
- <span data-ttu-id="68cd4-135">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68cd4-135">'Identity'</span></span>
- <span data-ttu-id="68cd4-136">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68cd4-136">'Let's Encrypt'</span></span>
- <span data-ttu-id="68cd4-137">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68cd4-137">'Razor'</span></span>
- <span data-ttu-id="68cd4-138">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="68cd4-138">'SignalR' uid:</span></span> 

-
<span data-ttu-id="68cd4-139">title:' ASP.NET Core Blazor gestione eventi ' Author: Description:' informazioni sulle Blazor funzionalità di gestione degli eventi, inclusi i tipi di argomenti dell'evento, i callback degli eventi e la gestione degli eventi predefiniti del browser.</span><span class="sxs-lookup"><span data-stu-id="68cd4-139">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="68cd4-140">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="68cd4-140">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68cd4-141">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68cd4-141">'Blazor'</span></span>
- <span data-ttu-id="68cd4-142">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68cd4-142">'Identity'</span></span>
- <span data-ttu-id="68cd4-143">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68cd4-143">'Let's Encrypt'</span></span>
- <span data-ttu-id="68cd4-144">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68cd4-144">'Razor'</span></span>
- <span data-ttu-id="68cd4-145">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="68cd4-145">'SignalR' uid:</span></span> 

-
<span data-ttu-id="68cd4-146">title:' ASP.NET Core Blazor gestione eventi ' Author: Description:' informazioni sulle Blazor funzionalità di gestione degli eventi, inclusi i tipi di argomenti dell'evento, i callback degli eventi e la gestione degli eventi predefiniti del browser.</span><span class="sxs-lookup"><span data-stu-id="68cd4-146">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="68cd4-147">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="68cd4-147">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68cd4-148">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68cd4-148">'Blazor'</span></span>
- <span data-ttu-id="68cd4-149">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68cd4-149">'Identity'</span></span>
- <span data-ttu-id="68cd4-150">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68cd4-150">'Let's Encrypt'</span></span>
- <span data-ttu-id="68cd4-151">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68cd4-151">'Razor'</span></span>
- <span data-ttu-id="68cd4-152">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="68cd4-152">'SignalR' uid:</span></span> 

-
<span data-ttu-id="68cd4-153">title:' ASP.NET Core Blazor gestione eventi ' Author: Description:' informazioni sulle Blazor funzionalità di gestione degli eventi, inclusi i tipi di argomenti dell'evento, i callback degli eventi e la gestione degli eventi predefiniti del browser.</span><span class="sxs-lookup"><span data-stu-id="68cd4-153">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="68cd4-154">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="68cd4-154">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68cd4-155">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68cd4-155">'Blazor'</span></span>
- <span data-ttu-id="68cd4-156">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68cd4-156">'Identity'</span></span>
- <span data-ttu-id="68cd4-157">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68cd4-157">'Let's Encrypt'</span></span>
- <span data-ttu-id="68cd4-158">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68cd4-158">'Razor'</span></span>
- <span data-ttu-id="68cd4-159">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="68cd4-159">'SignalR' uid:</span></span> 

-
<span data-ttu-id="68cd4-160">title:' ASP.NET Core Blazor gestione eventi ' Author: Description:' informazioni sulle Blazor funzionalità di gestione degli eventi, inclusi i tipi di argomenti dell'evento, i callback degli eventi e la gestione degli eventi predefiniti del browser.</span><span class="sxs-lookup"><span data-stu-id="68cd4-160">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="68cd4-161">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="68cd4-161">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68cd4-162">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68cd4-162">'Blazor'</span></span>
- <span data-ttu-id="68cd4-163">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68cd4-163">'Identity'</span></span>
- <span data-ttu-id="68cd4-164">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68cd4-164">'Let's Encrypt'</span></span>
- <span data-ttu-id="68cd4-165">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68cd4-165">'Razor'</span></span>
- <span data-ttu-id="68cd4-166">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="68cd4-166">'SignalR' uid:</span></span> 

<span data-ttu-id="68cd4-167">-------- | Titolo---: "ASP.NET Core Blazor gestione eventi" autore: Descrizione: "informazioni sulle Blazor funzionalità di gestione degli eventi, inclusi i tipi di argomenti degli eventi, callback di eventi e gestione degli eventi del browser predefiniti".</span><span class="sxs-lookup"><span data-stu-id="68cd4-167">-------- | --- title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="68cd4-168">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="68cd4-168">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68cd4-169">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68cd4-169">'Blazor'</span></span>
- <span data-ttu-id="68cd4-170">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68cd4-170">'Identity'</span></span>
- <span data-ttu-id="68cd4-171">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68cd4-171">'Let's Encrypt'</span></span>
- <span data-ttu-id="68cd4-172">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68cd4-172">'Razor'</span></span>
- <span data-ttu-id="68cd4-173">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="68cd4-173">'SignalR' uid:</span></span> 

-
<span data-ttu-id="68cd4-174">title:' ASP.NET Core Blazor gestione eventi ' Author: Description:' informazioni sulle Blazor funzionalità di gestione degli eventi, inclusi i tipi di argomenti dell'evento, i callback degli eventi e la gestione degli eventi predefiniti del browser.</span><span class="sxs-lookup"><span data-stu-id="68cd4-174">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="68cd4-175">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="68cd4-175">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68cd4-176">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68cd4-176">'Blazor'</span></span>
- <span data-ttu-id="68cd4-177">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68cd4-177">'Identity'</span></span>
- <span data-ttu-id="68cd4-178">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68cd4-178">'Let's Encrypt'</span></span>
- <span data-ttu-id="68cd4-179">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68cd4-179">'Razor'</span></span>
- <span data-ttu-id="68cd4-180">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="68cd4-180">'SignalR' uid:</span></span> 

-
<span data-ttu-id="68cd4-181">title:' ASP.NET Core Blazor gestione eventi ' Author: Description:' informazioni sulle Blazor funzionalità di gestione degli eventi, inclusi i tipi di argomenti dell'evento, i callback degli eventi e la gestione degli eventi predefiniti del browser.</span><span class="sxs-lookup"><span data-stu-id="68cd4-181">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="68cd4-182">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="68cd4-182">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68cd4-183">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68cd4-183">'Blazor'</span></span>
- <span data-ttu-id="68cd4-184">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68cd4-184">'Identity'</span></span>
- <span data-ttu-id="68cd4-185">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68cd4-185">'Let's Encrypt'</span></span>
- <span data-ttu-id="68cd4-186">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68cd4-186">'Razor'</span></span>
- <span data-ttu-id="68cd4-187">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="68cd4-187">'SignalR' uid:</span></span> 

-
<span data-ttu-id="68cd4-188">title:' ASP.NET Core Blazor gestione eventi ' Author: Description:' informazioni sulle Blazor funzionalità di gestione degli eventi, inclusi i tipi di argomenti dell'evento, i callback degli eventi e la gestione degli eventi predefiniti del browser.</span><span class="sxs-lookup"><span data-stu-id="68cd4-188">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="68cd4-189">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="68cd4-189">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68cd4-190">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68cd4-190">'Blazor'</span></span>
- <span data-ttu-id="68cd4-191">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68cd4-191">'Identity'</span></span>
- <span data-ttu-id="68cd4-192">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68cd4-192">'Let's Encrypt'</span></span>
- <span data-ttu-id="68cd4-193">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68cd4-193">'Razor'</span></span>
- <span data-ttu-id="68cd4-194">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="68cd4-194">'SignalR' uid:</span></span> 

-
<span data-ttu-id="68cd4-195">title:' ASP.NET Core Blazor gestione eventi ' Author: Description:' informazioni sulle Blazor funzionalità di gestione degli eventi, inclusi i tipi di argomenti dell'evento, i callback degli eventi e la gestione degli eventi predefiniti del browser.</span><span class="sxs-lookup"><span data-stu-id="68cd4-195">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="68cd4-196">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="68cd4-196">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68cd4-197">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68cd4-197">'Blazor'</span></span>
- <span data-ttu-id="68cd4-198">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68cd4-198">'Identity'</span></span>
- <span data-ttu-id="68cd4-199">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68cd4-199">'Let's Encrypt'</span></span>
- <span data-ttu-id="68cd4-200">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68cd4-200">'Razor'</span></span>
- <span data-ttu-id="68cd4-201">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="68cd4-201">'SignalR' uid:</span></span> 

-
<span data-ttu-id="68cd4-202">title:' ASP.NET Core Blazor gestione eventi ' Author: Description:' informazioni sulle Blazor funzionalità di gestione degli eventi, inclusi i tipi di argomenti dell'evento, i callback degli eventi e la gestione degli eventi predefiniti del browser.</span><span class="sxs-lookup"><span data-stu-id="68cd4-202">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="68cd4-203">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="68cd4-203">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68cd4-204">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68cd4-204">'Blazor'</span></span>
- <span data-ttu-id="68cd4-205">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68cd4-205">'Identity'</span></span>
- <span data-ttu-id="68cd4-206">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68cd4-206">'Let's Encrypt'</span></span>
- <span data-ttu-id="68cd4-207">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68cd4-207">'Razor'</span></span>
- <span data-ttu-id="68cd4-208">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="68cd4-208">'SignalR' uid:</span></span> 

-
<span data-ttu-id="68cd4-209">title:' ASP.NET Core Blazor gestione eventi ' Author: Description:' informazioni sulle Blazor funzionalità di gestione degli eventi, inclusi i tipi di argomenti dell'evento, i callback degli eventi e la gestione degli eventi predefiniti del browser.</span><span class="sxs-lookup"><span data-stu-id="68cd4-209">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="68cd4-210">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="68cd4-210">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68cd4-211">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68cd4-211">'Blazor'</span></span>
- <span data-ttu-id="68cd4-212">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68cd4-212">'Identity'</span></span>
- <span data-ttu-id="68cd4-213">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68cd4-213">'Let's Encrypt'</span></span>
- <span data-ttu-id="68cd4-214">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68cd4-214">'Razor'</span></span>
- <span data-ttu-id="68cd4-215">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="68cd4-215">'SignalR' uid:</span></span> 

-
<span data-ttu-id="68cd4-216">title:' ASP.NET Core Blazor gestione eventi ' Author: Description:' informazioni sulle Blazor funzionalità di gestione degli eventi, inclusi i tipi di argomenti dell'evento, i callback degli eventi e la gestione degli eventi predefiniti del browser.</span><span class="sxs-lookup"><span data-stu-id="68cd4-216">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="68cd4-217">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="68cd4-217">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68cd4-218">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68cd4-218">'Blazor'</span></span>
- <span data-ttu-id="68cd4-219">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68cd4-219">'Identity'</span></span>
- <span data-ttu-id="68cd4-220">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68cd4-220">'Let's Encrypt'</span></span>
- <span data-ttu-id="68cd4-221">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68cd4-221">'Razor'</span></span>
- <span data-ttu-id="68cd4-222">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="68cd4-222">'SignalR' uid:</span></span> 

<span data-ttu-id="68cd4-223">---------- | Titolo---: "ASP.NET Core Blazor gestione eventi" autore: Descrizione: "informazioni sulle Blazor funzionalità di gestione degli eventi, inclusi i tipi di argomenti degli eventi, callback di eventi e gestione degli eventi del browser predefiniti".</span><span class="sxs-lookup"><span data-stu-id="68cd4-223">---------- | --- title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="68cd4-224">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="68cd4-224">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68cd4-225">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68cd4-225">'Blazor'</span></span>
- <span data-ttu-id="68cd4-226">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68cd4-226">'Identity'</span></span>
- <span data-ttu-id="68cd4-227">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68cd4-227">'Let's Encrypt'</span></span>
- <span data-ttu-id="68cd4-228">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68cd4-228">'Razor'</span></span>
- <span data-ttu-id="68cd4-229">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="68cd4-229">'SignalR' uid:</span></span> 

-
<span data-ttu-id="68cd4-230">title:' ASP.NET Core Blazor gestione eventi ' Author: Description:' informazioni sulle Blazor funzionalità di gestione degli eventi, inclusi i tipi di argomenti dell'evento, i callback degli eventi e la gestione degli eventi predefiniti del browser.</span><span class="sxs-lookup"><span data-stu-id="68cd4-230">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="68cd4-231">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="68cd4-231">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68cd4-232">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68cd4-232">'Blazor'</span></span>
- <span data-ttu-id="68cd4-233">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68cd4-233">'Identity'</span></span>
- <span data-ttu-id="68cd4-234">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68cd4-234">'Let's Encrypt'</span></span>
- <span data-ttu-id="68cd4-235">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68cd4-235">'Razor'</span></span>
- <span data-ttu-id="68cd4-236">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="68cd4-236">'SignalR' uid:</span></span> 

-
<span data-ttu-id="68cd4-237">title:' ASP.NET Core Blazor gestione eventi ' Author: Description:' informazioni sulle Blazor funzionalità di gestione degli eventi, inclusi i tipi di argomenti dell'evento, i callback degli eventi e la gestione degli eventi predefiniti del browser.</span><span class="sxs-lookup"><span data-stu-id="68cd4-237">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="68cd4-238">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="68cd4-238">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68cd4-239">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68cd4-239">'Blazor'</span></span>
- <span data-ttu-id="68cd4-240">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68cd4-240">'Identity'</span></span>
- <span data-ttu-id="68cd4-241">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68cd4-241">'Let's Encrypt'</span></span>
- <span data-ttu-id="68cd4-242">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68cd4-242">'Razor'</span></span>
- <span data-ttu-id="68cd4-243">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="68cd4-243">'SignalR' uid:</span></span> 

-
<span data-ttu-id="68cd4-244">title:' ASP.NET Core Blazor gestione eventi ' Author: Description:' informazioni sulle Blazor funzionalità di gestione degli eventi, inclusi i tipi di argomenti dell'evento, i callback degli eventi e la gestione degli eventi predefiniti del browser.</span><span class="sxs-lookup"><span data-stu-id="68cd4-244">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="68cd4-245">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="68cd4-245">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68cd4-246">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68cd4-246">'Blazor'</span></span>
- <span data-ttu-id="68cd4-247">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68cd4-247">'Identity'</span></span>
- <span data-ttu-id="68cd4-248">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68cd4-248">'Let's Encrypt'</span></span>
- <span data-ttu-id="68cd4-249">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68cd4-249">'Razor'</span></span>
- <span data-ttu-id="68cd4-250">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="68cd4-250">'SignalR' uid:</span></span> 

-
<span data-ttu-id="68cd4-251">title:' ASP.NET Core Blazor gestione eventi ' Author: Description:' informazioni sulle Blazor funzionalità di gestione degli eventi, inclusi i tipi di argomenti dell'evento, i callback degli eventi e la gestione degli eventi predefiniti del browser.</span><span class="sxs-lookup"><span data-stu-id="68cd4-251">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="68cd4-252">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="68cd4-252">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68cd4-253">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68cd4-253">'Blazor'</span></span>
- <span data-ttu-id="68cd4-254">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68cd4-254">'Identity'</span></span>
- <span data-ttu-id="68cd4-255">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68cd4-255">'Let's Encrypt'</span></span>
- <span data-ttu-id="68cd4-256">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68cd4-256">'Razor'</span></span>
- <span data-ttu-id="68cd4-257">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="68cd4-257">'SignalR' uid:</span></span> 

-
<span data-ttu-id="68cd4-258">title:' ASP.NET Core Blazor gestione eventi ' Author: Description:' informazioni sulle Blazor funzionalità di gestione degli eventi, inclusi i tipi di argomenti dell'evento, i callback degli eventi e la gestione degli eventi predefiniti del browser.</span><span class="sxs-lookup"><span data-stu-id="68cd4-258">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="68cd4-259">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="68cd4-259">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68cd4-260">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68cd4-260">'Blazor'</span></span>
- <span data-ttu-id="68cd4-261">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68cd4-261">'Identity'</span></span>
- <span data-ttu-id="68cd4-262">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68cd4-262">'Let's Encrypt'</span></span>
- <span data-ttu-id="68cd4-263">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68cd4-263">'Razor'</span></span>
- <span data-ttu-id="68cd4-264">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="68cd4-264">'SignalR' uid:</span></span> 

-
<span data-ttu-id="68cd4-265">title:' ASP.NET Core Blazor gestione eventi ' Author: Description:' informazioni sulle Blazor funzionalità di gestione degli eventi, inclusi i tipi di argomenti dell'evento, i callback degli eventi e la gestione degli eventi predefiniti del browser.</span><span class="sxs-lookup"><span data-stu-id="68cd4-265">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="68cd4-266">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="68cd4-266">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68cd4-267">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68cd4-267">'Blazor'</span></span>
- <span data-ttu-id="68cd4-268">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68cd4-268">'Identity'</span></span>
- <span data-ttu-id="68cd4-269">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68cd4-269">'Let's Encrypt'</span></span>
- <span data-ttu-id="68cd4-270">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68cd4-270">'Razor'</span></span>
- <span data-ttu-id="68cd4-271">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="68cd4-271">'SignalR' uid:</span></span> 

-
<span data-ttu-id="68cd4-272">title:' ASP.NET Core Blazor gestione eventi ' Author: Description:' informazioni sulle Blazor funzionalità di gestione degli eventi, inclusi i tipi di argomenti dell'evento, i callback degli eventi e la gestione degli eventi predefiniti del browser.</span><span class="sxs-lookup"><span data-stu-id="68cd4-272">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="68cd4-273">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="68cd4-273">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68cd4-274">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68cd4-274">'Blazor'</span></span>
- <span data-ttu-id="68cd4-275">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68cd4-275">'Identity'</span></span>
- <span data-ttu-id="68cd4-276">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68cd4-276">'Let's Encrypt'</span></span>
- <span data-ttu-id="68cd4-277">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68cd4-277">'Razor'</span></span>
- <span data-ttu-id="68cd4-278">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="68cd4-278">'SignalR' uid:</span></span> 

<span data-ttu-id="68cd4-279">---------- | | Appunti | <xref:Microsoft.AspNetCore.Components.Web.ClipboardEventArgs> | `oncut`, `oncopy`, `onpaste` | | Trascina | <xref:Microsoft.AspNetCore.Components.Web.DragEventArgs> | `ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`,`ondragend`</span><span class="sxs-lookup"><span data-stu-id="68cd4-279">---------- | | Clipboard        | <xref:Microsoft.AspNetCore.Components.Web.ClipboardEventArgs> | `oncut`, `oncopy`, `onpaste` | | Drag             | <xref:Microsoft.AspNetCore.Components.Web.DragEventArgs> | `ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span></span><br><br><span data-ttu-id="68cd4-280"><xref:Microsoft.AspNetCore.Components.Web.DataTransfer>e <xref:Microsoft.AspNetCore.Components.Web.DataTransferItem> contengono dati di elementi trascinati.</span><span class="sxs-lookup"><span data-stu-id="68cd4-280"><xref:Microsoft.AspNetCore.Components.Web.DataTransfer> and <xref:Microsoft.AspNetCore.Components.Web.DataTransferItem> hold dragged item data.</span></span> <span data-ttu-id="68cd4-281">| | Errore | <xref:Microsoft.AspNetCore.Components.Web.ErrorEventArgs> | `onerror` | | Evento | <xref:System.EventArgs>  |  *Informazioni generali*</span><span class="sxs-lookup"><span data-stu-id="68cd4-281">| | Error            | <xref:Microsoft.AspNetCore.Components.Web.ErrorEventArgs> | `onerror` | | Event            | <xref:System.EventArgs> | *General*</span></span><br><span data-ttu-id="68cd4-282">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onended`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span><span class="sxs-lookup"><span data-stu-id="68cd4-282">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onended`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span></span><br><br><span data-ttu-id="68cd4-283">*Appunti*</span><span class="sxs-lookup"><span data-stu-id="68cd4-283">*Clipboard*</span></span><br><span data-ttu-id="68cd4-284">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span><span class="sxs-lookup"><span data-stu-id="68cd4-284">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span></span><br><br><span data-ttu-id="68cd4-285">*Input*</span><span class="sxs-lookup"><span data-stu-id="68cd4-285">*Input*</span></span><br><span data-ttu-id="68cd4-286">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit></span><span class="sxs-lookup"><span data-stu-id="68cd4-286">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit></span></span><br><br><span data-ttu-id="68cd4-287">*Supporti*</span><span class="sxs-lookup"><span data-stu-id="68cd4-287">*Media*</span></span><br><span data-ttu-id="68cd4-288">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`</span><span class="sxs-lookup"><span data-stu-id="68cd4-288">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`</span></span><br><br><span data-ttu-id="68cd4-289"><xref:Microsoft.AspNetCore.Components.Web.EventHandlers>include gli attributi per configurare i mapping tra i nomi di evento e i tipi di argomenti dell'evento.</span><span class="sxs-lookup"><span data-stu-id="68cd4-289"><xref:Microsoft.AspNetCore.Components.Web.EventHandlers> holds attributes to configure the mappings between event names and event argument types.</span></span> <span data-ttu-id="68cd4-290">| | Stato attivo | <xref:Microsoft.AspNetCore.Components.Web.FocusEventArgs> | `onfocus`, `onblur`, `onfocusin`,`onfocusout`</span><span class="sxs-lookup"><span data-stu-id="68cd4-290">| | Focus            | <xref:Microsoft.AspNetCore.Components.Web.FocusEventArgs> | `onfocus`, `onblur`, `onfocusin`, `onfocusout`</span></span><br><br><span data-ttu-id="68cd4-291">Non include il supporto per `relatedTarget` .</span><span class="sxs-lookup"><span data-stu-id="68cd4-291">Doesn't include support for `relatedTarget`.</span></span> <span data-ttu-id="68cd4-292">| | Input | <xref:Microsoft.AspNetCore.Components.ChangeEventArgs> | `onchange`, `oninput` | | Tastiera | <xref:Microsoft.AspNetCore.Components.Web.KeyboardEventArgs> | `onkeydown`, `onkeypress`, `onkeyup` | | Mouse | <xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs> | `onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout` | | Puntatore del mouse | <xref:Microsoft.AspNetCore.Components.Web.PointerEventArgs> | `onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture` | | Rotellina del mouse | <xref:Microsoft.AspNetCore.Components.Web.WheelEventArgs> | `onwheel`, `onmousewheel` | | Stato di avanzamento | <xref:Microsoft.AspNetCore.Components.Web.ProgressEventArgs> | `onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout` | | Tocco | <xref:Microsoft.AspNetCore.Components.Web.TouchEventArgs> | `ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`,`ontouchcancel`</span><span class="sxs-lookup"><span data-stu-id="68cd4-292">| | Input            | <xref:Microsoft.AspNetCore.Components.ChangeEventArgs> | `onchange`, `oninput` | | Keyboard         | <xref:Microsoft.AspNetCore.Components.Web.KeyboardEventArgs> | `onkeydown`, `onkeypress`, `onkeyup` | | Mouse            | <xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs> | `onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout` | | Mouse pointer    | <xref:Microsoft.AspNetCore.Components.Web.PointerEventArgs> | `onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture` | | Mouse wheel      | <xref:Microsoft.AspNetCore.Components.Web.WheelEventArgs> | `onwheel`, `onmousewheel` | | Progress         | <xref:Microsoft.AspNetCore.Components.Web.ProgressEventArgs> | `onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout` | | Touch            | <xref:Microsoft.AspNetCore.Components.Web.TouchEventArgs> | `ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span></span><br><br><span data-ttu-id="68cd4-293"><xref:Microsoft.AspNetCore.Components.Web.TouchPoint>rappresenta un singolo punto di contatto su un dispositivo sensibile al tocco.</span><span class="sxs-lookup"><span data-stu-id="68cd4-293"><xref:Microsoft.AspNetCore.Components.Web.TouchPoint> represents a single contact point on a touch-sensitive device.</span></span> |

<span data-ttu-id="68cd4-294">Per altre informazioni, vedere le seguenti risorse:</span><span class="sxs-lookup"><span data-stu-id="68cd4-294">For more information, see the following resources:</span></span>

* <span data-ttu-id="68cd4-295">[Classi EventArgs nell'origine riferimento ASP.NET Core (ramo DotNet/aspnetcore Release/3.1)](https://github.com/dotnet/aspnetcore/tree/release/3.1/src/Components/Web/src/Web).</span><span class="sxs-lookup"><span data-stu-id="68cd4-295">[EventArgs classes in the ASP.NET Core reference source (dotnet/aspnetcore release/3.1 branch)](https://github.com/dotnet/aspnetcore/tree/release/3.1/src/Components/Web/src/Web).</span></span>
* <span data-ttu-id="68cd4-296">[MDN Web docs: GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers): include informazioni su quali elementi HTML supportano ogni evento DOM.</span><span class="sxs-lookup"><span data-stu-id="68cd4-296">[MDN web docs: GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers): Includes information on which HTML elements support each DOM event.</span></span>

## <a name="lambda-expressions"></a><span data-ttu-id="68cd4-297">Espressioni lambda</span><span class="sxs-lookup"><span data-stu-id="68cd4-297">Lambda expressions</span></span>

<span data-ttu-id="68cd4-298">È inoltre possibile utilizzare le [espressioni lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions) :</span><span class="sxs-lookup"><span data-stu-id="68cd4-298">[Lambda expressions](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions) can also be used:</span></span>

```razor
<button @onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

<span data-ttu-id="68cd4-299">Spesso è consigliabile chiudere i valori aggiuntivi, ad esempio quando si esegue l'iterazione su un set di elementi.</span><span class="sxs-lookup"><span data-stu-id="68cd4-299">It's often convenient to close over additional values, such as when iterating over a set of elements.</span></span> <span data-ttu-id="68cd4-300">Nell'esempio seguente vengono creati tre pulsanti, ciascuno dei quali chiama il `UpdateHeading` passaggio di un argomento di evento ( <xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs> ) e il relativo numero di pulsante ( `buttonNumber` ) quando vengono selezionati nell'interfaccia utente:</span><span class="sxs-lookup"><span data-stu-id="68cd4-300">The following example creates three buttons, each of which calls `UpdateHeading` passing an event argument (<xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs>) and its button number (`buttonNumber`) when selected in the UI:</span></span>

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
> <span data-ttu-id="68cd4-301">**Non** usare la variabile di ciclo ( `i` ) in un `for` ciclo direttamente in un'espressione lambda.</span><span class="sxs-lookup"><span data-stu-id="68cd4-301">Do **not** use the loop variable (`i`) in a `for` loop directly in a lambda expression.</span></span> <span data-ttu-id="68cd4-302">In caso contrario, la stessa variabile viene utilizzata da tutte le espressioni lambda causando `i` che il valore di sia lo stesso in tutte le espressioni lambda.</span><span class="sxs-lookup"><span data-stu-id="68cd4-302">Otherwise the same variable is used by all lambda expressions causing `i`'s value to be the same in all lambdas.</span></span> <span data-ttu-id="68cd4-303">Acquisire sempre il relativo valore in una variabile locale ( `buttonNumber` nell'esempio precedente) e quindi usarlo.</span><span class="sxs-lookup"><span data-stu-id="68cd4-303">Always capture its value in a local variable (`buttonNumber` in the preceding example) and then use it.</span></span>

## <a name="eventcallback"></a><span data-ttu-id="68cd4-304">EventCallback</span><span class="sxs-lookup"><span data-stu-id="68cd4-304">EventCallback</span></span>

<span data-ttu-id="68cd4-305">Uno scenario comune con i componenti annidati è la volontà di eseguire il metodo di un componente padre quando si verifica un evento del componente figlio.</span><span class="sxs-lookup"><span data-stu-id="68cd4-305">A common scenario with nested components is the desire to run a parent component's method when a child component event occurs.</span></span> <span data-ttu-id="68cd4-306">Un `onclick` evento che si verifica nel componente figlio è un caso d'uso comune.</span><span class="sxs-lookup"><span data-stu-id="68cd4-306">An `onclick` event occurring in the child component is a common use case.</span></span> <span data-ttu-id="68cd4-307">Per esporre gli eventi tra i componenti, usare un oggetto <xref:Microsoft.AspNetCore.Components.EventCallback> .</span><span class="sxs-lookup"><span data-stu-id="68cd4-307">To expose events across components, use an <xref:Microsoft.AspNetCore.Components.EventCallback>.</span></span> <span data-ttu-id="68cd4-308">Un componente padre può assegnare un metodo di callback a un componente figlio <xref:Microsoft.AspNetCore.Components.EventCallback> .</span><span class="sxs-lookup"><span data-stu-id="68cd4-308">A parent component can assign a callback method to a child component's <xref:Microsoft.AspNetCore.Components.EventCallback>.</span></span>

<span data-ttu-id="68cd4-309">`ChildComponent`Nell'app di esempio (*Components/ChildComponent. Razor*) viene illustrato il modo in cui il gestore di un pulsante `onclick` viene configurato per ricevere un <xref:Microsoft.AspNetCore.Components.EventCallback> delegato dall'oggetto dell'esempio `ParentComponent` .</span><span class="sxs-lookup"><span data-stu-id="68cd4-309">The `ChildComponent` in the sample app (*Components/ChildComponent.razor*) demonstrates how a button's `onclick` handler is set up to receive an <xref:Microsoft.AspNetCore.Components.EventCallback> delegate from the sample's `ParentComponent`.</span></span> <span data-ttu-id="68cd4-310"><xref:Microsoft.AspNetCore.Components.EventCallback>Viene digitato con `MouseEventArgs` , appropriato per un `onclick` evento da un dispositivo periferico:</span><span class="sxs-lookup"><span data-stu-id="68cd4-310">The <xref:Microsoft.AspNetCore.Components.EventCallback> is typed with `MouseEventArgs`, which is appropriate for an `onclick` event from a peripheral device:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=5-7,17-18)]

<span data-ttu-id="68cd4-311">`ParentComponent`Imposta l'oggetto () del figlio sul <xref:Microsoft.AspNetCore.Components.EventCallback%601> `OnClickCallback` relativo `ShowMessage` metodo.</span><span class="sxs-lookup"><span data-stu-id="68cd4-311">The `ParentComponent` sets the child's <xref:Microsoft.AspNetCore.Components.EventCallback%601> (`OnClickCallback`) to its `ShowMessage` method.</span></span>

<span data-ttu-id="68cd4-312">*Pages/ParentComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="68cd4-312">*Pages/ParentComponent.razor*:</span></span>

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

<span data-ttu-id="68cd4-313">Quando il pulsante è selezionato in `ChildComponent` :</span><span class="sxs-lookup"><span data-stu-id="68cd4-313">When the button is selected in the `ChildComponent`:</span></span>

* <span data-ttu-id="68cd4-314">`ParentComponent` `ShowMessage` Viene chiamato il metodo di.</span><span class="sxs-lookup"><span data-stu-id="68cd4-314">The `ParentComponent`'s `ShowMessage` method is called.</span></span> <span data-ttu-id="68cd4-315">`messageText`viene aggiornato e visualizzato nell'oggetto `ParentComponent` .</span><span class="sxs-lookup"><span data-stu-id="68cd4-315">`messageText` is updated and displayed in the `ParentComponent`.</span></span>
* <span data-ttu-id="68cd4-316">Una chiamata a [StateHasChanged](xref:blazor/lifecycle#state-changes) non è obbligatoria nel metodo del callback ( `ShowMessage` ).</span><span class="sxs-lookup"><span data-stu-id="68cd4-316">A call to [StateHasChanged](xref:blazor/lifecycle#state-changes) isn't required in the callback's method (`ShowMessage`).</span></span> <span data-ttu-id="68cd4-317"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>viene chiamato automaticamente per eseguire il rendering di `ParentComponent` , proprio come gli eventi figlio attivano il rendering dei componenti nei gestori eventi che vengono eseguiti all'interno dell'elemento figlio.</span><span class="sxs-lookup"><span data-stu-id="68cd4-317"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called automatically to rerender the `ParentComponent`, just as child events trigger component rerendering in event handlers that execute within the child.</span></span>

<span data-ttu-id="68cd4-318"><xref:Microsoft.AspNetCore.Components.EventCallback>e <xref:Microsoft.AspNetCore.Components.EventCallback%601> consentono delegati asincroni.</span><span class="sxs-lookup"><span data-stu-id="68cd4-318"><xref:Microsoft.AspNetCore.Components.EventCallback> and <xref:Microsoft.AspNetCore.Components.EventCallback%601> permit asynchronous delegates.</span></span> <span data-ttu-id="68cd4-319"><xref:Microsoft.AspNetCore.Components.EventCallback%601>è fortemente tipizzato e richiede un tipo di argomento specifico.</span><span class="sxs-lookup"><span data-stu-id="68cd4-319"><xref:Microsoft.AspNetCore.Components.EventCallback%601> is strongly typed and requires a specific argument type.</span></span> <span data-ttu-id="68cd4-320"><xref:Microsoft.AspNetCore.Components.EventCallback>è debolmente tipizzato e consente qualsiasi tipo di argomento.</span><span class="sxs-lookup"><span data-stu-id="68cd4-320"><xref:Microsoft.AspNetCore.Components.EventCallback> is weakly typed and allows any argument type.</span></span>

```razor
<ChildComponent 
    OnClickCallback="@(async () => { await Task.Yield(); messageText = "Blaze It!"; })" />
```

<span data-ttu-id="68cd4-321">Richiamare <xref:Microsoft.AspNetCore.Components.EventCallback> o <xref:Microsoft.AspNetCore.Components.EventCallback%601> con <xref:Microsoft.AspNetCore.Components.EventCallback.InvokeAsync%2A> e attendere <xref:System.Threading.Tasks.Task> :</span><span class="sxs-lookup"><span data-stu-id="68cd4-321">Invoke an <xref:Microsoft.AspNetCore.Components.EventCallback> or <xref:Microsoft.AspNetCore.Components.EventCallback%601> with <xref:Microsoft.AspNetCore.Components.EventCallback.InvokeAsync%2A> and await the <xref:System.Threading.Tasks.Task>:</span></span>

```csharp
await callback.InvokeAsync(arg);
```

<span data-ttu-id="68cd4-322">Usare <xref:Microsoft.AspNetCore.Components.EventCallback> e <xref:Microsoft.AspNetCore.Components.EventCallback%601> per la gestione degli eventi e i parametri del componente di associazione.</span><span class="sxs-lookup"><span data-stu-id="68cd4-322">Use <xref:Microsoft.AspNetCore.Components.EventCallback> and <xref:Microsoft.AspNetCore.Components.EventCallback%601> for event handling and binding component parameters.</span></span>

<span data-ttu-id="68cd4-323">Preferisci l'oggetto fortemente tipizzato <xref:Microsoft.AspNetCore.Components.EventCallback%601> <xref:Microsoft.AspNetCore.Components.EventCallback> .</span><span class="sxs-lookup"><span data-stu-id="68cd4-323">Prefer the strongly typed <xref:Microsoft.AspNetCore.Components.EventCallback%601> over <xref:Microsoft.AspNetCore.Components.EventCallback>.</span></span> <span data-ttu-id="68cd4-324"><xref:Microsoft.AspNetCore.Components.EventCallback%601>fornisce un migliore feedback sugli errori agli utenti del componente.</span><span class="sxs-lookup"><span data-stu-id="68cd4-324"><xref:Microsoft.AspNetCore.Components.EventCallback%601> provides better error feedback to users of the component.</span></span> <span data-ttu-id="68cd4-325">Analogamente ad altri gestori di eventi dell'interfaccia utente, la specifica del parametro dell'evento è facoltativa.</span><span class="sxs-lookup"><span data-stu-id="68cd4-325">Similar to other UI event handlers, specifying the event parameter is optional.</span></span> <span data-ttu-id="68cd4-326">Usare <xref:Microsoft.AspNetCore.Components.EventCallback> quando non è stato passato alcun valore al callback.</span><span class="sxs-lookup"><span data-stu-id="68cd4-326">Use <xref:Microsoft.AspNetCore.Components.EventCallback> when there's no value passed to the callback.</span></span>

## <a name="prevent-default-actions"></a><span data-ttu-id="68cd4-327">Impedisci azioni predefinite</span><span class="sxs-lookup"><span data-stu-id="68cd4-327">Prevent default actions</span></span>

<span data-ttu-id="68cd4-328">Utilizzare l' [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) attributo Directive per impedire l'azione predefinita per un evento.</span><span class="sxs-lookup"><span data-stu-id="68cd4-328">Use the [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) directive attribute to prevent the default action for an event.</span></span>

<span data-ttu-id="68cd4-329">Quando si seleziona un tasto in un dispositivo di input e lo stato attivo dell'elemento si trova in una casella di testo, in un browser viene in genere visualizzato il carattere della chiave nella casella di testo.</span><span class="sxs-lookup"><span data-stu-id="68cd4-329">When a key is selected on an input device and the element focus is on a text box, a browser normally displays the key's character in the text box.</span></span> <span data-ttu-id="68cd4-330">Nell'esempio seguente viene impedito il comportamento predefinito specificando l' `@onkeypress:preventDefault` attributo della direttiva.</span><span class="sxs-lookup"><span data-stu-id="68cd4-330">In the following example, the default behavior is prevented by specifying the `@onkeypress:preventDefault` directive attribute.</span></span> <span data-ttu-id="68cd4-331">Il contatore viene incrementato e la **+** chiave non viene acquisita nel `<input>` valore dell'elemento:</span><span class="sxs-lookup"><span data-stu-id="68cd4-331">The counter increments, and the **+** key isn't captured into the `<input>` element's value:</span></span>

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

<span data-ttu-id="68cd4-332">La specifica dell' `@on{EVENT}:preventDefault` attributo senza un valore equivale a `@on{EVENT}:preventDefault="true"` .</span><span class="sxs-lookup"><span data-stu-id="68cd4-332">Specifying the `@on{EVENT}:preventDefault` attribute without a value is equivalent to `@on{EVENT}:preventDefault="true"`.</span></span>

<span data-ttu-id="68cd4-333">Il valore dell'attributo può anche essere un'espressione.</span><span class="sxs-lookup"><span data-stu-id="68cd4-333">The value of the attribute can also be an expression.</span></span> <span data-ttu-id="68cd4-334">Nell'esempio seguente `shouldPreventDefault` è un `bool` campo impostato su `true` o `false` :</span><span class="sxs-lookup"><span data-stu-id="68cd4-334">In the following example, `shouldPreventDefault` is a `bool` field set to either `true` or `false`:</span></span>

```razor
<input @onkeypress:preventDefault="shouldPreventDefault" />
```

<span data-ttu-id="68cd4-335">Per impedire l'azione predefinita, non è necessario un gestore eventi.</span><span class="sxs-lookup"><span data-stu-id="68cd4-335">An event handler isn't required to prevent the default action.</span></span> <span data-ttu-id="68cd4-336">Il gestore eventi e impedire scenari di azione predefiniti possono essere usati in modo indipendente.</span><span class="sxs-lookup"><span data-stu-id="68cd4-336">The event handler and prevent default action scenarios can be used independently.</span></span>

## <a name="stop-event-propagation"></a><span data-ttu-id="68cd4-337">Arresta propagazione eventi</span><span class="sxs-lookup"><span data-stu-id="68cd4-337">Stop event propagation</span></span>

<span data-ttu-id="68cd4-338">Utilizzare l' [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) attributo Directive per arrestare la propagazione degli eventi.</span><span class="sxs-lookup"><span data-stu-id="68cd4-338">Use the [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) directive attribute to stop event propagation.</span></span>

<span data-ttu-id="68cd4-339">Nell'esempio seguente la selezione della casella di controllo impedisce la propagazione degli eventi click dal secondo figlio `<div>` all'elemento padre `<div>` :</span><span class="sxs-lookup"><span data-stu-id="68cd4-339">In the following example, selecting the check box prevents click events from the second child `<div>` from propagating to the parent `<div>`:</span></span>

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
