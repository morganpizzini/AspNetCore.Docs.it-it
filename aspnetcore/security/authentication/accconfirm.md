---
title: Account confirmation and password recovery in ASP.NET Core (Conferma dell'account e recupero della password in ASP.NET Core)
author: rick-anderson
description: Informazioni su come creare un'app ASP.NET Core con la conferma della posta elettronica e la reimpostazione della password.
ms.author: riande
ms.date: 03/11/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/accconfirm
ms.openlocfilehash: 8d4488b3953a8c87033d3a092b656409a0c6a52d
ms.sourcegitcommit: d243fadeda20ad4f142ea60301ae5f5e0d41ed60
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/12/2020
ms.locfileid: "84724367"
---
# <a name="account-confirmation-and-password-recovery-in-aspnet-core"></a><span data-ttu-id="91a83-103">Account confirmation and password recovery in ASP.NET Core (Conferma dell'account e recupero della password in ASP.NET Core)</span><span class="sxs-lookup"><span data-stu-id="91a83-103">Account confirmation and password recovery in ASP.NET Core</span></span>

<span data-ttu-id="91a83-104">Di [Rick Anderson](https://twitter.com/RickAndMSFT), [Ponant](https://github.com/Ponant)e [Joe Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="91a83-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Ponant](https://github.com/Ponant), and [Joe Audette](https://twitter.com/joeaudette)</span></span>

<span data-ttu-id="91a83-105">Questa esercitazione illustra come compilare un'app ASP.NET Core con la conferma della posta elettronica e la reimpostazione della password.</span><span class="sxs-lookup"><span data-stu-id="91a83-105">This tutorial shows how to build an ASP.NET Core app with email confirmation and password reset.</span></span> <span data-ttu-id="91a83-106">Questa esercitazione **non** è un argomento iniziale.</span><span class="sxs-lookup"><span data-stu-id="91a83-106">This tutorial is **not** a beginning topic.</span></span> <span data-ttu-id="91a83-107">È necessario avere familiarità con:</span><span class="sxs-lookup"><span data-stu-id="91a83-107">You should be familiar with:</span></span>

* [<span data-ttu-id="91a83-108">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="91a83-108">ASP.NET Core</span></span>](xref:tutorials/razor-pages/razor-pages-start)
* [<span data-ttu-id="91a83-109">autenticazione</span><span class="sxs-lookup"><span data-stu-id="91a83-109">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="91a83-110">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="91a83-110">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

<!-- see C:/Dropbox/wrk/Code/SendGridConsole/Program.cs -->

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="91a83-111">Vedere [questo file PDF](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf) per la versione ASP.NET Core 1,1.</span><span class="sxs-lookup"><span data-stu-id="91a83-111">See [this PDF file](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf) for the ASP.NET Core 1.1 version.</span></span>

::: moniker-end

::: moniker range="> aspnetcore-2.2"

## <a name="prerequisites"></a><span data-ttu-id="91a83-112">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="91a83-112">Prerequisites</span></span>

[<span data-ttu-id="91a83-113">.NET Core 3,0 SDK o versione successiva</span><span class="sxs-lookup"><span data-stu-id="91a83-113">.NET Core 3.0 SDK or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core/3.0)

## <a name="create-and-test-a-web-app-with-authentication"></a><span data-ttu-id="91a83-114">Creare e testare un'app Web con autenticazione</span><span class="sxs-lookup"><span data-stu-id="91a83-114">Create and test a web app with authentication</span></span>

<span data-ttu-id="91a83-115">Eseguire i comandi seguenti per creare un'app Web con l'autenticazione di.</span><span class="sxs-lookup"><span data-stu-id="91a83-115">Run the following commands to create a web app with authentication.</span></span>

```dotnetcli
dotnet new webapp -au Individual -uld -o WebPWrecover
cd WebPWrecover
dotnet run
```

<span data-ttu-id="91a83-116">Eseguire l'app, selezionare il collegamento **Register** e registrare un utente.</span><span class="sxs-lookup"><span data-stu-id="91a83-116">Run the app, select the **Register** link, and register a user.</span></span> <span data-ttu-id="91a83-117">Una volta effettuata la registrazione, si verrà reindirizzati alla `/Identity/Account/RegisterConfirmation` pagina a contenente un collegamento per simulare la conferma della posta elettronica:</span><span class="sxs-lookup"><span data-stu-id="91a83-117">Once registered, you are redirected to the to `/Identity/Account/RegisterConfirmation` page which contains a link to simulate email confirmation:</span></span>

* <span data-ttu-id="91a83-118">Selezionare il `Click here to confirm your account` collegamento.</span><span class="sxs-lookup"><span data-stu-id="91a83-118">Select the `Click here to confirm your account` link.</span></span>
* <span data-ttu-id="91a83-119">Selezionare il collegamento di **accesso** e accedere con le stesse credenziali.</span><span class="sxs-lookup"><span data-stu-id="91a83-119">Select the **Login** link and sign-in with the same credentials.</span></span>
* <span data-ttu-id="91a83-120">Selezionare il `Hello YourEmail@provider.com!` collegamento per reindirizzare l'utente alla `/Identity/Account/Manage/PersonalData` pagina.</span><span class="sxs-lookup"><span data-stu-id="91a83-120">Select the `Hello YourEmail@provider.com!` link, which redirects you to the `/Identity/Account/Manage/PersonalData` page.</span></span>
* <span data-ttu-id="91a83-121">Selezionare la scheda **Personal Data (dati personali** ) a sinistra e quindi fare clic su **Delete (Elimina**).</span><span class="sxs-lookup"><span data-stu-id="91a83-121">Select the **Personal data** tab on the left, and then select **Delete**.</span></span>

### <a name="configure-an-email-provider"></a><span data-ttu-id="91a83-122">Configurare un provider di posta elettronica</span><span class="sxs-lookup"><span data-stu-id="91a83-122">Configure an email provider</span></span>

<span data-ttu-id="91a83-123">In questa esercitazione viene usato [SendGrid](https://sendgrid.com) per inviare messaggi di posta elettronica.</span><span class="sxs-lookup"><span data-stu-id="91a83-123">In this tutorial, [SendGrid](https://sendgrid.com) is used to send email.</span></span> <span data-ttu-id="91a83-124">Per inviare messaggi di posta elettronica sono necessari un account e una chiave di SendGrid.</span><span class="sxs-lookup"><span data-stu-id="91a83-124">You need a SendGrid account and key to send email.</span></span> <span data-ttu-id="91a83-125">È possibile usare altri provider di posta elettronica.</span><span class="sxs-lookup"><span data-stu-id="91a83-125">You can use other email providers.</span></span> <span data-ttu-id="91a83-126">È consigliabile usare SendGrid o un altro servizio di posta elettronica per inviare messaggi di posta elettronica.</span><span class="sxs-lookup"><span data-stu-id="91a83-126">We recommend you use SendGrid or another email service to send email.</span></span> <span data-ttu-id="91a83-127">SMTP è difficile da proteggere e configurare correttamente.</span><span class="sxs-lookup"><span data-stu-id="91a83-127">SMTP is difficult to secure and set up correctly.</span></span>

<span data-ttu-id="91a83-128">L'account SendGrid può richiedere l' [aggiunta di un mittente](https://sendgrid.com/docs/ui/sending-email/senders/).</span><span class="sxs-lookup"><span data-stu-id="91a83-128">The SendGrid account may require [adding a Sender](https://sendgrid.com/docs/ui/sending-email/senders/).</span></span>

<span data-ttu-id="91a83-129">Creare una classe per recuperare la chiave di posta elettronica sicura.</span><span class="sxs-lookup"><span data-stu-id="91a83-129">Create a class to fetch the secure email key.</span></span> <span data-ttu-id="91a83-130">Per questo esempio, creare *Servizi/AuthMessageSenderOptions. cs*:</span><span class="sxs-lookup"><span data-stu-id="91a83-130">For this sample, create *Services/AuthMessageSenderOptions.cs*:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/Services/AuthMessageSenderOptions.cs?name=snippet1)]

#### <a name="configure-sendgrid-user-secrets"></a><span data-ttu-id="91a83-131">Configurare i segreti utente di SendGrid</span><span class="sxs-lookup"><span data-stu-id="91a83-131">Configure SendGrid user secrets</span></span>

<span data-ttu-id="91a83-132">Impostare `SendGridUser` e `SendGridKey` con lo [strumento di gestione dei segreti](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="91a83-132">Set the `SendGridUser` and `SendGridKey` with the [secret-manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="91a83-133">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="91a83-133">For example:</span></span>

```dotnetcli
dotnet user-secrets set SendGridUser RickAndMSFT
dotnet user-secrets set SendGridKey <key>

Successfully saved SendGridUser = RickAndMSFT to the secret store.
```

<span data-ttu-id="91a83-134">In Windows, gestione Secret archivia le coppie chiave/valore in una *secrets.js* nel file nella `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` Directory.</span><span class="sxs-lookup"><span data-stu-id="91a83-134">On Windows, Secret Manager stores keys/value pairs in a *secrets.json* file in the `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` directory.</span></span>

<span data-ttu-id="91a83-135">Il contenuto del *secrets.jsnel* file non è crittografato.</span><span class="sxs-lookup"><span data-stu-id="91a83-135">The contents of the *secrets.json* file aren't encrypted.</span></span> <span data-ttu-id="91a83-136">Il markup seguente mostra il *secrets.jssu* file.</span><span class="sxs-lookup"><span data-stu-id="91a83-136">The following markup shows the *secrets.json* file.</span></span> <span data-ttu-id="91a83-137">Il `SendGridKey` valore è stato rimosso.</span><span class="sxs-lookup"><span data-stu-id="91a83-137">The `SendGridKey` value has been removed.</span></span>

```json
{
  "SendGridUser": "RickAndMSFT",
  "SendGridKey": "<key removed>"
}
```

<span data-ttu-id="91a83-138">Per ulteriori informazioni, vedere il [modello](xref:fundamentals/configuration/options) e la [configurazione](xref:fundamentals/configuration/index)delle opzioni.</span><span class="sxs-lookup"><span data-stu-id="91a83-138">For more information, see the [Options pattern](xref:fundamentals/configuration/options) and [configuration](xref:fundamentals/configuration/index).</span></span>

### <a name="install-sendgrid"></a><span data-ttu-id="91a83-139">Installare SendGrid</span><span class="sxs-lookup"><span data-stu-id="91a83-139">Install SendGrid</span></span>

<span data-ttu-id="91a83-140">Questa esercitazione illustra come aggiungere notifiche tramite posta elettronica tramite [SendGrid](https://sendgrid.com/), ma è possibile inviare messaggi di posta elettronica usando SMTP e altri meccanismi.</span><span class="sxs-lookup"><span data-stu-id="91a83-140">This tutorial shows how to add email notifications through [SendGrid](https://sendgrid.com/), but you can send email using SMTP and other mechanisms.</span></span>

<span data-ttu-id="91a83-141">Installare il `SendGrid` pacchetto NuGet:</span><span class="sxs-lookup"><span data-stu-id="91a83-141">Install the `SendGrid` NuGet package:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="91a83-142">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="91a83-142">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="91a83-143">Dalla console di gestione pacchetti immettere il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="91a83-143">From the Package Manager Console, enter the following command:</span></span>

```powershell
Install-Package SendGrid
```

# <a name="net-core-cli"></a>[<span data-ttu-id="91a83-144">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="91a83-144">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="91a83-145">Dalla console di immettere il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="91a83-145">From the console, enter the following command:</span></span>

```dotnetcli
dotnet add package SendGrid
```

---

<span data-ttu-id="91a83-146">Per una registrazione gratuita per un account SendGrid gratuito, vedere [Introduzione a SendGrid](https://sendgrid.com/free/) .</span><span class="sxs-lookup"><span data-stu-id="91a83-146">See [Get Started with SendGrid for Free](https://sendgrid.com/free/) to register for a free SendGrid account.</span></span>

### <a name="implement-iemailsender"></a><span data-ttu-id="91a83-147">Implementare IEmailSender</span><span class="sxs-lookup"><span data-stu-id="91a83-147">Implement IEmailSender</span></span>

<span data-ttu-id="91a83-148">Per implementare `IEmailSender` , creare *Services/EmailSender. cs* con codice simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="91a83-148">To Implement `IEmailSender`, create *Services/EmailSender.cs* with code similar to the following:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/Services/EmailSender.cs)]

### <a name="configure-startup-to-support-email"></a><span data-ttu-id="91a83-149">Configurare l'avvio per il supporto della posta elettronica</span><span class="sxs-lookup"><span data-stu-id="91a83-149">Configure startup to support email</span></span>

<span data-ttu-id="91a83-150">Aggiungere il codice seguente al `ConfigureServices` metodo nel file *Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="91a83-150">Add the following code to the `ConfigureServices` method in the *Startup.cs* file:</span></span>

* <span data-ttu-id="91a83-151">Aggiungere `EmailSender` come servizio temporaneo.</span><span class="sxs-lookup"><span data-stu-id="91a83-151">Add `EmailSender` as a transient service.</span></span>
* <span data-ttu-id="91a83-152">Registrare l' `AuthMessageSenderOptions` istanza di configurazione.</span><span class="sxs-lookup"><span data-stu-id="91a83-152">Register the `AuthMessageSenderOptions` configuration instance.</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/Startup.cs?name=snippet1&highlight=11-15)]

## <a name="register-confirm-email-and-reset-password"></a><span data-ttu-id="91a83-153">Registrare, confermare la posta elettronica e reimpostare la password</span><span class="sxs-lookup"><span data-stu-id="91a83-153">Register, confirm email, and reset password</span></span>

<span data-ttu-id="91a83-154">Eseguire l'app Web e testare la conferma dell'account e il flusso di recupero della password.</span><span class="sxs-lookup"><span data-stu-id="91a83-154">Run the web app, and test the account confirmation and password recovery flow.</span></span>

* <span data-ttu-id="91a83-155">Eseguire l'app e registrare un nuovo utente</span><span class="sxs-lookup"><span data-stu-id="91a83-155">Run the app and register a new user</span></span>
* <span data-ttu-id="91a83-156">Controllare la posta elettronica per il collegamento di conferma dell'account.</span><span class="sxs-lookup"><span data-stu-id="91a83-156">Check your email for the account confirmation link.</span></span> <span data-ttu-id="91a83-157">Se non si riceve il messaggio di posta elettronica, vedere [debug](#debug) della posta elettronica.</span><span class="sxs-lookup"><span data-stu-id="91a83-157">See [Debug email](#debug) if you don't get the email.</span></span>
* <span data-ttu-id="91a83-158">Fare clic sul collegamento per confermare la posta elettronica.</span><span class="sxs-lookup"><span data-stu-id="91a83-158">Click the link to confirm your email.</span></span>
* <span data-ttu-id="91a83-159">Accedere con l'indirizzo di posta elettronica e la password.</span><span class="sxs-lookup"><span data-stu-id="91a83-159">Sign in with your email and password.</span></span>
* <span data-ttu-id="91a83-160">Uscire,</span><span class="sxs-lookup"><span data-stu-id="91a83-160">Sign out.</span></span>

### <a name="test-password-reset"></a><span data-ttu-id="91a83-161">Testare la reimpostazione della password</span><span class="sxs-lookup"><span data-stu-id="91a83-161">Test password reset</span></span>

* <span data-ttu-id="91a83-162">Se è stato eseguito l'accesso, selezionare **Disconnetti**.</span><span class="sxs-lookup"><span data-stu-id="91a83-162">If you're signed in, select **Logout**.</span></span>
* <span data-ttu-id="91a83-163">Selezionare il collegamento **Accedi** e selezionare il collegamento **password dimenticata?** .</span><span class="sxs-lookup"><span data-stu-id="91a83-163">Select the **Log in** link and select the **Forgot your password?** link.</span></span>
* <span data-ttu-id="91a83-164">Immettere il messaggio di posta elettronica usato per registrare l'account.</span><span class="sxs-lookup"><span data-stu-id="91a83-164">Enter the email you used to register the account.</span></span>
* <span data-ttu-id="91a83-165">Viene inviato un messaggio di posta elettronica con un collegamento per reimpostare la password.</span><span class="sxs-lookup"><span data-stu-id="91a83-165">An email with a link to reset your password is sent.</span></span> <span data-ttu-id="91a83-166">Controllare la posta elettronica e fare clic sul collegamento per reimpostare la password.</span><span class="sxs-lookup"><span data-stu-id="91a83-166">Check your email and click the link to reset your password.</span></span> <span data-ttu-id="91a83-167">Dopo che la password è stata reimpostata, è possibile accedere con l'indirizzo di posta elettronica e la nuova password.</span><span class="sxs-lookup"><span data-stu-id="91a83-167">After your password has been successfully reset, you can sign in with your email and new password.</span></span>

## <a name="change-email-and-activity-timeout"></a><span data-ttu-id="91a83-168">Modificare il timeout dell'attività e del messaggio di posta elettronica</span><span class="sxs-lookup"><span data-stu-id="91a83-168">Change email and activity timeout</span></span>

<span data-ttu-id="91a83-169">Il timeout di inattività predefinito è di 14 giorni.</span><span class="sxs-lookup"><span data-stu-id="91a83-169">The default inactivity timeout is 14 days.</span></span> <span data-ttu-id="91a83-170">Il codice seguente imposta il timeout di inattività su 5 giorni:</span><span class="sxs-lookup"><span data-stu-id="91a83-170">The following code sets the inactivity timeout to 5 days:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupAppCookie.cs?name=snippet1)]

### <a name="change-all-data-protection-token-lifespans"></a><span data-ttu-id="91a83-171">Modifica tutte le durate dei token di protezione dati</span><span class="sxs-lookup"><span data-stu-id="91a83-171">Change all data protection token lifespans</span></span>

<span data-ttu-id="91a83-172">Il codice seguente modifica il periodo di timeout di tutti i token di protezione dati in 3 ore:</span><span class="sxs-lookup"><span data-stu-id="91a83-172">The following code changes all data protection tokens timeout period to 3 hours:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupAllTokens.cs?name=snippet1&highlight=11-12)]

<span data-ttu-id="91a83-173">I Identity token utente predefiniti (vedere [AspNetCore/src/ Identity /Extensions.core/src/TokenOptions.cs](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) ) hanno un timeout di un [giorno](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span><span class="sxs-lookup"><span data-stu-id="91a83-173">The built in Identity user tokens (see [AspNetCore/src/Identity/Extensions.Core/src/TokenOptions.cs](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) )have a [one day timeout](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span>

### <a name="change-the-email-token-lifespan"></a><span data-ttu-id="91a83-174">Modificare la durata del token di posta elettronica</span><span class="sxs-lookup"><span data-stu-id="91a83-174">Change the email token lifespan</span></span>

<span data-ttu-id="91a83-175">La durata del token predefinita [dei Identity token utente](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) è [un giorno](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span><span class="sxs-lookup"><span data-stu-id="91a83-175">The default token lifespan of [the Identity user tokens](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) is [one day](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span> <span data-ttu-id="91a83-176">Questa sezione illustra come modificare la durata del token di posta elettronica.</span><span class="sxs-lookup"><span data-stu-id="91a83-176">This section shows how to change the email token lifespan.</span></span>

<span data-ttu-id="91a83-177">Aggiungere un [DataProtectorTokenProvider \<TUser> ](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) personalizzato e <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions> :</span><span class="sxs-lookup"><span data-stu-id="91a83-177">Add a custom [DataProtectorTokenProvider\<TUser>](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) and <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions>:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/TokenProviders/CustomTokenProvider.cs?name=snippet1)]

<span data-ttu-id="91a83-178">Aggiungere il provider personalizzato al contenitore del servizio:</span><span class="sxs-lookup"><span data-stu-id="91a83-178">Add the custom provider to the service container:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupEmail.cs?name=snippet1&highlight=10-16)]

### <a name="resend-email-confirmation"></a><span data-ttu-id="91a83-179">Invia di nuovo la conferma tramite posta elettronica</span><span class="sxs-lookup"><span data-stu-id="91a83-179">Resend email confirmation</span></span>

<span data-ttu-id="91a83-180">Vedere [il problema in GitHub](https://github.com/dotnet/AspNetCore/issues/5410).</span><span class="sxs-lookup"><span data-stu-id="91a83-180">See [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/5410).</span></span>

<a name="debug"></a>

### <a name="debug-email"></a><span data-ttu-id="91a83-181">Posta elettronica di debug</span><span class="sxs-lookup"><span data-stu-id="91a83-181">Debug email</span></span>

<span data-ttu-id="91a83-182">Se non è possibile ottenere la posta elettronica:</span><span class="sxs-lookup"><span data-stu-id="91a83-182">If you can't get email working:</span></span>

* <span data-ttu-id="91a83-183">Impostare un punto di interruzione in `EmailSender.Execute` per verificare che `SendGridClient.SendEmailAsync` venga chiamato.</span><span class="sxs-lookup"><span data-stu-id="91a83-183">Set a breakpoint in `EmailSender.Execute` to verify `SendGridClient.SendEmailAsync` is called.</span></span>
* <span data-ttu-id="91a83-184">Creare un' [app console per inviare messaggi di posta elettronica](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) usando codice simile a `EmailSender.Execute` .</span><span class="sxs-lookup"><span data-stu-id="91a83-184">Create a [console app to send email](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) using similar code to `EmailSender.Execute`.</span></span>
* <span data-ttu-id="91a83-185">Esaminare la pagina [attività posta elettronica](https://sendgrid.com/docs/User_Guide/email_activity.html) .</span><span class="sxs-lookup"><span data-stu-id="91a83-185">Review the [Email Activity](https://sendgrid.com/docs/User_Guide/email_activity.html) page.</span></span>
* <span data-ttu-id="91a83-186">Controllare la cartella della posta indesiderata.</span><span class="sxs-lookup"><span data-stu-id="91a83-186">Check your spam folder.</span></span>
* <span data-ttu-id="91a83-187">Prova un altro alias di posta elettronica in un provider di posta elettronica diverso (Microsoft, Yahoo, Gmail e così via)</span><span class="sxs-lookup"><span data-stu-id="91a83-187">Try another email alias on a different email provider (Microsoft, Yahoo, Gmail, etc.)</span></span>
* <span data-ttu-id="91a83-188">Provare a inviare a diversi account di posta elettronica.</span><span class="sxs-lookup"><span data-stu-id="91a83-188">Try sending to different email accounts.</span></span>

<span data-ttu-id="91a83-189">**Una procedura di sicurezza consigliata** consiste nel **non** usare i segreti di produzione per test e sviluppo.</span><span class="sxs-lookup"><span data-stu-id="91a83-189">**A security best practice** is to **not** use production secrets in test and development.</span></span> <span data-ttu-id="91a83-190">Se si pubblica l'app in Azure, impostare i segreti SendGrid come impostazioni dell'applicazione nel portale dell'app Web di Azure.</span><span class="sxs-lookup"><span data-stu-id="91a83-190">If you publish the app to Azure, set the SendGrid secrets as application settings in the Azure Web App portal.</span></span> <span data-ttu-id="91a83-191">Il sistema di configurazione è configurato per la lettura delle chiavi dalle variabili di ambiente.</span><span class="sxs-lookup"><span data-stu-id="91a83-191">The configuration system is set up to read keys from environment variables.</span></span>

## <a name="combine-social-and-local-login-accounts"></a><span data-ttu-id="91a83-192">Combinare account di accesso di social networking e locali</span><span class="sxs-lookup"><span data-stu-id="91a83-192">Combine social and local login accounts</span></span>

<span data-ttu-id="91a83-193">Per completare questa sezione, è necessario innanzitutto abilitare un provider di autenticazione esterno.</span><span class="sxs-lookup"><span data-stu-id="91a83-193">To complete this section, you must first enable an external authentication provider.</span></span> <span data-ttu-id="91a83-194">Vedere [l'autenticazione di Facebook, Google e del provider esterno](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="91a83-194">See [Facebook, Google, and external provider authentication](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="91a83-195">È possibile combinare account locali e di social networking facendo clic sul collegamento di posta elettronica.</span><span class="sxs-lookup"><span data-stu-id="91a83-195">You can combine local and social accounts by clicking on your email link.</span></span> <span data-ttu-id="91a83-196">Nella sequenza seguente " RickAndMSFT@gmail.com " viene creato per la prima volta come account di accesso locale; tuttavia, è possibile creare prima l'account come account di accesso di social networking, quindi aggiungere un account di accesso locale.</span><span class="sxs-lookup"><span data-stu-id="91a83-196">In the following sequence, "RickAndMSFT@gmail.com" is first created as a local login; however, you can create the account as a social login first, then add a local login.</span></span>

![Applicazione Web: RickAndMSFT@gmail.com utente autenticato](accconfirm/_static/rick.png)

<span data-ttu-id="91a83-198">Fare clic sul collegamento **Gestisci** .</span><span class="sxs-lookup"><span data-stu-id="91a83-198">Click on the **Manage** link.</span></span> <span data-ttu-id="91a83-199">Si notino gli account di accesso di social network (0) esterni associati a questo account.</span><span class="sxs-lookup"><span data-stu-id="91a83-199">Note the 0 external (social logins) associated with this account.</span></span>

![Gestisci visualizzazione](accconfirm/_static/manage.png)

<span data-ttu-id="91a83-201">Fare clic sul collegamento a un altro servizio di accesso e accettare le richieste dell'app.</span><span class="sxs-lookup"><span data-stu-id="91a83-201">Click the link to another login service and accept the app requests.</span></span> <span data-ttu-id="91a83-202">Nell'immagine seguente, Facebook è il provider di autenticazione esterno:</span><span class="sxs-lookup"><span data-stu-id="91a83-202">In the following image, Facebook is the external authentication provider:</span></span>

![Gestire gli account di accesso esterni visualizzare l'elenco di Facebook](accconfirm/_static/fb.png)

<span data-ttu-id="91a83-204">I due account sono stati combinati.</span><span class="sxs-lookup"><span data-stu-id="91a83-204">The two accounts have been combined.</span></span> <span data-ttu-id="91a83-205">È possibile accedere con uno dei due account.</span><span class="sxs-lookup"><span data-stu-id="91a83-205">You are able to sign in with either account.</span></span> <span data-ttu-id="91a83-206">È possibile che gli utenti aggiungano gli account locali nel caso in cui il servizio di autenticazione per l'accesso di social networking sia inattivo o più probabilmente abbiano perso l'accesso al proprio account di social networking.</span><span class="sxs-lookup"><span data-stu-id="91a83-206">You might want your users to add local accounts in case their social login authentication service is down, or more likely they've lost access to their social account.</span></span>

## <a name="enable-account-confirmation-after-a-site-has-users"></a><span data-ttu-id="91a83-207">Abilitare la conferma dell'account dopo che un sito ha utenti</span><span class="sxs-lookup"><span data-stu-id="91a83-207">Enable account confirmation after a site has users</span></span>

<span data-ttu-id="91a83-208">L'abilitazione della conferma dell'account in un sito con utenti blocca tutti gli utenti esistenti.</span><span class="sxs-lookup"><span data-stu-id="91a83-208">Enabling account confirmation on a site with users locks out all the existing users.</span></span> <span data-ttu-id="91a83-209">Gli utenti esistenti sono bloccati perché i relativi account non sono confermati.</span><span class="sxs-lookup"><span data-stu-id="91a83-209">Existing users are locked out because their accounts aren't confirmed.</span></span> <span data-ttu-id="91a83-210">Per aggirare il blocco utente esistente, utilizzare uno degli approcci seguenti:</span><span class="sxs-lookup"><span data-stu-id="91a83-210">To work around existing user lockout, use one of the following approaches:</span></span>

* <span data-ttu-id="91a83-211">Aggiornare il database per contrassegnare tutti gli utenti esistenti come confermati.</span><span class="sxs-lookup"><span data-stu-id="91a83-211">Update the database to mark all existing users as being confirmed.</span></span>
* <span data-ttu-id="91a83-212">Verificare gli utenti esistenti.</span><span class="sxs-lookup"><span data-stu-id="91a83-212">Confirm existing users.</span></span> <span data-ttu-id="91a83-213">Ad esempio, invio in batch di messaggi di posta elettronica con collegamenti di conferma.</span><span class="sxs-lookup"><span data-stu-id="91a83-213">For example, batch-send emails with confirmation links.</span></span>

::: moniker-end

::: moniker range="> aspnetcore-2.0 < aspnetcore-3.0"

## <a name="prerequisites"></a><span data-ttu-id="91a83-214">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="91a83-214">Prerequisites</span></span>

[<span data-ttu-id="91a83-215">.NET Core 2,2 SDK o versione successiva</span><span class="sxs-lookup"><span data-stu-id="91a83-215">.NET Core 2.2 SDK or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)

## <a name="create-a-web--app-and-scaffold-identity"></a><span data-ttu-id="91a83-216">Creare un'app Web e un patiboloIdentity</span><span class="sxs-lookup"><span data-stu-id="91a83-216">Create a web  app and scaffold Identity</span></span>

<span data-ttu-id="91a83-217">Eseguire i comandi seguenti per creare un'app Web con l'autenticazione di.</span><span class="sxs-lookup"><span data-stu-id="91a83-217">Run the following commands to create a web app with authentication.</span></span>

```dotnetcli
dotnet new webapp -au Individual -uld -o WebPWrecover
cd WebPWrecover
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator identity -dc WebPWrecover.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout;Account.ConfirmEmail"
dotnet ef database drop -f
dotnet ef database update
dotnet run

```

## <a name="test-new-user-registration"></a><span data-ttu-id="91a83-218">Testare la registrazione di un nuovo utente</span><span class="sxs-lookup"><span data-stu-id="91a83-218">Test new user registration</span></span>

<span data-ttu-id="91a83-219">Eseguire l'app, selezionare il collegamento **Register** e registrare un utente.</span><span class="sxs-lookup"><span data-stu-id="91a83-219">Run the app, select the **Register** link, and register a user.</span></span> <span data-ttu-id="91a83-220">A questo punto, l'unica convalida sul messaggio di posta elettronica è con l' [`[EmailAddress]`](/dotnet/api/system.componentmodel.dataannotations.emailaddressattribute) attributo.</span><span class="sxs-lookup"><span data-stu-id="91a83-220">At this point, the only validation on the email is with the [`[EmailAddress]`](/dotnet/api/system.componentmodel.dataannotations.emailaddressattribute) attribute.</span></span> <span data-ttu-id="91a83-221">Dopo aver inviato la registrazione, si è connessi all'app.</span><span class="sxs-lookup"><span data-stu-id="91a83-221">After submitting the registration, you are logged into the app.</span></span> <span data-ttu-id="91a83-222">Più avanti nell'esercitazione, il codice viene aggiornato in modo che i nuovi utenti non possano accedere fino a quando non vengono convalidati i messaggi di posta elettronica.</span><span class="sxs-lookup"><span data-stu-id="91a83-222">Later in the tutorial, the code is updated so new users can't sign in until their email is validated.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<span data-ttu-id="91a83-223">Si noti che il `EmailConfirmed` campo della tabella è `False` .</span><span class="sxs-lookup"><span data-stu-id="91a83-223">Note the table's `EmailConfirmed` field is `False`.</span></span>

<span data-ttu-id="91a83-224">Potrebbe essere necessario usare nuovamente questo messaggio di posta elettronica nel passaggio successivo quando l'app invia un messaggio di posta elettronica di conferma.</span><span class="sxs-lookup"><span data-stu-id="91a83-224">You might want to use this email again in the next step when the app sends a confirmation email.</span></span> <span data-ttu-id="91a83-225">Fare clic con il pulsante destro del mouse sulla riga e scegliere **Elimina**.</span><span class="sxs-lookup"><span data-stu-id="91a83-225">Right-click on the row and select **Delete**.</span></span> <span data-ttu-id="91a83-226">L'eliminazione dell'alias di posta elettronica rende più semplice nei passaggi seguenti.</span><span class="sxs-lookup"><span data-stu-id="91a83-226">Deleting the email alias makes it easier in the following steps.</span></span>

<a name="prevent-login-at-registration"></a>

## <a name="require-email-confirmation"></a><span data-ttu-id="91a83-227">Richiedi conferma posta elettronica</span><span class="sxs-lookup"><span data-stu-id="91a83-227">Require email confirmation</span></span>

<span data-ttu-id="91a83-228">È consigliabile confermare il messaggio di posta elettronica di una nuova registrazione utente.</span><span class="sxs-lookup"><span data-stu-id="91a83-228">It's a best practice to confirm the email of a new user registration.</span></span> <span data-ttu-id="91a83-229">La conferma tramite posta elettronica consente di verificare che non stiano eseguendo la rappresentazione di un altro utente, ovvero che non sono stati registrati con il messaggio di posta elettronica di un altro utente.</span><span class="sxs-lookup"><span data-stu-id="91a83-229">Email confirmation helps to verify they're not impersonating someone else (that is, they haven't registered with someone else's email).</span></span> <span data-ttu-id="91a83-230">Si supponga di avere un forum di discussione e di voler impedire che " yli@example.com " si registri come " nolivetto@contoso.com ".</span><span class="sxs-lookup"><span data-stu-id="91a83-230">Suppose you had a discussion forum, and you wanted to prevent "yli@example.com" from registering as "nolivetto@contoso.com".</span></span> <span data-ttu-id="91a83-231">Senza la conferma della posta elettronica, " nolivetto@contoso.com " potrebbe ricevere un messaggio di posta elettronica indesiderato dall'app.</span><span class="sxs-lookup"><span data-stu-id="91a83-231">Without email confirmation, "nolivetto@contoso.com" could receive unwanted email from your app.</span></span> <span data-ttu-id="91a83-232">Si supponga che l'utente sia stato accidentalmente registrato come " ylo@example.com " e non abbia notato l'errore di ortografia di "Yli".</span><span class="sxs-lookup"><span data-stu-id="91a83-232">Suppose the user accidentally registered as "ylo@example.com" and hadn't noticed the misspelling of "yli".</span></span> <span data-ttu-id="91a83-233">Non saranno in grado di usare il ripristino della password perché l'app non ha la posta elettronica corretta.</span><span class="sxs-lookup"><span data-stu-id="91a83-233">They wouldn't be able to use password recovery because the app doesn't have their correct email.</span></span> <span data-ttu-id="91a83-234">La conferma tramite posta elettronica garantisce una protezione limitata dai bot.</span><span class="sxs-lookup"><span data-stu-id="91a83-234">Email confirmation provides limited protection from bots.</span></span> <span data-ttu-id="91a83-235">La conferma tramite posta elettronica non fornisce protezione da utenti malintenzionati con molti account di posta elettronica.</span><span class="sxs-lookup"><span data-stu-id="91a83-235">Email confirmation doesn't provide protection from malicious users with many email accounts.</span></span>

<span data-ttu-id="91a83-236">In genere si vuole impedire ai nuovi utenti di inviare dati al sito Web prima di avere un messaggio di posta elettronica confermato.</span><span class="sxs-lookup"><span data-stu-id="91a83-236">You generally want to prevent new users from posting any data to your web site before they have a confirmed email.</span></span>

<span data-ttu-id="91a83-237">Aggiornamento `Startup.ConfigureServices` per richiedere un messaggio di posta elettronica confermato:</span><span class="sxs-lookup"><span data-stu-id="91a83-237">Update `Startup.ConfigureServices`  to require a confirmed email:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Startup.cs?name=snippet1&highlight=8-11)]

<span data-ttu-id="91a83-238">`config.SignIn.RequireConfirmedEmail = true;`impedisce agli utenti registrati di accedere fino a quando non viene confermata la posta elettronica.</span><span class="sxs-lookup"><span data-stu-id="91a83-238">`config.SignIn.RequireConfirmedEmail = true;` prevents registered users from logging in until their email is confirmed.</span></span>

### <a name="configure-email-provider"></a><span data-ttu-id="91a83-239">Configurare il provider di posta elettronica</span><span class="sxs-lookup"><span data-stu-id="91a83-239">Configure email provider</span></span>

<span data-ttu-id="91a83-240">In questa esercitazione viene usato [SendGrid](https://sendgrid.com) per inviare messaggi di posta elettronica.</span><span class="sxs-lookup"><span data-stu-id="91a83-240">In this tutorial, [SendGrid](https://sendgrid.com) is used to send email.</span></span> <span data-ttu-id="91a83-241">Per inviare messaggi di posta elettronica sono necessari un account e una chiave di SendGrid.</span><span class="sxs-lookup"><span data-stu-id="91a83-241">You need a SendGrid account and key to send email.</span></span> <span data-ttu-id="91a83-242">È possibile usare altri provider di posta elettronica.</span><span class="sxs-lookup"><span data-stu-id="91a83-242">You can use other email providers.</span></span> <span data-ttu-id="91a83-243">ASP.NET Core 2. x include `System.Net.Mail` , che consente di inviare messaggi di posta elettronica dall'app.</span><span class="sxs-lookup"><span data-stu-id="91a83-243">ASP.NET Core 2.x includes `System.Net.Mail`, which allows you to send email from your app.</span></span> <span data-ttu-id="91a83-244">È consigliabile usare SendGrid o un altro servizio di posta elettronica per inviare messaggi di posta elettronica.</span><span class="sxs-lookup"><span data-stu-id="91a83-244">We recommend you use SendGrid or another email service to send email.</span></span> <span data-ttu-id="91a83-245">SMTP è difficile da proteggere e configurare correttamente.</span><span class="sxs-lookup"><span data-stu-id="91a83-245">SMTP is difficult to secure and set up correctly.</span></span>

<span data-ttu-id="91a83-246">Creare una classe per recuperare la chiave di posta elettronica sicura.</span><span class="sxs-lookup"><span data-stu-id="91a83-246">Create a class to fetch the secure email key.</span></span> <span data-ttu-id="91a83-247">Per questo esempio, creare *Servizi/AuthMessageSenderOptions. cs*:</span><span class="sxs-lookup"><span data-stu-id="91a83-247">For this sample, create *Services/AuthMessageSenderOptions.cs*:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Services/AuthMessageSenderOptions.cs?name=snippet1)]

#### <a name="configure-sendgrid-user-secrets"></a><span data-ttu-id="91a83-248">Configurare i segreti utente di SendGrid</span><span class="sxs-lookup"><span data-stu-id="91a83-248">Configure SendGrid user secrets</span></span>

<span data-ttu-id="91a83-249">Impostare `SendGridUser` e `SendGridKey` con lo [strumento di gestione dei segreti](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="91a83-249">Set the `SendGridUser` and `SendGridKey` with the [secret-manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="91a83-250">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="91a83-250">For example:</span></span>

```console
C:/WebAppl>dotnet user-secrets set SendGridUser RickAndMSFT
info: Successfully saved SendGridUser = RickAndMSFT to the secret store.
```

<span data-ttu-id="91a83-251">In Windows, gestione Secret archivia le coppie chiave/valore in una *secrets.js* nel file nella `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` Directory.</span><span class="sxs-lookup"><span data-stu-id="91a83-251">On Windows, Secret Manager stores keys/value pairs in a *secrets.json* file in the `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` directory.</span></span>

<span data-ttu-id="91a83-252">Il contenuto del *secrets.jsnel* file non è crittografato.</span><span class="sxs-lookup"><span data-stu-id="91a83-252">The contents of the *secrets.json* file aren't encrypted.</span></span> <span data-ttu-id="91a83-253">Il markup seguente mostra il *secrets.jssu* file.</span><span class="sxs-lookup"><span data-stu-id="91a83-253">The following markup shows the *secrets.json* file.</span></span> <span data-ttu-id="91a83-254">Il `SendGridKey` valore è stato rimosso.</span><span class="sxs-lookup"><span data-stu-id="91a83-254">The `SendGridKey` value has been removed.</span></span>

```json
{
  "SendGridUser": "RickAndMSFT",
  "SendGridKey": "<key removed>"
}
```

<span data-ttu-id="91a83-255">Per ulteriori informazioni, vedere il [modello](xref:fundamentals/configuration/options) e la [configurazione](xref:fundamentals/configuration/index)delle opzioni.</span><span class="sxs-lookup"><span data-stu-id="91a83-255">For more information, see the [Options pattern](xref:fundamentals/configuration/options) and [configuration](xref:fundamentals/configuration/index).</span></span>

### <a name="install-sendgrid"></a><span data-ttu-id="91a83-256">Installare SendGrid</span><span class="sxs-lookup"><span data-stu-id="91a83-256">Install SendGrid</span></span>

<span data-ttu-id="91a83-257">Questa esercitazione illustra come aggiungere notifiche tramite posta elettronica tramite [SendGrid](https://sendgrid.com/), ma è possibile inviare messaggi di posta elettronica usando SMTP e altri meccanismi.</span><span class="sxs-lookup"><span data-stu-id="91a83-257">This tutorial shows how to add email notifications through [SendGrid](https://sendgrid.com/), but you can send email using SMTP and other mechanisms.</span></span>

<span data-ttu-id="91a83-258">Installare il `SendGrid` pacchetto NuGet:</span><span class="sxs-lookup"><span data-stu-id="91a83-258">Install the `SendGrid` NuGet package:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="91a83-259">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="91a83-259">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="91a83-260">Dalla console di gestione pacchetti immettere il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="91a83-260">From the Package Manager Console, enter the following command:</span></span>

```powershell
Install-Package SendGrid
```

# <a name="net-core-cli"></a>[<span data-ttu-id="91a83-261">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="91a83-261">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="91a83-262">Dalla console di immettere il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="91a83-262">From the console, enter the following command:</span></span>

```dotnetcli
dotnet add package SendGrid
```

---

<span data-ttu-id="91a83-263">Per una registrazione gratuita per un account SendGrid gratuito, vedere [Introduzione a SendGrid](https://sendgrid.com/free/) .</span><span class="sxs-lookup"><span data-stu-id="91a83-263">See [Get Started with SendGrid for Free](https://sendgrid.com/free/) to register for a free SendGrid account.</span></span>

### <a name="implement-iemailsender"></a><span data-ttu-id="91a83-264">Implementare IEmailSender</span><span class="sxs-lookup"><span data-stu-id="91a83-264">Implement IEmailSender</span></span>

<span data-ttu-id="91a83-265">Per implementare `IEmailSender` , creare *Services/EmailSender. cs* con codice simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="91a83-265">To Implement `IEmailSender`, create *Services/EmailSender.cs* with code similar to the following:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Services/EmailSender.cs)]

### <a name="configure-startup-to-support-email"></a><span data-ttu-id="91a83-266">Configurare l'avvio per il supporto della posta elettronica</span><span class="sxs-lookup"><span data-stu-id="91a83-266">Configure startup to support email</span></span>

<span data-ttu-id="91a83-267">Aggiungere il codice seguente al `ConfigureServices` metodo nel file *Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="91a83-267">Add the following code to the `ConfigureServices` method in the *Startup.cs* file:</span></span>

* <span data-ttu-id="91a83-268">Aggiungere `EmailSender` come servizio temporaneo.</span><span class="sxs-lookup"><span data-stu-id="91a83-268">Add `EmailSender` as a transient service.</span></span>
* <span data-ttu-id="91a83-269">Registrare l' `AuthMessageSenderOptions` istanza di configurazione.</span><span class="sxs-lookup"><span data-stu-id="91a83-269">Register the `AuthMessageSenderOptions` configuration instance.</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Startup.cs?name=snippet1&highlight=15-99)]

## <a name="enable-account-confirmation-and-password-recovery"></a><span data-ttu-id="91a83-270">Abilitare la conferma dell'account e il recupero della password</span><span class="sxs-lookup"><span data-stu-id="91a83-270">Enable account confirmation and password recovery</span></span>

<span data-ttu-id="91a83-271">Il modello include il codice per la conferma dell'account e il recupero della password.</span><span class="sxs-lookup"><span data-stu-id="91a83-271">The template has the code for account confirmation and password recovery.</span></span> <span data-ttu-id="91a83-272">Trovare il `OnPostAsync` metodo in *aree/ Identity /pages/account/Register.cshtml.cs*.</span><span class="sxs-lookup"><span data-stu-id="91a83-272">Find the `OnPostAsync` method in *Areas/Identity/Pages/Account/Register.cshtml.cs*.</span></span>

<span data-ttu-id="91a83-273">Impedire l'accesso automatico degli utenti appena registrati impostando come commento la riga seguente:</span><span class="sxs-lookup"><span data-stu-id="91a83-273">Prevent newly registered users from being automatically signed in by commenting out the following line:</span></span>

```csharp
await _signInManager.SignInAsync(user, isPersistent: false);
```

<span data-ttu-id="91a83-274">Il metodo completo viene visualizzato con la riga modificata evidenziata:</span><span class="sxs-lookup"><span data-stu-id="91a83-274">The complete method is shown with the changed line highlighted:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Areas/Identity/Pages/Account/Register.cshtml.cs?highlight=22&name=snippet_Register)]

## <a name="register-confirm-email-and-reset-password"></a><span data-ttu-id="91a83-275">Registrare, confermare la posta elettronica e reimpostare la password</span><span class="sxs-lookup"><span data-stu-id="91a83-275">Register, confirm email, and reset password</span></span>

<span data-ttu-id="91a83-276">Eseguire l'app Web e testare la conferma dell'account e il flusso di recupero della password.</span><span class="sxs-lookup"><span data-stu-id="91a83-276">Run the web app, and test the account confirmation and password recovery flow.</span></span>

* <span data-ttu-id="91a83-277">Eseguire l'app e registrare un nuovo utente</span><span class="sxs-lookup"><span data-stu-id="91a83-277">Run the app and register a new user</span></span>
* <span data-ttu-id="91a83-278">Controllare la posta elettronica per il collegamento di conferma dell'account.</span><span class="sxs-lookup"><span data-stu-id="91a83-278">Check your email for the account confirmation link.</span></span> <span data-ttu-id="91a83-279">Se non si riceve il messaggio di posta elettronica, vedere [debug](#debug) della posta elettronica.</span><span class="sxs-lookup"><span data-stu-id="91a83-279">See [Debug email](#debug) if you don't get the email.</span></span>
* <span data-ttu-id="91a83-280">Fare clic sul collegamento per confermare la posta elettronica.</span><span class="sxs-lookup"><span data-stu-id="91a83-280">Click the link to confirm your email.</span></span>
* <span data-ttu-id="91a83-281">Accedere con l'indirizzo di posta elettronica e la password.</span><span class="sxs-lookup"><span data-stu-id="91a83-281">Sign in with your email and password.</span></span>
* <span data-ttu-id="91a83-282">Uscire,</span><span class="sxs-lookup"><span data-stu-id="91a83-282">Sign out.</span></span>

### <a name="view-the-manage-page"></a><span data-ttu-id="91a83-283">Visualizzare la pagina Gestisci</span><span class="sxs-lookup"><span data-stu-id="91a83-283">View the manage page</span></span>

<span data-ttu-id="91a83-284">Selezionare il nome utente nel browser: ![ finestra del browser con nome utente](accconfirm/_static/un.png)</span><span class="sxs-lookup"><span data-stu-id="91a83-284">Select your user name in the browser: ![browser window with user name](accconfirm/_static/un.png)</span></span>

<span data-ttu-id="91a83-285">La pagina Gestisci viene visualizzata con la scheda **profilo** selezionata.</span><span class="sxs-lookup"><span data-stu-id="91a83-285">The manage page is displayed with the **Profile** tab selected.</span></span> <span data-ttu-id="91a83-286">Viene visualizzata una casella di controllo che indica che il messaggio di **posta elettronica è** stato confermato.</span><span class="sxs-lookup"><span data-stu-id="91a83-286">The **Email** shows a check box indicating the email has been confirmed.</span></span>

### <a name="test-password-reset"></a><span data-ttu-id="91a83-287">Testare la reimpostazione della password</span><span class="sxs-lookup"><span data-stu-id="91a83-287">Test password reset</span></span>

* <span data-ttu-id="91a83-288">Se è stato eseguito l'accesso, selezionare **Disconnetti**.</span><span class="sxs-lookup"><span data-stu-id="91a83-288">If you're signed in, select **Logout**.</span></span>
* <span data-ttu-id="91a83-289">Selezionare il collegamento **Accedi** e selezionare il collegamento **password dimenticata?** .</span><span class="sxs-lookup"><span data-stu-id="91a83-289">Select the **Log in** link and select the **Forgot your password?** link.</span></span>
* <span data-ttu-id="91a83-290">Immettere il messaggio di posta elettronica usato per registrare l'account.</span><span class="sxs-lookup"><span data-stu-id="91a83-290">Enter the email you used to register the account.</span></span>
* <span data-ttu-id="91a83-291">Viene inviato un messaggio di posta elettronica con un collegamento per reimpostare la password.</span><span class="sxs-lookup"><span data-stu-id="91a83-291">An email with a link to reset your password is sent.</span></span> <span data-ttu-id="91a83-292">Controllare la posta elettronica e fare clic sul collegamento per reimpostare la password.</span><span class="sxs-lookup"><span data-stu-id="91a83-292">Check your email and click the link to reset your password.</span></span> <span data-ttu-id="91a83-293">Dopo che la password è stata reimpostata, è possibile accedere con l'indirizzo di posta elettronica e la nuova password.</span><span class="sxs-lookup"><span data-stu-id="91a83-293">After your password has been successfully reset, you can sign in with your email and new password.</span></span>

## <a name="change-email-and-activity-timeout"></a><span data-ttu-id="91a83-294">Modificare il timeout dell'attività e del messaggio di posta elettronica</span><span class="sxs-lookup"><span data-stu-id="91a83-294">Change email and activity timeout</span></span>

<span data-ttu-id="91a83-295">Il timeout di inattività predefinito è di 14 giorni.</span><span class="sxs-lookup"><span data-stu-id="91a83-295">The default inactivity timeout is 14 days.</span></span> <span data-ttu-id="91a83-296">Il codice seguente imposta il timeout di inattività su 5 giorni:</span><span class="sxs-lookup"><span data-stu-id="91a83-296">The following code sets the inactivity timeout to 5 days:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupAppCookie.cs?name=snippet1)]

### <a name="change-all-data-protection-token-lifespans"></a><span data-ttu-id="91a83-297">Modifica tutte le durate dei token di protezione dati</span><span class="sxs-lookup"><span data-stu-id="91a83-297">Change all data protection token lifespans</span></span>

<span data-ttu-id="91a83-298">Il codice seguente modifica il periodo di timeout di tutti i token di protezione dati in 3 ore:</span><span class="sxs-lookup"><span data-stu-id="91a83-298">The following code changes all data protection tokens timeout period to 3 hours:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupAllTokens.cs?name=snippet1&highlight=15-16)]

<span data-ttu-id="91a83-299">I Identity token utente predefiniti (vedere [AspNetCore/src/ Identity /Extensions.core/src/TokenOptions.cs](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) ) hanno un timeout di un [giorno](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span><span class="sxs-lookup"><span data-stu-id="91a83-299">The built in Identity user tokens (see [AspNetCore/src/Identity/Extensions.Core/src/TokenOptions.cs](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) )have a [one day timeout](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span>

### <a name="change-the-email-token-lifespan"></a><span data-ttu-id="91a83-300">Modificare la durata del token di posta elettronica</span><span class="sxs-lookup"><span data-stu-id="91a83-300">Change the email token lifespan</span></span>

<span data-ttu-id="91a83-301">La durata del token predefinita [dei Identity token utente](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) è [un giorno](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span><span class="sxs-lookup"><span data-stu-id="91a83-301">The default token lifespan of [the Identity user tokens](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) is [one day](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span> <span data-ttu-id="91a83-302">Questa sezione illustra come modificare la durata del token di posta elettronica.</span><span class="sxs-lookup"><span data-stu-id="91a83-302">This section shows how to change the email token lifespan.</span></span>

<span data-ttu-id="91a83-303">Aggiungere un [DataProtectorTokenProvider \<TUser> ](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) personalizzato e <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions> :</span><span class="sxs-lookup"><span data-stu-id="91a83-303">Add a custom [DataProtectorTokenProvider\<TUser>](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) and <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions>:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/TokenProviders/CustomTokenProvider.cs?name=snippet1)]

<span data-ttu-id="91a83-304">Aggiungere il provider personalizzato al contenitore del servizio:</span><span class="sxs-lookup"><span data-stu-id="91a83-304">Add the custom provider to the service container:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupEmail.cs?name=snippet1&highlight=10-13,18)]

### <a name="resend-email-confirmation"></a><span data-ttu-id="91a83-305">Invia di nuovo la conferma tramite posta elettronica</span><span class="sxs-lookup"><span data-stu-id="91a83-305">Resend email confirmation</span></span>

<span data-ttu-id="91a83-306">Vedere [il problema in GitHub](https://github.com/dotnet/AspNetCore/issues/5410).</span><span class="sxs-lookup"><span data-stu-id="91a83-306">See [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/5410).</span></span>

<a name="debug"></a>

### <a name="debug-email"></a><span data-ttu-id="91a83-307">Posta elettronica di debug</span><span class="sxs-lookup"><span data-stu-id="91a83-307">Debug email</span></span>

<span data-ttu-id="91a83-308">Se non è possibile ottenere la posta elettronica:</span><span class="sxs-lookup"><span data-stu-id="91a83-308">If you can't get email working:</span></span>

* <span data-ttu-id="91a83-309">Impostare un punto di interruzione in `EmailSender.Execute` per verificare che `SendGridClient.SendEmailAsync` venga chiamato.</span><span class="sxs-lookup"><span data-stu-id="91a83-309">Set a breakpoint in `EmailSender.Execute` to verify `SendGridClient.SendEmailAsync` is called.</span></span>
* <span data-ttu-id="91a83-310">Creare un' [app console per inviare messaggi di posta elettronica](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) usando codice simile a `EmailSender.Execute` .</span><span class="sxs-lookup"><span data-stu-id="91a83-310">Create a [console app to send email](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) using similar code to `EmailSender.Execute`.</span></span>
* <span data-ttu-id="91a83-311">Esaminare la pagina [attività posta elettronica](https://sendgrid.com/docs/User_Guide/email_activity.html) .</span><span class="sxs-lookup"><span data-stu-id="91a83-311">Review the [Email Activity](https://sendgrid.com/docs/User_Guide/email_activity.html) page.</span></span>
* <span data-ttu-id="91a83-312">Controllare la cartella della posta indesiderata.</span><span class="sxs-lookup"><span data-stu-id="91a83-312">Check your spam folder.</span></span>
* <span data-ttu-id="91a83-313">Prova un altro alias di posta elettronica in un provider di posta elettronica diverso (Microsoft, Yahoo, Gmail e così via)</span><span class="sxs-lookup"><span data-stu-id="91a83-313">Try another email alias on a different email provider (Microsoft, Yahoo, Gmail, etc.)</span></span>
* <span data-ttu-id="91a83-314">Provare a inviare a diversi account di posta elettronica.</span><span class="sxs-lookup"><span data-stu-id="91a83-314">Try sending to different email accounts.</span></span>

<span data-ttu-id="91a83-315">**Una procedura di sicurezza consigliata** consiste nel **non** usare i segreti di produzione per test e sviluppo.</span><span class="sxs-lookup"><span data-stu-id="91a83-315">**A security best practice** is to **not** use production secrets in test and development.</span></span> <span data-ttu-id="91a83-316">Se si pubblica l'app in Azure, è possibile impostare SendGrid Secrets come impostazioni dell'applicazione nel portale dell'app Web di Azure.</span><span class="sxs-lookup"><span data-stu-id="91a83-316">If you publish the app to Azure, you can set the SendGrid secrets as application settings in the Azure Web App portal.</span></span> <span data-ttu-id="91a83-317">Il sistema di configurazione è configurato per la lettura delle chiavi dalle variabili di ambiente.</span><span class="sxs-lookup"><span data-stu-id="91a83-317">The configuration system is set up to read keys from environment variables.</span></span>

## <a name="combine-social-and-local-login-accounts"></a><span data-ttu-id="91a83-318">Combinare account di accesso di social networking e locali</span><span class="sxs-lookup"><span data-stu-id="91a83-318">Combine social and local login accounts</span></span>

<span data-ttu-id="91a83-319">Per completare questa sezione, è necessario innanzitutto abilitare un provider di autenticazione esterno.</span><span class="sxs-lookup"><span data-stu-id="91a83-319">To complete this section, you must first enable an external authentication provider.</span></span> <span data-ttu-id="91a83-320">Vedere [l'autenticazione di Facebook, Google e del provider esterno](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="91a83-320">See [Facebook, Google, and external provider authentication](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="91a83-321">È possibile combinare account locali e di social networking facendo clic sul collegamento di posta elettronica.</span><span class="sxs-lookup"><span data-stu-id="91a83-321">You can combine local and social accounts by clicking on your email link.</span></span> <span data-ttu-id="91a83-322">Nella sequenza seguente " RickAndMSFT@gmail.com " viene creato per la prima volta come account di accesso locale; tuttavia, è possibile creare prima l'account come account di accesso di social networking, quindi aggiungere un account di accesso locale.</span><span class="sxs-lookup"><span data-stu-id="91a83-322">In the following sequence, "RickAndMSFT@gmail.com" is first created as a local login; however, you can create the account as a social login first, then add a local login.</span></span>

![Applicazione Web: RickAndMSFT@gmail.com utente autenticato](accconfirm/_static/rick.png)

<span data-ttu-id="91a83-324">Fare clic sul collegamento **Gestisci** .</span><span class="sxs-lookup"><span data-stu-id="91a83-324">Click on the **Manage** link.</span></span> <span data-ttu-id="91a83-325">Si notino gli account di accesso di social network (0) esterni associati a questo account.</span><span class="sxs-lookup"><span data-stu-id="91a83-325">Note the 0 external (social logins) associated with this account.</span></span>

![Gestisci visualizzazione](accconfirm/_static/manage.png)

<span data-ttu-id="91a83-327">Fare clic sul collegamento a un altro servizio di accesso e accettare le richieste dell'app.</span><span class="sxs-lookup"><span data-stu-id="91a83-327">Click the link to another login service and accept the app requests.</span></span> <span data-ttu-id="91a83-328">Nell'immagine seguente, Facebook è il provider di autenticazione esterno:</span><span class="sxs-lookup"><span data-stu-id="91a83-328">In the following image, Facebook is the external authentication provider:</span></span>

![Gestire gli account di accesso esterni visualizzare l'elenco di Facebook](accconfirm/_static/fb.png)

<span data-ttu-id="91a83-330">I due account sono stati combinati.</span><span class="sxs-lookup"><span data-stu-id="91a83-330">The two accounts have been combined.</span></span> <span data-ttu-id="91a83-331">È possibile accedere con uno dei due account.</span><span class="sxs-lookup"><span data-stu-id="91a83-331">You are able to sign in with either account.</span></span> <span data-ttu-id="91a83-332">È possibile che gli utenti aggiungano gli account locali nel caso in cui il servizio di autenticazione per l'accesso di social networking sia inattivo o più probabilmente abbiano perso l'accesso al proprio account di social networking.</span><span class="sxs-lookup"><span data-stu-id="91a83-332">You might want your users to add local accounts in case their social login authentication service is down, or more likely they've lost access to their social account.</span></span>

## <a name="enable-account-confirmation-after-a-site-has-users"></a><span data-ttu-id="91a83-333">Abilitare la conferma dell'account dopo che un sito ha utenti</span><span class="sxs-lookup"><span data-stu-id="91a83-333">Enable account confirmation after a site has users</span></span>

<span data-ttu-id="91a83-334">L'abilitazione della conferma dell'account in un sito con utenti blocca tutti gli utenti esistenti.</span><span class="sxs-lookup"><span data-stu-id="91a83-334">Enabling account confirmation on a site with users locks out all the existing users.</span></span> <span data-ttu-id="91a83-335">Gli utenti esistenti sono bloccati perché i relativi account non sono confermati.</span><span class="sxs-lookup"><span data-stu-id="91a83-335">Existing users are locked out because their accounts aren't confirmed.</span></span> <span data-ttu-id="91a83-336">Per aggirare il blocco utente esistente, utilizzare uno degli approcci seguenti:</span><span class="sxs-lookup"><span data-stu-id="91a83-336">To work around existing user lockout, use one of the following approaches:</span></span>

* <span data-ttu-id="91a83-337">Aggiornare il database per contrassegnare tutti gli utenti esistenti come confermati.</span><span class="sxs-lookup"><span data-stu-id="91a83-337">Update the database to mark all existing users as being confirmed.</span></span>
* <span data-ttu-id="91a83-338">Verificare gli utenti esistenti.</span><span class="sxs-lookup"><span data-stu-id="91a83-338">Confirm existing users.</span></span> <span data-ttu-id="91a83-339">Ad esempio, invio in batch di messaggi di posta elettronica con collegamenti di conferma.</span><span class="sxs-lookup"><span data-stu-id="91a83-339">For example, batch-send emails with confirmation links.</span></span>

::: moniker-end
