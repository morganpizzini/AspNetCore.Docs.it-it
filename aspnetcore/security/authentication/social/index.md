---
title: Autenticazione dei provider Facebook, Google ed esterni in ASP.NET Core
author: rick-anderson
description: Questa esercitazione illustra come compilare un'app ASP.NET Core usando OAuth 2,0 con provider di autenticazione esterni.
ms.author: riande
ms.custom: mvc
ms.date: 01/23/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/social/index
ms.openlocfilehash: a2c068df85290a59d45432cb168b2c882b59405b
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/26/2020
ms.locfileid: "85405367"
---
# <a name="facebook-google-and-external-provider-authentication-in-aspnet-core"></a><span data-ttu-id="c5c69-103">Autenticazione dei provider Facebook, Google ed esterni in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c5c69-103">Facebook, Google, and external provider authentication in ASP.NET Core</span></span>

<span data-ttu-id="c5c69-104">Da [Valeriy Novytskyy](https://github.com/01binary) e [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="c5c69-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="c5c69-105">Questa esercitazione illustra come compilare un'app ASP.NET Core 3,0 che consente agli utenti di accedere usando OAuth 2,0 con le credenziali dei provider di autenticazione esterni.</span><span class="sxs-lookup"><span data-stu-id="c5c69-105">This tutorial demonstrates how to build an ASP.NET Core 3.0 app that enables users to sign in using OAuth 2.0 with credentials from external authentication providers.</span></span>

<span data-ttu-id="c5c69-106">I provider [Facebook](xref:security/authentication/facebook-logins), [Twitter](xref:security/authentication/twitter-logins), [Google](xref:security/authentication/google-logins)e [Microsoft](xref:security/authentication/microsoft-logins) sono descritti nelle sezioni seguenti e usano il progetto iniziale creato in questo articolo.</span><span class="sxs-lookup"><span data-stu-id="c5c69-106">[Facebook](xref:security/authentication/facebook-logins), [Twitter](xref:security/authentication/twitter-logins), [Google](xref:security/authentication/google-logins), and [Microsoft](xref:security/authentication/microsoft-logins) providers are covered in the following sections and use the starter project created in this article.</span></span> <span data-ttu-id="c5c69-107">Altri provider sono disponibili nei pacchetti di terze parti, ad esempio [AspNet.Security.OAuth.Providers](https://github.com/aspnet-contrib/AspNet.Security.OAuth.Providers) e [AspNet.Security.OpenId.Providers](https://github.com/aspnet-contrib/AspNet.Security.OpenId.Providers).</span><span class="sxs-lookup"><span data-stu-id="c5c69-107">Other providers are available in third-party packages such as [AspNet.Security.OAuth.Providers](https://github.com/aspnet-contrib/AspNet.Security.OAuth.Providers) and [AspNet.Security.OpenId.Providers](https://github.com/aspnet-contrib/AspNet.Security.OpenId.Providers).</span></span>

<span data-ttu-id="c5c69-108">Il fatto di consentire agli utenti l'accesso con le proprie credenziali esistenti:</span><span class="sxs-lookup"><span data-stu-id="c5c69-108">Enabling users to sign in with their existing credentials:</span></span>

* <span data-ttu-id="c5c69-109">È pratico per gli utenti.</span><span class="sxs-lookup"><span data-stu-id="c5c69-109">Is convenient for the users.</span></span>
* <span data-ttu-id="c5c69-110">Trasferisce a terzi molti aspetti complicati del processo di accesso.</span><span class="sxs-lookup"><span data-stu-id="c5c69-110">Shifts many of the complexities of managing the sign-in process onto a third party.</span></span>

<span data-ttu-id="c5c69-111">Per degli esempi di come gli account di accesso ai social possano risultare utili per la conversione del traffico e del cliente, vedere dei case study da [Facebook](https://www.facebook.com/unsupportedbrowser) e [Twitter](https://dev.twitter.com/resources/case-studies).</span><span class="sxs-lookup"><span data-stu-id="c5c69-111">For examples of how social logins can drive traffic and customer conversions, see case studies by [Facebook](https://www.facebook.com/unsupportedbrowser) and [Twitter](https://dev.twitter.com/resources/case-studies).</span></span>

## <a name="create-a-new-aspnet-core-project"></a><span data-ttu-id="c5c69-112">Creare un nuovo progetto ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c5c69-112">Create a New ASP.NET Core Project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c5c69-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c5c69-113">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="c5c69-114">Creare un nuovo progetto.</span><span class="sxs-lookup"><span data-stu-id="c5c69-114">Create a new project.</span></span>
* <span data-ttu-id="c5c69-115">Selezionare **Applicazione Web ASP.NET Core** e **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="c5c69-115">Select **ASP.NET Core Web Application** and **Next**.</span></span>
* <span data-ttu-id="c5c69-116">Specificare un **Nome progetto** e confermare o modificare il **Percorso**.</span><span class="sxs-lookup"><span data-stu-id="c5c69-116">Provide a **Project name** and confirm or change the **Location**.</span></span> <span data-ttu-id="c5c69-117">Selezionare **Crea**.</span><span class="sxs-lookup"><span data-stu-id="c5c69-117">Select **Create**.</span></span>
* <span data-ttu-id="c5c69-118">Selezionare la versione più recente di ASP.NET Core nell'elenco a discesa (**ASP.NET Core {X. Y}**), quindi selezionare **applicazione Web**.</span><span class="sxs-lookup"><span data-stu-id="c5c69-118">Select the latest version of ASP.NET Core in the drop-down (**ASP.NET Core {X.Y}**), and then select **Web Application**.</span></span>
* <span data-ttu-id="c5c69-119">In **Autenticazione** selezionare **Modifica** e impostare l'autenticazione su **Account utente individuali**.</span><span class="sxs-lookup"><span data-stu-id="c5c69-119">Under **Authentication**, select **Change** and set the authentication to **Individual User Accounts**.</span></span> <span data-ttu-id="c5c69-120">Selezionare **OK**.</span><span class="sxs-lookup"><span data-stu-id="c5c69-120">Select **OK**.</span></span>
* <span data-ttu-id="c5c69-121">Nella finestra **Crea una nuova applicazione Web ASP.NET Core** selezionare **Crea**.</span><span class="sxs-lookup"><span data-stu-id="c5c69-121">In the **Create a new ASP.NET Core Web Application** window, select **Create**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="c5c69-122">Visual Studio Code / Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="c5c69-122">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="c5c69-123">Aprire il terminale.</span><span class="sxs-lookup"><span data-stu-id="c5c69-123">Open the terminal.</span></span>  <span data-ttu-id="c5c69-124">Per Visual Studio Code è possibile aprire il [terminale integrato](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="c5c69-124">For Visual Studio Code you can open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="c5c69-125">Cambiare directory (`cd`) e passare alla cartella che conterrà il progetto.</span><span class="sxs-lookup"><span data-stu-id="c5c69-125">Change directories (`cd`) to a folder which will contain the project.</span></span>

* <span data-ttu-id="c5c69-126">Per Windows, eseguire il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="c5c69-126">For Windows, run the following command:</span></span>

  ```dotnetcli
  dotnet new webapp -o WebApp1 -au Individual -uld
  ```

  <span data-ttu-id="c5c69-127">Per macOS e Linux, eseguire il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="c5c69-127">For macOS and Linux, run the following command:</span></span>

  ```dotnetcli
  dotnet new webapp -o WebApp1 -au Individual
  ```

  * <span data-ttu-id="c5c69-128">Il `dotnet new` comando crea un nuovo Razor progetto di pagine nella cartella *app Web 1* .</span><span class="sxs-lookup"><span data-stu-id="c5c69-128">The `dotnet new` command creates a new Razor Pages project in the *WebApp1* folder.</span></span>
  * <span data-ttu-id="c5c69-129">`-au Individual` crea il codice per l'autenticazione individuale.</span><span class="sxs-lookup"><span data-stu-id="c5c69-129">`-au Individual` creates the code for Individual authentication.</span></span>
  * <span data-ttu-id="c5c69-130">`-uld`Usa il database locale, una versione leggera di SQL Server Express per Windows.</span><span class="sxs-lookup"><span data-stu-id="c5c69-130">`-uld` uses LocalDB, a lightweight version of SQL Server Express for Windows.</span></span> <span data-ttu-id="c5c69-131">Omettere `-uld` per usare SQLite.</span><span class="sxs-lookup"><span data-stu-id="c5c69-131">Omit `-uld` to use SQLite.</span></span>
  * <span data-ttu-id="c5c69-132">Il comando `code` apre la cartella *WebApp1* in una nuova istanza di Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="c5c69-132">The `code` command opens the *WebApp1* folder in a new instance of Visual Studio Code.</span></span>

---

## <a name="apply-migrations"></a><span data-ttu-id="c5c69-133">Applicare le migrazioni</span><span class="sxs-lookup"><span data-stu-id="c5c69-133">Apply migrations</span></span>

* <span data-ttu-id="c5c69-134">Eseguire l'app e selezionare il collegamento **Registra**.</span><span class="sxs-lookup"><span data-stu-id="c5c69-134">Run the app and select the **Register** link.</span></span>
* <span data-ttu-id="c5c69-135">Immettere l'indirizzo di posta elettronica e la password per il nuovo account, quindi selezionare **Registra**.</span><span class="sxs-lookup"><span data-stu-id="c5c69-135">Enter the email and password for the new account, and then select **Register**.</span></span>
* <span data-ttu-id="c5c69-136">Seguire le istruzioni per applicare le migrazioni.</span><span class="sxs-lookup"><span data-stu-id="c5c69-136">Follow the instructions to apply migrations.</span></span>

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="use-secretmanager-to-store-tokens-assigned-by-login-providers"></a><span data-ttu-id="c5c69-137">Usare SecretManager per archiviare i token assegnati dai provider di accesso</span><span class="sxs-lookup"><span data-stu-id="c5c69-137">Use SecretManager to store tokens assigned by login providers</span></span>

<span data-ttu-id="c5c69-138">I provider di accesso ai social assegnano i token **ID applicazione** e **Segreto dell'applicazione** durante il processo di registrazione.</span><span class="sxs-lookup"><span data-stu-id="c5c69-138">Social login providers assign **Application Id** and **Application Secret** tokens during the registration process.</span></span> <span data-ttu-id="c5c69-139">La denominazione esatta dei token varia a seconda del provider.</span><span class="sxs-lookup"><span data-stu-id="c5c69-139">The exact token names vary by provider.</span></span> <span data-ttu-id="c5c69-140">Questi token rappresentano le credenziali usate dall'app per accedere alle API.</span><span class="sxs-lookup"><span data-stu-id="c5c69-140">These tokens represent the credentials your app uses to access their API.</span></span> <span data-ttu-id="c5c69-141">I token costituiscono i "segreti" che è possibile collegare alla configurazione dell'app usando [Secret Manager](xref:security/app-secrets#secret-manager).</span><span class="sxs-lookup"><span data-stu-id="c5c69-141">The tokens constitute the "secrets" that can be linked to your app configuration with the help of [Secret Manager](xref:security/app-secrets#secret-manager).</span></span> <span data-ttu-id="c5c69-142">Secret Manager è un'alternativa che offre maggior sicurezza rispetto alla memorizzazione dei token in un file di configurazione, ad esempio *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="c5c69-142">Secret Manager is a more secure alternative to storing the tokens in a configuration file, such as *appsettings.json*.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="c5c69-143">Secret Manager è progettato esclusivamente per lo sviluppo.</span><span class="sxs-lookup"><span data-stu-id="c5c69-143">Secret Manager is for development purposes only.</span></span> <span data-ttu-id="c5c69-144">È possibile memorizzare e proteggere i segreti relativi al test e alla produzione di Azure usando il [provider di configurazione di Azure Key Vault](xref:security/key-vault-configuration).</span><span class="sxs-lookup"><span data-stu-id="c5c69-144">You can store and protect Azure test and production secrets with the [Azure Key Vault configuration provider](xref:security/key-vault-configuration).</span></span>

<span data-ttu-id="c5c69-145">Seguire la procedura descritta nell'argomento [Archiviazione sicura di segreti dell'app durante lo sviluppo di ASP.NET Core](xref:security/app-secrets) per archiviare i token assegnati dai singoli provider di accesso elencati di seguito.</span><span class="sxs-lookup"><span data-stu-id="c5c69-145">Follow the steps in [Safe storage of app secrets in development in ASP.NET Core](xref:security/app-secrets) topic to store tokens assigned by each login provider below.</span></span>

## <a name="setup-login-providers-required-by-your-application"></a><span data-ttu-id="c5c69-146">Impostare i provider di accesso necessari per l'applicazione</span><span class="sxs-lookup"><span data-stu-id="c5c69-146">Setup login providers required by your application</span></span>

<span data-ttu-id="c5c69-147">Usare gli argomenti seguenti per configurare l'applicazione per usare i rispettivi provider:</span><span class="sxs-lookup"><span data-stu-id="c5c69-147">Use the following topics to configure your application to use the respective providers:</span></span>

* <span data-ttu-id="c5c69-148">Istruzioni di [Facebook](xref:security/authentication/facebook-logins)</span><span class="sxs-lookup"><span data-stu-id="c5c69-148">[Facebook](xref:security/authentication/facebook-logins) instructions</span></span>
* <span data-ttu-id="c5c69-149">Istruzioni di [Twitter](xref:security/authentication/twitter-logins)</span><span class="sxs-lookup"><span data-stu-id="c5c69-149">[Twitter](xref:security/authentication/twitter-logins) instructions</span></span>
* <span data-ttu-id="c5c69-150">Istruzioni di [Google](xref:security/authentication/google-logins)</span><span class="sxs-lookup"><span data-stu-id="c5c69-150">[Google](xref:security/authentication/google-logins) instructions</span></span>
* <span data-ttu-id="c5c69-151">Istruzioni di [Microsoft](xref:security/authentication/microsoft-logins)</span><span class="sxs-lookup"><span data-stu-id="c5c69-151">[Microsoft](xref:security/authentication/microsoft-logins) instructions</span></span>
* <span data-ttu-id="c5c69-152">Istruzioni di [altri provider](xref:security/authentication/otherlogins)</span><span class="sxs-lookup"><span data-stu-id="c5c69-152">[Other provider](xref:security/authentication/otherlogins) instructions</span></span>

[!INCLUDE[](includes/chain-auth-providers.md)]

## <a name="optionally-set-password"></a><span data-ttu-id="c5c69-153">Impostare facoltativamente la password</span><span class="sxs-lookup"><span data-stu-id="c5c69-153">Optionally set password</span></span>

<span data-ttu-id="c5c69-154">Quando ci si registra con un provider di accesso esterno, non si dispone di una password registrata con l'app.</span><span class="sxs-lookup"><span data-stu-id="c5c69-154">When you register with an external login provider, you don't have a password registered with the app.</span></span> <span data-ttu-id="c5c69-155">Questo evita la creazione e la memorizzazione di una password per il sito, ma crea anche una dipendenza dal provider di accesso esterno.</span><span class="sxs-lookup"><span data-stu-id="c5c69-155">This alleviates you from creating and remembering a password for the site, but it also makes you dependent on the external login provider.</span></span> <span data-ttu-id="c5c69-156">Se il provider di accesso esterno non è disponibile, non si potrà accedere al sito web.</span><span class="sxs-lookup"><span data-stu-id="c5c69-156">If the external login provider is unavailable, you won't be able to sign in to the web site.</span></span>

<span data-ttu-id="c5c69-157">Per creare una password e accedere usando la posta elettronica impostata durante il processo di accesso con provider esterni:</span><span class="sxs-lookup"><span data-stu-id="c5c69-157">To create a password and sign in using your email that you set during the sign in process with external providers:</span></span>

* <span data-ttu-id="c5c69-158">Selezionare il collegamento **Salve &lt;alias di posta elettronica&gt;** nell'angolo superiore destro per passare alla vista **Gestione**.</span><span class="sxs-lookup"><span data-stu-id="c5c69-158">Select the **Hello &lt;email alias&gt;** link at the top-right corner to navigate to the **Manage** view.</span></span>

![Vista Gestione dell'applicazione Web](index/_static/pass1a.png)

* <span data-ttu-id="c5c69-160">Selezionare **Crea**</span><span class="sxs-lookup"><span data-stu-id="c5c69-160">Select **Create**</span></span>

![Impostare la pagina della password](index/_static/pass2a.png)

* <span data-ttu-id="c5c69-162">Impostare una password valida in modo da usarla per accedere con la posta elettronica.</span><span class="sxs-lookup"><span data-stu-id="c5c69-162">Set a valid password and you can use this to sign in with your email.</span></span>

## <a name="next-steps"></a><span data-ttu-id="c5c69-163">Passaggi successivi</span><span class="sxs-lookup"><span data-stu-id="c5c69-163">Next steps</span></span>

* <span data-ttu-id="c5c69-164">Per informazioni su come personalizzare i pulsanti di accesso, vedere [questo problema di GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/10563) .</span><span class="sxs-lookup"><span data-stu-id="c5c69-164">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/10563) for information on how to customize the login buttons.</span></span>
* <span data-ttu-id="c5c69-165">Questo articolo ha introdotto l'autenticazione esterna e ha illustrato i prerequisiti necessari per aggiungere gli account di accesso esterni all'app di ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c5c69-165">This article introduced external authentication and explained the prerequisites required to add external logins to your ASP.NET Core app.</span></span>
* <span data-ttu-id="c5c69-166">Pagine di riferimento specifico del provider per configurare gli account di accesso per i provider richiesti dall'app.</span><span class="sxs-lookup"><span data-stu-id="c5c69-166">Reference provider-specific pages to configure logins for the providers required by your app.</span></span>
* <span data-ttu-id="c5c69-167">Può essere opportuno salvare in modo permanente dati aggiuntivi sull'utente e il relativo accesso e i token di aggiornamento.</span><span class="sxs-lookup"><span data-stu-id="c5c69-167">You may want to persist additional data about the user and their access and refresh tokens.</span></span> <span data-ttu-id="c5c69-168">Per altre informazioni, vedere <xref:security/authentication/social/additional-claims>.</span><span class="sxs-lookup"><span data-stu-id="c5c69-168">For more information, see <xref:security/authentication/social/additional-claims>.</span></span>
