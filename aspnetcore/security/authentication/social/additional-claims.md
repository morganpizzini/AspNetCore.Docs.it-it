---
<span data-ttu-id="c96bc-101">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="c96bc-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c96bc-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c96bc-102">'Blazor'</span></span>
- <span data-ttu-id="c96bc-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c96bc-103">'Identity'</span></span>
- <span data-ttu-id="c96bc-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c96bc-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="c96bc-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c96bc-105">'Razor'</span></span>
- <span data-ttu-id="c96bc-106">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="c96bc-106">'SignalR' uid:</span></span> 

---
# <a name="persist-additional-claims-and-tokens-from-external-providers-in-aspnet-core"></a><span data-ttu-id="c96bc-107">Mantieni attestazioni e token aggiuntivi da provider esterni in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c96bc-107">Persist additional claims and tokens from external providers in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="c96bc-108">Un'app ASP.NET Core può creare attestazioni e token aggiuntivi da provider di autenticazione esterni, ad esempio Facebook, Google, Microsoft e Twitter.</span><span class="sxs-lookup"><span data-stu-id="c96bc-108">An ASP.NET Core app can establish additional claims and tokens from external authentication providers, such as Facebook, Google, Microsoft, and Twitter.</span></span> <span data-ttu-id="c96bc-109">Ogni provider rivela informazioni diverse sugli utenti sulla propria piattaforma, ma il modello per la ricezione e la trasformazione dei dati utente in attestazioni aggiuntive è lo stesso.</span><span class="sxs-lookup"><span data-stu-id="c96bc-109">Each provider reveals different information about users on its platform, but the pattern for receiving and transforming user data into additional claims is the same.</span></span>

<span data-ttu-id="c96bc-110">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="c96bc-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="c96bc-111">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="c96bc-111">Prerequisites</span></span>

<span data-ttu-id="c96bc-112">Decidere quali provider di autenticazione esterni supportare nell'app.</span><span class="sxs-lookup"><span data-stu-id="c96bc-112">Decide which external authentication providers to support in the app.</span></span> <span data-ttu-id="c96bc-113">Per ogni provider, registrare l'app e ottenere un ID client e un segreto client.</span><span class="sxs-lookup"><span data-stu-id="c96bc-113">For each provider, register the app and obtain a client ID and client secret.</span></span> <span data-ttu-id="c96bc-114">Per altre informazioni, vedere <xref:security/authentication/social/index>.</span><span class="sxs-lookup"><span data-stu-id="c96bc-114">For more information, see <xref:security/authentication/social/index>.</span></span> <span data-ttu-id="c96bc-115">L'app di esempio usa il [provider di autenticazione Google](xref:security/authentication/google-logins).</span><span class="sxs-lookup"><span data-stu-id="c96bc-115">The sample app uses the [Google authentication provider](xref:security/authentication/google-logins).</span></span>

## <a name="set-the-client-id-and-client-secret"></a><span data-ttu-id="c96bc-116">Impostare l'ID client e il segreto client</span><span class="sxs-lookup"><span data-stu-id="c96bc-116">Set the client ID and client secret</span></span>

<span data-ttu-id="c96bc-117">Il provider di autenticazione OAuth stabilisce una relazione di trust con un'app che usa un ID client e un segreto client.</span><span class="sxs-lookup"><span data-stu-id="c96bc-117">The OAuth authentication provider establishes a trust relationship with an app using a client ID and client secret.</span></span> <span data-ttu-id="c96bc-118">I valori relativi all'ID client e al segreto client vengono creati per l'app dal provider di autenticazione esterno quando l'app viene registrata con il provider.</span><span class="sxs-lookup"><span data-stu-id="c96bc-118">Client ID and client secret values are created for the app by the external authentication provider when the app is registered with the provider.</span></span> <span data-ttu-id="c96bc-119">Ogni provider esterno usato dall'app deve essere configurato in modo indipendente con l'ID client e il segreto client del provider.</span><span class="sxs-lookup"><span data-stu-id="c96bc-119">Each external provider that the app uses must be configured independently with the provider's client ID and client secret.</span></span> <span data-ttu-id="c96bc-120">Per ulteriori informazioni, vedere gli argomenti del provider di autenticazione esterno applicabili allo scenario:</span><span class="sxs-lookup"><span data-stu-id="c96bc-120">For more information, see the external authentication provider topics that apply to your scenario:</span></span>

* [<span data-ttu-id="c96bc-121">Autenticazione Facebook</span><span class="sxs-lookup"><span data-stu-id="c96bc-121">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="c96bc-122">Autenticazione Google</span><span class="sxs-lookup"><span data-stu-id="c96bc-122">Google authentication</span></span>](xref:security/authentication/google-logins)
* [<span data-ttu-id="c96bc-123">Autenticazione Microsoft</span><span class="sxs-lookup"><span data-stu-id="c96bc-123">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="c96bc-124">Autenticazione Twitter</span><span class="sxs-lookup"><span data-stu-id="c96bc-124">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="c96bc-125">Altri provider di autenticazione</span><span class="sxs-lookup"><span data-stu-id="c96bc-125">Other authentication providers</span></span>](xref:security/authentication/otherlogins)
* [<span data-ttu-id="c96bc-126">OpenIdConnect</span><span class="sxs-lookup"><span data-stu-id="c96bc-126">OpenIdConnect</span></span>](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)

<span data-ttu-id="c96bc-127">L'app di esempio configura il provider di autenticazione Google con un ID client e un segreto client forniti da Google:</span><span class="sxs-lookup"><span data-stu-id="c96bc-127">The sample app configures the Google authentication provider with a client ID and client secret provided by Google:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=4,9)]

## <a name="establish-the-authentication-scope"></a><span data-ttu-id="c96bc-128">Definire l'ambito di autenticazione</span><span class="sxs-lookup"><span data-stu-id="c96bc-128">Establish the authentication scope</span></span>

<span data-ttu-id="c96bc-129">Specificare l'elenco di autorizzazioni da recuperare dal provider specificando <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*> .</span><span class="sxs-lookup"><span data-stu-id="c96bc-129">Specify the list of permissions to retrieve from the provider by specifying the <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*>.</span></span> <span data-ttu-id="c96bc-130">Gli ambiti di autenticazione per i provider esterni comuni sono riportati nella tabella seguente.</span><span class="sxs-lookup"><span data-stu-id="c96bc-130">Authentication scopes for common external providers appear in the following table.</span></span>

| <span data-ttu-id="c96bc-131">Provider</span><span class="sxs-lookup"><span data-stu-id="c96bc-131">Provider</span></span>  | <span data-ttu-id="c96bc-132">Ambito</span><span class="sxs-lookup"><span data-stu-id="c96bc-132">Scope</span></span>                                                            |
| ---
<span data-ttu-id="c96bc-133">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="c96bc-133">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c96bc-134">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c96bc-134">'Blazor'</span></span>
- <span data-ttu-id="c96bc-135">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c96bc-135">'Identity'</span></span>
- <span data-ttu-id="c96bc-136">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c96bc-136">'Let's Encrypt'</span></span>
- <span data-ttu-id="c96bc-137">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c96bc-137">'Razor'</span></span>
- <span data-ttu-id="c96bc-138">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="c96bc-138">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c96bc-139">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="c96bc-139">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c96bc-140">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c96bc-140">'Blazor'</span></span>
- <span data-ttu-id="c96bc-141">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c96bc-141">'Identity'</span></span>
- <span data-ttu-id="c96bc-142">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c96bc-142">'Let's Encrypt'</span></span>
- <span data-ttu-id="c96bc-143">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c96bc-143">'Razor'</span></span>
- <span data-ttu-id="c96bc-144">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="c96bc-144">'SignalR' uid:</span></span> 

<span data-ttu-id="c96bc-145">----- | Titolo---: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="c96bc-145">----- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c96bc-146">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c96bc-146">'Blazor'</span></span>
- <span data-ttu-id="c96bc-147">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c96bc-147">'Identity'</span></span>
- <span data-ttu-id="c96bc-148">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c96bc-148">'Let's Encrypt'</span></span>
- <span data-ttu-id="c96bc-149">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c96bc-149">'Razor'</span></span>
- <span data-ttu-id="c96bc-150">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="c96bc-150">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c96bc-151">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="c96bc-151">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c96bc-152">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c96bc-152">'Blazor'</span></span>
- <span data-ttu-id="c96bc-153">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c96bc-153">'Identity'</span></span>
- <span data-ttu-id="c96bc-154">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c96bc-154">'Let's Encrypt'</span></span>
- <span data-ttu-id="c96bc-155">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c96bc-155">'Razor'</span></span>
- <span data-ttu-id="c96bc-156">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="c96bc-156">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c96bc-157">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="c96bc-157">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c96bc-158">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c96bc-158">'Blazor'</span></span>
- <span data-ttu-id="c96bc-159">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c96bc-159">'Identity'</span></span>
- <span data-ttu-id="c96bc-160">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c96bc-160">'Let's Encrypt'</span></span>
- <span data-ttu-id="c96bc-161">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c96bc-161">'Razor'</span></span>
- <span data-ttu-id="c96bc-162">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="c96bc-162">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c96bc-163">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="c96bc-163">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c96bc-164">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c96bc-164">'Blazor'</span></span>
- <span data-ttu-id="c96bc-165">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c96bc-165">'Identity'</span></span>
- <span data-ttu-id="c96bc-166">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c96bc-166">'Let's Encrypt'</span></span>
- <span data-ttu-id="c96bc-167">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c96bc-167">'Razor'</span></span>
- <span data-ttu-id="c96bc-168">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="c96bc-168">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c96bc-169">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="c96bc-169">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c96bc-170">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c96bc-170">'Blazor'</span></span>
- <span data-ttu-id="c96bc-171">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c96bc-171">'Identity'</span></span>
- <span data-ttu-id="c96bc-172">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c96bc-172">'Let's Encrypt'</span></span>
- <span data-ttu-id="c96bc-173">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c96bc-173">'Razor'</span></span>
- <span data-ttu-id="c96bc-174">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="c96bc-174">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c96bc-175">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="c96bc-175">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c96bc-176">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c96bc-176">'Blazor'</span></span>
- <span data-ttu-id="c96bc-177">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c96bc-177">'Identity'</span></span>
- <span data-ttu-id="c96bc-178">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c96bc-178">'Let's Encrypt'</span></span>
- <span data-ttu-id="c96bc-179">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c96bc-179">'Razor'</span></span>
- <span data-ttu-id="c96bc-180">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="c96bc-180">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c96bc-181">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="c96bc-181">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c96bc-182">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c96bc-182">'Blazor'</span></span>
- <span data-ttu-id="c96bc-183">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c96bc-183">'Identity'</span></span>
- <span data-ttu-id="c96bc-184">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c96bc-184">'Let's Encrypt'</span></span>
- <span data-ttu-id="c96bc-185">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c96bc-185">'Razor'</span></span>
- <span data-ttu-id="c96bc-186">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="c96bc-186">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c96bc-187">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="c96bc-187">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c96bc-188">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c96bc-188">'Blazor'</span></span>
- <span data-ttu-id="c96bc-189">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c96bc-189">'Identity'</span></span>
- <span data-ttu-id="c96bc-190">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c96bc-190">'Let's Encrypt'</span></span>
- <span data-ttu-id="c96bc-191">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c96bc-191">'Razor'</span></span>
- <span data-ttu-id="c96bc-192">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="c96bc-192">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c96bc-193">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="c96bc-193">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c96bc-194">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c96bc-194">'Blazor'</span></span>
- <span data-ttu-id="c96bc-195">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c96bc-195">'Identity'</span></span>
- <span data-ttu-id="c96bc-196">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c96bc-196">'Let's Encrypt'</span></span>
- <span data-ttu-id="c96bc-197">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c96bc-197">'Razor'</span></span>
- <span data-ttu-id="c96bc-198">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="c96bc-198">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c96bc-199">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="c96bc-199">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c96bc-200">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c96bc-200">'Blazor'</span></span>
- <span data-ttu-id="c96bc-201">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c96bc-201">'Identity'</span></span>
- <span data-ttu-id="c96bc-202">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c96bc-202">'Let's Encrypt'</span></span>
- <span data-ttu-id="c96bc-203">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c96bc-203">'Razor'</span></span>
- <span data-ttu-id="c96bc-204">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="c96bc-204">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c96bc-205">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="c96bc-205">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c96bc-206">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c96bc-206">'Blazor'</span></span>
- <span data-ttu-id="c96bc-207">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c96bc-207">'Identity'</span></span>
- <span data-ttu-id="c96bc-208">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c96bc-208">'Let's Encrypt'</span></span>
- <span data-ttu-id="c96bc-209">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c96bc-209">'Razor'</span></span>
- <span data-ttu-id="c96bc-210">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="c96bc-210">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c96bc-211">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="c96bc-211">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c96bc-212">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c96bc-212">'Blazor'</span></span>
- <span data-ttu-id="c96bc-213">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c96bc-213">'Identity'</span></span>
- <span data-ttu-id="c96bc-214">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c96bc-214">'Let's Encrypt'</span></span>
- <span data-ttu-id="c96bc-215">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c96bc-215">'Razor'</span></span>
- <span data-ttu-id="c96bc-216">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="c96bc-216">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c96bc-217">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="c96bc-217">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c96bc-218">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c96bc-218">'Blazor'</span></span>
- <span data-ttu-id="c96bc-219">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c96bc-219">'Identity'</span></span>
- <span data-ttu-id="c96bc-220">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c96bc-220">'Let's Encrypt'</span></span>
- <span data-ttu-id="c96bc-221">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c96bc-221">'Razor'</span></span>
- <span data-ttu-id="c96bc-222">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="c96bc-222">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c96bc-223">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="c96bc-223">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c96bc-224">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c96bc-224">'Blazor'</span></span>
- <span data-ttu-id="c96bc-225">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c96bc-225">'Identity'</span></span>
- <span data-ttu-id="c96bc-226">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c96bc-226">'Let's Encrypt'</span></span>
- <span data-ttu-id="c96bc-227">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c96bc-227">'Razor'</span></span>
- <span data-ttu-id="c96bc-228">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="c96bc-228">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c96bc-229">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="c96bc-229">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c96bc-230">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c96bc-230">'Blazor'</span></span>
- <span data-ttu-id="c96bc-231">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c96bc-231">'Identity'</span></span>
- <span data-ttu-id="c96bc-232">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c96bc-232">'Let's Encrypt'</span></span>
- <span data-ttu-id="c96bc-233">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c96bc-233">'Razor'</span></span>
- <span data-ttu-id="c96bc-234">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="c96bc-234">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c96bc-235">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="c96bc-235">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c96bc-236">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c96bc-236">'Blazor'</span></span>
- <span data-ttu-id="c96bc-237">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c96bc-237">'Identity'</span></span>
- <span data-ttu-id="c96bc-238">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c96bc-238">'Let's Encrypt'</span></span>
- <span data-ttu-id="c96bc-239">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c96bc-239">'Razor'</span></span>
- <span data-ttu-id="c96bc-240">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="c96bc-240">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c96bc-241">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="c96bc-241">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c96bc-242">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c96bc-242">'Blazor'</span></span>
- <span data-ttu-id="c96bc-243">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c96bc-243">'Identity'</span></span>
- <span data-ttu-id="c96bc-244">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c96bc-244">'Let's Encrypt'</span></span>
- <span data-ttu-id="c96bc-245">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c96bc-245">'Razor'</span></span>
- <span data-ttu-id="c96bc-246">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="c96bc-246">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c96bc-247">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="c96bc-247">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c96bc-248">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c96bc-248">'Blazor'</span></span>
- <span data-ttu-id="c96bc-249">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c96bc-249">'Identity'</span></span>
- <span data-ttu-id="c96bc-250">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c96bc-250">'Let's Encrypt'</span></span>
- <span data-ttu-id="c96bc-251">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c96bc-251">'Razor'</span></span>
- <span data-ttu-id="c96bc-252">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="c96bc-252">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c96bc-253">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="c96bc-253">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c96bc-254">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c96bc-254">'Blazor'</span></span>
- <span data-ttu-id="c96bc-255">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c96bc-255">'Identity'</span></span>
- <span data-ttu-id="c96bc-256">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c96bc-256">'Let's Encrypt'</span></span>
- <span data-ttu-id="c96bc-257">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c96bc-257">'Razor'</span></span>
- <span data-ttu-id="c96bc-258">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="c96bc-258">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c96bc-259">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="c96bc-259">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c96bc-260">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c96bc-260">'Blazor'</span></span>
- <span data-ttu-id="c96bc-261">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c96bc-261">'Identity'</span></span>
- <span data-ttu-id="c96bc-262">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c96bc-262">'Let's Encrypt'</span></span>
- <span data-ttu-id="c96bc-263">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c96bc-263">'Razor'</span></span>
- <span data-ttu-id="c96bc-264">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="c96bc-264">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c96bc-265">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="c96bc-265">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c96bc-266">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c96bc-266">'Blazor'</span></span>
- <span data-ttu-id="c96bc-267">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c96bc-267">'Identity'</span></span>
- <span data-ttu-id="c96bc-268">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c96bc-268">'Let's Encrypt'</span></span>
- <span data-ttu-id="c96bc-269">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c96bc-269">'Razor'</span></span>
- <span data-ttu-id="c96bc-270">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="c96bc-270">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c96bc-271">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="c96bc-271">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c96bc-272">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c96bc-272">'Blazor'</span></span>
- <span data-ttu-id="c96bc-273">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c96bc-273">'Identity'</span></span>
- <span data-ttu-id="c96bc-274">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c96bc-274">'Let's Encrypt'</span></span>
- <span data-ttu-id="c96bc-275">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c96bc-275">'Razor'</span></span>
- <span data-ttu-id="c96bc-276">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="c96bc-276">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c96bc-277">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="c96bc-277">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c96bc-278">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c96bc-278">'Blazor'</span></span>
- <span data-ttu-id="c96bc-279">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c96bc-279">'Identity'</span></span>
- <span data-ttu-id="c96bc-280">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c96bc-280">'Let's Encrypt'</span></span>
- <span data-ttu-id="c96bc-281">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c96bc-281">'Razor'</span></span>
- <span data-ttu-id="c96bc-282">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="c96bc-282">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c96bc-283">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="c96bc-283">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c96bc-284">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c96bc-284">'Blazor'</span></span>
- <span data-ttu-id="c96bc-285">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c96bc-285">'Identity'</span></span>
- <span data-ttu-id="c96bc-286">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c96bc-286">'Let's Encrypt'</span></span>
- <span data-ttu-id="c96bc-287">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c96bc-287">'Razor'</span></span>
- <span data-ttu-id="c96bc-288">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="c96bc-288">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c96bc-289">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="c96bc-289">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c96bc-290">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c96bc-290">'Blazor'</span></span>
- <span data-ttu-id="c96bc-291">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c96bc-291">'Identity'</span></span>
- <span data-ttu-id="c96bc-292">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c96bc-292">'Let's Encrypt'</span></span>
- <span data-ttu-id="c96bc-293">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c96bc-293">'Razor'</span></span>
- <span data-ttu-id="c96bc-294">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="c96bc-294">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c96bc-295">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="c96bc-295">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c96bc-296">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c96bc-296">'Blazor'</span></span>
- <span data-ttu-id="c96bc-297">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c96bc-297">'Identity'</span></span>
- <span data-ttu-id="c96bc-298">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c96bc-298">'Let's Encrypt'</span></span>
- <span data-ttu-id="c96bc-299">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c96bc-299">'Razor'</span></span>
- <span data-ttu-id="c96bc-300">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="c96bc-300">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c96bc-301">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="c96bc-301">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c96bc-302">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c96bc-302">'Blazor'</span></span>
- <span data-ttu-id="c96bc-303">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c96bc-303">'Identity'</span></span>
- <span data-ttu-id="c96bc-304">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c96bc-304">'Let's Encrypt'</span></span>
- <span data-ttu-id="c96bc-305">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c96bc-305">'Razor'</span></span>
- <span data-ttu-id="c96bc-306">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="c96bc-306">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c96bc-307">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="c96bc-307">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c96bc-308">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c96bc-308">'Blazor'</span></span>
- <span data-ttu-id="c96bc-309">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c96bc-309">'Identity'</span></span>
- <span data-ttu-id="c96bc-310">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c96bc-310">'Let's Encrypt'</span></span>
- <span data-ttu-id="c96bc-311">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c96bc-311">'Razor'</span></span>
- <span data-ttu-id="c96bc-312">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="c96bc-312">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c96bc-313">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="c96bc-313">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c96bc-314">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c96bc-314">'Blazor'</span></span>
- <span data-ttu-id="c96bc-315">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c96bc-315">'Identity'</span></span>
- <span data-ttu-id="c96bc-316">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c96bc-316">'Let's Encrypt'</span></span>
- <span data-ttu-id="c96bc-317">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c96bc-317">'Razor'</span></span>
- <span data-ttu-id="c96bc-318">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="c96bc-318">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c96bc-319">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="c96bc-319">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c96bc-320">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c96bc-320">'Blazor'</span></span>
- <span data-ttu-id="c96bc-321">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c96bc-321">'Identity'</span></span>
- <span data-ttu-id="c96bc-322">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c96bc-322">'Let's Encrypt'</span></span>
- <span data-ttu-id="c96bc-323">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c96bc-323">'Razor'</span></span>
- <span data-ttu-id="c96bc-324">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="c96bc-324">'SignalR' uid:</span></span> 

<span data-ttu-id="c96bc-325">-------------------------------- | | Facebook | `https://www.facebook.com/dialog/oauth`                          |
| Google | `https://www.googleapis.com/auth/userinfo.profile`               |
| Microsoft | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| Twitter |`https://api.twitter.com/oauth/authenticate`                     |</span><span class="sxs-lookup"><span data-stu-id="c96bc-325">-------------------------------- | | Facebook  | `https://www.facebook.com/dialog/oauth`                          |
| Google    | `https://www.googleapis.com/auth/userinfo.profile`               |
| Microsoft | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| Twitter   | `https://api.twitter.com/oauth/authenticate`                     |</span></span>

<span data-ttu-id="c96bc-326">Nell'app di esempio, `userinfo.profile` l'ambito di Google viene aggiunto automaticamente dal framework quando <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> viene chiamato su <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="c96bc-326">In the sample app, Google's `userinfo.profile` scope is automatically added by the framework when <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> is called on the <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder>.</span></span> <span data-ttu-id="c96bc-327">Se l'app richiede ambiti aggiuntivi, aggiungerli alle opzioni.</span><span class="sxs-lookup"><span data-stu-id="c96bc-327">If the app requires additional scopes, add them to the options.</span></span> <span data-ttu-id="c96bc-328">Nell'esempio seguente `https://www.googleapis.com/auth/user.birthday.read` viene aggiunto l'ambito Google per recuperare il compleanno di un utente:</span><span class="sxs-lookup"><span data-stu-id="c96bc-328">In the following example, the Google `https://www.googleapis.com/auth/user.birthday.read` scope is added in order to retrieve a user's birthday:</span></span>

```csharp
options.Scope.Add("https://www.googleapis.com/auth/user.birthday.read");
```

## <a name="map-user-data-keys-and-create-claims"></a><span data-ttu-id="c96bc-329">Eseguire il mapping delle chiavi di dati utente e creare attestazioni</span><span class="sxs-lookup"><span data-stu-id="c96bc-329">Map user data keys and create claims</span></span>

<span data-ttu-id="c96bc-330">Nelle opzioni del provider specificare un <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> o <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> per ogni chiave/sottochiave nei dati utente JSON del provider esterno per l'identità dell'app da leggere durante l'accesso.</span><span class="sxs-lookup"><span data-stu-id="c96bc-330">In the provider's options, specify a <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> or <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> for each key/subkey in the external provider's JSON user data for the app identity to read on sign in.</span></span> <span data-ttu-id="c96bc-331">Per ulteriori informazioni sui tipi di attestazione, vedere <xref:System.Security.Claims.ClaimTypes> .</span><span class="sxs-lookup"><span data-stu-id="c96bc-331">For more information on claim types, see <xref:System.Security.Claims.ClaimTypes>.</span></span>

<span data-ttu-id="c96bc-332">L'app di esempio crea attestazioni delle impostazioni locali ( `urn:google:locale` ) e immagini ( `urn:google:picture` ) dalle `locale` `picture` chiavi e nei dati utente di Google:</span><span class="sxs-lookup"><span data-stu-id="c96bc-332">The sample app creates locale (`urn:google:locale`) and picture (`urn:google:picture`) claims from the `locale` and `picture` keys in Google user data:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=13-14)]

<span data-ttu-id="c96bc-333">In `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync` , un <xref:Microsoft.AspNetCore.Identity.IdentityUser> ( `ApplicationUser` ) è connesso all'app con <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*> .</span><span class="sxs-lookup"><span data-stu-id="c96bc-333">In `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync`, an <xref:Microsoft.AspNetCore.Identity.IdentityUser> (`ApplicationUser`) is signed into the app with <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*>.</span></span> <span data-ttu-id="c96bc-334">Durante il processo di accesso, il <xref:Microsoft.AspNetCore.Identity.UserManager%601> può archiviare `ApplicationUser` attestazioni per i dati utente disponibili da <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*> .</span><span class="sxs-lookup"><span data-stu-id="c96bc-334">During the sign in process, the <xref:Microsoft.AspNetCore.Identity.UserManager%601> can store an `ApplicationUser` claims for user data available from the <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*>.</span></span>

<span data-ttu-id="c96bc-335">Nell'app di esempio `OnPostConfirmationAsync` (*account/ExternalLogin. cshtml. cs*) vengono stabilite le attestazioni delle impostazioni locali ( `urn:google:locale` ) e immagine ( `urn:google:picture` ) per l'accesso `ApplicationUser` , inclusa un'attestazione per <xref:System.Security.Claims.ClaimTypes.GivenName> :</span><span class="sxs-lookup"><span data-stu-id="c96bc-335">In the sample app, `OnPostConfirmationAsync` (*Account/ExternalLogin.cshtml.cs*) establishes the locale (`urn:google:locale`) and picture (`urn:google:picture`) claims for the signed in `ApplicationUser`, including a claim for <xref:System.Security.Claims.ClaimTypes.GivenName>:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=35-51)]

<span data-ttu-id="c96bc-336">Per impostazione predefinita, le attestazioni di un utente vengono archiviate nel cookie di autenticazione.</span><span class="sxs-lookup"><span data-stu-id="c96bc-336">By default, a user's claims are stored in the authentication cookie.</span></span> <span data-ttu-id="c96bc-337">Se il cookie di autenticazione è troppo grande, può causare un errore dell'app perché:</span><span class="sxs-lookup"><span data-stu-id="c96bc-337">If the authentication cookie is too large, it can cause the app to fail because:</span></span>

* <span data-ttu-id="c96bc-338">Il browser rileva che l'intestazione del cookie è troppo lungo.</span><span class="sxs-lookup"><span data-stu-id="c96bc-338">The browser detects that the cookie header is too long.</span></span>
* <span data-ttu-id="c96bc-339">La dimensione complessiva della richiesta è troppo grande.</span><span class="sxs-lookup"><span data-stu-id="c96bc-339">The overall size of the request is too large.</span></span>

<span data-ttu-id="c96bc-340">Se per l'elaborazione delle richieste utente è richiesta una grande quantità di dati utente:</span><span class="sxs-lookup"><span data-stu-id="c96bc-340">If a large amount of user data is required for processing user requests:</span></span>

* <span data-ttu-id="c96bc-341">Limitare il numero e le dimensioni delle attestazioni utente per l'elaborazione delle richieste solo ai componenti richiesti dall'app.</span><span class="sxs-lookup"><span data-stu-id="c96bc-341">Limit the number and size of user claims for request processing to only what the app requires.</span></span>
* <span data-ttu-id="c96bc-342">Usare un personalizzato <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> per il middleware di autenticazione dei cookie <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> per archiviare l'identità tra le richieste.</span><span class="sxs-lookup"><span data-stu-id="c96bc-342">Use a custom <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> for the Cookie Authentication Middleware's <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> to store identity across requests.</span></span> <span data-ttu-id="c96bc-343">Mantenere grandi quantità di informazioni sull'identità sul server inviando al client solo una chiave di identificatore di sessione di piccole dimensioni.</span><span class="sxs-lookup"><span data-stu-id="c96bc-343">Preserve large quantities of identity information on the server while only sending a small session identifier key to the client.</span></span>

## <a name="save-the-access-token"></a><span data-ttu-id="c96bc-344">Salvare il token di accesso</span><span class="sxs-lookup"><span data-stu-id="c96bc-344">Save the access token</span></span>

<span data-ttu-id="c96bc-345"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*>definisce se i token di accesso e di aggiornamento devono essere archiviati in <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> dopo un'autorizzazione riuscita.</span><span class="sxs-lookup"><span data-stu-id="c96bc-345"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> defines whether access and refresh tokens should be stored in the <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> after a successful authorization.</span></span> <span data-ttu-id="c96bc-346">`SaveTokens`per impostazione predefinita, è impostato su `false` per ridurre le dimensioni del cookie di autenticazione finale.</span><span class="sxs-lookup"><span data-stu-id="c96bc-346">`SaveTokens` is set to `false` by default to reduce the size of the final authentication cookie.</span></span>

<span data-ttu-id="c96bc-347">L'app di esempio imposta il valore di `SaveTokens` su `true` in <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> :</span><span class="sxs-lookup"><span data-stu-id="c96bc-347">The sample app sets the value of `SaveTokens` to `true` in <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=15)]

<span data-ttu-id="c96bc-348">Quando `OnPostConfirmationAsync` viene eseguito, archivia il token di accesso ([ExternalLoginInfo. AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) dal provider esterno in `ApplicationUser` `AuthenticationProperties` .</span><span class="sxs-lookup"><span data-stu-id="c96bc-348">When `OnPostConfirmationAsync` executes, store the access token ([ExternalLoginInfo.AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) from the external provider in the `ApplicationUser`'s `AuthenticationProperties`.</span></span>

<span data-ttu-id="c96bc-349">L'app di esempio salva il token di accesso in `OnPostConfirmationAsync` (nuova registrazione utente) e `OnGetCallbackAsync` (utente registrato in precedenza) in *account/ExternalLogin. cshtml. cs*:</span><span class="sxs-lookup"><span data-stu-id="c96bc-349">The sample app saves the access token in `OnPostConfirmationAsync` (new user registration) and `OnGetCallbackAsync` (previously registered user) in *Account/ExternalLogin.cshtml.cs*:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=54-56)]

## <a name="how-to-add-additional-custom-tokens"></a><span data-ttu-id="c96bc-350">Come aggiungere token personalizzati aggiuntivi</span><span class="sxs-lookup"><span data-stu-id="c96bc-350">How to add additional custom tokens</span></span>

<span data-ttu-id="c96bc-351">Per illustrare come aggiungere un token personalizzato, archiviato come parte di `SaveTokens` , l'app di esempio aggiunge un oggetto <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> con l'oggetto corrente <xref:System.DateTime> per un [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) di `TicketCreated` :</span><span class="sxs-lookup"><span data-stu-id="c96bc-351">To demonstrate how to add a custom token, which is stored as part of `SaveTokens`, the sample app adds an <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> with the current <xref:System.DateTime> for an [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) of `TicketCreated`:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=17-30)]

## <a name="creating-and-adding-claims"></a><span data-ttu-id="c96bc-352">Creazione e aggiunta di attestazioni</span><span class="sxs-lookup"><span data-stu-id="c96bc-352">Creating and adding claims</span></span>

<span data-ttu-id="c96bc-353">Il Framework fornisce azioni comuni e metodi di estensione per la creazione e l'aggiunta di attestazioni alla raccolta.</span><span class="sxs-lookup"><span data-stu-id="c96bc-353">The framework provides common actions and extension methods for creating and adding claims to the collection.</span></span> <span data-ttu-id="c96bc-354">Per altre informazioni, vedere <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> e <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.</span><span class="sxs-lookup"><span data-stu-id="c96bc-354">For more information, see the <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> and <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.</span></span>

<span data-ttu-id="c96bc-355">Gli utenti possono definire azioni personalizzate derivando da <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> e implementando il <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> metodo astratto.</span><span class="sxs-lookup"><span data-stu-id="c96bc-355">Users can define custom actions by deriving from <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> and implementing the abstract <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> method.</span></span>

<span data-ttu-id="c96bc-356">Per altre informazioni, vedere <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span><span class="sxs-lookup"><span data-stu-id="c96bc-356">For more information, see <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span></span>

## <a name="removal-of-claim-actions-and-claims"></a><span data-ttu-id="c96bc-357">Rimozione di azioni attestazioni e attestazioni</span><span class="sxs-lookup"><span data-stu-id="c96bc-357">Removal of claim actions and claims</span></span>

<span data-ttu-id="c96bc-358">[ClaimActionCollection. Remove (String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) rimuove tutte le azioni di attestazione per l'oggetto specificato <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> dalla raccolta.</span><span class="sxs-lookup"><span data-stu-id="c96bc-358">[ClaimActionCollection.Remove(String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) removes all claim actions for the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the collection.</span></span> <span data-ttu-id="c96bc-359">[ClaimActionCollectionMapExtensions. DeleteClaim (ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) Elimina un'attestazione dell'oggetto specificato <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> dall'identità.</span><span class="sxs-lookup"><span data-stu-id="c96bc-359">[ClaimActionCollectionMapExtensions.DeleteClaim(ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) deletes a claim of the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the identity.</span></span> <span data-ttu-id="c96bc-360"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*>viene utilizzato principalmente con [OpenID Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) per rimuovere le attestazioni generate dal protocollo.</span><span class="sxs-lookup"><span data-stu-id="c96bc-360"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> is primarily used with [OpenID Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) to remove protocol-generated claims.</span></span>

## <a name="sample-app-output"></a><span data-ttu-id="c96bc-361">Esempio di output dell'app</span><span class="sxs-lookup"><span data-stu-id="c96bc-361">Sample app output</span></span>

```
User Claims

http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier
    9b342344f-7aab-43c2-1ac1-ba75912ca999
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name
    someone@gmail.com
AspNet.Identity.SecurityStamp
    7D4312MOWRYYBFI1KXRPHGOSTBVWSFDE
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname
    Judy
urn:google:locale
    en
urn:google:picture
    https://lh4.googleusercontent.com/-XXXXXX/XXXXXX/XXXXXX/XXXXXX/photo.jpg

Authentication Properties

.Token.access_token
    yc23.AlvoZqz56...1lxltXV7D-ZWP9
.Token.token_type
    Bearer
.Token.expires_at
    2019-04-11T22:14:51.0000000+00:00
.Token.TicketCreated
    4/11/2019 9:14:52 PM
.TokenNames
    access_token;token_type;expires_at;TicketCreated
.persistent
.issued
    Thu, 11 Apr 2019 20:51:06 GMT
.expires
    Thu, 25 Apr 2019 20:51:06 GMT

```

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="c96bc-362">Un'app ASP.NET Core può creare attestazioni e token aggiuntivi da provider di autenticazione esterni, ad esempio Facebook, Google, Microsoft e Twitter.</span><span class="sxs-lookup"><span data-stu-id="c96bc-362">An ASP.NET Core app can establish additional claims and tokens from external authentication providers, such as Facebook, Google, Microsoft, and Twitter.</span></span> <span data-ttu-id="c96bc-363">Ogni provider rivela informazioni diverse sugli utenti sulla propria piattaforma, ma il modello per la ricezione e la trasformazione dei dati utente in attestazioni aggiuntive è lo stesso.</span><span class="sxs-lookup"><span data-stu-id="c96bc-363">Each provider reveals different information about users on its platform, but the pattern for receiving and transforming user data into additional claims is the same.</span></span>

<span data-ttu-id="c96bc-364">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="c96bc-364">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="c96bc-365">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="c96bc-365">Prerequisites</span></span>

<span data-ttu-id="c96bc-366">Decidere quali provider di autenticazione esterni supportare nell'app.</span><span class="sxs-lookup"><span data-stu-id="c96bc-366">Decide which external authentication providers to support in the app.</span></span> <span data-ttu-id="c96bc-367">Per ogni provider, registrare l'app e ottenere un ID client e un segreto client.</span><span class="sxs-lookup"><span data-stu-id="c96bc-367">For each provider, register the app and obtain a client ID and client secret.</span></span> <span data-ttu-id="c96bc-368">Per altre informazioni, vedere <xref:security/authentication/social/index>.</span><span class="sxs-lookup"><span data-stu-id="c96bc-368">For more information, see <xref:security/authentication/social/index>.</span></span> <span data-ttu-id="c96bc-369">L'app di esempio usa il [provider di autenticazione Google](xref:security/authentication/google-logins).</span><span class="sxs-lookup"><span data-stu-id="c96bc-369">The sample app uses the [Google authentication provider](xref:security/authentication/google-logins).</span></span>

## <a name="set-the-client-id-and-client-secret"></a><span data-ttu-id="c96bc-370">Impostare l'ID client e il segreto client</span><span class="sxs-lookup"><span data-stu-id="c96bc-370">Set the client ID and client secret</span></span>

<span data-ttu-id="c96bc-371">Il provider di autenticazione OAuth stabilisce una relazione di trust con un'app che usa un ID client e un segreto client.</span><span class="sxs-lookup"><span data-stu-id="c96bc-371">The OAuth authentication provider establishes a trust relationship with an app using a client ID and client secret.</span></span> <span data-ttu-id="c96bc-372">I valori relativi all'ID client e al segreto client vengono creati per l'app dal provider di autenticazione esterno quando l'app viene registrata con il provider.</span><span class="sxs-lookup"><span data-stu-id="c96bc-372">Client ID and client secret values are created for the app by the external authentication provider when the app is registered with the provider.</span></span> <span data-ttu-id="c96bc-373">Ogni provider esterno usato dall'app deve essere configurato in modo indipendente con l'ID client e il segreto client del provider.</span><span class="sxs-lookup"><span data-stu-id="c96bc-373">Each external provider that the app uses must be configured independently with the provider's client ID and client secret.</span></span> <span data-ttu-id="c96bc-374">Per ulteriori informazioni, vedere gli argomenti del provider di autenticazione esterno applicabili allo scenario:</span><span class="sxs-lookup"><span data-stu-id="c96bc-374">For more information, see the external authentication provider topics that apply to your scenario:</span></span>

* [<span data-ttu-id="c96bc-375">Autenticazione Facebook</span><span class="sxs-lookup"><span data-stu-id="c96bc-375">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="c96bc-376">Autenticazione Google</span><span class="sxs-lookup"><span data-stu-id="c96bc-376">Google authentication</span></span>](xref:security/authentication/google-logins)
* [<span data-ttu-id="c96bc-377">Autenticazione Microsoft</span><span class="sxs-lookup"><span data-stu-id="c96bc-377">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="c96bc-378">Autenticazione Twitter</span><span class="sxs-lookup"><span data-stu-id="c96bc-378">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="c96bc-379">Altri provider di autenticazione</span><span class="sxs-lookup"><span data-stu-id="c96bc-379">Other authentication providers</span></span>](xref:security/authentication/otherlogins)
* [<span data-ttu-id="c96bc-380">OpenIdConnect</span><span class="sxs-lookup"><span data-stu-id="c96bc-380">OpenIdConnect</span></span>](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)

<span data-ttu-id="c96bc-381">L'app di esempio configura il provider di autenticazione Google con un ID client e un segreto client forniti da Google:</span><span class="sxs-lookup"><span data-stu-id="c96bc-381">The sample app configures the Google authentication provider with a client ID and client secret provided by Google:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=4,9)]

## <a name="establish-the-authentication-scope"></a><span data-ttu-id="c96bc-382">Definire l'ambito di autenticazione</span><span class="sxs-lookup"><span data-stu-id="c96bc-382">Establish the authentication scope</span></span>

<span data-ttu-id="c96bc-383">Specificare l'elenco di autorizzazioni da recuperare dal provider specificando <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*> .</span><span class="sxs-lookup"><span data-stu-id="c96bc-383">Specify the list of permissions to retrieve from the provider by specifying the <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*>.</span></span> <span data-ttu-id="c96bc-384">Gli ambiti di autenticazione per i provider esterni comuni sono riportati nella tabella seguente.</span><span class="sxs-lookup"><span data-stu-id="c96bc-384">Authentication scopes for common external providers appear in the following table.</span></span>

| <span data-ttu-id="c96bc-385">Provider</span><span class="sxs-lookup"><span data-stu-id="c96bc-385">Provider</span></span>  | <span data-ttu-id="c96bc-386">Ambito</span><span class="sxs-lookup"><span data-stu-id="c96bc-386">Scope</span></span>                                                            |
| ---
<span data-ttu-id="c96bc-387">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="c96bc-387">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c96bc-388">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c96bc-388">'Blazor'</span></span>
- <span data-ttu-id="c96bc-389">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c96bc-389">'Identity'</span></span>
- <span data-ttu-id="c96bc-390">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c96bc-390">'Let's Encrypt'</span></span>
- <span data-ttu-id="c96bc-391">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c96bc-391">'Razor'</span></span>
- <span data-ttu-id="c96bc-392">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="c96bc-392">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c96bc-393">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="c96bc-393">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c96bc-394">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c96bc-394">'Blazor'</span></span>
- <span data-ttu-id="c96bc-395">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c96bc-395">'Identity'</span></span>
- <span data-ttu-id="c96bc-396">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c96bc-396">'Let's Encrypt'</span></span>
- <span data-ttu-id="c96bc-397">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c96bc-397">'Razor'</span></span>
- <span data-ttu-id="c96bc-398">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="c96bc-398">'SignalR' uid:</span></span> 

<span data-ttu-id="c96bc-399">----- | Titolo---: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="c96bc-399">----- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c96bc-400">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c96bc-400">'Blazor'</span></span>
- <span data-ttu-id="c96bc-401">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c96bc-401">'Identity'</span></span>
- <span data-ttu-id="c96bc-402">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c96bc-402">'Let's Encrypt'</span></span>
- <span data-ttu-id="c96bc-403">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c96bc-403">'Razor'</span></span>
- <span data-ttu-id="c96bc-404">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="c96bc-404">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c96bc-405">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="c96bc-405">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c96bc-406">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c96bc-406">'Blazor'</span></span>
- <span data-ttu-id="c96bc-407">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c96bc-407">'Identity'</span></span>
- <span data-ttu-id="c96bc-408">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c96bc-408">'Let's Encrypt'</span></span>
- <span data-ttu-id="c96bc-409">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c96bc-409">'Razor'</span></span>
- <span data-ttu-id="c96bc-410">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="c96bc-410">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c96bc-411">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="c96bc-411">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c96bc-412">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c96bc-412">'Blazor'</span></span>
- <span data-ttu-id="c96bc-413">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c96bc-413">'Identity'</span></span>
- <span data-ttu-id="c96bc-414">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c96bc-414">'Let's Encrypt'</span></span>
- <span data-ttu-id="c96bc-415">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c96bc-415">'Razor'</span></span>
- <span data-ttu-id="c96bc-416">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="c96bc-416">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c96bc-417">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="c96bc-417">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c96bc-418">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c96bc-418">'Blazor'</span></span>
- <span data-ttu-id="c96bc-419">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c96bc-419">'Identity'</span></span>
- <span data-ttu-id="c96bc-420">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c96bc-420">'Let's Encrypt'</span></span>
- <span data-ttu-id="c96bc-421">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c96bc-421">'Razor'</span></span>
- <span data-ttu-id="c96bc-422">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="c96bc-422">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c96bc-423">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="c96bc-423">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c96bc-424">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c96bc-424">'Blazor'</span></span>
- <span data-ttu-id="c96bc-425">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c96bc-425">'Identity'</span></span>
- <span data-ttu-id="c96bc-426">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c96bc-426">'Let's Encrypt'</span></span>
- <span data-ttu-id="c96bc-427">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c96bc-427">'Razor'</span></span>
- <span data-ttu-id="c96bc-428">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="c96bc-428">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c96bc-429">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="c96bc-429">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c96bc-430">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c96bc-430">'Blazor'</span></span>
- <span data-ttu-id="c96bc-431">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c96bc-431">'Identity'</span></span>
- <span data-ttu-id="c96bc-432">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c96bc-432">'Let's Encrypt'</span></span>
- <span data-ttu-id="c96bc-433">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c96bc-433">'Razor'</span></span>
- <span data-ttu-id="c96bc-434">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="c96bc-434">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c96bc-435">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="c96bc-435">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c96bc-436">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c96bc-436">'Blazor'</span></span>
- <span data-ttu-id="c96bc-437">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c96bc-437">'Identity'</span></span>
- <span data-ttu-id="c96bc-438">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c96bc-438">'Let's Encrypt'</span></span>
- <span data-ttu-id="c96bc-439">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c96bc-439">'Razor'</span></span>
- <span data-ttu-id="c96bc-440">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="c96bc-440">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c96bc-441">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="c96bc-441">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c96bc-442">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c96bc-442">'Blazor'</span></span>
- <span data-ttu-id="c96bc-443">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c96bc-443">'Identity'</span></span>
- <span data-ttu-id="c96bc-444">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c96bc-444">'Let's Encrypt'</span></span>
- <span data-ttu-id="c96bc-445">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c96bc-445">'Razor'</span></span>
- <span data-ttu-id="c96bc-446">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="c96bc-446">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c96bc-447">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="c96bc-447">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c96bc-448">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c96bc-448">'Blazor'</span></span>
- <span data-ttu-id="c96bc-449">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c96bc-449">'Identity'</span></span>
- <span data-ttu-id="c96bc-450">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c96bc-450">'Let's Encrypt'</span></span>
- <span data-ttu-id="c96bc-451">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c96bc-451">'Razor'</span></span>
- <span data-ttu-id="c96bc-452">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="c96bc-452">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c96bc-453">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="c96bc-453">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c96bc-454">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c96bc-454">'Blazor'</span></span>
- <span data-ttu-id="c96bc-455">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c96bc-455">'Identity'</span></span>
- <span data-ttu-id="c96bc-456">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c96bc-456">'Let's Encrypt'</span></span>
- <span data-ttu-id="c96bc-457">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c96bc-457">'Razor'</span></span>
- <span data-ttu-id="c96bc-458">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="c96bc-458">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c96bc-459">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="c96bc-459">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c96bc-460">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c96bc-460">'Blazor'</span></span>
- <span data-ttu-id="c96bc-461">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c96bc-461">'Identity'</span></span>
- <span data-ttu-id="c96bc-462">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c96bc-462">'Let's Encrypt'</span></span>
- <span data-ttu-id="c96bc-463">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c96bc-463">'Razor'</span></span>
- <span data-ttu-id="c96bc-464">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="c96bc-464">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c96bc-465">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="c96bc-465">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c96bc-466">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c96bc-466">'Blazor'</span></span>
- <span data-ttu-id="c96bc-467">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c96bc-467">'Identity'</span></span>
- <span data-ttu-id="c96bc-468">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c96bc-468">'Let's Encrypt'</span></span>
- <span data-ttu-id="c96bc-469">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c96bc-469">'Razor'</span></span>
- <span data-ttu-id="c96bc-470">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="c96bc-470">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c96bc-471">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="c96bc-471">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c96bc-472">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c96bc-472">'Blazor'</span></span>
- <span data-ttu-id="c96bc-473">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c96bc-473">'Identity'</span></span>
- <span data-ttu-id="c96bc-474">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c96bc-474">'Let's Encrypt'</span></span>
- <span data-ttu-id="c96bc-475">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c96bc-475">'Razor'</span></span>
- <span data-ttu-id="c96bc-476">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="c96bc-476">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c96bc-477">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="c96bc-477">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c96bc-478">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c96bc-478">'Blazor'</span></span>
- <span data-ttu-id="c96bc-479">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c96bc-479">'Identity'</span></span>
- <span data-ttu-id="c96bc-480">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c96bc-480">'Let's Encrypt'</span></span>
- <span data-ttu-id="c96bc-481">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c96bc-481">'Razor'</span></span>
- <span data-ttu-id="c96bc-482">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="c96bc-482">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c96bc-483">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="c96bc-483">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c96bc-484">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c96bc-484">'Blazor'</span></span>
- <span data-ttu-id="c96bc-485">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c96bc-485">'Identity'</span></span>
- <span data-ttu-id="c96bc-486">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c96bc-486">'Let's Encrypt'</span></span>
- <span data-ttu-id="c96bc-487">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c96bc-487">'Razor'</span></span>
- <span data-ttu-id="c96bc-488">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="c96bc-488">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c96bc-489">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="c96bc-489">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c96bc-490">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c96bc-490">'Blazor'</span></span>
- <span data-ttu-id="c96bc-491">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c96bc-491">'Identity'</span></span>
- <span data-ttu-id="c96bc-492">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c96bc-492">'Let's Encrypt'</span></span>
- <span data-ttu-id="c96bc-493">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c96bc-493">'Razor'</span></span>
- <span data-ttu-id="c96bc-494">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="c96bc-494">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c96bc-495">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="c96bc-495">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c96bc-496">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c96bc-496">'Blazor'</span></span>
- <span data-ttu-id="c96bc-497">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c96bc-497">'Identity'</span></span>
- <span data-ttu-id="c96bc-498">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c96bc-498">'Let's Encrypt'</span></span>
- <span data-ttu-id="c96bc-499">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c96bc-499">'Razor'</span></span>
- <span data-ttu-id="c96bc-500">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="c96bc-500">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c96bc-501">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="c96bc-501">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c96bc-502">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c96bc-502">'Blazor'</span></span>
- <span data-ttu-id="c96bc-503">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c96bc-503">'Identity'</span></span>
- <span data-ttu-id="c96bc-504">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c96bc-504">'Let's Encrypt'</span></span>
- <span data-ttu-id="c96bc-505">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c96bc-505">'Razor'</span></span>
- <span data-ttu-id="c96bc-506">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="c96bc-506">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c96bc-507">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="c96bc-507">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c96bc-508">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c96bc-508">'Blazor'</span></span>
- <span data-ttu-id="c96bc-509">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c96bc-509">'Identity'</span></span>
- <span data-ttu-id="c96bc-510">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c96bc-510">'Let's Encrypt'</span></span>
- <span data-ttu-id="c96bc-511">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c96bc-511">'Razor'</span></span>
- <span data-ttu-id="c96bc-512">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="c96bc-512">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c96bc-513">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="c96bc-513">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c96bc-514">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c96bc-514">'Blazor'</span></span>
- <span data-ttu-id="c96bc-515">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c96bc-515">'Identity'</span></span>
- <span data-ttu-id="c96bc-516">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c96bc-516">'Let's Encrypt'</span></span>
- <span data-ttu-id="c96bc-517">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c96bc-517">'Razor'</span></span>
- <span data-ttu-id="c96bc-518">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="c96bc-518">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c96bc-519">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="c96bc-519">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c96bc-520">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c96bc-520">'Blazor'</span></span>
- <span data-ttu-id="c96bc-521">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c96bc-521">'Identity'</span></span>
- <span data-ttu-id="c96bc-522">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c96bc-522">'Let's Encrypt'</span></span>
- <span data-ttu-id="c96bc-523">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c96bc-523">'Razor'</span></span>
- <span data-ttu-id="c96bc-524">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="c96bc-524">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c96bc-525">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="c96bc-525">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c96bc-526">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c96bc-526">'Blazor'</span></span>
- <span data-ttu-id="c96bc-527">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c96bc-527">'Identity'</span></span>
- <span data-ttu-id="c96bc-528">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c96bc-528">'Let's Encrypt'</span></span>
- <span data-ttu-id="c96bc-529">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c96bc-529">'Razor'</span></span>
- <span data-ttu-id="c96bc-530">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="c96bc-530">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c96bc-531">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="c96bc-531">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c96bc-532">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c96bc-532">'Blazor'</span></span>
- <span data-ttu-id="c96bc-533">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c96bc-533">'Identity'</span></span>
- <span data-ttu-id="c96bc-534">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c96bc-534">'Let's Encrypt'</span></span>
- <span data-ttu-id="c96bc-535">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c96bc-535">'Razor'</span></span>
- <span data-ttu-id="c96bc-536">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="c96bc-536">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c96bc-537">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="c96bc-537">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c96bc-538">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c96bc-538">'Blazor'</span></span>
- <span data-ttu-id="c96bc-539">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c96bc-539">'Identity'</span></span>
- <span data-ttu-id="c96bc-540">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c96bc-540">'Let's Encrypt'</span></span>
- <span data-ttu-id="c96bc-541">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c96bc-541">'Razor'</span></span>
- <span data-ttu-id="c96bc-542">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="c96bc-542">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c96bc-543">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="c96bc-543">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c96bc-544">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c96bc-544">'Blazor'</span></span>
- <span data-ttu-id="c96bc-545">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c96bc-545">'Identity'</span></span>
- <span data-ttu-id="c96bc-546">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c96bc-546">'Let's Encrypt'</span></span>
- <span data-ttu-id="c96bc-547">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c96bc-547">'Razor'</span></span>
- <span data-ttu-id="c96bc-548">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="c96bc-548">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c96bc-549">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="c96bc-549">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c96bc-550">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c96bc-550">'Blazor'</span></span>
- <span data-ttu-id="c96bc-551">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c96bc-551">'Identity'</span></span>
- <span data-ttu-id="c96bc-552">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c96bc-552">'Let's Encrypt'</span></span>
- <span data-ttu-id="c96bc-553">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c96bc-553">'Razor'</span></span>
- <span data-ttu-id="c96bc-554">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="c96bc-554">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c96bc-555">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="c96bc-555">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c96bc-556">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c96bc-556">'Blazor'</span></span>
- <span data-ttu-id="c96bc-557">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c96bc-557">'Identity'</span></span>
- <span data-ttu-id="c96bc-558">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c96bc-558">'Let's Encrypt'</span></span>
- <span data-ttu-id="c96bc-559">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c96bc-559">'Razor'</span></span>
- <span data-ttu-id="c96bc-560">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="c96bc-560">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c96bc-561">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="c96bc-561">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c96bc-562">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c96bc-562">'Blazor'</span></span>
- <span data-ttu-id="c96bc-563">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c96bc-563">'Identity'</span></span>
- <span data-ttu-id="c96bc-564">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c96bc-564">'Let's Encrypt'</span></span>
- <span data-ttu-id="c96bc-565">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c96bc-565">'Razor'</span></span>
- <span data-ttu-id="c96bc-566">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="c96bc-566">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c96bc-567">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="c96bc-567">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c96bc-568">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c96bc-568">'Blazor'</span></span>
- <span data-ttu-id="c96bc-569">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c96bc-569">'Identity'</span></span>
- <span data-ttu-id="c96bc-570">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c96bc-570">'Let's Encrypt'</span></span>
- <span data-ttu-id="c96bc-571">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c96bc-571">'Razor'</span></span>
- <span data-ttu-id="c96bc-572">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="c96bc-572">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c96bc-573">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="c96bc-573">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c96bc-574">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c96bc-574">'Blazor'</span></span>
- <span data-ttu-id="c96bc-575">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c96bc-575">'Identity'</span></span>
- <span data-ttu-id="c96bc-576">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c96bc-576">'Let's Encrypt'</span></span>
- <span data-ttu-id="c96bc-577">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c96bc-577">'Razor'</span></span>
- <span data-ttu-id="c96bc-578">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="c96bc-578">'SignalR' uid:</span></span> 

<span data-ttu-id="c96bc-579">-------------------------------- | | Facebook | `https://www.facebook.com/dialog/oauth`                          |
| Google | `https://www.googleapis.com/auth/userinfo.profile`               |
| Microsoft | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| Twitter |`https://api.twitter.com/oauth/authenticate`                     |</span><span class="sxs-lookup"><span data-stu-id="c96bc-579">-------------------------------- | | Facebook  | `https://www.facebook.com/dialog/oauth`                          |
| Google    | `https://www.googleapis.com/auth/userinfo.profile`               |
| Microsoft | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| Twitter   | `https://api.twitter.com/oauth/authenticate`                     |</span></span>

<span data-ttu-id="c96bc-580">Nell'app di esempio, `userinfo.profile` l'ambito di Google viene aggiunto automaticamente dal framework quando <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> viene chiamato su <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="c96bc-580">In the sample app, Google's `userinfo.profile` scope is automatically added by the framework when <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> is called on the <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder>.</span></span> <span data-ttu-id="c96bc-581">Se l'app richiede ambiti aggiuntivi, aggiungerli alle opzioni.</span><span class="sxs-lookup"><span data-stu-id="c96bc-581">If the app requires additional scopes, add them to the options.</span></span> <span data-ttu-id="c96bc-582">Nell'esempio seguente `https://www.googleapis.com/auth/user.birthday.read` viene aggiunto l'ambito Google per recuperare il compleanno di un utente:</span><span class="sxs-lookup"><span data-stu-id="c96bc-582">In the following example, the Google `https://www.googleapis.com/auth/user.birthday.read` scope is added in order to retrieve a user's birthday:</span></span>

```csharp
options.Scope.Add("https://www.googleapis.com/auth/user.birthday.read");
```

## <a name="map-user-data-keys-and-create-claims"></a><span data-ttu-id="c96bc-583">Eseguire il mapping delle chiavi di dati utente e creare attestazioni</span><span class="sxs-lookup"><span data-stu-id="c96bc-583">Map user data keys and create claims</span></span>

<span data-ttu-id="c96bc-584">Nelle opzioni del provider specificare un <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> o <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> per ogni chiave/sottochiave nei dati utente JSON del provider esterno per l'identità dell'app da leggere durante l'accesso.</span><span class="sxs-lookup"><span data-stu-id="c96bc-584">In the provider's options, specify a <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> or <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> for each key/subkey in the external provider's JSON user data for the app identity to read on sign in.</span></span> <span data-ttu-id="c96bc-585">Per ulteriori informazioni sui tipi di attestazione, vedere <xref:System.Security.Claims.ClaimTypes> .</span><span class="sxs-lookup"><span data-stu-id="c96bc-585">For more information on claim types, see <xref:System.Security.Claims.ClaimTypes>.</span></span>

<span data-ttu-id="c96bc-586">L'app di esempio crea attestazioni delle impostazioni locali ( `urn:google:locale` ) e immagini ( `urn:google:picture` ) dalle `locale` `picture` chiavi e nei dati utente di Google:</span><span class="sxs-lookup"><span data-stu-id="c96bc-586">The sample app creates locale (`urn:google:locale`) and picture (`urn:google:picture`) claims from the `locale` and `picture` keys in Google user data:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=13-14)]

<span data-ttu-id="c96bc-587">In `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync` , un <xref:Microsoft.AspNetCore.Identity.IdentityUser> ( `ApplicationUser` ) è connesso all'app con <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*> .</span><span class="sxs-lookup"><span data-stu-id="c96bc-587">In `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync`, an <xref:Microsoft.AspNetCore.Identity.IdentityUser> (`ApplicationUser`) is signed into the app with <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*>.</span></span> <span data-ttu-id="c96bc-588">Durante il processo di accesso, il <xref:Microsoft.AspNetCore.Identity.UserManager%601> può archiviare `ApplicationUser` attestazioni per i dati utente disponibili da <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*> .</span><span class="sxs-lookup"><span data-stu-id="c96bc-588">During the sign in process, the <xref:Microsoft.AspNetCore.Identity.UserManager%601> can store an `ApplicationUser` claims for user data available from the <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*>.</span></span>

<span data-ttu-id="c96bc-589">Nell'app di esempio `OnPostConfirmationAsync` (*account/ExternalLogin. cshtml. cs*) vengono stabilite le attestazioni delle impostazioni locali ( `urn:google:locale` ) e immagine ( `urn:google:picture` ) per l'accesso `ApplicationUser` , inclusa un'attestazione per <xref:System.Security.Claims.ClaimTypes.GivenName> :</span><span class="sxs-lookup"><span data-stu-id="c96bc-589">In the sample app, `OnPostConfirmationAsync` (*Account/ExternalLogin.cshtml.cs*) establishes the locale (`urn:google:locale`) and picture (`urn:google:picture`) claims for the signed in `ApplicationUser`, including a claim for <xref:System.Security.Claims.ClaimTypes.GivenName>:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=35-51)]

<span data-ttu-id="c96bc-590">Per impostazione predefinita, le attestazioni di un utente vengono archiviate nel cookie di autenticazione.</span><span class="sxs-lookup"><span data-stu-id="c96bc-590">By default, a user's claims are stored in the authentication cookie.</span></span> <span data-ttu-id="c96bc-591">Se il cookie di autenticazione è troppo grande, può causare un errore dell'app perché:</span><span class="sxs-lookup"><span data-stu-id="c96bc-591">If the authentication cookie is too large, it can cause the app to fail because:</span></span>

* <span data-ttu-id="c96bc-592">Il browser rileva che l'intestazione del cookie è troppo lungo.</span><span class="sxs-lookup"><span data-stu-id="c96bc-592">The browser detects that the cookie header is too long.</span></span>
* <span data-ttu-id="c96bc-593">La dimensione complessiva della richiesta è troppo grande.</span><span class="sxs-lookup"><span data-stu-id="c96bc-593">The overall size of the request is too large.</span></span>

<span data-ttu-id="c96bc-594">Se per l'elaborazione delle richieste utente è richiesta una grande quantità di dati utente:</span><span class="sxs-lookup"><span data-stu-id="c96bc-594">If a large amount of user data is required for processing user requests:</span></span>

* <span data-ttu-id="c96bc-595">Limitare il numero e le dimensioni delle attestazioni utente per l'elaborazione delle richieste solo ai componenti richiesti dall'app.</span><span class="sxs-lookup"><span data-stu-id="c96bc-595">Limit the number and size of user claims for request processing to only what the app requires.</span></span>
* <span data-ttu-id="c96bc-596">Usare un personalizzato <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> per il middleware di autenticazione dei cookie <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> per archiviare l'identità tra le richieste.</span><span class="sxs-lookup"><span data-stu-id="c96bc-596">Use a custom <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> for the Cookie Authentication Middleware's <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> to store identity across requests.</span></span> <span data-ttu-id="c96bc-597">Mantenere grandi quantità di informazioni sull'identità sul server inviando al client solo una chiave di identificatore di sessione di piccole dimensioni.</span><span class="sxs-lookup"><span data-stu-id="c96bc-597">Preserve large quantities of identity information on the server while only sending a small session identifier key to the client.</span></span>

## <a name="save-the-access-token"></a><span data-ttu-id="c96bc-598">Salvare il token di accesso</span><span class="sxs-lookup"><span data-stu-id="c96bc-598">Save the access token</span></span>

<span data-ttu-id="c96bc-599"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*>definisce se i token di accesso e di aggiornamento devono essere archiviati in <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> dopo un'autorizzazione riuscita.</span><span class="sxs-lookup"><span data-stu-id="c96bc-599"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> defines whether access and refresh tokens should be stored in the <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> after a successful authorization.</span></span> <span data-ttu-id="c96bc-600">`SaveTokens`per impostazione predefinita, è impostato su `false` per ridurre le dimensioni del cookie di autenticazione finale.</span><span class="sxs-lookup"><span data-stu-id="c96bc-600">`SaveTokens` is set to `false` by default to reduce the size of the final authentication cookie.</span></span>

<span data-ttu-id="c96bc-601">L'app di esempio imposta il valore di `SaveTokens` su `true` in <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> :</span><span class="sxs-lookup"><span data-stu-id="c96bc-601">The sample app sets the value of `SaveTokens` to `true` in <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=15)]

<span data-ttu-id="c96bc-602">Quando `OnPostConfirmationAsync` viene eseguito, archivia il token di accesso ([ExternalLoginInfo. AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) dal provider esterno in `ApplicationUser` `AuthenticationProperties` .</span><span class="sxs-lookup"><span data-stu-id="c96bc-602">When `OnPostConfirmationAsync` executes, store the access token ([ExternalLoginInfo.AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) from the external provider in the `ApplicationUser`'s `AuthenticationProperties`.</span></span>

<span data-ttu-id="c96bc-603">L'app di esempio salva il token di accesso in `OnPostConfirmationAsync` (nuova registrazione utente) e `OnGetCallbackAsync` (utente registrato in precedenza) in *account/ExternalLogin. cshtml. cs*:</span><span class="sxs-lookup"><span data-stu-id="c96bc-603">The sample app saves the access token in `OnPostConfirmationAsync` (new user registration) and `OnGetCallbackAsync` (previously registered user) in *Account/ExternalLogin.cshtml.cs*:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=54-56)]

## <a name="how-to-add-additional-custom-tokens"></a><span data-ttu-id="c96bc-604">Come aggiungere token personalizzati aggiuntivi</span><span class="sxs-lookup"><span data-stu-id="c96bc-604">How to add additional custom tokens</span></span>

<span data-ttu-id="c96bc-605">Per illustrare come aggiungere un token personalizzato, archiviato come parte di `SaveTokens` , l'app di esempio aggiunge un oggetto <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> con l'oggetto corrente <xref:System.DateTime> per un [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) di `TicketCreated` :</span><span class="sxs-lookup"><span data-stu-id="c96bc-605">To demonstrate how to add a custom token, which is stored as part of `SaveTokens`, the sample app adds an <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> with the current <xref:System.DateTime> for an [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) of `TicketCreated`:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=17-30)]

## <a name="creating-and-adding-claims"></a><span data-ttu-id="c96bc-606">Creazione e aggiunta di attestazioni</span><span class="sxs-lookup"><span data-stu-id="c96bc-606">Creating and adding claims</span></span>

<span data-ttu-id="c96bc-607">Il Framework fornisce azioni comuni e metodi di estensione per la creazione e l'aggiunta di attestazioni alla raccolta.</span><span class="sxs-lookup"><span data-stu-id="c96bc-607">The framework provides common actions and extension methods for creating and adding claims to the collection.</span></span> <span data-ttu-id="c96bc-608">Per altre informazioni, vedere <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> e <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.</span><span class="sxs-lookup"><span data-stu-id="c96bc-608">For more information, see the <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> and <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.</span></span>

<span data-ttu-id="c96bc-609">Gli utenti possono definire azioni personalizzate derivando da <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> e implementando il <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> metodo astratto.</span><span class="sxs-lookup"><span data-stu-id="c96bc-609">Users can define custom actions by deriving from <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> and implementing the abstract <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> method.</span></span>

<span data-ttu-id="c96bc-610">Per altre informazioni, vedere <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span><span class="sxs-lookup"><span data-stu-id="c96bc-610">For more information, see <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span></span>

## <a name="removal-of-claim-actions-and-claims"></a><span data-ttu-id="c96bc-611">Rimozione di azioni attestazioni e attestazioni</span><span class="sxs-lookup"><span data-stu-id="c96bc-611">Removal of claim actions and claims</span></span>

<span data-ttu-id="c96bc-612">[ClaimActionCollection. Remove (String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) rimuove tutte le azioni di attestazione per l'oggetto specificato <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> dalla raccolta.</span><span class="sxs-lookup"><span data-stu-id="c96bc-612">[ClaimActionCollection.Remove(String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) removes all claim actions for the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the collection.</span></span> <span data-ttu-id="c96bc-613">[ClaimActionCollectionMapExtensions. DeleteClaim (ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) Elimina un'attestazione dell'oggetto specificato <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> dall'identità.</span><span class="sxs-lookup"><span data-stu-id="c96bc-613">[ClaimActionCollectionMapExtensions.DeleteClaim(ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) deletes a claim of the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the identity.</span></span> <span data-ttu-id="c96bc-614"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*>viene utilizzato principalmente con [OpenID Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) per rimuovere le attestazioni generate dal protocollo.</span><span class="sxs-lookup"><span data-stu-id="c96bc-614"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> is primarily used with [OpenID Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) to remove protocol-generated claims.</span></span>

## <a name="sample-app-output"></a><span data-ttu-id="c96bc-615">Esempio di output dell'app</span><span class="sxs-lookup"><span data-stu-id="c96bc-615">Sample app output</span></span>

```
User Claims

http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier
    9b342344f-7aab-43c2-1ac1-ba75912ca999
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name
    someone@gmail.com
AspNet.Identity.SecurityStamp
    7D4312MOWRYYBFI1KXRPHGOSTBVWSFDE
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname
    Judy
urn:google:locale
    en
urn:google:picture
    https://lh4.googleusercontent.com/-XXXXXX/XXXXXX/XXXXXX/XXXXXX/photo.jpg

Authentication Properties

.Token.access_token
    yc23.AlvoZqz56...1lxltXV7D-ZWP9
.Token.token_type
    Bearer
.Token.expires_at
    2019-04-11T22:14:51.0000000+00:00
.Token.TicketCreated
    4/11/2019 9:14:52 PM
.TokenNames
    access_token;token_type;expires_at;TicketCreated
.persistent
.issued
    Thu, 11 Apr 2019 20:51:06 GMT
.expires
    Thu, 25 Apr 2019 20:51:06 GMT

```

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="c96bc-616">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="c96bc-616">Additional resources</span></span>

* <span data-ttu-id="c96bc-617">[app SocialSample di progettazione DotNet/AspNetCore](https://github.com/dotnet/AspNetCore/tree/master/src/Security/Authentication/samples/SocialSample): l'app di esempio collegata si trova nel ramo di progettazione del [repository GitHub DotNet/AspNetCore](https://github.com/dotnet/AspNetCore) `master` .</span><span class="sxs-lookup"><span data-stu-id="c96bc-617">[dotnet/AspNetCore engineering SocialSample app](https://github.com/dotnet/AspNetCore/tree/master/src/Security/Authentication/samples/SocialSample): The linked sample app is on the [dotnet/AspNetCore GitHub repo's](https://github.com/dotnet/AspNetCore) `master` engineering branch.</span></span> <span data-ttu-id="c96bc-618">Il `master` ramo contiene il codice in fase di sviluppo attivo per la prossima versione di ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c96bc-618">The `master` branch contains code under active development for the next release of ASP.NET Core.</span></span> <span data-ttu-id="c96bc-619">Per visualizzare una versione dell'app di esempio per una versione rilasciata di ASP.NET Core, usare l'elenco a discesa **Branch** per selezionare un ramo di rilascio (ad esempio `release/{X.Y}` ).</span><span class="sxs-lookup"><span data-stu-id="c96bc-619">To see a version of the sample app for a released version of ASP.NET Core, use the **Branch** drop down list to select a release branch (for example `release/{X.Y}`).</span></span>
