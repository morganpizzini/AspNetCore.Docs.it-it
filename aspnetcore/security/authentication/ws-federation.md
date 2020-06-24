---
title: Autenticare gli utenti con WS-Federation in ASP.NET Core
author: chlowell
description: Questa esercitazione illustra come usare WS-Federation in un'app ASP.NET Core.
ms.author: scaddie
ms.custom: mvc
ms.date: 01/16/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/ws-federation
ms.openlocfilehash: 62b8e33d8b7eb17a65a7a54df2a9aa298acdfe36
ms.sourcegitcommit: 5e462c3328c70f95969d02adce9c71592049f54c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/24/2020
ms.locfileid: "85292814"
---
# <a name="authenticate-users-with-ws-federation-in-aspnet-core"></a>Autenticare gli utenti con WS-Federation in ASP.NET Core

Questa esercitazione illustra come consentire agli utenti di accedere con un provider di autenticazione WS-Federation, ad esempio Active Directory Federation Services (ADFS) o [Azure Active Directory](/azure/active-directory/) (AAD). Usa l'app di esempio ASP.NET Core descritta in [Facebook, Google e l'autenticazione del provider esterno](xref:security/authentication/social/index).

Per le app ASP.NET Core, il supporto di WS-Federation viene fornito da [Microsoft. AspNetCore. Authentication. WSFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation). Questo componente viene trasferito da [Microsoft. Owin. Security. WSFederation](https://www.nuget.org/packages/Microsoft.Owin.Security.WsFederation) e condivide molti dei meccanismi di tale componente. Tuttavia, i componenti differiscono in un paio di modi importanti.

Per impostazione predefinita, il nuovo middleware:

* Non consente accessi non richiesti. Questa funzionalità del protocollo WS-Federation è vulnerabile agli attacchi XSRF. Tuttavia, può essere abilitata con l' `AllowUnsolicitedLogins` opzione.
* Non controlla ogni post di form per i messaggi di accesso. Vengono verificate solo le richieste per gli `CallbackPath` accessi. il `CallbackPath` valore predefinito è, `/signin-wsfed` ma può essere modificato tramite la proprietà [RemoteAuthenticationOptions. CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) ereditata della classe [WsFederationOptions](/dotnet/api/microsoft.aspnetcore.authentication.wsfederation.wsfederationoptions) . Questo percorso può essere condiviso con altri provider di autenticazione abilitando l'opzione [SkipUnrecognizedRequests](/dotnet/api/microsoft.aspnetcore.authentication.wsfederation.wsfederationoptions.skipunrecognizedrequests) .

## <a name="register-the-app-with-active-directory"></a>Registrare l'app con Active Directory

### <a name="active-directory-federation-services"></a>Active Directory Federation Services

* Aprire l' **Aggiunta guidata del trust della relying party** dalla console di gestione di ADFS:

![Aggiunta guidata trust della relying party: Benvenuti](ws-federation/_static/AdfsAddTrust.png)

* Scegliere di immettere manualmente i dati:

![Aggiunta guidata attendibilità relying party: selezionare l'origine dati](ws-federation/_static/AdfsSelectDataSource.png)

* Immettere un nome visualizzato per il relying party. Il nome non è importante per l'app ASP.NET Core.

* [Microsoft. AspNetCore. Authentication. WSFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation) non dispone del supporto per la crittografia dei token, quindi non configurare un certificato di crittografia del token:

![Aggiunta guidata trust della relying party: configurare il certificato](ws-federation/_static/AdfsConfigureCert.png)

* Abilitare il supporto per il protocollo passivo WS-Federation usando l'URL dell'app. Verificare che la porta sia corretta per l'app:

![Aggiunta guidata attendibilità relying party: Configura URL](ws-federation/_static/AdfsConfigureUrl.png)

> [!NOTE]
> Deve essere un URL HTTPS. IIS Express possibile fornire un certificato autofirmato quando si ospita l'app durante lo sviluppo. Il gheppio richiede la configurazione manuale del certificato. Per altri dettagli, vedere la [documentazione di Gheppio](xref:fundamentals/servers/kestrel) .

* Fare clic su **Avanti** nel resto della procedura guidata e **chiudere** alla fine.

* ASP.NET Core Identity richiede l'attestazione **ID nome** . Aggiungerne uno dalla finestra di dialogo **modifica regole attestazione** :

![Modifica regole attestazione](ws-federation/_static/EditClaimRules.png)

* Nella **procedura guidata Aggiungi regola attestazione di trasformazione**lasciare selezionata l'opzione predefinita **Invia attributi LDAP come modello di attestazioni** e fare clic su **Avanti**. Aggiungere una regola mapping dell'attributo LDAP **SAM-account-name** all'attestazione in uscita **ID nome** :

![Aggiunta guidata regola attestazione di trasformazione: Configura regola attestazione](ws-federation/_static/AddTransformClaimRule.png)

* Fare clic su **fine**  >  **OK** nella finestra **modifica regole attestazione** .

### <a name="azure-active-directory"></a>Azure Active Directory

* Passare al pannello registrazioni per l'app del tenant AAD. Fare clic su **registrazione nuova applicazione**:

![Azure Active Directory: Registrazioni app](ws-federation/_static/AadNewAppRegistration.png)

* Immettere un nome per la registrazione dell'app. Questa operazione non è importante per l'app ASP.NET Core.
* Immettere l'URL su cui è in ascolto l'app come **URL di accesso**:

![Azure Active Directory: creare la registrazione dell'app](ws-federation/_static/AadCreateAppRegistration.png)

* Fare clic su **endpoint** e prendere nota dell'URL del **documento dei metadati di Federazione** . Si tratta del middleware WS-Federation `MetadataAddress` :

![Azure Active Directory: endpoint](ws-federation/_static/AadFederationMetadataDocument.png)

* Passare alla registrazione della nuova app. Fare clic su **esporre un'API**. Fare clic su ID applicazione URI **set**  >  **Save**. Prendere nota dell' **URI dell'ID applicazione**. Si tratta del middleware WS-Federation `Wtrealm` :

![Azure Active Directory: Proprietà registrazione app](ws-federation/_static/AadAppIdUri.png)

## <a name="use-ws-federation-without-aspnet-core-identity"></a>Usare WS-Federation senza ASP.NET CoreIdentity

Il middleware WS-Federation può essere utilizzato senza Identity . Ad esempio:
::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](ws-federation/samples/StartupNon31.cs?name=snippet)]
::: moniker-end

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"
[!code-csharp[](ws-federation/samples/StartupNon21.cs?name=snippet)]
::: moniker-end

## <a name="add-ws-federation-as-an-external-login-provider-for-aspnet-core-identity"></a>Aggiungere WS-Federation come provider di accesso esterno per ASP.NET CoreIdentity

* Aggiungere una dipendenza da [Microsoft. AspNetCore. Authentication. WSFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation) al progetto.
* Aggiungi WS-Federation a `Startup.ConfigureServices` :

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](ws-federation/samples/Startup31.cs?name=snippet)]
::: moniker-end

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"
[!code-csharp[](ws-federation/samples/Startup21.cs?name=snippet)]
::: moniker-end

[!INCLUDE [default settings configuration](social/includes/default-settings.md)]

### <a name="log-in-with-ws-federation"></a>Accedere con WS-Federation

Passare all'app e fare clic sul collegamento **Accedi** nell'intestazione NAV. È disponibile un'opzione per l'accesso con WsFederation: ![ pagina di accesso](ws-federation/_static/WsFederationButton.png)

Con ADFS come provider, il pulsante reindirizza a una pagina di accesso ad ADFS: pagina di accesso ad ADFS ![](ws-federation/_static/AdfsLoginPage.png)

Con Azure Active Directory come provider, il pulsante reindirizza a una pagina di accesso di AAD: pagina di accesso ad Aad ![](ws-federation/_static/AadSignIn.png)

Un accesso riuscito per un nuovo utente viene reindirizzato alla pagina di registrazione dell'utente dell'app: ![ pagina Register](ws-federation/_static/Register.png)
