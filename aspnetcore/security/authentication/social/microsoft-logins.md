---
title: Configurazione dell'account Microsoft per l'accesso esterno con ASP.NET Core
author: rick-anderson
description: In questo esempio viene illustrata l'integrazione dell'autenticazione utente dell'account Microsoft in un'app ASP.NET Core esistente.
ms.author: riande
ms.custom: mvc
ms.date: 03/19/2020
monikerRange: '>= aspnetcore-3.0'
uid: security/authentication/microsoft-logins
ms.openlocfilehash: 12c86456dad86731b86487a3a4dd725f36677002
ms.sourcegitcommit: f29a12486313e38e0163a643d8a97c8cecc7e871
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81384051"
---
# <a name="microsoft-account-external-login-setup-with-aspnet-core"></a><span data-ttu-id="e48ef-103">Configurazione dell'account Microsoft per l'accesso esterno con ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e48ef-103">Microsoft Account external login setup with ASP.NET Core</span></span>

<span data-ttu-id="e48ef-104">Da [Valeriy Novytskyy](https://github.com/01binary) e [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="e48ef-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="e48ef-105">In questo esempio viene illustrato come consentire agli utenti di accedere con il proprio account Microsoft aziendale, dell'istituto di istruzione o personale utilizzando il progetto ASP.NET Core 3.0 creato nella [pagina precedente.](xref:security/authentication/social/index)</span><span class="sxs-lookup"><span data-stu-id="e48ef-105">This sample shows you how to enable users to sign in with their work, school, or personal Microsoft account using the ASP.NET Core 3.0 project created on the [previous page](xref:security/authentication/social/index).</span></span>

## <a name="create-the-app-in-microsoft-developer-portal"></a><span data-ttu-id="e48ef-106">Creare l'app nel portale per sviluppatori MicrosoftCreate the app in Microsoft Developer Portal</span><span class="sxs-lookup"><span data-stu-id="e48ef-106">Create the app in Microsoft Developer Portal</span></span>

* <span data-ttu-id="e48ef-107">Aggiungere il pacchetto [Microsoft.AspNetCore.Authentication.MicrosoftAccount](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.MicrosoftAccount/) NuGet al progetto.</span><span class="sxs-lookup"><span data-stu-id="e48ef-107">Add the [Microsoft.AspNetCore.Authentication.MicrosoftAccount](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.MicrosoftAccount/) NuGet package to the project.</span></span>
* <span data-ttu-id="e48ef-108">Passare alla pagina Portale di Azure - Registrazioni app e creare o accedere a un account Microsoft:Navigate to the [Azure portal - App registrations](https://go.microsoft.com/fwlink/?linkid=2083908) page and create or sign into a Microsoft account:</span><span class="sxs-lookup"><span data-stu-id="e48ef-108">Navigate to the [Azure portal - App registrations](https://go.microsoft.com/fwlink/?linkid=2083908) page and create or sign into a Microsoft account:</span></span>

<span data-ttu-id="e48ef-109">Se non si dispone di un account Microsoft, selezionare **Crea uno**.</span><span class="sxs-lookup"><span data-stu-id="e48ef-109">If you don't have a Microsoft account, select **Create one**.</span></span> <span data-ttu-id="e48ef-110">Dopo l'accesso, si viene reindirizzati alla pagina **Registrazioni app:**</span><span class="sxs-lookup"><span data-stu-id="e48ef-110">After signing in, you are redirected to the **App registrations** page:</span></span>

* <span data-ttu-id="e48ef-111">Seleziona **Nuova registrazione**</span><span class="sxs-lookup"><span data-stu-id="e48ef-111">Select **New registration**</span></span>
* <span data-ttu-id="e48ef-112">Immettere un **Nome**.</span><span class="sxs-lookup"><span data-stu-id="e48ef-112">Enter a **Name**.</span></span>
* <span data-ttu-id="e48ef-113">Selezionare un'opzione per **Tipi di account supportati**.</span><span class="sxs-lookup"><span data-stu-id="e48ef-113">Select an option for **Supported account types**.</span></span>  <!-- Accounts for any org work with MS domain accounts. Most folks probably want the last option, personal MS accounts. It took 24 hours after setting this up for the keys to work -->
* <span data-ttu-id="e48ef-114">In **URI di reindirizzamento** `/signin-microsoft` immettere l'URL di sviluppo con l'aggiunta.</span><span class="sxs-lookup"><span data-stu-id="e48ef-114">Under **Redirect URI**, enter your development URL with `/signin-microsoft` appended.</span></span> <span data-ttu-id="e48ef-115">Ad esempio: `https://localhost:5001/signin-microsoft`.</span><span class="sxs-lookup"><span data-stu-id="e48ef-115">For example, `https://localhost:5001/signin-microsoft`.</span></span> <span data-ttu-id="e48ef-116">Lo schema di autenticazione Microsoft configurato più `/signin-microsoft` avanti in questo esempio gestirà automaticamente le richieste in corrispondenza della route per implementare il flusso OAuth.</span><span class="sxs-lookup"><span data-stu-id="e48ef-116">The Microsoft authentication scheme configured later in this sample will automatically handle requests at `/signin-microsoft` route to implement the OAuth flow.</span></span>
* <span data-ttu-id="e48ef-117">Seleziona **Registrazione**</span><span class="sxs-lookup"><span data-stu-id="e48ef-117">Select **Register**</span></span>

### <a name="create-client-secret"></a><span data-ttu-id="e48ef-118">Creare un segreto clientCreate client secret</span><span class="sxs-lookup"><span data-stu-id="e48ef-118">Create client secret</span></span>

* <span data-ttu-id="e48ef-119">Nel riquadro sinistro selezionare **Certificati & segreti**.</span><span class="sxs-lookup"><span data-stu-id="e48ef-119">In the left pane, select **Certificates & secrets**.</span></span>
* <span data-ttu-id="e48ef-120">In **Segreti client**selezionare Nuovo **segreto client**</span><span class="sxs-lookup"><span data-stu-id="e48ef-120">Under **Client secrets**, select **New client secret**</span></span>

  * <span data-ttu-id="e48ef-121">Aggiungere una descrizione per il segreto client.</span><span class="sxs-lookup"><span data-stu-id="e48ef-121">Add a description for the client secret.</span></span>
  * <span data-ttu-id="e48ef-122">Selezionare il pulsante **Aggiungi.Select** the Add button.</span><span class="sxs-lookup"><span data-stu-id="e48ef-122">Select the **Add** button.</span></span>

* <span data-ttu-id="e48ef-123">In **Segreti client**copiare il valore del segreto client.</span><span class="sxs-lookup"><span data-stu-id="e48ef-123">Under **Client secrets**, copy the value of the client secret.</span></span>

<span data-ttu-id="e48ef-124">Il segmento URI viene impostato come callback predefinito del provider di autenticazione Microsoft.The URI segment `/signin-microsoft` is set as the default callback of the Microsoft authentication provider.</span><span class="sxs-lookup"><span data-stu-id="e48ef-124">The URI segment `/signin-microsoft` is set as the default callback of the Microsoft authentication provider.</span></span> <span data-ttu-id="e48ef-125">È possibile modificare l'URI di callback predefinito durante la configurazione del middleware di autenticazione Microsoft tramite la proprietà [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) ereditata della classe [MicrosoftAccountOptions.](/dotnet/api/microsoft.aspnetcore.authentication.microsoftaccount.microsoftaccountoptions)</span><span class="sxs-lookup"><span data-stu-id="e48ef-125">You can change the default callback URI while configuring the Microsoft authentication middleware via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.authentication.microsoftaccount.microsoftaccountoptions) class.</span></span>

## <a name="store-the-microsoft-client-id-and-secret"></a><span data-ttu-id="e48ef-126">Archiviare l'ID client Microsoft e il segreto</span><span class="sxs-lookup"><span data-stu-id="e48ef-126">Store the Microsoft client ID and secret</span></span>

<span data-ttu-id="e48ef-127">Archiviare impostazioni riservate, ad esempio l'ID client Microsoft e i valori segreti con [Secret Manager](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="e48ef-127">Store sensitive settings such as the Microsoft client ID and secret values with [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="e48ef-128">Per questo esempio, usare la procedura seguente:For this sample, use the following steps:</span><span class="sxs-lookup"><span data-stu-id="e48ef-128">For this sample, use the following steps:</span></span>

1. <span data-ttu-id="e48ef-129">Inizializzare il progetto per l'archiviazione segreta in base alle istruzioni in [Abilitare l'archiviazione segreta.](xref:security/app-secrets#enable-secret-storage)</span><span class="sxs-lookup"><span data-stu-id="e48ef-129">Initialize the project for secret storage per the instructions at [Enable secret storage](xref:security/app-secrets#enable-secret-storage).</span></span>
1. <span data-ttu-id="e48ef-130">Memorizzare le impostazioni sensibili nell'archivio `Authentication:Microsoft:ClientId` `Authentication:Microsoft:ClientSecret`segreto locale con le chiavi segrete e:</span><span class="sxs-lookup"><span data-stu-id="e48ef-130">Store the sensitive settings in the local secret store with the secret keys `Authentication:Microsoft:ClientId` and `Authentication:Microsoft:ClientSecret`:</span></span>

    ```dotnetcli
    dotnet user-secrets set "Authentication:Microsoft:ClientId" "<client-id>"
    dotnet user-secrets set "Authentication:Microsoft:ClientSecret" "<client-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="configure-microsoft-account-authentication"></a><span data-ttu-id="e48ef-131">Configurare l'autenticazione dell'account Microsoft</span><span class="sxs-lookup"><span data-stu-id="e48ef-131">Configure Microsoft Account Authentication</span></span>

<span data-ttu-id="e48ef-132">Aggiungere il servizio Account `Startup.ConfigureServices`Microsoft a :</span><span class="sxs-lookup"><span data-stu-id="e48ef-132">Add the Microsoft Account service to the `Startup.ConfigureServices`:</span></span>

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupMS3x.cs?name=snippet&highlight=10-14)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

<span data-ttu-id="e48ef-133">Per altre informazioni sulle opzioni di configurazione supportate dall'autenticazione dell'account Microsoft, vedere le informazioni di riferimento sull'API [MicrosoftAccountOptions.For](/dotnet/api/microsoft.aspnetcore.builder.microsoftaccountoptions) more information about configuration options supported by Microsoft Account authentication, see the MicrosoftAccountOptions API reference.</span><span class="sxs-lookup"><span data-stu-id="e48ef-133">For more information about configuration options supported by Microsoft Account authentication, see the [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.builder.microsoftaccountoptions) API reference.</span></span> <span data-ttu-id="e48ef-134">Può essere utilizzato per richiedere informazioni diverse sull'utente.</span><span class="sxs-lookup"><span data-stu-id="e48ef-134">This can be used to request different information about the user.</span></span>

## <a name="sign-in-with-microsoft-account"></a><span data-ttu-id="e48ef-135">Accedi con l'account Microsoft</span><span class="sxs-lookup"><span data-stu-id="e48ef-135">Sign in with Microsoft Account</span></span>

<span data-ttu-id="e48ef-136">Eseguire l'app e fare clic su **Accedi**.</span><span class="sxs-lookup"><span data-stu-id="e48ef-136">Run the app and click **Log in**.</span></span> <span data-ttu-id="e48ef-137">Viene visualizzata un'opzione per accedere con Microsoft.</span><span class="sxs-lookup"><span data-stu-id="e48ef-137">An option to sign in with Microsoft appears.</span></span> <span data-ttu-id="e48ef-138">Quando si fa clic su Microsoft, si viene reindirizzati a Microsoft per l'autenticazione.</span><span class="sxs-lookup"><span data-stu-id="e48ef-138">When you click on Microsoft, you are redirected to Microsoft for authentication.</span></span> <span data-ttu-id="e48ef-139">Dopo aver effettuato l'accesso con il tuo account Microsoft, ti verrà richiesto di consentire all'app di accedere alle tue informazioni:</span><span class="sxs-lookup"><span data-stu-id="e48ef-139">After signing in with your Microsoft Account, you will be prompted to let the app access your info:</span></span>

<span data-ttu-id="e48ef-140">Toccare **Sì** e si verrà reindirizzati al sito web dove è possibile impostare la posta elettronica.</span><span class="sxs-lookup"><span data-stu-id="e48ef-140">Tap **Yes** and you will be redirected back to the web site where you can set your email.</span></span>

<span data-ttu-id="e48ef-141">A questo punto si è connessi utilizzando le credenziali Microsoft:</span><span class="sxs-lookup"><span data-stu-id="e48ef-141">You are now logged in using your Microsoft credentials:</span></span>

[!INCLUDE[](includes/chain-auth-providers.md)]

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a><span data-ttu-id="e48ef-142">Risoluzione dei problemi</span><span class="sxs-lookup"><span data-stu-id="e48ef-142">Troubleshooting</span></span>

* <span data-ttu-id="e48ef-143">Se il provider dell'account Microsoft reindirizza l'utente a una pagina di errore `#` di accesso, prendere nota del titolo dell'errore e dei parametri della stringa di query della descrizione direttamente seguendo il (hashtag) nell'URI.</span><span class="sxs-lookup"><span data-stu-id="e48ef-143">If the Microsoft Account provider redirects you to a sign in error page, note the error title and description query string parameters directly following the `#` (hashtag) in the Uri.</span></span>

  <span data-ttu-id="e48ef-144">Anche se il messaggio di errore sembra indicare un problema con l'autenticazione Microsoft, la causa più comune è l'URI dell'applicazione non corrisponde a nessuno degli URI di **reindirizzamento** specificati per la piattaforma **Web.**</span><span class="sxs-lookup"><span data-stu-id="e48ef-144">Although the error message seems to indicate a problem with Microsoft authentication, the most common cause is your application Uri not matching any of the **Redirect URIs** specified for the **Web** platform.</span></span>
* <span data-ttu-id="e48ef-145">Se Identity non è `services.AddIdentity` configurato `ConfigureServices`chiamando in , il tentativo di autenticazione comporterà *ArgumentException: deve essere fornita l'opzione 'SignInScheme'.*</span><span class="sxs-lookup"><span data-stu-id="e48ef-145">If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate will result in *ArgumentException: The 'SignInScheme' option must be provided*.</span></span> <span data-ttu-id="e48ef-146">Il modello di progetto utilizzato in questo esempio garantisce che questa operazione venga eseguita.</span><span class="sxs-lookup"><span data-stu-id="e48ef-146">The project template used in this sample ensures that this is done.</span></span>
* <span data-ttu-id="e48ef-147">Se il database del sito non è stato creato applicando la migrazione iniziale, si otterrà *un'operazione del database non riuscita durante l'elaborazione dell'errore* di richiesta.</span><span class="sxs-lookup"><span data-stu-id="e48ef-147">If the site database has not been created by applying the initial migration, you will get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="e48ef-148">Toccare **Applica migrazioni** per creare il database e aggiornare per continuare oltre l'errore.</span><span class="sxs-lookup"><span data-stu-id="e48ef-148">Tap **Apply Migrations** to create the database and refresh to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="e48ef-149">Passaggi successivi</span><span class="sxs-lookup"><span data-stu-id="e48ef-149">Next steps</span></span>

* <span data-ttu-id="e48ef-150">In questo articolo è stato illustrato come è possibile eseguire l'autenticazione con Microsoft.This article showed how you can authenticate with Microsoft.</span><span class="sxs-lookup"><span data-stu-id="e48ef-150">This article showed how you can authenticate with Microsoft.</span></span> <span data-ttu-id="e48ef-151">È possibile seguire un approccio simile per l'autenticazione con altri provider elencati nella [pagina precedente.](xref:security/authentication/social/index)</span><span class="sxs-lookup"><span data-stu-id="e48ef-151">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>

* <span data-ttu-id="e48ef-152">Dopo aver pubblicato il sito Web nell'app Web di Azure, creare nuovi segreti client nel portale per sviluppatori Microsoft.Once you publish your web site to Azure web app, create a new client secrets in the Microsoft Developer Portal.</span><span class="sxs-lookup"><span data-stu-id="e48ef-152">Once you publish your web site to Azure web app, create a new client secrets in the Microsoft Developer Portal.</span></span>

* <span data-ttu-id="e48ef-153">Impostare `Authentication:Microsoft:ClientId` `Authentication:Microsoft:ClientSecret` le impostazioni e come dell'applicazione nel portale di Azure.Set the and as application settings in the Azure portal.</span><span class="sxs-lookup"><span data-stu-id="e48ef-153">Set the `Authentication:Microsoft:ClientId` and `Authentication:Microsoft:ClientSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="e48ef-154">Il sistema di configurazione è impostato per leggere le chiavi dalle variabili di ambiente.</span><span class="sxs-lookup"><span data-stu-id="e48ef-154">The configuration system is set up to read keys from environment variables.</span></span>
