---
title: Configurazione dell'accesso esterno di Facebook in ASP.NET Core
author: rick-anderson
description: Esercitazione con esempi di codice che illustrano l'integrazione dell'autenticazione utente dell'account Facebook in un'app ASP.NET Core esistente.
ms.author: riande
ms.custom: seoapril2019, mvc, seodec18
ms.date: 03/19/2020
monikerRange: '>= aspnetcore-3.0'
uid: security/authentication/facebook-logins
ms.openlocfilehash: 9b3128addafb41ad6ec44af5cb12e89607e1ae59
ms.sourcegitcommit: 5af16166977da598953f82da3ed3b7712d38f6cb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81277301"
---
# <a name="facebook-external-login-setup-in-aspnet-core"></a><span data-ttu-id="54bf0-103">Configurazione dell'accesso esterno di Facebook in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="54bf0-103">Facebook external login setup in ASP.NET Core</span></span>

<span data-ttu-id="54bf0-104">Da [Valeriy Novytskyy](https://github.com/01binary) e [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="54bf0-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<!-- per @rick-anderson and scott addie, don't update images. Remove images and point the customer to the FB set up page. FB needs to maintain  instructions to get key and secret.
-->

<span data-ttu-id="54bf0-105">Questa esercitazione con esempi di codice mostra come abilitare gli utenti ad accedere con il proprio account Facebook usando un progetto ASP.NET Core 3.0 di esempio creato nella [pagina precedente.](xref:security/authentication/social/index)</span><span class="sxs-lookup"><span data-stu-id="54bf0-105">This tutorial with code examples shows how to enable your users to sign in with their Facebook account using a sample ASP.NET Core 3.0 project created on the [previous page](xref:security/authentication/social/index).</span></span> <span data-ttu-id="54bf0-106">Iniziamo creando un ID app di Facebook seguendo i [passaggi ufficiali](https://developers.facebook.com).</span><span class="sxs-lookup"><span data-stu-id="54bf0-106">We start by creating a Facebook App ID by following the [official steps](https://developers.facebook.com).</span></span>

## <a name="create-the-app-in-facebook"></a><span data-ttu-id="54bf0-107">Creare l'app in Facebook</span><span class="sxs-lookup"><span data-stu-id="54bf0-107">Create the app in Facebook</span></span>

* <span data-ttu-id="54bf0-108">Aggiungere il pacchetto [Microsoft.AspNetCore.Authentication.Facebook](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Facebook) NuGet al progetto.</span><span class="sxs-lookup"><span data-stu-id="54bf0-108">Add the [Microsoft.AspNetCore.Authentication.Facebook](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Facebook) NuGet package to the project.</span></span>

* <span data-ttu-id="54bf0-109">Accedi alla pagina [dell'app Facebook Developers](https://developers.facebook.com/apps/) e accedi.</span><span class="sxs-lookup"><span data-stu-id="54bf0-109">Navigate to the [Facebook Developers app](https://developers.facebook.com/apps/) page and sign in.</span></span> <span data-ttu-id="54bf0-110">Se non hai già un account Facebook, utilizza il link **Iscriviti a Facebook** nella pagina di accesso per crearne uno.</span><span class="sxs-lookup"><span data-stu-id="54bf0-110">If you don't already have a Facebook account, use the **Sign up for Facebook** link on the login page to create one.</span></span>  <span data-ttu-id="54bf0-111">Una volta che hai un account Facebook, segui le istruzioni per registrarti come sviluppatore di Facebook.</span><span class="sxs-lookup"><span data-stu-id="54bf0-111">Once you have a Facebook account, follow the instructions to register as a Facebook Developer.</span></span>

* <span data-ttu-id="54bf0-112">Dal menu **App personali** selezionare **Crea app** per creare un nuovo ID app.</span><span class="sxs-lookup"><span data-stu-id="54bf0-112">From the **My Apps** menu select **Create App** to create a new App ID.</span></span>

   ![Portale Facebook per sviluppatori aperto in Microsoft Edge](index/_static/FBMyApps.png)

* <span data-ttu-id="54bf0-114">Compila il modulo e tocca il pulsante **Crea ID app.**</span><span class="sxs-lookup"><span data-stu-id="54bf0-114">Fill out the form and tap the **Create App ID** button.</span></span>

  ![Creare un modulo Nuovo ID app](index/_static/FBNewAppId.png)

* <span data-ttu-id="54bf0-116">Nella nuova scheda App selezionare **Aggiungi un prodotto**.</span><span class="sxs-lookup"><span data-stu-id="54bf0-116">On the new App card, select **Add a Product**.</span></span>  <span data-ttu-id="54bf0-117">Nella scheda di accesso di **Facebook,** fai clic su **Configura**</span><span class="sxs-lookup"><span data-stu-id="54bf0-117">On the **Facebook Login** card, click **Set Up**</span></span> 

  ![Pagina di configurazione del prodotto](index/_static/FBProductSetup.png)

* <span data-ttu-id="54bf0-119">Verrà avviata la **procedura guidata Guida introduttiva** con **Scegli una piattaforma** come prima pagina.</span><span class="sxs-lookup"><span data-stu-id="54bf0-119">The **Quickstart** wizard launches with **Choose a Platform** as the first page.</span></span> <span data-ttu-id="54bf0-120">Ignorare la procedura guidata per il momento facendo clic sul collegamento **Impostazioni** di accesso **FaceBook** nel menu in basso a sinistra:</span><span class="sxs-lookup"><span data-stu-id="54bf0-120">Bypass the wizard for now by clicking the **FaceBook Login** **Settings** link in the menu on the lower left:</span></span>

  ![Salta la Guida introduttiva](index/_static/FBSkipQuickStart.png)

* <span data-ttu-id="54bf0-122">Viene visualizzata la pagina **Impostazioni OAuth client:**</span><span class="sxs-lookup"><span data-stu-id="54bf0-122">You are presented with the **Client OAuth Settings** page:</span></span>

  ![Pagina Impostazioni OAuth client](index/_static/FBOAuthSetup.png)

* <span data-ttu-id="54bf0-124">Inserisci l'URI di sviluppo con */signin-facebook* aggiunto nel campo **URI di reindirizzamento OAuth valido** (ad esempio: `https://localhost:44320/signin-facebook`).</span><span class="sxs-lookup"><span data-stu-id="54bf0-124">Enter your development URI with */signin-facebook* appended into the **Valid OAuth Redirect URIs** field (for example: `https://localhost:44320/signin-facebook`).</span></span> <span data-ttu-id="54bf0-125">L'autenticazione di Facebook configurata più avanti in questa esercitazione gestirà automaticamente le richieste nella route */signin-facebook* per implementare il flusso OAuth.</span><span class="sxs-lookup"><span data-stu-id="54bf0-125">The Facebook authentication configured later in this tutorial will automatically handle requests at */signin-facebook* route to implement the OAuth flow.</span></span>

> [!NOTE]
> <span data-ttu-id="54bf0-126">L'URI */signin-facebook* viene impostato come callback predefinito del provider di autenticazione di Facebook.</span><span class="sxs-lookup"><span data-stu-id="54bf0-126">The URI */signin-facebook* is set as the default callback of the Facebook authentication provider.</span></span> <span data-ttu-id="54bf0-127">È possibile modificare l'URI di callback predefinito durante la configurazione del middleware di autenticazione di Facebook tramite la proprietà [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) ereditata della classe [FacebookOptions.](/dotnet/api/microsoft.aspnetcore.authentication.facebook.facebookoptions)</span><span class="sxs-lookup"><span data-stu-id="54bf0-127">You can change the default callback URI while configuring the Facebook authentication middleware via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [FacebookOptions](/dotnet/api/microsoft.aspnetcore.authentication.facebook.facebookoptions) class.</span></span>

* <span data-ttu-id="54bf0-128">Fare clic su **Save Changes**.</span><span class="sxs-lookup"><span data-stu-id="54bf0-128">Click **Save Changes**.</span></span>

* <span data-ttu-id="54bf0-129">Fare clic sul collegamento **Impostazioni** > **di base** nel riquadro di spostamento sinistro.</span><span class="sxs-lookup"><span data-stu-id="54bf0-129">Click **Settings** > **Basic** link in the left navigation.</span></span>

  <span data-ttu-id="54bf0-130">In questa pagina, prendere `App ID` nota `App Secret`del vostro e il vostro .</span><span class="sxs-lookup"><span data-stu-id="54bf0-130">On this page, make a note of your `App ID` and your `App Secret`.</span></span> <span data-ttu-id="54bf0-131">Entrambi verranno aggiunti all'applicazione ASP.NET Core nella sezione successiva:You will add both in your ASP.NET Core application in the next section:</span><span class="sxs-lookup"><span data-stu-id="54bf0-131">You will add both into your ASP.NET Core application in the next section:</span></span>

* <span data-ttu-id="54bf0-132">Quando distribuisci il sito devi rivedere la pagina di configurazione di **Facebook Login** e registrare un nuovo URI pubblico.</span><span class="sxs-lookup"><span data-stu-id="54bf0-132">When deploying the site you need to revisit the **Facebook Login** setup page and register a new public URI.</span></span>

## <a name="store-the-facebook-app-id-and-secret"></a><span data-ttu-id="54bf0-133">Memorizzare l'ID e il segreto dell'app Facebook</span><span class="sxs-lookup"><span data-stu-id="54bf0-133">Store the Facebook app ID and secret</span></span>

<span data-ttu-id="54bf0-134">Memorizzare le impostazioni sensibili come l'ID app di Facebook e valori segreti con [Secret Manager](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="54bf0-134">Store sensitive settings such as the Facebook app ID and secret values with [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="54bf0-135">Per questo esempio, usare la procedura seguente:For this sample, use the following steps:</span><span class="sxs-lookup"><span data-stu-id="54bf0-135">For this sample, use the following steps:</span></span>

1. <span data-ttu-id="54bf0-136">Inizializzare il progetto per l'archiviazione segreta in base alle istruzioni in [Abilitare l'archiviazione segreta.](xref:security/app-secrets#enable-secret-storage)</span><span class="sxs-lookup"><span data-stu-id="54bf0-136">Initialize the project for secret storage per the instructions at [Enable secret storage](xref:security/app-secrets#enable-secret-storage).</span></span>
1. <span data-ttu-id="54bf0-137">Memorizzare le impostazioni sensibili nell'archivio `Authentication:Facebook:AppId` `Authentication:Facebook:AppSecret`segreto locale con le chiavi segrete e:</span><span class="sxs-lookup"><span data-stu-id="54bf0-137">Store the sensitive settings in the local secret store with the secret keys `Authentication:Facebook:AppId` and `Authentication:Facebook:AppSecret`:</span></span>

    ```dotnetcli
    dotnet user-secrets set "Authentication:Facebook:AppId" "<app-id>"
    dotnet user-secrets set "Authentication:Facebook:AppSecret" "<app-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="configure-facebook-authentication"></a><span data-ttu-id="54bf0-138">Configurare l'autenticazione di Facebook</span><span class="sxs-lookup"><span data-stu-id="54bf0-138">Configure Facebook Authentication</span></span>

<span data-ttu-id="54bf0-139">Aggiungere il servizio `ConfigureServices` Facebook nel metodo nel file *Startup.cs:*</span><span class="sxs-lookup"><span data-stu-id="54bf0-139">Add the Facebook service in the `ConfigureServices` method in the *Startup.cs* file:</span></span>

```csharp
services.AddAuthentication().AddFacebook(facebookOptions =>
{
    facebookOptions.AppId = Configuration["Authentication:Facebook:AppId"];
    facebookOptions.AppSecret = Configuration["Authentication:Facebook:AppSecret"];
});
```

[!INCLUDE [default settings configuration](includes/default-settings.md)]

## <a name="sign-in-with-facebook"></a><span data-ttu-id="54bf0-140">Accedi con Facebook</span><span class="sxs-lookup"><span data-stu-id="54bf0-140">Sign in with Facebook</span></span>

* <span data-ttu-id="54bf0-141">Eseguire l'app e selezionare **Accedi**.</span><span class="sxs-lookup"><span data-stu-id="54bf0-141">Run the app and select **Log in**.</span></span> 
* <span data-ttu-id="54bf0-142">In **Usa un altro servizio per accedere.**</span><span class="sxs-lookup"><span data-stu-id="54bf0-142">Under **Use another service to log in.**, select Facebook.</span></span>
* <span data-ttu-id="54bf0-143">Si viene reindirizzati a **Facebook** per l'autenticazione.</span><span class="sxs-lookup"><span data-stu-id="54bf0-143">You are redirected to **Facebook** for authentication.</span></span>
* <span data-ttu-id="54bf0-144">Inserisci le tue credenziali di Facebook.</span><span class="sxs-lookup"><span data-stu-id="54bf0-144">Enter your Facebook credentials.</span></span>
* <span data-ttu-id="54bf0-145">Si viene reindirizzati al sito in cui è possibile impostare la posta elettronica.</span><span class="sxs-lookup"><span data-stu-id="54bf0-145">You are redirected back to your site where you can set your email.</span></span>

<span data-ttu-id="54bf0-146">Ora hai effettuato l'accesso utilizzando le tue credenziali di Facebook:</span><span class="sxs-lookup"><span data-stu-id="54bf0-146">You are now logged in using your Facebook credentials:</span></span>

<a name="react"></a>

## <a name="react-to-cancel-authorize-external-sign-in"></a><span data-ttu-id="54bf0-147">Reagire per annullare l'autorizzazione dell'accesso esterno</span><span class="sxs-lookup"><span data-stu-id="54bf0-147">React to cancel authorize external sign-in</span></span>

<span data-ttu-id="54bf0-148"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.AccessDeniedPath>può fornire un percorso di reindirizzamento all'agente utente quando l'utente non approva la richiesta di autorizzazione richiesta.</span><span class="sxs-lookup"><span data-stu-id="54bf0-148"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.AccessDeniedPath> can provide a redirect path to the user agent when the user doesn't approve the requested authorization demand.</span></span>

<span data-ttu-id="54bf0-149">Il codice seguente `AccessDeniedPath` `"/AccessDeniedPathInfo"`imposta l'opzione su :</span><span class="sxs-lookup"><span data-stu-id="54bf0-149">The following code sets the `AccessDeniedPath` to `"/AccessDeniedPathInfo"`:</span></span>

[!code-csharp[](~/security/authentication/social/social-code/StartupAccessDeniedPath.cs?name=snippetFB)]

<span data-ttu-id="54bf0-150">Si consiglia `AccessDeniedPath` di utilizzare la pagina con le seguenti informazioni:</span><span class="sxs-lookup"><span data-stu-id="54bf0-150">We recommend the `AccessDeniedPath` page contain the following information:</span></span>

*  <span data-ttu-id="54bf0-151">L'autenticazione remota è stata annullata.</span><span class="sxs-lookup"><span data-stu-id="54bf0-151">Remote authentication was canceled.</span></span>
* <span data-ttu-id="54bf0-152">Questa applicazione richiede l'autenticazione.</span><span class="sxs-lookup"><span data-stu-id="54bf0-152">This app requires authentication.</span></span>
* <span data-ttu-id="54bf0-153">Per riprovare ad accedere, selezionare il collegamento Accesso.</span><span class="sxs-lookup"><span data-stu-id="54bf0-153">To try sign-in again, select the Login link.</span></span>

### <a name="test-accessdeniedpath"></a><span data-ttu-id="54bf0-154">Test AccessDeniedPath</span><span class="sxs-lookup"><span data-stu-id="54bf0-154">Test AccessDeniedPath</span></span>

* <span data-ttu-id="54bf0-155">Passare a [facebook.com](https://www.facebook.com/)</span><span class="sxs-lookup"><span data-stu-id="54bf0-155">Navigate to [facebook.com](https://www.facebook.com/)</span></span>
* <span data-ttu-id="54bf0-156">Se hai effettuato l'accesso, devi disconnetterti.</span><span class="sxs-lookup"><span data-stu-id="54bf0-156">If you are signed in, you must sign out.</span></span>
* <span data-ttu-id="54bf0-157">Esegui l'app e seleziona Accesso a Facebook.</span><span class="sxs-lookup"><span data-stu-id="54bf0-157">Run the app and select Facebook sign-in.</span></span>
* <span data-ttu-id="54bf0-158">Selezionare **Non ora**.</span><span class="sxs-lookup"><span data-stu-id="54bf0-158">Select **Not now**.</span></span> <span data-ttu-id="54bf0-159">Si viene reindirizzati `AccessDeniedPath` alla pagina specificata.</span><span class="sxs-lookup"><span data-stu-id="54bf0-159">You are redirected to the specified `AccessDeniedPath` page.</span></span>

<!-- End of React  -->
[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

<span data-ttu-id="54bf0-160">Vedere le informazioni di riferimento sull'API FacebookOptions per ulteriori informazioni sulle opzioni di configurazione supportate dall'autenticazione di Facebook.See the [FacebookOptions](/dotnet/api/microsoft.aspnetcore.builder.facebookoptions) API reference for more information on configuration options supported by Facebook authentication.</span><span class="sxs-lookup"><span data-stu-id="54bf0-160">See the [FacebookOptions](/dotnet/api/microsoft.aspnetcore.builder.facebookoptions) API reference for more information on configuration options supported by Facebook authentication.</span></span> <span data-ttu-id="54bf0-161">Le opzioni di configurazione possono essere utilizzate per:</span><span class="sxs-lookup"><span data-stu-id="54bf0-161">Configuration options can be used to:</span></span>

* <span data-ttu-id="54bf0-162">Richiedere informazioni diverse sull'utente.</span><span class="sxs-lookup"><span data-stu-id="54bf0-162">Request different information about the user.</span></span>
* <span data-ttu-id="54bf0-163">Aggiungere argomenti della stringa di query per personalizzare l'esperienza di accesso.</span><span class="sxs-lookup"><span data-stu-id="54bf0-163">Add query string arguments to customize the login experience.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="54bf0-164">Risoluzione dei problemi</span><span class="sxs-lookup"><span data-stu-id="54bf0-164">Troubleshooting</span></span>

* <span data-ttu-id="54bf0-165">**ASP.NET Solo Core 2.x:** Se Identity non è `services.AddIdentity` configurato `ConfigureServices`chiamando in , il tentativo di autenticazione comporterà *ArgumentException: deve essere fornita l'opzione 'SignInScheme'.*</span><span class="sxs-lookup"><span data-stu-id="54bf0-165">**ASP.NET Core 2.x only:** If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate will result in *ArgumentException: The 'SignInScheme' option must be provided*.</span></span> <span data-ttu-id="54bf0-166">Il modello di progetto usato in questa esercitazione garantisce che questa operazione venga eseguita.</span><span class="sxs-lookup"><span data-stu-id="54bf0-166">The project template used in this tutorial ensures that this is done.</span></span>
* <span data-ttu-id="54bf0-167">Se il database del sito non è stato creato applicando la migrazione iniziale, si ottiene *un'operazione del database non riuscita durante l'elaborazione dell'errore* di richiesta.</span><span class="sxs-lookup"><span data-stu-id="54bf0-167">If the site database has not been created by applying the initial migration, you get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="54bf0-168">Toccare **Applica migrazioni** per creare il database e aggiornare per continuare oltre l'errore.</span><span class="sxs-lookup"><span data-stu-id="54bf0-168">Tap **Apply Migrations** to create the database and refresh to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="54bf0-169">Passaggi successivi</span><span class="sxs-lookup"><span data-stu-id="54bf0-169">Next steps</span></span>

* <span data-ttu-id="54bf0-170">Questo articolo ha mostrato come è possibile eseguire l'autenticazione con Facebook.</span><span class="sxs-lookup"><span data-stu-id="54bf0-170">This article showed how you can authenticate with Facebook.</span></span> <span data-ttu-id="54bf0-171">È possibile seguire un approccio simile per l'autenticazione con altri provider elencati nella [pagina precedente.](xref:security/authentication/social/index)</span><span class="sxs-lookup"><span data-stu-id="54bf0-171">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>

* <span data-ttu-id="54bf0-172">Dopo aver pubblicato il sito Web nell'app `AppSecret` Web di Azure, è necessario reimpostare il sito nel portale per sviluppatori di Facebook.</span><span class="sxs-lookup"><span data-stu-id="54bf0-172">Once you publish your web site to Azure web app, you should reset the `AppSecret` in the Facebook developer portal.</span></span>

* <span data-ttu-id="54bf0-173">Impostare `Authentication:Facebook:AppId` `Authentication:Facebook:AppSecret` le impostazioni e come dell'applicazione nel portale di Azure.Set the and as application settings in the Azure portal.</span><span class="sxs-lookup"><span data-stu-id="54bf0-173">Set the `Authentication:Facebook:AppId` and `Authentication:Facebook:AppSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="54bf0-174">Il sistema di configurazione è impostato per leggere le chiavi dalle variabili di ambiente.</span><span class="sxs-lookup"><span data-stu-id="54bf0-174">The configuration system is set up to read keys from environment variables.</span></span>
