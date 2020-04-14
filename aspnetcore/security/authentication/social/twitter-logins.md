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
# <a name="twitter-external-sign-in-setup-with-aspnet-core"></a>Configurazione dell'accesso esterno di Twitter con ASP.NET Core

Da [Valeriy Novytskyy](https://github.com/01binary) e [Rick Anderson](https://twitter.com/RickAndMSFT)

In questo esempio viene illustrato come consentire agli utenti di accedere con il [proprio account Twitter](https://dev.twitter.com/web/sign-in/desktop-browser) utilizzando un progetto ASP.NET Core 3.0 creato nella pagina [precedente.](xref:security/authentication/social/index)

## <a name="create-the-app-in-twitter"></a>Creare l'app in Twitter

* Aggiungere il pacchetto [Microsoft.AspNetCore.Authentication.Twitter](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Twitter/3.0.0) NuGet al progetto.

* Individuare [https://apps.twitter.com/](https://apps.twitter.com/) ed eseguire l'accesso. Se non hai già un account Twitter, usa il link **[Iscriviti ora](https://twitter.com/signup)** per crearne uno.

* Selezionare **Crea un'app**. Compilare il **nome dell'app**, la **descrizione dell'applicazione** e l'URI **del sito Web** pubblico (questo può essere temporaneo fino a quando non si registra il nome di dominio):

* Seleziona la casella accanto a **Abilita l'accesso con Twitter**

* Microsoft.AspNetCore.Identity richiede agli utenti di disporre di un indirizzo di posta elettronica per impostazione predefinita. Passare alla scheda **Autorizzazioni,** fare clic sul pulsante **Modifica** e selezionare la casella accanto a **Richiedi indirizzo di posta elettronica agli utenti**.

* Immettere l'URI di sviluppo con `/signin-twitter` l'aggiunta nel campo URL di **callback,** ad esempio: `https://webapp128.azurewebsites.net/signin-twitter`. Lo schema di autenticazione di Twitter configurato `/signin-twitter` più avanti in questo esempio gestirà automaticamente le richieste durante la route per implementare il flusso OAuth.

  > [!NOTE]
  > Il segmento `/signin-twitter` URI viene impostato come callback predefinito del provider di autenticazione di Twitter. È possibile modificare l'URI di callback predefinito durante la configurazione del middleware di autenticazione di Twitter tramite la proprietà [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) ereditata della classe [TwitterOptions.](/dotnet/api/microsoft.aspnetcore.authentication.twitter.twitteroptions)

* Compilare il resto del modulo e selezionare **Crea**. Vengono visualizzati i nuovi dettagli dell'applicazione:

## <a name="store-the-twitter-consumer-api-key-and-secret"></a>Memorizzare la chiave API consumer di Twitter e il segreto

Memorizzare le impostazioni sensibili come la chiave API consumer Twitter e segreto con [Secret Manager](xref:security/app-secrets). Per questo esempio, usare la procedura seguente:For this sample, use the following steps:

1. Inizializzare il progetto per l'archiviazione segreta in base alle istruzioni in [Abilitare l'archiviazione segreta.](xref:security/app-secrets#enable-secret-storage)
1. Memorizzare le impostazioni sensibili nell'archivio `Authentication:Twitter:ConsumerKey` `Authentication:Twitter:ConsumerSecret`segreto locale con le chiavi secrets e :

    ```dotnetcli
    dotnet user-secrets set "Authentication:Twitter:ConsumerAPIKey" "<consumer-api-key>"
    dotnet user-secrets set "Authentication:Twitter:ConsumerSecret" "<consumer-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

Questi token sono disponibili nella scheda Chiavi e token di accesso dopo aver creato una nuova applicazione Twitter:These tokens can be found on the **Keys and Access Tokens** tab after creating a new Twitter application:

## <a name="configure-twitter-authentication"></a>Configurare l'autenticazione di Twitter

Aggiungere il servizio `ConfigureServices` Twitter nel metodo nel file *di Startup.cs:*

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupTwitter3x.cs?name=snippet&highlight=10-15)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

Per altre informazioni sulle opzioni di configurazione supportate dall'autenticazione di Twitter, vedere le informazioni di riferimento sull'API [TwitterOptions.See](/dotnet/api/microsoft.aspnetcore.builder.twitteroptions) the TwitterOptions API reference for more information on configuration options supported by Twitter authentication. Può essere utilizzato per richiedere informazioni diverse sull'utente.

## <a name="sign-in-with-twitter"></a>Accedi con Twitter

Eseguire l'app e selezionare **Accedi**. Viene visualizzata un'opzione per accedere con Twitter:

Cliccando su **Twitter** reindirizza a Twitter per l'autenticazione:

Dopo aver inserito le credenziali di Twitter, si viene reindirizzati al sito web dove è possibile impostare la posta elettronica.

Ora hai effettuato l'accesso utilizzando le tue credenziali Twitter:

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

<!-- 
### React to cancel Authorize External sign-in
Twitter doesn't support AccessDeniedPath
Rather in the twitter setup, you can provide an External sign-in homepage. The external sign-in homepage doesn't support localhost. Tested with https://cors3.azurewebsites.net/ and that works.
-->

## <a name="troubleshooting"></a>Risoluzione dei problemi

* **ASP.NET Solo Core 2.x:** Se Identity non è `services.AddIdentity` configurato `ConfigureServices`chiamando in , il tentativo di autenticazione comporterà *ArgumentException: deve essere fornita l'opzione 'SignInScheme'.* Il modello di progetto utilizzato in questo esempio garantisce che questa operazione venga eseguita.
* Se il database del sito non è stato creato applicando la migrazione iniziale, si otterrà *un'operazione del database non riuscita durante l'elaborazione dell'errore* di richiesta. Toccare **Applica migrazioni** per creare il database e aggiornare per continuare oltre l'errore.

## <a name="next-steps"></a>Passaggi successivi

* Questo articolo ha mostrato come è possibile eseguire l'autenticazione con Twitter. È possibile seguire un approccio simile per l'autenticazione con altri provider elencati nella [pagina precedente.](xref:security/authentication/social/index)

* Dopo aver pubblicato il sito Web nell'app `ConsumerSecret` Web di Azure, è necessario reimpostare il sito nel portale per sviluppatori di Twitter.Once you publish your web site to Azure web app, you should reset the in the Twitter developer portal.

* Impostare `Authentication:Twitter:ConsumerKey` `Authentication:Twitter:ConsumerSecret` le impostazioni e come dell'applicazione nel portale di Azure.Set the and as application settings in the Azure portal. Il sistema di configurazione è impostato per leggere le chiavi dalle variabili di ambiente.
