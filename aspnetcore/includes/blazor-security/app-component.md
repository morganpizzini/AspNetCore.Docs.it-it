Il `App` componente ( `App.razor` ) è simile al `App` componente presente nelle app del server Blazer:

* Il <xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState> componente gestisce l'esposizione al <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState> resto dell'app.
* Il <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView> componente verifica che l'utente corrente sia autorizzato ad accedere a una pagina specificata o altrimenti esegua il rendering del `RedirectToLogin` componente.
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
