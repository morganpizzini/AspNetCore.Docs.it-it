<span data-ttu-id="47329-101">Il `FetchData` componente Mostra come:</span><span class="sxs-lookup"><span data-stu-id="47329-101">The `FetchData` component shows how to:</span></span>

* <span data-ttu-id="47329-102">Effettuare il provisioning di un token di accesso.</span><span class="sxs-lookup"><span data-stu-id="47329-102">Provision an access token.</span></span>
* <span data-ttu-id="47329-103">Usare il token di accesso per chiamare un'API di risorse protette nell'app *Server* .</span><span class="sxs-lookup"><span data-stu-id="47329-103">Use the access token to call a protected resource API in the *Server* app.</span></span>

<span data-ttu-id="47329-104">La [`@attribute [Authorize]`](xref:mvc/views/razor#attribute) direttiva indica al sistema di autorizzazione Webassembly di Blaze che l'utente deve essere autorizzato per visitare il componente.</span><span class="sxs-lookup"><span data-stu-id="47329-104">The [`@attribute [Authorize]`](xref:mvc/views/razor#attribute) directive indicates to the Blazor WebAssembly authorization system that the user must be authorized in order to visit this component.</span></span> <span data-ttu-id="47329-105">La presenza dell'attributo nell'app *client* non impedisce che l'API sul server venga chiamata senza credenziali appropriate.</span><span class="sxs-lookup"><span data-stu-id="47329-105">The presence of the attribute in the *Client* app doesn't prevent the API on the server from being called without proper credentials.</span></span> <span data-ttu-id="47329-106">L'app *Server* deve anche usare `[Authorize]` sugli endpoint appropriati per la protezione corretta.</span><span class="sxs-lookup"><span data-stu-id="47329-106">The *Server* app also must use `[Authorize]` on the appropriate endpoints to correctly protect them.</span></span>

<span data-ttu-id="47329-107"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.IAccessTokenProvider.RequestAccessToken%2A?displayProperty=nameWithType>si occupa della richiesta di un token di accesso che può essere aggiunto alla richiesta per chiamare l'API.</span><span class="sxs-lookup"><span data-stu-id="47329-107"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.IAccessTokenProvider.RequestAccessToken%2A?displayProperty=nameWithType> takes care of requesting an access token that can be added to the request to call the API.</span></span> <span data-ttu-id="47329-108">Se il token è memorizzato nella cache oppure il servizio è in grado di effettuare il provisioning di un nuovo token di accesso senza interazione dell'utente, la richiesta del token viene completata.</span><span class="sxs-lookup"><span data-stu-id="47329-108">If the token is cached or the service is able to provision a new access token without user interaction, the token request succeeds.</span></span> <span data-ttu-id="47329-109">In caso contrario, la richiesta di token ha esito negativo con un oggetto <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> , che viene rilevato in un'istruzione [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) .</span><span class="sxs-lookup"><span data-stu-id="47329-109">Otherwise, the token request fails with an <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException>, which is caught in a [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statement.</span></span>

<span data-ttu-id="47329-110">Per ottenere il token effettivo da includere nella richiesta, l'app deve verificare che la richiesta abbia avuto esito positivo chiamando [tokenResult. TryGetToken (out var token)](xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenResult.TryGetToken%2A).</span><span class="sxs-lookup"><span data-stu-id="47329-110">In order to obtain the actual token to include in the request, the app must check that the request succeeded by calling [tokenResult.TryGetToken(out var token)](xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenResult.TryGetToken%2A).</span></span>

<span data-ttu-id="47329-111">Se la richiesta ha avuto esito positivo, la variabile del token viene popolata con il token di accesso.</span><span class="sxs-lookup"><span data-stu-id="47329-111">If the request was successful, the token variable is populated with the access token.</span></span> <span data-ttu-id="47329-112">La <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessToken.Value?displayProperty=nameWithType> proprietà del token espone la stringa letterale da includere nell' `Authorization` intestazione della richiesta.</span><span class="sxs-lookup"><span data-stu-id="47329-112">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessToken.Value?displayProperty=nameWithType> property of the token exposes the literal string to include in the `Authorization` request header.</span></span>

<span data-ttu-id="47329-113">Se la richiesta non è riuscita perché non è stato possibile eseguire il provisioning del token senza l'interazione dell'utente, il risultato del token contiene un URL di reindirizzamento.</span><span class="sxs-lookup"><span data-stu-id="47329-113">If the request failed because the token couldn't be provisioned without user interaction, the token result contains a redirect URL.</span></span> <span data-ttu-id="47329-114">Se si passa a questo URL, l'utente viene indirizzato alla pagina di accesso e torna alla pagina corrente dopo un'autenticazione corretta.</span><span class="sxs-lookup"><span data-stu-id="47329-114">Navigating to this URL takes the user to the login page and back to the current page after a successful authentication.</span></span>

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
