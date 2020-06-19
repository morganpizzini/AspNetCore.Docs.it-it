---
title: Sicurezza ASP.NET Core Blazor Webassembly
author: guardrex
description: Informazioni su come proteggere Blazor le app WebAssemlby come applicazioni a pagina singola (Spa).
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/01/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/security/webassembly/index
ms.openlocfilehash: 5b20ab96f5419a86ab162fdcf7a57020f6aa7227
ms.sourcegitcommit: 490434a700ba8c5ed24d849bd99d8489858538e3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/19/2020
ms.locfileid: "85103964"
---
# <a name="secure-aspnet-core-blazor-webassembly"></a>Sicurezza ASP.NET Core Blazor Webassembly

Di [Javier Calvarro Nelson](https://github.com/javiercn)

BlazorLe app webassembly sono protette in modo analogo alle applicazioni a pagina singola (Spa). Esistono diversi approcci per l'autenticazione degli utenti in Spa, ma l'approccio più comune e completo consiste nell'usare un'implementazione basata sul [protocollo OAuth 2,0](https://oauth.net/), ad esempio [Open ID Connect (OIDC)](https://openid.net/connect/).

## <a name="authentication-library"></a>Libreria di autenticazione

BlazorWebassembly supporta l'autenticazione e l'autorizzazione delle app usando OIDC tramite la libreria [Microsoft. AspNetCore. Components. webassembly. Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) . La libreria fornisce un set di primitive per l'autenticazione uniforme rispetto a ASP.NET Core backend. La libreria integra ASP.NET Core Identity con il supporto dell'autorizzazione API basato sul [ Identity server](https://identityserver.io/). La libreria può eseguire l'autenticazione per qualsiasi provider di terze parti Identity (IP) che supporta OIDC, chiamati provider OpenID (op).

Il supporto per l'autenticazione in Blazor webassembly è basato sulla libreria *oidc-client.js* , che consente di gestire i dettagli del protocollo di autenticazione sottostante.

Sono disponibili altre opzioni per l'autenticazione di Spa, ad esempio l'uso di cookie navigava sullostesso sito. Tuttavia, la progettazione ingegneristica di Blazor webassembly viene stabilita in OAuth e OIDC come opzione migliore per l'autenticazione nelle Blazor app webassembly. È stata scelta [l'autenticazione](xref:security/anti-request-forgery#token-based-authentication) basata su token basata su [token Web JSON (token JWT)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) tramite [l'autenticazione basata su cookie](xref:security/anti-request-forgery#cookie-based-authentication) per motivi funzionali e di sicurezza:

* L'uso di un protocollo basato su token offre una superficie di attacco più piccola, poiché i token non vengono inviati in tutte le richieste.
* Gli endpoint server non richiedono la protezione da [richieste intersito falsificazione (CSRF)](xref:security/anti-request-forgery) perché i token vengono inviati in modo esplicito. In questo modo è possibile ospitare le Blazor app webassembly insieme alle app MVC o Razor pages.
* I token hanno autorizzazioni più strette rispetto ai cookie. Ad esempio, non è possibile usare i token per gestire l'account utente o modificare la password di un utente, a meno che tale funzionalità non venga implementata in modo esplicito.
* I token hanno una durata breve, un'ora per impostazione predefinita, che limita la finestra di attacco. I token possono anche essere revocati in qualsiasi momento.
* Il token JWT autonomo offre garanzie al client e al server per il processo di autenticazione. Un client, ad esempio, è in grado di rilevare e verificare che i token ricevuti siano legittimi e che siano stati emessi come parte di un determinato processo di autenticazione. Se una terza parte tenta di cambiare un token durante il processo di autenticazione, il client può rilevare il token cambiato ed evitare di usarlo.
* I token con OAuth e OIDC non si basano sulla corretta presenza dell'agente utente per assicurarsi che l'applicazione sia protetta.
* I protocolli basati su token, ad esempio OAuth e OIDC, consentono di autenticare e autorizzare app ospitate e autonome con lo stesso set di caratteristiche di sicurezza.

## <a name="authentication-process-with-oidc"></a>Processo di autenticazione con OIDC

La libreria [Microsoft. AspNetCore. Components. webassembly. Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) offre diverse primitive per implementare l'autenticazione e l'autorizzazione usando OIDC. In termini generali, l'autenticazione funziona nel modo seguente:

* Quando un utente anonimo seleziona il pulsante di accesso o richiede una pagina con l' [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attributo applicato, l'utente viene reindirizzato alla pagina di accesso dell'app ( `/authentication/login` ).
* Nella pagina di accesso, la libreria di autenticazione prepara un reindirizzamento all'endpoint di autorizzazione. L'endpoint di autorizzazione è esterno all' Blazor app webassembly e può essere ospitato a un'origine separata. L'endpoint è responsabile per determinare se l'utente è autenticato e per emettere uno o più token in risposta. La libreria di autenticazione fornisce un callback di accesso per ricevere la risposta di autenticazione.
  * Se l'utente non è autenticato, l'utente viene reindirizzato al sistema di autenticazione sottostante, che in genere è ASP.NET Core Identity .
  * Se l'utente è già stato autenticato, l'endpoint di autorizzazione genera i token appropriati e reindirizza di nuovo il browser all'endpoint di callback dell'account di accesso ( `/authentication/login-callback` ).
* Quando l' Blazor app webassembly carica l'endpoint di callback dell'account di accesso ( `/authentication/login-callback` ), viene elaborata la risposta di autenticazione.
  * Se il processo di autenticazione viene completato correttamente, l'utente viene autenticato e, facoltativamente, restituito all'URL protetto originale richiesto dall'utente.
  * Se il processo di autenticazione ha esito negativo per qualsiasi motivo, l'utente viene inviato alla pagina di accesso non riuscita ( `/authentication/login-failed` ) e viene visualizzato un errore.

## <a name="authorization"></a>Autorizzazione

Nelle Blazor app webassembly i controlli di autorizzazione possono essere ignorati perché tutto il codice lato client può essere modificato dagli utenti. Lo stesso vale per tutte le tecnologie per app sul lato client, tra cui i framework JavaScript SPA o le app native per qualsiasi sistema operativo.

**Eseguire sempre i controlli di autorizzazione nel server all'interno degli eventuali endpoint dell'API a cui accede l'app sul lato client.**

## <a name="refresh-tokens"></a>Token di aggiornamento

I token di aggiornamento non possono essere protetti sul lato client nelle Blazor app webassembly. Pertanto, i token di aggiornamento non devono essere inviati all'app per l'uso diretto.

I token di aggiornamento possono essere gestiti e usati dall'app sul lato server in una Blazor soluzione di assembly Web ospitata per accedere alle API di terze parti. Per altre informazioni, vedere <xref:blazor/security/webassembly/additional-scenarios#authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party>.

## <a name="implementation-guidance"></a>Indicazioni relative all'implementazione

Gli articoli in questa *Panoramica* forniscono informazioni sull'autenticazione degli utenti nelle Blazor app webassembly su provider specifici.

BlazorApp Webassembly autonome:

* [Linee guida generali per i provider OIDC e la libreria di autenticazione webassembly](xref:blazor/security/webassembly/standalone-with-authentication-library)
* [Account Microsoft](xref:blazor/security/webassembly/standalone-with-microsoft-accounts)
* [Azure Active Directory (AAD)](xref:blazor/security/webassembly/standalone-with-azure-active-directory)
* [Azure Active Directory (AAD) B2C](xref:blazor/security/webassembly/standalone-with-azure-active-directory-b2c)

BlazorApp Webassembly ospitate:

* [Azure Active Directory (AAD)](xref:blazor/security/webassembly/hosted-with-azure-active-directory)
* [Azure Active Directory (AAD) B2C](xref:blazor/security/webassembly/hosted-with-azure-active-directory-b2c)
* [IdentityServer](xref:blazor/security/webassembly/hosted-with-identity-server)

Per ulteriori informazioni sulla configurazione, vedere <xref:blazor/security/webassembly/additional-scenarios> .
