<span data-ttu-id="190e0-101">`RedirectToLogin`Componente ( `Shared/RedirectToLogin.razor` ):</span><span class="sxs-lookup"><span data-stu-id="190e0-101">The `RedirectToLogin` component (`Shared/RedirectToLogin.razor`):</span></span>

* <span data-ttu-id="190e0-102">Gestisce il reindirizzamento degli utenti non autorizzati alla pagina di accesso.</span><span class="sxs-lookup"><span data-stu-id="190e0-102">Manages redirecting unauthorized users to the login page.</span></span>
* <span data-ttu-id="190e0-103">Conserva l'URL corrente a cui l'utente sta tentando di accedere, in modo che possano essere restituiti a tale pagina se l'autenticazione ha esito positivo.</span><span class="sxs-lookup"><span data-stu-id="190e0-103">Preserves the current URL that the user is attempting to access so that they can be returned to that page if authentication is successful.</span></span>

```razor
@inject NavigationManager Navigation
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@code {
    protected override void OnInitialized()
    {
        Navigation.NavigateTo($"authentication/login?returnUrl=" +
            Uri.EscapeDataString(Navigation.Uri));
    }
}
```
