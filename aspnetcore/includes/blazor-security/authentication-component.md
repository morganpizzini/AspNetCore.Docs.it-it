La pagina prodotta dal `Authentication` componente ( `Pages/Authentication.razor` ) definisce le route necessarie per la gestione di diverse fasi di autenticazione.

Il <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> componente:

* Viene fornito dal [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) pacchetto.
* Gestisce l'esecuzione delle azioni appropriate in ogni fase dell'autenticazione.

```razor
@page "/authentication/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code {
    [Parameter]
    public string Action { get; set; }
}
```
