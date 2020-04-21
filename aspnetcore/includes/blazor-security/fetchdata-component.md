Il `FetchData` componente mostra come:

* Effettuare il provisioning di un token di accesso.
* Usare il token di accesso per chiamare un'API di risorse protetta nell'app *Server.Use* the access token to call a protected resource API in the Server app.

La `@attribute [Authorize]` direttiva indica al sistema di autorizzazione WebAssembly Blazor che l'utente deve essere autorizzato per visitare questo componente. La presenza dell'attributo nell'app *Client* non impedisce la chiamata dell'API sul server senza credenziali appropriate. L'app *Server* `[Authorize]` deve inoltre essere usata negli endpoint appropriati per proteggerli correttamente.

`AuthenticationService.RequestAccessToken();`si occupa di richiedere un token di accesso che può essere aggiunto alla richiesta per chiamare l'API. Se il token è memorizzato nella cache o il servizio è in grado di eseguire il provisioning di un nuovo token di accesso senza l'interazione dell'utente, la richiesta del token ha esito positivo. In caso contrario, la richiesta di token ha esito negativo.

Per ottenere il token effettivo da includere nella richiesta, l'app deve `tokenResult.TryGetToken(out var token)`verificare che la richiesta abbia avuto esito positivo chiamando . 

Se la richiesta ha avuto esito positivo, la variabile di token viene popolata con il token di accesso. La `Value` proprietà del token espone la stringa `Authorization` letterale da includere nell'intestazione della richiesta.

Se la richiesta non è riuscita perché non è stato possibile eseguire il provisioning del token senza l'interazione dell'utente, il risultato del token contiene un URL di reindirizzamento. La navigazione a questo URL porta l'utente alla pagina di accesso e torna alla pagina corrente dopo una corretta autenticazione.

```razor
@page "/fetchdata"
@using Microsoft.AspNetCore.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject IAccessTokenProvider AuthenticationService
@inject NavigationManager Navigation
@using {APPLICATION NAMESPACE}.Shared
@attribute [Authorize]

...

@code {
    private WeatherForecast[] forecasts;

    protected override async Task OnInitializedAsync()
    {
        var httpClient = new HttpClient();
        httpClient.BaseAddress = new Uri(Navigation.BaseUri);

        var tokenResult = await AuthenticationService.RequestAccessToken();

        if (tokenResult.TryGetToken(out var token))
        {
            httpClient.DefaultRequestHeaders.Add("Authorization", 
                $"Bearer {token.Value}");
            forecasts = await httpClient.GetFromJsonAsync<WeatherForecast[]>(
                "WeatherForecast");
        }
        else
        {
            Navigation.NavigateTo(tokenResult.RedirectUrl);
        }

    }
}
```

Per altre informazioni, vedere [Salvare lo stato dell'app prima di un'operazione di autenticazione.](xref:security/blazor/webassembly/additional-scenarios#save-app-state-before-an-authentication-operation)
