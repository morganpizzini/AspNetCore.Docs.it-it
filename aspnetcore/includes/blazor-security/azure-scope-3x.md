<span data-ttu-id="71e41-101">Il portale di Azure fornisce uno dei seguenti formati URI ID app a seconda che il tenant di AAD disponga o meno di un [dominio di pubblicazione verificato o non verificato](/azure/active-directory/develop/howto-configure-publisher-domain):</span><span class="sxs-lookup"><span data-stu-id="71e41-101">The Azure portal provides one of the following App ID URI formats based on whether or not the AAD tenant has a [verified or unverified publisher domain](/azure/active-directory/develop/howto-configure-publisher-domain):</span></span>

* `api://{SERVER API APP CLIENT ID OR CUSTOM VALUE}`
* `https://{TENANT}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}`

<span data-ttu-id="71e41-102">Quando il secondo URI ID app precedente viene usato nell'app client per formare l'URI dell'ambito e l'autorizzazione non riesce, provare a specificare l'URI dell'ambito senza lo schema e l'host:</span><span class="sxs-lookup"><span data-stu-id="71e41-102">When the latter of the two preceding App ID URIs is used in the client app to form the scope URI and authorization isn't successful, try supplying the scope URI without the scheme and host:</span></span>

```csharp
options.ProviderOptions.DefaultAccessTokenScopes.Add(
    "{SERVER API APP CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
```

<span data-ttu-id="71e41-103">Esempio:</span><span class="sxs-lookup"><span data-stu-id="71e41-103">Example:</span></span>

```csharp
options.ProviderOptions.DefaultAccessTokenScopes.Add(
    "41451fa7-82d9-4673-8fa5-69eff5a761fd/API.Access");
```
