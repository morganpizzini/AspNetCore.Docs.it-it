---
title: Mantieni attestazioni e token aggiuntivi da provider esterni in ASP.NET Core
author: rick-anderson
description: Informazioni su come creare attestazioni e token aggiuntivi da provider esterni.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/15/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/social/additional-claims
ms.openlocfilehash: 6acc1d78bf5cc39fd69329bad1cff0fbe52d9358
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82769030"
---
# <a name="persist-additional-claims-and-tokens-from-external-providers-in-aspnet-core"></a><span data-ttu-id="3a1d2-103">Mantieni attestazioni e token aggiuntivi da provider esterni in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3a1d2-103">Persist additional claims and tokens from external providers in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="3a1d2-104">Un'app ASP.NET Core può creare attestazioni e token aggiuntivi da provider di autenticazione esterni, ad esempio Facebook, Google, Microsoft e Twitter.</span><span class="sxs-lookup"><span data-stu-id="3a1d2-104">An ASP.NET Core app can establish additional claims and tokens from external authentication providers, such as Facebook, Google, Microsoft, and Twitter.</span></span> <span data-ttu-id="3a1d2-105">Ogni provider rivela informazioni diverse sugli utenti sulla propria piattaforma, ma il modello per la ricezione e la trasformazione dei dati utente in attestazioni aggiuntive è lo stesso.</span><span class="sxs-lookup"><span data-stu-id="3a1d2-105">Each provider reveals different information about users on its platform, but the pattern for receiving and transforming user data into additional claims is the same.</span></span>

<span data-ttu-id="3a1d2-106">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="3a1d2-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="3a1d2-107">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="3a1d2-107">Prerequisites</span></span>

<span data-ttu-id="3a1d2-108">Decidere quali provider di autenticazione esterni supportare nell'app.</span><span class="sxs-lookup"><span data-stu-id="3a1d2-108">Decide which external authentication providers to support in the app.</span></span> <span data-ttu-id="3a1d2-109">Per ogni provider, registrare l'app e ottenere un ID client e un segreto client.</span><span class="sxs-lookup"><span data-stu-id="3a1d2-109">For each provider, register the app and obtain a client ID and client secret.</span></span> <span data-ttu-id="3a1d2-110">Per altre informazioni, vedere <xref:security/authentication/social/index>.</span><span class="sxs-lookup"><span data-stu-id="3a1d2-110">For more information, see <xref:security/authentication/social/index>.</span></span> <span data-ttu-id="3a1d2-111">L'app di esempio usa il [provider di autenticazione Google](xref:security/authentication/google-logins).</span><span class="sxs-lookup"><span data-stu-id="3a1d2-111">The sample app uses the [Google authentication provider](xref:security/authentication/google-logins).</span></span>

## <a name="set-the-client-id-and-client-secret"></a><span data-ttu-id="3a1d2-112">Impostare l'ID client e il segreto client</span><span class="sxs-lookup"><span data-stu-id="3a1d2-112">Set the client ID and client secret</span></span>

<span data-ttu-id="3a1d2-113">Il provider di autenticazione OAuth stabilisce una relazione di trust con un'app che usa un ID client e un segreto client.</span><span class="sxs-lookup"><span data-stu-id="3a1d2-113">The OAuth authentication provider establishes a trust relationship with an app using a client ID and client secret.</span></span> <span data-ttu-id="3a1d2-114">I valori relativi all'ID client e al segreto client vengono creati per l'app dal provider di autenticazione esterno quando l'app viene registrata con il provider.</span><span class="sxs-lookup"><span data-stu-id="3a1d2-114">Client ID and client secret values are created for the app by the external authentication provider when the app is registered with the provider.</span></span> <span data-ttu-id="3a1d2-115">Ogni provider esterno usato dall'app deve essere configurato in modo indipendente con l'ID client e il segreto client del provider.</span><span class="sxs-lookup"><span data-stu-id="3a1d2-115">Each external provider that the app uses must be configured independently with the provider's client ID and client secret.</span></span> <span data-ttu-id="3a1d2-116">Per ulteriori informazioni, vedere gli argomenti del provider di autenticazione esterno applicabili allo scenario:</span><span class="sxs-lookup"><span data-stu-id="3a1d2-116">For more information, see the external authentication provider topics that apply to your scenario:</span></span>

* [<span data-ttu-id="3a1d2-117">Autenticazione Facebook</span><span class="sxs-lookup"><span data-stu-id="3a1d2-117">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="3a1d2-118">Autenticazione Google</span><span class="sxs-lookup"><span data-stu-id="3a1d2-118">Google authentication</span></span>](xref:security/authentication/google-logins)
* [<span data-ttu-id="3a1d2-119">Autenticazione Microsoft</span><span class="sxs-lookup"><span data-stu-id="3a1d2-119">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="3a1d2-120">Autenticazione Twitter</span><span class="sxs-lookup"><span data-stu-id="3a1d2-120">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="3a1d2-121">Altri provider di autenticazione</span><span class="sxs-lookup"><span data-stu-id="3a1d2-121">Other authentication providers</span></span>](xref:security/authentication/otherlogins)
* [<span data-ttu-id="3a1d2-122">OpenIdConnect</span><span class="sxs-lookup"><span data-stu-id="3a1d2-122">OpenIdConnect</span></span>](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)

<span data-ttu-id="3a1d2-123">L'app di esempio configura il provider di autenticazione Google con un ID client e un segreto client forniti da Google:</span><span class="sxs-lookup"><span data-stu-id="3a1d2-123">The sample app configures the Google authentication provider with a client ID and client secret provided by Google:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=4,9)]

## <a name="establish-the-authentication-scope"></a><span data-ttu-id="3a1d2-124">Definire l'ambito di autenticazione</span><span class="sxs-lookup"><span data-stu-id="3a1d2-124">Establish the authentication scope</span></span>

<span data-ttu-id="3a1d2-125">Specificare l'elenco di autorizzazioni da recuperare dal provider specificando <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*>.</span><span class="sxs-lookup"><span data-stu-id="3a1d2-125">Specify the list of permissions to retrieve from the provider by specifying the <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*>.</span></span> <span data-ttu-id="3a1d2-126">Gli ambiti di autenticazione per i provider esterni comuni sono riportati nella tabella seguente.</span><span class="sxs-lookup"><span data-stu-id="3a1d2-126">Authentication scopes for common external providers appear in the following table.</span></span>

| <span data-ttu-id="3a1d2-127">Provider</span><span class="sxs-lookup"><span data-stu-id="3a1d2-127">Provider</span></span>  | <span data-ttu-id="3a1d2-128">Ambito</span><span class="sxs-lookup"><span data-stu-id="3a1d2-128">Scope</span></span>                                                            |
| --------- | ---------------------------------------------------------------- |
| <span data-ttu-id="3a1d2-129">Facebook</span><span class="sxs-lookup"><span data-stu-id="3a1d2-129">Facebook</span></span>  | `https://www.facebook.com/dialog/oauth`                          |
| <span data-ttu-id="3a1d2-130">Google</span><span class="sxs-lookup"><span data-stu-id="3a1d2-130">Google</span></span>    | `https://www.googleapis.com/auth/userinfo.profile`               |
| <span data-ttu-id="3a1d2-131">Microsoft</span><span class="sxs-lookup"><span data-stu-id="3a1d2-131">Microsoft</span></span> | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| <span data-ttu-id="3a1d2-132">Twitter</span><span class="sxs-lookup"><span data-stu-id="3a1d2-132">Twitter</span></span>   | `https://api.twitter.com/oauth/authenticate`                     |

<span data-ttu-id="3a1d2-133">Nell'app di esempio, l' `userinfo.profile` ambito di Google viene aggiunto automaticamente dal framework quando <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> viene chiamato su. <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder></span><span class="sxs-lookup"><span data-stu-id="3a1d2-133">In the sample app, Google's `userinfo.profile` scope is automatically added by the framework when <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> is called on the <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder>.</span></span> <span data-ttu-id="3a1d2-134">Se l'app richiede ambiti aggiuntivi, aggiungerli alle opzioni.</span><span class="sxs-lookup"><span data-stu-id="3a1d2-134">If the app requires additional scopes, add them to the options.</span></span> <span data-ttu-id="3a1d2-135">Nell'esempio seguente viene aggiunto l'ambito `https://www.googleapis.com/auth/user.birthday.read` Google per recuperare il compleanno di un utente:</span><span class="sxs-lookup"><span data-stu-id="3a1d2-135">In the following example, the Google `https://www.googleapis.com/auth/user.birthday.read` scope is added in order to retrieve a user's birthday:</span></span>

```csharp
options.Scope.Add("https://www.googleapis.com/auth/user.birthday.read");
```

## <a name="map-user-data-keys-and-create-claims"></a><span data-ttu-id="3a1d2-136">Eseguire il mapping delle chiavi di dati utente e creare attestazioni</span><span class="sxs-lookup"><span data-stu-id="3a1d2-136">Map user data keys and create claims</span></span>

<span data-ttu-id="3a1d2-137">Nelle opzioni del provider specificare un <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> o <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> per ogni chiave/sottochiave nei dati utente JSON del provider esterno per l'identità dell'app da leggere durante l'accesso.</span><span class="sxs-lookup"><span data-stu-id="3a1d2-137">In the provider's options, specify a <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> or <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> for each key/subkey in the external provider's JSON user data for the app identity to read on sign in.</span></span> <span data-ttu-id="3a1d2-138">Per ulteriori informazioni sui tipi di attestazione <xref:System.Security.Claims.ClaimTypes>, vedere.</span><span class="sxs-lookup"><span data-stu-id="3a1d2-138">For more information on claim types, see <xref:System.Security.Claims.ClaimTypes>.</span></span>

<span data-ttu-id="3a1d2-139">L'app di esempio crea attestazioni`urn:google:locale`delle impostazioni locali (`urn:google:picture`) e immagini ( `locale` ) `picture` dalle chiavi e nei dati utente di Google:</span><span class="sxs-lookup"><span data-stu-id="3a1d2-139">The sample app creates locale (`urn:google:locale`) and picture (`urn:google:picture`) claims from the `locale` and `picture` keys in Google user data:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=13-14)]

<span data-ttu-id="3a1d2-140">In `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync`, un <xref:Microsoft.AspNetCore.Identity.IdentityUser> (`ApplicationUser`) è connesso all'app con <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*>.</span><span class="sxs-lookup"><span data-stu-id="3a1d2-140">In `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync`, an <xref:Microsoft.AspNetCore.Identity.IdentityUser> (`ApplicationUser`) is signed into the app with <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*>.</span></span> <span data-ttu-id="3a1d2-141">Durante il processo di accesso, il <xref:Microsoft.AspNetCore.Identity.UserManager%601> può archiviare `ApplicationUser` attestazioni per i dati utente disponibili da <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*>.</span><span class="sxs-lookup"><span data-stu-id="3a1d2-141">During the sign in process, the <xref:Microsoft.AspNetCore.Identity.UserManager%601> can store an `ApplicationUser` claims for user data available from the <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*>.</span></span>

<span data-ttu-id="3a1d2-142">Nell'app di `OnPostConfirmationAsync` esempio (*account/ExternalLogin. cshtml. cs*) vengono stabilite le attestazioni`urn:google:locale`delle impostazioni locali (`urn:google:picture`) e immagine () per `ApplicationUser`l'accesso, inclusa un' <xref:System.Security.Claims.ClaimTypes.GivenName>attestazione per:</span><span class="sxs-lookup"><span data-stu-id="3a1d2-142">In the sample app, `OnPostConfirmationAsync` (*Account/ExternalLogin.cshtml.cs*) establishes the locale (`urn:google:locale`) and picture (`urn:google:picture`) claims for the signed in `ApplicationUser`, including a claim for <xref:System.Security.Claims.ClaimTypes.GivenName>:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=35-51)]

<span data-ttu-id="3a1d2-143">Per impostazione predefinita, le attestazioni di un utente vengono archiviate nel cookie di autenticazione.</span><span class="sxs-lookup"><span data-stu-id="3a1d2-143">By default, a user's claims are stored in the authentication cookie.</span></span> <span data-ttu-id="3a1d2-144">Se il cookie di autenticazione è troppo grande, può causare un errore dell'app perché:</span><span class="sxs-lookup"><span data-stu-id="3a1d2-144">If the authentication cookie is too large, it can cause the app to fail because:</span></span>

* <span data-ttu-id="3a1d2-145">Il browser rileva che l'intestazione del cookie è troppo lungo.</span><span class="sxs-lookup"><span data-stu-id="3a1d2-145">The browser detects that the cookie header is too long.</span></span>
* <span data-ttu-id="3a1d2-146">La dimensione complessiva della richiesta è troppo grande.</span><span class="sxs-lookup"><span data-stu-id="3a1d2-146">The overall size of the request is too large.</span></span>

<span data-ttu-id="3a1d2-147">Se per l'elaborazione delle richieste utente è richiesta una grande quantità di dati utente:</span><span class="sxs-lookup"><span data-stu-id="3a1d2-147">If a large amount of user data is required for processing user requests:</span></span>

* <span data-ttu-id="3a1d2-148">Limitare il numero e le dimensioni delle attestazioni utente per l'elaborazione delle richieste solo ai componenti richiesti dall'app.</span><span class="sxs-lookup"><span data-stu-id="3a1d2-148">Limit the number and size of user claims for request processing to only what the app requires.</span></span>
* <span data-ttu-id="3a1d2-149">Usare un personalizzato <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> per il middleware di autenticazione dei <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> cookie per archiviare l'identità tra le richieste.</span><span class="sxs-lookup"><span data-stu-id="3a1d2-149">Use a custom <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> for the Cookie Authentication Middleware's <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> to store identity across requests.</span></span> <span data-ttu-id="3a1d2-150">Mantenere grandi quantità di informazioni sull'identità sul server inviando al client solo una chiave di identificatore di sessione di piccole dimensioni.</span><span class="sxs-lookup"><span data-stu-id="3a1d2-150">Preserve large quantities of identity information on the server while only sending a small session identifier key to the client.</span></span>

## <a name="save-the-access-token"></a><span data-ttu-id="3a1d2-151">Salvare il token di accesso</span><span class="sxs-lookup"><span data-stu-id="3a1d2-151">Save the access token</span></span>

<span data-ttu-id="3a1d2-152"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*>definisce se i <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> token di accesso e di aggiornamento devono essere archiviati in dopo un'autorizzazione riuscita.</span><span class="sxs-lookup"><span data-stu-id="3a1d2-152"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> defines whether access and refresh tokens should be stored in the <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> after a successful authorization.</span></span> <span data-ttu-id="3a1d2-153">`SaveTokens`per impostazione predefinita `false` , è impostato su per ridurre le dimensioni del cookie di autenticazione finale.</span><span class="sxs-lookup"><span data-stu-id="3a1d2-153">`SaveTokens` is set to `false` by default to reduce the size of the final authentication cookie.</span></span>

<span data-ttu-id="3a1d2-154">L'app di esempio imposta il valore `SaveTokens` di `true` su <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>in:</span><span class="sxs-lookup"><span data-stu-id="3a1d2-154">The sample app sets the value of `SaveTokens` to `true` in <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=15)]

<span data-ttu-id="3a1d2-155">Quando `OnPostConfirmationAsync` viene eseguito, archivia il token di accesso ([ExternalLoginInfo. AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) dal provider esterno in `ApplicationUser` `AuthenticationProperties`.</span><span class="sxs-lookup"><span data-stu-id="3a1d2-155">When `OnPostConfirmationAsync` executes, store the access token ([ExternalLoginInfo.AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) from the external provider in the `ApplicationUser`'s `AuthenticationProperties`.</span></span>

<span data-ttu-id="3a1d2-156">L'app di esempio salva il token di `OnPostConfirmationAsync` accesso in (nuova registrazione utente `OnGetCallbackAsync` ) e (utente registrato in precedenza) in *account/ExternalLogin. cshtml. cs*:</span><span class="sxs-lookup"><span data-stu-id="3a1d2-156">The sample app saves the access token in `OnPostConfirmationAsync` (new user registration) and `OnGetCallbackAsync` (previously registered user) in *Account/ExternalLogin.cshtml.cs*:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=54-56)]

## <a name="how-to-add-additional-custom-tokens"></a><span data-ttu-id="3a1d2-157">Come aggiungere token personalizzati aggiuntivi</span><span class="sxs-lookup"><span data-stu-id="3a1d2-157">How to add additional custom tokens</span></span>

<span data-ttu-id="3a1d2-158">Per illustrare come aggiungere un token personalizzato, archiviato come parte di `SaveTokens`, l'app di esempio aggiunge un oggetto <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> con l'oggetto <xref:System.DateTime> corrente per un [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) di `TicketCreated`:</span><span class="sxs-lookup"><span data-stu-id="3a1d2-158">To demonstrate how to add a custom token, which is stored as part of `SaveTokens`, the sample app adds an <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> with the current <xref:System.DateTime> for an [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) of `TicketCreated`:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=17-30)]

## <a name="creating-and-adding-claims"></a><span data-ttu-id="3a1d2-159">Creazione e aggiunta di attestazioni</span><span class="sxs-lookup"><span data-stu-id="3a1d2-159">Creating and adding claims</span></span>

<span data-ttu-id="3a1d2-160">Il Framework fornisce azioni comuni e metodi di estensione per la creazione e l'aggiunta di attestazioni alla raccolta.</span><span class="sxs-lookup"><span data-stu-id="3a1d2-160">The framework provides common actions and extension methods for creating and adding claims to the collection.</span></span> <span data-ttu-id="3a1d2-161">Per altre informazioni, vedere <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> e <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.</span><span class="sxs-lookup"><span data-stu-id="3a1d2-161">For more information, see the <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> and <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.</span></span>

<span data-ttu-id="3a1d2-162">Gli utenti possono definire azioni personalizzate derivando <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> da e implementando <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> il metodo astratto.</span><span class="sxs-lookup"><span data-stu-id="3a1d2-162">Users can define custom actions by deriving from <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> and implementing the abstract <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> method.</span></span>

<span data-ttu-id="3a1d2-163">Per altre informazioni, vedere <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span><span class="sxs-lookup"><span data-stu-id="3a1d2-163">For more information, see <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span></span>

## <a name="removal-of-claim-actions-and-claims"></a><span data-ttu-id="3a1d2-164">Rimozione di azioni attestazioni e attestazioni</span><span class="sxs-lookup"><span data-stu-id="3a1d2-164">Removal of claim actions and claims</span></span>

<span data-ttu-id="3a1d2-165">[ClaimActionCollection. Remove (String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) rimuove tutte le azioni di attestazione <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> per l'oggetto specificato dalla raccolta.</span><span class="sxs-lookup"><span data-stu-id="3a1d2-165">[ClaimActionCollection.Remove(String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) removes all claim actions for the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the collection.</span></span> <span data-ttu-id="3a1d2-166">[ClaimActionCollectionMapExtensions. DeleteClaim (ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) Elimina un'attestazione dell'oggetto <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> specificato dall'identità.</span><span class="sxs-lookup"><span data-stu-id="3a1d2-166">[ClaimActionCollectionMapExtensions.DeleteClaim(ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) deletes a claim of the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the identity.</span></span> <span data-ttu-id="3a1d2-167"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*>viene utilizzato principalmente con [OpenID Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) per rimuovere le attestazioni generate dal protocollo.</span><span class="sxs-lookup"><span data-stu-id="3a1d2-167"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> is primarily used with [OpenID Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) to remove protocol-generated claims.</span></span>

## <a name="sample-app-output"></a><span data-ttu-id="3a1d2-168">Esempio di output dell'app</span><span class="sxs-lookup"><span data-stu-id="3a1d2-168">Sample app output</span></span>

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

<span data-ttu-id="3a1d2-169">Un'app ASP.NET Core può creare attestazioni e token aggiuntivi da provider di autenticazione esterni, ad esempio Facebook, Google, Microsoft e Twitter.</span><span class="sxs-lookup"><span data-stu-id="3a1d2-169">An ASP.NET Core app can establish additional claims and tokens from external authentication providers, such as Facebook, Google, Microsoft, and Twitter.</span></span> <span data-ttu-id="3a1d2-170">Ogni provider rivela informazioni diverse sugli utenti sulla propria piattaforma, ma il modello per la ricezione e la trasformazione dei dati utente in attestazioni aggiuntive è lo stesso.</span><span class="sxs-lookup"><span data-stu-id="3a1d2-170">Each provider reveals different information about users on its platform, but the pattern for receiving and transforming user data into additional claims is the same.</span></span>

<span data-ttu-id="3a1d2-171">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="3a1d2-171">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="3a1d2-172">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="3a1d2-172">Prerequisites</span></span>

<span data-ttu-id="3a1d2-173">Decidere quali provider di autenticazione esterni supportare nell'app.</span><span class="sxs-lookup"><span data-stu-id="3a1d2-173">Decide which external authentication providers to support in the app.</span></span> <span data-ttu-id="3a1d2-174">Per ogni provider, registrare l'app e ottenere un ID client e un segreto client.</span><span class="sxs-lookup"><span data-stu-id="3a1d2-174">For each provider, register the app and obtain a client ID and client secret.</span></span> <span data-ttu-id="3a1d2-175">Per altre informazioni, vedere <xref:security/authentication/social/index>.</span><span class="sxs-lookup"><span data-stu-id="3a1d2-175">For more information, see <xref:security/authentication/social/index>.</span></span> <span data-ttu-id="3a1d2-176">L'app di esempio usa il [provider di autenticazione Google](xref:security/authentication/google-logins).</span><span class="sxs-lookup"><span data-stu-id="3a1d2-176">The sample app uses the [Google authentication provider](xref:security/authentication/google-logins).</span></span>

## <a name="set-the-client-id-and-client-secret"></a><span data-ttu-id="3a1d2-177">Impostare l'ID client e il segreto client</span><span class="sxs-lookup"><span data-stu-id="3a1d2-177">Set the client ID and client secret</span></span>

<span data-ttu-id="3a1d2-178">Il provider di autenticazione OAuth stabilisce una relazione di trust con un'app che usa un ID client e un segreto client.</span><span class="sxs-lookup"><span data-stu-id="3a1d2-178">The OAuth authentication provider establishes a trust relationship with an app using a client ID and client secret.</span></span> <span data-ttu-id="3a1d2-179">I valori relativi all'ID client e al segreto client vengono creati per l'app dal provider di autenticazione esterno quando l'app viene registrata con il provider.</span><span class="sxs-lookup"><span data-stu-id="3a1d2-179">Client ID and client secret values are created for the app by the external authentication provider when the app is registered with the provider.</span></span> <span data-ttu-id="3a1d2-180">Ogni provider esterno usato dall'app deve essere configurato in modo indipendente con l'ID client e il segreto client del provider.</span><span class="sxs-lookup"><span data-stu-id="3a1d2-180">Each external provider that the app uses must be configured independently with the provider's client ID and client secret.</span></span> <span data-ttu-id="3a1d2-181">Per ulteriori informazioni, vedere gli argomenti del provider di autenticazione esterno applicabili allo scenario:</span><span class="sxs-lookup"><span data-stu-id="3a1d2-181">For more information, see the external authentication provider topics that apply to your scenario:</span></span>

* [<span data-ttu-id="3a1d2-182">Autenticazione Facebook</span><span class="sxs-lookup"><span data-stu-id="3a1d2-182">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="3a1d2-183">Autenticazione Google</span><span class="sxs-lookup"><span data-stu-id="3a1d2-183">Google authentication</span></span>](xref:security/authentication/google-logins)
* [<span data-ttu-id="3a1d2-184">Autenticazione Microsoft</span><span class="sxs-lookup"><span data-stu-id="3a1d2-184">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="3a1d2-185">Autenticazione Twitter</span><span class="sxs-lookup"><span data-stu-id="3a1d2-185">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="3a1d2-186">Altri provider di autenticazione</span><span class="sxs-lookup"><span data-stu-id="3a1d2-186">Other authentication providers</span></span>](xref:security/authentication/otherlogins)
* [<span data-ttu-id="3a1d2-187">OpenIdConnect</span><span class="sxs-lookup"><span data-stu-id="3a1d2-187">OpenIdConnect</span></span>](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)

<span data-ttu-id="3a1d2-188">L'app di esempio configura il provider di autenticazione Google con un ID client e un segreto client forniti da Google:</span><span class="sxs-lookup"><span data-stu-id="3a1d2-188">The sample app configures the Google authentication provider with a client ID and client secret provided by Google:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=4,9)]

## <a name="establish-the-authentication-scope"></a><span data-ttu-id="3a1d2-189">Definire l'ambito di autenticazione</span><span class="sxs-lookup"><span data-stu-id="3a1d2-189">Establish the authentication scope</span></span>

<span data-ttu-id="3a1d2-190">Specificare l'elenco di autorizzazioni da recuperare dal provider specificando <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*>.</span><span class="sxs-lookup"><span data-stu-id="3a1d2-190">Specify the list of permissions to retrieve from the provider by specifying the <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*>.</span></span> <span data-ttu-id="3a1d2-191">Gli ambiti di autenticazione per i provider esterni comuni sono riportati nella tabella seguente.</span><span class="sxs-lookup"><span data-stu-id="3a1d2-191">Authentication scopes for common external providers appear in the following table.</span></span>

| <span data-ttu-id="3a1d2-192">Provider</span><span class="sxs-lookup"><span data-stu-id="3a1d2-192">Provider</span></span>  | <span data-ttu-id="3a1d2-193">Ambito</span><span class="sxs-lookup"><span data-stu-id="3a1d2-193">Scope</span></span>                                                            |
| --------- | ---------------------------------------------------------------- |
| <span data-ttu-id="3a1d2-194">Facebook</span><span class="sxs-lookup"><span data-stu-id="3a1d2-194">Facebook</span></span>  | `https://www.facebook.com/dialog/oauth`                          |
| <span data-ttu-id="3a1d2-195">Google</span><span class="sxs-lookup"><span data-stu-id="3a1d2-195">Google</span></span>    | `https://www.googleapis.com/auth/userinfo.profile`               |
| <span data-ttu-id="3a1d2-196">Microsoft</span><span class="sxs-lookup"><span data-stu-id="3a1d2-196">Microsoft</span></span> | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| <span data-ttu-id="3a1d2-197">Twitter</span><span class="sxs-lookup"><span data-stu-id="3a1d2-197">Twitter</span></span>   | `https://api.twitter.com/oauth/authenticate`                     |

<span data-ttu-id="3a1d2-198">Nell'app di esempio, l' `userinfo.profile` ambito di Google viene aggiunto automaticamente dal framework quando <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> viene chiamato su. <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder></span><span class="sxs-lookup"><span data-stu-id="3a1d2-198">In the sample app, Google's `userinfo.profile` scope is automatically added by the framework when <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> is called on the <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder>.</span></span> <span data-ttu-id="3a1d2-199">Se l'app richiede ambiti aggiuntivi, aggiungerli alle opzioni.</span><span class="sxs-lookup"><span data-stu-id="3a1d2-199">If the app requires additional scopes, add them to the options.</span></span> <span data-ttu-id="3a1d2-200">Nell'esempio seguente viene aggiunto l'ambito `https://www.googleapis.com/auth/user.birthday.read` Google per recuperare il compleanno di un utente:</span><span class="sxs-lookup"><span data-stu-id="3a1d2-200">In the following example, the Google `https://www.googleapis.com/auth/user.birthday.read` scope is added in order to retrieve a user's birthday:</span></span>

```csharp
options.Scope.Add("https://www.googleapis.com/auth/user.birthday.read");
```

## <a name="map-user-data-keys-and-create-claims"></a><span data-ttu-id="3a1d2-201">Eseguire il mapping delle chiavi di dati utente e creare attestazioni</span><span class="sxs-lookup"><span data-stu-id="3a1d2-201">Map user data keys and create claims</span></span>

<span data-ttu-id="3a1d2-202">Nelle opzioni del provider specificare un <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> o <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> per ogni chiave/sottochiave nei dati utente JSON del provider esterno per l'identità dell'app da leggere durante l'accesso.</span><span class="sxs-lookup"><span data-stu-id="3a1d2-202">In the provider's options, specify a <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> or <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> for each key/subkey in the external provider's JSON user data for the app identity to read on sign in.</span></span> <span data-ttu-id="3a1d2-203">Per ulteriori informazioni sui tipi di attestazione <xref:System.Security.Claims.ClaimTypes>, vedere.</span><span class="sxs-lookup"><span data-stu-id="3a1d2-203">For more information on claim types, see <xref:System.Security.Claims.ClaimTypes>.</span></span>

<span data-ttu-id="3a1d2-204">L'app di esempio crea attestazioni`urn:google:locale`delle impostazioni locali (`urn:google:picture`) e immagini ( `locale` ) `picture` dalle chiavi e nei dati utente di Google:</span><span class="sxs-lookup"><span data-stu-id="3a1d2-204">The sample app creates locale (`urn:google:locale`) and picture (`urn:google:picture`) claims from the `locale` and `picture` keys in Google user data:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=13-14)]

<span data-ttu-id="3a1d2-205">In `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync`, un <xref:Microsoft.AspNetCore.Identity.IdentityUser> (`ApplicationUser`) è connesso all'app con <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*>.</span><span class="sxs-lookup"><span data-stu-id="3a1d2-205">In `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync`, an <xref:Microsoft.AspNetCore.Identity.IdentityUser> (`ApplicationUser`) is signed into the app with <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*>.</span></span> <span data-ttu-id="3a1d2-206">Durante il processo di accesso, il <xref:Microsoft.AspNetCore.Identity.UserManager%601> può archiviare `ApplicationUser` attestazioni per i dati utente disponibili da <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*>.</span><span class="sxs-lookup"><span data-stu-id="3a1d2-206">During the sign in process, the <xref:Microsoft.AspNetCore.Identity.UserManager%601> can store an `ApplicationUser` claims for user data available from the <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*>.</span></span>

<span data-ttu-id="3a1d2-207">Nell'app di `OnPostConfirmationAsync` esempio (*account/ExternalLogin. cshtml. cs*) vengono stabilite le attestazioni`urn:google:locale`delle impostazioni locali (`urn:google:picture`) e immagine () per `ApplicationUser`l'accesso, inclusa un' <xref:System.Security.Claims.ClaimTypes.GivenName>attestazione per:</span><span class="sxs-lookup"><span data-stu-id="3a1d2-207">In the sample app, `OnPostConfirmationAsync` (*Account/ExternalLogin.cshtml.cs*) establishes the locale (`urn:google:locale`) and picture (`urn:google:picture`) claims for the signed in `ApplicationUser`, including a claim for <xref:System.Security.Claims.ClaimTypes.GivenName>:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=35-51)]

<span data-ttu-id="3a1d2-208">Per impostazione predefinita, le attestazioni di un utente vengono archiviate nel cookie di autenticazione.</span><span class="sxs-lookup"><span data-stu-id="3a1d2-208">By default, a user's claims are stored in the authentication cookie.</span></span> <span data-ttu-id="3a1d2-209">Se il cookie di autenticazione è troppo grande, può causare un errore dell'app perché:</span><span class="sxs-lookup"><span data-stu-id="3a1d2-209">If the authentication cookie is too large, it can cause the app to fail because:</span></span>

* <span data-ttu-id="3a1d2-210">Il browser rileva che l'intestazione del cookie è troppo lungo.</span><span class="sxs-lookup"><span data-stu-id="3a1d2-210">The browser detects that the cookie header is too long.</span></span>
* <span data-ttu-id="3a1d2-211">La dimensione complessiva della richiesta è troppo grande.</span><span class="sxs-lookup"><span data-stu-id="3a1d2-211">The overall size of the request is too large.</span></span>

<span data-ttu-id="3a1d2-212">Se per l'elaborazione delle richieste utente è richiesta una grande quantità di dati utente:</span><span class="sxs-lookup"><span data-stu-id="3a1d2-212">If a large amount of user data is required for processing user requests:</span></span>

* <span data-ttu-id="3a1d2-213">Limitare il numero e le dimensioni delle attestazioni utente per l'elaborazione delle richieste solo ai componenti richiesti dall'app.</span><span class="sxs-lookup"><span data-stu-id="3a1d2-213">Limit the number and size of user claims for request processing to only what the app requires.</span></span>
* <span data-ttu-id="3a1d2-214">Usare un personalizzato <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> per il middleware di autenticazione dei <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> cookie per archiviare l'identità tra le richieste.</span><span class="sxs-lookup"><span data-stu-id="3a1d2-214">Use a custom <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> for the Cookie Authentication Middleware's <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> to store identity across requests.</span></span> <span data-ttu-id="3a1d2-215">Mantenere grandi quantità di informazioni sull'identità sul server inviando al client solo una chiave di identificatore di sessione di piccole dimensioni.</span><span class="sxs-lookup"><span data-stu-id="3a1d2-215">Preserve large quantities of identity information on the server while only sending a small session identifier key to the client.</span></span>

## <a name="save-the-access-token"></a><span data-ttu-id="3a1d2-216">Salvare il token di accesso</span><span class="sxs-lookup"><span data-stu-id="3a1d2-216">Save the access token</span></span>

<span data-ttu-id="3a1d2-217"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*>definisce se i <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> token di accesso e di aggiornamento devono essere archiviati in dopo un'autorizzazione riuscita.</span><span class="sxs-lookup"><span data-stu-id="3a1d2-217"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> defines whether access and refresh tokens should be stored in the <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> after a successful authorization.</span></span> <span data-ttu-id="3a1d2-218">`SaveTokens`per impostazione predefinita `false` , è impostato su per ridurre le dimensioni del cookie di autenticazione finale.</span><span class="sxs-lookup"><span data-stu-id="3a1d2-218">`SaveTokens` is set to `false` by default to reduce the size of the final authentication cookie.</span></span>

<span data-ttu-id="3a1d2-219">L'app di esempio imposta il valore `SaveTokens` di `true` su <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>in:</span><span class="sxs-lookup"><span data-stu-id="3a1d2-219">The sample app sets the value of `SaveTokens` to `true` in <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=15)]

<span data-ttu-id="3a1d2-220">Quando `OnPostConfirmationAsync` viene eseguito, archivia il token di accesso ([ExternalLoginInfo. AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) dal provider esterno in `ApplicationUser` `AuthenticationProperties`.</span><span class="sxs-lookup"><span data-stu-id="3a1d2-220">When `OnPostConfirmationAsync` executes, store the access token ([ExternalLoginInfo.AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) from the external provider in the `ApplicationUser`'s `AuthenticationProperties`.</span></span>

<span data-ttu-id="3a1d2-221">L'app di esempio salva il token di `OnPostConfirmationAsync` accesso in (nuova registrazione utente `OnGetCallbackAsync` ) e (utente registrato in precedenza) in *account/ExternalLogin. cshtml. cs*:</span><span class="sxs-lookup"><span data-stu-id="3a1d2-221">The sample app saves the access token in `OnPostConfirmationAsync` (new user registration) and `OnGetCallbackAsync` (previously registered user) in *Account/ExternalLogin.cshtml.cs*:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=54-56)]

## <a name="how-to-add-additional-custom-tokens"></a><span data-ttu-id="3a1d2-222">Come aggiungere token personalizzati aggiuntivi</span><span class="sxs-lookup"><span data-stu-id="3a1d2-222">How to add additional custom tokens</span></span>

<span data-ttu-id="3a1d2-223">Per illustrare come aggiungere un token personalizzato, archiviato come parte di `SaveTokens`, l'app di esempio aggiunge un oggetto <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> con l'oggetto <xref:System.DateTime> corrente per un [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) di `TicketCreated`:</span><span class="sxs-lookup"><span data-stu-id="3a1d2-223">To demonstrate how to add a custom token, which is stored as part of `SaveTokens`, the sample app adds an <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> with the current <xref:System.DateTime> for an [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) of `TicketCreated`:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=17-30)]

## <a name="creating-and-adding-claims"></a><span data-ttu-id="3a1d2-224">Creazione e aggiunta di attestazioni</span><span class="sxs-lookup"><span data-stu-id="3a1d2-224">Creating and adding claims</span></span>

<span data-ttu-id="3a1d2-225">Il Framework fornisce azioni comuni e metodi di estensione per la creazione e l'aggiunta di attestazioni alla raccolta.</span><span class="sxs-lookup"><span data-stu-id="3a1d2-225">The framework provides common actions and extension methods for creating and adding claims to the collection.</span></span> <span data-ttu-id="3a1d2-226">Per altre informazioni, vedere <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> e <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.</span><span class="sxs-lookup"><span data-stu-id="3a1d2-226">For more information, see the <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> and <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.</span></span>

<span data-ttu-id="3a1d2-227">Gli utenti possono definire azioni personalizzate derivando <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> da e implementando <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> il metodo astratto.</span><span class="sxs-lookup"><span data-stu-id="3a1d2-227">Users can define custom actions by deriving from <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> and implementing the abstract <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> method.</span></span>

<span data-ttu-id="3a1d2-228">Per altre informazioni, vedere <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span><span class="sxs-lookup"><span data-stu-id="3a1d2-228">For more information, see <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span></span>

## <a name="removal-of-claim-actions-and-claims"></a><span data-ttu-id="3a1d2-229">Rimozione di azioni attestazioni e attestazioni</span><span class="sxs-lookup"><span data-stu-id="3a1d2-229">Removal of claim actions and claims</span></span>

<span data-ttu-id="3a1d2-230">[ClaimActionCollection. Remove (String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) rimuove tutte le azioni di attestazione <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> per l'oggetto specificato dalla raccolta.</span><span class="sxs-lookup"><span data-stu-id="3a1d2-230">[ClaimActionCollection.Remove(String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) removes all claim actions for the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the collection.</span></span> <span data-ttu-id="3a1d2-231">[ClaimActionCollectionMapExtensions. DeleteClaim (ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) Elimina un'attestazione dell'oggetto <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> specificato dall'identità.</span><span class="sxs-lookup"><span data-stu-id="3a1d2-231">[ClaimActionCollectionMapExtensions.DeleteClaim(ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) deletes a claim of the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the identity.</span></span> <span data-ttu-id="3a1d2-232"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*>viene utilizzato principalmente con [OpenID Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) per rimuovere le attestazioni generate dal protocollo.</span><span class="sxs-lookup"><span data-stu-id="3a1d2-232"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> is primarily used with [OpenID Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) to remove protocol-generated claims.</span></span>

## <a name="sample-app-output"></a><span data-ttu-id="3a1d2-233">Esempio di output dell'app</span><span class="sxs-lookup"><span data-stu-id="3a1d2-233">Sample app output</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="3a1d2-234">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="3a1d2-234">Additional resources</span></span>

* <span data-ttu-id="3a1d2-235">&ndash; [app SocialSample di ingegneria DotNet/AspNetCore](https://github.com/dotnet/AspNetCore/tree/master/src/Security/Authentication/samples/SocialSample) l'app di esempio collegata si trova nel ramo di `master` progettazione del [repository GitHub DotNet/AspNetCore](https://github.com/dotnet/AspNetCore) .</span><span class="sxs-lookup"><span data-stu-id="3a1d2-235">[dotnet/AspNetCore engineering SocialSample app](https://github.com/dotnet/AspNetCore/tree/master/src/Security/Authentication/samples/SocialSample) &ndash; The linked sample app is on the [dotnet/AspNetCore GitHub repo's](https://github.com/dotnet/AspNetCore) `master` engineering branch.</span></span> <span data-ttu-id="3a1d2-236">Il `master` ramo contiene il codice in fase di sviluppo attivo per la prossima versione di ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="3a1d2-236">The `master` branch contains code under active development for the next release of ASP.NET Core.</span></span> <span data-ttu-id="3a1d2-237">Per visualizzare una versione dell'app di esempio per una versione rilasciata di ASP.NET Core, usare l'elenco a discesa **Branch** per selezionare un ramo di rilascio `release/{X.Y}`(ad esempio).</span><span class="sxs-lookup"><span data-stu-id="3a1d2-237">To see a version of the sample app for a released version of ASP.NET Core, use the **Branch** drop down list to select a release branch (for example `release/{X.Y}`).</span></span>
