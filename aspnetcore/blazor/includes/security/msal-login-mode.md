Per impostazione predefinita, il Framework esegue il popup della modalità di accesso e viene eseguito il fallback per reindirizzare la modalità di accesso se non è possibile aprire un popup. Configurare MSAL per l'uso della modalità di accesso Reindirizzamento impostando la `LoginMode` proprietà di <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions> su `redirect` :

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.LoginMode = "redirect";
});
```

L'impostazione predefinita è `popup` e il valore stringa non fa distinzione tra maiuscole e minuscole.
