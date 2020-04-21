<span data-ttu-id="3cc07-101">Il `FetchData` componente mostra come:</span><span class="sxs-lookup"><span data-stu-id="3cc07-101">The `FetchData` component shows how to:</span></span>

* <span data-ttu-id="3cc07-102">Effettuare il provisioning di un token di accesso.</span><span class="sxs-lookup"><span data-stu-id="3cc07-102">Provision an access token.</span></span>
* <span data-ttu-id="3cc07-103">Usare il token di accesso per chiamare un'API di risorse protetta nell'app *Server.Use* the access token to call a protected resource API in the Server app.</span><span class="sxs-lookup"><span data-stu-id="3cc07-103">Use the access token to call a protected resource API in the *Server* app.</span></span>

<span data-ttu-id="3cc07-104">La `@attribute [Authorize]` direttiva indica al sistema di autorizzazione WebAssembly Blazor che l'utente deve essere autorizzato per visitare questo componente.</span><span class="sxs-lookup"><span data-stu-id="3cc07-104">The `@attribute [Authorize]` directive indicates to the Blazor WebAssembly authorization system that the user must be authorized in order to visit this component.</span></span> <span data-ttu-id="3cc07-105">La presenza dell'attributo nell'app *Client* non impedisce la chiamata dell'API sul server senza credenziali appropriate.</span><span class="sxs-lookup"><span data-stu-id="3cc07-105">The presence of the attribute in the *Client* app doesn't prevent the API on the server from being called without proper credentials.</span></span> <span data-ttu-id="3cc07-106">L'app *Server* `[Authorize]` deve inoltre essere usata negli endpoint appropriati per proteggerli correttamente.</span><span class="sxs-lookup"><span data-stu-id="3cc07-106">The *Server* app also must use `[Authorize]` on the appropriate endpoints to correctly protect them.</span></span>

<span data-ttu-id="3cc07-107">`AuthenticationService.RequestAccessToken();`si occupa di richiedere un token di accesso che può essere aggiunto alla richiesta per chiamare l'API.</span><span class="sxs-lookup"><span data-stu-id="3cc07-107">`AuthenticationService.RequestAccessToken();` takes care of requesting an access token that can be added to the request to call the API.</span></span> <span data-ttu-id="3cc07-108">Se il token è memorizzato nella cache o il servizio è in grado di eseguire il provisioning di un nuovo token di accesso senza l'interazione dell'utente, la richiesta del token ha esito positivo.</span><span class="sxs-lookup"><span data-stu-id="3cc07-108">If the token is cached or the service is able to provision a new access token without user interaction, the token request succeeds.</span></span> <span data-ttu-id="3cc07-109">In caso contrario, la richiesta di token ha esito negativo.</span><span class="sxs-lookup"><span data-stu-id="3cc07-109">Otherwise, the token request fails.</span></span>

<span data-ttu-id="3cc07-110">Per ottenere il token effettivo da includere nella richiesta, l'app deve `tokenResult.TryGetToken(out var token)`verificare che la richiesta abbia avuto esito positivo chiamando .</span><span class="sxs-lookup"><span data-stu-id="3cc07-110">In order to obtain the actual token to include in the request, the app must check that the request succeeded by calling `tokenResult.TryGetToken(out var token)`.</span></span> 

<span data-ttu-id="3cc07-111">Se la richiesta ha avuto esito positivo, la variabile di token viene popolata con il token di accesso.</span><span class="sxs-lookup"><span data-stu-id="3cc07-111">If the request was successful, the token variable is populated with the access token.</span></span> <span data-ttu-id="3cc07-112">La `Value` proprietà del token espone la stringa `Authorization` letterale da includere nell'intestazione della richiesta.</span><span class="sxs-lookup"><span data-stu-id="3cc07-112">The `Value` property of the token exposes the literal string to include in the `Authorization` request header.</span></span>

<span data-ttu-id="3cc07-113">Se la richiesta non è riuscita perché non è stato possibile eseguire il provisioning del token senza l'interazione dell'utente, il risultato del token contiene un URL di reindirizzamento.</span><span class="sxs-lookup"><span data-stu-id="3cc07-113">If the request failed because the token couldn't be provisioned without user interaction, the token result contains a redirect URL.</span></span> <span data-ttu-id="3cc07-114">La navigazione a questo URL porta l'utente alla pagina di accesso e torna alla pagina corrente dopo una corretta autenticazione.</span><span class="sxs-lookup"><span data-stu-id="3cc07-114">Navigating to this URL takes the user to the login page and back to the current page after a successful authentication.</span></span>

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

<span data-ttu-id="3cc07-115">Per altre informazioni, vedere [Salvare lo stato dell'app prima di un'operazione di autenticazione.](xref:security/blazor/webassembly/additional-scenarios#save-app-state-before-an-authentication-operation)</span><span class="sxs-lookup"><span data-stu-id="3cc07-115">For more information, see [Save app state before an authentication operation](xref:security/blazor/webassembly/additional-scenarios#save-app-state-before-an-authentication-operation).</span></span>
