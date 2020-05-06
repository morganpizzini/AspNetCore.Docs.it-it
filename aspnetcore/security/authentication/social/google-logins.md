---
title: Installazione di Google External login in ASP.NET Core
author: rick-anderson
description: Questa esercitazione illustra l'integrazione dell'autenticazione utente dell'account Google in un'app ASP.NET Core esistente.
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 03/19/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/google-logins
ms.openlocfilehash: 06a5cf77e47d9fc618fddf3a096001a4f31688be
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776994"
---
# <a name="google-external-login-setup-in-aspnet-core"></a><span data-ttu-id="d7f5d-103">Installazione di Google External login in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d7f5d-103">Google external login setup in ASP.NET Core</span></span>

<span data-ttu-id="d7f5d-104">Da [Valeriy Novytskyy](https://github.com/01binary) e [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="d7f5d-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="d7f5d-105">Questa esercitazione illustra come consentire agli utenti di accedere con il proprio account Google usando il progetto ASP.NET Core 3,0 creato nella [pagina precedente](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="d7f5d-105">This tutorial shows you how to enable users to sign in with their Google account using the ASP.NET Core 3.0 project created on the [previous page](xref:security/authentication/social/index).</span></span>

## <a name="create-a-google-api-console-project-and-client-id"></a><span data-ttu-id="d7f5d-106">Creare un progetto console e un ID client di Google API</span><span class="sxs-lookup"><span data-stu-id="d7f5d-106">Create a Google API Console project and client ID</span></span>

* <span data-ttu-id="d7f5d-107">Installare [Microsoft. AspNetCore. Authentication. Google](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Google).</span><span class="sxs-lookup"><span data-stu-id="d7f5d-107">Install [Microsoft.AspNetCore.Authentication.Google](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Google).</span></span>
* <span data-ttu-id="d7f5d-108">Passare all'articolo relativo all' [integrazione di Google Sign-in nell'app Web](https://developers.google.com/identity/sign-in/web/devconsole-project) e selezionare **configura un progetto**.</span><span class="sxs-lookup"><span data-stu-id="d7f5d-108">Navigate to [Integrating Google Sign-In into your web app](https://developers.google.com/identity/sign-in/web/devconsole-project) and select **CONFIGURE A PROJECT**.</span></span>
* <span data-ttu-id="d7f5d-109">Nella finestra di dialogo **Configura client OAuth** selezionare **server Web**.</span><span class="sxs-lookup"><span data-stu-id="d7f5d-109">In the **Configure your OAuth client** dialog, select **Web server**.</span></span>
* <span data-ttu-id="d7f5d-110">Nella casella voce di testo **URI di reindirizzamento autorizzato** impostare l'URI di reindirizzamento.</span><span class="sxs-lookup"><span data-stu-id="d7f5d-110">In the **Authorized redirect URIs** text entry box, set the redirect URI.</span></span> <span data-ttu-id="d7f5d-111">Ad esempio, usare `https://localhost:44312/signin-google`</span><span class="sxs-lookup"><span data-stu-id="d7f5d-111">For example, `https://localhost:44312/signin-google`</span></span>
* <span data-ttu-id="d7f5d-112">Salvare l' **ID client** e il **segreto client**.</span><span class="sxs-lookup"><span data-stu-id="d7f5d-112">Save the **Client ID** and **Client Secret**.</span></span>
* <span data-ttu-id="d7f5d-113">Quando si distribuisce il sito, registrare il nuovo URL pubblico dalla **console di Google**.</span><span class="sxs-lookup"><span data-stu-id="d7f5d-113">When deploying the site, register the new public url from the **Google Console**.</span></span>

## <a name="store-the-google-client-id-and-secret"></a><span data-ttu-id="d7f5d-114">Archiviare l'ID e il segreto del client Google</span><span class="sxs-lookup"><span data-stu-id="d7f5d-114">Store the Google client ID and secret</span></span>

<span data-ttu-id="d7f5d-115">Archiviare le impostazioni riservate, ad esempio i valori di ID client e segreto di Google con [gestione Secret](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="d7f5d-115">Store sensitive settings such as the Google client ID and secret values with [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="d7f5d-116">Per questo esempio, attenersi alla procedura seguente:</span><span class="sxs-lookup"><span data-stu-id="d7f5d-116">For this sample, use the following steps:</span></span>

1. <span data-ttu-id="d7f5d-117">Inizializzare il progetto per l'archiviazione segreta in base alle istruzioni riportate in [abilitare l'archiviazione segreta](xref:security/app-secrets#enable-secret-storage).</span><span class="sxs-lookup"><span data-stu-id="d7f5d-117">Initialize the project for secret storage per the instructions at [Enable secret storage](xref:security/app-secrets#enable-secret-storage).</span></span>
1. <span data-ttu-id="d7f5d-118">Archiviare le impostazioni sensibili nell'archivio dei segreti locali con le chiavi `Authentication:Google:ClientId` segrete `Authentication:Google:ClientSecret`e:</span><span class="sxs-lookup"><span data-stu-id="d7f5d-118">Store the sensitive settings in the local secret store with the secret keys `Authentication:Google:ClientId` and `Authentication:Google:ClientSecret`:</span></span>

    ```dotnetcli
    dotnet user-secrets set "Authentication:Google:ClientId" "<client-id>"
    dotnet user-secrets set "Authentication:Google:ClientSecret" "<client-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="d7f5d-119">È possibile gestire le credenziali e l'utilizzo dell'API nella [console API](https://console.developers.google.com/apis/dashboard).</span><span class="sxs-lookup"><span data-stu-id="d7f5d-119">You can manage your API credentials and usage in the [API Console](https://console.developers.google.com/apis/dashboard).</span></span>

## <a name="configure-google-authentication"></a><span data-ttu-id="d7f5d-120">Configurare l'autenticazione di Google</span><span class="sxs-lookup"><span data-stu-id="d7f5d-120">Configure Google authentication</span></span>

<span data-ttu-id="d7f5d-121">Aggiungere il servizio Google a `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="d7f5d-121">Add the Google service to `Startup.ConfigureServices`:</span></span>

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupGoogle3x.cs?highlight=11-19)]

[!INCLUDE [default settings configuration](includes/default-settings2-2.md)]

## <a name="sign-in-with-google"></a><span data-ttu-id="d7f5d-122">Accedere con Google</span><span class="sxs-lookup"><span data-stu-id="d7f5d-122">Sign in with Google</span></span>

* <span data-ttu-id="d7f5d-123">Eseguire l'app e fare clic su **Accedi**.</span><span class="sxs-lookup"><span data-stu-id="d7f5d-123">Run the app and click **Log in**.</span></span> <span data-ttu-id="d7f5d-124">Viene visualizzata un'opzione per accedere con Google.</span><span class="sxs-lookup"><span data-stu-id="d7f5d-124">An option to sign in with Google appears.</span></span>
* <span data-ttu-id="d7f5d-125">Fare clic sul pulsante **Google** , che reindirizza a Google per l'autenticazione.</span><span class="sxs-lookup"><span data-stu-id="d7f5d-125">Click the **Google** button, which redirects to Google for authentication.</span></span>
* <span data-ttu-id="d7f5d-126">Dopo aver immesso le credenziali Google, viene reindirizzato di nuovo al sito Web.</span><span class="sxs-lookup"><span data-stu-id="d7f5d-126">After entering your Google credentials, you are redirected back to the web site.</span></span>

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

<span data-ttu-id="d7f5d-127">Per altre <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> informazioni sulle opzioni di configurazione supportate da Google Authentication, vedere le informazioni di riferimento sulle API.</span><span class="sxs-lookup"><span data-stu-id="d7f5d-127">See the <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> API reference for more information on configuration options supported by Google authentication.</span></span> <span data-ttu-id="d7f5d-128">Questa operazione può essere utilizzata per richiedere informazioni diverse sull'utente.</span><span class="sxs-lookup"><span data-stu-id="d7f5d-128">This can be used to request different information about the user.</span></span>

## <a name="change-the-default-callback-uri"></a><span data-ttu-id="d7f5d-129">Modificare l'URI di callback predefinito</span><span class="sxs-lookup"><span data-stu-id="d7f5d-129">Change the default callback URI</span></span>

<span data-ttu-id="d7f5d-130">Il segmento `/signin-google` URI viene impostato come callback predefinito del provider di autenticazione Google.</span><span class="sxs-lookup"><span data-stu-id="d7f5d-130">The URI segment `/signin-google` is set as the default callback of the Google authentication provider.</span></span> <span data-ttu-id="d7f5d-131">È possibile modificare l'URI di callback predefinito durante la configurazione del middleware di autenticazione di Google tramite la proprietà [RemoteAuthenticationOptions. CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) ereditata della classe [GoogleOptions](/dotnet/api/microsoft.aspnetcore.authentication.google.googleoptions) .</span><span class="sxs-lookup"><span data-stu-id="d7f5d-131">You can change the default callback URI while configuring the Google authentication middleware via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [GoogleOptions](/dotnet/api/microsoft.aspnetcore.authentication.google.googleoptions) class.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="d7f5d-132">Risoluzione dei problemi</span><span class="sxs-lookup"><span data-stu-id="d7f5d-132">Troubleshooting</span></span>

* <span data-ttu-id="d7f5d-133">Se l'accesso non funziona e non si ricevono errori, passare alla modalità di sviluppo per semplificare il debug del problema.</span><span class="sxs-lookup"><span data-stu-id="d7f5d-133">If the sign-in doesn't work and you aren't getting any errors, switch to development mode to make the issue easier to debug.</span></span>
* <span data-ttu-id="d7f5d-134">Se Identity non è configurato chiamando `services.AddIdentity` in `ConfigureServices`, il tentativo di autenticare i risultati in *ArgumentException: è necessario specificare l'opzione ' SignInScheme '*.</span><span class="sxs-lookup"><span data-stu-id="d7f5d-134">If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate results in *ArgumentException: The 'SignInScheme' option must be provided*.</span></span> <span data-ttu-id="d7f5d-135">Il modello di progetto usato in questa esercitazione garantisce che questa operazione venga eseguita.</span><span class="sxs-lookup"><span data-stu-id="d7f5d-135">The project template used in this tutorial ensures that this is done.</span></span>
* <span data-ttu-id="d7f5d-136">Se il database del sito non è stato creato applicando la migrazione iniziale, si ottiene *un'operazione di database non riuscita durante l'elaborazione dell'* errore di richiesta.</span><span class="sxs-lookup"><span data-stu-id="d7f5d-136">If the site database has not been created by applying the initial migration, you get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="d7f5d-137">Selezionare **applica migrazioni** per creare il database e aggiornare la pagina per continuare a superare l'errore.</span><span class="sxs-lookup"><span data-stu-id="d7f5d-137">Select **Apply Migrations** to create the database, and refresh the page to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="d7f5d-138">Passaggi successivi</span><span class="sxs-lookup"><span data-stu-id="d7f5d-138">Next steps</span></span>

* <span data-ttu-id="d7f5d-139">Questo articolo ha illustrato come è possibile eseguire l'autenticazione con Google.</span><span class="sxs-lookup"><span data-stu-id="d7f5d-139">This article showed how you can authenticate with Google.</span></span> <span data-ttu-id="d7f5d-140">È possibile seguire un approccio simile per l'autenticazione con altri provider elencati nella [pagina precedente](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="d7f5d-140">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>
* <span data-ttu-id="d7f5d-141">Dopo aver pubblicato l'app in Azure, reimpostare `ClientSecret` la nella console dell'API Google.</span><span class="sxs-lookup"><span data-stu-id="d7f5d-141">Once you publish the app to Azure, reset the `ClientSecret` in the Google API Console.</span></span>
* <span data-ttu-id="d7f5d-142">Impostare `Authentication:Google:ClientId` e `Authentication:Google:ClientSecret` come impostazioni dell'applicazione nella portale di Azure.</span><span class="sxs-lookup"><span data-stu-id="d7f5d-142">Set the `Authentication:Google:ClientId` and `Authentication:Google:ClientSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="d7f5d-143">Il sistema di configurazione è configurato per la lettura delle chiavi dalle variabili di ambiente.</span><span class="sxs-lookup"><span data-stu-id="d7f5d-143">The configuration system is set up to read keys from environment variables.</span></span>
