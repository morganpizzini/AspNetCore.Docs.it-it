<span data-ttu-id="47ab6-101">Il `FetchData` componente Mostra come:</span><span class="sxs-lookup"><span data-stu-id="47ab6-101">The `FetchData` component shows how to:</span></span>

* <span data-ttu-id="47ab6-102">Effettuare il provisioning di un token di accesso.</span><span class="sxs-lookup"><span data-stu-id="47ab6-102">Provision an access token.</span></span>
* <span data-ttu-id="47ab6-103">Usare il token di accesso per chiamare un'API di risorse protette nell'app *Server* .</span><span class="sxs-lookup"><span data-stu-id="47ab6-103">Use the access token to call a protected resource API in the *Server* app.</span></span>

<span data-ttu-id="47ab6-104">La `@attribute [Authorize]` direttiva indica al sistema di autorizzazione webassembly di Blaze che l'utente deve essere autorizzato per visitare il componente.</span><span class="sxs-lookup"><span data-stu-id="47ab6-104">The `@attribute [Authorize]` directive indicates to the Blazor WebAssembly authorization system that the user must be authorized in order to visit this component.</span></span> <span data-ttu-id="47ab6-105">La presenza dell'attributo nell'app *client* non impedisce che l'API sul server venga chiamata senza credenziali appropriate.</span><span class="sxs-lookup"><span data-stu-id="47ab6-105">The presence of the attribute in the *Client* app doesn't prevent the API on the server from being called without proper credentials.</span></span> <span data-ttu-id="47ab6-106">L'app *Server* deve anche usare `[Authorize]` sugli endpoint appropriati per la protezione corretta.</span><span class="sxs-lookup"><span data-stu-id="47ab6-106">The *Server* app also must use `[Authorize]` on the appropriate endpoints to correctly protect them.</span></span>

<span data-ttu-id="47ab6-107">`AuthenticationService.RequestAccessToken();`si occupa della richiesta di un token di accesso che può essere aggiunto alla richiesta per chiamare l'API.</span><span class="sxs-lookup"><span data-stu-id="47ab6-107">`AuthenticationService.RequestAccessToken();` takes care of requesting an access token that can be added to the request to call the API.</span></span> <span data-ttu-id="47ab6-108">Se il token è memorizzato nella cache oppure il servizio è in grado di effettuare il provisioning di un nuovo token di accesso senza interazione dell'utente, la richiesta del token viene completata.</span><span class="sxs-lookup"><span data-stu-id="47ab6-108">If the token is cached or the service is able to provision a new access token without user interaction, the token request succeeds.</span></span> <span data-ttu-id="47ab6-109">In caso contrario, la richiesta del token non riesce.</span><span class="sxs-lookup"><span data-stu-id="47ab6-109">Otherwise, the token request fails.</span></span>

<span data-ttu-id="47ab6-110">Per ottenere il token effettivo da includere nella richiesta, l'app deve verificare che la richiesta abbia avuto esito positivo chiamando `tokenResult.TryGetToken(out var token)`.</span><span class="sxs-lookup"><span data-stu-id="47ab6-110">In order to obtain the actual token to include in the request, the app must check that the request succeeded by calling `tokenResult.TryGetToken(out var token)`.</span></span> 

<span data-ttu-id="47ab6-111">Se la richiesta ha avuto esito positivo, la variabile del token viene popolata con il token di accesso.</span><span class="sxs-lookup"><span data-stu-id="47ab6-111">If the request was successful, the token variable is populated with the access token.</span></span> <span data-ttu-id="47ab6-112">La `Value` proprietà del token espone la stringa letterale da includere nell'intestazione `Authorization` della richiesta.</span><span class="sxs-lookup"><span data-stu-id="47ab6-112">The `Value` property of the token exposes the literal string to include in the `Authorization` request header.</span></span>

<span data-ttu-id="47ab6-113">Se la richiesta non è riuscita perché non è stato possibile eseguire il provisioning del token senza l'interazione dell'utente, il risultato del token contiene un URL di reindirizzamento.</span><span class="sxs-lookup"><span data-stu-id="47ab6-113">If the request failed because the token couldn't be provisioned without user interaction, the token result contains a redirect URL.</span></span> <span data-ttu-id="47ab6-114">Se si passa a questo URL, l'utente viene indirizzato alla pagina di accesso e torna alla pagina corrente dopo un'autenticazione corretta.</span><span class="sxs-lookup"><span data-stu-id="47ab6-114">Navigating to this URL takes the user to the login page and back to the current page after a successful authentication.</span></span>

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
