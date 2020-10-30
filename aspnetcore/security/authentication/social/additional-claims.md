---
title: Mantieni attestazioni e token aggiuntivi da provider esterni in ASP.NET Core
author: rick-anderson
description: Informazioni su come creare attestazioni e token aggiuntivi da provider esterni.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/15/2019
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: security/authentication/social/additional-claims
ms.openlocfilehash: 733afec8d3253ec58a7edf6d7fcf35e303a7fe57
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060326"
---
# <a name="persist-additional-claims-and-tokens-from-external-providers-in-aspnet-core"></a><span data-ttu-id="9161c-103">Mantieni attestazioni e token aggiuntivi da provider esterni in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9161c-103">Persist additional claims and tokens from external providers in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="9161c-104">Un'app ASP.NET Core può creare attestazioni e token aggiuntivi da provider di autenticazione esterni, ad esempio Facebook, Google, Microsoft e Twitter.</span><span class="sxs-lookup"><span data-stu-id="9161c-104">An ASP.NET Core app can establish additional claims and tokens from external authentication providers, such as Facebook, Google, Microsoft, and Twitter.</span></span> <span data-ttu-id="9161c-105">Ogni provider rivela informazioni diverse sugli utenti sulla propria piattaforma, ma il modello per la ricezione e la trasformazione dei dati utente in attestazioni aggiuntive è lo stesso.</span><span class="sxs-lookup"><span data-stu-id="9161c-105">Each provider reveals different information about users on its platform, but the pattern for receiving and transforming user data into additional claims is the same.</span></span>

<span data-ttu-id="9161c-106">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="9161c-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="9161c-107">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="9161c-107">Prerequisites</span></span>

<span data-ttu-id="9161c-108">Decidere quali provider di autenticazione esterni supportare nell'app.</span><span class="sxs-lookup"><span data-stu-id="9161c-108">Decide which external authentication providers to support in the app.</span></span> <span data-ttu-id="9161c-109">Per ogni provider, registrare l'app e ottenere un ID client e un segreto client.</span><span class="sxs-lookup"><span data-stu-id="9161c-109">For each provider, register the app and obtain a client ID and client secret.</span></span> <span data-ttu-id="9161c-110">Per altre informazioni, vedere <xref:security/authentication/social/index>.</span><span class="sxs-lookup"><span data-stu-id="9161c-110">For more information, see <xref:security/authentication/social/index>.</span></span> <span data-ttu-id="9161c-111">L'app di esempio usa il [provider di autenticazione Google](xref:security/authentication/google-logins).</span><span class="sxs-lookup"><span data-stu-id="9161c-111">The sample app uses the [Google authentication provider](xref:security/authentication/google-logins).</span></span>

## <a name="set-the-client-id-and-client-secret"></a><span data-ttu-id="9161c-112">Impostare l'ID client e il segreto client</span><span class="sxs-lookup"><span data-stu-id="9161c-112">Set the client ID and client secret</span></span>

<span data-ttu-id="9161c-113">Il provider di autenticazione OAuth stabilisce una relazione di trust con un'app che usa un ID client e un segreto client.</span><span class="sxs-lookup"><span data-stu-id="9161c-113">The OAuth authentication provider establishes a trust relationship with an app using a client ID and client secret.</span></span> <span data-ttu-id="9161c-114">I valori relativi all'ID client e al segreto client vengono creati per l'app dal provider di autenticazione esterno quando l'app viene registrata con il provider.</span><span class="sxs-lookup"><span data-stu-id="9161c-114">Client ID and client secret values are created for the app by the external authentication provider when the app is registered with the provider.</span></span> <span data-ttu-id="9161c-115">Ogni provider esterno usato dall'app deve essere configurato in modo indipendente con l'ID client e il segreto client del provider.</span><span class="sxs-lookup"><span data-stu-id="9161c-115">Each external provider that the app uses must be configured independently with the provider's client ID and client secret.</span></span> <span data-ttu-id="9161c-116">Per ulteriori informazioni, vedere gli argomenti del provider di autenticazione esterno applicabili allo scenario:</span><span class="sxs-lookup"><span data-stu-id="9161c-116">For more information, see the external authentication provider topics that apply to your scenario:</span></span>

* [<span data-ttu-id="9161c-117">Autenticazione Facebook</span><span class="sxs-lookup"><span data-stu-id="9161c-117">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="9161c-118">Autenticazione Google</span><span class="sxs-lookup"><span data-stu-id="9161c-118">Google authentication</span></span>](xref:security/authentication/google-logins)
* [<span data-ttu-id="9161c-119">Autenticazione Microsoft</span><span class="sxs-lookup"><span data-stu-id="9161c-119">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="9161c-120">Autenticazione Twitter</span><span class="sxs-lookup"><span data-stu-id="9161c-120">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="9161c-121">Altri provider di autenticazione</span><span class="sxs-lookup"><span data-stu-id="9161c-121">Other authentication providers</span></span>](xref:security/authentication/otherlogins)
* [<span data-ttu-id="9161c-122">OpenIdConnect</span><span class="sxs-lookup"><span data-stu-id="9161c-122">OpenIdConnect</span></span>](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)

<span data-ttu-id="9161c-123">L'app di esempio configura il provider di autenticazione Google con un ID client e un segreto client forniti da Google:</span><span class="sxs-lookup"><span data-stu-id="9161c-123">The sample app configures the Google authentication provider with a client ID and client secret provided by Google:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=4,9)]

## <a name="establish-the-authentication-scope"></a><span data-ttu-id="9161c-124">Definire l'ambito di autenticazione</span><span class="sxs-lookup"><span data-stu-id="9161c-124">Establish the authentication scope</span></span>

<span data-ttu-id="9161c-125">Specificare l'elenco di autorizzazioni da recuperare dal provider specificando <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*> .</span><span class="sxs-lookup"><span data-stu-id="9161c-125">Specify the list of permissions to retrieve from the provider by specifying the <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*>.</span></span> <span data-ttu-id="9161c-126">Gli ambiti di autenticazione per i provider esterni comuni sono riportati nella tabella seguente.</span><span class="sxs-lookup"><span data-stu-id="9161c-126">Authentication scopes for common external providers appear in the following table.</span></span>

| <span data-ttu-id="9161c-127">Provider</span><span class="sxs-lookup"><span data-stu-id="9161c-127">Provider</span></span>  | <span data-ttu-id="9161c-128">Ambito</span><span class="sxs-lookup"><span data-stu-id="9161c-128">Scope</span></span>                                                            |
| --------- | ---------------------------------------------------------------- |
| <span data-ttu-id="9161c-129">Facebook</span><span class="sxs-lookup"><span data-stu-id="9161c-129">Facebook</span></span>  | `https://www.facebook.com/dialog/oauth`                          |
| <span data-ttu-id="9161c-130">Google</span><span class="sxs-lookup"><span data-stu-id="9161c-130">Google</span></span>    | `https://www.googleapis.com/auth/userinfo.profile`               |
| <span data-ttu-id="9161c-131">Microsoft</span><span class="sxs-lookup"><span data-stu-id="9161c-131">Microsoft</span></span> | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| <span data-ttu-id="9161c-132">Twitter</span><span class="sxs-lookup"><span data-stu-id="9161c-132">Twitter</span></span>   | `https://api.twitter.com/oauth/authenticate`                     |

<span data-ttu-id="9161c-133">Nell'app di esempio, `userinfo.profile` l'ambito di Google viene aggiunto automaticamente dal framework quando <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> viene chiamato su <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="9161c-133">In the sample app, Google's `userinfo.profile` scope is automatically added by the framework when <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> is called on the <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder>.</span></span> <span data-ttu-id="9161c-134">Se l'app richiede ambiti aggiuntivi, aggiungerli alle opzioni.</span><span class="sxs-lookup"><span data-stu-id="9161c-134">If the app requires additional scopes, add them to the options.</span></span> <span data-ttu-id="9161c-135">Nell'esempio seguente `https://www.googleapis.com/auth/user.birthday.read` viene aggiunto l'ambito Google per recuperare il compleanno di un utente:</span><span class="sxs-lookup"><span data-stu-id="9161c-135">In the following example, the Google `https://www.googleapis.com/auth/user.birthday.read` scope is added in order to retrieve a user's birthday:</span></span>

```csharp
options.Scope.Add("https://www.googleapis.com/auth/user.birthday.read");
```

## <a name="map-user-data-keys-and-create-claims"></a><span data-ttu-id="9161c-136">Eseguire il mapping delle chiavi di dati utente e creare attestazioni</span><span class="sxs-lookup"><span data-stu-id="9161c-136">Map user data keys and create claims</span></span>

<span data-ttu-id="9161c-137">Nelle opzioni del provider specificare un <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> o <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> per ogni chiave/sottochiave nei dati utente JSON del provider esterno per l'identità dell'app da leggere durante l'accesso.</span><span class="sxs-lookup"><span data-stu-id="9161c-137">In the provider's options, specify a <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> or <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> for each key/subkey in the external provider's JSON user data for the app identity to read on sign in.</span></span> <span data-ttu-id="9161c-138">Per ulteriori informazioni sui tipi di attestazione, vedere <xref:System.Security.Claims.ClaimTypes> .</span><span class="sxs-lookup"><span data-stu-id="9161c-138">For more information on claim types, see <xref:System.Security.Claims.ClaimTypes>.</span></span>

<span data-ttu-id="9161c-139">L'app di esempio crea attestazioni delle impostazioni locali ( `urn:google:locale` ) e immagini ( `urn:google:picture` ) dalle `locale` `picture` chiavi e nei dati utente di Google:</span><span class="sxs-lookup"><span data-stu-id="9161c-139">The sample app creates locale (`urn:google:locale`) and picture (`urn:google:picture`) claims from the `locale` and `picture` keys in Google user data:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=13-14)]

<span data-ttu-id="9161c-140">In `Microsoft.AspNetCore.:::no-loc(Identity):::.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync` , un <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.:::no-loc(Identity):::User> ( `ApplicationUser` ) è connesso all'app con <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.SignInManager%601.SignInAsync*> .</span><span class="sxs-lookup"><span data-stu-id="9161c-140">In `Microsoft.AspNetCore.:::no-loc(Identity):::.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync`, an <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.:::no-loc(Identity):::User> (`ApplicationUser`) is signed into the app with <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.SignInManager%601.SignInAsync*>.</span></span> <span data-ttu-id="9161c-141">Durante il processo di accesso, il <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.UserManager%601> può archiviare `ApplicationUser` attestazioni per i dati utente disponibili da <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.ExternalLoginInfo.Principal*> .</span><span class="sxs-lookup"><span data-stu-id="9161c-141">During the sign in process, the <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.UserManager%601> can store an `ApplicationUser` claims for user data available from the <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.ExternalLoginInfo.Principal*>.</span></span>

<span data-ttu-id="9161c-142">Nell'app di esempio `OnPostConfirmationAsync` ( *account/ExternalLogin. cshtml. cs* ) vengono stabilite le attestazioni delle impostazioni locali ( `urn:google:locale` ) e immagine ( `urn:google:picture` ) per l'accesso `ApplicationUser` , inclusa un'attestazione per <xref:System.Security.Claims.ClaimTypes.GivenName> :</span><span class="sxs-lookup"><span data-stu-id="9161c-142">In the sample app, `OnPostConfirmationAsync` ( *Account/ExternalLogin.cshtml.cs* ) establishes the locale (`urn:google:locale`) and picture (`urn:google:picture`) claims for the signed in `ApplicationUser`, including a claim for <xref:System.Security.Claims.ClaimTypes.GivenName>:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Areas/:::no-loc(Identity):::/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=35-51)]

<span data-ttu-id="9161c-143">Per impostazione predefinita, le attestazioni di un utente vengono archiviate nell'autenticazione di :::no-loc(cookie)::: .</span><span class="sxs-lookup"><span data-stu-id="9161c-143">By default, a user's claims are stored in the authentication :::no-loc(cookie):::.</span></span> <span data-ttu-id="9161c-144">Se l'autenticazione :::no-loc(cookie)::: è troppo grande, può causare un errore dell'app perché:</span><span class="sxs-lookup"><span data-stu-id="9161c-144">If the authentication :::no-loc(cookie)::: is too large, it can cause the app to fail because:</span></span>

* <span data-ttu-id="9161c-145">Il browser rileva che l' :::no-loc(cookie)::: intestazione è troppo lungo.</span><span class="sxs-lookup"><span data-stu-id="9161c-145">The browser detects that the :::no-loc(cookie)::: header is too long.</span></span>
* <span data-ttu-id="9161c-146">La dimensione complessiva della richiesta è troppo grande.</span><span class="sxs-lookup"><span data-stu-id="9161c-146">The overall size of the request is too large.</span></span>

<span data-ttu-id="9161c-147">Se per l'elaborazione delle richieste utente è richiesta una grande quantità di dati utente:</span><span class="sxs-lookup"><span data-stu-id="9161c-147">If a large amount of user data is required for processing user requests:</span></span>

* <span data-ttu-id="9161c-148">Limitare il numero e le dimensioni delle attestazioni utente per l'elaborazione delle richieste solo ai componenti richiesti dall'app.</span><span class="sxs-lookup"><span data-stu-id="9161c-148">Limit the number and size of user claims for request processing to only what the app requires.</span></span>
* <span data-ttu-id="9161c-149">Usare un personalizzato <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.ITicketStore> per il :::no-loc(Cookie)::: middleware di autenticazione <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationOptions.SessionStore> per archiviare l'identità tra le richieste.</span><span class="sxs-lookup"><span data-stu-id="9161c-149">Use a custom <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.ITicketStore> for the :::no-loc(Cookie)::: Authentication Middleware's <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationOptions.SessionStore> to store identity across requests.</span></span> <span data-ttu-id="9161c-150">Mantenere grandi quantità di informazioni sull'identità sul server inviando al client solo una chiave di identificatore di sessione di piccole dimensioni.</span><span class="sxs-lookup"><span data-stu-id="9161c-150">Preserve large quantities of identity information on the server while only sending a small session identifier key to the client.</span></span>

## <a name="save-the-access-token"></a><span data-ttu-id="9161c-151">Salvare il token di accesso</span><span class="sxs-lookup"><span data-stu-id="9161c-151">Save the access token</span></span>

<span data-ttu-id="9161c-152"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> definisce se i token di accesso e di aggiornamento devono essere archiviati in <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> dopo un'autorizzazione riuscita.</span><span class="sxs-lookup"><span data-stu-id="9161c-152"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> defines whether access and refresh tokens should be stored in the <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> after a successful authorization.</span></span> <span data-ttu-id="9161c-153">`SaveTokens` è impostato su per `false` impostazione predefinita per ridurre le dimensioni dell'autenticazione finale :::no-loc(cookie)::: .</span><span class="sxs-lookup"><span data-stu-id="9161c-153">`SaveTokens` is set to `false` by default to reduce the size of the final authentication :::no-loc(cookie):::.</span></span>

<span data-ttu-id="9161c-154">L'app di esempio imposta il valore di `SaveTokens` su `true` in <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> :</span><span class="sxs-lookup"><span data-stu-id="9161c-154">The sample app sets the value of `SaveTokens` to `true` in <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=15)]

<span data-ttu-id="9161c-155">Quando `OnPostConfirmationAsync` viene eseguito, archivia il token di accesso ([ExternalLoginInfo. AuthenticationTokens](xref:Microsoft.AspNetCore.:::no-loc(Identity):::.ExternalLoginInfo.AuthenticationTokens*)) dal provider esterno in `ApplicationUser` `AuthenticationProperties` .</span><span class="sxs-lookup"><span data-stu-id="9161c-155">When `OnPostConfirmationAsync` executes, store the access token ([ExternalLoginInfo.AuthenticationTokens](xref:Microsoft.AspNetCore.:::no-loc(Identity):::.ExternalLoginInfo.AuthenticationTokens*)) from the external provider in the `ApplicationUser`'s `AuthenticationProperties`.</span></span>

<span data-ttu-id="9161c-156">L'app di esempio salva il token di accesso in `OnPostConfirmationAsync` (nuova registrazione utente) e `OnGetCallbackAsync` (utente registrato in precedenza) in *account/ExternalLogin. cshtml. cs* :</span><span class="sxs-lookup"><span data-stu-id="9161c-156">The sample app saves the access token in `OnPostConfirmationAsync` (new user registration) and `OnGetCallbackAsync` (previously registered user) in *Account/ExternalLogin.cshtml.cs* :</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Areas/:::no-loc(Identity):::/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=54-56)]

## <a name="how-to-add-additional-custom-tokens"></a><span data-ttu-id="9161c-157">Come aggiungere token personalizzati aggiuntivi</span><span class="sxs-lookup"><span data-stu-id="9161c-157">How to add additional custom tokens</span></span>

<span data-ttu-id="9161c-158">Per illustrare come aggiungere un token personalizzato, archiviato come parte di `SaveTokens` , l'app di esempio aggiunge un oggetto <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> con l'oggetto corrente <xref:System.DateTime> per un [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) di `TicketCreated` :</span><span class="sxs-lookup"><span data-stu-id="9161c-158">To demonstrate how to add a custom token, which is stored as part of `SaveTokens`, the sample app adds an <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> with the current <xref:System.DateTime> for an [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) of `TicketCreated`:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=17-30)]

## <a name="creating-and-adding-claims"></a><span data-ttu-id="9161c-159">Creazione e aggiunta di attestazioni</span><span class="sxs-lookup"><span data-stu-id="9161c-159">Creating and adding claims</span></span>

<span data-ttu-id="9161c-160">Il Framework fornisce azioni comuni e metodi di estensione per la creazione e l'aggiunta di attestazioni alla raccolta.</span><span class="sxs-lookup"><span data-stu-id="9161c-160">The framework provides common actions and extension methods for creating and adding claims to the collection.</span></span> <span data-ttu-id="9161c-161">Per altre informazioni, vedere <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> e <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.</span><span class="sxs-lookup"><span data-stu-id="9161c-161">For more information, see the <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> and <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.</span></span>

<span data-ttu-id="9161c-162">Gli utenti possono definire azioni personalizzate derivando da <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> e implementando il <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> metodo astratto.</span><span class="sxs-lookup"><span data-stu-id="9161c-162">Users can define custom actions by deriving from <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> and implementing the abstract <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> method.</span></span>

<span data-ttu-id="9161c-163">Per altre informazioni, vedere <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span><span class="sxs-lookup"><span data-stu-id="9161c-163">For more information, see <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span></span>

## <a name="removal-of-claim-actions-and-claims"></a><span data-ttu-id="9161c-164">Rimozione di azioni attestazioni e attestazioni</span><span class="sxs-lookup"><span data-stu-id="9161c-164">Removal of claim actions and claims</span></span>

<span data-ttu-id="9161c-165">[ClaimActionCollection. Remove (String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) rimuove tutte le azioni di attestazione per l'oggetto specificato <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> dalla raccolta.</span><span class="sxs-lookup"><span data-stu-id="9161c-165">[ClaimActionCollection.Remove(String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) removes all claim actions for the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the collection.</span></span> <span data-ttu-id="9161c-166">[ClaimActionCollectionMapExtensions. DeleteClaim (ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) Elimina un'attestazione dell'oggetto specificato <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> dall'identità.</span><span class="sxs-lookup"><span data-stu-id="9161c-166">[ClaimActionCollectionMapExtensions.DeleteClaim(ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) deletes a claim of the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the identity.</span></span> <span data-ttu-id="9161c-167"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> viene utilizzato principalmente con [OpenID Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) per rimuovere le attestazioni generate dal protocollo.</span><span class="sxs-lookup"><span data-stu-id="9161c-167"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> is primarily used with [OpenID Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) to remove protocol-generated claims.</span></span>

## <a name="sample-app-output"></a><span data-ttu-id="9161c-168">Esempio di output dell'app</span><span class="sxs-lookup"><span data-stu-id="9161c-168">Sample app output</span></span>

```
User Claims

http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier
    9b342344f-7aab-43c2-1ac1-ba75912ca999
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name
    someone@gmail.com
AspNet.:::no-loc(Identity):::.SecurityStamp
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

<span data-ttu-id="9161c-169">Un'app ASP.NET Core può creare attestazioni e token aggiuntivi da provider di autenticazione esterni, ad esempio Facebook, Google, Microsoft e Twitter.</span><span class="sxs-lookup"><span data-stu-id="9161c-169">An ASP.NET Core app can establish additional claims and tokens from external authentication providers, such as Facebook, Google, Microsoft, and Twitter.</span></span> <span data-ttu-id="9161c-170">Ogni provider rivela informazioni diverse sugli utenti sulla propria piattaforma, ma il modello per la ricezione e la trasformazione dei dati utente in attestazioni aggiuntive è lo stesso.</span><span class="sxs-lookup"><span data-stu-id="9161c-170">Each provider reveals different information about users on its platform, but the pattern for receiving and transforming user data into additional claims is the same.</span></span>

<span data-ttu-id="9161c-171">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="9161c-171">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="9161c-172">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="9161c-172">Prerequisites</span></span>

<span data-ttu-id="9161c-173">Decidere quali provider di autenticazione esterni supportare nell'app.</span><span class="sxs-lookup"><span data-stu-id="9161c-173">Decide which external authentication providers to support in the app.</span></span> <span data-ttu-id="9161c-174">Per ogni provider, registrare l'app e ottenere un ID client e un segreto client.</span><span class="sxs-lookup"><span data-stu-id="9161c-174">For each provider, register the app and obtain a client ID and client secret.</span></span> <span data-ttu-id="9161c-175">Per altre informazioni, vedere <xref:security/authentication/social/index>.</span><span class="sxs-lookup"><span data-stu-id="9161c-175">For more information, see <xref:security/authentication/social/index>.</span></span> <span data-ttu-id="9161c-176">L'app di esempio usa il [provider di autenticazione Google](xref:security/authentication/google-logins).</span><span class="sxs-lookup"><span data-stu-id="9161c-176">The sample app uses the [Google authentication provider](xref:security/authentication/google-logins).</span></span>

## <a name="set-the-client-id-and-client-secret"></a><span data-ttu-id="9161c-177">Impostare l'ID client e il segreto client</span><span class="sxs-lookup"><span data-stu-id="9161c-177">Set the client ID and client secret</span></span>

<span data-ttu-id="9161c-178">Il provider di autenticazione OAuth stabilisce una relazione di trust con un'app che usa un ID client e un segreto client.</span><span class="sxs-lookup"><span data-stu-id="9161c-178">The OAuth authentication provider establishes a trust relationship with an app using a client ID and client secret.</span></span> <span data-ttu-id="9161c-179">I valori relativi all'ID client e al segreto client vengono creati per l'app dal provider di autenticazione esterno quando l'app viene registrata con il provider.</span><span class="sxs-lookup"><span data-stu-id="9161c-179">Client ID and client secret values are created for the app by the external authentication provider when the app is registered with the provider.</span></span> <span data-ttu-id="9161c-180">Ogni provider esterno usato dall'app deve essere configurato in modo indipendente con l'ID client e il segreto client del provider.</span><span class="sxs-lookup"><span data-stu-id="9161c-180">Each external provider that the app uses must be configured independently with the provider's client ID and client secret.</span></span> <span data-ttu-id="9161c-181">Per ulteriori informazioni, vedere gli argomenti del provider di autenticazione esterno applicabili allo scenario:</span><span class="sxs-lookup"><span data-stu-id="9161c-181">For more information, see the external authentication provider topics that apply to your scenario:</span></span>

* [<span data-ttu-id="9161c-182">Autenticazione Facebook</span><span class="sxs-lookup"><span data-stu-id="9161c-182">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="9161c-183">Autenticazione Google</span><span class="sxs-lookup"><span data-stu-id="9161c-183">Google authentication</span></span>](xref:security/authentication/google-logins)
* [<span data-ttu-id="9161c-184">Autenticazione Microsoft</span><span class="sxs-lookup"><span data-stu-id="9161c-184">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="9161c-185">Autenticazione Twitter</span><span class="sxs-lookup"><span data-stu-id="9161c-185">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="9161c-186">Altri provider di autenticazione</span><span class="sxs-lookup"><span data-stu-id="9161c-186">Other authentication providers</span></span>](xref:security/authentication/otherlogins)
* [<span data-ttu-id="9161c-187">OpenIdConnect</span><span class="sxs-lookup"><span data-stu-id="9161c-187">OpenIdConnect</span></span>](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)

<span data-ttu-id="9161c-188">L'app di esempio configura il provider di autenticazione Google con un ID client e un segreto client forniti da Google:</span><span class="sxs-lookup"><span data-stu-id="9161c-188">The sample app configures the Google authentication provider with a client ID and client secret provided by Google:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=4,9)]

## <a name="establish-the-authentication-scope"></a><span data-ttu-id="9161c-189">Definire l'ambito di autenticazione</span><span class="sxs-lookup"><span data-stu-id="9161c-189">Establish the authentication scope</span></span>

<span data-ttu-id="9161c-190">Specificare l'elenco di autorizzazioni da recuperare dal provider specificando <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*> .</span><span class="sxs-lookup"><span data-stu-id="9161c-190">Specify the list of permissions to retrieve from the provider by specifying the <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*>.</span></span> <span data-ttu-id="9161c-191">Gli ambiti di autenticazione per i provider esterni comuni sono riportati nella tabella seguente.</span><span class="sxs-lookup"><span data-stu-id="9161c-191">Authentication scopes for common external providers appear in the following table.</span></span>

| <span data-ttu-id="9161c-192">Provider</span><span class="sxs-lookup"><span data-stu-id="9161c-192">Provider</span></span>  | <span data-ttu-id="9161c-193">Ambito</span><span class="sxs-lookup"><span data-stu-id="9161c-193">Scope</span></span>                                                            |
| --------- | ---------------------------------------------------------------- |
| <span data-ttu-id="9161c-194">Facebook</span><span class="sxs-lookup"><span data-stu-id="9161c-194">Facebook</span></span>  | `https://www.facebook.com/dialog/oauth`                          |
| <span data-ttu-id="9161c-195">Google</span><span class="sxs-lookup"><span data-stu-id="9161c-195">Google</span></span>    | `https://www.googleapis.com/auth/userinfo.profile`               |
| <span data-ttu-id="9161c-196">Microsoft</span><span class="sxs-lookup"><span data-stu-id="9161c-196">Microsoft</span></span> | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| <span data-ttu-id="9161c-197">Twitter</span><span class="sxs-lookup"><span data-stu-id="9161c-197">Twitter</span></span>   | `https://api.twitter.com/oauth/authenticate`                     |

<span data-ttu-id="9161c-198">Nell'app di esempio, `userinfo.profile` l'ambito di Google viene aggiunto automaticamente dal framework quando <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> viene chiamato su <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="9161c-198">In the sample app, Google's `userinfo.profile` scope is automatically added by the framework when <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> is called on the <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder>.</span></span> <span data-ttu-id="9161c-199">Se l'app richiede ambiti aggiuntivi, aggiungerli alle opzioni.</span><span class="sxs-lookup"><span data-stu-id="9161c-199">If the app requires additional scopes, add them to the options.</span></span> <span data-ttu-id="9161c-200">Nell'esempio seguente `https://www.googleapis.com/auth/user.birthday.read` viene aggiunto l'ambito Google per recuperare il compleanno di un utente:</span><span class="sxs-lookup"><span data-stu-id="9161c-200">In the following example, the Google `https://www.googleapis.com/auth/user.birthday.read` scope is added in order to retrieve a user's birthday:</span></span>

```csharp
options.Scope.Add("https://www.googleapis.com/auth/user.birthday.read");
```

## <a name="map-user-data-keys-and-create-claims"></a><span data-ttu-id="9161c-201">Eseguire il mapping delle chiavi di dati utente e creare attestazioni</span><span class="sxs-lookup"><span data-stu-id="9161c-201">Map user data keys and create claims</span></span>

<span data-ttu-id="9161c-202">Nelle opzioni del provider specificare un <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> o <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> per ogni chiave/sottochiave nei dati utente JSON del provider esterno per l'identità dell'app da leggere durante l'accesso.</span><span class="sxs-lookup"><span data-stu-id="9161c-202">In the provider's options, specify a <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> or <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> for each key/subkey in the external provider's JSON user data for the app identity to read on sign in.</span></span> <span data-ttu-id="9161c-203">Per ulteriori informazioni sui tipi di attestazione, vedere <xref:System.Security.Claims.ClaimTypes> .</span><span class="sxs-lookup"><span data-stu-id="9161c-203">For more information on claim types, see <xref:System.Security.Claims.ClaimTypes>.</span></span>

<span data-ttu-id="9161c-204">L'app di esempio crea attestazioni delle impostazioni locali ( `urn:google:locale` ) e immagini ( `urn:google:picture` ) dalle `locale` `picture` chiavi e nei dati utente di Google:</span><span class="sxs-lookup"><span data-stu-id="9161c-204">The sample app creates locale (`urn:google:locale`) and picture (`urn:google:picture`) claims from the `locale` and `picture` keys in Google user data:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=13-14)]

<span data-ttu-id="9161c-205">In `Microsoft.AspNetCore.:::no-loc(Identity):::.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync` , un <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.:::no-loc(Identity):::User> ( `ApplicationUser` ) è connesso all'app con <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.SignInManager%601.SignInAsync*> .</span><span class="sxs-lookup"><span data-stu-id="9161c-205">In `Microsoft.AspNetCore.:::no-loc(Identity):::.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync`, an <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.:::no-loc(Identity):::User> (`ApplicationUser`) is signed into the app with <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.SignInManager%601.SignInAsync*>.</span></span> <span data-ttu-id="9161c-206">Durante il processo di accesso, il <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.UserManager%601> può archiviare `ApplicationUser` attestazioni per i dati utente disponibili da <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.ExternalLoginInfo.Principal*> .</span><span class="sxs-lookup"><span data-stu-id="9161c-206">During the sign in process, the <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.UserManager%601> can store an `ApplicationUser` claims for user data available from the <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.ExternalLoginInfo.Principal*>.</span></span>

<span data-ttu-id="9161c-207">Nell'app di esempio `OnPostConfirmationAsync` ( *account/ExternalLogin. cshtml. cs* ) vengono stabilite le attestazioni delle impostazioni locali ( `urn:google:locale` ) e immagine ( `urn:google:picture` ) per l'accesso `ApplicationUser` , inclusa un'attestazione per <xref:System.Security.Claims.ClaimTypes.GivenName> :</span><span class="sxs-lookup"><span data-stu-id="9161c-207">In the sample app, `OnPostConfirmationAsync` ( *Account/ExternalLogin.cshtml.cs* ) establishes the locale (`urn:google:locale`) and picture (`urn:google:picture`) claims for the signed in `ApplicationUser`, including a claim for <xref:System.Security.Claims.ClaimTypes.GivenName>:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Areas/:::no-loc(Identity):::/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=35-51)]

<span data-ttu-id="9161c-208">Per impostazione predefinita, le attestazioni di un utente vengono archiviate nell'autenticazione di :::no-loc(cookie)::: .</span><span class="sxs-lookup"><span data-stu-id="9161c-208">By default, a user's claims are stored in the authentication :::no-loc(cookie):::.</span></span> <span data-ttu-id="9161c-209">Se l'autenticazione :::no-loc(cookie)::: è troppo grande, può causare un errore dell'app perché:</span><span class="sxs-lookup"><span data-stu-id="9161c-209">If the authentication :::no-loc(cookie)::: is too large, it can cause the app to fail because:</span></span>

* <span data-ttu-id="9161c-210">Il browser rileva che l' :::no-loc(cookie)::: intestazione è troppo lungo.</span><span class="sxs-lookup"><span data-stu-id="9161c-210">The browser detects that the :::no-loc(cookie)::: header is too long.</span></span>
* <span data-ttu-id="9161c-211">La dimensione complessiva della richiesta è troppo grande.</span><span class="sxs-lookup"><span data-stu-id="9161c-211">The overall size of the request is too large.</span></span>

<span data-ttu-id="9161c-212">Se per l'elaborazione delle richieste utente è richiesta una grande quantità di dati utente:</span><span class="sxs-lookup"><span data-stu-id="9161c-212">If a large amount of user data is required for processing user requests:</span></span>

* <span data-ttu-id="9161c-213">Limitare il numero e le dimensioni delle attestazioni utente per l'elaborazione delle richieste solo ai componenti richiesti dall'app.</span><span class="sxs-lookup"><span data-stu-id="9161c-213">Limit the number and size of user claims for request processing to only what the app requires.</span></span>
* <span data-ttu-id="9161c-214">Usare un personalizzato <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.ITicketStore> per il :::no-loc(Cookie)::: middleware di autenticazione <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationOptions.SessionStore> per archiviare l'identità tra le richieste.</span><span class="sxs-lookup"><span data-stu-id="9161c-214">Use a custom <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.ITicketStore> for the :::no-loc(Cookie)::: Authentication Middleware's <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationOptions.SessionStore> to store identity across requests.</span></span> <span data-ttu-id="9161c-215">Mantenere grandi quantità di informazioni sull'identità sul server inviando al client solo una chiave di identificatore di sessione di piccole dimensioni.</span><span class="sxs-lookup"><span data-stu-id="9161c-215">Preserve large quantities of identity information on the server while only sending a small session identifier key to the client.</span></span>

## <a name="save-the-access-token"></a><span data-ttu-id="9161c-216">Salvare il token di accesso</span><span class="sxs-lookup"><span data-stu-id="9161c-216">Save the access token</span></span>

<span data-ttu-id="9161c-217"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> definisce se i token di accesso e di aggiornamento devono essere archiviati in <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> dopo un'autorizzazione riuscita.</span><span class="sxs-lookup"><span data-stu-id="9161c-217"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> defines whether access and refresh tokens should be stored in the <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> after a successful authorization.</span></span> <span data-ttu-id="9161c-218">`SaveTokens` è impostato su per `false` impostazione predefinita per ridurre le dimensioni dell'autenticazione finale :::no-loc(cookie)::: .</span><span class="sxs-lookup"><span data-stu-id="9161c-218">`SaveTokens` is set to `false` by default to reduce the size of the final authentication :::no-loc(cookie):::.</span></span>

<span data-ttu-id="9161c-219">L'app di esempio imposta il valore di `SaveTokens` su `true` in <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> :</span><span class="sxs-lookup"><span data-stu-id="9161c-219">The sample app sets the value of `SaveTokens` to `true` in <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=15)]

<span data-ttu-id="9161c-220">Quando `OnPostConfirmationAsync` viene eseguito, archivia il token di accesso ([ExternalLoginInfo. AuthenticationTokens](xref:Microsoft.AspNetCore.:::no-loc(Identity):::.ExternalLoginInfo.AuthenticationTokens*)) dal provider esterno in `ApplicationUser` `AuthenticationProperties` .</span><span class="sxs-lookup"><span data-stu-id="9161c-220">When `OnPostConfirmationAsync` executes, store the access token ([ExternalLoginInfo.AuthenticationTokens](xref:Microsoft.AspNetCore.:::no-loc(Identity):::.ExternalLoginInfo.AuthenticationTokens*)) from the external provider in the `ApplicationUser`'s `AuthenticationProperties`.</span></span>

<span data-ttu-id="9161c-221">L'app di esempio salva il token di accesso in `OnPostConfirmationAsync` (nuova registrazione utente) e `OnGetCallbackAsync` (utente registrato in precedenza) in *account/ExternalLogin. cshtml. cs* :</span><span class="sxs-lookup"><span data-stu-id="9161c-221">The sample app saves the access token in `OnPostConfirmationAsync` (new user registration) and `OnGetCallbackAsync` (previously registered user) in *Account/ExternalLogin.cshtml.cs* :</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Areas/:::no-loc(Identity):::/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=54-56)]

## <a name="how-to-add-additional-custom-tokens"></a><span data-ttu-id="9161c-222">Come aggiungere token personalizzati aggiuntivi</span><span class="sxs-lookup"><span data-stu-id="9161c-222">How to add additional custom tokens</span></span>

<span data-ttu-id="9161c-223">Per illustrare come aggiungere un token personalizzato, archiviato come parte di `SaveTokens` , l'app di esempio aggiunge un oggetto <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> con l'oggetto corrente <xref:System.DateTime> per un [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) di `TicketCreated` :</span><span class="sxs-lookup"><span data-stu-id="9161c-223">To demonstrate how to add a custom token, which is stored as part of `SaveTokens`, the sample app adds an <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> with the current <xref:System.DateTime> for an [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) of `TicketCreated`:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=17-30)]

## <a name="creating-and-adding-claims"></a><span data-ttu-id="9161c-224">Creazione e aggiunta di attestazioni</span><span class="sxs-lookup"><span data-stu-id="9161c-224">Creating and adding claims</span></span>

<span data-ttu-id="9161c-225">Il Framework fornisce azioni comuni e metodi di estensione per la creazione e l'aggiunta di attestazioni alla raccolta.</span><span class="sxs-lookup"><span data-stu-id="9161c-225">The framework provides common actions and extension methods for creating and adding claims to the collection.</span></span> <span data-ttu-id="9161c-226">Per altre informazioni, vedere <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> e <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.</span><span class="sxs-lookup"><span data-stu-id="9161c-226">For more information, see the <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> and <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.</span></span>

<span data-ttu-id="9161c-227">Gli utenti possono definire azioni personalizzate derivando da <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> e implementando il <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> metodo astratto.</span><span class="sxs-lookup"><span data-stu-id="9161c-227">Users can define custom actions by deriving from <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> and implementing the abstract <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> method.</span></span>

<span data-ttu-id="9161c-228">Per altre informazioni, vedere <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span><span class="sxs-lookup"><span data-stu-id="9161c-228">For more information, see <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span></span>

## <a name="removal-of-claim-actions-and-claims"></a><span data-ttu-id="9161c-229">Rimozione di azioni attestazioni e attestazioni</span><span class="sxs-lookup"><span data-stu-id="9161c-229">Removal of claim actions and claims</span></span>

<span data-ttu-id="9161c-230">[ClaimActionCollection. Remove (String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) rimuove tutte le azioni di attestazione per l'oggetto specificato <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> dalla raccolta.</span><span class="sxs-lookup"><span data-stu-id="9161c-230">[ClaimActionCollection.Remove(String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) removes all claim actions for the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the collection.</span></span> <span data-ttu-id="9161c-231">[ClaimActionCollectionMapExtensions. DeleteClaim (ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) Elimina un'attestazione dell'oggetto specificato <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> dall'identità.</span><span class="sxs-lookup"><span data-stu-id="9161c-231">[ClaimActionCollectionMapExtensions.DeleteClaim(ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) deletes a claim of the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the identity.</span></span> <span data-ttu-id="9161c-232"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> viene utilizzato principalmente con [OpenID Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) per rimuovere le attestazioni generate dal protocollo.</span><span class="sxs-lookup"><span data-stu-id="9161c-232"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> is primarily used with [OpenID Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) to remove protocol-generated claims.</span></span>

## <a name="sample-app-output"></a><span data-ttu-id="9161c-233">Esempio di output dell'app</span><span class="sxs-lookup"><span data-stu-id="9161c-233">Sample app output</span></span>

```
User Claims

http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier
    9b342344f-7aab-43c2-1ac1-ba75912ca999
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name
    someone@gmail.com
AspNet.:::no-loc(Identity):::.SecurityStamp
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

## <a name="additional-resources"></a><span data-ttu-id="9161c-234">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="9161c-234">Additional resources</span></span>

* <span data-ttu-id="9161c-235">[app SocialSample di progettazione DotNet/AspNetCore](https://github.com/dotnet/AspNetCore/tree/master/src/Security/Authentication/samples/SocialSample): l'app di esempio collegata si trova nel ramo di progettazione del [repository GitHub DotNet/AspNetCore](https://github.com/dotnet/AspNetCore) `master` .</span><span class="sxs-lookup"><span data-stu-id="9161c-235">[dotnet/AspNetCore engineering SocialSample app](https://github.com/dotnet/AspNetCore/tree/master/src/Security/Authentication/samples/SocialSample): The linked sample app is on the [dotnet/AspNetCore GitHub repo's](https://github.com/dotnet/AspNetCore) `master` engineering branch.</span></span> <span data-ttu-id="9161c-236">Il `master` ramo contiene il codice in fase di sviluppo attivo per la prossima versione di ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="9161c-236">The `master` branch contains code under active development for the next release of ASP.NET Core.</span></span> <span data-ttu-id="9161c-237">Per visualizzare una versione dell'app di esempio per una versione rilasciata di ASP.NET Core, usare l'elenco a discesa **Branch** per selezionare un ramo di rilascio (ad esempio `release/{X.Y}` ).</span><span class="sxs-lookup"><span data-stu-id="9161c-237">To see a version of the sample app for a released version of ASP.NET Core, use the **Branch** drop down list to select a release branch (for example `release/{X.Y}`).</span></span>
