---
title: BlazorAutenticazione e autorizzazione ASP.NET Core
author: guardrex
description: Informazioni sugli Blazor scenari di autenticazione e autorizzazione.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/security/index
ms.openlocfilehash: f198cef0cf990dcc2806d3967130db64af47c1d0
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/19/2020
ms.locfileid: "88627832"
---
# <a name="aspnet-core-no-locblazor-authentication-and-authorization"></a><span data-ttu-id="62c86-103">BlazorAutenticazione e autorizzazione ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="62c86-103">ASP.NET Core Blazor authentication and authorization</span></span>

<span data-ttu-id="62c86-104">Di [Steve Sanderson](https://github.com/SteveSandersonMS) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="62c86-104">By [Steve Sanderson](https://github.com/SteveSandersonMS) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="62c86-105">ASP.NET Core supporta la configurazione e la gestione della sicurezza nelle Blazor app.</span><span class="sxs-lookup"><span data-stu-id="62c86-105">ASP.NET Core supports the configuration and management of security in Blazor apps.</span></span>

<span data-ttu-id="62c86-106">Gli scenari di sicurezza sono diversi tra Blazor Server le Blazor WebAssembly app e.</span><span class="sxs-lookup"><span data-stu-id="62c86-106">Security scenarios differ between Blazor Server and Blazor WebAssembly apps.</span></span> <span data-ttu-id="62c86-107">Poiché Blazor Server le app vengono eseguite nel server, i controlli delle autorizzazioni sono in grado di determinare:</span><span class="sxs-lookup"><span data-stu-id="62c86-107">Because Blazor Server apps run on the server, authorization checks are able to determine:</span></span>

* <span data-ttu-id="62c86-108">Le opzioni dell'interfaccia utente presentate all'utente (ad esempio, le voci di menu disponibili per un utente).</span><span class="sxs-lookup"><span data-stu-id="62c86-108">The UI options presented to a user (for example, which menu entries are available to a user).</span></span>
* <span data-ttu-id="62c86-109">Le regole di accesso per le aree dell'app e i componenti.</span><span class="sxs-lookup"><span data-stu-id="62c86-109">Access rules for areas of the app and components.</span></span>

<span data-ttu-id="62c86-110">Blazor WebAssembly app eseguite sul client.</span><span class="sxs-lookup"><span data-stu-id="62c86-110">Blazor WebAssembly apps run on the client.</span></span> <span data-ttu-id="62c86-111">L'autorizzazione viene usata *solo* per determinare quali opzioni dell'interfaccia utente visualizzare.</span><span class="sxs-lookup"><span data-stu-id="62c86-111">Authorization is *only* used to determine which UI options to show.</span></span> <span data-ttu-id="62c86-112">Poiché i controlli lato client possono essere modificati o ignorati da un utente, un' Blazor WebAssembly app non può applicare regole di accesso alle autorizzazioni.</span><span class="sxs-lookup"><span data-stu-id="62c86-112">Since client-side checks can be modified or bypassed by a user, a Blazor WebAssembly app can't enforce authorization access rules.</span></span>

<span data-ttu-id="62c86-113">Le [ Razor convenzioni di autorizzazione pagine](xref:security/authorization/razor-pages-authorization) non si applicano ai componenti instradabili Razor .</span><span class="sxs-lookup"><span data-stu-id="62c86-113">[Razor Pages authorization conventions](xref:security/authorization/razor-pages-authorization) don't apply to routable Razor components.</span></span> <span data-ttu-id="62c86-114">Se un componente non instradabile Razor è [incorporato in una pagina](xref:blazor/components/integrate-components-into-razor-pages-and-mvc-apps#render-components-from-a-page-or-view), le convenzioni di autorizzazione della pagina influiscono indirettamente sul Razor componente insieme al resto del contenuto della pagina.</span><span class="sxs-lookup"><span data-stu-id="62c86-114">If a non-routable Razor component is [embedded in a page](xref:blazor/components/integrate-components-into-razor-pages-and-mvc-apps#render-components-from-a-page-or-view), the page's authorization conventions indirectly affect the Razor component along with the rest of the page's content.</span></span>

> [!NOTE]
> <span data-ttu-id="62c86-115"><xref:Microsoft.AspNetCore.Identity.SignInManager%601> e <xref:Microsoft.AspNetCore.Identity.UserManager%601> non sono supportati nei Razor componenti di.</span><span class="sxs-lookup"><span data-stu-id="62c86-115"><xref:Microsoft.AspNetCore.Identity.SignInManager%601> and <xref:Microsoft.AspNetCore.Identity.UserManager%601> aren't supported in Razor components.</span></span>

## <a name="authentication"></a><span data-ttu-id="62c86-116">Authentication</span><span class="sxs-lookup"><span data-stu-id="62c86-116">Authentication</span></span>

<span data-ttu-id="62c86-117">Blazor USA i meccanismi di autenticazione ASP.NET Core esistenti per stabilire l'identità dell'utente.</span><span class="sxs-lookup"><span data-stu-id="62c86-117">Blazor uses the existing ASP.NET Core authentication mechanisms to establish the user's identity.</span></span> <span data-ttu-id="62c86-118">Il meccanismo esatto dipende dalla modalità di Blazor hosting dell'app o da Blazor WebAssembly Blazor Server .</span><span class="sxs-lookup"><span data-stu-id="62c86-118">The exact mechanism depends on how the Blazor app is hosted, Blazor WebAssembly or Blazor Server.</span></span>

### <a name="no-locblazor-webassembly-authentication"></a><span data-ttu-id="62c86-119">Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="62c86-119">Blazor WebAssembly authentication</span></span>

<span data-ttu-id="62c86-120">Nelle Blazor WebAssembly app, i controlli di autenticazione possono essere ignorati perché tutto il codice lato client può essere modificato dagli utenti.</span><span class="sxs-lookup"><span data-stu-id="62c86-120">In Blazor WebAssembly apps, authentication checks can be bypassed because all client-side code can be modified by users.</span></span> <span data-ttu-id="62c86-121">Lo stesso vale per tutte le tecnologie per app sul lato client, tra cui i framework JavaScript SPA o le app native per qualsiasi sistema operativo.</span><span class="sxs-lookup"><span data-stu-id="62c86-121">The same is true for all client-side app technologies, including JavaScript SPA frameworks or native apps for any operating system.</span></span>

<span data-ttu-id="62c86-122">Aggiungere quanto segue:</span><span class="sxs-lookup"><span data-stu-id="62c86-122">Add the following:</span></span>

* <span data-ttu-id="62c86-123">Riferimento al pacchetto per [`Microsoft.AspNetCore.Components.Authorization`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Authorization) il file di progetto dell'app.</span><span class="sxs-lookup"><span data-stu-id="62c86-123">A package reference for [`Microsoft.AspNetCore.Components.Authorization`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Authorization) to the app's project file.</span></span>
* <span data-ttu-id="62c86-124">`Microsoft.AspNetCore.Components.Authorization`Spazio dei nomi del file dell' `_Imports.razor` app.</span><span class="sxs-lookup"><span data-stu-id="62c86-124">The `Microsoft.AspNetCore.Components.Authorization` namespace to the app's `_Imports.razor` file.</span></span>

<span data-ttu-id="62c86-125">Per gestire l'autenticazione, l'uso di un servizio incorporato o personalizzato <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> viene trattato nelle sezioni seguenti.</span><span class="sxs-lookup"><span data-stu-id="62c86-125">To handle authentication, use of a built-in or custom <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> service is covered in the following sections.</span></span>

<span data-ttu-id="62c86-126">Per ulteriori informazioni sulla creazione di app e configurazione, vedere <xref:blazor/security/webassembly/index> .</span><span class="sxs-lookup"><span data-stu-id="62c86-126">For more information on creating apps and configuration, see <xref:blazor/security/webassembly/index>.</span></span>

### <a name="no-locblazor-server-authentication"></a><span data-ttu-id="62c86-127">Blazor Server</span><span class="sxs-lookup"><span data-stu-id="62c86-127">Blazor Server authentication</span></span>

<span data-ttu-id="62c86-128">Blazor Server le app funzionano in una connessione in tempo reale creata con SignalR .</span><span class="sxs-lookup"><span data-stu-id="62c86-128">Blazor Server apps operate over a real-time connection that's created using SignalR.</span></span> <span data-ttu-id="62c86-129">L' [autenticazione in SignalR app basate su](xref:signalr/authn-and-authz) viene gestita quando viene stabilita la connessione.</span><span class="sxs-lookup"><span data-stu-id="62c86-129">[Authentication in SignalR-based apps](xref:signalr/authn-and-authz) is handled when the connection is established.</span></span> <span data-ttu-id="62c86-130">L'autenticazione può essere basata su un cookie o su altri Bearer token.</span><span class="sxs-lookup"><span data-stu-id="62c86-130">Authentication can be based on a cookie or some other bearer token.</span></span>

<span data-ttu-id="62c86-131">Il <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> servizio incorporato per le Blazor Server app ottiene i dati sullo stato di autenticazione da ASP.NET Core `HttpContext.User` .</span><span class="sxs-lookup"><span data-stu-id="62c86-131">The built-in <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> service for Blazor Server apps obtains authentication state data from ASP.NET Core's `HttpContext.User`.</span></span> <span data-ttu-id="62c86-132">Questo è il modo in cui lo stato di autenticazione si integra con i meccanismi di autenticazione ASP.NET Core esistenti.</span><span class="sxs-lookup"><span data-stu-id="62c86-132">This is how authentication state integrates with existing ASP.NET Core authentication mechanisms.</span></span>

<span data-ttu-id="62c86-133">Per ulteriori informazioni sulla creazione di app e configurazione, vedere <xref:blazor/security/server/index> .</span><span class="sxs-lookup"><span data-stu-id="62c86-133">For more information on creating apps and configuration, see <xref:blazor/security/server/index>.</span></span>

## <a name="authenticationstateprovider-service"></a><span data-ttu-id="62c86-134">Servizio AuthenticationStateProvider</span><span class="sxs-lookup"><span data-stu-id="62c86-134">AuthenticationStateProvider service</span></span>

<span data-ttu-id="62c86-135"><xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> è il servizio sottostante usato dal componente <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> e dal componente <xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState> per ottenere lo stato di autenticazione.</span><span class="sxs-lookup"><span data-stu-id="62c86-135"><xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> is the underlying service used by the <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> component and <xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState> component to get the authentication state.</span></span>

<span data-ttu-id="62c86-136">In genere non si usa <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> direttamente.</span><span class="sxs-lookup"><span data-stu-id="62c86-136">You don't typically use <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> directly.</span></span> <span data-ttu-id="62c86-137">Usare il [ `AuthorizeView` componente](#authorizeview-component) o gli [`Task<AuthenticationState>`](#expose-the-authentication-state-as-a-cascading-parameter) approcci descritti più avanti in questo articolo.</span><span class="sxs-lookup"><span data-stu-id="62c86-137">Use the [`AuthorizeView` component](#authorizeview-component) or [`Task<AuthenticationState>`](#expose-the-authentication-state-as-a-cascading-parameter) approaches described later in this article.</span></span> <span data-ttu-id="62c86-138">Lo svantaggio principale dell'uso diretto di <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> è che il componente non riceve alcuna notifica automaticamente se i dati relativi allo stato di autenticazione sottostanti cambiano.</span><span class="sxs-lookup"><span data-stu-id="62c86-138">The main drawback to using <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> directly is that the component isn't notified automatically if the underlying authentication state data changes.</span></span>

<span data-ttu-id="62c86-139">Il servizio <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> può fornire i dati <xref:System.Security.Claims.ClaimsPrincipal> dell'utente corrente, come illustrato nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="62c86-139">The <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> service can provide the current user's <xref:System.Security.Claims.ClaimsPrincipal> data, as shown in the following example:</span></span>

```razor
@page "/"
@using System.Security.Claims
@using Microsoft.AspNetCore.Components.Authorization
@inject AuthenticationStateProvider AuthenticationStateProvider

<h3>ClaimsPrincipal Data</h3>

<button @onclick="GetClaimsPrincipalData">Get ClaimsPrincipal Data</button>

<p>@_authMessage</p>

@if (_claims.Count() > 0)
{
    <ul>
        @foreach (var claim in _claims)
        {
            <li>@claim.Type: @claim.Value</li>
        }
    </ul>
}

<p>@_surnameMessage</p>

@code {
    private string _authMessage;
    private string _surnameMessage;
    private IEnumerable<Claim> _claims = Enumerable.Empty<Claim>();

    private async Task GetClaimsPrincipalData()
    {
        var authState = await AuthenticationStateProvider.GetAuthenticationStateAsync();
        var user = authState.User;

        if (user.Identity.IsAuthenticated)
        {
            _authMessage = $"{user.Identity.Name} is authenticated.";
            _claims = user.Claims;
            _surnameMessage = 
                $"Surname: {user.FindFirst(c => c.Type == ClaimTypes.Surname)?.Value}";
        }
        else
        {
            _authMessage = "The user is NOT authenticated.";
        }
    }
}
```

<span data-ttu-id="62c86-140">Se `user.Identity.IsAuthenticated` è `true` e perché l'utente è un <xref:System.Security.Claims.ClaimsPrincipal>, è possibile enumerare le attestazioni e valutare l'appartenenza ai ruoli.</span><span class="sxs-lookup"><span data-stu-id="62c86-140">If `user.Identity.IsAuthenticated` is `true` and because the user is a <xref:System.Security.Claims.ClaimsPrincipal>, claims can be enumerated and membership in roles evaluated.</span></span>

<span data-ttu-id="62c86-141">Per altre informazioni sull'inserimento delle dipendenze e sui servizi, vedere <xref:blazor/fundamentals/dependency-injection> e <xref:fundamentals/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="62c86-141">For more information on dependency injection (DI) and services, see <xref:blazor/fundamentals/dependency-injection> and <xref:fundamentals/dependency-injection>.</span></span>

## <a name="implement-a-custom-authenticationstateprovider"></a><span data-ttu-id="62c86-142">Implementare un AuthenticationStateProvider personalizzato</span><span class="sxs-lookup"><span data-stu-id="62c86-142">Implement a custom AuthenticationStateProvider</span></span>

<span data-ttu-id="62c86-143">Se l'app richiede un provider personalizzato, implementare <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> ed eseguire l'override di `GetAuthenticationStateAsync` :</span><span class="sxs-lookup"><span data-stu-id="62c86-143">If the app requires a custom provider, implement <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> and override `GetAuthenticationStateAsync`:</span></span>

```csharp
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.Authorization;

public class CustomAuthStateProvider : AuthenticationStateProvider
{
    public override Task<AuthenticationState> GetAuthenticationStateAsync()
    {
        var identity = new ClaimsIdentity(new[]
        {
            new Claim(ClaimTypes.Name, "mrfibuli"),
        }, "Fake authentication type");

        var user = new ClaimsPrincipal(identity);

        return Task.FromResult(new AuthenticationState(user));
    }
}
```

<span data-ttu-id="62c86-144">In un' Blazor WebAssembly app, il `CustomAuthStateProvider` servizio è registrato in `Main` di `Program.cs` :</span><span class="sxs-lookup"><span data-stu-id="62c86-144">In a Blazor WebAssembly app, the `CustomAuthStateProvider` service is registered in `Main` of `Program.cs`:</span></span>

```csharp
using Microsoft.AspNetCore.Components.Authorization;

...

builder.Services.AddScoped<AuthenticationStateProvider, CustomAuthStateProvider>();
```

<span data-ttu-id="62c86-145">In un' Blazor Server app il `CustomAuthStateProvider` servizio è registrato in `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="62c86-145">In a Blazor Server app, the `CustomAuthStateProvider` service is registered in `Startup.ConfigureServices`:</span></span>

```csharp
using Microsoft.AspNetCore.Components.Authorization;

...

services.AddScoped<AuthenticationStateProvider, CustomAuthStateProvider>();
```

<span data-ttu-id="62c86-146">Utilizzando l'oggetto `CustomAuthStateProvider` nell'esempio precedente, tutti gli utenti vengono autenticati con il nome utente `mrfibuli` .</span><span class="sxs-lookup"><span data-stu-id="62c86-146">Using the `CustomAuthStateProvider` in the preceding example, all users are authenticated with the username `mrfibuli`.</span></span>

## <a name="expose-the-authentication-state-as-a-cascading-parameter"></a><span data-ttu-id="62c86-147">Esporre lo stato di autenticazione come un parametro a catena</span><span class="sxs-lookup"><span data-stu-id="62c86-147">Expose the authentication state as a cascading parameter</span></span>

<span data-ttu-id="62c86-148">Se per la logica procedurale sono necessari dati sullo stato di autenticazione, ad esempio quando si esegue un'azione attivata dall'utente, ottenere i dati dello stato di autenticazione definendo un parametro di propagazione di tipo `Task<` <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState> `>` :</span><span class="sxs-lookup"><span data-stu-id="62c86-148">If authentication state data is required for procedural logic, such as when performing an action triggered by the user, obtain the authentication state data by defining a cascading parameter of type `Task<`<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>`>`:</span></span>

```razor
@page "/"

<button @onclick="LogUsername">Log username</button>

<p>@_authMessage</p>

@code {
    [CascadingParameter]
    private Task<AuthenticationState> authenticationStateTask { get; set; }

    private string _authMessage;

    private async Task LogUsername()
    {
        var authState = await authenticationStateTask;
        var user = authState.User;

        if (user.Identity.IsAuthenticated)
        {
            _authMessage = $"{user.Identity.Name} is authenticated.";
        }
        else
        {
            _authMessage = "The user is NOT authenticated.";
        }
    }
}
```

<span data-ttu-id="62c86-149">Se `user.Identity.IsAuthenticated` è `true`, è possibile enumerare le attestazioni e valutare l'appartenenza ai ruoli.</span><span class="sxs-lookup"><span data-stu-id="62c86-149">If `user.Identity.IsAuthenticated` is `true`, claims can be enumerated and membership in roles evaluated.</span></span>

<span data-ttu-id="62c86-150">Configurare il parametro di propagazione `Task<` <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState> `>` utilizzando <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView> i <xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState> componenti e nel `App` componente ( `App.razor` ):</span><span class="sxs-lookup"><span data-stu-id="62c86-150">Set up the `Task<`<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>`>` cascading parameter using the <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView> and <xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState> components in the `App` component (`App.razor`):</span></span>

```razor
<CascadingAuthenticationState>
    <Router AppAssembly="@typeof(Program).Assembly">
        <Found Context="routeData">
            <AuthorizeRouteView RouteData="@routeData" 
                DefaultLayout="@typeof(MainLayout)" />
        </Found>
        <NotFound>
            <LayoutView Layout="@typeof(MainLayout)">
                <p>Sorry, there's nothing at this address.</p>
            </LayoutView>
        </NotFound>
    </Router>
</CascadingAuthenticationState>
```

<span data-ttu-id="62c86-151">In un' Blazor WebAssembly App aggiungere servizi per le opzioni e l'autorizzazione a `Program.Main` :</span><span class="sxs-lookup"><span data-stu-id="62c86-151">In a Blazor WebAssembly App, add services for options and authorization to `Program.Main`:</span></span>

```csharp
builder.Services.AddOptions();
builder.Services.AddAuthorizationCore();
```

<span data-ttu-id="62c86-152">In un' Blazor Server applicazione i servizi per le opzioni e l'autorizzazione sono già presenti, pertanto non è richiesta alcuna azione aggiuntiva.</span><span class="sxs-lookup"><span data-stu-id="62c86-152">In a Blazor Server app, services for options and authorization are already present, so no further action is required.</span></span>

## <a name="authorization"></a><span data-ttu-id="62c86-153">Autorizzazione</span><span class="sxs-lookup"><span data-stu-id="62c86-153">Authorization</span></span>

<span data-ttu-id="62c86-154">Dopo l'autenticazione di un utente, vengono applicate le regole di *autorizzazione* per controllare le operazioni consentite per un utente.</span><span class="sxs-lookup"><span data-stu-id="62c86-154">After a user is authenticated, *authorization* rules are applied to control what the user can do.</span></span>

<span data-ttu-id="62c86-155">L'accesso viene in genere concesso o negato in base alle condizioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="62c86-155">Access is typically granted or denied based on whether:</span></span>

* <span data-ttu-id="62c86-156">L'utente è autenticato (ha eseguito l'accesso).</span><span class="sxs-lookup"><span data-stu-id="62c86-156">A user is authenticated (signed in).</span></span>
* <span data-ttu-id="62c86-157">L'utente è incluso in un *ruolo*.</span><span class="sxs-lookup"><span data-stu-id="62c86-157">A user is in a *role*.</span></span>
* <span data-ttu-id="62c86-158">L'utente ha un'*attestazione*.</span><span class="sxs-lookup"><span data-stu-id="62c86-158">A user has a *claim*.</span></span>
* <span data-ttu-id="62c86-159">I *criteri* sono soddisfatti.</span><span class="sxs-lookup"><span data-stu-id="62c86-159">A *policy* is satisfied.</span></span>

<span data-ttu-id="62c86-160">Ognuno di questi concetti è identico a quello di un'app ASP.NET Core MVC o Razor pages.</span><span class="sxs-lookup"><span data-stu-id="62c86-160">Each of these concepts is the same as in an ASP.NET Core MVC or Razor Pages app.</span></span> <span data-ttu-id="62c86-161">Per ulteriori informazioni sulla sicurezza ASP.NET Core, vedere gli articoli in [sicurezza ASP.NET Core e Identity ](xref:security/index).</span><span class="sxs-lookup"><span data-stu-id="62c86-161">For more information on ASP.NET Core security, see the articles under [ASP.NET Core Security and Identity](xref:security/index).</span></span>

## <a name="authorizeview-component"></a><span data-ttu-id="62c86-162">Componente AuthorizeView</span><span class="sxs-lookup"><span data-stu-id="62c86-162">AuthorizeView component</span></span>

<span data-ttu-id="62c86-163">Il componente <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> visualizza in modo selettivo l'interfaccia utente a seconda del fatto che l'utente sia autorizzato a visualizzarla.</span><span class="sxs-lookup"><span data-stu-id="62c86-163">The <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> component selectively displays UI depending on whether the user is authorized to see it.</span></span> <span data-ttu-id="62c86-164">Questo approccio è utile quando è necessario solo *visualizzare* dati per l'utente e non occorre usare l'identità dell'utente nella logica procedurale.</span><span class="sxs-lookup"><span data-stu-id="62c86-164">This approach is useful when you only need to *display* data for the user and don't need to use the user's identity in procedural logic.</span></span>

<span data-ttu-id="62c86-165">Il componente espone una variabile `context` di tipo <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>, che è possibile usare per accedere alle informazioni sull'utente connesso:</span><span class="sxs-lookup"><span data-stu-id="62c86-165">The component exposes a `context` variable of type <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>, which you can use to access information about the signed-in user:</span></span>

```razor
<AuthorizeView>
    <h1>Hello, @context.User.Identity.Name!</h1>
    <p>You can only see this content if you're authenticated.</p>
</AuthorizeView>
```

<span data-ttu-id="62c86-166">Se l'utente non è autenticato, è anche possibile fornire un contenuto diverso per la visualizzazione:</span><span class="sxs-lookup"><span data-stu-id="62c86-166">You can also supply different content for display if the user isn't authenticated:</span></span>

```razor
<AuthorizeView>
    <Authorized>
        <h1>Hello, @context.User.Identity.Name!</h1>
        <p>You can only see this content if you're authenticated.</p>
    </Authorized>
    <NotAuthorized>
        <h1>Authentication Failure!</h1>
        <p>You're not signed in.</p>
    </NotAuthorized>
</AuthorizeView>
```

<span data-ttu-id="62c86-167">Il <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> componente può essere utilizzato nel `NavMenu` componente () `Shared/NavMenu.razor` per visualizzare un elemento elenco ( `<li>...</li>` ) per un [ `NavLink` componente](xref:blazor/fundamentals/routing#navlink-component) ( <xref:Microsoft.AspNetCore.Components.Routing.NavLink> ), ma si noti che questo approccio rimuove solo l'elemento dell'elenco dall'output di cui è stato eseguito il rendering.</span><span class="sxs-lookup"><span data-stu-id="62c86-167">The <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> component can be used in the `NavMenu` component (`Shared/NavMenu.razor`) to display a list item (`<li>...</li>`) for a [`NavLink` component](xref:blazor/fundamentals/routing#navlink-component) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), but note that this approach only removes the list item from the rendered output.</span></span> <span data-ttu-id="62c86-168">Non impedisce all'utente di spostarsi nel componente.</span><span class="sxs-lookup"><span data-stu-id="62c86-168">It doesn't prevent the user from navigating to the component.</span></span>

<span data-ttu-id="62c86-169">Il contenuto dei `<Authorized>` `<NotAuthorized>` tag e può includere elementi arbitrari, ad esempio altri componenti interattivi.</span><span class="sxs-lookup"><span data-stu-id="62c86-169">The content of `<Authorized>` and `<NotAuthorized>` tags can include arbitrary items, such as other interactive components.</span></span>

<span data-ttu-id="62c86-170">Le condizioni di autorizzazione, ad esempio i ruoli o i criteri che consentono di controllare le opzioni dell'interfaccia utente o l'accesso, sono presentate nella sezione [Autorizzazione](#authorization).</span><span class="sxs-lookup"><span data-stu-id="62c86-170">Authorization conditions, such as roles or policies that control UI options or access, are covered in the [Authorization](#authorization) section.</span></span>

<span data-ttu-id="62c86-171">Se non sono specificate condizioni di autorizzazione, <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> usa criteri predefiniti e considera:</span><span class="sxs-lookup"><span data-stu-id="62c86-171">If authorization conditions aren't specified, <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> uses a default policy and treats:</span></span>

* <span data-ttu-id="62c86-172">Gli utenti autenticati (che hanno eseguito l'accesso) come autorizzati.</span><span class="sxs-lookup"><span data-stu-id="62c86-172">Authenticated (signed-in) users as authorized.</span></span>
* <span data-ttu-id="62c86-173">Gli utenti non autenticati (disconnessi) come non autorizzati.</span><span class="sxs-lookup"><span data-stu-id="62c86-173">Unauthenticated (signed-out) users as unauthorized.</span></span>

### <a name="role-based-and-policy-based-authorization"></a><span data-ttu-id="62c86-174">Autorizzazione basata sui ruoli e basata sui criteri</span><span class="sxs-lookup"><span data-stu-id="62c86-174">Role-based and policy-based authorization</span></span>

<span data-ttu-id="62c86-175">Il componente <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> supporta l'autorizzazione *basata sui ruoli* oppure *basata sui criteri*.</span><span class="sxs-lookup"><span data-stu-id="62c86-175">The <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> component supports *role-based* or *policy-based* authorization.</span></span>

<span data-ttu-id="62c86-176">Per l'autorizzazione basata sui ruoli, usare il parametro <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Roles>:</span><span class="sxs-lookup"><span data-stu-id="62c86-176">For role-based authorization, use the <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Roles> parameter:</span></span>

```razor
<AuthorizeView Roles="admin, superuser">
    <p>You can only see this if you're an admin or superuser.</p>
</AuthorizeView>
```

<span data-ttu-id="62c86-177">Per altre informazioni, vedere <xref:security/authorization/roles>.</span><span class="sxs-lookup"><span data-stu-id="62c86-177">For more information, see <xref:security/authorization/roles>.</span></span>

<span data-ttu-id="62c86-178">Per l'autorizzazione basata sui criteri, usare il parametro <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Policy>:</span><span class="sxs-lookup"><span data-stu-id="62c86-178">For policy-based authorization, use the <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Policy> parameter:</span></span>

```razor
<AuthorizeView Policy="content-editor">
    <p>You can only see this if you satisfy the "content-editor" policy.</p>
</AuthorizeView>
```

<span data-ttu-id="62c86-179">L'autorizzazione basata sulle attestazioni è un caso speciale di autorizzazione basata su criteri.</span><span class="sxs-lookup"><span data-stu-id="62c86-179">Claims-based authorization is a special case of policy-based authorization.</span></span> <span data-ttu-id="62c86-180">Ad esempio, è possibile definire un criterio che richiede che gli utenti abbiano una determinata attestazione.</span><span class="sxs-lookup"><span data-stu-id="62c86-180">For example, you can define a policy that requires users to have a certain claim.</span></span> <span data-ttu-id="62c86-181">Per altre informazioni, vedere <xref:security/authorization/policies>.</span><span class="sxs-lookup"><span data-stu-id="62c86-181">For more information, see <xref:security/authorization/policies>.</span></span>

<span data-ttu-id="62c86-182">Queste API possono essere usate in Blazor Server o nelle Blazor WebAssembly app.</span><span class="sxs-lookup"><span data-stu-id="62c86-182">These APIs can be used in either Blazor Server or Blazor WebAssembly apps.</span></span>

<span data-ttu-id="62c86-183">Se non si specifica <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Roles> o <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Policy>, <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> usa i criteri predefiniti.</span><span class="sxs-lookup"><span data-stu-id="62c86-183">If neither <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Roles> nor <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Policy> is specified, <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> uses the default policy.</span></span>

### <a name="content-displayed-during-asynchronous-authentication"></a><span data-ttu-id="62c86-184">Contenuto visualizzato durante l'autenticazione asincrona</span><span class="sxs-lookup"><span data-stu-id="62c86-184">Content displayed during asynchronous authentication</span></span>

<span data-ttu-id="62c86-185">Blazor consente di determinare lo stato di autenticazione in *modo asincrono*.</span><span class="sxs-lookup"><span data-stu-id="62c86-185">Blazor allows for authentication state to be determined *asynchronously*.</span></span> <span data-ttu-id="62c86-186">Lo scenario principale per questo approccio è nelle Blazor WebAssembly app che effettuano una richiesta a un endpoint esterno per l'autenticazione.</span><span class="sxs-lookup"><span data-stu-id="62c86-186">The primary scenario for this approach is in Blazor WebAssembly apps that make a request to an external endpoint for authentication.</span></span>

<span data-ttu-id="62c86-187">Mentre è in corso l'autenticazione <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> non visualizza alcun contenuto per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="62c86-187">While authentication is in progress, <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> displays no content by default.</span></span> <span data-ttu-id="62c86-188">Per visualizzare il contenuto quando si verifica l'autenticazione, usare il `<Authorizing>` Tag:</span><span class="sxs-lookup"><span data-stu-id="62c86-188">To display content while authentication occurs, use the `<Authorizing>` tag:</span></span>

```razor
<AuthorizeView>
    <Authorized>
        <h1>Hello, @context.User.Identity.Name!</h1>
        <p>You can only see this content if you're authenticated.</p>
    </Authorized>
    <Authorizing>
        <h1>Authentication in progress</h1>
        <p>You can only see this content while authentication is in progress.</p>
    </Authorizing>
</AuthorizeView>
```

<span data-ttu-id="62c86-189">Questo approccio non è in genere applicabile alle Blazor Server app.</span><span class="sxs-lookup"><span data-stu-id="62c86-189">This approach isn't normally applicable to Blazor Server apps.</span></span> <span data-ttu-id="62c86-190">Blazor Server le app conoscono lo stato di autenticazione non appena viene stabilito lo stato.</span><span class="sxs-lookup"><span data-stu-id="62c86-190">Blazor Server apps know the authentication state as soon as the state is established.</span></span> <span data-ttu-id="62c86-191"><xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeViewCore.Authorizing> il contenuto può essere fornito nel Blazor Server componente di un'app <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> , ma il contenuto non viene mai visualizzato.</span><span class="sxs-lookup"><span data-stu-id="62c86-191"><xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeViewCore.Authorizing> content can be provided in a Blazor Server app's <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> component, but the content is never displayed.</span></span>

## <a name="authorize-attribute"></a><span data-ttu-id="62c86-192">Attributo [Authorize]</span><span class="sxs-lookup"><span data-stu-id="62c86-192">[Authorize] attribute</span></span>

<span data-ttu-id="62c86-193">L' [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attributo può essere utilizzato nei Razor componenti di:</span><span class="sxs-lookup"><span data-stu-id="62c86-193">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute can be used in Razor components:</span></span>

```razor
@page "/"
@attribute [Authorize]

You can only see this if you're signed in.
```

> [!IMPORTANT]
> <span data-ttu-id="62c86-194">Usare solo [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) sui `@page` componenti raggiunti tramite il Blazor router.</span><span class="sxs-lookup"><span data-stu-id="62c86-194">Only use [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) on `@page` components reached via the Blazor Router.</span></span> <span data-ttu-id="62c86-195">L'autorizzazione viene eseguita solo come un aspetto del routing e *non* per i componenti figlio di cui viene eseguito il rendering all'interno di una pagina.</span><span class="sxs-lookup"><span data-stu-id="62c86-195">Authorization is only performed as an aspect of routing and *not* for child components rendered within a page.</span></span> <span data-ttu-id="62c86-196">Per autorizzare la visualizzazione di parti specifiche all'interno di una pagina, usare invece <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView>.</span><span class="sxs-lookup"><span data-stu-id="62c86-196">To authorize the display of specific parts within a page, use <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> instead.</span></span>

<span data-ttu-id="62c86-197">L' [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attributo supporta inoltre l'autorizzazione basata su ruoli o basata su criteri.</span><span class="sxs-lookup"><span data-stu-id="62c86-197">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute also supports role-based or policy-based authorization.</span></span> <span data-ttu-id="62c86-198">Per l'autorizzazione basata sui ruoli, usare il parametro <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Roles>:</span><span class="sxs-lookup"><span data-stu-id="62c86-198">For role-based authorization, use the <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Roles> parameter:</span></span>

```razor
@page "/"
@attribute [Authorize(Roles = "admin, superuser")]

<p>You can only see this if you're in the 'admin' or 'superuser' role.</p>
```

<span data-ttu-id="62c86-199">Per l'autorizzazione basata sui criteri, usare il parametro <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Policy>:</span><span class="sxs-lookup"><span data-stu-id="62c86-199">For policy-based authorization, use the <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Policy> parameter:</span></span>

```razor
@page "/"
@attribute [Authorize(Policy = "content-editor")]

<p>You can only see this if you satisfy the 'content-editor' policy.</p>
```

<span data-ttu-id="62c86-200">Se non <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Roles> <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Policy> si specifica né né, [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) Usa i criteri predefiniti, che per impostazione predefinita devono trattare:</span><span class="sxs-lookup"><span data-stu-id="62c86-200">If neither <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Roles> nor <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Policy> is specified, [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) uses the default policy, which by default is to treat:</span></span>

* <span data-ttu-id="62c86-201">Gli utenti autenticati (che hanno eseguito l'accesso) come autorizzati.</span><span class="sxs-lookup"><span data-stu-id="62c86-201">Authenticated (signed-in) users as authorized.</span></span>
* <span data-ttu-id="62c86-202">Gli utenti non autenticati (disconnessi) come non autorizzati.</span><span class="sxs-lookup"><span data-stu-id="62c86-202">Unauthenticated (signed-out) users as unauthorized.</span></span>

## <a name="customize-unauthorized-content-with-the-router-component"></a><span data-ttu-id="62c86-203">Personalizzare il contenuto non autorizzato con il componente Router</span><span class="sxs-lookup"><span data-stu-id="62c86-203">Customize unauthorized content with the Router component</span></span>

<span data-ttu-id="62c86-204">Il <xref:Microsoft.AspNetCore.Components.Routing.Router> componente, insieme al <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView> componente, consente all'app di specificare contenuto personalizzato se:</span><span class="sxs-lookup"><span data-stu-id="62c86-204">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component, in conjunction with the <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView> component, allows the app to specify custom content if:</span></span>

* <span data-ttu-id="62c86-205">Non viene trovato contenuto.</span><span class="sxs-lookup"><span data-stu-id="62c86-205">Content isn't found.</span></span>
* <span data-ttu-id="62c86-206">L'utente non riesce [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) a applicare una condizione al componente.</span><span class="sxs-lookup"><span data-stu-id="62c86-206">The user fails an [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) condition applied to the component.</span></span> <span data-ttu-id="62c86-207">L' [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attributo viene analizzato nella sezione [ `[Authorize]` attribute](#authorize-attribute) .</span><span class="sxs-lookup"><span data-stu-id="62c86-207">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute is covered in the [`[Authorize]` attribute](#authorize-attribute) section.</span></span>
* <span data-ttu-id="62c86-208">L'autenticazione asincrona è in corso.</span><span class="sxs-lookup"><span data-stu-id="62c86-208">Asynchronous authentication is in progress.</span></span>

<span data-ttu-id="62c86-209">Nel modello di Blazor Server progetto predefinito, il `App` componente ( `App.razor` ) illustra come impostare il contenuto personalizzato:</span><span class="sxs-lookup"><span data-stu-id="62c86-209">In the default Blazor Server project template, the `App` component (`App.razor`) demonstrates how to set custom content:</span></span>

```razor
<CascadingAuthenticationState>
    <Router AppAssembly="@typeof(Program).Assembly">
        <Found Context="routeData">
            <AuthorizeRouteView RouteData="@routeData" 
                DefaultLayout="@typeof(MainLayout)">
                <NotAuthorized>
                    <h1>Sorry</h1>
                    <p>You're not authorized to reach this page.</p>
                    <p>You may need to log in as a different user.</p>
                </NotAuthorized>
                <Authorizing>
                    <h1>Authentication in progress</h1>
                    <p>Only visible while authentication is in progress.</p>
                </Authorizing>
            </AuthorizeRouteView>
        </Found>
        <NotFound>
            <LayoutView Layout="@typeof(MainLayout)">
                <h1>Sorry</h1>
                <p>Sorry, there's nothing at this address.</p>
            </LayoutView>
        </NotFound>
    </Router>
</CascadingAuthenticationState>
```

<span data-ttu-id="62c86-210">Il contenuto dei `<NotFound>` `<NotAuthorized>` tag, e `<Authorizing>` può includere elementi arbitrari, ad esempio altri componenti interattivi.</span><span class="sxs-lookup"><span data-stu-id="62c86-210">The content of `<NotFound>`, `<NotAuthorized>`, and `<Authorizing>` tags can include arbitrary items, such as other interactive components.</span></span>

<span data-ttu-id="62c86-211">Se il `<NotAuthorized>` tag non è specificato, <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView> utilizza il seguente messaggio di fallback:</span><span class="sxs-lookup"><span data-stu-id="62c86-211">If the `<NotAuthorized>` tag isn't specified, the <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView> uses the following fallback message:</span></span>

```html
Not authorized.
```

## <a name="notification-about-authentication-state-changes"></a><span data-ttu-id="62c86-212">Notifica per le modifiche dello stato di autenticazione</span><span class="sxs-lookup"><span data-stu-id="62c86-212">Notification about authentication state changes</span></span>

<span data-ttu-id="62c86-213">Se l'app determina che i dati sullo stato di autenticazione sottostanti sono stati modificati, ad esempio perché l'utente ha effettuato la disconnessione o un altro utente ha modificato i ruoli, un oggetto [personalizzato `AuthenticationStateProvider` ](#implement-a-custom-authenticationstateprovider) può richiamare facoltativamente il metodo <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider.NotifyAuthenticationStateChanged%2A> sulla <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> classe di base.</span><span class="sxs-lookup"><span data-stu-id="62c86-213">If the app determines that the underlying authentication state data has changed (for example, because the user signed out or another user has changed their roles), a [custom `AuthenticationStateProvider`](#implement-a-custom-authenticationstateprovider) can optionally invoke the method <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider.NotifyAuthenticationStateChanged%2A> on the <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> base class.</span></span> <span data-ttu-id="62c86-214">Viene così inviata notifica ai consumer dei dati di stato di autenticazione (ad esempio, <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView>) di eseguire nuovamente il rendering usando i nuovi dati.</span><span class="sxs-lookup"><span data-stu-id="62c86-214">This notifies consumers of the authentication state data (for example, <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView>) to rerender using the new data.</span></span>

## <a name="procedural-logic"></a><span data-ttu-id="62c86-215">Logica procedurale</span><span class="sxs-lookup"><span data-stu-id="62c86-215">Procedural logic</span></span>

<span data-ttu-id="62c86-216">Se l'app è necessaria per verificare le regole di autorizzazione come parte della logica procedurale, usare un parametro a cascata di tipo `Task<` <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState> `>` per ottenere l'utente <xref:System.Security.Claims.ClaimsPrincipal> .</span><span class="sxs-lookup"><span data-stu-id="62c86-216">If the app is required to check authorization rules as part of procedural logic, use a cascaded parameter of type `Task<`<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>`>` to obtain the user's <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="62c86-217">`Task<`<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>`>` può essere combinato con altri servizi, ad esempio `IAuthorizationService` , per valutare i criteri.</span><span class="sxs-lookup"><span data-stu-id="62c86-217">`Task<`<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>`>` can be combined with other services, such as `IAuthorizationService`, to evaluate policies.</span></span>

```razor
@using Microsoft.AspNetCore.Authorization
@inject IAuthorizationService AuthorizationService

<button @onclick="@DoSomething">Do something important</button>

@code {
    [CascadingParameter]
    private Task<AuthenticationState> authenticationStateTask { get; set; }

    private async Task DoSomething()
    {
        var user = (await authenticationStateTask).User;

        if (user.Identity.IsAuthenticated)
        {
            // Perform an action only available to authenticated (signed-in) users.
        }

        if (user.IsInRole("admin"))
        {
            // Perform an action only available to users in the 'admin' role.
        }

        if ((await AuthorizationService.AuthorizeAsync(user, "content-editor"))
            .Succeeded)
        {
            // Perform an action only available to users satisfying the 
            // 'content-editor' policy.
        }
    }
}
```

> [!NOTE]
> <span data-ttu-id="62c86-218">In un Blazor WebAssembly componente dell'app aggiungere gli <xref:Microsoft.AspNetCore.Authorization> <xref:Microsoft.AspNetCore.Components.Authorization> spazi dei nomi e:</span><span class="sxs-lookup"><span data-stu-id="62c86-218">In a Blazor WebAssembly app component, add the <xref:Microsoft.AspNetCore.Authorization> and <xref:Microsoft.AspNetCore.Components.Authorization> namespaces:</span></span>
>
> ```razor
> @using Microsoft.AspNetCore.Authorization
> @using Microsoft.AspNetCore.Components.Authorization
> ```
>
> <span data-ttu-id="62c86-219">Questi spazi dei nomi possono essere forniti a livello globale aggiungendoli al file dell'app `_Imports.razor` .</span><span class="sxs-lookup"><span data-stu-id="62c86-219">These namespaces can be provided globally by adding them to the app's `_Imports.razor` file.</span></span>

## <a name="troubleshoot-errors"></a><span data-ttu-id="62c86-220">Risolvere gli errori</span><span class="sxs-lookup"><span data-stu-id="62c86-220">Troubleshoot errors</span></span>

<span data-ttu-id="62c86-221">Errori comuni:</span><span class="sxs-lookup"><span data-stu-id="62c86-221">Common errors:</span></span>

* <span data-ttu-id="62c86-222">**L'autorizzazione richiede un parametro di propagazione di tipo `Task\<AuthenticationState>` . Prendere `CascadingAuthenticationState` in considerazione l'uso di per fornire questo.**</span><span class="sxs-lookup"><span data-stu-id="62c86-222">**Authorization requires a cascading parameter of type `Task\<AuthenticationState>`. Consider using `CascadingAuthenticationState` to supply this.**</span></span>

* <span data-ttu-id="62c86-223">**`null` valore ricevuto per `authenticationStateTask`**</span><span class="sxs-lookup"><span data-stu-id="62c86-223">**`null` value is received for `authenticationStateTask`**</span></span>

<span data-ttu-id="62c86-224">È probabile che il progetto non sia stato creato usando un Blazor Server modello con autenticazione abilitata.</span><span class="sxs-lookup"><span data-stu-id="62c86-224">It's likely that the project wasn't created using a Blazor Server template with authentication enabled.</span></span> <span data-ttu-id="62c86-225">Eseguire il wrapping di un oggetto intorno a una `<CascadingAuthenticationState>` parte dell'albero dell'interfaccia utente, ad esempio nel `App` componente ( `App.razor` ) come indicato di seguito:</span><span class="sxs-lookup"><span data-stu-id="62c86-225">Wrap a `<CascadingAuthenticationState>` around some part of the UI tree, for example in the `App` component (`App.razor`) as follows:</span></span>

```razor
<CascadingAuthenticationState>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CascadingAuthenticationState>
```

<span data-ttu-id="62c86-226">Fornisce il parametro di propagazione <xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState> `Task<` <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState> `>` , che a sua volta riceve dal <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> servizio di.</span><span class="sxs-lookup"><span data-stu-id="62c86-226">The <xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState> supplies the `Task<`<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>`>` cascading parameter, which in turn it receives from the underlying <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> DI service.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="62c86-227">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="62c86-227">Additional resources</span></span>

* <xref:security/index>
* <xref:security/authentication/windowsauth>
* <span data-ttu-id="62c86-228">[Awesome Blazor :](https://github.com/AdrienTorris/awesome-blazor#authentication) collegamenti di esempio della community di autenticazione</span><span class="sxs-lookup"><span data-stu-id="62c86-228">[Awesome Blazor: Authentication](https://github.com/AdrienTorris/awesome-blazor#authentication) community sample links</span></span>
