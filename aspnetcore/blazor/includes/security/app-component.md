<span data-ttu-id="f80fa-101">Il `App` componente ( `App.razor` ) è simile al `App` componente presente nelle app del server Blazer:</span><span class="sxs-lookup"><span data-stu-id="f80fa-101">The `App` component (`App.razor`) is similar to the `App` component found in Blazor Server apps:</span></span>

* <span data-ttu-id="f80fa-102">Il <xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState> componente gestisce l'esposizione al <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState> resto dell'app.</span><span class="sxs-lookup"><span data-stu-id="f80fa-102">The <xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState> component manages exposing the <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState> to the rest of the app.</span></span>
* <span data-ttu-id="f80fa-103">Il <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView> componente verifica che l'utente corrente sia autorizzato ad accedere a una pagina specificata o altrimenti esegua il rendering del `RedirectToLogin` componente.</span><span class="sxs-lookup"><span data-stu-id="f80fa-103">The <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView> component makes sure that the current user is authorized to access a given page or otherwise renders the `RedirectToLogin` component.</span></span>
* <span data-ttu-id="f80fa-104">Il `RedirectToLogin` componente gestisce il reindirizzamento degli utenti non autorizzati alla pagina di accesso.</span><span class="sxs-lookup"><span data-stu-id="f80fa-104">The `RedirectToLogin` component manages redirecting unauthorized users to the login page.</span></span>

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

[!include[](../prefer-exact-matches.md)]
