---
title: Supporto di Regolamento generale sulla protezione dei dati (GDPR) in ASP.NET Core
author: rick-anderson
description: Informazioni su come accedere ai punti di estensione GDPR in un'app Web ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 07/11/2019
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/gdpr
ms.openlocfilehash: ec65a2c8362c15716bebd6b22f5639785ba74c98
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051005"
---
# <a name="eu-general-data-protection-regulation-gdpr-support-in-aspnet-core"></a>Supporto per Regolamento generale sulla protezione dei dati UE (GDPR) in ASP.NET Core

Autore: [Rick Anderson](https://twitter.com/RickAndMSFT)

ASP.NET Core fornisce API e modelli per aiutare a soddisfare alcuni dei requisiti di [regolamento generale sulla protezione dei dati UE (GDPR)](https://ec.europa.eu/info/law/law-topic/data-protection/reform/what-does-general-data-protection-regulation-gdpr-govern_en) :

::: moniker range=">= aspnetcore-3.0"

* I modelli di progetto includono i punti di estensione e il markup con stub che è possibile sostituire con la privacy e i cookie criteri di utilizzo.
* La pagina *pages/privacy. cshtml* o *views/Home/Privacy. cshtml* Visualizza una pagina che illustra in dettaglio l'informativa sulla privacy del sito.

Per abilitare la cookie funzionalità di consenso predefinita come quella disponibile nei modelli ASP.NET Core 2,2 in un'app generata da un modello ASP.NET Core 3,0:

* Aggiungere `using Microsoft.AspNetCore.Http` all'elenco delle direttive using.
* Aggiungere [ Cookie PolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) a `Startup.ConfigureServices` e [usare i Cookie criteri](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) per `Startup.Configure` :

  [!code-csharp[Main](gdpr/sample/RP3.0/Startup.cs?name=snippet1&highlight=12-19,38)]

* Aggiungere il cookie consenso parziale al file *_Layout. cshtml* :

  [!code-cshtml[Main](gdpr/sample/RP3.0/Pages/Shared/_Layout.cshtml?name=snippet&highlight=4)]

* Aggiungere il file *\_ Cookie ConsentPartial. cshtml* al progetto:

  [!code-cshtml[Main](gdpr/sample/RP3.0/Pages/Shared/_CookieConsentPartial.cshtml)]

* Selezionare la versione di ASP.NET Core 2,2 di questo articolo per leggere la cookie funzionalità di consenso.

::: moniker-end

::: moniker range="= aspnetcore-2.2"

* I modelli di progetto includono i punti di estensione e il markup con stub che è possibile sostituire con la privacy e i cookie criteri di utilizzo.
* Una cookie funzionalità di consenso consente di richiedere (e tenere traccia) il consenso degli utenti per l'archiviazione di informazioni personali. Se un utente non ha acconsentito alla raccolta dei dati e l'app [CheckConsentNeeded](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions.checkconsentneeded) è impostata su `true` , i non essenziali cookie non vengono inviati al browser.
* Cookiei possono essere contrassegnati come essenziali. Gli elementi essenziali cookie vengono inviati al browser anche quando l'utente non ha acconsentito e la verifica è disabilitata.
* [TempData e Session cookie s](#tempdata) non sono funzionali quando il rilevamento è disabilitato.
* La pagina [ Identity Gestisci](#pd) fornisce un collegamento per scaricare ed eliminare i dati utente.

L' [app di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) consente di testare la maggior parte delle API e dei punti di estensione GDPR aggiunti ai modelli ASP.NET Core 2,1. Per le istruzioni di test, vedere il file [Leggimi](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) .

[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) ([procedura per il download](xref:index#how-to-download-a-sample))

## <a name="aspnet-core-gdpr-support-in-template-generated-code"></a>Supporto di ASP.NET Core GDPR nel codice generato dal modello

Razor Le pagine e i progetti MVC creati con i modelli di progetto includono il supporto GDPR seguente:

* I [ Cookie criteri](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) [ Cookie PolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) e Use vengono impostati nella `Startup` classe.
* [Visualizzazione parziale](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper) *\_ Cookie ConsentPartial. cshtml* . In questo file è incluso un pulsante **Accept** . Quando l'utente fa clic sul pulsante **Accept** , viene fornito il consenso per l'archiviazione cookie .
* La pagina *pages/privacy. cshtml* o *views/Home/Privacy. cshtml* Visualizza una pagina che illustra in dettaglio l'informativa sulla privacy del sito. Il file *\_ Cookie ConsentPartial. cshtml* genera un collegamento alla pagina privacy.
* Per le app create con singoli account utente, la pagina Gestisci fornisce collegamenti per scaricare ed eliminare [i dati utente personali](#pd).

### <a name="no-loccookiepolicyoptions-and-useno-loccookiepolicy"></a>CookiePolicyOptions e criteri di utilizzo Cookie

[ Cookie PolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) vengono inizializzati in `Startup.ConfigureServices` :

[!code-csharp[Main](gdpr/sample/Startup.cs?name=snippet1&highlight=14-20)]

[Usa Cookie Il criterio](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) viene chiamato in `Startup.Configure` :

[!code-csharp[](gdpr/sample/Startup.cs?name=snippet1&highlight=51)]

### <a name="_no-loccookieconsentpartialcshtml-partial-view"></a>\_CookieVisualizzazione parziale ConsentPartial. cshtml

Visualizzazione parziale *\_ Cookie ConsentPartial. cshtml* :

[!code-cshtml[](gdpr/sample/RP2.2/Pages/Shared/_CookieConsentPartial.cshtml)]

Questa parte parziale:

* Ottiene lo stato di rilevamento per l'utente. Se l'app è configurata in modo da richiedere il consenso, l'utente deve fornire il consenso prima di cookie poter tenere traccia di s. Se è necessario il consenso, il cookie Pannello di consenso viene fissato nella parte superiore della barra di spostamento creata dal file *\_ layout. cshtml* .
* Fornisce un `<p>` elemento HTML per riepilogare la privacy e i cookie criteri di utilizzo.
* Fornisce un collegamento alla pagina privacy o alla visualizzazione in cui è possibile visualizzare i dettagli dell'informativa sulla privacy del sito.

## <a name="essential-no-loccookies"></a>Essential cookie s

Se cookie non è stato fornito il consenso per archiviare s, cookie al browser vengono inviate solo le chiavi contrassegnate come essenziali. Il codice seguente rende cookie essenziale:

[!code-csharp[Main](gdpr/sample/RP2.2/Pages/Cookie.cshtml.cs?name=snippet1&highlight=5)]

<a name="tempdata"></a>

### <a name="tempdata-provider-and-session-state-no-loccookies-arent-essential"></a>Il provider TempData e lo stato della sessione cookie non sono essenziali

Il [provider TempData](xref:fundamentals/app-state#tempdata) cookie non è essenziale. Se il rilevamento è disabilitato, il provider TempData non funziona. Per abilitare il provider TempData quando il rilevamento è disabilitato, contrassegnare il TempData cookie come essenziale in `Startup.ConfigureServices` :

[!code-csharp[Main](gdpr/sample/RP2.2/Startup.cs?name=snippet1)]

[Stato sessione](xref:fundamentals/app-state) cookie i non sono essenziali. Lo stato della sessione non è funzionante quando il rilevamento è disabilitato. Il codice seguente rende la sessione cookie essenziale:

[!code-csharp[](gdpr/sample/RP2.2/Startup.cs?name=snippet2)]

<a name="pd"></a>

## <a name="personal-data"></a>Dati personali

ASP.NET Core app create con account utente singoli includono il codice per scaricare ed eliminare i dati personali.

Selezionare il nome utente e quindi selezionare **dati personali** :

![Pagina Gestisci dati personali](gdpr/_static/pd.png)

Note:

* Per generare il `Account/Manage` codice, vedere [ Identity impalcatura ](xref:security/authentication/scaffold-identity).
* I collegamenti **Delete** e **download** agiscono solo sui dati di identità predefiniti. È necessario estendere le app che creano dati utente personalizzati per eliminare o scaricare i dati utente personalizzati. Per ulteriori informazioni, vedere [aggiungere, scaricare ed eliminare dati utente personalizzati in Identity ](xref:security/authentication/add-user-data).
* I token salvati per l'utente archiviati nella Identity tabella di database `AspNetUserTokens` vengono eliminati quando l'utente viene eliminato tramite il comportamento di eliminazione a cascata a causa della [chiave esterna](https://github.com/aspnet/Identity/blob/release/2.1/src/EF/IdentityUserContext.cs#L152).
* L' [autenticazione del provider esterno](xref:security/authentication/social/index), ad esempio Facebook e Google, non è disponibile prima che i cookie criteri vengano accettati.

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
