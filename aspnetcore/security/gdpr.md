---
title: Supporto di Regolamento generale sulla protezione dei dati (GDPR) in ASP.NET Core
author: rick-anderson
description: Informazioni su come accedere ai punti di estensione GDPR in un'app Web ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 07/11/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/gdpr
ms.openlocfilehash: 2e21f54ebfcb55be2b97da217b92a39843b5d702
ms.sourcegitcommit: 6c7a149168d2c4d747c36de210bfab3abd60809a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/09/2020
ms.locfileid: "83003207"
---
# <a name="eu-general-data-protection-regulation-gdpr-support-in-aspnet-core"></a>Supporto per Regolamento generale sulla protezione dei dati UE (GDPR) in ASP.NET Core

Autore: [Rick Anderson](https://twitter.com/RickAndMSFT)

ASP.NET Core fornisce API e modelli per aiutare a soddisfare alcuni dei requisiti di [regolamento generale sulla protezione dei dati UE (GDPR)](https://ec.europa.eu/info/law/law-topic/data-protection/reform/what-does-general-data-protection-regulation-gdpr-govern_en) :

::: moniker range=">= aspnetcore-3.0"

* I modelli di progetto includono punti di estensione e markup con stub che è possibile sostituire con i criteri di uso della privacy e dei cookie.
* La pagina *pages/privacy. cshtml* o *views/Home/Privacy. cshtml* Visualizza una pagina che illustra in dettaglio l'informativa sulla privacy del sito.

Per abilitare la funzionalità di consenso dei cookie predefinita come quella disponibile nei modelli ASP.NET Core 2,2 in un'app generata da un modello ASP.NET Core 3,0:

* Aggiungere `using Microsoft.AspNetCore.Http` all'elenco delle direttive using.
* Aggiungere [CookiePolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) a `Startup.ConfigureServices` e [UseCookiePolicy](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) a `Startup.Configure`:

  [!code-csharp[Main](gdpr/sample/RP3.0/Startup.cs?name=snippet1&highlight=12-19,38)]

* Aggiungere il consenso del cookie parziale al file *_Layout. cshtml* :

  [!code-cshtml[Main](gdpr/sample/RP3.0/Pages/Shared/_Layout.cshtml?name=snippet&highlight=4)]

* Aggiungere il * \_file CookieConsentPartial. cshtml* al progetto:

  [!code-cshtml[Main](gdpr/sample/RP3.0/Pages/Shared/_CookieConsentPartial.cshtml)]

* Selezionare la versione di ASP.NET Core 2,2 di questo articolo per informazioni sulla funzionalità di consenso dei cookie.

::: moniker-end

::: moniker range="= aspnetcore-2.2"

* I modelli di progetto includono punti di estensione e markup con stub che è possibile sostituire con i criteri di uso della privacy e dei cookie.
* Una funzionalità di consenso dei cookie consente di richiedere (e tenere traccia) il consenso degli utenti per l'archiviazione di informazioni personali. Se un utente non ha acconsentito alla raccolta dei dati e l'app [CheckConsentNeeded](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions.checkconsentneeded) è impostata su `true`CheckConsentNeeded, i cookie non essenziali non vengono inviati al browser.
* I cookie possono essere contrassegnati come essenziali. I cookie essenziali vengono inviati al browser anche quando l'utente non ha acconsentito e la verifica è disabilitata.
* [TempData e cookie di sessione](#tempdata) non sono funzionali quando il rilevamento è disabilitato.
* La pagina [Gestione identità](#pd) fornisce un collegamento per scaricare ed eliminare i dati utente.

L' [app di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) consente di testare la maggior parte delle API e dei punti di estensione GDPR aggiunti ai modelli ASP.NET Core 2,1. Per le istruzioni di test, vedere il file [Leggimi](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) .

[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) ([procedura per il download](xref:index#how-to-download-a-sample))

## <a name="aspnet-core-gdpr-support-in-template-generated-code"></a>Supporto di ASP.NET Core GDPR nel codice generato dal modello

I progetti Razor Pages e MVC creati con i modelli di progetto includono il supporto GDPR seguente:

* [CookiePolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) e [UseCookiePolicy](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) vengono impostati nella `Startup` classe.
* [Visualizzazione parziale](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper) * \_CookieConsentPartial. cshtml* . In questo file è incluso un pulsante **Accept** . Quando l'utente fa clic sul pulsante **Accept** , viene fornito il consenso per l'archiviazione dei cookie.
* La pagina *pages/privacy. cshtml* o *views/Home/Privacy. cshtml* Visualizza una pagina che illustra in dettaglio l'informativa sulla privacy del sito. Il * \_file CookieConsentPartial. cshtml* genera un collegamento alla pagina privacy.
* Per le app create con singoli account utente, la pagina Gestisci fornisce collegamenti per scaricare ed eliminare [i dati utente personali](#pd).

### <a name="cookiepolicyoptions-and-usecookiepolicy"></a>CookiePolicyOptions e UseCookiePolicy

[CookiePolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) vengono inizializzati `Startup.ConfigureServices`in:

[!code-csharp[Main](gdpr/sample/Startup.cs?name=snippet1&highlight=14-20)]

[UseCookiePolicy](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) viene chiamato in `Startup.Configure`:

[!code-csharp[](gdpr/sample/Startup.cs?name=snippet1&highlight=51)]

### <a name="_cookieconsentpartialcshtml-partial-view"></a>\_Visualizzazione parziale CookieConsentPartial. cshtml

Visualizzazione parziale * \_CookieConsentPartial. cshtml* :

[!code-html[](gdpr/sample/RP2.2/Pages/Shared/_CookieConsentPartial.cshtml)]

Questa parte parziale:

* Ottiene lo stato di rilevamento per l'utente. Se l'app è configurata in modo da richiedere il consenso, l'utente deve fornire il consenso prima di poter tenere traccia dei cookie. Se è necessario il consenso, il pannello di consenso del cookie viene fissato nella parte superiore della barra di spostamento creata dal file * \_layout. cshtml* .
* Fornisce un elemento `<p>` HTML per riepilogare i criteri di utilizzo della privacy e dei cookie.
* Fornisce un collegamento alla pagina privacy o alla visualizzazione in cui è possibile visualizzare i dettagli dell'informativa sulla privacy del sito.

## <a name="essential-cookies"></a>Cookie essenziali

Se non è stato fornito il consenso per l'archiviazione dei cookie, verranno inviati al browser solo i cookie contrassegnati come essenziali. Il codice seguente rende un cookie essenziale:

[!code-csharp[Main](gdpr/sample/RP2.2/Pages/Cookie.cshtml.cs?name=snippet1&highlight=5)]

<a name="tempdata"></a>

### <a name="tempdata-provider-and-session-state-cookies-arent-essential"></a>I cookie di stato della sessione e del provider TempData non sono essenziali

Il cookie del [provider TempData](xref:fundamentals/app-state#tempdata) non è essenziale. Se il rilevamento è disabilitato, il provider TempData non funziona. Per abilitare il provider TempData quando il rilevamento è disabilitato, contrassegnare il cookie TempData `Startup.ConfigureServices`come essenziale in:

[!code-csharp[Main](gdpr/sample/RP2.2/Startup.cs?name=snippet1)]

I cookie di [stato della sessione](xref:fundamentals/app-state) non sono essenziali. Lo stato della sessione non è funzionante quando il rilevamento è disabilitato. Il codice seguente rende essenziali i cookie di sessione:

[!code-csharp[](gdpr/sample/RP2.2/Startup.cs?name=snippet2)]

<a name="pd"></a>

## <a name="personal-data"></a>Dati personali

ASP.NET Core app create con account utente singoli includono il codice per scaricare ed eliminare i dati personali.

Selezionare il nome utente e quindi selezionare **dati personali**:

![Pagina Gestisci dati personali](gdpr/_static/pd.png)

Note:

* Per generare il `Account/Manage` codice, vedere [impalcatura Identity](xref:security/authentication/scaffold-identity).
* I collegamenti **Delete** e **download** agiscono solo sui dati di identità predefiniti. È necessario estendere le app che creano dati utente personalizzati per eliminare o scaricare i dati utente personalizzati. Per altre informazioni, vedere [aggiungere, scaricare ed eliminare dati utente personalizzati in identità](xref:security/authentication/add-user-data).
* I token salvati per l'utente archiviati nella tabella `AspNetUserTokens` del database di identità vengono eliminati quando l'utente viene eliminato tramite il comportamento di eliminazione a cascata a causa della [chiave esterna](https://github.com/aspnet/Identity/blob/release/2.1/src/EF/IdentityUserContext.cs#L152).
* L' [autenticazione del provider esterno](xref:security/authentication/social/index), ad esempio Facebook e Google, non è disponibile prima che i criteri dei cookie vengano accettati.

::: moniker-end

## <a name="encryption-at-rest"></a>Crittografia di dati inattivi

Alcuni database e meccanismi di archiviazione consentono la crittografia dei dati inattivi. Crittografia di dati inattivi:

* Crittografa automaticamente i dati archiviati.
* Crittografa senza configurazione, programmazione o altro lavoro per il software che accede ai dati.
* È l'opzione più semplice e sicura.
* Consente al database di gestire le chiavi e la crittografia.

Ad esempio:

* Microsoft SQL e Azure SQL forniscono [Transparent Data Encryption](/sql/relational-databases/security/encryption/transparent-data-encryption) (Transparent Data Encryption).
* [Per impostazione predefinita, SQL Azure crittografare il database](https://azure.microsoft.com/updates/newly-created-azure-sql-databases-encrypted-by-default/)
* [Archiviazione BLOB, file, tabelle e code di Azure vengono crittografati per impostazione predefinita](https://azure.microsoft.com/blog/announcing-default-encryption-for-azure-blobs-files-table-and-queue-storage/).

Per i database che non forniscono la crittografia incorporata, è possibile usare la crittografia del disco per fornire la stessa protezione. Ad esempio:

* [BitLocker per Windows Server](/windows/security/information-protection/bitlocker/bitlocker-how-to-deploy-on-windows-server)
* Linux:
  * [eCryptfs](https://launchpad.net/ecryptfs)
  * [Encfs](https://github.com/vgough/encfs).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Microsoft.com/GDPR](https://www.microsoft.com/trustcenter/Privacy/GDPR)
* [GDPR-aggiunta di un pulsante di consenso per la revoca in ASP.NET Core](https://www.joeaudette.com/blog/2018/08/28/gdpr---adding-a-revoke-consent-button-in-aspnet-core)
