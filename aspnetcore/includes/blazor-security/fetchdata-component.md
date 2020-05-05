Il `FetchData` componente Mostra come:

* Effettuare il provisioning di un token di accesso.
* Usare il token di accesso per chiamare un'API di risorse protette nell'app *Server* .

La `@attribute [Authorize]` direttiva indica al sistema di autorizzazione webassembly di Blaze che l'utente deve essere autorizzato per visitare il componente. La presenza dell'attributo nell'app *client* non impedisce che l'API sul server venga chiamata senza credenziali appropriate. L'app *Server* deve anche usare `[Authorize]` sugli endpoint appropriati per la protezione corretta.

`IAccessTokenProvider.RequestAccessToken();`si occupa della richiesta di un token di accesso che può essere aggiunto alla richiesta per chiamare l'API. Se il token è memorizzato nella cache oppure il servizio è in grado di effettuare il provisioning di un nuovo token di accesso senza interazione dell'utente, la richiesta del token viene completata. In caso contrario, la richiesta del token `AccessTokenNotAvailableException` ha esito negativo e viene generato `try-catch` un errore, che viene rilevato in un'istruzione.

Per ottenere il token effettivo da includere nella richiesta, l'app deve verificare che la richiesta abbia avuto esito positivo chiamando `tokenResult.TryGetToken(out var token)`. 

Se la richiesta ha avuto esito positivo, la variabile del token viene popolata con il token di accesso. La `Value` proprietà del token espone la stringa letterale da includere nell'intestazione `Authorization` della richiesta.

Se la richiesta non è riuscita perché non è stato possibile eseguire il provisioning del token senza l'interazione dell'utente, il risultato del token contiene un URL di reindirizzamento. Se si passa a questo URL, l'utente viene indirizzato alla pagina di accesso e torna alla pagina corrente dopo un'autenticazione corretta.

```razor
@page "/fetchdata"
@using Microsoft.AspNetCore.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@using {APP NAMESPACE}.Shared
@attribute [Authorize]
@inject HttpClient Http

...

@code {
    private WeatherForecast[] forecasts;

    protected override async Task OnInitializedAsync()
    {
        try
        {
            forecasts = await Http.GetFromJsonAsync<WeatherForecast[]>("WeatherForecast");
        }
        catch (AccessTokenNotAvailableException exception)
        {
            exception.Redirect();
        }
    }
}
```
