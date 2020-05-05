---
---
Mentre un'app del server blazer è prerendering, alcune azioni, ad esempio la chiamata a JavaScript, non sono possibili perché non è stata stabilita una connessione con il browser. I componenti potrebbero dover eseguire il rendering in modo diverso quando ne viene eseguito il rendering.

Per ritardare le chiamate di interoperabilità JavaScript finché non viene stabilita la connessione con il browser, è possibile usare l'evento del ciclo di vita del [componente OnAfterRenderAsync](xref:blazor/lifecycle#after-component-render). Questo evento viene chiamato solo dopo che viene eseguito il rendering completo dell'app e viene stabilita la connessione client.

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

Per il codice di esempio precedente, fornire `setElementText` una funzione JavaScript all' `<head>` interno dell'elemento di *wwwroot/index.html* (Blazer webassembly) o *pages/_Host. cshtml* (server Blaze). La funzione viene chiamata con `IJSRuntime.InvokeVoidAsync` e non restituisce un valore:

```html
<script>
  window.setElementText = (element, text) => element.innerText = text;
</script>
```

> [!WARNING]
> Nell'esempio precedente la Document Object Model (DOM) viene modificata direttamente a scopo dimostrativo. La modifica diretta del DOM con JavaScript non è consigliata nella maggior parte degli scenari perché JavaScript può interferire con il rilevamento delle modifiche di Blazer.

Nel componente seguente viene illustrato come utilizzare l'interoperabilità JavaScript come parte della logica di inizializzazione di un componente in modo che sia compatibile con il prerendering. Il componente indica che è possibile attivare un aggiornamento di rendering dall'interno `OnAfterRenderAsync`di. Lo sviluppatore deve evitare di creare un ciclo infinito in questo scenario.

Dove `JSRuntime.InvokeAsync` viene chiamato, `ElementRef` viene usato solo in `OnAfterRenderAsync` e non in nessun metodo del ciclo di vita precedente perché non è presente alcun elemento JavaScript finché non viene eseguito il rendering del componente.

[StateHasChanged](xref:blazor/lifecycle#state-changes) viene chiamato per eseguire nuovamente il rendering del componente con il nuovo stato ottenuto dalla chiamata di interoperabilità JavaScript. Il codice non crea un ciclo infinito perché `StateHasChanged` viene chiamato solo quando `infoFromJs` è `null`.

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

Per il codice di esempio precedente, fornire `setElementText` una funzione JavaScript all' `<head>` interno dell'elemento di *wwwroot/index.html* (Blazer webassembly) o *pages/_Host. cshtml* (server Blaze). La funzione viene chiamata con `IJSRuntime.InvokeAsync` e restituisce un valore:

```html
<script>
  window.setElementText = (element, text) => {
    element.innerText = text;
    return text;
  };
</script>
```

> [!WARNING]
> Nell'esempio precedente la Document Object Model (DOM) viene modificata direttamente a scopo dimostrativo. La modifica diretta del DOM con JavaScript non è consigliata nella maggior parte degli scenari perché JavaScript può interferire con il rilevamento delle modifiche di Blazer.
