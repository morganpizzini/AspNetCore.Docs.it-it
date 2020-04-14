---
title: Configurazione dell'accesso esterno di Twitter con ASP.NET Core
author: rick-anderson
description: Questa esercitazione illustra l'integrazione dell'autenticazione utente dell'account Twitter in un'app ASP.NET Core esistente.
ms.author: riande
ms.custom: mvc
ms.date: 03/19/2020
monikerRange: '>= aspnetcore-3.0'
uid: security/authentication/twitter-logins
ms.openlocfilehash: 1f5d667e905e49ae05f5aa31bd5b69ad126f6e28
ms.sourcegitcommit: 5af16166977da598953f82da3ed3b7712d38f6cb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81277288"
---
# <a name="twitter-external-sign-in-setup-with-aspnet-core"></a><span data-ttu-id="cab22-103">Configurazione dell'accesso esterno di Twitter con ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="cab22-103">Twitter external sign-in setup with ASP.NET Core</span></span>

<span data-ttu-id="cab22-104">Da [Valeriy Novytskyy](https://github.com/01binary) e [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="cab22-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="cab22-105">In questo esempio viene illustrato come consentire agli utenti di accedere con il [proprio account Twitter](https://dev.twitter.com/web/sign-in/desktop-browser) utilizzando un progetto ASP.NET Core 3.0 creato nella pagina [precedente.](xref:security/authentication/social/index)</span><span class="sxs-lookup"><span data-stu-id="cab22-105">This sample shows how to enable users to [sign in with their Twitter account](https://dev.twitter.com/web/sign-in/desktop-browser) using a sample ASP.NET Core 3.0 project created on the [previous page](xref:security/authentication/social/index).</span></span>

## <a name="create-the-app-in-twitter"></a><span data-ttu-id="cab22-106">Creare l'app in Twitter</span><span class="sxs-lookup"><span data-stu-id="cab22-106">Create the app in Twitter</span></span>

* <span data-ttu-id="cab22-107">Aggiungere il pacchetto [Microsoft.AspNetCore.Authentication.Twitter](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Twitter/3.0.0) NuGet al progetto.</span><span class="sxs-lookup"><span data-stu-id="cab22-107">Add the [Microsoft.AspNetCore.Authentication.Twitter](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Twitter/3.0.0) NuGet package to the project.</span></span>

* <span data-ttu-id="cab22-108">Individuare [https://apps.twitter.com/](https://apps.twitter.com/) ed eseguire l'accesso.</span><span class="sxs-lookup"><span data-stu-id="cab22-108">Navigate to [https://apps.twitter.com/](https://apps.twitter.com/) and sign in.</span></span> <span data-ttu-id="cab22-109">Se non hai già un account Twitter, usa il link **[Iscriviti ora](https://twitter.com/signup)** per crearne uno.</span><span class="sxs-lookup"><span data-stu-id="cab22-109">If you don't already have a Twitter account, use the **[Sign up now](https://twitter.com/signup)** link to create one.</span></span>

* <span data-ttu-id="cab22-110">Selezionare **Crea un'app**.</span><span class="sxs-lookup"><span data-stu-id="cab22-110">Select **Create an app**.</span></span> <span data-ttu-id="cab22-111">Compilare il **nome dell'app**, la **descrizione dell'applicazione** e l'URI **del sito Web** pubblico (questo può essere temporaneo fino a quando non si registra il nome di dominio):</span><span class="sxs-lookup"><span data-stu-id="cab22-111">Fill out the **App name**, **Application description** and public **Website** URI (this can be temporary until you register the domain name):</span></span>

* <span data-ttu-id="cab22-112">Seleziona la casella accanto a **Abilita l'accesso con Twitter**</span><span class="sxs-lookup"><span data-stu-id="cab22-112">Check the box next to **Enable Sign in with Twitter**</span></span>

* <span data-ttu-id="cab22-113">Microsoft.AspNetCore.Identity richiede agli utenti di disporre di un indirizzo di posta elettronica per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="cab22-113">Microsoft.AspNetCore.Identity requires users to have an email address by default.</span></span> <span data-ttu-id="cab22-114">Passare alla scheda **Autorizzazioni,** fare clic sul pulsante **Modifica** e selezionare la casella accanto a **Richiedi indirizzo di posta elettronica agli utenti**.</span><span class="sxs-lookup"><span data-stu-id="cab22-114">Go to the **Permissions** tab, click the **Edit** button and check the box next to **Request email address from users**.</span></span>

* <span data-ttu-id="cab22-115">Immettere l'URI di sviluppo con `/signin-twitter` l'aggiunta nel campo URL di **callback,** ad esempio: `https://webapp128.azurewebsites.net/signin-twitter`.</span><span class="sxs-lookup"><span data-stu-id="cab22-115">Enter your development URI with `/signin-twitter` appended into the **Callback URLs** field (for example: `https://webapp128.azurewebsites.net/signin-twitter`).</span></span> <span data-ttu-id="cab22-116">Lo schema di autenticazione di Twitter configurato `/signin-twitter` più avanti in questo esempio gestirà automaticamente le richieste durante la route per implementare il flusso OAuth.</span><span class="sxs-lookup"><span data-stu-id="cab22-116">The Twitter authentication scheme configured later in this sample will automatically handle requests at `/signin-twitter` route to implement the OAuth flow.</span></span>

  > [!NOTE]
  > <span data-ttu-id="cab22-117">Il segmento `/signin-twitter` URI viene impostato come callback predefinito del provider di autenticazione di Twitter.</span><span class="sxs-lookup"><span data-stu-id="cab22-117">The URI segment `/signin-twitter` is set as the default callback of the Twitter authentication provider.</span></span> <span data-ttu-id="cab22-118">È possibile modificare l'URI di callback predefinito durante la configurazione del middleware di autenticazione di Twitter tramite la proprietà [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) ereditata della classe [TwitterOptions.](/dotnet/api/microsoft.aspnetcore.authentication.twitter.twitteroptions)</span><span class="sxs-lookup"><span data-stu-id="cab22-118">You can change the default callback URI while configuring the Twitter authentication middleware via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [TwitterOptions](/dotnet/api/microsoft.aspnetcore.authentication.twitter.twitteroptions) class.</span></span>

* <span data-ttu-id="cab22-119">Compilare il resto del modulo e selezionare **Crea**.</span><span class="sxs-lookup"><span data-stu-id="cab22-119">Fill out the rest of the form and select **Create**.</span></span> <span data-ttu-id="cab22-120">Vengono visualizzati i nuovi dettagli dell'applicazione:</span><span class="sxs-lookup"><span data-stu-id="cab22-120">New application details are displayed:</span></span>

## <a name="store-the-twitter-consumer-api-key-and-secret"></a><span data-ttu-id="cab22-121">Memorizzare la chiave API consumer di Twitter e il segreto</span><span class="sxs-lookup"><span data-stu-id="cab22-121">Store the Twitter consumer API key and secret</span></span>

<span data-ttu-id="cab22-122">Memorizzare le impostazioni sensibili come la chiave API consumer Twitter e segreto con [Secret Manager](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="cab22-122">Store sensitive settings such as the Twitter consumer API key and secret with [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="cab22-123">Per questo esempio, usare la procedura seguente:For this sample, use the following steps:</span><span class="sxs-lookup"><span data-stu-id="cab22-123">For this sample, use the following steps:</span></span>

1. <span data-ttu-id="cab22-124">Inizializzare il progetto per l'archiviazione segreta in base alle istruzioni in [Abilitare l'archiviazione segreta.](xref:security/app-secrets#enable-secret-storage)</span><span class="sxs-lookup"><span data-stu-id="cab22-124">Initialize the project for secret storage per the instructions at [Enable secret storage](xref:security/app-secrets#enable-secret-storage).</span></span>
1. <span data-ttu-id="cab22-125">Memorizzare le impostazioni sensibili nell'archivio `Authentication:Twitter:ConsumerKey` `Authentication:Twitter:ConsumerSecret`segreto locale con le chiavi secrets e :</span><span class="sxs-lookup"><span data-stu-id="cab22-125">Store the sensitive settings in the local secret store with the secrets keys `Authentication:Twitter:ConsumerKey` and `Authentication:Twitter:ConsumerSecret`:</span></span>

    ```dotnetcli
    dotnet user-secrets set "Authentication:Twitter:ConsumerAPIKey" "<consumer-api-key>"
    dotnet user-secrets set "Authentication:Twitter:ConsumerSecret" "<consumer-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="cab22-126">Questi token sono disponibili nella scheda Chiavi e token di accesso dopo aver creato una nuova applicazione Twitter:These tokens can be found on the **Keys and Access Tokens** tab after creating a new Twitter application:</span><span class="sxs-lookup"><span data-stu-id="cab22-126">These tokens can be found on the **Keys and Access Tokens** tab after creating a new Twitter application:</span></span>

## <a name="configure-twitter-authentication"></a><span data-ttu-id="cab22-127">Configurare l'autenticazione di Twitter</span><span class="sxs-lookup"><span data-stu-id="cab22-127">Configure Twitter Authentication</span></span>

<span data-ttu-id="cab22-128">Aggiungere il servizio `ConfigureServices` Twitter nel metodo nel file *di Startup.cs:*</span><span class="sxs-lookup"><span data-stu-id="cab22-128">Add the Twitter service in the `ConfigureServices` method in *Startup.cs* file:</span></span>

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupTwitter3x.cs?name=snippet&highlight=10-15)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

<span data-ttu-id="cab22-129">Per altre informazioni sulle opzioni di configurazione supportate dall'autenticazione di Twitter, vedere le informazioni di riferimento sull'API [TwitterOptions.See](/dotnet/api/microsoft.aspnetcore.builder.twitteroptions) the TwitterOptions API reference for more information on configuration options supported by Twitter authentication.</span><span class="sxs-lookup"><span data-stu-id="cab22-129">See the [TwitterOptions](/dotnet/api/microsoft.aspnetcore.builder.twitteroptions) API reference for more information on configuration options supported by Twitter authentication.</span></span> <span data-ttu-id="cab22-130">Può essere utilizzato per richiedere informazioni diverse sull'utente.</span><span class="sxs-lookup"><span data-stu-id="cab22-130">This can be used to request different information about the user.</span></span>

## <a name="sign-in-with-twitter"></a><span data-ttu-id="cab22-131">Accedi con Twitter</span><span class="sxs-lookup"><span data-stu-id="cab22-131">Sign in with Twitter</span></span>

<span data-ttu-id="cab22-132">Eseguire l'app e selezionare **Accedi**.</span><span class="sxs-lookup"><span data-stu-id="cab22-132">Run the app and select **Log in**.</span></span> <span data-ttu-id="cab22-133">Viene visualizzata un'opzione per accedere con Twitter:</span><span class="sxs-lookup"><span data-stu-id="cab22-133">An option to sign in with Twitter appears:</span></span>

<span data-ttu-id="cab22-134">Cliccando su **Twitter** reindirizza a Twitter per l'autenticazione:</span><span class="sxs-lookup"><span data-stu-id="cab22-134">Clicking on **Twitter** redirects to Twitter for authentication:</span></span>

<span data-ttu-id="cab22-135">Dopo aver inserito le credenziali di Twitter, si viene reindirizzati al sito web dove è possibile impostare la posta elettronica.</span><span class="sxs-lookup"><span data-stu-id="cab22-135">After entering your Twitter credentials, you are redirected back to the web site where you can set your email.</span></span>

<span data-ttu-id="cab22-136">Ora hai effettuato l'accesso utilizzando le tue credenziali Twitter:</span><span class="sxs-lookup"><span data-stu-id="cab22-136">You are now logged in using your Twitter credentials:</span></span>

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

<!-- 
### React to cancel Authorize External sign-in
Twitter doesn't support AccessDeniedPath
Rather in the twitter setup, you can provide an External sign-in homepage. The external sign-in homepage doesn't support localhost. Tested with https://cors3.azurewebsites.net/ and that works.
-->

## <a name="troubleshooting"></a><span data-ttu-id="cab22-137">Risoluzione dei problemi</span><span class="sxs-lookup"><span data-stu-id="cab22-137">Troubleshooting</span></span>

* <span data-ttu-id="cab22-138">**ASP.NET Solo Core 2.x:** Se Identity non è `services.AddIdentity` configurato `ConfigureServices`chiamando in , il tentativo di autenticazione comporterà *ArgumentException: deve essere fornita l'opzione 'SignInScheme'.*</span><span class="sxs-lookup"><span data-stu-id="cab22-138">**ASP.NET Core 2.x only:** If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate will result in *ArgumentException: The 'SignInScheme' option must be provided*.</span></span> <span data-ttu-id="cab22-139">Il modello di progetto utilizzato in questo esempio garantisce che questa operazione venga eseguita.</span><span class="sxs-lookup"><span data-stu-id="cab22-139">The project template used in this sample ensures that this is done.</span></span>
* <span data-ttu-id="cab22-140">Se il database del sito non è stato creato applicando la migrazione iniziale, si otterrà *un'operazione del database non riuscita durante l'elaborazione dell'errore* di richiesta.</span><span class="sxs-lookup"><span data-stu-id="cab22-140">If the site database has not been created by applying the initial migration, you will get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="cab22-141">Toccare **Applica migrazioni** per creare il database e aggiornare per continuare oltre l'errore.</span><span class="sxs-lookup"><span data-stu-id="cab22-141">Tap **Apply Migrations** to create the database and refresh to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="cab22-142">Passaggi successivi</span><span class="sxs-lookup"><span data-stu-id="cab22-142">Next steps</span></span>

* <span data-ttu-id="cab22-143">Questo articolo ha mostrato come è possibile eseguire l'autenticazione con Twitter.</span><span class="sxs-lookup"><span data-stu-id="cab22-143">This article showed how you can authenticate with Twitter.</span></span> <span data-ttu-id="cab22-144">È possibile seguire un approccio simile per l'autenticazione con altri provider elencati nella [pagina precedente.](xref:security/authentication/social/index)</span><span class="sxs-lookup"><span data-stu-id="cab22-144">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>

* <span data-ttu-id="cab22-145">Dopo aver pubblicato il sito Web nell'app `ConsumerSecret` Web di Azure, è necessario reimpostare il sito nel portale per sviluppatori di Twitter.Once you publish your web site to Azure web app, you should reset the in the Twitter developer portal.</span><span class="sxs-lookup"><span data-stu-id="cab22-145">Once you publish your web site to Azure web app, you should reset the `ConsumerSecret` in the Twitter developer portal.</span></span>

* <span data-ttu-id="cab22-146">Impostare `Authentication:Twitter:ConsumerKey` `Authentication:Twitter:ConsumerSecret` le impostazioni e come dell'applicazione nel portale di Azure.Set the and as application settings in the Azure portal.</span><span class="sxs-lookup"><span data-stu-id="cab22-146">Set the `Authentication:Twitter:ConsumerKey` and `Authentication:Twitter:ConsumerSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="cab22-147">Il sistema di configurazione è impostato per leggere le chiavi dalle variabili di ambiente.</span><span class="sxs-lookup"><span data-stu-id="cab22-147">The configuration system is set up to read keys from environment variables.</span></span>
