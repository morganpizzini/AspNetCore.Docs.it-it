<span data-ttu-id="57c30-101">La `wwwroot/index.html` pagina di indice () include uno script che definisce `AuthenticationService` in JavaScript.</span><span class="sxs-lookup"><span data-stu-id="57c30-101">The Index page (`wwwroot/index.html`) page includes a script that defines the `AuthenticationService` in JavaScript.</span></span> <span data-ttu-id="57c30-102">`AuthenticationService`gestisce i dettagli di basso livello del protocollo OIDC.</span><span class="sxs-lookup"><span data-stu-id="57c30-102">`AuthenticationService` handles the low-level details of the the OIDC protocol.</span></span> <span data-ttu-id="57c30-103">L'app chiama internamente i metodi definiti nello script per eseguire le operazioni di autenticazione.</span><span class="sxs-lookup"><span data-stu-id="57c30-103">The app internally calls methods defined in the script to perform the authentication operations.</span></span>

```html
<script src="_content/Microsoft.AspNetCore.Components.WebAssembly.Authentication/
    AuthenticationService.js"></script>
```
