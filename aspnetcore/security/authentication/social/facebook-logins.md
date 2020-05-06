---
title: Configurazione dell'accesso esterno a Facebook in ASP.NET Core
author: rick-anderson
description: Esercitazione con esempi di codice che illustrano l'integrazione dell'autenticazione utente dell'account Facebook in un'app ASP.NET Core esistente.
ms.author: riande
ms.custom: seoapril2019, mvc, seodec18
ms.date: 03/19/2020
monikerRange: '>= aspnetcore-3.0'
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/facebook-logins
ms.openlocfilehash: 11ddc7314a694446d488da6ef1b2e3423bf7241a
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82777033"
---
# <a name="facebook-external-login-setup-in-aspnet-core"></a>Configurazione dell'accesso esterno a Facebook in ASP.NET Core

Da [Valeriy Novytskyy](https://github.com/01binary) e [Rick Anderson](https://twitter.com/RickAndMSFT)

<!-- per @rick-anderson and scott addie, don't update images. Remove images and point the customer to the FB set up page. FB needs to maintain  instructions to get key and secret.
-->

Questa esercitazione con esempi di codice Mostra come consentire agli utenti di accedere con il proprio account Facebook usando un progetto ASP.NET Core 3,0 di esempio creato nella [pagina precedente](xref:security/authentication/social/index). Si inizia creando un ID app Facebook seguendo la [procedura ufficiale](https://developers.facebook.com).

## <a name="create-the-app-in-facebook"></a>Creare l'app in Facebook

* Aggiungere il pacchetto NuGet [Microsoft. AspNetCore. Authentication. Facebook](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Facebook) al progetto.

* Passare alla pagina dell' [app per sviluppatori Facebook](https://developers.facebook.com/apps/) ed eseguire l'accesso. Se non si ha già un account Facebook, usare il collegamento **Iscriviti a Facebook** nella pagina di accesso per crearne uno.  Dopo aver creato un account Facebook, seguire le istruzioni per registrarsi come sviluppatore di Facebook.

* Dal menu **app personali** selezionare **Crea app** per creare un nuovo ID app.

   ![Portale di Facebook for Developers aperto in Microsoft Edge](index/_static/FBMyApps.png)

* Compilare il modulo e toccare il pulsante **Crea ID app** .

  ![Creare un nuovo modulo ID app](index/_static/FBNewAppId.png)

* Nella scheda nuova app selezionare **Aggiungi un prodotto**.  Nella scheda di **accesso di Facebook** fare clic su **Configura** 

  ![Pagina impostazione prodotto](index/_static/FBProductSetup.png)

* Viene avviata la procedura guidata **avvio rapido** con **scegliere una piattaforma** come prima pagina. Ignorare la procedura guidata per ora facendo clic sul collegamento **Impostazioni** di **accesso di Facebook** nel menu in basso a sinistra:

  ![Ignora Avvio rapido](index/_static/FBSkipQuickStart.png)

* Viene visualizzata la pagina **impostazioni OAuth client** :

  ![Pagina impostazioni OAuth client](index/_static/FBOAuthSetup.png)

* Immettere l'URI di sviluppo con */SignIn-Facebook* aggiunto nel campo **Valid URI di reindirizzamento OAuth** (ad esempio: `https://localhost:44320/signin-facebook`). L'autenticazione di Facebook configurata più avanti in questa esercitazione gestirà automaticamente le richieste in */SignIn-Facebook* route per implementare il flusso OAuth.

> [!NOTE]
> L'URI */SignIn-Facebook* viene impostato come callback predefinito del provider di autenticazione di Facebook. È possibile modificare l'URI di callback predefinito durante la configurazione del middleware di autenticazione di Facebook tramite la proprietà [RemoteAuthenticationOptions. CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) ereditata della classe [FacebookOptions](/dotnet/api/microsoft.aspnetcore.authentication.facebook.facebookoptions) .

* Fare clic su **Salva modifiche**.

* Fare clic sul collegamento **Impostazioni** > di**base** nella finestra di spostamento a sinistra.

  In questa pagina prendere nota del `App ID` e del. `App Secret` Si aggiungeranno entrambi nell'applicazione ASP.NET Core nella sezione successiva:

* Quando si distribuisce il sito, è necessario rivedere la pagina di configurazione dell' **account di accesso di Facebook** e registrare un nuovo URI pubblico.

## <a name="store-the-facebook-app-id-and-secret"></a>Archiviare l'ID e il segreto dell'app Facebook

Archiviare le impostazioni riservate, ad esempio l'ID app Facebook e i valori segreti con [gestione Secret](xref:security/app-secrets). Per questo esempio, attenersi alla procedura seguente:

1. Inizializzare il progetto per l'archiviazione segreta in base alle istruzioni riportate in [abilitare l'archiviazione segreta](xref:security/app-secrets#enable-secret-storage).
1. Archiviare le impostazioni sensibili nell'archivio dei segreti locali con le chiavi `Authentication:Facebook:AppId` segrete `Authentication:Facebook:AppSecret`e:

    ```dotnetcli
    dotnet user-secrets set "Authentication:Facebook:AppId" "<app-id>"
    dotnet user-secrets set "Authentication:Facebook:AppSecret" "<app-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="configure-facebook-authentication"></a>Configurare l'autenticazione di Facebook

Aggiungere il servizio Facebook nel `ConfigureServices` metodo nel file *Startup.cs* :

```csharp
services.AddAuthentication().AddFacebook(facebookOptions =>
{
    facebookOptions.AppId = Configuration["Authentication:Facebook:AppId"];
    facebookOptions.AppSecret = Configuration["Authentication:Facebook:AppSecret"];
});
```

[!INCLUDE [default settings configuration](includes/default-settings.md)]

## <a name="sign-in-with-facebook"></a>Accedi con Facebook

* Eseguire l'app e selezionare **Accedi**. 
* In **usare un altro servizio per accedere**selezionare Facebook.
* Si viene reindirizzati a **Facebook** per l'autenticazione.
* Immettere le credenziali di Facebook.
* Si viene reindirizzati di nuovo al sito in cui è possibile impostare la posta elettronica.

A questo punto è stato effettuato l'accesso con le credenziali di Facebook:

<a name="react"></a>

## <a name="react-to-cancel-authorize-external-sign-in"></a>Reagire per annullare l'autorizzazione dell'accesso esterno

<xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.AccessDeniedPath>può fornire un percorso di reindirizzamento all'agente utente quando l'utente non approva la richiesta di autorizzazione richiesta.

Il codice seguente imposta `AccessDeniedPath` su: `"/AccessDeniedPathInfo"`

[!code-csharp[](~/security/authentication/social/social-code/StartupAccessDeniedPath.cs?name=snippetFB)]

È consigliabile che `AccessDeniedPath` la pagina contenga le informazioni seguenti:

*  L'autenticazione remota è stata annullata.
* Questa app richiede l'autenticazione.
* Per provare a eseguire di nuovo l'accesso, selezionare il collegamento di accesso.

### <a name="test-accessdeniedpath"></a>Test AccessDeniedPath

* Passare a [Facebook.com](https://www.facebook.com/)
* Se è stato eseguito l'accesso, è necessario disconnettersi.
* Eseguire l'app e selezionare l'accesso a Facebook.
* Selezionare **non ora**. Si verrà reindirizzati alla `AccessDeniedPath` pagina specificata.

<!-- End of React  -->
[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

Vedere le informazioni di riferimento sull'API [FacebookOptions](/dotnet/api/microsoft.aspnetcore.builder.facebookoptions) per altre informazioni sulle opzioni di configurazione supportate dall'autenticazione di Facebook. È possibile utilizzare le opzioni di configurazione per:

* Richiedere informazioni diverse sull'utente.
* Aggiungere gli argomenti della stringa di query per personalizzare l'esperienza di accesso.

## <a name="troubleshooting"></a>Risoluzione dei problemi

* **Solo ASP.NET Core 2. x:** Se Identity non è configurato chiamando `services.AddIdentity` in `ConfigureServices`, il tentativo di eseguire l'autenticazione comporterà *l'eccezione ArgumentException: è necessario specificare l'opzione ' SignInScheme '*. Il modello di progetto usato in questa esercitazione garantisce che questa operazione venga eseguita.
* Se il database del sito non è stato creato applicando la migrazione iniziale, si ottiene *un'operazione di database non riuscita durante l'elaborazione dell'* errore di richiesta. Toccare **applica migrazioni** per creare il database e aggiornare per continuare a superare l'errore.

## <a name="next-steps"></a>Passaggi successivi

* Questo articolo ha illustrato come è possibile eseguire l'autenticazione con Facebook. È possibile seguire un approccio simile per l'autenticazione con altri provider elencati nella [pagina precedente](xref:security/authentication/social/index).

* Dopo aver pubblicato il sito Web nell'app Web di Azure, è necessario reimpostare il `AppSecret` nel portale per sviluppatori di Facebook.

* Impostare `Authentication:Facebook:AppId` e `Authentication:Facebook:AppSecret` come impostazioni dell'applicazione nella portale di Azure. Il sistema di configurazione è configurato per la lettura delle chiavi dalle variabili di ambiente.
