---
title: Proteggere le Blazor app di ASP.NET Core ServerSecure ASP.NET Core Server apps
author: guardrex
description: Informazioni su come ridurre le Blazor minacce alla sicurezza per le app server.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/02/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/server
ms.openlocfilehash: bd03f811d0425fdfdb7bbbc24fea5481b49b8ed3
ms.sourcegitcommit: 9675db7bf4b67ae269f9226b6f6f439b5cce4603
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80626014"
---
# <a name="secure-aspnet-core-blazor-server-apps"></a><span data-ttu-id="30ffe-103">Proteggere ASP.NET le app di core Blazor Server</span><span class="sxs-lookup"><span data-stu-id="30ffe-103">Secure ASP.NET Core Blazor Server apps</span></span>

<span data-ttu-id="30ffe-104">Di [Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="30ffe-104">By [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

<span data-ttu-id="30ffe-105">Le app Blazor Server adottano un modello di elaborazione dei dati *con stato,* in cui il server e il client mantengono una relazione di lunga durata.</span><span class="sxs-lookup"><span data-stu-id="30ffe-105">Blazor Server apps adopt a *stateful* data processing model, where the server and client maintain a long-lived relationship.</span></span> <span data-ttu-id="30ffe-106">Lo stato persistente viene mantenuto da un [circuito](xref:blazor/state-management), che può estendersi su connessioni potenzialmente longeve.</span><span class="sxs-lookup"><span data-stu-id="30ffe-106">The persistent state is maintained by a [circuit](xref:blazor/state-management), which can span connections that are also potentially long-lived.</span></span>

<span data-ttu-id="30ffe-107">Quando un utente visita un sito Blazor Server, il server crea un circuito nella memoria del server.</span><span class="sxs-lookup"><span data-stu-id="30ffe-107">When a user visits a Blazor Server site, the server creates a circuit in the server's memory.</span></span> <span data-ttu-id="30ffe-108">Il circuito indica al browser il contenuto di cui eseguire il rendering e risponde agli eventi, ad esempio quando l'utente seleziona un pulsante nell'interfaccia utente.</span><span class="sxs-lookup"><span data-stu-id="30ffe-108">The circuit indicates to the browser what content to render and responds to events, such as when the user selects a button in the UI.</span></span> <span data-ttu-id="30ffe-109">Per eseguire queste azioni, un circuito richiama le funzioni JavaScript nel browser dell'utente e i metodi .NET sul server.</span><span class="sxs-lookup"><span data-stu-id="30ffe-109">To perform these actions, a circuit invokes JavaScript functions in the user's browser and .NET methods on the server.</span></span> <span data-ttu-id="30ffe-110">Questa interazione bidirezionale basata su JavaScript viene definita [interoperabilità JavaScript (interoperabilità JS).](xref:blazor/call-javascript-from-dotnet)</span><span class="sxs-lookup"><span data-stu-id="30ffe-110">This two-way JavaScript-based interaction is referred to as [JavaScript interop (JS interop)](xref:blazor/call-javascript-from-dotnet).</span></span>

<span data-ttu-id="30ffe-111">Poiché l'interoperabilità JS si verifica su Internet e il client utilizza un browser remoto, le app Blazor Server condividono la maggior parte dei problemi di sicurezza delle app Web.</span><span class="sxs-lookup"><span data-stu-id="30ffe-111">Because JS interop occurs over the Internet and the client uses a remote browser, Blazor Server apps share most web app security concerns.</span></span> <span data-ttu-id="30ffe-112">Questo argomento descrive le minacce comuni alle app blazor Server e fornisce indicazioni sull'attenuazione delle minacce incentrate sulle app con connessione Internet.This topic describes common threats to Blazor Server apps and provides threat to Blazor Server apps and provides threat a threat mitigation guidance focused on Internet-facing apps.</span><span class="sxs-lookup"><span data-stu-id="30ffe-112">This topic describes common threats to Blazor Server apps and provides threat mitigation guidance focused on Internet-facing apps.</span></span>

<span data-ttu-id="30ffe-113">In ambienti vincolati, ad esempio all'interno di reti aziendali o Intranet, alcune delle indicazioni per la mitigazione:</span><span class="sxs-lookup"><span data-stu-id="30ffe-113">In constrained environments, such as inside corporate networks or intranets, some of the mitigation guidance either:</span></span>

* <span data-ttu-id="30ffe-114">Non si applica nell'ambiente vincolato.</span><span class="sxs-lookup"><span data-stu-id="30ffe-114">Doesn't apply in the constrained environment.</span></span>
* <span data-ttu-id="30ffe-115">Non vale i costi da implementare perché il rischio per la sicurezza è basso in un ambiente vincolato.</span><span class="sxs-lookup"><span data-stu-id="30ffe-115">Isn't worth the cost to implement because the security risk is low in a constrained environment.</span></span>

## <a name="blazor-server-project-template"></a><span data-ttu-id="30ffe-116">Modello di progetto Blazor Server</span><span class="sxs-lookup"><span data-stu-id="30ffe-116">Blazor Server project template</span></span>

<span data-ttu-id="30ffe-117">Il modello di progetto Blazor Server può essere configurato per l'autenticazione quando viene creato il progetto.</span><span class="sxs-lookup"><span data-stu-id="30ffe-117">The Blazor Server project template can be configured for authentication when the project is created.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="30ffe-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="30ffe-118">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="30ffe-119">Seguire le indicazioni di <xref:blazor/get-started> Visual Studio nell'articolo per creare un nuovo progetto Blazor Server con un meccanismo di autenticazione.</span><span class="sxs-lookup"><span data-stu-id="30ffe-119">Follow the Visual Studio guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism.</span></span>

<span data-ttu-id="30ffe-120">Dopo aver scelto il modello **App server Blazor** nella finestra di dialogo **Crea una nuova applicazione Web ASP.NET Core**, selezionare **Modifica** in \*\*Autenticazione \*\*.</span><span class="sxs-lookup"><span data-stu-id="30ffe-120">After choosing the **Blazor Server App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication**.</span></span>

<span data-ttu-id="30ffe-121">Viene visualizzata una finestra di dialogo che offre lo stesso set di meccanismi di autenticazione disponibili per altri progetti ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="30ffe-121">A dialog opens to offer the same set of authentication mechanisms available for other ASP.NET Core projects:</span></span>

* <span data-ttu-id="30ffe-122">**Nessuna autenticazione**</span><span class="sxs-lookup"><span data-stu-id="30ffe-122">**No Authentication**</span></span>
* <span data-ttu-id="30ffe-123">**Account utente individuali** &ndash; Gli account utente possono essere archiviati:</span><span class="sxs-lookup"><span data-stu-id="30ffe-123">**Individual User Accounts** &ndash; User accounts can be stored:</span></span>
  * <span data-ttu-id="30ffe-124">All'interno dell'app usando il sistema di [identità](xref:security/authentication/identity) di ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="30ffe-124">Within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system.</span></span>
  * <span data-ttu-id="30ffe-125">Con [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span><span class="sxs-lookup"><span data-stu-id="30ffe-125">With [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span></span>
* <span data-ttu-id="30ffe-126">**Account aziendali o dell'istituto di istruzione**</span><span class="sxs-lookup"><span data-stu-id="30ffe-126">**Work or School Accounts**</span></span>
* <span data-ttu-id="30ffe-127">**Autenticazione di Windows**</span><span class="sxs-lookup"><span data-stu-id="30ffe-127">**Windows Authentication**</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="30ffe-128">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="30ffe-128">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="30ffe-129">Seguire le linee guida <xref:blazor/get-started> del codice di Visual Studio nell'articolo per creare un nuovo progetto Blazor Server con un meccanismo di autenticazione:Follow the Visual Studio Code guidance in the article to create a new Blazor Server project with an authentication mechanism:</span><span class="sxs-lookup"><span data-stu-id="30ffe-129">Follow the Visual Studio Code guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="30ffe-130">I valori di autenticazione consentiti (`{AUTHENTICATION}`) sono indicati nella tabella seguente.</span><span class="sxs-lookup"><span data-stu-id="30ffe-130">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="30ffe-131">Meccanismo di autenticazione</span><span class="sxs-lookup"><span data-stu-id="30ffe-131">Authentication mechanism</span></span>                                                                 | <span data-ttu-id="30ffe-132">Valore della proprietà `{AUTHENTICATION}`</span><span class="sxs-lookup"><span data-stu-id="30ffe-132">`{AUTHENTICATION}` value</span></span> |
| ---------------------------------------------------------------------------------------- | :----------------------: |
| <span data-ttu-id="30ffe-133">Nessuna autenticazione</span><span class="sxs-lookup"><span data-stu-id="30ffe-133">No Authentication</span></span>                                                                        | `None`                   |
| <span data-ttu-id="30ffe-134">Utenti</span><span class="sxs-lookup"><span data-stu-id="30ffe-134">Individual</span></span><br><span data-ttu-id="30ffe-135">individuali archiviati nell'app con ASP.NET Core Identity.</span><span class="sxs-lookup"><span data-stu-id="30ffe-135">Users stored in the app with ASP.NET Core Identity.</span></span>                        | `Individual`             |
| <span data-ttu-id="30ffe-136">Utenti</span><span class="sxs-lookup"><span data-stu-id="30ffe-136">Individual</span></span><br><span data-ttu-id="30ffe-137">individuali archiviati in [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span><span class="sxs-lookup"><span data-stu-id="30ffe-137">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span></span> | `IndividualB2C`          |
| <span data-ttu-id="30ffe-138">Account aziendali o dell'istituto di istruzione</span><span class="sxs-lookup"><span data-stu-id="30ffe-138">Work or School Accounts</span></span><br><span data-ttu-id="30ffe-139">Autenticazione aziendale per un singolo tenant.</span><span class="sxs-lookup"><span data-stu-id="30ffe-139">Organizational authentication for a single tenant.</span></span>            | `SingleOrg`              |
| <span data-ttu-id="30ffe-140">Account aziendali o dell'istituto di istruzione</span><span class="sxs-lookup"><span data-stu-id="30ffe-140">Work or School Accounts</span></span><br><span data-ttu-id="30ffe-141">Autenticazione aziendale per più tenant.</span><span class="sxs-lookup"><span data-stu-id="30ffe-141">Organizational authentication for multiple tenants.</span></span>           | `MultiOrg`               |
| <span data-ttu-id="30ffe-142">Autenticazione di Windows</span><span class="sxs-lookup"><span data-stu-id="30ffe-142">Windows Authentication</span></span>                                                                   | `Windows`                |

<span data-ttu-id="30ffe-143">Il comando crea una cartella denominata con il valore fornito per il segnaposto `{APP NAME}` e il nome della cartella viene usato come nome dell'app.</span><span class="sxs-lookup"><span data-stu-id="30ffe-143">The command creates a folder named with the value provided for the `{APP NAME}` placeholder and uses the folder name as the app's name.</span></span> <span data-ttu-id="30ffe-144">Per altre informazioni, vedere il comando [dotnet new](/dotnet/core/tools/dotnet-new) nella guida per .NET Core.</span><span class="sxs-lookup"><span data-stu-id="30ffe-144">For more information, see the [dotnet new](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="30ffe-145">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="30ffe-145">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="30ffe-146">Seguire le linee guida di <xref:blazor/get-started> Visual Studio per Mac nell'articolo.</span><span class="sxs-lookup"><span data-stu-id="30ffe-146">Follow the Visual Studio for Mac guidance in the <xref:blazor/get-started> article.</span></span>

1. <span data-ttu-id="30ffe-147">Nel passaggio Configura la **nuova app Blazor Server** selezionare **Autenticazione singola (in-app)** dall'elenco a discesa **Autenticazione.**</span><span class="sxs-lookup"><span data-stu-id="30ffe-147">On the **Configure your new Blazor Server App** step, select **Individual Authentication (in-app)** from the **Authentication** drop down.</span></span>

1. <span data-ttu-id="30ffe-148">L'app viene creata per i singoli utenti archiviati nell'app con ASP.NET identità di base.</span><span class="sxs-lookup"><span data-stu-id="30ffe-148">The app is created for individual users stored in the app with ASP.NET Core Identity.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="30ffe-149">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="30ffe-149">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="30ffe-150">Seguire le indicazioni dell'interfaccia <xref:blazor/get-started> della riga di comando di .NET Core nell'articolo per creare un nuovo progetto Blazor Server con un meccanismo di autenticazione:</span><span class="sxs-lookup"><span data-stu-id="30ffe-150">Follow the .NET Core CLI guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="30ffe-151">I valori di autenticazione consentiti (`{AUTHENTICATION}`) sono indicati nella tabella seguente.</span><span class="sxs-lookup"><span data-stu-id="30ffe-151">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="30ffe-152">Meccanismo di autenticazione</span><span class="sxs-lookup"><span data-stu-id="30ffe-152">Authentication mechanism</span></span>                                                                 | <span data-ttu-id="30ffe-153">Valore della proprietà `{AUTHENTICATION}`</span><span class="sxs-lookup"><span data-stu-id="30ffe-153">`{AUTHENTICATION}` value</span></span> |
| ---------------------------------------------------------------------------------------- | :----------------------: |
| <span data-ttu-id="30ffe-154">Nessuna autenticazione</span><span class="sxs-lookup"><span data-stu-id="30ffe-154">No Authentication</span></span>                                                                        | `None`                   |
| <span data-ttu-id="30ffe-155">Utenti</span><span class="sxs-lookup"><span data-stu-id="30ffe-155">Individual</span></span><br><span data-ttu-id="30ffe-156">individuali archiviati nell'app con ASP.NET Core Identity.</span><span class="sxs-lookup"><span data-stu-id="30ffe-156">Users stored in the app with ASP.NET Core Identity.</span></span>                        | `Individual`             |
| <span data-ttu-id="30ffe-157">Utenti</span><span class="sxs-lookup"><span data-stu-id="30ffe-157">Individual</span></span><br><span data-ttu-id="30ffe-158">individuali archiviati in [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span><span class="sxs-lookup"><span data-stu-id="30ffe-158">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span></span> | `IndividualB2C`          |
| <span data-ttu-id="30ffe-159">Account aziendali o dell'istituto di istruzione</span><span class="sxs-lookup"><span data-stu-id="30ffe-159">Work or School Accounts</span></span><br><span data-ttu-id="30ffe-160">Autenticazione aziendale per un singolo tenant.</span><span class="sxs-lookup"><span data-stu-id="30ffe-160">Organizational authentication for a single tenant.</span></span>            | `SingleOrg`              |
| <span data-ttu-id="30ffe-161">Account aziendali o dell'istituto di istruzione</span><span class="sxs-lookup"><span data-stu-id="30ffe-161">Work or School Accounts</span></span><br><span data-ttu-id="30ffe-162">Autenticazione aziendale per più tenant.</span><span class="sxs-lookup"><span data-stu-id="30ffe-162">Organizational authentication for multiple tenants.</span></span>           | `MultiOrg`               |
| <span data-ttu-id="30ffe-163">Autenticazione di Windows</span><span class="sxs-lookup"><span data-stu-id="30ffe-163">Windows Authentication</span></span>                                                                   | `Windows`                |

<span data-ttu-id="30ffe-164">Il comando crea una cartella denominata con il valore fornito per il segnaposto `{APP NAME}` e il nome della cartella viene usato come nome dell'app.</span><span class="sxs-lookup"><span data-stu-id="30ffe-164">The command creates a folder named with the value provided for the `{APP NAME}` placeholder and uses the folder name as the app's name.</span></span> <span data-ttu-id="30ffe-165">Per altre informazioni, vedere il comando [dotnet new](/dotnet/core/tools/dotnet-new) nella guida per .NET Core.</span><span class="sxs-lookup"><span data-stu-id="30ffe-165">For more information, see the [dotnet new](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

---

## <a name="pass-tokens-to-a-blazor-server-app"></a><span data-ttu-id="30ffe-166">Passare token a un'app Blazor ServerPass tokens to a Blazor Server app</span><span class="sxs-lookup"><span data-stu-id="30ffe-166">Pass tokens to a Blazor Server app</span></span>

<span data-ttu-id="30ffe-167">Autenticare l'app Blazor Server come si farebbe con un normale Razor Pages o MVC app.</span><span class="sxs-lookup"><span data-stu-id="30ffe-167">Authenticate the Blazor Server app as you would with a regular Razor Pages or MVC app.</span></span> <span data-ttu-id="30ffe-168">Effettuare il provisioning e salvare i token nel cookie di autenticazione.</span><span class="sxs-lookup"><span data-stu-id="30ffe-168">Provision and save the tokens to the authentication cookie.</span></span> <span data-ttu-id="30ffe-169">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="30ffe-169">For example:</span></span>

```csharp
using Microsoft.AspNetCore.Authentication.OpenIdConnect;

...

services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
{
    options.ResponseType = "code";
    options.SaveTokens = true;

    options.Scope.Add("offline_access");
    options.Scope.Add("{SCOPE}");
    options.Resource = "{RESOURCE}";
});
```

<span data-ttu-id="30ffe-170">Per il codice di `Startup.ConfigureServices` esempio, incluso un esempio completo, vedere passaggio di [token a un'applicazione Blazor lato server](https://github.com/javiercn/blazor-server-aad-sample).</span><span class="sxs-lookup"><span data-stu-id="30ffe-170">For sample code, including a complete `Startup.ConfigureServices` example, see the [Passing tokens to a server-side Blazor application](https://github.com/javiercn/blazor-server-aad-sample).</span></span>

<span data-ttu-id="30ffe-171">Definisci una classe da passare allo stato iniziale dell'app con i token di accesso e aggiornamento:Define a class to pass in the initial app state with the access and refresh tokens:</span><span class="sxs-lookup"><span data-stu-id="30ffe-171">Define a class to pass in the initial app state with the access and refresh tokens:</span></span>

```csharp
public class InitialApplicationState
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

<span data-ttu-id="30ffe-172">Definire un servizio provider di token con ambito che può essere usato all'interno dell'app Blazor per risolvere i token da DI:Define a **scoped** token provider service that can be used within the Blazor app to resolve the tokens from DI:</span><span class="sxs-lookup"><span data-stu-id="30ffe-172">Define a **scoped** token provider service that can be used within the Blazor app to resolve the tokens from DI:</span></span>

```csharp
using System;
using System.Security.Claims;
using System.Threading.Tasks;

public class TokenProvider
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

<span data-ttu-id="30ffe-173">In `Startup.ConfigureServices`, aggiungere servizi per:</span><span class="sxs-lookup"><span data-stu-id="30ffe-173">In `Startup.ConfigureServices`, add services for:</span></span>

* `IHttpClientFactory`
* `TokenProvider`

```csharp
services.AddHttpClient();
services.AddScoped<TokenProvider>();
```

<span data-ttu-id="30ffe-174">Nel file *_Host.cshtml* creare e `InitialApplicationState` creare un'istanza di e passarlo come parametro all'app:In the _Host.cshtml file, create and instance of and pass it as a parameter to the app:</span><span class="sxs-lookup"><span data-stu-id="30ffe-174">In the *_Host.cshtml* file, create and instance of `InitialApplicationState` and pass it as a parameter to the app:</span></span>

```cshtml
@using Microsoft.AspNetCore.Authentication

...

@{
    var tokens = new InitialApplicationState
    {
        AccessToken = await HttpContext.GetTokenAsync("access_token"),
        RefreshToken = await HttpContext.GetTokenAsync("refresh_token")
    };
}

<app>
    <component type="typeof(App)" param-InitialState="tokens" 
        render-mode="ServerPrerendered" />
</app>
```

<span data-ttu-id="30ffe-175">Nel `App` componente (*App.razor*), risolvere il servizio e inizializzarlo con i dati del parametro :</span><span class="sxs-lookup"><span data-stu-id="30ffe-175">In the `App` component (*App.razor*), resolve the service and initialize it with the data from the parameter:</span></span>

```razor
@inject TokenProvider TokensProvider

...

@code {
    [Parameter]
    public InitialApplicationState InitialState { get; set; }

    protected override Task OnInitializedAsync()
    {
        TokensProvider.AccessToken = InitialState.AccessToken;
        TokensProvider.RefreshToken = InitialState.RefreshToken;

        return base.OnInitializedAsync();
    }
}
```

<span data-ttu-id="30ffe-176">Nel servizio che effettua una richiesta API sicura, inserire il provider di token e recuperare il token per chiamare l'API:In the service that makes a secure API request, inject the token provider and retrieve the token to call the API:</span><span class="sxs-lookup"><span data-stu-id="30ffe-176">In the service that makes a secure API request, inject the token provider and retrieve the token to call the API:</span></span>

```csharp
public class WeatherForecastService
{
    private readonly TokenProvider _store;

    public WeatherForecastService(IHttpClientFactory clientFactory, 
        TokenProvider tokenProvider)
    {
        Client = clientFactory.CreateClient();
        _store = tokenProvider;
    }

    public HttpClient Client { get; }

    public async Task<WeatherForecast[]> GetForecastAsync(DateTime startDate)
    {
        var token = _store.AccessToken;
        var request = new HttpRequestMessage(HttpMethod.Get, 
            "https://localhost:5003/WeatherForecast");
        request.Headers.Add("Authorization", $"Bearer {token}");
        var response = await Client.SendAsync(request);
        response.EnsureSuccessStatusCode();

        return await response.Content.ReadAsAsync<WeatherForecast[]>();
    }
}
```

## <a name="resource-exhaustion"></a><span data-ttu-id="30ffe-177">Esaurimento delle risorse</span><span class="sxs-lookup"><span data-stu-id="30ffe-177">Resource exhaustion</span></span>

<span data-ttu-id="30ffe-178">L'esaurimento delle risorse può verificarsi quando un client interagisce con il server e fa sì che il server consumi risorse eccessive.</span><span class="sxs-lookup"><span data-stu-id="30ffe-178">Resource exhaustion can occur when a client interacts with the server and causes the server to consume excessive resources.</span></span> <span data-ttu-id="30ffe-179">Un consumo eccessivo di risorse influisce principalmente:Excessive resource consumption primarily affects:</span><span class="sxs-lookup"><span data-stu-id="30ffe-179">Excessive resource consumption primarily affects:</span></span>

* [<span data-ttu-id="30ffe-180">CPU</span><span class="sxs-lookup"><span data-stu-id="30ffe-180">CPU</span></span>](#cpu)
* [<span data-ttu-id="30ffe-181">Memoria</span><span class="sxs-lookup"><span data-stu-id="30ffe-181">Memory</span></span>](#memory)
* [<span data-ttu-id="30ffe-182">Connessioni client</span><span class="sxs-lookup"><span data-stu-id="30ffe-182">Client connections</span></span>](#client-connections)

<span data-ttu-id="30ffe-183">Gli attacchi DoS (Denial of Service) in genere cercano di esaurire le risorse di un'app o di un server.</span><span class="sxs-lookup"><span data-stu-id="30ffe-183">Denial of service (DoS) attacks usually seek to exhaust an app or server's resources.</span></span> <span data-ttu-id="30ffe-184">Tuttavia, l'esaurimento delle risorse non è necessariamente il risultato di un attacco al sistema.</span><span class="sxs-lookup"><span data-stu-id="30ffe-184">However, resource exhaustion isn't necessarily the result of an attack on the system.</span></span> <span data-ttu-id="30ffe-185">Ad esempio, le risorse limitate possono essere esaurite a causa dell'elevata richiesta degli utenti.</span><span class="sxs-lookup"><span data-stu-id="30ffe-185">For example, finite resources can be exhausted due to high user demand.</span></span> <span data-ttu-id="30ffe-186">DoS è trattato ulteriormente nella sezione [Attacchi DoS (Denial of Service).](#denial-of-service-dos-attacks)</span><span class="sxs-lookup"><span data-stu-id="30ffe-186">DoS is covered further in the [Denial of service (DoS) attacks](#denial-of-service-dos-attacks) section.</span></span>

<span data-ttu-id="30ffe-187">Le risorse esterne al framework Blazor, ad esempio database e handle di file (utilizzati per leggere e scrivere file), possono anche verificarsi l'esaurimento delle risorse.</span><span class="sxs-lookup"><span data-stu-id="30ffe-187">Resources external to the Blazor framework, such as databases and file handles (used to read and write files), may also experience resource exhaustion.</span></span> <span data-ttu-id="30ffe-188">Per altre informazioni, vedere <xref:performance/performance-best-practices>.</span><span class="sxs-lookup"><span data-stu-id="30ffe-188">For more information, see <xref:performance/performance-best-practices>.</span></span>

### <a name="cpu"></a><span data-ttu-id="30ffe-189">CPU</span><span class="sxs-lookup"><span data-stu-id="30ffe-189">CPU</span></span>

<span data-ttu-id="30ffe-190">L'esaurimento della CPU può verificarsi quando uno o più client forzano il server a eseguire un lavoro intensivo della CPU.</span><span class="sxs-lookup"><span data-stu-id="30ffe-190">CPU exhaustion can occur when one or more clients force the server to perform intensive CPU work.</span></span>

<span data-ttu-id="30ffe-191">Si consideri, ad esempio, un'app Blazor Server che calcola un *numero Fibonnacci*.</span><span class="sxs-lookup"><span data-stu-id="30ffe-191">For example, consider a Blazor Server app that calculates a *Fibonnacci number*.</span></span> <span data-ttu-id="30ffe-192">Un numero Fibonnacci viene prodotto da una sequenza di Fibonnacci, dove ogni numero nella sequenza è la somma dei due numeri precedenti.</span><span class="sxs-lookup"><span data-stu-id="30ffe-192">A Fibonnacci number is produced from a Fibonnacci sequence, where each number in the sequence is the sum of the two preceding numbers.</span></span> <span data-ttu-id="30ffe-193">La quantità di lavoro necessaria per raggiungere la risposta dipende dalla lunghezza della sequenza e dalla dimensione del valore iniziale.</span><span class="sxs-lookup"><span data-stu-id="30ffe-193">The amount of work required to reach the answer depends on the length of the sequence and the size of the initial value.</span></span> <span data-ttu-id="30ffe-194">Se l'app non limita la richiesta di un client, i calcoli che richiedono un utilizzo intensivo della CPU possono dominare il tempo della CPU e ridurre le prestazioni di altre attività.</span><span class="sxs-lookup"><span data-stu-id="30ffe-194">If the app doesn't place limits on a client's request, the CPU-intensive calculations may dominate the CPU's time and diminish the performance of other tasks.</span></span> <span data-ttu-id="30ffe-195">L'utilizzo eccessivo delle risorse è un problema di sicurezza che influisce sulla disponibilità.</span><span class="sxs-lookup"><span data-stu-id="30ffe-195">Excessive resource consumption is a security concern impacting availability.</span></span>

<span data-ttu-id="30ffe-196">L'esaurimento della CPU è un problema per tutte le app pubbliche.</span><span class="sxs-lookup"><span data-stu-id="30ffe-196">CPU exhaustion is a concern for all public-facing apps.</span></span> <span data-ttu-id="30ffe-197">Nelle normali app Web, le richieste e le connessioni soffrono per la protezione, ma le app Blazor Server non forniscono le stesse misure di sicurezza.</span><span class="sxs-lookup"><span data-stu-id="30ffe-197">In regular web apps, requests and connections time out as a safeguard, but Blazor Server apps don't provide the same safeguards.</span></span> <span data-ttu-id="30ffe-198">Le app del server Blazor devono includere controlli e limiti appropriati prima di eseguire un lavoro potenzialmente intensivo della CPU.</span><span class="sxs-lookup"><span data-stu-id="30ffe-198">Blazor Server apps must include appropriate checks and limits before performing potentially CPU-intensive work.</span></span>

### <a name="memory"></a><span data-ttu-id="30ffe-199">Memoria</span><span class="sxs-lookup"><span data-stu-id="30ffe-199">Memory</span></span>

<span data-ttu-id="30ffe-200">L'esaurimento della memoria può verificarsi quando uno o più client forzano il server a utilizzare una grande quantità di memoria.</span><span class="sxs-lookup"><span data-stu-id="30ffe-200">Memory exhaustion can occur when one or more clients force the server to consume a large amount of memory.</span></span>

<span data-ttu-id="30ffe-201">Si consideri, ad esempio, un'app lato server Blazor con un componente che accetta e visualizza un elenco di elementi.</span><span class="sxs-lookup"><span data-stu-id="30ffe-201">For example, consider a Blazor-server side app with a component that accepts and displays a list of items.</span></span> <span data-ttu-id="30ffe-202">Se l'app Blazor non limita il numero di elementi consentiti o il numero di elementi di cui viene eseguito il rendering al client, l'elaborazione e il rendering che richiedono un utilizzo intensivo della memoria possono dominare la memoria del server fino al punto in cui le prestazioni del server ne risentono.</span><span class="sxs-lookup"><span data-stu-id="30ffe-202">If the Blazor app doesn't place limits on the number of items allowed or the number of items rendered back to the client, the memory-intensive processing and rendering may dominate the server's memory to the point where performance of the server suffers.</span></span> <span data-ttu-id="30ffe-203">Il server potrebbe bloccarsi o rallentare al punto che sembra essere in modo anomalo.</span><span class="sxs-lookup"><span data-stu-id="30ffe-203">The server may crash or slow to the point that it appears to have crashed.</span></span>

<span data-ttu-id="30ffe-204">Si consideri lo scenario seguente per la gestione e la visualizzazione di un elenco di elementi relativi a uno scenario di esaurimento della memoria potenziale nel server:</span><span class="sxs-lookup"><span data-stu-id="30ffe-204">Consider the following scenario for maintaining and displaying a list of items that pertain to a potential memory exhaustion scenario on the server:</span></span>

* <span data-ttu-id="30ffe-205">Gli elementi `List<MyItem>` in una proprietà o in un campo utilizzano la memoria del server.</span><span class="sxs-lookup"><span data-stu-id="30ffe-205">The items in a `List<MyItem>` property or field use the server's memory.</span></span> <span data-ttu-id="30ffe-206">Se l'app consente all'elenco di elementi di crescere illimitato, c'è il rischio che il server esaurisca la memoria.</span><span class="sxs-lookup"><span data-stu-id="30ffe-206">If the app allows the list of items to grow unbounded, there's a risk of the server running out of memory.</span></span> <span data-ttu-id="30ffe-207">L'esaurimento della memoria causa la fine della sessione corrente (arresto anomalo) e tutte le sessioni simultanee nell'istanza del server ricevono un'eccezione di memoria insufficiente.</span><span class="sxs-lookup"><span data-stu-id="30ffe-207">Running out of memory causes the current session to end (crash) and all of the concurrent sessions in that server instance receive an out-of-memory exception.</span></span> <span data-ttu-id="30ffe-208">Per evitare che si verifichi questo scenario, l'app deve usare una struttura di dati che impone un limite di elementi per gli utenti simultanei.</span><span class="sxs-lookup"><span data-stu-id="30ffe-208">To prevent this scenario from occurring, the app must use a data structure that imposes an item limit on concurrent users.</span></span>
* <span data-ttu-id="30ffe-209">Se uno schema di paging non viene utilizzato per il rendering, il server utilizza memoria aggiuntiva per gli oggetti che non sono visibili nell'interfaccia utente.</span><span class="sxs-lookup"><span data-stu-id="30ffe-209">If a paging scheme isn't used for rendering, the server uses additional memory for objects that aren't visible in the UI.</span></span> <span data-ttu-id="30ffe-210">Senza un limite al numero di elementi, le richieste di memoria potrebbero esaurire la memoria disponibile del server.</span><span class="sxs-lookup"><span data-stu-id="30ffe-210">Without a limit on the number of items, memory demands may exhaust the available server memory.</span></span> <span data-ttu-id="30ffe-211">Per evitare questo scenario, utilizzare uno dei seguenti approcci:</span><span class="sxs-lookup"><span data-stu-id="30ffe-211">To prevent this scenario, use one of the following approaches:</span></span>
  * <span data-ttu-id="30ffe-212">Utilizzare gli elenchi impaginati durante il rendering.</span><span class="sxs-lookup"><span data-stu-id="30ffe-212">Use paginated lists when rendering.</span></span>
  * <span data-ttu-id="30ffe-213">Visualizzare solo i primi 100-1.000 elementi e richiedere all'utente di immettere i criteri di ricerca per trovare gli elementi oltre gli elementi visualizzati.</span><span class="sxs-lookup"><span data-stu-id="30ffe-213">Only display the first 100 to 1,000 items and require the user to enter search criteria to find items beyond the items displayed.</span></span>
  * <span data-ttu-id="30ffe-214">Per uno scenario di rendering più avanzato, implementare elenchi o griglie che supportano *la virtualizzazione.*</span><span class="sxs-lookup"><span data-stu-id="30ffe-214">For a more advanced rendering scenario, implement lists or grids that support *virtualization*.</span></span> <span data-ttu-id="30ffe-215">Utilizzando la virtualizzazione, gli elenchi eseguono il rendering solo di un sottoinsieme di elementi attualmente visibili all'utente.</span><span class="sxs-lookup"><span data-stu-id="30ffe-215">Using virtualization, lists only render a subset of items currently visible to the user.</span></span> <span data-ttu-id="30ffe-216">Quando l'utente interagisce con la barra di scorrimento nell'interfaccia utente, il componente esegue il rendering solo degli elementi necessari per la visualizzazione.</span><span class="sxs-lookup"><span data-stu-id="30ffe-216">When the user interacts with the scrollbar in the UI, the component renders only those items required for display.</span></span> <span data-ttu-id="30ffe-217">Gli elementi che non sono attualmente necessari per la visualizzazione possono essere conservati nella memoria secondaria, che è l'approccio ideale.</span><span class="sxs-lookup"><span data-stu-id="30ffe-217">The items that aren't currently required for display can be held in secondary storage, which is the ideal approach.</span></span> <span data-ttu-id="30ffe-218">Gli elementi non visualizzati possono anche essere conservati in memoria, il che è meno ideale.</span><span class="sxs-lookup"><span data-stu-id="30ffe-218">Undisplayed items can also be held in memory, which is less ideal.</span></span>

<span data-ttu-id="30ffe-219">Blazor Server apps offer a similar programming model to other UI frameworks for stateful apps, such as WPF, Windows Forms, or Blazor WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="30ffe-219">Blazor Server apps offer a similar programming model to other UI frameworks for stateful apps, such as WPF, Windows Forms, or Blazor WebAssembly.</span></span> <span data-ttu-id="30ffe-220">La differenza principale è che in molti dei framework dell'interfaccia utente la memoria utilizzata dall'app appartiene al client e influisce solo su tale singolo client.</span><span class="sxs-lookup"><span data-stu-id="30ffe-220">The main difference is that in several of the UI frameworks the memory consumed by the app belongs to the client and only affects that individual client.</span></span> <span data-ttu-id="30ffe-221">Ad esempio, un'app Blazor WebAssembly viene eseguita interamente sul client e utilizza solo le risorse di memoria client.</span><span class="sxs-lookup"><span data-stu-id="30ffe-221">For example, a Blazor WebAssembly app runs entirely on the client and only uses client memory resources.</span></span> <span data-ttu-id="30ffe-222">Nello scenario Blazor Server, la memoria utilizzata dall'app appartiene al server e viene condivisa tra i client nell'istanza del server.</span><span class="sxs-lookup"><span data-stu-id="30ffe-222">In the Blazor Server scenario, the memory consumed by the app belongs to the server and is shared among clients on the server instance.</span></span>

<span data-ttu-id="30ffe-223">Le richieste di memoria lato server sono una considerazione per tutte le applicazioni Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="30ffe-223">Server-side memory demands are a consideration for all Blazor Server apps.</span></span> <span data-ttu-id="30ffe-224">Tuttavia, la maggior parte delle app Web sono senza stato e la memoria utilizzata durante l'elaborazione di una richiesta viene rilasciata quando viene restituita la risposta.</span><span class="sxs-lookup"><span data-stu-id="30ffe-224">However, most web apps are stateless, and the memory used while processing a request is released when the response is returned.</span></span> <span data-ttu-id="30ffe-225">Come raccomandazione generale, non consentire ai client di allocare una quantità di memoria non associata come in qualsiasi altra app sul lato server che rende persistenti le connessioni client.</span><span class="sxs-lookup"><span data-stu-id="30ffe-225">As a general recommendation, don't permit clients to allocate an unbound amount of memory as in any other server-side app that persists client connections.</span></span> <span data-ttu-id="30ffe-226">La memoria utilizzata da un'app Blazor Server persiste per un periodo di tempo più lungo rispetto a una singola richiesta.</span><span class="sxs-lookup"><span data-stu-id="30ffe-226">The memory consumed by a Blazor Server app persists for a longer time than a single request.</span></span>

> [!NOTE]
> <span data-ttu-id="30ffe-227">Durante lo sviluppo, è possibile utilizzare un profiler o acquisire una traccia per valutare le richieste di memoria dei client.</span><span class="sxs-lookup"><span data-stu-id="30ffe-227">During development, a profiler can be used or a trace captured to assess memory demands of clients.</span></span> <span data-ttu-id="30ffe-228">Un profiler o una traccia non acquisiscono la memoria allocata a un client specifico.</span><span class="sxs-lookup"><span data-stu-id="30ffe-228">A profiler or trace won't capture the memory allocated to a specific client.</span></span> <span data-ttu-id="30ffe-229">Per acquisire l'utilizzo della memoria di un client specifico durante lo sviluppo, acquisire un dump ed esaminare la richiesta di memoria di tutti gli oggetti radicati nel circuito di un utente.</span><span class="sxs-lookup"><span data-stu-id="30ffe-229">To capture the memory use of a specific client during development, capture a dump and examine the memory demand of all the objects rooted at a user's circuit.</span></span>

### <a name="client-connections"></a><span data-ttu-id="30ffe-230">Connessioni client</span><span class="sxs-lookup"><span data-stu-id="30ffe-230">Client connections</span></span>

<span data-ttu-id="30ffe-231">L'esaurimento della connessione può verificarsi quando uno o più client aprono troppe connessioni simultanee al server, impedendo ad altri client di stabilire nuove connessioni.</span><span class="sxs-lookup"><span data-stu-id="30ffe-231">Connection exhaustion can occur when one or more clients open too many concurrent connections to the server, preventing other clients from establishing new connections.</span></span>

<span data-ttu-id="30ffe-232">I client Blazor stabiliscono una singola connessione per sessione e mantengono aperta la connessione per tutto il tempo in cui la finestra del browser è aperta.</span><span class="sxs-lookup"><span data-stu-id="30ffe-232">Blazor clients establish a single connection per session and keep the connection open for as long as the browser window is open.</span></span> <span data-ttu-id="30ffe-233">Le richieste sul server di mantenere tutte le connessioni non sono specifiche per le app Blazor.</span><span class="sxs-lookup"><span data-stu-id="30ffe-233">The demands on the server of maintaining all of the connections isn't specific to Blazor apps.</span></span> <span data-ttu-id="30ffe-234">Data la natura persistente delle connessioni e la natura con stato delle app del server Blazor, l'esaurimento della connessione è un rischio maggiore per la disponibilità dell'app.</span><span class="sxs-lookup"><span data-stu-id="30ffe-234">Given the persistent nature of the connections and the stateful nature of Blazor Server apps, connection exhaustion is a greater risk to availability of the app.</span></span>

<span data-ttu-id="30ffe-235">Per impostazione predefinita, non esiste alcun limite al numero di connessioni per utente per un'app Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="30ffe-235">By default, there's no limit on the number of connections per user for a Blazor Server app.</span></span> <span data-ttu-id="30ffe-236">Se l'app richiede un limite di connessione, adottare uno o più degli approcci seguenti:</span><span class="sxs-lookup"><span data-stu-id="30ffe-236">If the app requires a connection limit, take one or more of the following approaches:</span></span>

* <span data-ttu-id="30ffe-237">Richiedere l'autenticazione, che limita naturalmente la capacità degli utenti non autorizzati di connettersi all'app.</span><span class="sxs-lookup"><span data-stu-id="30ffe-237">Require authentication, which naturally limits the ability of unauthorized users to connect to the app.</span></span> <span data-ttu-id="30ffe-238">Affinché questo scenario sia efficace, è necessario impedire agli utenti di eseguire il provisioning di nuovi utenti a livello di volontà.</span><span class="sxs-lookup"><span data-stu-id="30ffe-238">For this scenario to be effective, users must be prevented from provisioning new users at will.</span></span>
* <span data-ttu-id="30ffe-239">Limitare il numero di connessioni per utente.</span><span class="sxs-lookup"><span data-stu-id="30ffe-239">Limit the number of connections per user.</span></span> <span data-ttu-id="30ffe-240">La limitazione delle connessioni può essere eseguita tramite i seguenti approcci.</span><span class="sxs-lookup"><span data-stu-id="30ffe-240">Limiting connections can be accomplished via the following approaches.</span></span> <span data-ttu-id="30ffe-241">Esercitare attenzione per consentire agli utenti legittimi di accedere all'app (ad esempio, quando viene stabilito un limite di connessione in base all'indirizzo IP del client).</span><span class="sxs-lookup"><span data-stu-id="30ffe-241">Exercise care to allow legitimate users to access the app (for example, when a connection limit is established based on the client's IP address).</span></span>
  * <span data-ttu-id="30ffe-242">A livello di app:</span><span class="sxs-lookup"><span data-stu-id="30ffe-242">At the app level:</span></span>
    * <span data-ttu-id="30ffe-243">Estendibilità del routing degli endpoint.</span><span class="sxs-lookup"><span data-stu-id="30ffe-243">Endpoint routing extensibility.</span></span>
    * <span data-ttu-id="30ffe-244">Richiedere l'autenticazione per connettersi all'app e tenere traccia delle sessioni attive per utente.</span><span class="sxs-lookup"><span data-stu-id="30ffe-244">Require authentication to connect to the app and keep track of the active sessions per user.</span></span>
    * <span data-ttu-id="30ffe-245">Rifiuta nuove sessioni al raggiungimento di un limite.</span><span class="sxs-lookup"><span data-stu-id="30ffe-245">Reject new sessions upon reaching a limit.</span></span>
    * <span data-ttu-id="30ffe-246">Proxy WebSocket connessioni a un'app tramite l'uso di un proxy, ad esempio il [servizio SignalR](/azure/azure-signalr/signalr-overview) di Azure che multiplexa le connessioni dai client a un'app.</span><span class="sxs-lookup"><span data-stu-id="30ffe-246">Proxy WebSocket connections to an app through the use of a proxy, such as the [Azure SignalR Service](/azure/azure-signalr/signalr-overview) that multiplexes connections from clients to an app.</span></span> <span data-ttu-id="30ffe-247">Ciò fornisce a un'app una capacità di connessione maggiore di quella che un singolo client può stabilire, impedendo a un client di esaurire le connessioni al server.</span><span class="sxs-lookup"><span data-stu-id="30ffe-247">This provides an app with greater connection capacity than a single client can establish, preventing a client from exhausting the connections to the server.</span></span>
  * <span data-ttu-id="30ffe-248">A livello di server: usare un proxy/gateway davanti all'app.</span><span class="sxs-lookup"><span data-stu-id="30ffe-248">At the server level: Use a proxy/gateway in front of the app.</span></span> <span data-ttu-id="30ffe-249">Ad esempio, [il portefronte](/azure/frontdoor/front-door-overview) di Azure consente di definire, gestire e monitorare il routing globale del traffico Web a un'app.</span><span class="sxs-lookup"><span data-stu-id="30ffe-249">For example, [Azure Front Door](/azure/frontdoor/front-door-overview) enables you to define, manage, and monitor the global routing of web traffic to an app.</span></span>

## <a name="denial-of-service-dos-attacks"></a><span data-ttu-id="30ffe-250">Attacchi DoS (Denial of Service)</span><span class="sxs-lookup"><span data-stu-id="30ffe-250">Denial of service (DoS) attacks</span></span>

<span data-ttu-id="30ffe-251">Gli attacchi DoS (Denial of Service) coinvolgono un client che causa l'esaurimento di una o più risorse da parte del server che rende l'app non disponibile.</span><span class="sxs-lookup"><span data-stu-id="30ffe-251">Denial of service (DoS) attacks involve a client causing the server to exhaust one or more of its resources making the app unavailable.</span></span> <span data-ttu-id="30ffe-252">Le app di Blazor Server includono alcuni limiti predefiniti e si basano su altri limiti ASP.NET Core e SignalR per proteggersi dagli attacchi DoS:</span><span class="sxs-lookup"><span data-stu-id="30ffe-252">Blazor Server apps include some default limits and rely on other ASP.NET Core and SignalR limits to protect against DoS attacks:</span></span>

| <span data-ttu-id="30ffe-253">Limite dell'app Blazor Server</span><span class="sxs-lookup"><span data-stu-id="30ffe-253">Blazor Server app limit</span></span>                            | <span data-ttu-id="30ffe-254">Descrizione</span><span class="sxs-lookup"><span data-stu-id="30ffe-254">Description</span></span> | <span data-ttu-id="30ffe-255">Predefinito</span><span class="sxs-lookup"><span data-stu-id="30ffe-255">Default</span></span> |
| ------------------------------------------------------- | ----------- | ------- |
| `CircuitOptions.DisconnectedCircuitMaxRetained`         | <span data-ttu-id="30ffe-256">Numero massimo di circuiti disconnessi che un determinato server conserva in memoria alla volta.</span><span class="sxs-lookup"><span data-stu-id="30ffe-256">Maximum number of disconnected circuits that a given server holds in memory at a time.</span></span> | <span data-ttu-id="30ffe-257">100</span><span class="sxs-lookup"><span data-stu-id="30ffe-257">100</span></span> |
| `CircuitOptions.DisconnectedCircuitRetentionPeriod`     | <span data-ttu-id="30ffe-258">Quantità massima di tempo in cui un circuito disconnesso viene mantenuto in memoria prima di essere abbattuto.</span><span class="sxs-lookup"><span data-stu-id="30ffe-258">Maximum amount of time a disconnected circuit is held in memory before being torn down.</span></span> | <span data-ttu-id="30ffe-259">3 minuti</span><span class="sxs-lookup"><span data-stu-id="30ffe-259">3 minutes</span></span> |
| `CircuitOptions.JSInteropDefaultCallTimeout`            | <span data-ttu-id="30ffe-260">Quantità massima di tempo di attesa del server prima del timeout di una chiamata asincrona di funzione JavaScript.</span><span class="sxs-lookup"><span data-stu-id="30ffe-260">Maximum amount of time the server waits before timing out an asynchronous JavaScript function invocation.</span></span> | <span data-ttu-id="30ffe-261">1 minuto</span><span class="sxs-lookup"><span data-stu-id="30ffe-261">1 minute</span></span> |
| `CircuitOptions.MaxBufferedUnacknowledgedRenderBatches` | <span data-ttu-id="30ffe-262">Numero massimo di batch di rendering non riconosciuti che il server conserva in memoria per circuito in un determinato momento per supportare una riconnessione affidabile.</span><span class="sxs-lookup"><span data-stu-id="30ffe-262">Maximum number of unacknowledged render batches the server keeps in memory per circuit at a given time to support robust reconnection.</span></span> <span data-ttu-id="30ffe-263">Dopo aver raggiunto il limite, il server interrompe la produzione di nuovi batch di rendering fino a quando uno o più batch non sono stati riconosciuti dal client.</span><span class="sxs-lookup"><span data-stu-id="30ffe-263">After reaching the limit, the server stops producing new render batches until one or more batches have been acknowledged by the client.</span></span> | <span data-ttu-id="30ffe-264">10</span><span class="sxs-lookup"><span data-stu-id="30ffe-264">10</span></span> |


| <span data-ttu-id="30ffe-265">SignalR e ASP.NET Limite Core</span><span class="sxs-lookup"><span data-stu-id="30ffe-265">SignalR and ASP.NET Core limit</span></span>             | <span data-ttu-id="30ffe-266">Descrizione</span><span class="sxs-lookup"><span data-stu-id="30ffe-266">Description</span></span> | <span data-ttu-id="30ffe-267">Predefinito</span><span class="sxs-lookup"><span data-stu-id="30ffe-267">Default</span></span> |
| ------------------------------------------ | ----------- | ------- |
| `CircuitOptions.MaximumReceiveMessageSize` | <span data-ttu-id="30ffe-268">Dimensione del messaggio per un singolo messaggio.</span><span class="sxs-lookup"><span data-stu-id="30ffe-268">Message size for an individual message.</span></span> | <span data-ttu-id="30ffe-269">32 KB</span><span class="sxs-lookup"><span data-stu-id="30ffe-269">32 KB</span></span> |

## <a name="interactions-with-the-browser-client"></a><span data-ttu-id="30ffe-270">Interazioni con il browser (client)</span><span class="sxs-lookup"><span data-stu-id="30ffe-270">Interactions with the browser (client)</span></span>

<span data-ttu-id="30ffe-271">Un client interagisce con il server tramite l'invio di eventi di interoperabilità JS e il completamento del rendering.</span><span class="sxs-lookup"><span data-stu-id="30ffe-271">A client interacts with the server through JS interop event dispatching and render completion.</span></span> <span data-ttu-id="30ffe-272">La comunicazione di interoperabilità JS va in entrambe le direzioni tra JavaScript e .NET:</span><span class="sxs-lookup"><span data-stu-id="30ffe-272">JS interop communication goes both ways between JavaScript and .NET:</span></span>

* <span data-ttu-id="30ffe-273">Gli eventi del browser vengono inviati dal client al server in modo asincrono.</span><span class="sxs-lookup"><span data-stu-id="30ffe-273">Browser events are dispatched from the client to the server in an asynchronous fashion.</span></span>
* <span data-ttu-id="30ffe-274">Il server risponde in modo asincrono nuovo il rendering dell'interfaccia utente in base alle esigenze.</span><span class="sxs-lookup"><span data-stu-id="30ffe-274">The server responds asynchronously rerendering the UI as necessary.</span></span>

### <a name="javascript-functions-invoked-from-net"></a><span data-ttu-id="30ffe-275">Funzioni JavaScript richiamate da .NET</span><span class="sxs-lookup"><span data-stu-id="30ffe-275">JavaScript functions invoked from .NET</span></span>

<span data-ttu-id="30ffe-276">Per le chiamate da metodi .NET a JavaScript:</span><span class="sxs-lookup"><span data-stu-id="30ffe-276">For calls from .NET methods to JavaScript:</span></span>

* <span data-ttu-id="30ffe-277">Tutte le chiamate hanno un timeout configurabile dopo <xref:System.OperationCanceledException> il quale hanno esito negativo, restituendo un al chiamante.</span><span class="sxs-lookup"><span data-stu-id="30ffe-277">All invocations have a configurable timeout after which they fail, returning a <xref:System.OperationCanceledException> to the caller.</span></span>
  * <span data-ttu-id="30ffe-278">Esiste un timeout predefinito per`CircuitOptions.JSInteropDefaultCallTimeout`le chiamate ( ) di un minuto.</span><span class="sxs-lookup"><span data-stu-id="30ffe-278">There's a default timeout for the calls (`CircuitOptions.JSInteropDefaultCallTimeout`) of one minute.</span></span> <span data-ttu-id="30ffe-279">Per configurare questo <xref:blazor/call-javascript-from-dotnet#harden-js-interop-calls>limite, vedere .</span><span class="sxs-lookup"><span data-stu-id="30ffe-279">To configure this limit, see <xref:blazor/call-javascript-from-dotnet#harden-js-interop-calls>.</span></span>
  * <span data-ttu-id="30ffe-280">È possibile fornire un token di annullamento per controllare l'annullamento per ogni chiamata.</span><span class="sxs-lookup"><span data-stu-id="30ffe-280">A cancellation token can be provided to control the cancellation on a per-call basis.</span></span> <span data-ttu-id="30ffe-281">Fare affidamento sul timeout di chiamata predefinito, se possibile, e per tutte le chiamate al client se viene fornito un token di annullamento.</span><span class="sxs-lookup"><span data-stu-id="30ffe-281">Rely on the default call timeout where possible and time-bound any call to the client if a cancellation token is provided.</span></span>
* <span data-ttu-id="30ffe-282">Il risultato di una chiamata JavaScript non può essere considerato attendibile.</span><span class="sxs-lookup"><span data-stu-id="30ffe-282">The result of a JavaScript call can't be trusted.</span></span> <span data-ttu-id="30ffe-283">Il Blazor client dell'app in esecuzione nel browser cerca la funzione JavaScript da richiamare.</span><span class="sxs-lookup"><span data-stu-id="30ffe-283">The Blazor app client running in the browser searches for the JavaScript function to invoke.</span></span> <span data-ttu-id="30ffe-284">La funzione viene richiamata e viene generato il risultato o un errore.</span><span class="sxs-lookup"><span data-stu-id="30ffe-284">The function is invoked, and either the result or an error is produced.</span></span> <span data-ttu-id="30ffe-285">Un client dannoso può tentare di:</span><span class="sxs-lookup"><span data-stu-id="30ffe-285">A malicious client can attempt to:</span></span>
  * <span data-ttu-id="30ffe-286">Causa un problema nell'app restituendo un errore dalla funzione JavaScript.</span><span class="sxs-lookup"><span data-stu-id="30ffe-286">Cause an issue in the app by returning an error from the JavaScript function.</span></span>
  * <span data-ttu-id="30ffe-287">Indurre un comportamento indesiderato sul server restituendo un risultato imprevisto dalla funzione JavaScript.</span><span class="sxs-lookup"><span data-stu-id="30ffe-287">Induce an unintended behavior on the server by returning an unexpected result from the JavaScript function.</span></span>

<span data-ttu-id="30ffe-288">Prendere le seguenti precauzioni per proteggersi dagli scenari precedenti:</span><span class="sxs-lookup"><span data-stu-id="30ffe-288">Take the following precautions to guard against the preceding scenarios:</span></span>

* <span data-ttu-id="30ffe-289">Eseguire il wrapping delle chiamate di interoperabilità JS all'interno di istruzioni [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) per tenere conto degli errori che potrebbero verificarsi durante le chiamate.</span><span class="sxs-lookup"><span data-stu-id="30ffe-289">Wrap JS interop calls within [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statements to account for errors that might occur during the invocations.</span></span> <span data-ttu-id="30ffe-290">Per altre informazioni, vedere <xref:blazor/handle-errors#javascript-interop>.</span><span class="sxs-lookup"><span data-stu-id="30ffe-290">For more information, see <xref:blazor/handle-errors#javascript-interop>.</span></span>
* <span data-ttu-id="30ffe-291">Convalidare i dati restituiti dalle chiamate di interoperabilità JS, inclusi i messaggi di errore, prima di eseguire qualsiasi azione.</span><span class="sxs-lookup"><span data-stu-id="30ffe-291">Validate data returned from JS interop invocations, including error messages, before taking any action.</span></span>

### <a name="net-methods-invoked-from-the-browser"></a><span data-ttu-id="30ffe-292">Metodi .NET richiamati dal browser</span><span class="sxs-lookup"><span data-stu-id="30ffe-292">.NET methods invoked from the browser</span></span>

<span data-ttu-id="30ffe-293">Non considerare attendibili le chiamate da JavaScript ai metodi .NET.</span><span class="sxs-lookup"><span data-stu-id="30ffe-293">Don't trust calls from JavaScript to .NET methods.</span></span> <span data-ttu-id="30ffe-294">Quando un metodo .NET viene esposto a JavaScript, considerare come viene richiamato il metodo .NET:When a .NET method is exposed to JavaScript, consider how the .NET method is invoked:</span><span class="sxs-lookup"><span data-stu-id="30ffe-294">When a .NET method is exposed to JavaScript, consider how the .NET method is invoked:</span></span>

* <span data-ttu-id="30ffe-295">Tratta qualsiasi metodo .NET esposto a JavaScript come se si trattasse di un endpoint pubblico per l'app.</span><span class="sxs-lookup"><span data-stu-id="30ffe-295">Treat any .NET method exposed to JavaScript as you would a public endpoint to the app.</span></span>
  * <span data-ttu-id="30ffe-296">Convalidare l'input.</span><span class="sxs-lookup"><span data-stu-id="30ffe-296">Validate input.</span></span>
    * <span data-ttu-id="30ffe-297">Assicurarsi che i valori siano compresi negli intervalli previsti.</span><span class="sxs-lookup"><span data-stu-id="30ffe-297">Ensure that values are within expected ranges.</span></span>
    * <span data-ttu-id="30ffe-298">Assicurarsi che l'utente disponga dell'autorizzazione per eseguire l'azione richiesta.</span><span class="sxs-lookup"><span data-stu-id="30ffe-298">Ensure that the user has permission to perform the action requested.</span></span>
  * <span data-ttu-id="30ffe-299">Non allocare una quantità eccessiva di risorse come parte della chiamata al metodo .NET.</span><span class="sxs-lookup"><span data-stu-id="30ffe-299">Don't allocate an excessive quantity of resources as part of the .NET method invocation.</span></span> <span data-ttu-id="30ffe-300">Ad esempio, eseguire controlli e porre limiti all'utilizzo della CPU e della memoria.</span><span class="sxs-lookup"><span data-stu-id="30ffe-300">For example, perform checks and place limits on CPU and memory use.</span></span>
  * <span data-ttu-id="30ffe-301">Tenere conto del fatto che i metodi statici e di istanza possono essere esposti ai client JavaScript.</span><span class="sxs-lookup"><span data-stu-id="30ffe-301">Take into account that static and instance methods can be exposed to JavaScript clients.</span></span> <span data-ttu-id="30ffe-302">Evitare di condividere lo stato tra le sessioni, a meno che la progettazione non richieda lo stato di condivisione con vincoli appropriati.</span><span class="sxs-lookup"><span data-stu-id="30ffe-302">Avoid sharing state across sessions unless the design calls for sharing state with appropriate constraints.</span></span>
    * <span data-ttu-id="30ffe-303">Per i metodi `DotNetReference` di istanza esposti tramite oggetti creati in origine tramite inserimento delle dipendenze (DI), gli oggetti devono essere registrati come oggetti con ambito.</span><span class="sxs-lookup"><span data-stu-id="30ffe-303">For instance methods exposed through `DotNetReference` objects that are originally created through dependency injection (DI), the objects should be registered as scoped objects.</span></span> <span data-ttu-id="30ffe-304">Questo vale per qualsiasi Blazor servizio DI utilizzato dall'app Server.</span><span class="sxs-lookup"><span data-stu-id="30ffe-304">This applies to any DI service that the Blazor Server app uses.</span></span>
    * <span data-ttu-id="30ffe-305">Per i metodi statici, evitare di stabilire uno stato che non può essere definito nel client a meno che l'app non condivida in modo esplicito lo stato in base alla progettazione tra tutti gli utenti in un'istanza del server.</span><span class="sxs-lookup"><span data-stu-id="30ffe-305">For static methods, avoid establishing state that can't be scoped to the client unless the app is explicitly sharing state by-design across all users on a server instance.</span></span>
  * <span data-ttu-id="30ffe-306">Evitare di passare i dati forniti dall'utente nei parametri alle chiamate JavaScript.</span><span class="sxs-lookup"><span data-stu-id="30ffe-306">Avoid passing user-supplied data in parameters to JavaScript calls.</span></span> <span data-ttu-id="30ffe-307">Se il passaggio dei dati nei parametri è assolutamente necessario, assicurarsi che il codice JavaScript gestisca il passaggio dei dati senza introdurre vulnerabilità [di cross-site scripting (XSS).](#cross-site-scripting-xss)</span><span class="sxs-lookup"><span data-stu-id="30ffe-307">If passing data in parameters is absolutely required, ensure that the JavaScript code handles passing the data without introducing [Cross-site scripting (XSS)](#cross-site-scripting-xss) vulnerabilities.</span></span> <span data-ttu-id="30ffe-308">Ad esempio, non scrivere dati forniti dall'utente nel modello DOM `innerHTML` (Document Object Model) impostando la proprietà di un elemento.</span><span class="sxs-lookup"><span data-stu-id="30ffe-308">For example, don't write user-supplied data to the Document Object Model (DOM) by setting the `innerHTML` property of an element.</span></span> <span data-ttu-id="30ffe-309">Prendere in considerazione l'utilizzo di `eval` criteri di sicurezza del contenuto [(CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP) per disabilitare e altre primitive JavaScript non sicure.</span><span class="sxs-lookup"><span data-stu-id="30ffe-309">Consider using [Content Security Policy (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP) to disable `eval` and other unsafe JavaScript primitives.</span></span>
* <span data-ttu-id="30ffe-310">Evitare di implementare l'invio personalizzato delle chiamate .NET in cima all'implementazione di invio del framework.</span><span class="sxs-lookup"><span data-stu-id="30ffe-310">Avoid implementing custom dispatching of .NET invocations on top of the framework's dispatching implementation.</span></span> <span data-ttu-id="30ffe-311">L'esposizione di metodi .NET al browser è Blazor uno scenario avanzato, non consigliato per lo sviluppo generale.</span><span class="sxs-lookup"><span data-stu-id="30ffe-311">Exposing .NET methods to the browser is an advanced scenario, not recommended for general Blazor development.</span></span>

### <a name="events"></a><span data-ttu-id="30ffe-312">Events</span><span class="sxs-lookup"><span data-stu-id="30ffe-312">Events</span></span>

<span data-ttu-id="30ffe-313">Gli eventi forniscono Blazor un punto di ingresso a un'app Server.Events provide an entry point to a Server app.</span><span class="sxs-lookup"><span data-stu-id="30ffe-313">Events provide an entry point to a Blazor Server app.</span></span> <span data-ttu-id="30ffe-314">Le stesse regole per la protezione degli endpoint Blazor nelle app Web si applicano alla gestione degli eventi nelle app server.</span><span class="sxs-lookup"><span data-stu-id="30ffe-314">The same rules for safeguarding endpoints in web apps apply to event handling in Blazor Server apps.</span></span> <span data-ttu-id="30ffe-315">Un client dannoso può inviare tutti i dati che desidera inviare come payload per un evento.</span><span class="sxs-lookup"><span data-stu-id="30ffe-315">A malicious client can send any data it wishes to send as the payload for an event.</span></span>

<span data-ttu-id="30ffe-316">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="30ffe-316">For example:</span></span>

* <span data-ttu-id="30ffe-317">Un evento di `<select>` modifica per un oggetto potrebbe inviare un valore che non è all'interno delle opzioni che l'app ha presentato al client.</span><span class="sxs-lookup"><span data-stu-id="30ffe-317">A change event for a `<select>` could send a value that isn't within the options that the app presented to the client.</span></span>
* <span data-ttu-id="30ffe-318">Un `<input>` può inviare dati di testo al server, ignorando la convalida lato client.</span><span class="sxs-lookup"><span data-stu-id="30ffe-318">An `<input>` could send any text data to the server, bypassing client-side validation.</span></span>

<span data-ttu-id="30ffe-319">L'app deve convalidare i dati per qualsiasi evento che gestisce l'app.</span><span class="sxs-lookup"><span data-stu-id="30ffe-319">The app must validate the data for any event that the app handles.</span></span> <span data-ttu-id="30ffe-320">Il Blazor framework [forma i componenti](xref:blazor/forms-validation) che eseguono convalide di base.</span><span class="sxs-lookup"><span data-stu-id="30ffe-320">The Blazor framework [forms components](xref:blazor/forms-validation) perform basic validations.</span></span> <span data-ttu-id="30ffe-321">Se l'app usa componenti di moduli personalizzati, è necessario scrivere codice personalizzato per convalidare i dati dell'evento in base alle esigenze.</span><span class="sxs-lookup"><span data-stu-id="30ffe-321">If the app uses custom forms components, custom code must be written to validate event data as appropriate.</span></span>

Blazor<span data-ttu-id="30ffe-322">Gli eventi del server sono asincroni, pertanto è possibile inviare più eventi al server prima che l'app abbia il tempo di reagire producendo un nuovo rendering.</span><span class="sxs-lookup"><span data-stu-id="30ffe-322"> Server events are asynchronous, so multiple events can be dispatched to the server before the app has time to react by producing a new render.</span></span> <span data-ttu-id="30ffe-323">Ciò ha alcune implicazioni di sicurezza da considerare.</span><span class="sxs-lookup"><span data-stu-id="30ffe-323">This has some security implications to consider.</span></span> <span data-ttu-id="30ffe-324">La limitazione delle azioni client nell'app deve essere eseguita all'interno dei gestori eventi e non dipende dallo stato di visualizzazione di cui è stato eseguito il rendering corrente.</span><span class="sxs-lookup"><span data-stu-id="30ffe-324">Limiting client actions in the app must be performed inside event handlers and not depend on the current rendered view state.</span></span>

<span data-ttu-id="30ffe-325">Si consideri un componente contatore che deve consentire all'utente di incrementare un contatore per un massimo di tre volte.</span><span class="sxs-lookup"><span data-stu-id="30ffe-325">Consider a counter component that should allow a user to increment a counter a maximum of three times.</span></span> <span data-ttu-id="30ffe-326">Il pulsante per incrementare il contatore `count`è basato in modo condizionale sul valore di :</span><span class="sxs-lookup"><span data-stu-id="30ffe-326">The button to increment the counter is conditionally based on the value of `count`:</span></span>

```razor
<p>Count: @count<p>

@if (count < 3)
{
    <button @onclick="IncrementCount" value="Increment count" />
}

@code 
{
    private int count = 0;

    private void IncrementCount()
    {
        count++;
    }
}
```

<span data-ttu-id="30ffe-327">Un client può inviare uno o più eventi di incremento prima che il framework produca un nuovo rendering di questo componente.</span><span class="sxs-lookup"><span data-stu-id="30ffe-327">A client can dispatch one or more increment events before the framework produces a new render of this component.</span></span> <span data-ttu-id="30ffe-328">Il risultato `count` è che l'oggetto può essere incrementato *di tre volte* dall'utente perché il pulsante non viene rimosso dall'interfaccia utente abbastanza rapidamente.</span><span class="sxs-lookup"><span data-stu-id="30ffe-328">The result is that the `count` can be incremented *over three times* by the user because the button isn't removed by the UI quickly enough.</span></span> <span data-ttu-id="30ffe-329">Il modo corretto per raggiungere `count` il limite di tre incrementi è illustrato nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="30ffe-329">The correct way to achieve the limit of three `count` increments is shown in the following example:</span></span>

```razor
<p>Count: @count<p>

@if (count < 3)
{
    <button @onclick="IncrementCount" value="Increment count" />
}

@code 
{
    private int count = 0;

    private void IncrementCount()
    {
        if (count < 3)
        {
            count++;
        }
    }
}
```

<span data-ttu-id="30ffe-330">Aggiungendo il `if (count < 3) { ... }` controllo all'interno del `count` gestore, la decisione di incrementare si basa sullo stato corrente dell'app.</span><span class="sxs-lookup"><span data-stu-id="30ffe-330">By adding the `if (count < 3) { ... }` check inside the handler, the decision to increment `count` is based on the current app state.</span></span> <span data-ttu-id="30ffe-331">La decisione non si basa sullo stato dell'interfaccia utente come nell'esempio precedente, che potrebbe essere temporaneamente obsoleto.</span><span class="sxs-lookup"><span data-stu-id="30ffe-331">The decision isn't based on the state of the UI as it was in the previous example, which might be temporarily stale.</span></span>

### <a name="guard-against-multiple-dispatches"></a><span data-ttu-id="30ffe-332">Guardia contro più dispacci</span><span class="sxs-lookup"><span data-stu-id="30ffe-332">Guard against multiple dispatches</span></span>

<span data-ttu-id="30ffe-333">Se un callback di eventi richiama un'operazione a esecuzione prolungata in modo asincrono, ad esempio il recupero di dati da un database o da un servizio esterno, è consigliabile usare una protezione.</span><span class="sxs-lookup"><span data-stu-id="30ffe-333">If an event callback invokes a long running operation asynchronously, such as fetching data from an external service or database, consider using a guard.</span></span> <span data-ttu-id="30ffe-334">La guardia può impedire all'utente di accodare più operazioni mentre l'operazione è in corso con il feedback visivo.</span><span class="sxs-lookup"><span data-stu-id="30ffe-334">The guard can prevent the user from queueing up multiple operations while the operation is in progress with visual feedback.</span></span> <span data-ttu-id="30ffe-335">Il codice del `isLoading` `true` componente `GetForecastAsync` seguente imposta su while ottiene i dati dal server.</span><span class="sxs-lookup"><span data-stu-id="30ffe-335">The following component code sets `isLoading` to `true` while `GetForecastAsync` obtains data from the server.</span></span> <span data-ttu-id="30ffe-336">Mentre `isLoading` `true`è , il pulsante è disabilitato nell'interfaccia utente:</span><span class="sxs-lookup"><span data-stu-id="30ffe-336">While `isLoading` is `true`, the button is disabled in the UI:</span></span>

```razor
@page "/fetchdata"
@using BlazorServerSample.Data
@inject WeatherForecastService ForecastService

<button disabled="@isLoading" @onclick="UpdateForecasts">Update</button>

@code {
    private bool isLoading;
    private WeatherForecast[] forecasts;

    private async Task UpdateForecasts()
    {
        if (!isLoading)
        {
            isLoading = true;
            forecasts = await ForecastService.GetForecastAsync(DateTime.Now);
            isLoading = false;
        }
    }
}
```

<span data-ttu-id="30ffe-337">Il modello di protezione illustrato nell'esempio precedente funziona se l'operazione in background viene eseguita in modo asincrono con il `async` - `await` modello.</span><span class="sxs-lookup"><span data-stu-id="30ffe-337">The guard pattern demonstrated in the preceding example works if the background operation is executed asynchronously with the `async`-`await` pattern.</span></span>

### <a name="cancel-early-and-avoid-use-after-dispose"></a><span data-ttu-id="30ffe-338">Annullare in anticipo ed evitare l'uso dopo la smaltimento</span><span class="sxs-lookup"><span data-stu-id="30ffe-338">Cancel early and avoid use-after-dispose</span></span>

<span data-ttu-id="30ffe-339">Oltre a utilizzare una guardia come descritto nella sezione [Protezione contro più dispacci,](#guard-against-multiple-dispatches) è consigliabile utilizzare un <xref:System.Threading.CancellationToken> oggetto per annullare le operazioni a esecuzione prolungata quando il componente viene eliminato.</span><span class="sxs-lookup"><span data-stu-id="30ffe-339">In addition to using a guard as described in the [Guard against multiple dispatches](#guard-against-multiple-dispatches) section, consider using a <xref:System.Threading.CancellationToken> to cancel long-running operations when the component is disposed.</span></span> <span data-ttu-id="30ffe-340">Questo approccio ha l'ulteriore vantaggio di evitare l'uso dopo la smaltimento nei componenti:This approach has the added vantaggio of avoiding *use-after-dispose* in components:</span><span class="sxs-lookup"><span data-stu-id="30ffe-340">This approach has the added benefit of avoiding *use-after-dispose* in components:</span></span>

```razor
@implements IDisposable

...

@code {
    private readonly CancellationTokenSource TokenSource = 
        new CancellationTokenSource();

    private async Task UpdateForecasts()
    {
        ...

        forecasts = await ForecastService.GetForecastAsync(DateTime.Now, 
            TokenSource.Token);

        if (TokenSource.Token.IsCancellationRequested)
        {
           return;
        }

        ...
    }

    public void Dispose()
    {
        TokenSource.Cancel();
    }
}
```

### <a name="avoid-events-that-produce-large-amounts-of-data"></a><span data-ttu-id="30ffe-341">Evitare eventi che producono grandi quantità di datiAvoid events that produce large amounts of data</span><span class="sxs-lookup"><span data-stu-id="30ffe-341">Avoid events that produce large amounts of data</span></span>

<span data-ttu-id="30ffe-342">Alcuni eventi DOM, `oninput` `onscroll`ad esempio o , possono produrre una grande quantità di dati.</span><span class="sxs-lookup"><span data-stu-id="30ffe-342">Some DOM events, such as `oninput` or `onscroll`, can produce a large amount of data.</span></span> <span data-ttu-id="30ffe-343">Evitare di Blazor utilizzare questi eventi nelle app server.</span><span class="sxs-lookup"><span data-stu-id="30ffe-343">Avoid using these events in Blazor server apps.</span></span>

## <a name="additional-security-guidance"></a><span data-ttu-id="30ffe-344">Ulteriori indicazioni sulla sicurezza</span><span class="sxs-lookup"><span data-stu-id="30ffe-344">Additional security guidance</span></span>

<span data-ttu-id="30ffe-345">Le linee guida per la Blazor protezione di ASP.NET le app Core si applicano alle app server e sono illustrate nelle sezioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="30ffe-345">The guidance for securing ASP.NET Core apps apply to Blazor Server apps and are covered in the following sections:</span></span>

* [<span data-ttu-id="30ffe-346">Registrazione e dati sensibili</span><span class="sxs-lookup"><span data-stu-id="30ffe-346">Logging and sensitive data</span></span>](#logging-and-sensitive-data)
* [<span data-ttu-id="30ffe-347">Proteggere le informazioni in transito con HTTPS</span><span class="sxs-lookup"><span data-stu-id="30ffe-347">Protect information in transit with HTTPS</span></span>](#protect-information-in-transit-with-https)
* <span data-ttu-id="30ffe-348">[Cross-site scripting (XSS)](#cross-site-scripting-xss))</span><span class="sxs-lookup"><span data-stu-id="30ffe-348">[Cross-site scripting (XSS)](#cross-site-scripting-xss))</span></span>
* [<span data-ttu-id="30ffe-349">Protezione tra origini</span><span class="sxs-lookup"><span data-stu-id="30ffe-349">Cross-origin protection</span></span>](#cross-origin-protection)
* [<span data-ttu-id="30ffe-350">Click-jacking</span><span class="sxs-lookup"><span data-stu-id="30ffe-350">Click-jacking</span></span>](#click-jacking)
* [<span data-ttu-id="30ffe-351">Reindirizzamenti aperti</span><span class="sxs-lookup"><span data-stu-id="30ffe-351">Open redirects</span></span>](#open-redirects)

### <a name="logging-and-sensitive-data"></a><span data-ttu-id="30ffe-352">Registrazione e dati sensibili</span><span class="sxs-lookup"><span data-stu-id="30ffe-352">Logging and sensitive data</span></span>

<span data-ttu-id="30ffe-353">Le interazioni di interoperabilità JS tra il client e <xref:Microsoft.Extensions.Logging.ILogger> il server vengono registrate nei log del server con istanze.</span><span class="sxs-lookup"><span data-stu-id="30ffe-353">JS interop interactions between the client and server are recorded in the server's logs with <xref:Microsoft.Extensions.Logging.ILogger> instances.</span></span> Blazor<span data-ttu-id="30ffe-354">evita la registrazione di informazioni riservate, ad esempio eventi effettivi o input e output di interoperabilità JS.</span><span class="sxs-lookup"><span data-stu-id="30ffe-354"> avoids logging sensitive information, such as actual events or JS interop inputs and outputs.</span></span>

<span data-ttu-id="30ffe-355">Quando si verifica un errore sul server, il framework notifica al client e annulla la sessione.</span><span class="sxs-lookup"><span data-stu-id="30ffe-355">When an error occurs on the server, the framework notifies the client and tears down the session.</span></span> <span data-ttu-id="30ffe-356">Per impostazione predefinita, il client riceve un messaggio di errore generico che può essere visualizzato negli strumenti di sviluppo del browser.</span><span class="sxs-lookup"><span data-stu-id="30ffe-356">By default, the client receives a generic error message that can be seen in the browser's developer tools.</span></span>

<span data-ttu-id="30ffe-357">L'errore sul lato client non include lo stack di chiamate e non fornisce dettagli sulla causa dell'errore, ma i log del server contengono tali informazioni.</span><span class="sxs-lookup"><span data-stu-id="30ffe-357">The client-side error doesn't include the callstack and doesn't provide detail on the cause of the error, but server logs do contain such information.</span></span> <span data-ttu-id="30ffe-358">Ai fini dello sviluppo, le informazioni sensibili sugli errori possono essere rese disponibili al client abilitando errori dettagliati.</span><span class="sxs-lookup"><span data-stu-id="30ffe-358">For development purposes, sensitive error information can be made available to the client by enabling detailed errors.</span></span>

<span data-ttu-id="30ffe-359">Abilita errori dettagliati con:</span><span class="sxs-lookup"><span data-stu-id="30ffe-359">Enable detailed errors with:</span></span>

* <span data-ttu-id="30ffe-360">`CircuitOptions.DetailedErrors`.</span><span class="sxs-lookup"><span data-stu-id="30ffe-360">`CircuitOptions.DetailedErrors`.</span></span>
* <span data-ttu-id="30ffe-361">`DetailedErrors`chiave di configurazione.</span><span class="sxs-lookup"><span data-stu-id="30ffe-361">`DetailedErrors` configuration key.</span></span> <span data-ttu-id="30ffe-362">Ad esempio, `ASPNETCORE_DETAILEDERRORS` impostare la variabile `true`di ambiente su un valore di .</span><span class="sxs-lookup"><span data-stu-id="30ffe-362">For example, set the `ASPNETCORE_DETAILEDERRORS` environment variable to a value of `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="30ffe-363">L'esposizione di informazioni sugli errori ai client su Internet è un rischio per la sicurezza che deve sempre essere evitato.</span><span class="sxs-lookup"><span data-stu-id="30ffe-363">Exposing error information to clients on the Internet is a security risk that should always be avoided.</span></span>

### <a name="protect-information-in-transit-with-https"></a><span data-ttu-id="30ffe-364">Proteggere le informazioni in transito con HTTPS</span><span class="sxs-lookup"><span data-stu-id="30ffe-364">Protect information in transit with HTTPS</span></span>

Blazor<span data-ttu-id="30ffe-365">Il SignalR server utilizza per la comunicazione tra il client e il server.</span><span class="sxs-lookup"><span data-stu-id="30ffe-365"> Server uses SignalR for communication between the client and the server.</span></span> Blazor<span data-ttu-id="30ffe-366">Server utilizza in SignalR genere il trasporto che negozia, che in genere è WebSockets.</span><span class="sxs-lookup"><span data-stu-id="30ffe-366"> Server normally uses the transport that SignalR negotiates, which is typically WebSockets.</span></span>

Blazor<span data-ttu-id="30ffe-367">Server non garantisce l'integrità e la riservatezza dei dati inviati tra il server e il client.</span><span class="sxs-lookup"><span data-stu-id="30ffe-367"> Server doesn't ensure the integrity and confidentiality of the data sent between the server and the client.</span></span> <span data-ttu-id="30ffe-368">Utilizzare sempre HTTPS.</span><span class="sxs-lookup"><span data-stu-id="30ffe-368">Always use HTTPS.</span></span>

### <a name="cross-site-scripting-xss"></a><span data-ttu-id="30ffe-369">Cross-site scripting (XSS)</span><span class="sxs-lookup"><span data-stu-id="30ffe-369">Cross-site scripting (XSS)</span></span>

<span data-ttu-id="30ffe-370">Il Cross-site scripting (XSS) consente a un'entità non autorizzata di eseguire logica arbitraria nel contesto del browser.</span><span class="sxs-lookup"><span data-stu-id="30ffe-370">Cross-site scripting (XSS) allows an unauthorized party to execute arbitrary logic in the context of the browser.</span></span> <span data-ttu-id="30ffe-371">Un'app compromessa potrebbe potenzialmente eseguire codice arbitrario sul client.</span><span class="sxs-lookup"><span data-stu-id="30ffe-371">A compromised app could potentially run arbitrary code on the client.</span></span> <span data-ttu-id="30ffe-372">La vulnerabilità potrebbe essere utilizzato per eseguire potenzialmente una serie di azioni dannose contro il server:</span><span class="sxs-lookup"><span data-stu-id="30ffe-372">The vulnerability could be used to potentially perform a number of malicious actions against the server:</span></span>

* <span data-ttu-id="30ffe-373">Inviare eventi falsi/non validi al server.</span><span class="sxs-lookup"><span data-stu-id="30ffe-373">Dispatch fake/invalid events to the server.</span></span>
* <span data-ttu-id="30ffe-374">Completamenti di rendering non riusciti/non validi dell'invio.</span><span class="sxs-lookup"><span data-stu-id="30ffe-374">Dispatch fail/invalid render completions.</span></span>
* <span data-ttu-id="30ffe-375">Evitare di inviare completamenti di rendering.</span><span class="sxs-lookup"><span data-stu-id="30ffe-375">Avoid dispatching render completions.</span></span>
* <span data-ttu-id="30ffe-376">Inviare chiamate di interoperabilità da JavaScript a .NET.</span><span class="sxs-lookup"><span data-stu-id="30ffe-376">Dispatch interop calls from JavaScript to .NET.</span></span>
* <span data-ttu-id="30ffe-377">Modificare la risposta delle chiamate di interoperabilità da .NET a JavaScript.</span><span class="sxs-lookup"><span data-stu-id="30ffe-377">Modify the response of interop calls from .NET to JavaScript.</span></span>
* <span data-ttu-id="30ffe-378">Evitare di inviare .NET ai risultati di interoperabilità JS.</span><span class="sxs-lookup"><span data-stu-id="30ffe-378">Avoid dispatching .NET to JS interop results.</span></span>

<span data-ttu-id="30ffe-379">Il Blazor framework del server adotta le misure necessarie per la protezione da alcune delle minacce precedenti:</span><span class="sxs-lookup"><span data-stu-id="30ffe-379">The Blazor Server framework takes steps to protect against some of the preceding threats:</span></span>

* <span data-ttu-id="30ffe-380">Interrompe la produzione di nuovi aggiornamenti dell'interfaccia utente se il client non riconosce i batch di rendering.</span><span class="sxs-lookup"><span data-stu-id="30ffe-380">Stops producing new UI updates if the client isn't acknowledging render batches.</span></span> <span data-ttu-id="30ffe-381">Configurato `CircuitOptions.MaxBufferedUnacknowledgedRenderBatches`con .</span><span class="sxs-lookup"><span data-stu-id="30ffe-381">Configured with `CircuitOptions.MaxBufferedUnacknowledgedRenderBatches`.</span></span>
* <span data-ttu-id="30ffe-382">Timeout di qualsiasi chiamata da .NET a JavaScript dopo un minuto senza ricevere una risposta dal client.</span><span class="sxs-lookup"><span data-stu-id="30ffe-382">Times out any .NET to JavaScript call after one minute without receiving a response from the client.</span></span> <span data-ttu-id="30ffe-383">Configurato `CircuitOptions.JSInteropDefaultCallTimeout`con .</span><span class="sxs-lookup"><span data-stu-id="30ffe-383">Configured with `CircuitOptions.JSInteropDefaultCallTimeout`.</span></span>
* <span data-ttu-id="30ffe-384">Esegue la convalida di base su tutti gli input provenienti dal browser durante l'interoperabilità JS:</span><span class="sxs-lookup"><span data-stu-id="30ffe-384">Performs basic validation on all input coming from the browser during JS interop:</span></span>
  * <span data-ttu-id="30ffe-385">I riferimenti .NET sono validi e del tipo previsto dal metodo .NET.</span><span class="sxs-lookup"><span data-stu-id="30ffe-385">.NET references are valid and of the type expected by the .NET method.</span></span>
  * <span data-ttu-id="30ffe-386">Il formato non corretto dei dati non è corretto.</span><span class="sxs-lookup"><span data-stu-id="30ffe-386">The data isn't malformed.</span></span>
  * <span data-ttu-id="30ffe-387">Il numero corretto di argomenti per il metodo è presente nel payload.</span><span class="sxs-lookup"><span data-stu-id="30ffe-387">The correct number of arguments for the method are present in the payload.</span></span>
  * <span data-ttu-id="30ffe-388">Gli argomenti o il risultato possono essere deserializzati correttamente prima di richiamare il metodo .</span><span class="sxs-lookup"><span data-stu-id="30ffe-388">The arguments or result can be deserialized correctly before invoking the method.</span></span>
* <span data-ttu-id="30ffe-389">Esegue la convalida di base in tutti gli input provenienti dal browser dagli eventi inviati:</span><span class="sxs-lookup"><span data-stu-id="30ffe-389">Performs basic validation in all input coming from the browser from dispatched events:</span></span>
  * <span data-ttu-id="30ffe-390">L'evento ha un tipo valido.</span><span class="sxs-lookup"><span data-stu-id="30ffe-390">The event has a valid type.</span></span>
  * <span data-ttu-id="30ffe-391">I dati per l'evento possono essere deserializzati.</span><span class="sxs-lookup"><span data-stu-id="30ffe-391">The data for the event can be deserialized.</span></span>
  * <span data-ttu-id="30ffe-392">C'è un gestore eventi associato all'evento.</span><span class="sxs-lookup"><span data-stu-id="30ffe-392">There's an event handler associated with the event.</span></span>

<span data-ttu-id="30ffe-393">Oltre alle misure di sicurezza implementate dal framework, l'app deve essere codificata dallo sviluppatore per proteggersi dalle minacce e intraprendere le azioni appropriate:In addition to the safeguards that the framework implements, the app must be coded by the developer to safeguard against threats and take appropriate actions:</span><span class="sxs-lookup"><span data-stu-id="30ffe-393">In addition to the safeguards that the framework implements, the app must be coded by the developer to safeguard against threats and take appropriate actions:</span></span>

* <span data-ttu-id="30ffe-394">Convalidare sempre i dati durante la gestione degli eventi.</span><span class="sxs-lookup"><span data-stu-id="30ffe-394">Always validate data when handling events.</span></span>
* <span data-ttu-id="30ffe-395">Adottare le misure appropriate dopo aver ricevuto dati non validi:</span><span class="sxs-lookup"><span data-stu-id="30ffe-395">Take appropriate action upon receiving invalid data:</span></span>
  * <span data-ttu-id="30ffe-396">Ignorare i dati e restituire.</span><span class="sxs-lookup"><span data-stu-id="30ffe-396">Ignore the data and return.</span></span> <span data-ttu-id="30ffe-397">Ciò consente all'app di continuare l'elaborazione delle richieste.</span><span class="sxs-lookup"><span data-stu-id="30ffe-397">This allows the app to continue processing requests.</span></span>
  * <span data-ttu-id="30ffe-398">Se l'app determina che l'input è illegittimo e non può essere prodotto dal client legittimo, genera un'eccezione.</span><span class="sxs-lookup"><span data-stu-id="30ffe-398">If the app determines that the input is illegitimate and couldn't be produced by legitimate client, throw an exception.</span></span> <span data-ttu-id="30ffe-399">La generazione di un'eccezione abbatte il circuito e termina la sessione.</span><span class="sxs-lookup"><span data-stu-id="30ffe-399">Throwing an exception tears down the circuit and ends the session.</span></span>
* <span data-ttu-id="30ffe-400">Non considerare attendibile il messaggio di errore fornito dai completamenti batch di rendering inclusi nei log.</span><span class="sxs-lookup"><span data-stu-id="30ffe-400">Don't trust the error message provided by render batch completions included in the logs.</span></span> <span data-ttu-id="30ffe-401">L'errore viene fornito dal client e non può in genere essere considerato attendibile, in quanto il client potrebbe essere compromesso.</span><span class="sxs-lookup"><span data-stu-id="30ffe-401">The error is provided by the client and can't generally be trusted, as the client might be compromised.</span></span>
* <span data-ttu-id="30ffe-402">Non considerare attendibile l'input nelle chiamate di interoperabilità JS in entrambe le direzioni tra JavaScript e metodi .NET.</span><span class="sxs-lookup"><span data-stu-id="30ffe-402">Don't trust the input on JS interop calls in either direction between JavaScript and .NET methods.</span></span>
* <span data-ttu-id="30ffe-403">L'app è responsabile della convalida che il contenuto degli argomenti e dei risultati sia valido, anche se gli argomenti o i risultati vengono deserializzati correttamente.</span><span class="sxs-lookup"><span data-stu-id="30ffe-403">The app is responsible for validating that the content of arguments and results are valid, even if the arguments or results are correctly deserialized.</span></span>

<span data-ttu-id="30ffe-404">Affinché esista una vulnerabilità XSS, l'app deve incorporare l'input dell'utente nella pagina di cui è stato eseguito il rendering.</span><span class="sxs-lookup"><span data-stu-id="30ffe-404">For a XSS vulnerability to exist, the app must incorporate user input in the rendered page.</span></span> Blazor<span data-ttu-id="30ffe-405">I componenti server eseguono un passaggio in fase di compilazione in cui il markup in un file *con estensione razor* viene trasformato in logica procedurale di C.</span><span class="sxs-lookup"><span data-stu-id="30ffe-405"> Server components execute a compile-time step where the markup in a *.razor* file is transformed into procedural C# logic.</span></span> <span data-ttu-id="30ffe-406">In fase di esecuzione, la logica di C' compila una *struttura ad albero* di rendering che descrive gli elementi, il testo e i componenti figlio.</span><span class="sxs-lookup"><span data-stu-id="30ffe-406">At runtime, the C# logic builds a *render tree* describing the elements, text, and child components.</span></span> <span data-ttu-id="30ffe-407">Questo viene applicato al DOM del browser tramite una sequenza di istruzioni JavaScript (o viene serializzato in HTML nel caso di prerendering):</span><span class="sxs-lookup"><span data-stu-id="30ffe-407">This is applied to the browser's DOM via a sequence of JavaScript instructions (or is serialized to HTML in the case of prerendering):</span></span>

* <span data-ttu-id="30ffe-408">L'input dell'utente eseguito il `@someStringValue`rendering tramite la normale sintassi Razor (ad esempio, ) non espone una vulnerabilità XSS perché la sintassi Razor viene aggiunta al DOM tramite comandi che possono scrivere solo testo.</span><span class="sxs-lookup"><span data-stu-id="30ffe-408">User input rendered via normal Razor syntax (for example, `@someStringValue`) doesn't expose a XSS vulnerability because the Razor syntax is added to the DOM via commands that can only write text.</span></span> <span data-ttu-id="30ffe-409">Anche se il valore include markup HTML, il valore viene visualizzato come testo statico.</span><span class="sxs-lookup"><span data-stu-id="30ffe-409">Even if the value includes HTML markup, the value is displayed as static text.</span></span> <span data-ttu-id="30ffe-410">Durante il prerendering, l'output è codificato in HTML, che visualizza anche il contenuto come testo statico.</span><span class="sxs-lookup"><span data-stu-id="30ffe-410">When prerendering, the output is HTML-encoded, which also displays the content as static text.</span></span>
* <span data-ttu-id="30ffe-411">I tag di script non sono consentiti e non devono essere inclusi nell'albero di rendering dei componenti dell'app.</span><span class="sxs-lookup"><span data-stu-id="30ffe-411">Script tags aren't allowed and shouldn't be included in the app's component render tree.</span></span> <span data-ttu-id="30ffe-412">Se un tag di script è incluso nel markup di un componente, viene generato un errore in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="30ffe-412">If a script tag is included in a component's markup, a compile-time error is generated.</span></span>
* <span data-ttu-id="30ffe-413">Gli autori dei componenti possono creare componenti in C, senza usare Razor.</span><span class="sxs-lookup"><span data-stu-id="30ffe-413">Component authors can author components in C# without using Razor.</span></span> <span data-ttu-id="30ffe-414">L'autore del componente è responsabile dell'utilizzo delle API corrette quando si genera l'output.</span><span class="sxs-lookup"><span data-stu-id="30ffe-414">The component author is responsible for using the correct APIs when emitting output.</span></span> <span data-ttu-id="30ffe-415">Ad esempio, `builder.AddContent(0, someUserSuppliedString)` utilizzare e *non* `builder.AddMarkupContent(0, someUserSuppliedString)`, poiché quest'ultimo potrebbe creare una vulnerabilità XSS.</span><span class="sxs-lookup"><span data-stu-id="30ffe-415">For example, use `builder.AddContent(0, someUserSuppliedString)` and *not* `builder.AddMarkupContent(0, someUserSuppliedString)`, as the latter could create a XSS vulnerability.</span></span>

<span data-ttu-id="30ffe-416">Come parte della protezione contro gli attacchi XSS, prendere in considerazione l'implementazione di attenuazioni XSS, ad esempio [Content Security Policy (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP).</span><span class="sxs-lookup"><span data-stu-id="30ffe-416">As part of protecting against XSS attacks, consider implementing XSS mitigations, such as [Content Security Policy (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP).</span></span>

<span data-ttu-id="30ffe-417">Per altre informazioni, vedere <xref:security/cross-site-scripting>.</span><span class="sxs-lookup"><span data-stu-id="30ffe-417">For more information, see <xref:security/cross-site-scripting>.</span></span>

### <a name="cross-origin-protection"></a><span data-ttu-id="30ffe-418">Protezione tra origini</span><span class="sxs-lookup"><span data-stu-id="30ffe-418">Cross-origin protection</span></span>

<span data-ttu-id="30ffe-419">Gli attacchi tra origini coinvolgono un client di origine diversa che esegue un'azione sul server.</span><span class="sxs-lookup"><span data-stu-id="30ffe-419">Cross-origin attacks involve a client from a different origin performing an action against the server.</span></span> <span data-ttu-id="30ffe-420">L'azione dannoso è in genere una richiesta GET o un modulo POST (Cross-Site Request Forgery, CSRF), ma l'apertura di un WebSocket dannoso è anche possibile.</span><span class="sxs-lookup"><span data-stu-id="30ffe-420">The malicious action is typically a GET request or a form POST (Cross-Site Request Forgery, CSRF), but opening a malicious WebSocket is also possible.</span></span> Blazor<span data-ttu-id="30ffe-421">Le app server offrono [le SignalR stesse garanzie che qualsiasi altra app che usa il protocollo hub offre:](xref:signalr/security)</span><span class="sxs-lookup"><span data-stu-id="30ffe-421"> Server apps offer [the same guarantees that any other SignalR app using the hub protocol offer](xref:signalr/security):</span></span>

* Blazor<span data-ttu-id="30ffe-422">È possibile accedere alle app server tra origini diverse, a meno che non vengano adottate misure aggiuntive per impedirlo.</span><span class="sxs-lookup"><span data-stu-id="30ffe-422"> Server apps can be accessed cross-origin unless additional measures are taken to prevent it.</span></span> <span data-ttu-id="30ffe-423">Per disabilitare l'accesso tra origini, disabilitare CORS nell'endpoint aggiungendo il `DisableCorsAttribute` middleware CORS alla pipeline e aggiungendo il server ai metadati dell'endpoint Blazor o limitare il set di origini consentite [configurando SignalR la condivisione](xref:signalr/security#cross-origin-resource-sharing)delle risorse tra origini.</span><span class="sxs-lookup"><span data-stu-id="30ffe-423">To disable cross-origin access, either disable CORS in the endpoint by adding the CORS middleware to the pipeline and adding the `DisableCorsAttribute` to the Blazor endpoint metadata or limit the set of allowed origins by [configuring SignalR for cross-origin resource sharing](xref:signalr/security#cross-origin-resource-sharing).</span></span>
* <span data-ttu-id="30ffe-424">Se CORS è abilitato, potrebbero essere necessari passaggi aggiuntivi per proteggere l'app in base alla configurazione di CORS.</span><span class="sxs-lookup"><span data-stu-id="30ffe-424">If CORS is enabled, extra steps might be required to protect the app depending on the CORS configuration.</span></span> <span data-ttu-id="30ffe-425">Se CORS è abilitato a livello globale, Blazor CORS può `DisableCorsAttribute` essere disabilitato per `hub.MapBlazorHub()`l'hub Server aggiungendo i metadati ai metadati dell'endpoint dopo la chiamata a .</span><span class="sxs-lookup"><span data-stu-id="30ffe-425">If CORS is globally enabled, CORS can be disabled for the Blazor Server hub by adding the `DisableCorsAttribute` metadata to the endpoint metadata after calling `hub.MapBlazorHub()`.</span></span>

<span data-ttu-id="30ffe-426">Per altre informazioni, vedere <xref:security/anti-request-forgery>.</span><span class="sxs-lookup"><span data-stu-id="30ffe-426">For more information, see <xref:security/anti-request-forgery>.</span></span>

### <a name="click-jacking"></a><span data-ttu-id="30ffe-427">Click-jacking</span><span class="sxs-lookup"><span data-stu-id="30ffe-427">Click-jacking</span></span>

<span data-ttu-id="30ffe-428">Click-jacking comporta il rendering `<iframe>` di un sito come un all'interno di un sito da un'origine diversa al fine di indurre l'utente a eseguire azioni sul sito sotto attacco.</span><span class="sxs-lookup"><span data-stu-id="30ffe-428">Click-jacking involves rendering a site as an `<iframe>` inside a site from a different origin in order to trick the user into performing actions on the site under attack.</span></span>

<span data-ttu-id="30ffe-429">Per proteggere un'app dal `<iframe>`rendering all'interno di un `X-Frame-Options` oggetto , usa Criteri di sicurezza del contenuto [(CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP) e l'intestazione.</span><span class="sxs-lookup"><span data-stu-id="30ffe-429">To protect an app from rendering inside of an `<iframe>`, use [Content Security Policy (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP) and the `X-Frame-Options` header.</span></span> <span data-ttu-id="30ffe-430">Per ulteriori informazioni, consultate [Documenti Web MDN: Opzioni X-Frame](https://developer.mozilla.org/docs/Web/HTTP/Headers/X-Frame-Options).</span><span class="sxs-lookup"><span data-stu-id="30ffe-430">For more information, see [MDN web docs: X-Frame-Options](https://developer.mozilla.org/docs/Web/HTTP/Headers/X-Frame-Options).</span></span>

### <a name="open-redirects"></a><span data-ttu-id="30ffe-431">Reindirizzamenti aperti</span><span class="sxs-lookup"><span data-stu-id="30ffe-431">Open redirects</span></span>

<span data-ttu-id="30ffe-432">Quando Blazor viene avviata una sessione dell'app Server, il server esegue la convalida di base degli URL inviati come parte dell'avvio della sessione.</span><span class="sxs-lookup"><span data-stu-id="30ffe-432">When a Blazor Server app session starts, the server performs basic validation of the URLs sent as part of starting the session.</span></span> <span data-ttu-id="30ffe-433">Il framework verifica che l'URL di base sia un elemento padre dell'URL corrente prima di stabilire il circuito.</span><span class="sxs-lookup"><span data-stu-id="30ffe-433">The framework checks that the base URL is a parent of the current URL before establishing the circuit.</span></span> <span data-ttu-id="30ffe-434">Il framework non esegue ulteriori controlli.</span><span class="sxs-lookup"><span data-stu-id="30ffe-434">No additional checks are performed by the framework.</span></span>

<span data-ttu-id="30ffe-435">Quando un utente seleziona un collegamento sul client, l'URL del collegamento viene inviato al server, che determina l'azione da eseguire.</span><span class="sxs-lookup"><span data-stu-id="30ffe-435">When a user selects a link on the client, the URL for the link is sent to the server, which determines what action to take.</span></span> <span data-ttu-id="30ffe-436">Ad esempio, l'app può eseguire una navigazione sul lato client o indicare al browser per passare alla nuova posizione.</span><span class="sxs-lookup"><span data-stu-id="30ffe-436">For example, the app may perform a client-side navigation or indicate to the browser to go to the new location.</span></span>

<span data-ttu-id="30ffe-437">I componenti possono anche attivare le `NavigationManager`richieste di navigazione a livello di codice tramite l'utilizzo di .</span><span class="sxs-lookup"><span data-stu-id="30ffe-437">Components can also trigger navigation requests programatically through the use of `NavigationManager`.</span></span> <span data-ttu-id="30ffe-438">In tali scenari, l'app potrebbe eseguire una navigazione sul lato client o indicare al browser di passare alla nuova posizione.</span><span class="sxs-lookup"><span data-stu-id="30ffe-438">In such scenarios, the app might perform a client-side navigation or indicate to the browser to go to the new location.</span></span>

<span data-ttu-id="30ffe-439">I componenti devono:</span><span class="sxs-lookup"><span data-stu-id="30ffe-439">Components must:</span></span>

* <span data-ttu-id="30ffe-440">Evitare di utilizzare l'input dell'utente come parte degli argomenti della chiamata di navigazione.</span><span class="sxs-lookup"><span data-stu-id="30ffe-440">Avoid using user input as part of the navigation call arguments.</span></span>
* <span data-ttu-id="30ffe-441">Convalidare gli argomenti per assicurarsi che la destinazione sia consentita dall'app.</span><span class="sxs-lookup"><span data-stu-id="30ffe-441">Validate arguments to ensure that the target is allowed by the app.</span></span>

<span data-ttu-id="30ffe-442">In caso contrario, un utente malintenzionato può forzare il browser a passare a un sito controllato da un utente malintenzionato.</span><span class="sxs-lookup"><span data-stu-id="30ffe-442">Otherwise, a malicious user can force the browser to go to an attacker-controlled site.</span></span> <span data-ttu-id="30ffe-443">In questo scenario, l'utente malintenzionato trucchi l'applicazione `NavigationManager.Navigate` in utilizzare l'input dell'utente come parte della chiamata del metodo.</span><span class="sxs-lookup"><span data-stu-id="30ffe-443">In this scenario, the attacker tricks the app into using some user input as part of the invocation of the `NavigationManager.Navigate` method.</span></span>

<span data-ttu-id="30ffe-444">Questo consiglio si applica anche quando si esegue il rendering dei collegamenti come parte dell'app:</span><span class="sxs-lookup"><span data-stu-id="30ffe-444">This advice also applies when rendering links as part of the app:</span></span>

* <span data-ttu-id="30ffe-445">Se possibile, utilizzare collegamenti relativi.</span><span class="sxs-lookup"><span data-stu-id="30ffe-445">If possible, use relative links.</span></span>
* <span data-ttu-id="30ffe-446">Verificare che le destinazioni dei collegamenti assoluti siano valide prima di includerle in una pagina.</span><span class="sxs-lookup"><span data-stu-id="30ffe-446">Validate that absolute link destinations are valid before including them in a page.</span></span>

<span data-ttu-id="30ffe-447">Per altre informazioni, vedere <xref:security/preventing-open-redirects>.</span><span class="sxs-lookup"><span data-stu-id="30ffe-447">For more information, see <xref:security/preventing-open-redirects>.</span></span>

## <a name="authentication-and-authorization"></a><span data-ttu-id="30ffe-448">Autenticazione e autorizzazione</span><span class="sxs-lookup"><span data-stu-id="30ffe-448">Authentication and authorization</span></span>

<span data-ttu-id="30ffe-449">Per istruzioni su autenticazione <xref:security/blazor/index>e autorizzazione, vedere .</span><span class="sxs-lookup"><span data-stu-id="30ffe-449">For guidance on authentication and authorization, see <xref:security/blazor/index>.</span></span>

## <a name="security-checklist"></a><span data-ttu-id="30ffe-450">Elenco di controllo relativo alla sicurezza</span><span class="sxs-lookup"><span data-stu-id="30ffe-450">Security checklist</span></span>

<span data-ttu-id="30ffe-451">Il seguente elenco di considerazioni sulla sicurezza non è esaustivo:</span><span class="sxs-lookup"><span data-stu-id="30ffe-451">The following list of security considerations isn't comprehensive:</span></span>

* <span data-ttu-id="30ffe-452">Convalidare gli argomenti dagli eventi.</span><span class="sxs-lookup"><span data-stu-id="30ffe-452">Validate arguments from events.</span></span>
* <span data-ttu-id="30ffe-453">Convalidare gli input e i risultati dalle chiamate di interoperabilità JS.</span><span class="sxs-lookup"><span data-stu-id="30ffe-453">Validate inputs and results from JS interop calls.</span></span>
* <span data-ttu-id="30ffe-454">Evitare di utilizzare (o convalidare in anticipo) l'input dell'utente per le chiamate di interoperabilità da .NET a JS.</span><span class="sxs-lookup"><span data-stu-id="30ffe-454">Avoid using (or validate beforehand) user input for .NET to JS interop calls.</span></span>
* <span data-ttu-id="30ffe-455">Impedire al client di allocare una quantità di memoria non associata.</span><span class="sxs-lookup"><span data-stu-id="30ffe-455">Prevent the client from allocating an unbound amount of memory.</span></span>
  * <span data-ttu-id="30ffe-456">Dati all'interno del componente.</span><span class="sxs-lookup"><span data-stu-id="30ffe-456">Data within the component.</span></span>
  * <span data-ttu-id="30ffe-457">`DotNetObject`riferimenti restituiti al client.</span><span class="sxs-lookup"><span data-stu-id="30ffe-457">`DotNetObject` references returned to the client.</span></span>
* <span data-ttu-id="30ffe-458">Protezione da più dispacci.</span><span class="sxs-lookup"><span data-stu-id="30ffe-458">Guard against multiple dispatches.</span></span>
* <span data-ttu-id="30ffe-459">Annullare le operazioni a esecuzione prolungata quando il componente viene eliminato.</span><span class="sxs-lookup"><span data-stu-id="30ffe-459">Cancel long-running operations when the component is disposed.</span></span>
* <span data-ttu-id="30ffe-460">Evitare eventi che producono grandi quantità di dati.</span><span class="sxs-lookup"><span data-stu-id="30ffe-460">Avoid events that produce large amounts of data.</span></span>
* <span data-ttu-id="30ffe-461">Evitare di usare l'input dell'utente come parte delle chiamate `NavigationManager.Navigate` e convalidare l'input dell'utente per gli URL rispetto a un set di origini consentite, se inevitabile.</span><span class="sxs-lookup"><span data-stu-id="30ffe-461">Avoid using user input as part of calls to `NavigationManager.Navigate` and validate user input for URLs against a set of allowed origins first if unavoidable.</span></span>
* <span data-ttu-id="30ffe-462">Non prendere decisioni di autorizzazione in base allo stato dell'interfaccia utente, ma solo dallo stato del componente.</span><span class="sxs-lookup"><span data-stu-id="30ffe-462">Don't make authorization decisions based on the state of the UI but only from component state.</span></span>
* <span data-ttu-id="30ffe-463">Valutare la possibilità di utilizzare i criteri di [sicurezza del contenuto (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP) per la protezione contro gli attacchi XSS.</span><span class="sxs-lookup"><span data-stu-id="30ffe-463">Consider using [Content Security Policy (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP) to protect against XSS attacks.</span></span>
* <span data-ttu-id="30ffe-464">Prendi in considerazione l'uso di CSP e [X-Frame-Options](https://developer.mozilla.org/docs/Web/HTTP/Headers/X-Frame-Options) per proteggerti dal click-jacking.</span><span class="sxs-lookup"><span data-stu-id="30ffe-464">Consider using CSP and [X-Frame-Options](https://developer.mozilla.org/docs/Web/HTTP/Headers/X-Frame-Options) to protect against click-jacking.</span></span>
* <span data-ttu-id="30ffe-465">Verificare che le impostazioni CORS siano appropriate Blazor quando si abilita CORS o si disabilita esplicitamente CORS per le app.</span><span class="sxs-lookup"><span data-stu-id="30ffe-465">Ensure CORS settings are appropriate when enabling CORS or explicitly disable CORS for Blazor apps.</span></span>
* <span data-ttu-id="30ffe-466">Verifica che i limiti sul lato server per l'app Blazor forniscano un'esperienza utente accettabile senza livelli di rischio inaccettabili.</span><span class="sxs-lookup"><span data-stu-id="30ffe-466">Test to ensure that the server-side limits for the Blazor app provide an acceptable user experience without unacceptable levels of risk.</span></span>
