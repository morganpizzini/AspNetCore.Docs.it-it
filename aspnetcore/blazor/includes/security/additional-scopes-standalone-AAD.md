Aggiungere un oggetto <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions> per l' `User.Read` autorizzazione con <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions.DefaultAccessTokenScopes> :

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes
        .Add("https://graph.microsoft.com/User.Read");
});
```
