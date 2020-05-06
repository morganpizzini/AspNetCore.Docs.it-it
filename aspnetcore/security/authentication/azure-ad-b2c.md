---
title: Autenticazione cloud con Azure Active Directory B2C in ASP.NET Core
author: camsoper
description: Scopri come configurare l'autenticazione Azure Active Directory B2C con ASP.NET Core.
ms.author: casoper
ms.custom: mvc
ms.date: 01/21/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/azure-ad-b2c
ms.openlocfilehash: 86808e6bdd3bb669a62ad9e333b11df4c09a9cd3
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774639"
---
# <a name="cloud-authentication-with-azure-active-directory-b2c-in-aspnet-core"></a>Autenticazione cloud con Azure Active Directory B2C in ASP.NET Core

Di [Cam Soper](https://twitter.com/camsoper)

[Azure Active Directory B2C](/azure/active-directory-b2c/active-directory-b2c-overview) (Azure ad B2C) è una soluzione di gestione delle identità cloud per app Web e per dispositivi mobili. Il servizio fornisce l'autenticazione per le app ospitate nel cloud e in locale. I tipi di autenticazione includono singoli account, account social network e account aziendali federati. Inoltre, Azure AD B2C possibile fornire l'autenticazione a più fattori con la configurazione minima.

> [!TIP]
> Azure Active Directory (Azure AD) e Azure AD B2C sono offerte di prodotti separate. Un tenant di Azure AD rappresenta un'organizzazione, mentre un tenant Azure AD B2C rappresenta una raccolta di identità da usare con le applicazioni di relying party. Per altre informazioni, vedere [Azure ad B2C: domande frequenti (FAQ)](/azure/active-directory-b2c/active-directory-b2c-faqs).

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare un tenant di Azure Active Directory B2C
> * Registrare un'app in Azure AD B2C
> * Usare Visual Studio per creare un'app Web ASP.NET Core configurata per l'uso del tenant Azure AD B2C per l'autenticazione
> * Configurare i criteri che controllano il comportamento del tenant Azure AD B2C

## <a name="prerequisites"></a>Prerequisiti

Per questa procedura dettagliata sono necessari gli elementi seguenti:

* [Sottoscrizione di Microsoft Azure](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019)

## <a name="create-the-azure-active-directory-b2c-tenant"></a>Creare il tenant di Azure Active Directory B2C

Creare un tenant [di Azure Active Directory B2C come descritto nella documentazione](/azure/active-directory-b2c/active-directory-b2c-get-started). Quando richiesto, l'associazione del tenant a una sottoscrizione di Azure è facoltativa per questa esercitazione.

## <a name="register-the-app-in-azure-ad-b2c"></a>Registrare l'app in Azure AD B2C

Nel tenant di Azure AD B2C appena creato registrare l'app seguendo [la procedura descritta nella documentazione](/azure/active-directory-b2c/tutorial-register-applications#register-a-web-application) nella sezione **registrare un'app Web** . Arrestare la sezione **creare un segreto client dell'app Web** . Per questa esercitazione non è necessario un segreto client. 

Usare i valori seguenti:

| Impostazione                       | Valore                     | Note                                                                                                                                                                                              |
|-------------------------------|---------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Nome**                      | *&lt;nome dell'app&gt;*        | Immettere un **nome** per l'app che descriva l'app agli utenti.                                                                                                                                 |
| **Includi app Web/API Web** | Sì                       |                                                                                                                                                                                                    |
| **Consenti il flusso implicito**       | Sì                       |                                                                                                                                                                                                    |
| **URL di risposta**                 | `https://localhost:44300/signin-oidc` | Gli URL di risposta sono gli endpoint a cui Azure AD B2C restituisce eventuali token richiesti dall'app. Visual Studio fornisce l'URL di risposta da usare. Per il momento, `https://localhost:44300/signin-oidc` immettere per completare il modulo. |
| **URI ID app**                | Lasciare vuoto               | Non richiesto per questa esercitazione.                                                                                                                                                                    |
| **Includi client nativo**     | No                        |                                                                                                                                                                                                    |

> [!WARNING]
> Se si configura un URL di risposta non localhost, tenere presente i [vincoli sugli elementi consentiti nell'elenco URL di risposta](/azure/active-directory-b2c/tutorial-register-applications#register-a-web-application). 

Dopo la registrazione dell'app, viene visualizzato l'elenco delle app nel tenant. Selezionare l'app appena registrata. Selezionare l'icona **copia** a destra del campo **ID applicazione** per copiarla negli Appunti.

Al momento non è possibile configurare altre informazioni nel tenant di Azure AD B2C, ma lasciare aperta la finestra del browser. Dopo la creazione dell'app ASP.NET Core è presente una maggiore configurazione.

## <a name="create-an-aspnet-core-app-in-visual-studio"></a>Creare un'app ASP.NET Core in Visual Studio

Il modello di applicazione Web di Visual Studio può essere configurato per l'uso del tenant Azure AD B2C per l'autenticazione.

In Visual Studio:

1. Creare una nuova applicazione Web ASP.NET Core. 
2. Selezionare **applicazione Web** dall'elenco di modelli.
3. Selezionare il pulsante **Modifica autenticazione** .
    
    ![Pulsante Modifica autenticazione](./azure-ad-b2c/_static/changeauth.png)

4. Nella finestra di dialogo **Cambia autenticazione** selezionare **account utente singoli**, quindi selezionare **Connetti a un archivio utente esistente nel cloud** nell'elenco a discesa. 
    
    ![Finestra di dialogo Cambia autenticazione](./azure-ad-b2c/_static/changeauthdialog.png)

5. Completare il modulo con i valori seguenti:
    
    | Impostazione                       | Valore                                                 |
    |-------------------------------|-------------------------------------------------------|
    | **Nome di dominio**               | *&lt;nome di dominio del tenant B2C&gt;*          |
    | **ID applicazione**            | *&lt;incollare l'ID applicazione dagli Appunti&gt;* |
    | **Percorso di callback**             | *&lt;Usa il valore predefinito&gt;*                       |
    | **Criteri di iscrizione o di accesso** | `B2C_1_SiUpIn`                                        |
    | **Reimposta criteri password**     | `B2C_1_SSPR`                                          |
    | **Modificare i criteri del profilo**       | *&lt;lascia vuoto&gt;*                                 |
    
    Selezionare il collegamento **copia** accanto a **URI di risposta** per copiare l'URI di risposta negli Appunti. Selezionare **OK** per chiudere la finestra di dialogo **Modifica autenticazione** . Selezionare **OK** per creare l'app Web.

## <a name="finish-the-b2c-app-registration"></a>Completare la registrazione dell'app B2C

Tornare alla finestra del browser con le proprietà dell'app B2C ancora aperte. Modificare l' **URL di risposta** temporaneo specificato in precedenza per il valore copiato da Visual Studio. Selezionare **Save (Salva** ) nella parte superiore della finestra.

> [!TIP]
> Se l'URL di risposta non è stato copiato, usare l'indirizzo HTTPS dalla scheda debug delle proprietà del progetto Web e aggiungere il valore **CallbackPath** da *appSettings. JSON*.

## <a name="configure-policies"></a>Configurare i criteri

Usare la procedura descritta nella documentazione di Azure AD B2C per [creare un criterio di iscrizione o di accesso](/azure/active-directory-b2c/active-directory-b2c-reference-policies#user-flow-versions), quindi [creare un criterio di reimpostazione della password](/azure/active-directory-b2c/active-directory-b2c-reference-policies#user-flow-versions). Usare i valori di esempio forniti nella documentazione per ** Identity i provider**, **gli attributi di iscrizione**e le **attestazioni dell'applicazione**. Usare il pulsante **Esegui adesso** per testare i criteri come descritto nella documentazione è facoltativo.

> [!WARNING]
> Verificare che i nomi dei criteri siano esattamente come descritto nella documentazione, perché tali criteri sono stati usati nella finestra di dialogo **Cambia autenticazione** in Visual Studio. I nomi dei criteri possono essere verificati in *appSettings. JSON*.

## <a name="configure-the-underlying-openidconnectoptionsjwtbearercookie-options"></a>Configurare le opzioni di OpenIdConnectOptions/JwtBearer/cookie sottostanti

Per configurare direttamente le opzioni sottostanti, utilizzare la costante dello schema `Startup.ConfigureServices`appropriata in:

```csharp
services.Configure<OpenIdConnectOptions>(
    AzureAD[B2C]Defaults.OpenIdScheme, options => 
    {
        // Omitted for brevity
    });

services.Configure<CookieAuthenticationOptions>(
    AzureAD[B2C]Defaults.CookieScheme, options => 
    {
        // Omitted for brevity
    });

services.Configure<JwtBearerOptions>(
    AzureAD[B2C]Defaults.JwtBearerAuthenticationScheme, options => 
    {
        // Omitted for brevity
    });
```

## <a name="run-the-app"></a>Eseguire l'app

In Visual Studio premere **F5** per compilare ed eseguire l'app. Dopo l'avvio dell'app Web, selezionare **Accept (accetta** ) per accettare l'uso dei cookie (se richiesto) e quindi selezionare **Sign in (accedi**).

![Accedi all'app](./azure-ad-b2c/_static/signin.png)

Il browser reindirizza al tenant Azure AD B2C. Accedere con un account esistente (se ne è stato creato il test dei criteri) o selezionare **Iscriviti adesso** per creare un nuovo account. Il collegamento **password dimenticata?** viene usato per reimpostare una password dimenticata.

![Accesso ad Azure AD B2C](./azure-ad-b2c/_static/b2csts.png)

Dopo aver eseguito l'accesso, il browser reindirizza all'app Web.

![Operazione completata](./azure-ad-b2c/_static/success.png)

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono state illustrate le procedure per:

> [!div class="checklist"]
> * Creare un tenant di Azure Active Directory B2C
> * Registrare un'app in Azure AD B2C
> * Usare Visual Studio per creare un'applicazione Web ASP.NET Core configurata per l'uso del tenant Azure AD B2C per l'autenticazione
> * Configurare i criteri che controllano il comportamento del tenant Azure AD B2C

Ora che l'app ASP.NET Core è configurata in modo da usare Azure AD B2C per l'autenticazione, è possibile usare l' [attributo di autorizzazione](xref:security/authorization/simple) per proteggere l'app. Continua a sviluppare l'app imparando a:

* [Personalizzare l'interfaccia utente di Azure ad B2C](/azure/active-directory-b2c/active-directory-b2c-reference-ui-customization).
* [Configurare i requisiti di complessità delle password](/azure/active-directory-b2c/active-directory-b2c-reference-password-complexity).
* [Abilitare l'autenticazione a più fattori](/azure/active-directory-b2c/active-directory-b2c-reference-mfa).
* Configurare provider di identità aggiuntivi, ad esempio [Microsoft](/azure/active-directory-b2c/active-directory-b2c-setup-msa-app), [Facebook](/azure/active-directory-b2c/active-directory-b2c-setup-fb-app), [Google](/azure/active-directory-b2c/active-directory-b2c-setup-goog-app), [Amazon](/azure/active-directory-b2c/active-directory-b2c-setup-amzn-app), [Twitter](/azure/active-directory-b2c/active-directory-b2c-setup-twitter-app)e altri.
* [Usare il API Graph Azure ad](/azure/active-directory-b2c/active-directory-b2c-devquickstarts-graph-dotnet) per recuperare informazioni aggiuntive sull'utente, ad esempio l'appartenenza al gruppo, dal tenant di Azure ad B2C.
* [Proteggere un'API web ASP.NET Core usando Azure ad B2C](https://azure.microsoft.com/resources/samples/active-directory-b2c-dotnetcore-webapi/).
* [Chiamare un'API Web .NET da un'app Web .NET usando Azure ad B2C](/azure/active-directory-b2c/active-directory-b2c-devquickstarts-web-api-dotnet).