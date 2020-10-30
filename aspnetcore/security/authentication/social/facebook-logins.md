---
title: Configurazione dell'accesso esterno a Facebook in ASP.NET Core
author: rick-anderson
description: Esercitazione con esempi di codice che illustrano l'integrazione dell'autenticazione utente dell'account Facebook in un'app ASP.NET Core esistente.
ms.author: riande
ms.custom: seoapril2019, mvc, seodec18
ms.date: 03/19/2020
monikerRange: '>= aspnetcore-3.0'
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
uid: security/authentication/facebook-logins
ms.openlocfilehash: be0b655645fd2bd0eab9f9c30a65485f386cead3
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053358"
---
# <a name="facebook-external-login-setup-in-aspnet-core"></a><span data-ttu-id="efceb-103">Configurazione dell'accesso esterno a Facebook in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="efceb-103">Facebook external login setup in ASP.NET Core</span></span>

<span data-ttu-id="efceb-104">Da [Valeriy Novytskyy](https://github.com/01binary) e [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="efceb-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<!-- per @rick-anderson and scott addie, don't update images. Remove images and point the customer to the FB set up page. FB needs to maintain  instructions to get key and secret.
-->

<span data-ttu-id="efceb-105">Questa esercitazione con esempi di codice Mostra come consentire agli utenti di accedere con il proprio account Facebook usando un progetto ASP.NET Core 3,0 di esempio creato nella [pagina precedente](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="efceb-105">This tutorial with code examples shows how to enable your users to sign in with their Facebook account using a sample ASP.NET Core 3.0 project created on the [previous page](xref:security/authentication/social/index).</span></span> <span data-ttu-id="efceb-106">Si inizia creando un ID app Facebook seguendo la [procedura ufficiale](https://developers.facebook.com).</span><span class="sxs-lookup"><span data-stu-id="efceb-106">We start by creating a Facebook App ID by following the [official steps](https://developers.facebook.com).</span></span>

## <a name="create-the-app-in-facebook"></a><span data-ttu-id="efceb-107">Creare l'app in Facebook</span><span class="sxs-lookup"><span data-stu-id="efceb-107">Create the app in Facebook</span></span>

* <span data-ttu-id="efceb-108">Aggiungere il pacchetto NuGet [Microsoft. AspNetCore. Authentication. Facebook](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Facebook) al progetto.</span><span class="sxs-lookup"><span data-stu-id="efceb-108">Add the [Microsoft.AspNetCore.Authentication.Facebook](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Facebook) NuGet package to the project.</span></span>

* <span data-ttu-id="efceb-109">Passare alla pagina dell' [app per sviluppatori Facebook](https://developers.facebook.com/apps/) ed eseguire l'accesso.</span><span class="sxs-lookup"><span data-stu-id="efceb-109">Navigate to the [Facebook Developers app](https://developers.facebook.com/apps/) page and sign in.</span></span> <span data-ttu-id="efceb-110">Se non si ha già un account Facebook, usare il collegamento **Iscriviti a Facebook** nella pagina di accesso per crearne uno.</span><span class="sxs-lookup"><span data-stu-id="efceb-110">If you don't already have a Facebook account, use the **Sign up for Facebook** link on the login page to create one.</span></span>  <span data-ttu-id="efceb-111">Dopo aver creato un account Facebook, seguire le istruzioni per registrarsi come sviluppatore di Facebook.</span><span class="sxs-lookup"><span data-stu-id="efceb-111">Once you have a Facebook account, follow the instructions to register as a Facebook Developer.</span></span>

* <span data-ttu-id="efceb-112">Dal menu **app personali** selezionare **Crea app** per creare un nuovo ID app.</span><span class="sxs-lookup"><span data-stu-id="efceb-112">From the **My Apps** menu select **Create App** to create a new App ID.</span></span>

   ![Portale di Facebook for Developers aperto in Microsoft Edge](index/_static/FBMyApps.png)

* <span data-ttu-id="efceb-114">Compilare il modulo e toccare il pulsante **Crea ID app** .</span><span class="sxs-lookup"><span data-stu-id="efceb-114">Fill out the form and tap the **Create App ID** button.</span></span>

  ![Creare un nuovo modulo ID app](index/_static/FBNewAppId.png)

* <span data-ttu-id="efceb-116">Nella scheda nuova app selezionare **Aggiungi un prodotto** .</span><span class="sxs-lookup"><span data-stu-id="efceb-116">On the new App card, select **Add a Product** .</span></span>  <span data-ttu-id="efceb-117">Nella scheda di **accesso di Facebook** fare clic su **Configura**</span><span class="sxs-lookup"><span data-stu-id="efceb-117">On the **Facebook Login** card, click **Set Up**</span></span> 

  ![Pagina impostazione prodotto](index/_static/FBProductSetup.png)

* <span data-ttu-id="efceb-119">Viene avviata la procedura guidata **avvio rapido** con **scegliere una piattaforma** come prima pagina.</span><span class="sxs-lookup"><span data-stu-id="efceb-119">The **Quickstart** wizard launches with **Choose a Platform** as the first page.</span></span> <span data-ttu-id="efceb-120">Ignorare la procedura guidata per ora facendo clic sul collegamento **Impostazioni** di **accesso di Facebook** nel menu in basso a sinistra:</span><span class="sxs-lookup"><span data-stu-id="efceb-120">Bypass the wizard for now by clicking the **FaceBook Login** **Settings** link in the menu on the lower left:</span></span>

  ![Ignora Avvio rapido](index/_static/FBSkipQuickStart.png)

* <span data-ttu-id="efceb-122">Viene visualizzata la pagina **impostazioni OAuth client** :</span><span class="sxs-lookup"><span data-stu-id="efceb-122">You are presented with the **Client OAuth Settings** page:</span></span>

  ![Pagina impostazioni OAuth client](index/_static/FBOAuthSetup.png)

* <span data-ttu-id="efceb-124">Immettere l'URI di sviluppo con */SignIn-Facebook* aggiunto nel campo **Valid URI di reindirizzamento OAuth** (ad esempio: `https://localhost:44320/signin-facebook` ).</span><span class="sxs-lookup"><span data-stu-id="efceb-124">Enter your development URI with */signin-facebook* appended into the **Valid OAuth Redirect URIs** field (for example: `https://localhost:44320/signin-facebook`).</span></span> <span data-ttu-id="efceb-125">L'autenticazione di Facebook configurata più avanti in questa esercitazione gestirà automaticamente le richieste in */SignIn-Facebook* route per implementare il flusso OAuth.</span><span class="sxs-lookup"><span data-stu-id="efceb-125">The Facebook authentication configured later in this tutorial will automatically handle requests at */signin-facebook* route to implement the OAuth flow.</span></span>

> [!NOTE]
> <span data-ttu-id="efceb-126">L'URI */SignIn-Facebook* viene impostato come callback predefinito del provider di autenticazione di Facebook.</span><span class="sxs-lookup"><span data-stu-id="efceb-126">The URI */signin-facebook* is set as the default callback of the Facebook authentication provider.</span></span> <span data-ttu-id="efceb-127">È possibile modificare l'URI di callback predefinito durante la configurazione del middleware di autenticazione di Facebook tramite la proprietà [RemoteAuthenticationOptions. CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) ereditata della classe [FacebookOptions](/dotnet/api/microsoft.aspnetcore.authentication.facebook.facebookoptions) .</span><span class="sxs-lookup"><span data-stu-id="efceb-127">You can change the default callback URI while configuring the Facebook authentication middleware via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [FacebookOptions](/dotnet/api/microsoft.aspnetcore.authentication.facebook.facebookoptions) class.</span></span>

* <span data-ttu-id="efceb-128">Fare clic su **Salva modifiche** .</span><span class="sxs-lookup"><span data-stu-id="efceb-128">Click **Save Changes** .</span></span>

* <span data-ttu-id="efceb-129">Fare clic sul collegamento **Impostazioni**  >  di **base** nella finestra di spostamento a sinistra.</span><span class="sxs-lookup"><span data-stu-id="efceb-129">Click **Settings** > **Basic** link in the left navigation.</span></span>

  <span data-ttu-id="efceb-130">In questa pagina prendere nota del `App ID` e del `App Secret` .</span><span class="sxs-lookup"><span data-stu-id="efceb-130">On this page, make a note of your `App ID` and your `App Secret`.</span></span> <span data-ttu-id="efceb-131">Si aggiungeranno entrambi nell'applicazione ASP.NET Core nella sezione successiva:</span><span class="sxs-lookup"><span data-stu-id="efceb-131">You will add both into your ASP.NET Core application in the next section:</span></span>

* <span data-ttu-id="efceb-132">Quando si distribuisce il sito, è necessario rivedere la pagina di configurazione dell' **account di accesso di Facebook** e registrare un nuovo URI pubblico.</span><span class="sxs-lookup"><span data-stu-id="efceb-132">When deploying the site you need to revisit the **Facebook Login** setup page and register a new public URI.</span></span>

## <a name="store-the-facebook-app-id-and-secret"></a><span data-ttu-id="efceb-133">Archiviare l'ID e il segreto dell'app Facebook</span><span class="sxs-lookup"><span data-stu-id="efceb-133">Store the Facebook app ID and secret</span></span>

<span data-ttu-id="efceb-134">Archiviare le impostazioni riservate, ad esempio l'ID app Facebook e i valori segreti con [gestione Secret](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="efceb-134">Store sensitive settings such as the Facebook app ID and secret values with [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="efceb-135">Per questo esempio, attenersi alla procedura seguente:</span><span class="sxs-lookup"><span data-stu-id="efceb-135">For this sample, use the following steps:</span></span>

1. <span data-ttu-id="efceb-136">Inizializzare il progetto per l'archiviazione segreta in base alle istruzioni riportate in [abilitare l'archiviazione segreta](xref:security/app-secrets#enable-secret-storage).</span><span class="sxs-lookup"><span data-stu-id="efceb-136">Initialize the project for secret storage per the instructions at [Enable secret storage](xref:security/app-secrets#enable-secret-storage).</span></span>
1. <span data-ttu-id="efceb-137">Archiviare le impostazioni sensibili nell'archivio dei segreti locali con le chiavi segrete `Authentication:Facebook:AppId` e `Authentication:Facebook:AppSecret` :</span><span class="sxs-lookup"><span data-stu-id="efceb-137">Store the sensitive settings in the local secret store with the secret keys `Authentication:Facebook:AppId` and `Authentication:Facebook:AppSecret`:</span></span>

    ```dotnetcli
    dotnet user-secrets set "Authentication:Facebook:AppId" "<app-id>"
    dotnet user-secrets set "Authentication:Facebook:AppSecret" "<app-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="configure-facebook-authentication"></a><span data-ttu-id="efceb-138">Configurare l'autenticazione di Facebook</span><span class="sxs-lookup"><span data-stu-id="efceb-138">Configure Facebook Authentication</span></span>

<span data-ttu-id="efceb-139">Aggiungere il servizio Facebook nel `ConfigureServices` metodo nel file *Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="efceb-139">Add the Facebook service in the `ConfigureServices` method in the *Startup.cs* file:</span></span>

```csharp
services.AddAuthentication().AddFacebook(facebookOptions =>
{
    facebookOptions.AppId = Configuration["Authentication:Facebook:AppId"];
    facebookOptions.AppSecret = Configuration["Authentication:Facebook:AppSecret"];
});
```

[!INCLUDE [default settings configuration](includes/default-settings.md)]

## <a name="sign-in-with-facebook"></a><span data-ttu-id="efceb-140">Accedi con Facebook</span><span class="sxs-lookup"><span data-stu-id="efceb-140">Sign in with Facebook</span></span>

* <span data-ttu-id="efceb-141">Eseguire l'app e selezionare **Accedi** .</span><span class="sxs-lookup"><span data-stu-id="efceb-141">Run the app and select **Log in** .</span></span> 
* <span data-ttu-id="efceb-142">In **usare un altro servizio per accedere** selezionare Facebook.</span><span class="sxs-lookup"><span data-stu-id="efceb-142">Under **Use another service to log in.** , select Facebook.</span></span>
* <span data-ttu-id="efceb-143">Si viene reindirizzati a **Facebook** per l'autenticazione.</span><span class="sxs-lookup"><span data-stu-id="efceb-143">You are redirected to **Facebook** for authentication.</span></span>
* <span data-ttu-id="efceb-144">Immettere le credenziali di Facebook.</span><span class="sxs-lookup"><span data-stu-id="efceb-144">Enter your Facebook credentials.</span></span>
* <span data-ttu-id="efceb-145">Si viene reindirizzati di nuovo al sito in cui è possibile impostare la posta elettronica.</span><span class="sxs-lookup"><span data-stu-id="efceb-145">You are redirected back to your site where you can set your email.</span></span>

<span data-ttu-id="efceb-146">A questo punto è stato effettuato l'accesso con le credenziali di Facebook:</span><span class="sxs-lookup"><span data-stu-id="efceb-146">You are now logged in using your Facebook credentials:</span></span>

<a name="react"></a>

## <a name="react-to-cancel-authorize-external-sign-in"></a><span data-ttu-id="efceb-147">Reagire per annullare l'autorizzazione dell'accesso esterno</span><span class="sxs-lookup"><span data-stu-id="efceb-147">React to cancel authorize external sign-in</span></span>

<span data-ttu-id="efceb-148"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.AccessDeniedPath> può fornire un percorso di reindirizzamento all'agente utente quando l'utente non approva la richiesta di autorizzazione richiesta.</span><span class="sxs-lookup"><span data-stu-id="efceb-148"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.AccessDeniedPath> can provide a redirect path to the user agent when the user doesn't approve the requested authorization demand.</span></span>

<span data-ttu-id="efceb-149">Il codice seguente imposta `AccessDeniedPath` su `"/AccessDeniedPathInfo"` :</span><span class="sxs-lookup"><span data-stu-id="efceb-149">The following code sets the `AccessDeniedPath` to `"/AccessDeniedPathInfo"`:</span></span>

[!code-csharp[](~/security/authentication/social/social-code/StartupAccessDeniedPath.cs?name=snippetFB)]

<span data-ttu-id="efceb-150">È consigliabile che la `AccessDeniedPath` pagina contenga le informazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="efceb-150">We recommend the `AccessDeniedPath` page contain the following information:</span></span>

*  <span data-ttu-id="efceb-151">L'autenticazione remota è stata annullata.</span><span class="sxs-lookup"><span data-stu-id="efceb-151">Remote authentication was canceled.</span></span>
* <span data-ttu-id="efceb-152">Questa app richiede l'autenticazione.</span><span class="sxs-lookup"><span data-stu-id="efceb-152">This app requires authentication.</span></span>
* <span data-ttu-id="efceb-153">Per provare a eseguire di nuovo l'accesso, selezionare il collegamento di accesso.</span><span class="sxs-lookup"><span data-stu-id="efceb-153">To try sign-in again, select the Login link.</span></span>

### <a name="test-accessdeniedpath"></a><span data-ttu-id="efceb-154">Test AccessDeniedPath</span><span class="sxs-lookup"><span data-stu-id="efceb-154">Test AccessDeniedPath</span></span>

* <span data-ttu-id="efceb-155">Passare a [Facebook.com](https://www.facebook.com/)</span><span class="sxs-lookup"><span data-stu-id="efceb-155">Navigate to [facebook.com](https://www.facebook.com/)</span></span>
* <span data-ttu-id="efceb-156">Se è stato eseguito l'accesso, è necessario disconnettersi.</span><span class="sxs-lookup"><span data-stu-id="efceb-156">If you are signed in, you must sign out.</span></span>
* <span data-ttu-id="efceb-157">Eseguire l'app e selezionare l'accesso a Facebook.</span><span class="sxs-lookup"><span data-stu-id="efceb-157">Run the app and select Facebook sign-in.</span></span>
* <span data-ttu-id="efceb-158">Selezionare **non ora** .</span><span class="sxs-lookup"><span data-stu-id="efceb-158">Select **Not now** .</span></span> <span data-ttu-id="efceb-159">Si verrà reindirizzati alla `AccessDeniedPath` pagina specificata.</span><span class="sxs-lookup"><span data-stu-id="efceb-159">You are redirected to the specified `AccessDeniedPath` page.</span></span>

<!-- End of React  -->
[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

<span data-ttu-id="efceb-160">Vedere le informazioni di riferimento sull'API [FacebookOptions](/dotnet/api/microsoft.aspnetcore.builder.facebookoptions) per altre informazioni sulle opzioni di configurazione supportate dall'autenticazione di Facebook.</span><span class="sxs-lookup"><span data-stu-id="efceb-160">See the [FacebookOptions](/dotnet/api/microsoft.aspnetcore.builder.facebookoptions) API reference for more information on configuration options supported by Facebook authentication.</span></span> <span data-ttu-id="efceb-161">È possibile utilizzare le opzioni di configurazione per:</span><span class="sxs-lookup"><span data-stu-id="efceb-161">Configuration options can be used to:</span></span>

* <span data-ttu-id="efceb-162">Richiedere informazioni diverse sull'utente.</span><span class="sxs-lookup"><span data-stu-id="efceb-162">Request different information about the user.</span></span>
* <span data-ttu-id="efceb-163">Aggiungere gli argomenti della stringa di query per personalizzare l'esperienza di accesso.</span><span class="sxs-lookup"><span data-stu-id="efceb-163">Add query string arguments to customize the login experience.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="efceb-164">risoluzione dei problemi</span><span class="sxs-lookup"><span data-stu-id="efceb-164">Troubleshooting</span></span>

* <span data-ttu-id="efceb-165">**Solo ASP.NET Core 2. x:** Se :::no-loc(Identity)::: non è configurato chiamando `services.Add:::no-loc(Identity):::` in `ConfigureServices` , il tentativo di eseguire l'autenticazione comporterà *l'eccezione ArgumentException: è necessario specificare l'opzione ' SignInScheme '* .</span><span class="sxs-lookup"><span data-stu-id="efceb-165">**ASP.NET Core 2.x only:** If :::no-loc(Identity)::: isn't configured by calling `services.Add:::no-loc(Identity):::` in `ConfigureServices`, attempting to authenticate will result in *ArgumentException: The 'SignInScheme' option must be provided* .</span></span> <span data-ttu-id="efceb-166">Il modello di progetto usato in questa esercitazione garantisce che questa operazione venga eseguita.</span><span class="sxs-lookup"><span data-stu-id="efceb-166">The project template used in this tutorial ensures that this is done.</span></span>
* <span data-ttu-id="efceb-167">Se il database del sito non è stato creato applicando la migrazione iniziale, si ottiene *un'operazione di database non riuscita durante l'elaborazione dell'* errore di richiesta.</span><span class="sxs-lookup"><span data-stu-id="efceb-167">If the site database has not been created by applying the initial migration, you get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="efceb-168">Toccare **applica migrazioni** per creare il database e aggiornare per continuare a superare l'errore.</span><span class="sxs-lookup"><span data-stu-id="efceb-168">Tap **Apply Migrations** to create the database and refresh to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="efceb-169">Passaggi successivi</span><span class="sxs-lookup"><span data-stu-id="efceb-169">Next steps</span></span>

* <span data-ttu-id="efceb-170">Questo articolo ha illustrato come è possibile eseguire l'autenticazione con Facebook.</span><span class="sxs-lookup"><span data-stu-id="efceb-170">This article showed how you can authenticate with Facebook.</span></span> <span data-ttu-id="efceb-171">È possibile seguire un approccio simile per l'autenticazione con altri provider elencati nella [pagina precedente](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="efceb-171">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>

* <span data-ttu-id="efceb-172">Dopo aver pubblicato il sito Web nell'app Web di Azure, è necessario reimpostare il `AppSecret` nel portale per sviluppatori di Facebook.</span><span class="sxs-lookup"><span data-stu-id="efceb-172">Once you publish your web site to Azure web app, you should reset the `AppSecret` in the Facebook developer portal.</span></span>

* <span data-ttu-id="efceb-173">Impostare `Authentication:Facebook:AppId` e `Authentication:Facebook:AppSecret` come impostazioni dell'applicazione nella portale di Azure.</span><span class="sxs-lookup"><span data-stu-id="efceb-173">Set the `Authentication:Facebook:AppId` and `Authentication:Facebook:AppSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="efceb-174">Il sistema di configurazione è configurato per la lettura delle chiavi dalle variabili di ambiente.</span><span class="sxs-lookup"><span data-stu-id="efceb-174">The configuration system is set up to read keys from environment variables.</span></span>
