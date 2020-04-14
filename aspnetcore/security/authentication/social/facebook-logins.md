---
title: Configurazione dell'accesso esterno di Facebook in ASP.NET Core
author: rick-anderson
description: Esercitazione con esempi di codice che illustrano l'integrazione dell'autenticazione utente dell'account Facebook in un'app ASP.NET Core esistente.
ms.author: riande
ms.custom: seoapril2019, mvc, seodec18
ms.date: 03/19/2020
monikerRange: '>= aspnetcore-3.0'
uid: security/authentication/facebook-logins
ms.openlocfilehash: 9b3128addafb41ad6ec44af5cb12e89607e1ae59
ms.sourcegitcommit: 5af16166977da598953f82da3ed3b7712d38f6cb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81277301"
---
# <a name="facebook-external-login-setup-in-aspnet-core"></a>Configurazione dell'accesso esterno di Facebook in ASP.NET Core

Da [Valeriy Novytskyy](https://github.com/01binary) e [Rick Anderson](https://twitter.com/RickAndMSFT)

<!-- per @rick-anderson and scott addie, don't update images. Remove images and point the customer to the FB set up page. FB needs to maintain  instructions to get key and secret.
-->

Questa esercitazione con esempi di codice mostra come abilitare gli utenti ad accedere con il proprio account Facebook usando un progetto ASP.NET Core 3.0 di esempio creato nella [pagina precedente.](xref:security/authentication/social/index) Iniziamo creando un ID app di Facebook seguendo i [passaggi ufficiali](https://developers.facebook.com).

## <a name="create-the-app-in-facebook"></a>Creare l'app in Facebook

* Aggiungere il pacchetto [Microsoft.AspNetCore.Authentication.Facebook](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Facebook) NuGet al progetto.

* Accedi alla pagina [dell'app Facebook Developers](https://developers.facebook.com/apps/) e accedi. Se non hai già un account Facebook, utilizza il link **Iscriviti a Facebook** nella pagina di accesso per crearne uno.  Una volta che hai un account Facebook, segui le istruzioni per registrarti come sviluppatore di Facebook.

* Dal menu **App personali** selezionare **Crea app** per creare un nuovo ID app.

   ![Portale Facebook per sviluppatori aperto in Microsoft Edge](index/_static/FBMyApps.png)

* Compila il modulo e tocca il pulsante **Crea ID app.**

  ![Creare un modulo Nuovo ID app](index/_static/FBNewAppId.png)

* Nella nuova scheda App selezionare **Aggiungi un prodotto**.  Nella scheda di accesso di **Facebook,** fai clic su **Configura** 

  ![Pagina di configurazione del prodotto](index/_static/FBProductSetup.png)

* Verrà avviata la **procedura guidata Guida introduttiva** con **Scegli una piattaforma** come prima pagina. Ignorare la procedura guidata per il momento facendo clic sul collegamento **Impostazioni** di accesso **FaceBook** nel menu in basso a sinistra:

  ![Salta la Guida introduttiva](index/_static/FBSkipQuickStart.png)

* Viene visualizzata la pagina **Impostazioni OAuth client:**

  ![Pagina Impostazioni OAuth client](index/_static/FBOAuthSetup.png)

* Inserisci l'URI di sviluppo con */signin-facebook* aggiunto nel campo **URI di reindirizzamento OAuth valido** (ad esempio: `https://localhost:44320/signin-facebook`). L'autenticazione di Facebook configurata più avanti in questa esercitazione gestirà automaticamente le richieste nella route */signin-facebook* per implementare il flusso OAuth.

> [!NOTE]
> L'URI */signin-facebook* viene impostato come callback predefinito del provider di autenticazione di Facebook. È possibile modificare l'URI di callback predefinito durante la configurazione del middleware di autenticazione di Facebook tramite la proprietà [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) ereditata della classe [FacebookOptions.](/dotnet/api/microsoft.aspnetcore.authentication.facebook.facebookoptions)

* Fare clic su **Save Changes**.

* Fare clic sul collegamento **Impostazioni** > **di base** nel riquadro di spostamento sinistro.

  In questa pagina, prendere `App ID` nota `App Secret`del vostro e il vostro . Entrambi verranno aggiunti all'applicazione ASP.NET Core nella sezione successiva:You will add both in your ASP.NET Core application in the next section:

* Quando distribuisci il sito devi rivedere la pagina di configurazione di **Facebook Login** e registrare un nuovo URI pubblico.

## <a name="store-the-facebook-app-id-and-secret"></a>Memorizzare l'ID e il segreto dell'app Facebook

Memorizzare le impostazioni sensibili come l'ID app di Facebook e valori segreti con [Secret Manager](xref:security/app-secrets). Per questo esempio, usare la procedura seguente:For this sample, use the following steps:

1. Inizializzare il progetto per l'archiviazione segreta in base alle istruzioni in [Abilitare l'archiviazione segreta.](xref:security/app-secrets#enable-secret-storage)
1. Memorizzare le impostazioni sensibili nell'archivio `Authentication:Facebook:AppId` `Authentication:Facebook:AppSecret`segreto locale con le chiavi segrete e:

    ```dotnetcli
    dotnet user-secrets set "Authentication:Facebook:AppId" "<app-id>"
    dotnet user-secrets set "Authentication:Facebook:AppSecret" "<app-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="configure-facebook-authentication"></a>Configurare l'autenticazione di Facebook

Aggiungere il servizio `ConfigureServices` Facebook nel metodo nel file *Startup.cs:*

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
* In **Usa un altro servizio per accedere.**
* Si viene reindirizzati a **Facebook** per l'autenticazione.
* Inserisci le tue credenziali di Facebook.
* Si viene reindirizzati al sito in cui è possibile impostare la posta elettronica.

Ora hai effettuato l'accesso utilizzando le tue credenziali di Facebook:

<a name="react"></a>

## <a name="react-to-cancel-authorize-external-sign-in"></a>Reagire per annullare l'autorizzazione dell'accesso esterno

<xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.AccessDeniedPath>può fornire un percorso di reindirizzamento all'agente utente quando l'utente non approva la richiesta di autorizzazione richiesta.

Il codice seguente `AccessDeniedPath` `"/AccessDeniedPathInfo"`imposta l'opzione su :

[!code-csharp[](~/security/authentication/social/social-code/StartupAccessDeniedPath.cs?name=snippetFB)]

Si consiglia `AccessDeniedPath` di utilizzare la pagina con le seguenti informazioni:

*  L'autenticazione remota è stata annullata.
* Questa applicazione richiede l'autenticazione.
* Per riprovare ad accedere, selezionare il collegamento Accesso.

### <a name="test-accessdeniedpath"></a>Test AccessDeniedPath

* Passare a [facebook.com](https://www.facebook.com/)
* Se hai effettuato l'accesso, devi disconnetterti.
* Esegui l'app e seleziona Accesso a Facebook.
* Selezionare **Non ora**. Si viene reindirizzati `AccessDeniedPath` alla pagina specificata.

<!-- End of React  -->
[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

Vedere le informazioni di riferimento sull'API FacebookOptions per ulteriori informazioni sulle opzioni di configurazione supportate dall'autenticazione di Facebook.See the [FacebookOptions](/dotnet/api/microsoft.aspnetcore.builder.facebookoptions) API reference for more information on configuration options supported by Facebook authentication. Le opzioni di configurazione possono essere utilizzate per:

* Richiedere informazioni diverse sull'utente.
* Aggiungere argomenti della stringa di query per personalizzare l'esperienza di accesso.

## <a name="troubleshooting"></a>Risoluzione dei problemi

* **ASP.NET Solo Core 2.x:** Se Identity non è `services.AddIdentity` configurato `ConfigureServices`chiamando in , il tentativo di autenticazione comporterà *ArgumentException: deve essere fornita l'opzione 'SignInScheme'.* Il modello di progetto usato in questa esercitazione garantisce che questa operazione venga eseguita.
* Se il database del sito non è stato creato applicando la migrazione iniziale, si ottiene *un'operazione del database non riuscita durante l'elaborazione dell'errore* di richiesta. Toccare **Applica migrazioni** per creare il database e aggiornare per continuare oltre l'errore.

## <a name="next-steps"></a>Passaggi successivi

* Questo articolo ha mostrato come è possibile eseguire l'autenticazione con Facebook. È possibile seguire un approccio simile per l'autenticazione con altri provider elencati nella [pagina precedente.](xref:security/authentication/social/index)

* Dopo aver pubblicato il sito Web nell'app `AppSecret` Web di Azure, è necessario reimpostare il sito nel portale per sviluppatori di Facebook.

* Impostare `Authentication:Facebook:AppId` `Authentication:Facebook:AppSecret` le impostazioni e come dell'applicazione nel portale di Azure.Set the and as application settings in the Azure portal. Il sistema di configurazione è impostato per leggere le chiavi dalle variabili di ambiente.
