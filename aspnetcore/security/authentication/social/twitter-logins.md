---
title: Configurazione dell'accesso esterno a Twitter con ASP.NET Core
author: rick-anderson
description: Questa esercitazione illustra l'integrazione dell'autenticazione utente dell'account Twitter in un'app ASP.NET Core esistente.
ms.author: riande
ms.custom: mvc
ms.date: 03/19/2020
monikerRange: '>= aspnetcore-3.0'
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/twitter-logins
ms.openlocfilehash: 61c983de33b91a16ad207d8a350daf4859c89eaf
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/26/2020
ms.locfileid: "85406095"
---
# <a name="twitter-external-sign-in-setup-with-aspnet-core"></a>Configurazione dell'accesso esterno a Twitter con ASP.NET Core

Da [Valeriy Novytskyy](https://github.com/01binary) e [Rick Anderson](https://twitter.com/RickAndMSFT)

Questo esempio illustra come consentire agli utenti di [accedere con il proprio account Twitter](https://dev.twitter.com/web/sign-in/desktop-browser) usando un progetto ASP.NET Core 3,0 di esempio creato nella [pagina precedente](xref:security/authentication/social/index).

## <a name="create-the-app-in-twitter"></a>Creare l'app in Twitter

* Aggiungere il pacchetto NuGet [Microsoft. AspNetCore. Authentication. Twitter](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Twitter/3.0.0) al progetto.

* Passare a [https://apps.twitter.com/](https://apps.twitter.com/) ed eseguire l'accesso. Se non si ha già un account Twitter, usare il collegamento **[Iscriviti ora](https://twitter.com/signup)** per crearne uno.

* Selezionare **Crea un'app**. Compilare il **nome dell'app**, la **Descrizione dell'applicazione** e l'URI del **sito Web** pubblico. questa operazione può essere temporanea fino a quando non si registra il nome di dominio:

* Selezionare la casella accanto a **Abilita accesso con Twitter**

* Microsoft. AspNetCore.Identity richiede che gli utenti dispongano di un indirizzo di posta elettronica per impostazione predefinita. Passare alla scheda **autorizzazioni** , fare clic sul pulsante **modifica** e selezionare la casella accanto a **Richiedi indirizzo di posta elettronica dagli utenti**.

* Immettere l'URI di sviluppo con `/signin-twitter` accodato nel campo **URL di callback** (ad esempio: `https://webapp128.azurewebsites.net/signin-twitter` ). Lo schema di autenticazione di Twitter configurato più avanti in questo esempio gestirà automaticamente le richieste in `/signin-twitter` Route per implementare il flusso OAuth.

  > [!NOTE]
  > Il segmento URI `/signin-twitter` viene impostato come callback predefinito del provider di autenticazione Twitter. È possibile modificare l'URI di callback predefinito durante la configurazione del middleware di autenticazione Twitter tramite la proprietà [RemoteAuthenticationOptions. CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) ereditata della classe [TwitterOptions](/dotnet/api/microsoft.aspnetcore.authentication.twitter.twitteroptions) .

* Compilare il resto del modulo e selezionare **Crea**. Vengono visualizzati i dettagli della nuova applicazione:

## <a name="store-the-twitter-consumer-api-key-and-secret"></a>Archiviare la chiave e il segreto API del consumer di Twitter

Archiviare le impostazioni riservate, ad esempio la chiave API del consumer di Twitter e il segreto con [gestione Secret](xref:security/app-secrets). Per questo esempio, attenersi alla procedura seguente:

1. Inizializzare il progetto per l'archiviazione segreta in base alle istruzioni riportate in [abilitare l'archiviazione segreta](xref:security/app-secrets#enable-secret-storage).
1. Archiviare le impostazioni sensibili nell'archivio dei segreti locali con le chiavi dei segreti `Authentication:Twitter:ConsumerKey` e `Authentication:Twitter:ConsumerSecret` :

    ```dotnetcli
    dotnet user-secrets set "Authentication:Twitter:ConsumerAPIKey" "<consumer-api-key>"
    dotnet user-secrets set "Authentication:Twitter:ConsumerSecret" "<consumer-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

Questi token si trovano nella scheda **chiavi e token di accesso** dopo la creazione di una nuova applicazione Twitter:

## <a name="configure-twitter-authentication"></a>Configurare l'autenticazione di Twitter

Aggiungere il servizio Twitter nel `ConfigureServices` metodo nel file *Startup.cs* :

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupTwitter3x.cs?name=snippet&highlight=10-15)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

Vedere le informazioni di riferimento sull'API [TwitterOptions](/dotnet/api/microsoft.aspnetcore.builder.twitteroptions) per altre informazioni sulle opzioni di configurazione supportate dall'autenticazione Twitter. Questa operazione può essere utilizzata per richiedere informazioni diverse sull'utente.

## <a name="sign-in-with-twitter"></a>Accedi con Twitter

Eseguire l'app e selezionare **Accedi**. Viene visualizzata un'opzione per accedere con Twitter:

Clic su **Twitter** reindirizza a Twitter per l'autenticazione:

Dopo aver immesso le credenziali di Twitter, viene reindirizzato di nuovo al sito Web in cui è possibile impostare la posta elettronica.

A questo punto è stato effettuato l'accesso con le credenziali di Twitter:

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

<!-- 
### React to cancel Authorize External sign-in
Twitter doesn't support AccessDeniedPath
Rather in the twitter setup, you can provide an External sign-in homepage. The external sign-in homepage doesn't support localhost. Tested with https://cors3.azurewebsites.net/ and that works.
-->

## <a name="troubleshooting"></a>Risoluzione dei problemi

* **Solo ASP.NET Core 2. x:** Se Identity non è configurato chiamando `services.AddIdentity` in `ConfigureServices` , il tentativo di eseguire l'autenticazione comporterà *l'eccezione ArgumentException: è necessario specificare l'opzione ' SignInScheme '*. Il modello di progetto utilizzato in questo esempio garantisce che questa operazione venga eseguita.
* Se il database del sito non è stato creato applicando la migrazione iniziale, si otterrà *un'operazione di database non riuscita durante l'elaborazione dell'* errore di richiesta. Toccare **applica migrazioni** per creare il database e aggiornare per continuare a superare l'errore.

## <a name="next-steps"></a>Passaggi successivi

* Questo articolo ha illustrato come è possibile eseguire l'autenticazione con Twitter. È possibile seguire un approccio simile per l'autenticazione con altri provider elencati nella [pagina precedente](xref:security/authentication/social/index).

* Dopo aver pubblicato il sito Web nell'app Web di Azure, è necessario reimpostare il `ConsumerSecret` nel portale per sviluppatori di Twitter.

* Impostare `Authentication:Twitter:ConsumerKey` e `Authentication:Twitter:ConsumerSecret` come impostazioni dell'applicazione nella portale di Azure. Il sistema di configurazione è configurato per la lettura delle chiavi dalle variabili di ambiente.
