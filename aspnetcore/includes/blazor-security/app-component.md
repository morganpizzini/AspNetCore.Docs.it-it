<span data-ttu-id="163de-101">Il `App` componente (*app. Razor*) è simile al `App` componente presente nelle app del server Blazer:</span><span class="sxs-lookup"><span data-stu-id="163de-101">The `App` component (*App.razor*) is similar to the `App` component found in Blazor Server apps:</span></span>

* <span data-ttu-id="163de-102">Il `CascadingAuthenticationState` componente gestisce l' `AuthenticationState` esposizione al resto dell'app.</span><span class="sxs-lookup"><span data-stu-id="163de-102">The `CascadingAuthenticationState` component manages exposing the `AuthenticationState` to the rest of the app.</span></span>
* <span data-ttu-id="163de-103">Il `AuthorizeRouteView` componente verifica che l'utente corrente sia autorizzato ad accedere a una pagina specificata o altrimenti esegua il `RedirectToLogin` rendering del componente.</span><span class="sxs-lookup"><span data-stu-id="163de-103">The `AuthorizeRouteView` component makes sure that the current user is authorized to access a given page or otherwise renders the `RedirectToLogin` component.</span></span>
* <span data-ttu-id="163de-104">Il `RedirectToLogin` componente gestisce il reindirizzamento degli utenti non autorizzati alla pagina di accesso.</span><span class="sxs-lookup"><span data-stu-id="163de-104">The `RedirectToLogin` component manages redirecting unauthorized users to the login page.</span></span>

```razor
<CascadingAuthenticationState>
    <Router AppAssembly="@typeof(Program).Assembly">
        <Found Context="routeData">
            <AuthorizeRouteView RouteData="@routeData" 
                DefaultLayout="@typeof(MainLayout)">
                <NotAuthorized>
                    @if (!context.User.Identity.IsAuthenticated)
                    {
                        <RedirectToLogin />
                    }
                    else
                    {
                        <p>
                            You are not authorized to access 
                            this resource.
                        </p>
                    }
                </NotAuthorized>
            </AuthorizeRouteView>
        </Found>
        <NotFound>
            <LayoutView Layout="@typeof(MainLayout)">
                <p>Sorry, there's nothing at this address.</p>
            </LayoutView>
        </NotFound>
    </Router>
</CascadingAuthenticationState>
```
