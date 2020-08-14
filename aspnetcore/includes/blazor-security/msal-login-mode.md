<span data-ttu-id="cd5a0-101">Per impostazione predefinita, il Framework esegue il popup della modalità di accesso e viene eseguito il fallback per reindirizzare la modalità di accesso se non è possibile aprire un popup.</span><span class="sxs-lookup"><span data-stu-id="cd5a0-101">The framework defaults to pop-up login mode and falls back to redirect login mode if a pop-up can't be opened.</span></span> <span data-ttu-id="cd5a0-102">Configurare MSAL per l'uso della modalità di accesso Reindirizzamento impostando la `LoginMode` proprietà di <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions> su `redirect` :</span><span class="sxs-lookup"><span data-stu-id="cd5a0-102">Configure MSAL to use redirect login mode by setting the `LoginMode` property of <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions> to `redirect`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.LoginMode = "redirect";
});
```

<span data-ttu-id="cd5a0-103">L'impostazione predefinita è `popup` e il valore stringa non fa distinzione tra maiuscole e minuscole.</span><span class="sxs-lookup"><span data-stu-id="cd5a0-103">The default setting is `popup`, and the string value isn't case sensitive.</span></span>
