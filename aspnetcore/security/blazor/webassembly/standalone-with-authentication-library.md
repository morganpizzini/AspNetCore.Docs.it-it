---
<span data-ttu-id="8893a-101">title:' proteggere un ASP.NET Core Blazor app webassembly autonoma con la libreria di autenticazione ' Author: Description: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="8893a-101">title: 'Secure an ASP.NET Core Blazor WebAssembly standalone app with the Authentication library' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8893a-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8893a-102">'Blazor'</span></span>
- <span data-ttu-id="8893a-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8893a-103">'Identity'</span></span>
- <span data-ttu-id="8893a-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8893a-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="8893a-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8893a-105">'Razor'</span></span>
- <span data-ttu-id="8893a-106">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="8893a-106">'SignalR' uid:</span></span> 

---
# <a name="secure-an-aspnet-core-blazor-webassembly-standalone-app-with-the-authentication-library"></a><span data-ttu-id="8893a-107">Proteggere un' Blazor app ASP.NET Core webassembly autonoma con la libreria di autenticazione</span><span class="sxs-lookup"><span data-stu-id="8893a-107">Secure an ASP.NET Core Blazor WebAssembly standalone app with the Authentication library</span></span>

<span data-ttu-id="8893a-108">Di [Javier Calvarro Nelson](https://github.com/javiercn) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="8893a-108">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="8893a-109">*Per Azure Active Directory (AAD) e Azure Active Directory B2C (AAD B2C), non seguire le istruzioni riportate in questo argomento. Vedere gli argomenti di AAD e AAD B2C in questo nodo Sommario.*</span><span class="sxs-lookup"><span data-stu-id="8893a-109">*For Azure Active Directory (AAD) and Azure Active Directory B2C (AAD B2C), don't follow the guidance in this topic. See the AAD and AAD B2C topics in this table of contents node.*</span></span>

<span data-ttu-id="8893a-110">Per creare un' Blazor app webassembly autonoma che usa `Microsoft.AspNetCore.Components.WebAssembly.Authentication` la libreria, eseguire il comando seguente in una shell dei comandi:</span><span class="sxs-lookup"><span data-stu-id="8893a-110">To create a Blazor WebAssembly standalone app that uses `Microsoft.AspNetCore.Components.WebAssembly.Authentication` library, execute the following command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au Individual
```

<span data-ttu-id="8893a-111">Per specificare il percorso di output, che crea una cartella di progetto, se non esiste, includere l'opzione di output nel comando con un percorso (ad esempio, `-o BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="8893a-111">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="8893a-112">Il nome della cartella diventa anche parte del nome del progetto.</span><span class="sxs-lookup"><span data-stu-id="8893a-112">The folder name also becomes part of the project's name.</span></span>

<span data-ttu-id="8893a-113">In Visual Studio [creare un' Blazor app webassembly](xref:blazor/get-started).</span><span class="sxs-lookup"><span data-stu-id="8893a-113">In Visual Studio, [create a Blazor WebAssembly app](xref:blazor/get-started).</span></span> <span data-ttu-id="8893a-114">Impostare l' **autenticazione** per **singoli account utente** con l'opzione **Archivia account utente in-app** .</span><span class="sxs-lookup"><span data-stu-id="8893a-114">Set **Authentication** to **Individual User Accounts** with the **Store user accounts in-app** option.</span></span>

## <a name="authentication-package"></a><span data-ttu-id="8893a-115">Pacchetto di autenticazione</span><span class="sxs-lookup"><span data-stu-id="8893a-115">Authentication package</span></span>

<span data-ttu-id="8893a-116">Quando si crea un'app per usare account utente singoli, l'app riceve automaticamente un riferimento al pacchetto per il `Microsoft.AspNetCore.Components.WebAssembly.Authentication` pacchetto nel file di progetto dell'app.</span><span class="sxs-lookup"><span data-stu-id="8893a-116">When an app is created to use Individual User Accounts, the app automatically receives a package reference for the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package in the app's project file.</span></span> <span data-ttu-id="8893a-117">Il pacchetto fornisce un set di primitive che consentono all'app di autenticare gli utenti e ottenere i token per chiamare le API protette.</span><span class="sxs-lookup"><span data-stu-id="8893a-117">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="8893a-118">Se si aggiunge l'autenticazione a un'app, aggiungere manualmente il pacchetto al file di progetto dell'app:</span><span class="sxs-lookup"><span data-stu-id="8893a-118">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference 
  Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
  Version="3.2.0" />
```

## <a name="authentication-service-support"></a><span data-ttu-id="8893a-119">Supporto del servizio di autenticazione</span><span class="sxs-lookup"><span data-stu-id="8893a-119">Authentication service support</span></span>

<span data-ttu-id="8893a-120">Il supporto per l'autenticazione degli utenti viene registrato nel contenitore del servizio con il `AddOidcAuthentication` metodo di estensione fornito dal `Microsoft.AspNetCore.Components.WebAssembly.Authentication` pacchetto.</span><span class="sxs-lookup"><span data-stu-id="8893a-120">Support for authenticating users is registered in the service container with the `AddOidcAuthentication` extension method provided by the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package.</span></span> <span data-ttu-id="8893a-121">Questo metodo configura i servizi necessari per l'interazione dell'app con il Identity provider (IP).</span><span class="sxs-lookup"><span data-stu-id="8893a-121">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="8893a-122">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="8893a-122">*Program.cs*:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    builder.Configuration.Bind("Local", options.ProviderOptions);
});
```

<span data-ttu-id="8893a-123">La configurazione viene fornita dal file *wwwroot/appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="8893a-123">Configuration is supplied by the *wwwroot/appsettings.json* file:</span></span>

```json
{
    "Local": {
        "Authority": "{AUTHORITY}",
        "ClientId": "{CLIENT ID}"
    }
}
```

<span data-ttu-id="8893a-124">Il supporto per l'autenticazione per le app autonome è disponibile con Open ID Connect (OIDC).</span><span class="sxs-lookup"><span data-stu-id="8893a-124">Authentication support for standalone apps is offered using Open ID Connect (OIDC).</span></span> <span data-ttu-id="8893a-125">Il `AddOidcAuthentication` metodo accetta un callback per configurare i parametri necessari per autenticare un'app usando OIDC.</span><span class="sxs-lookup"><span data-stu-id="8893a-125">The `AddOidcAuthentication` method accepts a callback to configure the parameters required to authenticate an app using OIDC.</span></span> <span data-ttu-id="8893a-126">I valori necessari per la configurazione dell'app possono essere ottenuti dall'IP conforme a OIDC.</span><span class="sxs-lookup"><span data-stu-id="8893a-126">The values required for configuring the app can be obtained from the OIDC-compliant IP.</span></span> <span data-ttu-id="8893a-127">Ottenere i valori quando si registra l'app, che in genere si verifica nel portale online.</span><span class="sxs-lookup"><span data-stu-id="8893a-127">Obtain the values when you register the app, which typically occurs in their online portal.</span></span>

## <a name="access-token-scopes"></a><span data-ttu-id="8893a-128">Ambiti del token di accesso</span><span class="sxs-lookup"><span data-stu-id="8893a-128">Access token scopes</span></span>

<span data-ttu-id="8893a-129">Il Blazor modello webassembly non configura automaticamente l'app per richiedere un token di accesso per un'API protetta.</span><span class="sxs-lookup"><span data-stu-id="8893a-129">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="8893a-130">Per eseguire il provisioning di un token di accesso come parte del flusso di accesso, aggiungere l'ambito agli ambiti dei token predefiniti di `OidcProviderOptions` :</span><span class="sxs-lookup"><span data-stu-id="8893a-130">To provision an access token as part of the sign-in flow, add the scope to the default token scopes of the `OidcProviderOptions`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

<span data-ttu-id="8893a-131">Per ulteriori informazioni, vedere le sezioni seguenti dell'articolo *scenari aggiuntivi* :</span><span class="sxs-lookup"><span data-stu-id="8893a-131">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="8893a-132">Richiedere token di accesso aggiuntivi</span><span class="sxs-lookup"><span data-stu-id="8893a-132">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="8893a-133">Connetti token alle richieste in uscita</span><span class="sxs-lookup"><span data-stu-id="8893a-133">Attach tokens to outgoing requests</span></span>](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a><span data-ttu-id="8893a-134">Importa file</span><span class="sxs-lookup"><span data-stu-id="8893a-134">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="8893a-135">Pagina di indice</span><span class="sxs-lookup"><span data-stu-id="8893a-135">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

## <a name="app-component"></a><span data-ttu-id="8893a-136">Componente app</span><span class="sxs-lookup"><span data-stu-id="8893a-136">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="8893a-137">Componente RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="8893a-137">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="8893a-138">Componente LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="8893a-138">LoginDisplay component</span></span>

<span data-ttu-id="8893a-139">Il `LoginDisplay` componente (*Shared/LoginDisplay. Razor*) viene sottoposto a rendering nel `MainLayout` componente (*Shared/MainLayout. Razor*) e gestisce i comportamenti seguenti:</span><span class="sxs-lookup"><span data-stu-id="8893a-139">The `LoginDisplay` component (*Shared/LoginDisplay.razor*) is rendered in the `MainLayout` component (*Shared/MainLayout.razor*) and manages the following behaviors:</span></span>

* <span data-ttu-id="8893a-140">Per utenti autenticati:</span><span class="sxs-lookup"><span data-stu-id="8893a-140">For authenticated users:</span></span>
  * <span data-ttu-id="8893a-141">Visualizza il nome utente corrente.</span><span class="sxs-lookup"><span data-stu-id="8893a-141">Displays the current username.</span></span>
  * <span data-ttu-id="8893a-142">Offre un pulsante per disconnettersi dall'app.</span><span class="sxs-lookup"><span data-stu-id="8893a-142">Offers a button to log out of the app.</span></span>
* <span data-ttu-id="8893a-143">Per gli utenti anonimi, offre la possibilità di eseguire l'accesso.</span><span class="sxs-lookup"><span data-stu-id="8893a-143">For anonymous users, offers the option to log in.</span></span>

```razor
@using Microsoft.AspNetCore.Components.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject NavigationManager Navigation
@inject SignOutSessionStateManager SignOutManager

<AuthorizeView>
    <Authorized>
        Hello, @context.User.Identity.Name!
        <button class="nav-link btn btn-link" @onclick="BeginSignOut">
            Log out
        </button>
    </Authorized>
    <NotAuthorized>
        <a href="authentication/login">Log in</a>
    </NotAuthorized>
</AuthorizeView>

@code {
    private async Task BeginSignOut(MouseEventArgs args)
    {
        await SignOutManager.SetSignOutState();
        Navigation.NavigateTo("authentication/logout");
    }
}
```

## <a name="authentication-component"></a><span data-ttu-id="8893a-144">Componente di autenticazione</span><span class="sxs-lookup"><span data-stu-id="8893a-144">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="8893a-145">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="8893a-145">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* [<span data-ttu-id="8893a-146">Richieste API Web non autenticate o non autorizzate in un'app con un client predefinito sicuro</span><span class="sxs-lookup"><span data-stu-id="8893a-146">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:security/blazor/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
