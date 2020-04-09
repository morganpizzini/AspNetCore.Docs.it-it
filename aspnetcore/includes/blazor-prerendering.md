---
no-loc:
- Blazor
- SignalR
ms.openlocfilehash: 5f3e22e04fe18149ec5a8acb42f42a8ef83a7664
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "78659719"
---
<span data-ttu-id="10c7f-101">Mentre Blazor un'app Server esegue il prerendering, alcune azioni, ad esempio la chiamata in JavaScript, non sono possibili perché non è stata stabilita una connessione con il browser.</span><span class="sxs-lookup"><span data-stu-id="10c7f-101">While a Blazor Server app is prerendering, certain actions, such as calling into JavaScript, aren't possible because a connection with the browser hasn't been established.</span></span> <span data-ttu-id="10c7f-102">Potrebbe essere necessario eseguire il rendering dei componenti in modo diverso quando viene eseguito il prerendering.</span><span class="sxs-lookup"><span data-stu-id="10c7f-102">Components may need to render differently when prerendered.</span></span>

<span data-ttu-id="10c7f-103">Per ritardare le chiamate di interoperabilità JavaScript fino a quando non viene stabilita la connessione con il browser, è possibile utilizzare [l'evento del ciclo di vita del componente OnAfterRenderAsync](xref:blazor/lifecycle#after-component-render).</span><span class="sxs-lookup"><span data-stu-id="10c7f-103">To delay JavaScript interop calls until after the connection with the browser is established, you can use the [OnAfterRenderAsync component lifecycle event](xref:blazor/lifecycle#after-component-render).</span></span> <span data-ttu-id="10c7f-104">Questo evento viene chiamato solo dopo che l'app è stata completamente sottoposta a rendering e viene stabilita la connessione client.</span><span class="sxs-lookup"><span data-stu-id="10c7f-104">This event is only called after the app is fully rendered and the client connection is established.</span></span>

```cshtml
@using Microsoft.JSInterop
@inject IJSRuntime JSRuntime

<div @ref="divElement">Text during render</div>

@code {
    private ElementReference divElement;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            await JSRuntime.InvokeVoidAsync(
                "setElementText", divElement, "Text after render");
        }
    }
}
```

<span data-ttu-id="10c7f-105">Per il codice di esempio `setElementText` precedente, fornire `<head>` una funzioneBlazor JavaScript all'interno dell'elementoBlazor di *wwwroot/index.html* ( WebAssembly) o *Pages/_Host.cshtml* ( Server).</span><span class="sxs-lookup"><span data-stu-id="10c7f-105">For the preceding example code, provide a `setElementText` JavaScript function inside the `<head>` element of *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server).</span></span> <span data-ttu-id="10c7f-106">La funzione viene `IJSRuntime.InvokeVoidAsync` chiamata con e non restituisce un valore:The function is called with and doesn't return a value:</span><span class="sxs-lookup"><span data-stu-id="10c7f-106">The function is called with `IJSRuntime.InvokeVoidAsync` and doesn't return a value:</span></span>

```html
<script>
  window.setElementText = (element, text) => element.innerText = text;
</script>
```

> [!WARNING]
> <span data-ttu-id="10c7f-107">L'esempio precedente modifica direttamente il modello DOM (Document Object Model) solo a scopo dimostrativo.</span><span class="sxs-lookup"><span data-stu-id="10c7f-107">The preceding example modifies the Document Object Model (DOM) directly for demonstration purposes only.</span></span> <span data-ttu-id="10c7f-108">La modifica diretta del DOM con JavaScript non è consigliata Blazornella maggior parte degli scenari perché JavaScript può interferire con il rilevamento delle modifiche.</span><span class="sxs-lookup"><span data-stu-id="10c7f-108">Directly modifying the DOM with JavaScript isn't recommended in most scenarios because JavaScript can interfere with Blazor's change tracking.</span></span>

<span data-ttu-id="10c7f-109">Il componente seguente illustra come usare l'interoperabilità JavaScript come parte della logica di inizializzazione di un componente in modo compatibile con il prerendering.</span><span class="sxs-lookup"><span data-stu-id="10c7f-109">The following component demonstrates how to use JavaScript interop as part of a component's initialization logic in a way that's compatible with prerendering.</span></span> <span data-ttu-id="10c7f-110">Il componente mostra che è possibile attivare `OnAfterRenderAsync`un aggiornamento del rendering dall'interno di .</span><span class="sxs-lookup"><span data-stu-id="10c7f-110">The component shows that it's possible to trigger a rendering update from inside `OnAfterRenderAsync`.</span></span> <span data-ttu-id="10c7f-111">Lo sviluppatore deve evitare di creare un ciclo infinito in questo scenario.</span><span class="sxs-lookup"><span data-stu-id="10c7f-111">The developer must avoid creating an infinite loop in this scenario.</span></span>

<span data-ttu-id="10c7f-112">Dove `JSRuntime.InvokeAsync` viene `ElementRef` chiamato, viene `OnAfterRenderAsync` utilizzato solo in e non in qualsiasi metodo del ciclo di vita precedente perché non è presente alcun elemento JavaScript fino a dopo il rendering del componente.</span><span class="sxs-lookup"><span data-stu-id="10c7f-112">Where `JSRuntime.InvokeAsync` is called, `ElementRef` is only used in `OnAfterRenderAsync` and not in any earlier lifecycle method because there's no JavaScript element until after the component is rendered.</span></span>

<span data-ttu-id="10c7f-113">[StateHasChanged](xref:blazor/lifecycle#state-changes) viene chiamato per eseguire nuovamente il rendering del componente con il nuovo stato ottenuto dalla chiamata di interoperabilità JavaScript.</span><span class="sxs-lookup"><span data-stu-id="10c7f-113">[StateHasChanged](xref:blazor/lifecycle#state-changes) is called to rerender the component with the new state obtained from the JavaScript interop call.</span></span> <span data-ttu-id="10c7f-114">Il codice non crea un `StateHasChanged` ciclo infinito `infoFromJs` `null`perché viene chiamato solo quando è .</span><span class="sxs-lookup"><span data-stu-id="10c7f-114">The code doesn't create an infinite loop because `StateHasChanged` is only called when `infoFromJs` is `null`.</span></span>

```cshtml
@page "/prerendered-interop"
@using Microsoft.AspNetCore.Components
@using Microsoft.JSInterop
@inject IJSRuntime JSRuntime

<p>
    Get value via JS interop call:
    <strong id="val-get-by-interop">@(infoFromJs ?? "No value yet")</strong>
</p>

Set value via JS interop call:
<div id="val-set-by-interop" @ref="divElement"></div>

@code {
    private string infoFromJs;
    private ElementReference divElement;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender && infoFromJs == null)
        {
            infoFromJs = await JSRuntime.InvokeAsync<string>(
                "setElementText", divElement, "Hello from interop call!");

            StateHasChanged();
        }
    }
}
```

<span data-ttu-id="10c7f-115">Per il codice di esempio `setElementText` precedente, fornire `<head>` una funzioneBlazor JavaScript all'interno dell'elementoBlazor di *wwwroot/index.html* ( WebAssembly) o *Pages/_Host.cshtml* ( Server).</span><span class="sxs-lookup"><span data-stu-id="10c7f-115">For the preceding example code, provide a `setElementText` JavaScript function inside the `<head>` element of *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server).</span></span> <span data-ttu-id="10c7f-116">La funzione viene `IJSRuntime.InvokeAsync` chiamata con e restituisce un valore:The function is called with and returns a value:</span><span class="sxs-lookup"><span data-stu-id="10c7f-116">The function is called with `IJSRuntime.InvokeAsync` and returns a value:</span></span>

```html
<script>
  window.setElementText = (element, text) => {
    element.innerText = text;
    return text;
  };
</script>
```

> [!WARNING]
> <span data-ttu-id="10c7f-117">L'esempio precedente modifica direttamente il modello DOM (Document Object Model) solo a scopo dimostrativo.</span><span class="sxs-lookup"><span data-stu-id="10c7f-117">The preceding example modifies the Document Object Model (DOM) directly for demonstration purposes only.</span></span> <span data-ttu-id="10c7f-118">La modifica diretta del DOM con JavaScript non è consigliata Blazornella maggior parte degli scenari perché JavaScript può interferire con il rilevamento delle modifiche.</span><span class="sxs-lookup"><span data-stu-id="10c7f-118">Directly modifying the DOM with JavaScript isn't recommended in most scenarios because JavaScript can interfere with Blazor's change tracking.</span></span>
