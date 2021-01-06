Il portale di Azure fornisce uno dei seguenti formati URI ID app a seconda che il tenant di AAD disponga o meno di un [dominio di pubblicazione verificato o non verificato](/azure/active-directory/develop/howto-configure-publisher-domain):

* `api://{SERVER API APP CLIENT ID OR CUSTOM VALUE}`
* `https://{TENANT}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}`

Quando il secondo URI ID app precedente viene usato nell'app client per formare l'URI dell'ambito e l'autorizzazione non riesce, provare a specificare l'URI dell'ambito senza lo schema e l'host:

```csharp
options.ProviderOptions.DefaultAccessTokenScopes.Add(
    "{SERVER API APP CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
```

Esempio:

```csharp
options.ProviderOptions.DefaultAccessTokenScopes.Add(
    "41451fa7-82d9-4673-8fa5-69eff5a761fd/API.Access");
```
