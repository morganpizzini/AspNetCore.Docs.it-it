---
title: Autenticazione Blazor e autorizzazione ASP.NET Core
author: guardrex
description: Informazioni sugli Blazor scenari di autenticazione e autorizzazione.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/26/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/index
ms.openlocfilehash: ced8e90147b08bc75aec4534fdd8d8552506f88c
ms.sourcegitcommit: 56861af66bb364a5d60c3c72d133d854b4cf292d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82206099"
---
# <a name="aspnet-core-blazor-authentication-and-authorization"></a><span data-ttu-id="a32c7-103">Autenticazione Blazor e autorizzazione ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a32c7-103">ASP.NET Core Blazor authentication and authorization</span></span>

<span data-ttu-id="a32c7-104">Di [Steve Sanderson](https://github.com/SteveSandersonMS) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="a32c7-104">By [Steve Sanderson](https://github.com/SteveSandersonMS) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

> [!NOTE]
> <span data-ttu-id="a32c7-105">Per le linee guida in questo articolo applicabili a Blazor webassembly, è necessario Blazor il modello di ASP.NET Core webassembly versione 3,2 o successiva.</span><span class="sxs-lookup"><span data-stu-id="a32c7-105">For the guidance in this article that applies to Blazor WebAssembly, the ASP.NET Core Blazor WebAssembly template version 3.2 or later is required.</span></span> <span data-ttu-id="a32c7-106">Se non si usa Visual Studio versione 16,6 Preview 2 o versioni successive, ottenere il Blazor modello webassembly più recente seguendo le istruzioni <xref:blazor/get-started>riportate in.</span><span class="sxs-lookup"><span data-stu-id="a32c7-106">If you aren't using Visual Studio version 16.6 Preview 2 or later, obtain the latest Blazor WebAssembly template by following the guidance in <xref:blazor/get-started>.</span></span>

<span data-ttu-id="a32c7-107">ASP.NET Core supporta la configurazione e la gestione della sicurezza Blazor nelle app.</span><span class="sxs-lookup"><span data-stu-id="a32c7-107">ASP.NET Core supports the configuration and management of security in Blazor apps.</span></span>

<span data-ttu-id="a32c7-108">Gli scenari di sicurezza Blazor sono diversi Blazor tra le app Server e webassembly.</span><span class="sxs-lookup"><span data-stu-id="a32c7-108">Security scenarios differ between Blazor Server and Blazor WebAssembly apps.</span></span> <span data-ttu-id="a32c7-109">Poiché Blazor le app Server vengono eseguite nel server, i controlli delle autorizzazioni sono in grado di determinare:</span><span class="sxs-lookup"><span data-stu-id="a32c7-109">Because Blazor Server apps run on the server, authorization checks are able to determine:</span></span>

* <span data-ttu-id="a32c7-110">Le opzioni dell'interfaccia utente presentate all'utente (ad esempio, le voci di menu disponibili per un utente).</span><span class="sxs-lookup"><span data-stu-id="a32c7-110">The UI options presented to a user (for example, which menu entries are available to a user).</span></span>
* <span data-ttu-id="a32c7-111">Le regole di accesso per le aree dell'app e i componenti.</span><span class="sxs-lookup"><span data-stu-id="a32c7-111">Access rules for areas of the app and components.</span></span>

Blazor<span data-ttu-id="a32c7-112">Le app webassembly vengono eseguite sul client.</span><span class="sxs-lookup"><span data-stu-id="a32c7-112"> WebAssembly apps run on the client.</span></span> <span data-ttu-id="a32c7-113">L'autorizzazione viene usata *solo* per determinare quali opzioni dell'interfaccia utente visualizzare.</span><span class="sxs-lookup"><span data-stu-id="a32c7-113">Authorization is *only* used to determine which UI options to show.</span></span> <span data-ttu-id="a32c7-114">Poiché i controlli lato client possono essere modificati o ignorati da un utente, un' Blazor app webassembly non può applicare le regole di accesso all'autorizzazione.</span><span class="sxs-lookup"><span data-stu-id="a32c7-114">Since client-side checks can be modified or bypassed by a user, a Blazor WebAssembly app can't enforce authorization access rules.</span></span>

<span data-ttu-id="a32c7-115">[Razor Pages le convenzioni di autorizzazione](xref:security/authorization/razor-pages-authorization) non si applicano ai componenti Razor instradabili.</span><span class="sxs-lookup"><span data-stu-id="a32c7-115">[Razor Pages authorization conventions](xref:security/authorization/razor-pages-authorization) don't apply to routable Razor components.</span></span> <span data-ttu-id="a32c7-116">Se un componente Razor non instradabile è [incorporato in una pagina](xref:blazor/integrate-components#render-components-from-a-page-or-view), le convenzioni di autorizzazione della pagina influiscono indirettamente sul componente Razor insieme al resto del contenuto della pagina.</span><span class="sxs-lookup"><span data-stu-id="a32c7-116">If a non-routable Razor component is [embedded in a page](xref:blazor/integrate-components#render-components-from-a-page-or-view), the page's authorization conventions indirectly affect the Razor component along with the rest of the page's content.</span></span>

> [!NOTE]
> <span data-ttu-id="a32c7-117"><xref:Microsoft.AspNetCore.Identity.SignInManager%601>e <xref:Microsoft.AspNetCore.Identity.UserManager%601> non sono supportati nei componenti Razor.</span><span class="sxs-lookup"><span data-stu-id="a32c7-117"><xref:Microsoft.AspNetCore.Identity.SignInManager%601> and <xref:Microsoft.AspNetCore.Identity.UserManager%601> aren't supported in Razor components.</span></span>

## <a name="authentication"></a><span data-ttu-id="a32c7-118">Autenticazione</span><span class="sxs-lookup"><span data-stu-id="a32c7-118">Authentication</span></span>

Blazor<span data-ttu-id="a32c7-119">USA i meccanismi di autenticazione ASP.NET Core esistenti per stabilire l'identità dell'utente.</span><span class="sxs-lookup"><span data-stu-id="a32c7-119"> uses the existing ASP.NET Core authentication mechanisms to establish the user's identity.</span></span> <span data-ttu-id="a32c7-120">Il meccanismo esatto dipende dal modo in cui Blazor l'app è ospitata, Blazor webassembly o Blazor server.</span><span class="sxs-lookup"><span data-stu-id="a32c7-120">The exact mechanism depends on how the Blazor app is hosted, Blazor WebAssembly or Blazor Server.</span></span>

### <a name="blazor-webassembly-authentication"></a>Blazor<span data-ttu-id="a32c7-121">Autenticazione webassembly</span><span class="sxs-lookup"><span data-stu-id="a32c7-121"> WebAssembly authentication</span></span>

<span data-ttu-id="a32c7-122">Nelle Blazor app webassembly i controlli di autenticazione possono essere ignorati perché tutto il codice lato client può essere modificato dagli utenti.</span><span class="sxs-lookup"><span data-stu-id="a32c7-122">In Blazor WebAssembly apps, authentication checks can be bypassed because all client-side code can be modified by users.</span></span> <span data-ttu-id="a32c7-123">Lo stesso vale per tutte le tecnologie per app sul lato client, tra cui i framework JavaScript SPA o le app native per qualsiasi sistema operativo.</span><span class="sxs-lookup"><span data-stu-id="a32c7-123">The same is true for all client-side app technologies, including JavaScript SPA frameworks or native apps for any operating system.</span></span>

<span data-ttu-id="a32c7-124">Aggiungere quanto segue:</span><span class="sxs-lookup"><span data-stu-id="a32c7-124">Add the following:</span></span>

* <span data-ttu-id="a32c7-125">Riferimento al pacchetto per [Microsoft. AspNetCore. Components. Authorization](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Authorization/) per il file di progetto dell'app.</span><span class="sxs-lookup"><span data-stu-id="a32c7-125">A package reference for [Microsoft.AspNetCore.Components.Authorization](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Authorization/) to the app's project file.</span></span>
* <span data-ttu-id="a32c7-126">`Microsoft.AspNetCore.Components.Authorization` Spazio dei nomi del file *_Imports. Razor* dell'app.</span><span class="sxs-lookup"><span data-stu-id="a32c7-126">The `Microsoft.AspNetCore.Components.Authorization` namespace to the app's *_Imports.razor* file.</span></span>

<span data-ttu-id="a32c7-127">Per gestire l'autenticazione, l'implementazione di un servizio incorporato o `AuthenticationStateProvider` personalizzato è illustrata nelle sezioni seguenti.</span><span class="sxs-lookup"><span data-stu-id="a32c7-127">To handle authentication, implementation of a built-in or custom `AuthenticationStateProvider` service is covered in the following sections.</span></span>

<span data-ttu-id="a32c7-128">Per ulteriori informazioni sulla creazione di app e configurazione, <xref:security/blazor/webassembly/index>vedere.</span><span class="sxs-lookup"><span data-stu-id="a32c7-128">For more information on creating apps and configuration, see <xref:security/blazor/webassembly/index>.</span></span>

### <a name="blazor-server-authentication"></a>Blazor<span data-ttu-id="a32c7-129">Autenticazione server</span><span class="sxs-lookup"><span data-stu-id="a32c7-129"> Server authentication</span></span>

Blazor<span data-ttu-id="a32c7-130">Le app server operano su una connessione in tempo reale creata con SignalR.</span><span class="sxs-lookup"><span data-stu-id="a32c7-130"> Server apps operate over a real-time connection that's created using SignalR.</span></span> <span data-ttu-id="a32c7-131">L' [autenticazione SignalRin app basate su](xref:signalr/authn-and-authz) viene gestita quando viene stabilita la connessione.</span><span class="sxs-lookup"><span data-stu-id="a32c7-131">[Authentication in SignalR-based apps](xref:signalr/authn-and-authz) is handled when the connection is established.</span></span> <span data-ttu-id="a32c7-132">L'autenticazione può basarsi su un cookie o su altri bearer token.</span><span class="sxs-lookup"><span data-stu-id="a32c7-132">Authentication can be based on a cookie or some other bearer token.</span></span>

<span data-ttu-id="a32c7-133">Per ulteriori informazioni sulla creazione di app e configurazione, <xref:security/blazor/server/index>vedere.</span><span class="sxs-lookup"><span data-stu-id="a32c7-133">For more information on creating apps and configuration, see <xref:security/blazor/server/index>.</span></span>

## <a name="authenticationstateprovider-service"></a><span data-ttu-id="a32c7-134">Servizio AuthenticationStateProvider</span><span class="sxs-lookup"><span data-stu-id="a32c7-134">AuthenticationStateProvider service</span></span>

<span data-ttu-id="a32c7-135">Il `AuthenticationStateProvider` servizio incorporato ottiene i dati sullo stato di autenticazione da ASP.NET Core `HttpContext.User`.</span><span class="sxs-lookup"><span data-stu-id="a32c7-135">The built-in `AuthenticationStateProvider` service obtains authentication state data from ASP.NET Core's `HttpContext.User`.</span></span> <span data-ttu-id="a32c7-136">Questo è il modo in cui lo stato di autenticazione si integra con i meccanismi di autenticazione ASP.NET Core esistenti.</span><span class="sxs-lookup"><span data-stu-id="a32c7-136">This is how authentication state integrates with existing ASP.NET Core authentication mechanisms.</span></span>

<span data-ttu-id="a32c7-137">`AuthenticationStateProvider` è il servizio sottostante usato dal componente `AuthorizeView` e dal componente `CascadingAuthenticationState` per ottenere lo stato di autenticazione.</span><span class="sxs-lookup"><span data-stu-id="a32c7-137">`AuthenticationStateProvider` is the underlying service used by the `AuthorizeView` component and `CascadingAuthenticationState` component to get the authentication state.</span></span>

<span data-ttu-id="a32c7-138">In genere non si usa `AuthenticationStateProvider` direttamente.</span><span class="sxs-lookup"><span data-stu-id="a32c7-138">You don't typically use `AuthenticationStateProvider` directly.</span></span> <span data-ttu-id="a32c7-139">Usare il [componente AuthorizeView](#authorizeview-component) o [l'\<attività AuthenticationState>](#expose-the-authentication-state-as-a-cascading-parameter) gli approcci descritti più avanti in questo articolo.</span><span class="sxs-lookup"><span data-stu-id="a32c7-139">Use the [AuthorizeView component](#authorizeview-component) or [Task\<AuthenticationState>](#expose-the-authentication-state-as-a-cascading-parameter) approaches described later in this article.</span></span> <span data-ttu-id="a32c7-140">Lo svantaggio principale dell'uso diretto di `AuthenticationStateProvider` è che il componente non riceve alcuna notifica automaticamente se i dati relativi allo stato di autenticazione sottostanti cambiano.</span><span class="sxs-lookup"><span data-stu-id="a32c7-140">The main drawback to using `AuthenticationStateProvider` directly is that the component isn't notified automatically if the underlying authentication state data changes.</span></span>

<span data-ttu-id="a32c7-141">Il servizio `AuthenticationStateProvider` può fornire i dati <xref:System.Security.Claims.ClaimsPrincipal> dell'utente corrente, come illustrato nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="a32c7-141">The `AuthenticationStateProvider` service can provide the current user's <xref:System.Security.Claims.ClaimsPrincipal> data, as shown in the following example:</span></span>

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
            <li>@claim.Type &ndash; @claim.Value</li>
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

<span data-ttu-id="a32c7-142">Se `user.Identity.IsAuthenticated` è `true` e perché l'utente è un <xref:System.Security.Claims.ClaimsPrincipal>, è possibile enumerare le attestazioni e valutare l'appartenenza ai ruoli.</span><span class="sxs-lookup"><span data-stu-id="a32c7-142">If `user.Identity.IsAuthenticated` is `true` and because the user is a <xref:System.Security.Claims.ClaimsPrincipal>, claims can be enumerated and membership in roles evaluated.</span></span>

<span data-ttu-id="a32c7-143">Per altre informazioni sull'inserimento delle dipendenze e sui servizi, vedere <xref:blazor/dependency-injection> e <xref:fundamentals/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="a32c7-143">For more information on dependency injection (DI) and services, see <xref:blazor/dependency-injection> and <xref:fundamentals/dependency-injection>.</span></span>

## <a name="implement-a-custom-authenticationstateprovider"></a><span data-ttu-id="a32c7-144">Implementare un AuthenticationStateProvider personalizzato</span><span class="sxs-lookup"><span data-stu-id="a32c7-144">Implement a custom AuthenticationStateProvider</span></span>

<span data-ttu-id="a32c7-145">Se l'app richiede un provider personalizzato, implementare `AuthenticationStateProvider` ed eseguire `GetAuthenticationStateAsync`l'override di:</span><span class="sxs-lookup"><span data-stu-id="a32c7-145">If the app requires a custom provider, implement `AuthenticationStateProvider` and override `GetAuthenticationStateAsync`:</span></span>

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

<span data-ttu-id="a32c7-146">In un' Blazor app webassembly il `CustomAuthStateProvider` servizio è registrato in `Main` di *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="a32c7-146">In a Blazor WebAssembly app, the `CustomAuthStateProvider` service is registered in `Main` of *Program.cs*:</span></span>

```csharp
using Microsoft.AspNetCore.Components.Authorization;

...

builder.Services.AddScoped<AuthenticationStateProvider, CustomAuthStateProvider>();
```

<span data-ttu-id="a32c7-147">In un' Blazor app Server, il `CustomAuthStateProvider` servizio è registrato in `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="a32c7-147">In a Blazor Server app, the `CustomAuthStateProvider` service is registered in `Startup.ConfigureServices`:</span></span>

```csharp
using Microsoft.AspNetCore.Components.Authorization;

...

services.AddScoped<AuthenticationStateProvider, CustomAuthStateProvider>();
```

<span data-ttu-id="a32c7-148">Utilizzando l' `CustomAuthStateProvider` oggetto nell'esempio precedente, tutti gli utenti vengono autenticati con il `mrfibuli`nome utente.</span><span class="sxs-lookup"><span data-stu-id="a32c7-148">Using the `CustomAuthStateProvider` in the preceding example, all users are authenticated with the username `mrfibuli`.</span></span>

## <a name="expose-the-authentication-state-as-a-cascading-parameter"></a><span data-ttu-id="a32c7-149">Esporre lo stato di autenticazione come un parametro a catena</span><span class="sxs-lookup"><span data-stu-id="a32c7-149">Expose the authentication state as a cascading parameter</span></span>

<span data-ttu-id="a32c7-150">Se i dati dello stato di autenticazione sono necessari per la logica procedurale, ad esempio quando si esegue un'azione attivata dall'utente, ottenere i dati dello stato di autenticazione definendo un parametro a catena di tipo `Task<AuthenticationState>`:</span><span class="sxs-lookup"><span data-stu-id="a32c7-150">If authentication state data is required for procedural logic, such as when performing an action triggered by the user, obtain the authentication state data by defining a cascading parameter of type `Task<AuthenticationState>`:</span></span>

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

<span data-ttu-id="a32c7-151">Se `user.Identity.IsAuthenticated` è `true`, è possibile enumerare le attestazioni e valutare l'appartenenza ai ruoli.</span><span class="sxs-lookup"><span data-stu-id="a32c7-151">If `user.Identity.IsAuthenticated` is `true`, claims can be enumerated and membership in roles evaluated.</span></span>

<span data-ttu-id="a32c7-152">`Task<AuthenticationState>` Configurare il parametro di propagazione usando `AuthorizeRouteView` i `CascadingAuthenticationState` componenti e nel `App` componente (*app. Razor*):</span><span class="sxs-lookup"><span data-stu-id="a32c7-152">Set up the `Task<AuthenticationState>` cascading parameter using the `AuthorizeRouteView` and `CascadingAuthenticationState` components in the `App` component (*App.razor*):</span></span>

```razor
<Router AppAssembly="@typeof(Program).Assembly">
    <Found Context="routeData">
        <AuthorizeRouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
    </Found>
    <NotFound>
        <CascadingAuthenticationState>
            <LayoutView Layout="@typeof(MainLayout)">
                <p>Sorry, there's nothing at this address.</p>
            </LayoutView>
        </CascadingAuthenticationState>
    </NotFound>
</Router>
```

<span data-ttu-id="a32c7-153">In un' Blazor app webassembly aggiungere servizi per le opzioni e l'autorizzazione `Program.Main`a:</span><span class="sxs-lookup"><span data-stu-id="a32c7-153">In a Blazor WebAssembly App, add services for options and authorization to `Program.Main`:</span></span>

```csharp
builder.Services.AddOptions();
builder.Services.AddAuthorizationCore();
```

<span data-ttu-id="a32c7-154">In un' Blazor app Server i servizi per le opzioni e l'autorizzazione sono già presenti, quindi non sono necessarie ulteriori azioni.</span><span class="sxs-lookup"><span data-stu-id="a32c7-154">In a Blazor Server app, services for options and authorization are already present, so no further action is required.</span></span>

## <a name="authorization"></a><span data-ttu-id="a32c7-155">Autorizzazione</span><span class="sxs-lookup"><span data-stu-id="a32c7-155">Authorization</span></span>

<span data-ttu-id="a32c7-156">Dopo l'autenticazione di un utente, vengono applicate le regole di *autorizzazione* per controllare le operazioni consentite per un utente.</span><span class="sxs-lookup"><span data-stu-id="a32c7-156">After a user is authenticated, *authorization* rules are applied to control what the user can do.</span></span>

<span data-ttu-id="a32c7-157">L'accesso viene in genere concesso o negato in base alle condizioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="a32c7-157">Access is typically granted or denied based on whether:</span></span>

* <span data-ttu-id="a32c7-158">L'utente è autenticato (ha eseguito l'accesso).</span><span class="sxs-lookup"><span data-stu-id="a32c7-158">A user is authenticated (signed in).</span></span>
* <span data-ttu-id="a32c7-159">L'utente è incluso in un *ruolo*.</span><span class="sxs-lookup"><span data-stu-id="a32c7-159">A user is in a *role*.</span></span>
* <span data-ttu-id="a32c7-160">L'utente ha un'*attestazione*.</span><span class="sxs-lookup"><span data-stu-id="a32c7-160">A user has a *claim*.</span></span>
* <span data-ttu-id="a32c7-161">I *criteri* sono soddisfatti.</span><span class="sxs-lookup"><span data-stu-id="a32c7-161">A *policy* is satisfied.</span></span>

<span data-ttu-id="a32c7-162">Questi concetti sono uguali a quelli validi per un'app ASP.NET Core MVC o Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="a32c7-162">Each of these concepts is the same as in an ASP.NET Core MVC or Razor Pages app.</span></span> <span data-ttu-id="a32c7-163">Per altre informazioni sulla sicurezza di ASP.NET Core, vedere gli articoli in [Sicurezza e identità per ASP.NET Core](xref:security/index).</span><span class="sxs-lookup"><span data-stu-id="a32c7-163">For more information on ASP.NET Core security, see the articles under [ASP.NET Core Security and Identity](xref:security/index).</span></span>

## <a name="authorizeview-component"></a><span data-ttu-id="a32c7-164">Componente AuthorizeView</span><span class="sxs-lookup"><span data-stu-id="a32c7-164">AuthorizeView component</span></span>

<span data-ttu-id="a32c7-165">Il componente `AuthorizeView` visualizza in modo selettivo l'interfaccia utente a seconda del fatto che l'utente sia autorizzato a visualizzarla.</span><span class="sxs-lookup"><span data-stu-id="a32c7-165">The `AuthorizeView` component selectively displays UI depending on whether the user is authorized to see it.</span></span> <span data-ttu-id="a32c7-166">Questo approccio è utile quando è necessario solo *visualizzare* dati per l'utente e non occorre usare l'identità dell'utente nella logica procedurale.</span><span class="sxs-lookup"><span data-stu-id="a32c7-166">This approach is useful when you only need to *display* data for the user and don't need to use the user's identity in procedural logic.</span></span>

<span data-ttu-id="a32c7-167">Il componente espone una variabile `context` di tipo `AuthenticationState`, che è possibile usare per accedere alle informazioni sull'utente connesso:</span><span class="sxs-lookup"><span data-stu-id="a32c7-167">The component exposes a `context` variable of type `AuthenticationState`, which you can use to access information about the signed-in user:</span></span>

```razor
<AuthorizeView>
    <h1>Hello, @context.User.Identity.Name!</h1>
    <p>You can only see this content if you're authenticated.</p>
</AuthorizeView>
```

<span data-ttu-id="a32c7-168">Se l'utente non è autenticato, è anche possibile fornire un contenuto diverso per la visualizzazione:</span><span class="sxs-lookup"><span data-stu-id="a32c7-168">You can also supply different content for display if the user isn't authenticated:</span></span>

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

<span data-ttu-id="a32c7-169">Il `AuthorizeView` componente può essere usato nel `NavMenu` componente (*Shared/NavMenu. Razor*) per visualizzare un elemento elenco (`<li>...</li>`) per un `NavLink`, ma si noti che questo approccio rimuove solo l'elemento dell'elenco dall'output sottoposto a rendering.</span><span class="sxs-lookup"><span data-stu-id="a32c7-169">The `AuthorizeView` component can be used in the `NavMenu` component (*Shared/NavMenu.razor*) to display a list item (`<li>...</li>`) for a `NavLink`, but note that this approach only removes the list item from the rendered output.</span></span> <span data-ttu-id="a32c7-170">Non impedisce all'utente di spostarsi nel componente.</span><span class="sxs-lookup"><span data-stu-id="a32c7-170">It doesn't prevent the user from navigating to the component.</span></span>

<span data-ttu-id="a32c7-171">Il contenuto dei `<Authorized>` tag `<NotAuthorized>` e può includere elementi arbitrari, ad esempio altri componenti interattivi.</span><span class="sxs-lookup"><span data-stu-id="a32c7-171">The content of `<Authorized>` and `<NotAuthorized>` tags can include arbitrary items, such as other interactive components.</span></span>

<span data-ttu-id="a32c7-172">Le condizioni di autorizzazione, ad esempio i ruoli o i criteri che consentono di controllare le opzioni dell'interfaccia utente o l'accesso, sono presentate nella sezione [Autorizzazione](#authorization).</span><span class="sxs-lookup"><span data-stu-id="a32c7-172">Authorization conditions, such as roles or policies that control UI options or access, are covered in the [Authorization](#authorization) section.</span></span>

<span data-ttu-id="a32c7-173">Se non sono specificate condizioni di autorizzazione, `AuthorizeView` usa criteri predefiniti e considera:</span><span class="sxs-lookup"><span data-stu-id="a32c7-173">If authorization conditions aren't specified, `AuthorizeView` uses a default policy and treats:</span></span>

* <span data-ttu-id="a32c7-174">Gli utenti autenticati (che hanno eseguito l'accesso) come autorizzati.</span><span class="sxs-lookup"><span data-stu-id="a32c7-174">Authenticated (signed-in) users as authorized.</span></span>
* <span data-ttu-id="a32c7-175">Gli utenti non autenticati (disconnessi) come non autorizzati.</span><span class="sxs-lookup"><span data-stu-id="a32c7-175">Unauthenticated (signed-out) users as unauthorized.</span></span>

### <a name="role-based-and-policy-based-authorization"></a><span data-ttu-id="a32c7-176">Autorizzazione basata sui ruoli e basata sui criteri</span><span class="sxs-lookup"><span data-stu-id="a32c7-176">Role-based and policy-based authorization</span></span>

<span data-ttu-id="a32c7-177">Il componente `AuthorizeView` supporta l'autorizzazione *basata sui ruoli* oppure *basata sui criteri*.</span><span class="sxs-lookup"><span data-stu-id="a32c7-177">The `AuthorizeView` component supports *role-based* or *policy-based* authorization.</span></span>

<span data-ttu-id="a32c7-178">Per l'autorizzazione basata sui ruoli, usare il parametro `Roles`:</span><span class="sxs-lookup"><span data-stu-id="a32c7-178">For role-based authorization, use the `Roles` parameter:</span></span>

```razor
<AuthorizeView Roles="admin, superuser">
    <p>You can only see this if you're an admin or superuser.</p>
</AuthorizeView>
```

<span data-ttu-id="a32c7-179">Per altre informazioni, vedere <xref:security/authorization/roles>.</span><span class="sxs-lookup"><span data-stu-id="a32c7-179">For more information, see <xref:security/authorization/roles>.</span></span>

<span data-ttu-id="a32c7-180">Per l'autorizzazione basata sui criteri, usare il parametro `Policy`:</span><span class="sxs-lookup"><span data-stu-id="a32c7-180">For policy-based authorization, use the `Policy` parameter:</span></span>

```razor
<AuthorizeView Policy="content-editor">
    <p>You can only see this if you satisfy the "content-editor" policy.</p>
</AuthorizeView>
```

<span data-ttu-id="a32c7-181">L'autorizzazione basata sulle attestazioni è un caso speciale di autorizzazione basata su criteri.</span><span class="sxs-lookup"><span data-stu-id="a32c7-181">Claims-based authorization is a special case of policy-based authorization.</span></span> <span data-ttu-id="a32c7-182">Ad esempio, è possibile definire un criterio che richiede che gli utenti abbiano una determinata attestazione.</span><span class="sxs-lookup"><span data-stu-id="a32c7-182">For example, you can define a policy that requires users to have a certain claim.</span></span> <span data-ttu-id="a32c7-183">Per altre informazioni, vedere <xref:security/authorization/policies>.</span><span class="sxs-lookup"><span data-stu-id="a32c7-183">For more information, see <xref:security/authorization/policies>.</span></span>

<span data-ttu-id="a32c7-184">Queste API possono essere usate nelle app Blazor server o Blazor webassembly.</span><span class="sxs-lookup"><span data-stu-id="a32c7-184">These APIs can be used in either Blazor Server or Blazor WebAssembly apps.</span></span>

<span data-ttu-id="a32c7-185">Se non si specifica `Roles` o `Policy`, `AuthorizeView` usa i criteri predefiniti.</span><span class="sxs-lookup"><span data-stu-id="a32c7-185">If neither `Roles` nor `Policy` is specified, `AuthorizeView` uses the default policy.</span></span>

### <a name="content-displayed-during-asynchronous-authentication"></a><span data-ttu-id="a32c7-186">Contenuto visualizzato durante l'autenticazione asincrona</span><span class="sxs-lookup"><span data-stu-id="a32c7-186">Content displayed during asynchronous authentication</span></span>

Blazor<span data-ttu-id="a32c7-187">consente di determinare lo stato di autenticazione in *modo asincrono*.</span><span class="sxs-lookup"><span data-stu-id="a32c7-187"> allows for authentication state to be determined *asynchronously*.</span></span> <span data-ttu-id="a32c7-188">Lo scenario principale per questo approccio è nelle Blazor app webassembly che effettuano una richiesta a un endpoint esterno per l'autenticazione.</span><span class="sxs-lookup"><span data-stu-id="a32c7-188">The primary scenario for this approach is in Blazor WebAssembly apps that make a request to an external endpoint for authentication.</span></span>

<span data-ttu-id="a32c7-189">Mentre è in corso l'autenticazione `AuthorizeView` non visualizza alcun contenuto per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="a32c7-189">While authentication is in progress, `AuthorizeView` displays no content by default.</span></span> <span data-ttu-id="a32c7-190">Per visualizzare contenuto durante l'autenticazione, usare l'elemento `<Authorizing>`:</span><span class="sxs-lookup"><span data-stu-id="a32c7-190">To display content while authentication occurs, use the `<Authorizing>` element:</span></span>

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

<span data-ttu-id="a32c7-191">Questo approccio non è in genere Blazor applicabile alle app Server.</span><span class="sxs-lookup"><span data-stu-id="a32c7-191">This approach isn't normally applicable to Blazor Server apps.</span></span> Blazor<span data-ttu-id="a32c7-192">Le app server conoscono lo stato di autenticazione non appena viene stabilito lo stato.</span><span class="sxs-lookup"><span data-stu-id="a32c7-192"> Server apps know the authentication state as soon as the state is established.</span></span> <span data-ttu-id="a32c7-193">`Authorizing`il contenuto può essere fornito nel Blazor componente di `AuthorizeView` un'app Server, ma il contenuto non viene mai visualizzato.</span><span class="sxs-lookup"><span data-stu-id="a32c7-193">`Authorizing` content can be provided in a Blazor Server app's `AuthorizeView` component, but the content is never displayed.</span></span>

## <a name="authorize-attribute"></a><span data-ttu-id="a32c7-194">Attributo [Authorize]</span><span class="sxs-lookup"><span data-stu-id="a32c7-194">[Authorize] attribute</span></span>

<span data-ttu-id="a32c7-195">L' `[Authorize]` attributo può essere usato nei componenti Razor:</span><span class="sxs-lookup"><span data-stu-id="a32c7-195">The `[Authorize]` attribute can be used in Razor components:</span></span>

```razor
@page "/"
@attribute [Authorize]

You can only see this if you're signed in.
```

> [!IMPORTANT]
> <span data-ttu-id="a32c7-196">Usare `[Authorize]` solo sui `@page` componenti raggiunti tramite Blazor il router.</span><span class="sxs-lookup"><span data-stu-id="a32c7-196">Only use `[Authorize]` on `@page` components reached via the Blazor Router.</span></span> <span data-ttu-id="a32c7-197">L'autorizzazione viene eseguita solo come un aspetto del routing e *non* per i componenti figlio di cui viene eseguito il rendering all'interno di una pagina.</span><span class="sxs-lookup"><span data-stu-id="a32c7-197">Authorization is only performed as an aspect of routing and *not* for child components rendered within a page.</span></span> <span data-ttu-id="a32c7-198">Per autorizzare la visualizzazione di parti specifiche all'interno di una pagina, usare invece `AuthorizeView`.</span><span class="sxs-lookup"><span data-stu-id="a32c7-198">To authorize the display of specific parts within a page, use `AuthorizeView` instead.</span></span>

<span data-ttu-id="a32c7-199">L'attributo `[Authorize]` supporta anche l'autorizzazione basata sui ruoli o basata sui criteri.</span><span class="sxs-lookup"><span data-stu-id="a32c7-199">The `[Authorize]` attribute also supports role-based or policy-based authorization.</span></span> <span data-ttu-id="a32c7-200">Per l'autorizzazione basata sui ruoli, usare il parametro `Roles`:</span><span class="sxs-lookup"><span data-stu-id="a32c7-200">For role-based authorization, use the `Roles` parameter:</span></span>

```razor
@page "/"
@attribute [Authorize(Roles = "admin, superuser")]

<p>You can only see this if you're in the 'admin' or 'superuser' role.</p>
```

<span data-ttu-id="a32c7-201">Per l'autorizzazione basata sui criteri, usare il parametro `Policy`:</span><span class="sxs-lookup"><span data-stu-id="a32c7-201">For policy-based authorization, use the `Policy` parameter:</span></span>

```razor
@page "/"
@attribute [Authorize(Policy = "content-editor")]

<p>You can only see this if you satisfy the 'content-editor' policy.</p>
```

<span data-ttu-id="a32c7-202">Se non si specifica `Roles` o `Policy`, `[Authorize]` usa i criteri predefiniti, che per impostazione predefinita considerano:</span><span class="sxs-lookup"><span data-stu-id="a32c7-202">If neither `Roles` nor `Policy` is specified, `[Authorize]` uses the default policy, which by default is to treat:</span></span>

* <span data-ttu-id="a32c7-203">Gli utenti autenticati (che hanno eseguito l'accesso) come autorizzati.</span><span class="sxs-lookup"><span data-stu-id="a32c7-203">Authenticated (signed-in) users as authorized.</span></span>
* <span data-ttu-id="a32c7-204">Gli utenti non autenticati (disconnessi) come non autorizzati.</span><span class="sxs-lookup"><span data-stu-id="a32c7-204">Unauthenticated (signed-out) users as unauthorized.</span></span>

## <a name="customize-unauthorized-content-with-the-router-component"></a><span data-ttu-id="a32c7-205">Personalizzare il contenuto non autorizzato con il componente Router</span><span class="sxs-lookup"><span data-stu-id="a32c7-205">Customize unauthorized content with the Router component</span></span>

<span data-ttu-id="a32c7-206">Il `Router` componente, insieme al `AuthorizeRouteView` componente, consente all'app di specificare contenuto personalizzato se:</span><span class="sxs-lookup"><span data-stu-id="a32c7-206">The `Router` component, in conjunction with the `AuthorizeRouteView` component, allows the app to specify custom content if:</span></span>

* <span data-ttu-id="a32c7-207">Non viene trovato contenuto.</span><span class="sxs-lookup"><span data-stu-id="a32c7-207">Content isn't found.</span></span>
* <span data-ttu-id="a32c7-208">L'utente non supera una condizione `[Authorize]` applicata al componente.</span><span class="sxs-lookup"><span data-stu-id="a32c7-208">The user fails an `[Authorize]` condition applied to the component.</span></span> <span data-ttu-id="a32c7-209">L' `[Authorize]` attributo viene analizzato nella sezione [ `[Authorize]` attribute](#authorize-attribute) .</span><span class="sxs-lookup"><span data-stu-id="a32c7-209">The `[Authorize]` attribute is covered in the [`[Authorize]` attribute](#authorize-attribute) section.</span></span>
* <span data-ttu-id="a32c7-210">L'autenticazione asincrona è in corso.</span><span class="sxs-lookup"><span data-stu-id="a32c7-210">Asynchronous authentication is in progress.</span></span>

<span data-ttu-id="a32c7-211">Nel modello di Blazor progetto server predefinito, il `App` componente (*app. Razor*) illustra come impostare il contenuto personalizzato:</span><span class="sxs-lookup"><span data-stu-id="a32c7-211">In the default Blazor Server project template, the `App` component (*App.razor*) demonstrates how to set custom content:</span></span>

```razor
<Router AppAssembly="@typeof(Program).Assembly">
    <Found Context="routeData">
        <AuthorizeRouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)">
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
        <CascadingAuthenticationState>
            <LayoutView Layout="@typeof(MainLayout)">
                <h1>Sorry</h1>
                <p>Sorry, there's nothing at this address.</p>
            </LayoutView>
        </CascadingAuthenticationState>
    </NotFound>
</Router>
```

<span data-ttu-id="a32c7-212">Il contenuto dei `<NotFound>`tag `<NotAuthorized>`, e `<Authorizing>` può includere elementi arbitrari, ad esempio altri componenti interattivi.</span><span class="sxs-lookup"><span data-stu-id="a32c7-212">The content of `<NotFound>`, `<NotAuthorized>`, and `<Authorizing>` tags can include arbitrary items, such as other interactive components.</span></span>

<span data-ttu-id="a32c7-213">Se l' `<NotAuthorized>` elemento non è specificato, `AuthorizeRouteView` utilizza il seguente messaggio di fallback:</span><span class="sxs-lookup"><span data-stu-id="a32c7-213">If the `<NotAuthorized>` element isn't specified, the `AuthorizeRouteView` uses the following fallback message:</span></span>

```html
Not authorized.
```

## <a name="notification-about-authentication-state-changes"></a><span data-ttu-id="a32c7-214">Notifica per le modifiche dello stato di autenticazione</span><span class="sxs-lookup"><span data-stu-id="a32c7-214">Notification about authentication state changes</span></span>

<span data-ttu-id="a32c7-215">Se l'app determina che i dati sullo stato di autenticazione sottostanti sono stati modificati, ad esempio perché l'utente disconnesso o un altro utente ha modificato i ruoli, un [AuthenticationStateProvider personalizzato](#implement-a-custom-authenticationstateprovider) può richiamare facoltativamente `NotifyAuthenticationStateChanged` il `AuthenticationStateProvider` metodo sulla classe di base.</span><span class="sxs-lookup"><span data-stu-id="a32c7-215">If the app determines that the underlying authentication state data has changed (for example, because the user signed out or another user has changed their roles), a [custom AuthenticationStateProvider](#implement-a-custom-authenticationstateprovider) can optionally invoke the method `NotifyAuthenticationStateChanged` on the `AuthenticationStateProvider` base class.</span></span> <span data-ttu-id="a32c7-216">Viene così inviata notifica ai consumer dei dati di stato di autenticazione (ad esempio, `AuthorizeView`) di eseguire nuovamente il rendering usando i nuovi dati.</span><span class="sxs-lookup"><span data-stu-id="a32c7-216">This notifies consumers of the authentication state data (for example, `AuthorizeView`) to rerender using the new data.</span></span>

## <a name="procedural-logic"></a><span data-ttu-id="a32c7-217">Logica procedurale</span><span class="sxs-lookup"><span data-stu-id="a32c7-217">Procedural logic</span></span>

<span data-ttu-id="a32c7-218">Se l'app deve controllare le regole di autorizzazione come parte della logica procedurale, usare un parametro a catena di tipo `Task<AuthenticationState>` per ottenere il <xref:System.Security.Claims.ClaimsPrincipal> dell'utente.</span><span class="sxs-lookup"><span data-stu-id="a32c7-218">If the app is required to check authorization rules as part of procedural logic, use a cascaded parameter of type `Task<AuthenticationState>` to obtain the user's <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="a32c7-219">`Task<AuthenticationState>` può essere combinato con altri servizi, ad esempio `IAuthorizationService`, per valutare i criteri.</span><span class="sxs-lookup"><span data-stu-id="a32c7-219">`Task<AuthenticationState>` can be combined with other services, such as `IAuthorizationService`, to evaluate policies.</span></span>

```razor
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
> <span data-ttu-id="a32c7-220">In un Blazor componente dell'app webassembly aggiungere gli `Microsoft.AspNetCore.Authorization` spazi `Microsoft.AspNetCore.Components.Authorization` dei nomi e:</span><span class="sxs-lookup"><span data-stu-id="a32c7-220">In a Blazor WebAssembly app component, add the `Microsoft.AspNetCore.Authorization` and `Microsoft.AspNetCore.Components.Authorization` namespaces:</span></span>
>
> ```razor
> @using Microsoft.AspNetCore.Authorization
> @using Microsoft.AspNetCore.Components.Authorization
> ```
>
> <span data-ttu-id="a32c7-221">Questi spazi dei nomi possono essere forniti a livello globale aggiungendoli al file *_Imports. Razor* dell'app.</span><span class="sxs-lookup"><span data-stu-id="a32c7-221">These namespaces can be provided globally by adding them to the app's *_Imports.razor* file.</span></span>

## <a name="authorization-in-blazor-webassembly-apps"></a><span data-ttu-id="a32c7-222">Autorizzazione nelle Blazor app webassembly</span><span class="sxs-lookup"><span data-stu-id="a32c7-222">Authorization in Blazor WebAssembly apps</span></span>

<span data-ttu-id="a32c7-223">Nelle Blazor app webassembly i controlli di autorizzazione possono essere ignorati perché tutto il codice lato client può essere modificato dagli utenti.</span><span class="sxs-lookup"><span data-stu-id="a32c7-223">In Blazor WebAssembly apps, authorization checks can be bypassed because all client-side code can be modified by users.</span></span> <span data-ttu-id="a32c7-224">Lo stesso vale per tutte le tecnologie per app sul lato client, tra cui i framework JavaScript SPA o le app native per qualsiasi sistema operativo.</span><span class="sxs-lookup"><span data-stu-id="a32c7-224">The same is true for all client-side app technologies, including JavaScript SPA frameworks or native apps for any operating system.</span></span>

<span data-ttu-id="a32c7-225">**Eseguire sempre i controlli di autorizzazione nel server all'interno degli eventuali endpoint dell'API a cui accede l'app sul lato client.**</span><span class="sxs-lookup"><span data-stu-id="a32c7-225">**Always perform authorization checks on the server within any API endpoints accessed by your client-side app.**</span></span>

<span data-ttu-id="a32c7-226">Per ulteriori informazioni, vedere gli articoli in <xref:security/blazor/webassembly/index>.</span><span class="sxs-lookup"><span data-stu-id="a32c7-226">For more information, see the articles under <xref:security/blazor/webassembly/index>.</span></span>

## <a name="troubleshoot-errors"></a><span data-ttu-id="a32c7-227">Risolvere gli errori</span><span class="sxs-lookup"><span data-stu-id="a32c7-227">Troubleshoot errors</span></span>

<span data-ttu-id="a32c7-228">Errori comuni:</span><span class="sxs-lookup"><span data-stu-id="a32c7-228">Common errors:</span></span>

* <span data-ttu-id="a32c7-229">**Per l'autorizzazione è necessario un parametro di propagazione di tipo Task\<AuthenticationState>. Per fornire questo, provare a usare CascadingAuthenticationState.**</span><span class="sxs-lookup"><span data-stu-id="a32c7-229">**Authorization requires a cascading parameter of type Task\<AuthenticationState>. Consider using CascadingAuthenticationState to supply this.**</span></span>

* <span data-ttu-id="a32c7-230">**`null`valore ricevuto per`authenticationStateTask`**</span><span class="sxs-lookup"><span data-stu-id="a32c7-230">**`null` value is received for `authenticationStateTask`**</span></span>

<span data-ttu-id="a32c7-231">È probabile che il progetto non sia stato creato usando Blazor un modello server con autenticazione abilitata.</span><span class="sxs-lookup"><span data-stu-id="a32c7-231">It's likely that the project wasn't created using a Blazor Server template with authentication enabled.</span></span> <span data-ttu-id="a32c7-232">Eseguire il `<CascadingAuthenticationState>` wrapping di una parte della struttura ad albero dell'interfaccia utente, `App` ad esempio nel componente (*app. Razor*) come indicato di seguito:</span><span class="sxs-lookup"><span data-stu-id="a32c7-232">Wrap a `<CascadingAuthenticationState>` around some part of the UI tree, for example in the `App` component (*App.razor*) as follows:</span></span>

```razor
<CascadingAuthenticationState>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CascadingAuthenticationState>
```

<span data-ttu-id="a32c7-233">`CascadingAuthenticationState` fornisce il parametro a catena `Task<AuthenticationState>`, ricevuto a sua volta dal servizio di inserimento delle dipendenze `AuthenticationStateProvider` sottostante.</span><span class="sxs-lookup"><span data-stu-id="a32c7-233">The `CascadingAuthenticationState` supplies the `Task<AuthenticationState>` cascading parameter, which in turn it receives from the underlying `AuthenticationStateProvider` DI service.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a32c7-234">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="a32c7-234">Additional resources</span></span>

* <xref:security/index>
* <xref:security/authentication/windowsauth>
* <span data-ttu-id="a32c7-235">[Awesome Blazor:](https://github.com/AdrienTorris/awesome-blazor#authentication) collegamenti di esempio della community di autenticazione</span><span class="sxs-lookup"><span data-stu-id="a32c7-235">[Awesome Blazor: Authentication](https://github.com/AdrienTorris/awesome-blazor#authentication) community sample links</span></span>
