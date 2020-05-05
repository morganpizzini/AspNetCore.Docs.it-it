---
title: Sicurezza ASP.NET Core Blazor webassembly
author: guardrex
description: Informazioni su come proteggere Blazor le app WebAssemlby come applicazioni a pagina singola (Spa).
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/24/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/blazor/webassembly/index
ms.openlocfilehash: e8ea5e6b6d7e28906e6109e6730ac25f190b4191
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82768000"
---
# <a name="secure-aspnet-core-blazor-webassembly"></a>Sicurezza ASP.NET Core Blazor webassembly

Di [Javier Calvarro Nelson](https://github.com/javiercn)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

BlazorLe app webassembly sono protette in modo analogo alle applicazioni a pagina singola (Spa). Esistono diversi approcci per l'autenticazione degli utenti in Spa, ma l'approccio più comune e completo consiste nell'usare un'implementazione basata sul [protocollo OAuth 2,0](https://oauth.net/), ad esempio [Open ID Connect (OIDC)](https://openid.net/connect/).

## <a name="authentication-library"></a>Libreria di autenticazione

BlazorWebassembly supporta l'autenticazione e l'autorizzazione delle app usando OIDC tramite `Microsoft.AspNetCore.Components.WebAssembly.Authentication` la libreria. La libreria fornisce un set di primitive per l'autenticazione uniforme rispetto a ASP.NET Core backend. La libreria integra ASP.NET Core Identity con il supporto dell' [ Identity ](https://identityserver.io/)autorizzazione API basato sul server. La libreria può eseguire l'autenticazione per qualsiasi provider di Identity terze parti (IP) che supporta OIDC, chiamati provider OPENID (op).

Il supporto per l' Blazor autenticazione in webassembly è basato sulla libreria *oidc-client. js* , che consente di gestire i dettagli del protocollo di autenticazione sottostante.

Sono disponibili altre opzioni per l'autenticazione di Spa, ad esempio l'uso di cookie navigava sullostesso sito. Tuttavia, la progettazione ingegneristica Blazor di webassembly viene stabilita in OAuth e OIDC come opzione migliore per l' Blazor autenticazione nelle app webassembly. È stata scelta [l'autenticazione](xref:security/anti-request-forgery#token-based-authentication) basata su token basata su [token Web JSON (token JWT)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) tramite [l'autenticazione basata su cookie](xref:security/anti-request-forgery#cookie-based-authentication) per motivi funzionali e di sicurezza:

* L'uso di un protocollo basato su token offre una superficie di attacco più piccola, poiché i token non vengono inviati in tutte le richieste.
* Gli endpoint server non richiedono la protezione da [richieste intersito falsificazione (CSRF)](xref:security/anti-request-forgery) perché i token vengono inviati in modo esplicito. In questo modo è possibile Blazor ospitare le app webassembly insieme Razor alle app MVC o Pages.
* I token hanno autorizzazioni più strette rispetto ai cookie. Ad esempio, non è possibile usare i token per gestire l'account utente o modificare la password di un utente, a meno che tale funzionalità non venga implementata in modo esplicito.
* I token hanno una durata breve, un'ora per impostazione predefinita, che limita la finestra di attacco. I token possono anche essere revocati in qualsiasi momento.
* Il token JWT autonomo offre garanzie al client e al server per il processo di autenticazione. Un client, ad esempio, è in grado di rilevare e verificare che i token ricevuti siano legittimi e che siano stati emessi come parte di un determinato processo di autenticazione. Se una terza parte tenta di cambiare un token durante il processo di autenticazione, il client può rilevare il token cambiato ed evitare di usarlo.
* I token con OAuth e OIDC non si basano sulla corretta presenza dell'agente utente per assicurarsi che l'applicazione sia protetta.
* I protocolli basati su token, ad esempio OAuth e OIDC, consentono di autenticare e autorizzare app ospitate e autonome con lo stesso set di caratteristiche di sicurezza.

## <a name="authentication-process-with-oidc"></a>Processo di autenticazione con OIDC

La `Microsoft.AspNetCore.Components.WebAssembly.Authentication` libreria offre diverse primitive per implementare l'autenticazione e l'autorizzazione usando OIDC. In termini generali, l'autenticazione funziona nel modo seguente:

* Quando un utente anonimo seleziona il pulsante di accesso o richiede una pagina con [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) l'attributo applicato, l'utente viene reindirizzato alla pagina di accesso`/authentication/login`dell'app ().
* Nella pagina di accesso, la libreria di autenticazione prepara un reindirizzamento all'endpoint di autorizzazione. L'endpoint di Blazor autorizzazione è esterno all'app webassembly e può essere ospitato a un'origine separata. L'endpoint è responsabile per determinare se l'utente è autenticato e per emettere uno o più token in risposta. La libreria di autenticazione fornisce un callback di accesso per ricevere la risposta di autenticazione.
  * Se l'utente non è autenticato, l'utente viene reindirizzato al sistema di autenticazione sottostante, che Identityin genere è ASP.NET Core.
  * Se l'utente è già stato autenticato, l'endpoint di autorizzazione genera i token appropriati e reindirizza di nuovo il browser all'endpoint di callback dell'account`/authentication/login-callback`di accesso ().
* Blazor Quando l'app webassembly carica l'endpoint di callback dell'`/authentication/login-callback`account di accesso (), viene elaborata la risposta di autenticazione.
  * Se il processo di autenticazione viene completato correttamente, l'utente viene autenticato e, facoltativamente, restituito all'URL protetto originale richiesto dall'utente.
  * Se il processo di autenticazione ha esito negativo per qualsiasi motivo, l'utente viene inviato alla pagina`/authentication/login-failed`di accesso non riuscita () e viene visualizzato un errore.

## <a name="additional-resources"></a>Risorse aggiuntive

* Gli articoli in questa *Panoramica* forniscono informazioni sull'autenticazione degli utenti Blazor nelle app webassembly su provider specifici.
* <xref:security/blazor/webassembly/additional-scenarios>
