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
Mentre Blazor un'app Server esegue il prerendering, alcune azioni, ad esempio la chiamata in JavaScript, non sono possibili perché non è stata stabilita una connessione con il browser. Potrebbe essere necessario eseguire il rendering dei componenti in modo diverso quando viene eseguito il prerendering.

Per ritardare le chiamate di interoperabilità JavaScript fino a quando non viene stabilita la connessione con il browser, è possibile utilizzare [l'evento del ciclo di vita del componente OnAfterRenderAsync](xref:blazor/lifecycle#after-component-render). Questo evento viene chiamato solo dopo che l'app è stata completamente sottoposta a rendering e viene stabilita la connessione client.

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

Per il codice di esempio `setElementText` precedente, fornire `<head>` una funzioneBlazor JavaScript all'interno dell'elementoBlazor di *wwwroot/index.html* ( WebAssembly) o *Pages/_Host.cshtml* ( Server). La funzione viene `IJSRuntime.InvokeVoidAsync` chiamata con e non restituisce un valore:The function is called with and doesn't return a value:

```html
<script>
  window.setElementText = (element, text) => element.innerText = text;
</script>
```

> [!WARNING]
> L'esempio precedente modifica direttamente il modello DOM (Document Object Model) solo a scopo dimostrativo. La modifica diretta del DOM con JavaScript non è consigliata Blazornella maggior parte degli scenari perché JavaScript può interferire con il rilevamento delle modifiche.

Il componente seguente illustra come usare l'interoperabilità JavaScript come parte della logica di inizializzazione di un componente in modo compatibile con il prerendering. Il componente mostra che è possibile attivare `OnAfterRenderAsync`un aggiornamento del rendering dall'interno di . Lo sviluppatore deve evitare di creare un ciclo infinito in questo scenario.

Dove `JSRuntime.InvokeAsync` viene `ElementRef` chiamato, viene `OnAfterRenderAsync` utilizzato solo in e non in qualsiasi metodo del ciclo di vita precedente perché non è presente alcun elemento JavaScript fino a dopo il rendering del componente.

[StateHasChanged](xref:blazor/lifecycle#state-changes) viene chiamato per eseguire nuovamente il rendering del componente con il nuovo stato ottenuto dalla chiamata di interoperabilità JavaScript. Il codice non crea un `StateHasChanged` ciclo infinito `infoFromJs` `null`perché viene chiamato solo quando è .

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

Per il codice di esempio `setElementText` precedente, fornire `<head>` una funzioneBlazor JavaScript all'interno dell'elementoBlazor di *wwwroot/index.html* ( WebAssembly) o *Pages/_Host.cshtml* ( Server). La funzione viene `IJSRuntime.InvokeAsync` chiamata con e restituisce un valore:The function is called with and returns a value:

```html
<script>
  window.setElementText = (element, text) => {
    element.innerText = text;
    return text;
  };
</script>
```

> [!WARNING]
> L'esempio precedente modifica direttamente il modello DOM (Document Object Model) solo a scopo dimostrativo. La modifica diretta del DOM con JavaScript non è consigliata Blazornella maggior parte degli scenari perché JavaScript può interferire con il rilevamento delle modifiche.
