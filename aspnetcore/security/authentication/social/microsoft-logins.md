---
title: Configurazione dell'accesso esterno all'account Microsoft con ASP.NET Core
author: rick-anderson
description: In questo esempio viene illustrata l'integrazione di account Microsoft autenticazione utente in un'app ASP.NET Core esistente.
ms.author: riande
ms.custom: mvc
ms.date: 03/19/2020
monikerRange: '>= aspnetcore-3.0'
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
uid: security/authentication/microsoft-logins
ms.openlocfilehash: 36341a0e439be57d7da4f787aa6103b92c624e96
ms.sourcegitcommit: 62cc131969b2379f7a45c286a751e22d961dfbdb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90847585"
---
# <a name="microsoft-account-external-login-setup-with-aspnet-core"></a><span data-ttu-id="2e42c-103">Configurazione dell'accesso esterno all'account Microsoft con ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2e42c-103">Microsoft Account external login setup with ASP.NET Core</span></span>

<span data-ttu-id="2e42c-104">Da [Valeriy Novytskyy](https://github.com/01binary) e [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="2e42c-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="2e42c-105">Questo esempio illustra come consentire agli utenti di accedere con il proprio lavoro, Istituto di istruzione o personale account Microsoft usando il progetto ASP.NET Core 3,0 creato nella [pagina precedente](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="2e42c-105">This sample shows you how to enable users to sign in with their work, school, or personal Microsoft account using the ASP.NET Core 3.0 project created on the [previous page](xref:security/authentication/social/index).</span></span>

## <a name="create-the-app-in-microsoft-developer-portal"></a><span data-ttu-id="2e42c-106">Creare l'app nel portale per sviluppatori Microsoft</span><span class="sxs-lookup"><span data-stu-id="2e42c-106">Create the app in Microsoft Developer Portal</span></span>

* <span data-ttu-id="2e42c-107">Aggiungere il pacchetto NuGet [Microsoft. AspNetCore. Authentication. MicrosoftAccount](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.MicrosoftAccount/) al progetto.</span><span class="sxs-lookup"><span data-stu-id="2e42c-107">Add the [Microsoft.AspNetCore.Authentication.MicrosoftAccount](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.MicrosoftAccount/) NuGet package to the project.</span></span>
* <span data-ttu-id="2e42c-108">Passare alla pagina [portale di Azure-registrazioni app](https://go.microsoft.com/fwlink/?linkid=2083908) e creare o accedere a un account Microsoft:</span><span class="sxs-lookup"><span data-stu-id="2e42c-108">Navigate to the [Azure portal - App registrations](https://go.microsoft.com/fwlink/?linkid=2083908) page and create or sign into a Microsoft account:</span></span>

<span data-ttu-id="2e42c-109">Se non si dispone di un account Microsoft, selezionare **crearne uno**.</span><span class="sxs-lookup"><span data-stu-id="2e42c-109">If you don't have a Microsoft account, select **Create one**.</span></span> <span data-ttu-id="2e42c-110">Dopo aver eseguito l'accesso, si verrà reindirizzati alla pagina **registrazioni app** :</span><span class="sxs-lookup"><span data-stu-id="2e42c-110">After signing in, you are redirected to the **App registrations** page:</span></span>

* <span data-ttu-id="2e42c-111">Seleziona **nuova registrazione**</span><span class="sxs-lookup"><span data-stu-id="2e42c-111">Select **New registration**</span></span>
* <span data-ttu-id="2e42c-112">Immettere un **Nome**.</span><span class="sxs-lookup"><span data-stu-id="2e42c-112">Enter a **Name**.</span></span>
* <span data-ttu-id="2e42c-113">Selezionare un'opzione per i **tipi di account supportati**.</span><span class="sxs-lookup"><span data-stu-id="2e42c-113">Select an option for **Supported account types**.</span></span>  <!-- Accounts for any org work with MS domain accounts. Most folks probably want the last option, personal MS accounts. It took 24 hours after setting this up for the keys to work -->
  * <span data-ttu-id="2e42c-114">Il `MicrosoftAccount` pacchetto supporta le registrazioni di app create usando le opzioni "account in qualsiasi directory organizzativa" o "account in qualsiasi directory organizzativa e account Microsoft" per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="2e42c-114">The `MicrosoftAccount` package supports App Registrations created using "Accounts in any organizational directory" or "Accounts in any organizational directory and Microsoft accounts" options by default.</span></span>
  * <span data-ttu-id="2e42c-115">Per usare altre opzioni, impostare `AuthorizationEndpoint` e `TokenEndpoint` i membri di `MicrosoftAccountOptions` usati per inizializzare l'autenticazione dell'account Microsoft sugli URL visualizzati nella pagina **endpoint** della registrazione dell'app dopo che è stata creata (disponibile facendo clic su endpoint nella pagina **Panoramica** ).</span><span class="sxs-lookup"><span data-stu-id="2e42c-115">To use other options, set `AuthorizationEndpoint` and `TokenEndpoint` members of `MicrosoftAccountOptions` used to initialize the Microsoft Account authentication to the URLs displayed on **Endpoints** page of the App Registration after it is created (available by clicking Endpoints on the **Overview** page).</span></span>
* <span data-ttu-id="2e42c-116">In **URI di reindirizzamento**immettere l'URL di sviluppo con `/signin-microsoft` accodato.</span><span class="sxs-lookup"><span data-stu-id="2e42c-116">Under **Redirect URI**, enter your development URL with `/signin-microsoft` appended.</span></span> <span data-ttu-id="2e42c-117">Ad esempio: `https://localhost:5001/signin-microsoft`.</span><span class="sxs-lookup"><span data-stu-id="2e42c-117">For example, `https://localhost:5001/signin-microsoft`.</span></span> <span data-ttu-id="2e42c-118">Lo schema di autenticazione Microsoft configurato più avanti in questo esempio gestirà automaticamente le richieste in `/signin-microsoft` Route per implementare il flusso OAuth.</span><span class="sxs-lookup"><span data-stu-id="2e42c-118">The Microsoft authentication scheme configured later in this sample will automatically handle requests at `/signin-microsoft` route to implement the OAuth flow.</span></span>
* <span data-ttu-id="2e42c-119">Selezionare **Registra**</span><span class="sxs-lookup"><span data-stu-id="2e42c-119">Select **Register**</span></span>

### <a name="create-client-secret"></a><span data-ttu-id="2e42c-120">Crea segreto client</span><span class="sxs-lookup"><span data-stu-id="2e42c-120">Create client secret</span></span>

* <span data-ttu-id="2e42c-121">Nel riquadro sinistro selezionare **Certificati e segreti**.</span><span class="sxs-lookup"><span data-stu-id="2e42c-121">In the left pane, select **Certificates & secrets**.</span></span>
* <span data-ttu-id="2e42c-122">In **segreti client**selezionare **nuovo segreto client**</span><span class="sxs-lookup"><span data-stu-id="2e42c-122">Under **Client secrets**, select **New client secret**</span></span>

  * <span data-ttu-id="2e42c-123">Aggiungere una descrizione per il segreto client.</span><span class="sxs-lookup"><span data-stu-id="2e42c-123">Add a description for the client secret.</span></span>
  * <span data-ttu-id="2e42c-124">Fare clic sul pulsante **Aggiungi**.</span><span class="sxs-lookup"><span data-stu-id="2e42c-124">Select the **Add** button.</span></span>

* <span data-ttu-id="2e42c-125">In **segreti client**copiare il valore del segreto client.</span><span class="sxs-lookup"><span data-stu-id="2e42c-125">Under **Client secrets**, copy the value of the client secret.</span></span>

<span data-ttu-id="2e42c-126">Il segmento URI `/signin-microsoft` viene impostato come callback predefinito del provider di autenticazione Microsoft.</span><span class="sxs-lookup"><span data-stu-id="2e42c-126">The URI segment `/signin-microsoft` is set as the default callback of the Microsoft authentication provider.</span></span> <span data-ttu-id="2e42c-127">È possibile modificare l'URI di callback predefinito durante la configurazione del middleware di autenticazione Microsoft tramite la proprietà [RemoteAuthenticationOptions. CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) ereditata della classe [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.authentication.microsoftaccount.microsoftaccountoptions) .</span><span class="sxs-lookup"><span data-stu-id="2e42c-127">You can change the default callback URI while configuring the Microsoft authentication middleware via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.authentication.microsoftaccount.microsoftaccountoptions) class.</span></span>

## <a name="store-the-microsoft-client-id-and-secret"></a><span data-ttu-id="2e42c-128">Archiviare l'ID client e il segreto Microsoft</span><span class="sxs-lookup"><span data-stu-id="2e42c-128">Store the Microsoft client ID and secret</span></span>

<span data-ttu-id="2e42c-129">Archiviare le impostazioni riservate, ad esempio i valori di ID client e segreto Microsoft con [gestione Secret](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="2e42c-129">Store sensitive settings such as the Microsoft client ID and secret values with [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="2e42c-130">Per questo esempio, attenersi alla procedura seguente:</span><span class="sxs-lookup"><span data-stu-id="2e42c-130">For this sample, use the following steps:</span></span>

1. <span data-ttu-id="2e42c-131">Inizializzare il progetto per l'archiviazione segreta in base alle istruzioni riportate in [abilitare l'archiviazione segreta](xref:security/app-secrets#enable-secret-storage).</span><span class="sxs-lookup"><span data-stu-id="2e42c-131">Initialize the project for secret storage per the instructions at [Enable secret storage](xref:security/app-secrets#enable-secret-storage).</span></span>
1. <span data-ttu-id="2e42c-132">Archiviare le impostazioni sensibili nell'archivio dei segreti locali con le chiavi segrete `Authentication:Microsoft:ClientId` e `Authentication:Microsoft:ClientSecret` :</span><span class="sxs-lookup"><span data-stu-id="2e42c-132">Store the sensitive settings in the local secret store with the secret keys `Authentication:Microsoft:ClientId` and `Authentication:Microsoft:ClientSecret`:</span></span>

    ```dotnetcli
    dotnet user-secrets set "Authentication:Microsoft:ClientId" "<client-id>"
    dotnet user-secrets set "Authentication:Microsoft:ClientSecret" "<client-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="configure-microsoft-account-authentication"></a><span data-ttu-id="2e42c-133">Configurare l'autenticazione dell'account Microsoft</span><span class="sxs-lookup"><span data-stu-id="2e42c-133">Configure Microsoft Account Authentication</span></span>

<span data-ttu-id="2e42c-134">Aggiungere il servizio account Microsoft a `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="2e42c-134">Add the Microsoft Account service to the `Startup.ConfigureServices`:</span></span>

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupMS3x.cs?name=snippet&highlight=10-14)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

<span data-ttu-id="2e42c-135">Per altre informazioni sulle opzioni di configurazione supportate dall'autenticazione dell'account Microsoft, vedere le informazioni di riferimento sull'API [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.builder.microsoftaccountoptions) .</span><span class="sxs-lookup"><span data-stu-id="2e42c-135">For more information about configuration options supported by Microsoft Account authentication, see the [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.builder.microsoftaccountoptions) API reference.</span></span> <span data-ttu-id="2e42c-136">Questa operazione può essere utilizzata per richiedere informazioni diverse sull'utente.</span><span class="sxs-lookup"><span data-stu-id="2e42c-136">This can be used to request different information about the user.</span></span>

## <a name="sign-in-with-microsoft-account"></a><span data-ttu-id="2e42c-137">Account Accedi con Microsoft</span><span class="sxs-lookup"><span data-stu-id="2e42c-137">Sign in with Microsoft Account</span></span>

<span data-ttu-id="2e42c-138">Eseguire l'app e fare clic su **Accedi**.</span><span class="sxs-lookup"><span data-stu-id="2e42c-138">Run the app and click **Log in**.</span></span> <span data-ttu-id="2e42c-139">Viene visualizzata un'opzione per accedere con Microsoft.</span><span class="sxs-lookup"><span data-stu-id="2e42c-139">An option to sign in with Microsoft appears.</span></span> <span data-ttu-id="2e42c-140">Quando si fa clic su Microsoft, si viene reindirizzati a Microsoft per l'autenticazione.</span><span class="sxs-lookup"><span data-stu-id="2e42c-140">When you click on Microsoft, you are redirected to Microsoft for authentication.</span></span> <span data-ttu-id="2e42c-141">Dopo aver eseguito l'accesso con l'account Microsoft, verrà richiesto di consentire all'app di accedere alle informazioni:</span><span class="sxs-lookup"><span data-stu-id="2e42c-141">After signing in with your Microsoft Account, you will be prompted to let the app access your info:</span></span>

<span data-ttu-id="2e42c-142">Toccare **Sì** e si verrà reindirizzati di nuovo al sito Web in cui è possibile impostare la posta elettronica.</span><span class="sxs-lookup"><span data-stu-id="2e42c-142">Tap **Yes** and you will be redirected back to the web site where you can set your email.</span></span>

<span data-ttu-id="2e42c-143">A questo punto è stato effettuato l'accesso con le credenziali Microsoft:</span><span class="sxs-lookup"><span data-stu-id="2e42c-143">You are now logged in using your Microsoft credentials:</span></span>

[!INCLUDE[](includes/chain-auth-providers.md)]

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a><span data-ttu-id="2e42c-144">Risoluzione dei problemi</span><span class="sxs-lookup"><span data-stu-id="2e42c-144">Troubleshooting</span></span>

* <span data-ttu-id="2e42c-145">Se il provider di account Microsoft reindirizza l'utente a una pagina di errore di accesso, prendere nota dei parametri della stringa di query titolo e Descrizione dell'errore direttamente dopo l' `#` hashtag nell'URI.</span><span class="sxs-lookup"><span data-stu-id="2e42c-145">If the Microsoft Account provider redirects you to a sign in error page, note the error title and description query string parameters directly following the `#` (hashtag) in the Uri.</span></span>

  <span data-ttu-id="2e42c-146">Anche se il messaggio di errore sembra indicare un problema con l'autenticazione Microsoft, la causa più comune è l'URI dell'applicazione che non corrisponde ad alcuno degli **URI di reindirizzamento** specificati per la piattaforma **Web** .</span><span class="sxs-lookup"><span data-stu-id="2e42c-146">Although the error message seems to indicate a problem with Microsoft authentication, the most common cause is your application Uri not matching any of the **Redirect URIs** specified for the **Web** platform.</span></span>
* <span data-ttu-id="2e42c-147">Se Identity non è configurato chiamando `services.AddIdentity` in `ConfigureServices` , il tentativo di eseguire l'autenticazione comporterà *l'eccezione ArgumentException: è necessario specificare l'opzione ' SignInScheme '*.</span><span class="sxs-lookup"><span data-stu-id="2e42c-147">If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate will result in *ArgumentException: The 'SignInScheme' option must be provided*.</span></span> <span data-ttu-id="2e42c-148">Il modello di progetto utilizzato in questo esempio garantisce che questa operazione venga eseguita.</span><span class="sxs-lookup"><span data-stu-id="2e42c-148">The project template used in this sample ensures that this is done.</span></span>
* <span data-ttu-id="2e42c-149">Se il database del sito non è stato creato applicando la migrazione iniziale, si otterrà *un'operazione di database non riuscita durante l'elaborazione dell'* errore di richiesta.</span><span class="sxs-lookup"><span data-stu-id="2e42c-149">If the site database has not been created by applying the initial migration, you will get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="2e42c-150">Toccare **applica migrazioni** per creare il database e aggiornare per continuare a superare l'errore.</span><span class="sxs-lookup"><span data-stu-id="2e42c-150">Tap **Apply Migrations** to create the database and refresh to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="2e42c-151">Passaggi successivi</span><span class="sxs-lookup"><span data-stu-id="2e42c-151">Next steps</span></span>

* <span data-ttu-id="2e42c-152">Questo articolo ha illustrato come è possibile eseguire l'autenticazione con Microsoft.</span><span class="sxs-lookup"><span data-stu-id="2e42c-152">This article showed how you can authenticate with Microsoft.</span></span> <span data-ttu-id="2e42c-153">È possibile seguire un approccio simile per l'autenticazione con altri provider elencati nella [pagina precedente](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="2e42c-153">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>

* <span data-ttu-id="2e42c-154">Dopo aver pubblicato il sito Web nell'app Web di Azure, creare un nuovo segreto client nel portale per sviluppatori Microsoft.</span><span class="sxs-lookup"><span data-stu-id="2e42c-154">Once you publish your web site to Azure web app, create a new client secrets in the Microsoft Developer Portal.</span></span>

* <span data-ttu-id="2e42c-155">Impostare `Authentication:Microsoft:ClientId` e `Authentication:Microsoft:ClientSecret` come impostazioni dell'applicazione nella portale di Azure.</span><span class="sxs-lookup"><span data-stu-id="2e42c-155">Set the `Authentication:Microsoft:ClientId` and `Authentication:Microsoft:ClientSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="2e42c-156">Il sistema di configurazione è configurato per la lettura delle chiavi dalle variabili di ambiente.</span><span class="sxs-lookup"><span data-stu-id="2e42c-156">The configuration system is set up to read keys from environment variables.</span></span>
