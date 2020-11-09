---
title: Supporto di Regolamento generale sulla protezione dei dati (GDPR) in ASP.NET Core
author: rick-anderson
description: Informazioni su come accedere ai punti di estensione GDPR in un'app Web ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 07/11/2019
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: security/gdpr
ms.openlocfilehash: ec65a2c8362c15716bebd6b22f5639785ba74c98
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051005"
---
# <a name="eu-general-data-protection-regulation-gdpr-support-in-aspnet-core"></a><span data-ttu-id="9a308-103">Supporto per Regolamento generale sulla protezione dei dati UE (GDPR) in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9a308-103">EU General Data Protection Regulation (GDPR) support in ASP.NET Core</span></span>

<span data-ttu-id="9a308-104">Autore: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="9a308-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="9a308-105">ASP.NET Core fornisce API e modelli per aiutare a soddisfare alcuni dei requisiti di [regolamento generale sulla protezione dei dati UE (GDPR)](https://ec.europa.eu/info/law/law-topic/data-protection/reform/what-does-general-data-protection-regulation-gdpr-govern_en) :</span><span class="sxs-lookup"><span data-stu-id="9a308-105">ASP.NET Core provides APIs and templates to help meet some of the [EU General Data Protection Regulation (GDPR)](https://ec.europa.eu/info/law/law-topic/data-protection/reform/what-does-general-data-protection-regulation-gdpr-govern_en) requirements:</span></span>

::: moniker range=">= aspnetcore-3.0"

* <span data-ttu-id="9a308-106">I modelli di progetto includono i punti di estensione e il markup con stub che è possibile sostituire con la privacy e i cookie criteri di utilizzo.</span><span class="sxs-lookup"><span data-stu-id="9a308-106">The project templates include extension points and stubbed markup that you can replace with your privacy and cookie use policy.</span></span>
* <span data-ttu-id="9a308-107">La pagina *pages/privacy. cshtml* o *views/Home/Privacy. cshtml* Visualizza una pagina che illustra in dettaglio l'informativa sulla privacy del sito.</span><span class="sxs-lookup"><span data-stu-id="9a308-107">The *Pages/Privacy.cshtml* page or *Views/Home/Privacy.cshtml* view provides a page to detail your site's privacy policy.</span></span>

<span data-ttu-id="9a308-108">Per abilitare la cookie funzionalità di consenso predefinita come quella disponibile nei modelli ASP.NET Core 2,2 in un'app generata da un modello ASP.NET Core 3,0:</span><span class="sxs-lookup"><span data-stu-id="9a308-108">To enable the default cookie consent feature like that found in the ASP.NET Core 2.2 templates in an ASP.NET Core 3.0 template generated app:</span></span>

* <span data-ttu-id="9a308-109">Aggiungere `using Microsoft.AspNetCore.Http` all'elenco delle direttive using.</span><span class="sxs-lookup"><span data-stu-id="9a308-109">Add `using Microsoft.AspNetCore.Http` to the list of using directives.</span></span>
* <span data-ttu-id="9a308-110">Aggiungere [ Cookie PolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) a `Startup.ConfigureServices` e [usare i Cookie criteri](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) per `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="9a308-110">Add [CookiePolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) to `Startup.ConfigureServices` and [UseCookiePolicy](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) to `Startup.Configure`:</span></span>

  [!code-csharp[Main](gdpr/sample/RP3.0/Startup.cs?name=snippet1&highlight=12-19,38)]

* <span data-ttu-id="9a308-111">Aggiungere il cookie consenso parziale al file *_Layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="9a308-111">Add the cookie consent partial to the *_Layout.cshtml* file:</span></span>

  [!code-cshtml[Main](gdpr/sample/RP3.0/Pages/Shared/_Layout.cshtml?name=snippet&highlight=4)]

* <span data-ttu-id="9a308-112">Aggiungere il file *\_ Cookie ConsentPartial. cshtml* al progetto:</span><span class="sxs-lookup"><span data-stu-id="9a308-112">Add the *\_CookieConsentPartial.cshtml* file to the project:</span></span>

  [!code-cshtml[Main](gdpr/sample/RP3.0/Pages/Shared/_CookieConsentPartial.cshtml)]

* <span data-ttu-id="9a308-113">Selezionare la versione di ASP.NET Core 2,2 di questo articolo per leggere la cookie funzionalità di consenso.</span><span class="sxs-lookup"><span data-stu-id="9a308-113">Select the ASP.NET Core 2.2 version of this article to read about the cookie consent feature.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

* <span data-ttu-id="9a308-114">I modelli di progetto includono i punti di estensione e il markup con stub che è possibile sostituire con la privacy e i cookie criteri di utilizzo.</span><span class="sxs-lookup"><span data-stu-id="9a308-114">The project templates include extension points and stubbed markup that you can replace with your privacy and cookie use policy.</span></span>
* <span data-ttu-id="9a308-115">Una cookie funzionalità di consenso consente di richiedere (e tenere traccia) il consenso degli utenti per l'archiviazione di informazioni personali.</span><span class="sxs-lookup"><span data-stu-id="9a308-115">A cookie consent feature allows you to ask for (and track) consent from your users for storing personal information.</span></span> <span data-ttu-id="9a308-116">Se un utente non ha acconsentito alla raccolta dei dati e l'app [CheckConsentNeeded](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions.checkconsentneeded) è impostata su `true` , i non essenziali cookie non vengono inviati al browser.</span><span class="sxs-lookup"><span data-stu-id="9a308-116">If a user hasn't consented to data collection and the app has [CheckConsentNeeded](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions.checkconsentneeded) set to `true`, non-essential cookies aren't sent to the browser.</span></span>
* <span data-ttu-id="9a308-117">Cookiei possono essere contrassegnati come essenziali.</span><span class="sxs-lookup"><span data-stu-id="9a308-117">Cookies can be marked as essential.</span></span> <span data-ttu-id="9a308-118">Gli elementi essenziali cookie vengono inviati al browser anche quando l'utente non ha acconsentito e la verifica è disabilitata.</span><span class="sxs-lookup"><span data-stu-id="9a308-118">Essential cookies are sent to the browser even when the user hasn't consented and tracking is disabled.</span></span>
* <span data-ttu-id="9a308-119">[TempData e Session cookie s](#tempdata) non sono funzionali quando il rilevamento è disabilitato.</span><span class="sxs-lookup"><span data-stu-id="9a308-119">[TempData and Session cookies](#tempdata) aren't functional when tracking is disabled.</span></span>
* <span data-ttu-id="9a308-120">La pagina [ Identity Gestisci](#pd) fornisce un collegamento per scaricare ed eliminare i dati utente.</span><span class="sxs-lookup"><span data-stu-id="9a308-120">The [Identity manage](#pd) page provides a link to download and delete user data.</span></span>

<span data-ttu-id="9a308-121">L' [app di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) consente di testare la maggior parte delle API e dei punti di estensione GDPR aggiunti ai modelli ASP.NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="9a308-121">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) allows you test most of the GDPR extension points and APIs added to the ASP.NET Core 2.1 templates.</span></span> <span data-ttu-id="9a308-122">Per le istruzioni di test, vedere il file [Leggimi](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) .</span><span class="sxs-lookup"><span data-stu-id="9a308-122">See the [ReadMe](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) file for testing instructions.</span></span>

<span data-ttu-id="9a308-123">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="9a308-123">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="aspnet-core-gdpr-support-in-template-generated-code"></a><span data-ttu-id="9a308-124">Supporto di ASP.NET Core GDPR nel codice generato dal modello</span><span class="sxs-lookup"><span data-stu-id="9a308-124">ASP.NET Core GDPR support in template-generated code</span></span>

<span data-ttu-id="9a308-125">Razor Le pagine e i progetti MVC creati con i modelli di progetto includono il supporto GDPR seguente:</span><span class="sxs-lookup"><span data-stu-id="9a308-125">Razor Pages and MVC projects created with the project templates include the following GDPR support:</span></span>

* <span data-ttu-id="9a308-126">I [ Cookie criteri](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) [ Cookie PolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) e Use vengono impostati nella `Startup` classe.</span><span class="sxs-lookup"><span data-stu-id="9a308-126">[CookiePolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) and [UseCookiePolicy](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) are set in the `Startup` class.</span></span>
* <span data-ttu-id="9a308-127">[Visualizzazione parziale](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper) *\_ Cookie ConsentPartial. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="9a308-127">The *\_CookieConsentPartial.cshtml* [partial view](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper).</span></span> <span data-ttu-id="9a308-128">In questo file è incluso un pulsante **Accept** .</span><span class="sxs-lookup"><span data-stu-id="9a308-128">An **Accept** button is included in this file.</span></span> <span data-ttu-id="9a308-129">Quando l'utente fa clic sul pulsante **Accept** , viene fornito il consenso per l'archiviazione cookie .</span><span class="sxs-lookup"><span data-stu-id="9a308-129">When the user clicks the **Accept** button, consent to store cookies is provided.</span></span>
* <span data-ttu-id="9a308-130">La pagina *pages/privacy. cshtml* o *views/Home/Privacy. cshtml* Visualizza una pagina che illustra in dettaglio l'informativa sulla privacy del sito.</span><span class="sxs-lookup"><span data-stu-id="9a308-130">The *Pages/Privacy.cshtml* page or *Views/Home/Privacy.cshtml* view provides a page to detail your site's privacy policy.</span></span> <span data-ttu-id="9a308-131">Il file *\_ Cookie ConsentPartial. cshtml* genera un collegamento alla pagina privacy.</span><span class="sxs-lookup"><span data-stu-id="9a308-131">The *\_CookieConsentPartial.cshtml* file generates a link to the Privacy page.</span></span>
* <span data-ttu-id="9a308-132">Per le app create con singoli account utente, la pagina Gestisci fornisce collegamenti per scaricare ed eliminare [i dati utente personali](#pd).</span><span class="sxs-lookup"><span data-stu-id="9a308-132">For apps created with individual user accounts, the Manage page provides links to download and delete [personal user data](#pd).</span></span>

### <a name="no-loccookiepolicyoptions-and-useno-loccookiepolicy"></a><span data-ttu-id="9a308-133">CookiePolicyOptions e criteri di utilizzo Cookie</span><span class="sxs-lookup"><span data-stu-id="9a308-133">CookiePolicyOptions and UseCookiePolicy</span></span>

<span data-ttu-id="9a308-134">[ Cookie PolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) vengono inizializzati in `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="9a308-134">[CookiePolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) are initialized in `Startup.ConfigureServices`:</span></span>

[!code-csharp[Main](gdpr/sample/Startup.cs?name=snippet1&highlight=14-20)]

<span data-ttu-id="9a308-135">[Usa Cookie Il criterio](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) viene chiamato in `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="9a308-135">[UseCookiePolicy](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) is called in `Startup.Configure`:</span></span>

[!code-csharp[](gdpr/sample/Startup.cs?name=snippet1&highlight=51)]

### <a name="_no-loccookieconsentpartialcshtml-partial-view"></a><span data-ttu-id="9a308-136">\_CookieVisualizzazione parziale ConsentPartial. cshtml</span><span class="sxs-lookup"><span data-stu-id="9a308-136">\_CookieConsentPartial.cshtml partial view</span></span>

<span data-ttu-id="9a308-137">Visualizzazione parziale *\_ Cookie ConsentPartial. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="9a308-137">The *\_CookieConsentPartial.cshtml* partial view:</span></span>

[!code-cshtml[](gdpr/sample/RP2.2/Pages/Shared/_CookieConsentPartial.cshtml)]

<span data-ttu-id="9a308-138">Questa parte parziale:</span><span class="sxs-lookup"><span data-stu-id="9a308-138">This partial:</span></span>

* <span data-ttu-id="9a308-139">Ottiene lo stato di rilevamento per l'utente.</span><span class="sxs-lookup"><span data-stu-id="9a308-139">Obtains the state of tracking for the user.</span></span> <span data-ttu-id="9a308-140">Se l'app è configurata in modo da richiedere il consenso, l'utente deve fornire il consenso prima di cookie poter tenere traccia di s.</span><span class="sxs-lookup"><span data-stu-id="9a308-140">If the app is configured to require consent, the user must consent before cookies can be tracked.</span></span> <span data-ttu-id="9a308-141">Se è necessario il consenso, il cookie Pannello di consenso viene fissato nella parte superiore della barra di spostamento creata dal file *\_ layout. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="9a308-141">If consent is required, the cookie consent panel is fixed at top of the navigation bar created by the *\_Layout.cshtml* file.</span></span>
* <span data-ttu-id="9a308-142">Fornisce un `<p>` elemento HTML per riepilogare la privacy e i cookie criteri di utilizzo.</span><span class="sxs-lookup"><span data-stu-id="9a308-142">Provides an HTML `<p>` element to summarize your privacy and cookie use policy.</span></span>
* <span data-ttu-id="9a308-143">Fornisce un collegamento alla pagina privacy o alla visualizzazione in cui è possibile visualizzare i dettagli dell'informativa sulla privacy del sito.</span><span class="sxs-lookup"><span data-stu-id="9a308-143">Provides a link to Privacy page or view where you can detail your site's privacy policy.</span></span>

## <a name="essential-no-loccookies"></a><span data-ttu-id="9a308-144">Essential cookie s</span><span class="sxs-lookup"><span data-stu-id="9a308-144">Essential cookies</span></span>

<span data-ttu-id="9a308-145">Se cookie non è stato fornito il consenso per archiviare s, cookie al browser vengono inviate solo le chiavi contrassegnate come essenziali.</span><span class="sxs-lookup"><span data-stu-id="9a308-145">If consent to store cookies hasn't been provided, only cookies marked essential are sent to the browser.</span></span> <span data-ttu-id="9a308-146">Il codice seguente rende cookie essenziale:</span><span class="sxs-lookup"><span data-stu-id="9a308-146">The following code makes a cookie essential:</span></span>

[!code-csharp[Main](gdpr/sample/RP2.2/Pages/Cookie.cshtml.cs?name=snippet1&highlight=5)]

<a name="tempdata"></a>

### <a name="tempdata-provider-and-session-state-no-loccookies-arent-essential"></a><span data-ttu-id="9a308-147">Il provider TempData e lo stato della sessione cookie non sono essenziali</span><span class="sxs-lookup"><span data-stu-id="9a308-147">TempData provider and session state cookies aren't essential</span></span>

<span data-ttu-id="9a308-148">Il [provider TempData](xref:fundamentals/app-state#tempdata) cookie non è essenziale.</span><span class="sxs-lookup"><span data-stu-id="9a308-148">The [TempData provider](xref:fundamentals/app-state#tempdata) cookie isn't essential.</span></span> <span data-ttu-id="9a308-149">Se il rilevamento è disabilitato, il provider TempData non funziona.</span><span class="sxs-lookup"><span data-stu-id="9a308-149">If tracking is disabled, the TempData provider isn't functional.</span></span> <span data-ttu-id="9a308-150">Per abilitare il provider TempData quando il rilevamento è disabilitato, contrassegnare il TempData cookie come essenziale in `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="9a308-150">To enable the TempData provider when tracking is disabled, mark the TempData cookie as essential in `Startup.ConfigureServices`:</span></span>

[!code-csharp[Main](gdpr/sample/RP2.2/Startup.cs?name=snippet1)]

<span data-ttu-id="9a308-151">[Stato sessione](xref:fundamentals/app-state) cookie i non sono essenziali.</span><span class="sxs-lookup"><span data-stu-id="9a308-151">[Session state](xref:fundamentals/app-state) cookies are not essential.</span></span> <span data-ttu-id="9a308-152">Lo stato della sessione non è funzionante quando il rilevamento è disabilitato.</span><span class="sxs-lookup"><span data-stu-id="9a308-152">Session state isn't functional when tracking is disabled.</span></span> <span data-ttu-id="9a308-153">Il codice seguente rende la sessione cookie essenziale:</span><span class="sxs-lookup"><span data-stu-id="9a308-153">The following code makes session cookies essential:</span></span>

[!code-csharp[](gdpr/sample/RP2.2/Startup.cs?name=snippet2)]

<a name="pd"></a>

## <a name="personal-data"></a><span data-ttu-id="9a308-154">Dati personali</span><span class="sxs-lookup"><span data-stu-id="9a308-154">Personal data</span></span>

<span data-ttu-id="9a308-155">ASP.NET Core app create con account utente singoli includono il codice per scaricare ed eliminare i dati personali.</span><span class="sxs-lookup"><span data-stu-id="9a308-155">ASP.NET Core apps created with individual user accounts include code to download and delete personal data.</span></span>

<span data-ttu-id="9a308-156">Selezionare il nome utente e quindi selezionare **dati personali** :</span><span class="sxs-lookup"><span data-stu-id="9a308-156">Select the user name and then select **Personal data** :</span></span>

![Pagina Gestisci dati personali](gdpr/_static/pd.png)

<span data-ttu-id="9a308-158">Note:</span><span class="sxs-lookup"><span data-stu-id="9a308-158">Notes:</span></span>

* <span data-ttu-id="9a308-159">Per generare il `Account/Manage` codice, vedere [ Identity impalcatura ](xref:security/authentication/scaffold-identity).</span><span class="sxs-lookup"><span data-stu-id="9a308-159">To generate the `Account/Manage` code, see [Scaffold Identity](xref:security/authentication/scaffold-identity).</span></span>
* <span data-ttu-id="9a308-160">I collegamenti **Delete** e **download** agiscono solo sui dati di identità predefiniti.</span><span class="sxs-lookup"><span data-stu-id="9a308-160">The **Delete** and **Download** links only act on the default identity data.</span></span> <span data-ttu-id="9a308-161">È necessario estendere le app che creano dati utente personalizzati per eliminare o scaricare i dati utente personalizzati.</span><span class="sxs-lookup"><span data-stu-id="9a308-161">Apps that create custom user data must be extended to delete/download the custom user data.</span></span> <span data-ttu-id="9a308-162">Per ulteriori informazioni, vedere [aggiungere, scaricare ed eliminare dati utente personalizzati in Identity ](xref:security/authentication/add-user-data).</span><span class="sxs-lookup"><span data-stu-id="9a308-162">For more information, see [Add, download, and delete custom user data to Identity](xref:security/authentication/add-user-data).</span></span>
* <span data-ttu-id="9a308-163">I token salvati per l'utente archiviati nella Identity tabella di database `AspNetUserTokens` vengono eliminati quando l'utente viene eliminato tramite il comportamento di eliminazione a cascata a causa della [chiave esterna](https://github.com/aspnet/Identity/blob/release/2.1/src/EF/IdentityUserContext.cs#L152).</span><span class="sxs-lookup"><span data-stu-id="9a308-163">Saved tokens for the user that are stored in the Identity database table `AspNetUserTokens` are deleted when the user is deleted via the cascading delete behavior due to the [foreign key](https://github.com/aspnet/Identity/blob/release/2.1/src/EF/IdentityUserContext.cs#L152).</span></span>
* <span data-ttu-id="9a308-164">L' [autenticazione del provider esterno](xref:security/authentication/social/index), ad esempio Facebook e Google, non è disponibile prima che i cookie criteri vengano accettati.</span><span class="sxs-lookup"><span data-stu-id="9a308-164">[External provider authentication](xref:security/authentication/social/index), such as Facebook and Google, isn't available before the cookie policy is accepted.</span></span>

::: moniker-end

## <a name="encryption-at-rest"></a><span data-ttu-id="9a308-165">Crittografia di dati inattivi</span><span class="sxs-lookup"><span data-stu-id="9a308-165">Encryption at rest</span></span>

<span data-ttu-id="9a308-166">Alcuni database e meccanismi di archiviazione consentono la crittografia dei dati inattivi.</span><span class="sxs-lookup"><span data-stu-id="9a308-166">Some databases and storage mechanisms allow for encryption at rest.</span></span> <span data-ttu-id="9a308-167">Crittografia di dati inattivi:</span><span class="sxs-lookup"><span data-stu-id="9a308-167">Encryption at rest:</span></span>

* <span data-ttu-id="9a308-168">Crittografa automaticamente i dati archiviati.</span><span class="sxs-lookup"><span data-stu-id="9a308-168">Encrypts stored data automatically.</span></span>
* <span data-ttu-id="9a308-169">Crittografa senza configurazione, programmazione o altro lavoro per il software che accede ai dati.</span><span class="sxs-lookup"><span data-stu-id="9a308-169">Encrypts without configuration, programming, or other work for the software that accesses the data.</span></span>
* <span data-ttu-id="9a308-170">È l'opzione più semplice e sicura.</span><span class="sxs-lookup"><span data-stu-id="9a308-170">Is the easiest and safest option.</span></span>
* <span data-ttu-id="9a308-171">Consente al database di gestire le chiavi e la crittografia.</span><span class="sxs-lookup"><span data-stu-id="9a308-171">Allows the database to manage keys and encryption.</span></span>

<span data-ttu-id="9a308-172">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="9a308-172">For example:</span></span>

* <span data-ttu-id="9a308-173">Microsoft SQL e Azure SQL forniscono [Transparent Data Encryption](/sql/relational-databases/security/encryption/transparent-data-encryption) (Transparent Data Encryption).</span><span class="sxs-lookup"><span data-stu-id="9a308-173">Microsoft SQL and Azure SQL provide [Transparent Data Encryption](/sql/relational-databases/security/encryption/transparent-data-encryption) (TDE).</span></span>
* [<span data-ttu-id="9a308-174">Per impostazione predefinita, SQL Azure crittografare il database</span><span class="sxs-lookup"><span data-stu-id="9a308-174">SQL Azure encrypts the database by default</span></span>](https://azure.microsoft.com/updates/newly-created-azure-sql-databases-encrypted-by-default/)
* <span data-ttu-id="9a308-175">[Archiviazione BLOB, file, tabelle e code di Azure vengono crittografati per impostazione predefinita](https://azure.microsoft.com/blog/announcing-default-encryption-for-azure-blobs-files-table-and-queue-storage/).</span><span class="sxs-lookup"><span data-stu-id="9a308-175">[Azure Blobs, Files, Table, and Queue Storage are encrypted by default](https://azure.microsoft.com/blog/announcing-default-encryption-for-azure-blobs-files-table-and-queue-storage/).</span></span>

<span data-ttu-id="9a308-176">Per i database che non forniscono la crittografia incorporata, è possibile usare la crittografia del disco per fornire la stessa protezione.</span><span class="sxs-lookup"><span data-stu-id="9a308-176">For databases that don't provide built-in encryption at rest, you may be able to use disk encryption to provide the same protection.</span></span> <span data-ttu-id="9a308-177">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="9a308-177">For example:</span></span>

* [<span data-ttu-id="9a308-178">BitLocker per Windows Server</span><span class="sxs-lookup"><span data-stu-id="9a308-178">BitLocker for Windows Server</span></span>](/windows/security/information-protection/bitlocker/bitlocker-how-to-deploy-on-windows-server)
* <span data-ttu-id="9a308-179">Linux:</span><span class="sxs-lookup"><span data-stu-id="9a308-179">Linux:</span></span>
  * [<span data-ttu-id="9a308-180">eCryptfs</span><span class="sxs-lookup"><span data-stu-id="9a308-180">eCryptfs</span></span>](https://launchpad.net/ecryptfs)
  * <span data-ttu-id="9a308-181">[Encfs](https://github.com/vgough/encfs).</span><span class="sxs-lookup"><span data-stu-id="9a308-181">[EncFS](https://github.com/vgough/encfs).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9a308-182">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="9a308-182">Additional resources</span></span>

* [<span data-ttu-id="9a308-183">Microsoft.com/GDPR</span><span class="sxs-lookup"><span data-stu-id="9a308-183">Microsoft.com/GDPR</span></span>](https://www.microsoft.com/trustcenter/Privacy/GDPR)
* [<span data-ttu-id="9a308-184">GDPR-aggiunta di un pulsante di consenso per la revoca in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9a308-184">GDPR - Adding a Revoke Consent Button in ASP.NET Core</span></span>](https://www.joeaudette.com/blog/2018/08/28/gdpr---adding-a-revoke-consent-button-in-aspnet-core)
