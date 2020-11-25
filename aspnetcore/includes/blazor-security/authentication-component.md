<span data-ttu-id="ab1e4-101">La pagina prodotta dal `Authentication` componente ( `Pages/Authentication.razor` ) definisce le route necessarie per la gestione di diverse fasi di autenticazione.</span><span class="sxs-lookup"><span data-stu-id="ab1e4-101">The page produced by the `Authentication` component (`Pages/Authentication.razor`) defines the routes required for handling different authentication stages.</span></span>

<span data-ttu-id="ab1e4-102">Il <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> componente:</span><span class="sxs-lookup"><span data-stu-id="ab1e4-102">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> component:</span></span>

* <span data-ttu-id="ab1e4-103">Viene fornito dal [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) pacchetto.</span><span class="sxs-lookup"><span data-stu-id="ab1e4-103">Is provided by the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package.</span></span>
* <span data-ttu-id="ab1e4-104">Gestisce l'esecuzione delle azioni appropriate in ogni fase dell'autenticazione.</span><span class="sxs-lookup"><span data-stu-id="ab1e4-104">Manages performing the appropriate actions at each stage of authentication.</span></span>

```razor
@page "/authentication/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code {
    [Parameter]
    public string Action { get; set; }
}
```
