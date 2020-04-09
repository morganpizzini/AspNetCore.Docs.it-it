---
title: Panoramica dell'autenticazione di base di ASP.NET
author: mjrousos
description: Informazioni sull'autenticazione in ASP.NET Core.Learn about authentication in ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 03/03/2020
uid: security/authentication/index
ms.openlocfilehash: 404904ecfa30d1fe7e47f0daaa423ddd6f1b06e8
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "79434330"
---
# <a name="overview-of-aspnet-core-authentication"></a>Panoramica dell'autenticazione ASP.NET Core

Di [Mike Rousos](https://github.com/mjrousos)

L'autenticazione è il processo di determinazione dell'identità di un utente. [L'autorizzazione](xref:security/authorization/introduction) è il processo che consente di determinare se un utente ha accesso a una risorsa. In ASP.NET Core l'autenticazione `IAuthenticationService`viene gestita dalla proprietà , utilizzata dal [middleware](xref:fundamentals/middleware/index)di autenticazione. Il servizio di autenticazione usa i gestori di autenticazione registrati per completare le azioni correlate all'autenticazione. Esempi di azioni correlate all'autenticazione includono:Examples of authentication-related actions include:

* Autenticazione di un utente.
* Risposta quando un utente non autenticato tenta di accedere a una risorsa con restrizioni.

I gestori di autenticazione registrati e le relative opzioni di configurazione sono denominati "schemi".

Gli schemi di autenticazione vengono `Startup.ConfigureServices`specificati registrando i servizi di autenticazione in:

* Chiamando un metodo di estensione specifico `services.AddAuthentication` dello schema `AddJwtBearer` `AddCookie`dopo una chiamata a (ad esempio, , ). Questi metodi di estensione utilizzano [AuthenticationBuilder.AddScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder.AddScheme*) per registrare gli schemi con le impostazioni appropriate.
* Meno comunemente, chiamando [AuthenticationBuilder.AddScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder.AddScheme*) direttamente.

Ad esempio, il codice seguente registra i servizi di autenticazione e i gestori per gli schemi di autenticazione dei cookie e del portatore JWT:

```csharp
services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
    .AddJwtBearer(JwtBearerDefaults.AuthenticationScheme, options => Configuration.Bind("JwtSettings", options))
    .AddCookie(CookieAuthenticationDefaults.AuthenticationScheme, options => Configuration.Bind("CookieSettings", options));
```

Il `AddAuthentication` `JwtBearerDefaults.AuthenticationScheme` parametro è il nome dello schema da utilizzare per impostazione predefinita quando non è richiesto uno schema specifico.

Se vengono utilizzati più schemi, i criteri di autorizzazione (o attributi di autorizzazione) possono specificare lo schema di [autenticazione (o schemi)](xref:security/authorization/limitingidentitybyscheme) da cui dipendono per autenticare l'utente. Nell'esempio precedente, lo schema di autenticazione dei`CookieAuthenticationDefaults.AuthenticationScheme` cookie può essere utilizzato specificandone `AddCookie`il nome (per impostazione predefinita, anche se durante la chiamata potrebbe essere fornito un nome diverso).

In alcuni casi, `AddAuthentication` la chiamata a viene effettuata automaticamente da altri metodi di estensione. Ad esempio, quando si `AddAuthentication` utilizza ASP.NET Core [Identity](xref:security/authentication/identity), viene chiamato internamente.

Il middleware autenticazione `Startup.Configure` viene <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> aggiunto chiamando il metodo `IApplicationBuilder`di estensione nell'oggetto . La `UseAuthentication` chiamata registra il middleware che utilizza gli schemi di autenticazione registrati in precedenza. Chiama `UseAuthentication` prima di qualsiasi middleware che dipende dagli utenti autenticati. Quando si usa il `UseAuthentication` routing degli endpoint, la chiamata a deve andare:

* Dopo `UseRouting`, in modo che le informazioni sull'instradamento siano disponibili per le decisioni di autenticazione.
* Prima `UseEndpoints`di , in modo che gli utenti vengano autenticati prima di accedere agli endpoint.

## <a name="authentication-concepts"></a>Concetti relativi all'autenticazione

### <a name="authentication-scheme"></a>Schema di autenticazione

Uno schema di autenticazione è un nome che corrisponde a:

* Gestore di autenticazione
* Opzioni per la configurazione dell'istanza specifica del gestore.

Gli schemi sono utili come meccanismo per fare riferimento all'autenticazione, alla richiesta di verifica e ai comportamenti del gestore associato. Ad esempio, un criterio di autorizzazione può utilizzare i nomi degli schemi per specificare lo schema di autenticazione (o gli schemi) da utilizzare per autenticare l'utente. Quando si configura l'autenticazione, è comune specificare lo schema di autenticazione predefinito. Lo schema predefinito viene utilizzato a meno che una risorsa non richieda uno schema specifico. È anche possibile:

* Specificare schemi predefiniti diversi da utilizzare per le azioni di autenticazione, verifica e proibisci.
* Combinare più schemi in uno utilizzando [gli schemi politici](xref:security/authentication/policyschemes).

### <a name="authentication-handler"></a>Gestore di autenticazione

Un gestore di autenticazione:An authentication handler:

* È un tipo che implementa il comportamento di uno schema.
* Deriva da <xref:Microsoft.AspNetCore.Authentication.IAuthenticationHandler> <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1>o .
* Ha la responsabilità principale di autenticare gli utenti.

In base alla configurazione dello schema di autenticazione e al contesto delle richieste in ingresso, i gestori di autenticazione:Based on the authentication scheme's configuration and the incoming request context, authentication handlers:

* Costruire <xref:Microsoft.AspNetCore.Authentication.AuthenticationTicket> oggetti che rappresentano l'identità dell'utente se l'autenticazione ha esito positivo.
* Restituisce 'nessun risultato' o 'errore' se l'autenticazione ha esito negativo.
* Disporre di metodi per la verifica e non consentire azioni per quando gli utenti tentano di accedere alle risorse:Have methods for challenge and bid actions for when users attempt to access resources:
  * Non sono autorizzati ad accedere (vietano).
  * Quando non sono autenticati (sfida).

### <a name="authenticate"></a>Authenticate

L'azione di autenticazione di uno schema di autenticazione è responsabile della costruzione dell'identità dell'utente in base al contesto della richiesta. Restituisce <xref:Microsoft.AspNetCore.Authentication.AuthenticateResult> un che indica se l'autenticazione ha avuto esito positivo e, in caso affermativo, l'identità dell'utente in un ticket di autenticazione. Vedere <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync%2A>. Gli esempi di autenticazione includono:Authenticate examples include:

* Uno schema di autenticazione dei cookie che costruisce l'identità dell'utente dai cookie.
* Uno schema di connessione JWT che deserializza e convalida di un token di connessione JWT per costruire l'identità dell'utente.

### <a name="challenge"></a>Esercizio

Una richiesta di autenticazione viene richiamata dall'autorizzazione quando un utente non autenticato richiede un endpoint che richiede l'autenticazione. Viene emessa una richiesta di autenticazione, ad esempio quando un utente anonimo richiede una risorsa con restrizioni o fa clic su un collegamento di accesso. L'autorizzazione richiama una richiesta di verifica utilizzando gli schema di autenticazione specificati o il valore predefinito se non ne è stato specificato alcuno. Vedere <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync%2A>. Esempi di problemi di autenticazione includono:Authentication challenge examples include:

* Uno schema di autenticazione dei cookie che reindirizza l'utente a una pagina di accesso.
* Uno schema di connessione JWT che restituisce `www-authenticate: bearer` un risultato 401 con un'intestazione.

Un'azione di verifica deve consentire all'utente di sapere quale meccanismo di autenticazione usare per accedere alla risorsa richiesta.

### <a name="forbid"></a>Proibire

L'azione proibita di uno schema di autenticazione viene chiamata da Authorization quando un utente autenticato tenta di accedere a una risorsa a cui non è consentito accedere. Vedere <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync%2A>. Gli esempi di autenticazione non possono essere:
* Uno schema di autenticazione dei cookie che reindirizza l'utente a una pagina che indica che l'accesso non è consentito.
* Un regime di derivazione JWT che restituisce un risultato 403.
* Schema di autenticazione personalizzato reindirizzamento a una pagina in cui l'utente può richiedere l'accesso alla risorsa.

Un'azione proibita può informare l'utente:

* Sono autenticati.
* Non è consentito accedere alla risorsa richiesta.

Vedere i collegamenti seguenti per le differenze tra la sfida e nonché:

* [Sfidare e proibire con un gestore di risorse operativo](xref:security/authorization/resourcebased#challenge-and-forbid-with-an-operational-resource-handler).
* [Differenze tra sfida e proibire](xref:security/authorization/secure-data#challenge).

## <a name="authentication-providers-per-tenant"></a>Provider di autenticazione per tenant

ASP.NET framework di base non dispone di una soluzione incorporata per l'autenticazione multi-tenant.
Anche se è certamente possibile per i clienti scriverne uno, utilizzando le funzionalità incorporate, si consiglia ai clienti di esaminare [Orchard Core](https://www.orchardcore.net/) per questo scopo.

Orchard Core è:

* Un framework di app modulare open source e multi-tenant creato con ASP.NET Core.
* Un sistema di gestione dei contenuti (CMS) basato su tale framework dell'app.

Vedere l'origine [Orchard Core](https://github.com/OrchardCMS/OrchardCore) per un esempio di provider di autenticazione per tenant.

## <a name="additional-resources"></a>Risorse aggiuntive

* <xref:security/authorization/limitingidentitybyscheme>
* <xref:security/authentication/policyschemes>
* <xref:security/authorization/secure-data>
