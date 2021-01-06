<span data-ttu-id="a98b0-101">Aggiungere un oggetto <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions> per l' `User.Read` autorizzazione con <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions.DefaultAccessTokenScopes> :</span><span class="sxs-lookup"><span data-stu-id="a98b0-101">Add a <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions> for `User.Read` permission with <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions.DefaultAccessTokenScopes>:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes
        .Add("https://graph.microsoft.com/User.Read");
});
```
