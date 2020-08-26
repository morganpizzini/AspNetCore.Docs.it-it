La `wwwroot/index.html` pagina di indice () include uno script che definisce `AuthenticationService` in JavaScript. `AuthenticationService` gestisce i dettagli di basso livello del protocollo OIDC. L'app chiama internamente i metodi definiti nello script per eseguire le operazioni di autenticazione.

```html
<script src="_content/Microsoft.AspNetCore.Components.WebAssembly.Authentication/
    AuthenticationService.js"></script>
```
