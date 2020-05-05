---
title: Configurazione dell'accesso esterno all'account Microsoft con ASP.NET Core
author: rick-anderson
description: In questo esempio viene illustrata l'integrazione di account Microsoft autenticazione utente in un'app ASP.NET Core esistente.
ms.author: riande
ms.custom: mvc
ms.date: 03/19/2020
monikerRange: '>= aspnetcore-3.0'
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/microsoft-logins
ms.openlocfilehash: 731a17085a1fd01852bb3fe2f0fc9f3e7a9ac30f
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775661"
---
# <a name="microsoft-account-external-login-setup-with-aspnet-core"></a>Configurazione dell'accesso esterno all'account Microsoft con ASP.NET Core

Da [Valeriy Novytskyy](https://github.com/01binary) e [Rick Anderson](https://twitter.com/RickAndMSFT)

Questo esempio illustra come consentire agli utenti di accedere con il proprio lavoro, Istituto di istruzione o personale account Microsoft usando il progetto ASP.NET Core 3,0 creato nella [pagina precedente](xref:security/authentication/social/index).

## <a name="create-the-app-in-microsoft-developer-portal"></a>Creare l'app nel portale per sviluppatori Microsoft

* Aggiungere il pacchetto NuGet [Microsoft. AspNetCore. Authentication. MicrosoftAccount](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.MicrosoftAccount/) al progetto.
* Passare alla pagina [portale di Azure-registrazioni app](https://go.microsoft.com/fwlink/?linkid=2083908) e creare o accedere a un account Microsoft:

Se non si dispone di un account Microsoft, selezionare **crearne uno**. Dopo aver eseguito l'accesso, si verrà reindirizzati alla pagina **registrazioni app** :

* Seleziona **nuova registrazione**
* Immettere un **nome**.
* Selezionare un'opzione per i **tipi di account supportati**.  <!-- Accounts for any org work with MS domain accounts. Most folks probably want the last option, personal MS accounts. It took 24 hours after setting this up for the keys to work -->
* In **URI di reindirizzamento**immettere l'URL di sviluppo `/signin-microsoft` con accodato. Ad esempio: `https://localhost:5001/signin-microsoft`. Lo schema di autenticazione Microsoft configurato più avanti in questo esempio gestirà automaticamente `/signin-microsoft` le richieste in route per implementare il flusso OAuth.
* Seleziona **Registro**

### <a name="create-client-secret"></a>Crea segreto client

* Nel riquadro sinistro selezionare **certificati & segreti**.
* In **segreti client**selezionare **nuovo segreto client**

  * Aggiungere una descrizione per il segreto client.
  * Selezionare il pulsante **Aggiungi** .

* In **segreti client**copiare il valore del segreto client.

Il segmento `/signin-microsoft` URI viene impostato come callback predefinito del provider di autenticazione Microsoft. È possibile modificare l'URI di callback predefinito durante la configurazione del middleware di autenticazione Microsoft tramite la proprietà [RemoteAuthenticationOptions. CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) ereditata della classe [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.authentication.microsoftaccount.microsoftaccountoptions) .

## <a name="store-the-microsoft-client-id-and-secret"></a>Archiviare l'ID client e il segreto Microsoft

Archiviare le impostazioni riservate, ad esempio i valori di ID client e segreto Microsoft con [gestione Secret](xref:security/app-secrets). Per questo esempio, attenersi alla procedura seguente:

1. Inizializzare il progetto per l'archiviazione segreta in base alle istruzioni riportate in [abilitare l'archiviazione segreta](xref:security/app-secrets#enable-secret-storage).
1. Archiviare le impostazioni sensibili nell'archivio dei segreti locali con le chiavi `Authentication:Microsoft:ClientId` segrete `Authentication:Microsoft:ClientSecret`e:

    ```dotnetcli
    dotnet user-secrets set "Authentication:Microsoft:ClientId" "<client-id>"
    dotnet user-secrets set "Authentication:Microsoft:ClientSecret" "<client-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="configure-microsoft-account-authentication"></a>Configurare l'autenticazione dell'account Microsoft

Aggiungere il servizio account Microsoft a `Startup.ConfigureServices`:

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupMS3x.cs?name=snippet&highlight=10-14)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

Per altre informazioni sulle opzioni di configurazione supportate dall'autenticazione dell'account Microsoft, vedere le informazioni di riferimento sull'API [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.builder.microsoftaccountoptions) . Questa operazione può essere utilizzata per richiedere informazioni diverse sull'utente.

## <a name="sign-in-with-microsoft-account"></a>Account Accedi con Microsoft

Eseguire l'app e fare clic su **Accedi**. Viene visualizzata un'opzione per accedere con Microsoft. Quando si fa clic su Microsoft, si viene reindirizzati a Microsoft per l'autenticazione. Dopo aver eseguito l'accesso con l'account Microsoft, verrà richiesto di consentire all'app di accedere alle informazioni:

Toccare **Sì** e si verrà reindirizzati di nuovo al sito Web in cui è possibile impostare la posta elettronica.

A questo punto è stato effettuato l'accesso con le credenziali Microsoft:

[!INCLUDE[](includes/chain-auth-providers.md)]

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a>Risoluzione dei problemi

* Se il provider di account Microsoft reindirizza l'utente a una pagina di errore di accesso, prendere nota dei parametri della stringa di query titolo e descrizione `#` dell'errore direttamente dopo l'hashtag nell'URI.

  Anche se il messaggio di errore sembra indicare un problema con l'autenticazione Microsoft, la causa più comune è l'URI dell'applicazione che non corrisponde ad alcuno degli **URI di reindirizzamento** specificati per la piattaforma **Web** .
* Se Identity non è configurato chiamando `services.AddIdentity` in `ConfigureServices`, il tentativo di eseguire l'autenticazione comporterà *l'eccezione ArgumentException: è necessario specificare l'opzione ' SignInScheme '*. Il modello di progetto utilizzato in questo esempio garantisce che questa operazione venga eseguita.
* Se il database del sito non è stato creato applicando la migrazione iniziale, si otterrà *un'operazione di database non riuscita durante l'elaborazione dell'* errore di richiesta. Toccare **applica migrazioni** per creare il database e aggiornare per continuare a superare l'errore.

## <a name="next-steps"></a>Passaggi successivi

* Questo articolo ha illustrato come è possibile eseguire l'autenticazione con Microsoft. È possibile seguire un approccio simile per l'autenticazione con altri provider elencati nella [pagina precedente](xref:security/authentication/social/index).

* Dopo aver pubblicato il sito Web nell'app Web di Azure, creare un nuovo segreto client nel portale per sviluppatori Microsoft.

* Impostare `Authentication:Microsoft:ClientId` e `Authentication:Microsoft:ClientSecret` come impostazioni dell'applicazione nella portale di Azure. Il sistema di configurazione è configurato per la lettura delle chiavi dalle variabili di ambiente.
