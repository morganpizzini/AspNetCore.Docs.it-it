Il `FetchData` componente Mostra come:

* Effettuare il provisioning di un token di accesso.
* Usare il token di accesso per chiamare un'API di risorse protette nell'app *Server* .

La [`@attribute [Authorize]`](xref:mvc/views/razor#attribute) direttiva indica al sistema di autorizzazione Webassembly di Blaze che l'utente deve essere autorizzato per visitare il componente. La presenza dell'attributo nell' `Client` app non impedisce che l'API sul server venga chiamata senza credenziali appropriate. `Server`Per una corretta protezione, l'app deve usare anche negli `[Authorize]` endpoint appropriati.

<xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.IAccessTokenProvider.RequestAccessToken%2A?displayProperty=nameWithType>si occupa della richiesta di un token di accesso che può essere aggiunto alla richiesta per chiamare l'API. Se il token è memorizzato nella cache oppure il servizio è in grado di effettuare il provisioning di un nuovo token di accesso senza interazione dell'utente, la richiesta del token viene completata. In caso contrario, la richiesta di token ha esito negativo con un oggetto <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> , che viene rilevato in un' [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) istruzione.

Per ottenere il token effettivo da includere nella richiesta, l'app deve verificare che la richiesta abbia avuto esito positivo chiamando [`tokenResult.TryGetToken(out var token)`](xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenResult.TryGetToken%2A) .

Se la richiesta ha avuto esito positivo, la variabile del token viene popolata con il token di accesso. La <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessToken.Value?displayProperty=nameWithType> proprietà del token espone la stringa letterale da includere nell' `Authorization` intestazione della richiesta.

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
