`RedirectToLogin`Componente ( `Shared/RedirectToLogin.razor` ):

* Gestisce il reindirizzamento degli utenti non autorizzati alla pagina di accesso.
* Conserva l'URL corrente a cui l'utente sta tentando di accedere, in modo che possano essere restituiti a tale pagina se l'autenticazione ha esito positivo.

```razor
@inject NavigationManager Navigation
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@code {
    protected override void OnInitialized()
    {
        Navigation.NavigateTo(
            $"authentication/login?returnUrl={Uri.EscapeDataString(Navigation.Uri)}");
    }
}
```
