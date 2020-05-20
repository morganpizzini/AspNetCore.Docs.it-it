> [!NOTE]
> Se il portale di Azure fornisce l'URI dell'ambito per l'app e l'app genera un'eccezione non gestita quando riceve una risposta *401 non autorizzata* dall'API, provare a usare un URI dell'ambito che non includa lo schema e l'host. Ad esempio, il portale di Azure può fornire uno dei seguenti formati di URI di ambito:
>
> * `https://{TENANT}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> Provare a specificare l'URI di ambito senza lo schema e l'host:
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```
