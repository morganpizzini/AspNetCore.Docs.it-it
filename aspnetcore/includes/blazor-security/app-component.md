Il `App` componente (*app. Razor*) è simile al `App` componente presente nelle app del server Blazer:

* Il `CascadingAuthenticationState` componente gestisce l' `AuthenticationState` esposizione al resto dell'app.
* Il `AuthorizeRouteView` componente verifica che l'utente corrente sia autorizzato ad accedere a una pagina specificata o altrimenti esegua il `RedirectToLogin` rendering del componente.
* Il `RedirectToLogin` componente gestisce il reindirizzamento degli utenti non autorizzati alla pagina di accesso.

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
