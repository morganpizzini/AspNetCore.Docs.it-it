---
title: Configurazione dell'account Microsoft per l'accesso esterno con ASP.NET Core
author: rick-anderson
description: In questo esempio viene illustrata l'integrazione dell'autenticazione utente dell'account Microsoft in un'app ASP.NET Core esistente.
ms.author: riande
ms.custom: mvc
ms.date: 03/19/2020
monikerRange: '>= aspnetcore-3.0'
uid: security/authentication/microsoft-logins
ms.openlocfilehash: 32315267e0672b0747917228f08591a15e4449f8
ms.sourcegitcommit: 5af16166977da598953f82da3ed3b7712d38f6cb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81277249"
---
# <a name="microsoft-account-external-login-setup-with-aspnet-core"></a>Configurazione dell'account Microsoft per l'accesso esterno con ASP.NET Core

Da [Valeriy Novytskyy](https://github.com/01binary) e [Rick Anderson](https://twitter.com/RickAndMSFT)

In questo esempio viene illustrato come consentire agli utenti di accedere con il proprio account Microsoft utilizzando il progetto ASP.NET Core 3.0 creato nella [pagina precedente.](xref:security/authentication/social/index)

## <a name="create-the-app-in-microsoft-developer-portal"></a>Creare l'app nel portale per sviluppatori MicrosoftCreate the app in Microsoft Developer Portal

* Aggiungere il pacchetto [Microsoft.AspNetCore.Authentication.MicrosoftAccount](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.MicrosoftAccount/) NuGet al progetto.
* Passare alla pagina Portale di Azure - Registrazioni app e creare o accedere a un account Microsoft:Navigate to the [Azure portal - App registrations](https://go.microsoft.com/fwlink/?linkid=2083908) page and create or sign into a Microsoft account:

Se non si dispone di un account Microsoft, selezionare **Crea uno**. Dopo l'accesso, si viene reindirizzati alla pagina **Registrazioni app:**

* Seleziona **Nuova registrazione**
* Immettere un **Nome**.
* Selezionare un'opzione per **Tipi di account supportati**.  <!-- Accounts for any org work with MS domain accounts. Most folks probably want the last option, personal MS accounts. It took 24 hours after setting this up for the keys to work -->
* In **URI di reindirizzamento** `/signin-microsoft` immettere l'URL di sviluppo con l'aggiunta. Ad esempio: `https://localhost:5001/signin-microsoft`. Lo schema di autenticazione Microsoft configurato più `/signin-microsoft` avanti in questo esempio gestirà automaticamente le richieste in corrispondenza della route per implementare il flusso OAuth.
* Seleziona **Registrazione**

### <a name="create-client-secret"></a>Creare un segreto clientCreate client secret

* Nel riquadro sinistro selezionare **Certificati & segreti**.
* In **Segreti client**selezionare Nuovo **segreto client**

  * Aggiungere una descrizione per il segreto client.
  * Selezionare il pulsante **Aggiungi.Select** the Add button.

* In **Segreti client**copiare il valore del segreto client.

Il segmento URI viene impostato come callback predefinito del provider di autenticazione Microsoft.The URI segment `/signin-microsoft` is set as the default callback of the Microsoft authentication provider. È possibile modificare l'URI di callback predefinito durante la configurazione del middleware di autenticazione Microsoft tramite la proprietà [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) ereditata della classe [MicrosoftAccountOptions.](/dotnet/api/microsoft.aspnetcore.authentication.microsoftaccount.microsoftaccountoptions)

## <a name="store-the-microsoft-client-id-and-secret"></a>Archiviare l'ID client Microsoft e il segreto

Archiviare impostazioni riservate, ad esempio l'ID client Microsoft e i valori segreti con [Secret Manager](xref:security/app-secrets). Per questo esempio, usare la procedura seguente:For this sample, use the following steps:

1. Inizializzare il progetto per l'archiviazione segreta in base alle istruzioni in [Abilitare l'archiviazione segreta.](xref:security/app-secrets#enable-secret-storage)
1. Memorizzare le impostazioni sensibili nell'archivio `Authentication:Microsoft:ClientId` `Authentication:Microsoft:ClientSecret`segreto locale con le chiavi segrete e:

    ```dotnetcli
    dotnet user-secrets set "Authentication:Microsoft:ClientId" "<client-id>"
    dotnet user-secrets set "Authentication:Microsoft:ClientSecret" "<client-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="configure-microsoft-account-authentication"></a>Configurare l'autenticazione dell'account Microsoft

Aggiungere il servizio Account `Startup.ConfigureServices`Microsoft a :

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupMS3x.cs?name=snippet&highlight=10-14)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

Per altre informazioni sulle opzioni di configurazione supportate dall'autenticazione dell'account Microsoft, vedere le informazioni di riferimento sull'API [MicrosoftAccountOptions.For](/dotnet/api/microsoft.aspnetcore.builder.microsoftaccountoptions) more information about configuration options supported by Microsoft Account authentication, see the MicrosoftAccountOptions API reference. Può essere utilizzato per richiedere informazioni diverse sull'utente.

## <a name="sign-in-with-microsoft-account"></a>Accedi con l'account Microsoft

Eseguire l'app e fare clic su **Accedi**. Viene visualizzata un'opzione per accedere con Microsoft. Quando si fa clic su Microsoft, si viene reindirizzati a Microsoft per l'autenticazione. Dopo aver effettuato l'accesso con il tuo account Microsoft, ti verrà richiesto di consentire all'app di accedere alle tue informazioni:

Toccare **Sì** e si verrà reindirizzati al sito web dove è possibile impostare la posta elettronica.

A questo punto si è connessi utilizzando le credenziali Microsoft:

[!INCLUDE[](includes/chain-auth-providers.md)]

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a>Risoluzione dei problemi

* Se il provider dell'account Microsoft reindirizza l'utente a una pagina di errore `#` di accesso, prendere nota del titolo dell'errore e dei parametri della stringa di query della descrizione direttamente seguendo il (hashtag) nell'URI.

  Anche se il messaggio di errore sembra indicare un problema con l'autenticazione Microsoft, la causa più comune è l'URI dell'applicazione non corrisponde a nessuno degli URI di **reindirizzamento** specificati per la piattaforma **Web.**
* Se Identity non è `services.AddIdentity` configurato `ConfigureServices`chiamando in , il tentativo di autenticazione comporterà *ArgumentException: deve essere fornita l'opzione 'SignInScheme'.* Il modello di progetto utilizzato in questo esempio garantisce che questa operazione venga eseguita.
* Se il database del sito non è stato creato applicando la migrazione iniziale, si otterrà *un'operazione del database non riuscita durante l'elaborazione dell'errore* di richiesta. Toccare **Applica migrazioni** per creare il database e aggiornare per continuare oltre l'errore.

## <a name="next-steps"></a>Passaggi successivi

* In questo articolo è stato illustrato come è possibile eseguire l'autenticazione con Microsoft.This article showed how you can authenticate with Microsoft. È possibile seguire un approccio simile per l'autenticazione con altri provider elencati nella [pagina precedente.](xref:security/authentication/social/index)

* Dopo aver pubblicato il sito Web nell'app Web di Azure, creare nuovi segreti client nel portale per sviluppatori Microsoft.Once you publish your web site to Azure web app, create a new client secrets in the Microsoft Developer Portal.

* Impostare `Authentication:Microsoft:ClientId` `Authentication:Microsoft:ClientSecret` le impostazioni e come dell'applicazione nel portale di Azure.Set the and as application settings in the Azure portal. Il sistema di configurazione è impostato per leggere le chiavi dalle variabili di ambiente.
