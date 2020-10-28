---
title: :::no-loc(Blazor):::Globalizzazione e localizzazione ASP.NET Core
author: guardrex
description: 'Informazioni su come rendere i :::no-loc(Razor)::: componenti accessibili agli utenti in più impostazioni cultura e lingue.'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/04/2020
no-loc:
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: blazor/globalization-localization
ms.openlocfilehash: 52810cb5a5961ffe932a7f5ac2a3a03033781cc9
ms.sourcegitcommit: c06a5bf419541d17595af30e4cf6f2787c21855e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92678487"
---
# <a name="aspnet-core-no-locblazor-globalization-and-localization"></a><span data-ttu-id="6292a-103">:::no-loc(Blazor):::Globalizzazione e localizzazione ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6292a-103">ASP.NET Core :::no-loc(Blazor)::: globalization and localization</span></span>

<span data-ttu-id="6292a-104">Di [Luke Latham](https://github.com/guardrex) e [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="6292a-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="6292a-105">:::no-loc(Razor)::: i componenti possono essere resi accessibili agli utenti in più impostazioni cultura e lingue.</span><span class="sxs-lookup"><span data-stu-id="6292a-105">:::no-loc(Razor)::: components can be made accessible to users in multiple cultures and languages.</span></span> <span data-ttu-id="6292a-106">Sono disponibili i seguenti scenari di globalizzazione e localizzazione di .NET:</span><span class="sxs-lookup"><span data-stu-id="6292a-106">The following .NET globalization and localization scenarios are available:</span></span>

* <span data-ttu-id="6292a-107">. Sistema di risorse di NET</span><span class="sxs-lookup"><span data-stu-id="6292a-107">.NET's resources system</span></span>
* <span data-ttu-id="6292a-108">Formattazione di numeri e date specifiche delle impostazioni cultura</span><span class="sxs-lookup"><span data-stu-id="6292a-108">Culture-specific number and date formatting</span></span>

<span data-ttu-id="6292a-109">Sono attualmente supportati un set limitato di scenari di localizzazione di ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="6292a-109">A limited set of ASP.NET Core's localization scenarios are currently supported:</span></span>

* <span data-ttu-id="6292a-110"><xref:Microsoft.Extensions.Localization.IStringLocalizer> e <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> *sono supportati* nelle :::no-loc(Blazor)::: app.</span><span class="sxs-lookup"><span data-stu-id="6292a-110"><xref:Microsoft.Extensions.Localization.IStringLocalizer> and <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> *are supported* in :::no-loc(Blazor)::: apps.</span></span>
* <span data-ttu-id="6292a-111"><xref:Microsoft.AspNetCore.Mvc.Localization.IHtmlLocalizer>la <xref:Microsoft.AspNetCore.Mvc.Localization.IViewLocalizer> localizzazione delle annotazioni dei dati, e è ASP.NET Core scenari MVC e **non è supportata** nelle :::no-loc(Blazor)::: app.</span><span class="sxs-lookup"><span data-stu-id="6292a-111"><xref:Microsoft.AspNetCore.Mvc.Localization.IHtmlLocalizer>, <xref:Microsoft.AspNetCore.Mvc.Localization.IViewLocalizer>, and Data Annotations localization are ASP.NET Core MVC scenarios and **not supported** in :::no-loc(Blazor)::: apps.</span></span>

<span data-ttu-id="6292a-112">Per altre informazioni, vedere <xref:fundamentals/localization>.</span><span class="sxs-lookup"><span data-stu-id="6292a-112">For more information, see <xref:fundamentals/localization>.</span></span>

## <a name="globalization"></a><span data-ttu-id="6292a-113">Globalizzazione</span><span class="sxs-lookup"><span data-stu-id="6292a-113">Globalization</span></span>

<span data-ttu-id="6292a-114">:::no-loc(Blazor):::la [`@bind`](xref:mvc/views/razor#bind) funzionalità di esegue formati e analizza i valori per la visualizzazione in base alle impostazioni cultura correnti dell'utente.</span><span class="sxs-lookup"><span data-stu-id="6292a-114">:::no-loc(Blazor):::'s [`@bind`](xref:mvc/views/razor#bind) functionality performs formats and parses values for display based on the user's current culture.</span></span>

<span data-ttu-id="6292a-115">È possibile accedere alle impostazioni cultura correnti dalla <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName> Proprietà.</span><span class="sxs-lookup"><span data-stu-id="6292a-115">The current culture can be accessed from the <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName> property.</span></span>

<span data-ttu-id="6292a-116"><xref:System.Globalization.CultureInfo.InvariantCulture?displayProperty=nameWithType> viene usato per i tipi di campo seguenti ( `<input type="{TYPE}" />` ):</span><span class="sxs-lookup"><span data-stu-id="6292a-116"><xref:System.Globalization.CultureInfo.InvariantCulture?displayProperty=nameWithType> is used for the following field types (`<input type="{TYPE}" />`):</span></span>

* `date`
* `number`

<span data-ttu-id="6292a-117">I tipi di campo precedenti:</span><span class="sxs-lookup"><span data-stu-id="6292a-117">The preceding field types:</span></span>

* <span data-ttu-id="6292a-118">Vengono visualizzati utilizzando le regole di formattazione appropriate basate su browser.</span><span class="sxs-lookup"><span data-stu-id="6292a-118">Are displayed using their appropriate browser-based formatting rules.</span></span>
* <span data-ttu-id="6292a-119">Non può contenere testo in formato libero.</span><span class="sxs-lookup"><span data-stu-id="6292a-119">Can't contain free-form text.</span></span>
* <span data-ttu-id="6292a-120">Fornire le caratteristiche di interazione utente in base all'implementazione del browser.</span><span class="sxs-lookup"><span data-stu-id="6292a-120">Provide user interaction characteristics based on the browser's implementation.</span></span>

<span data-ttu-id="6292a-121">I tipi di campo seguenti hanno requisiti di formattazione specifici e non sono attualmente supportati da perché non sono :::no-loc(Blazor)::: supportati da tutti i browser principali:</span><span class="sxs-lookup"><span data-stu-id="6292a-121">The following field types have specific formatting requirements and aren't currently supported by :::no-loc(Blazor)::: because they aren't supported by all major browsers:</span></span>

* `datetime-local`
* `month`
* `week`

<span data-ttu-id="6292a-122">[`@bind`](xref:mvc/views/razor#bind) supporta il `@bind:culture` parametro per fornire un oggetto <xref:System.Globalization.CultureInfo?displayProperty=fullName> per l'analisi e la formattazione di un valore.</span><span class="sxs-lookup"><span data-stu-id="6292a-122">[`@bind`](xref:mvc/views/razor#bind) supports the `@bind:culture` parameter to provide a <xref:System.Globalization.CultureInfo?displayProperty=fullName> for parsing and formatting a value.</span></span> <span data-ttu-id="6292a-123">Non è consigliabile specificare impostazioni cultura quando si usano i `date` `number` tipi di campo e.</span><span class="sxs-lookup"><span data-stu-id="6292a-123">Specifying a culture isn't recommended when using the `date` and `number` field types.</span></span> <span data-ttu-id="6292a-124">`date` e `number` dispongono di supporto incorporato :::no-loc(Blazor)::: che fornisce le impostazioni cultura richieste.</span><span class="sxs-lookup"><span data-stu-id="6292a-124">`date` and `number` have built-in :::no-loc(Blazor)::: support that provides the required culture.</span></span>

## <a name="localization"></a><span data-ttu-id="6292a-125">Localizzazione</span><span class="sxs-lookup"><span data-stu-id="6292a-125">Localization</span></span>

### :::no-loc(Blazor WebAssembly):::

<span data-ttu-id="6292a-126">:::no-loc(Blazor WebAssembly)::: le app impostano le impostazioni cultura usando le [Preferenze della lingua](https://developer.mozilla.org/docs/Web/API/NavigatorLanguage/languages)dell'utente.</span><span class="sxs-lookup"><span data-stu-id="6292a-126">:::no-loc(Blazor WebAssembly)::: apps set the culture using the user's [language preference](https://developer.mozilla.org/docs/Web/API/NavigatorLanguage/languages).</span></span>

<span data-ttu-id="6292a-127">Per configurare in modo esplicito le impostazioni cultura, impostare <xref:System.Globalization.CultureInfo.DefaultThreadCurrentCulture?displayProperty=nameWithType> e <xref:System.Globalization.CultureInfo.DefaultThreadCurrentUICulture?displayProperty=nameWithType> in `Program.Main` .</span><span class="sxs-lookup"><span data-stu-id="6292a-127">To explicitly configure the culture, set <xref:System.Globalization.CultureInfo.DefaultThreadCurrentCulture?displayProperty=nameWithType> and <xref:System.Globalization.CultureInfo.DefaultThreadCurrentUICulture?displayProperty=nameWithType> in `Program.Main`.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="6292a-128">Per impostazione predefinita, :::no-loc(Blazor WebAssembly)::: contiene risorse di globalizzazione minime necessarie per visualizzare i valori, ad esempio date e valute, nelle impostazioni cultura dell'utente.</span><span class="sxs-lookup"><span data-stu-id="6292a-128">By default, :::no-loc(Blazor WebAssembly)::: carries minimal globalization resources required to display values, such as dates and currency, in the user's culture.</span></span> <span data-ttu-id="6292a-129">Le applicazioni che devono supportare la modifica dinamica delle impostazioni cultura devono essere configurate `:::no-loc(Blazor):::WebAssemblyLoadAllGlobalizationData` nel file di progetto:</span><span class="sxs-lookup"><span data-stu-id="6292a-129">Applications that must support dynamically changing the culture should configure `:::no-loc(Blazor):::WebAssemblyLoadAllGlobalizationData` in the project file:</span></span>

```xml
<PropertyGroup>
  <:::no-loc(Blazor):::WebAssemblyLoadAllGlobalizationData>true</:::no-loc(Blazor):::WebAssemblyLoadAllGlobalizationData>
</PropertyGroup>
```

<span data-ttu-id="6292a-130">:::no-loc(Blazor WebAssembly)::: può anche essere configurato per avviare usando impostazioni cultura specifiche dell'applicazione usando le opzioni passate a `:::no-loc(Blazor):::.start` .</span><span class="sxs-lookup"><span data-stu-id="6292a-130">:::no-loc(Blazor WebAssembly)::: can also be configured to launch using a specific application culture using options passed to `:::no-loc(Blazor):::.start`.</span></span> <span data-ttu-id="6292a-131">Ad esempio, l'esempio seguente mostra un'app configurata per l'avvio usando le `en-GB` impostazioni cultura:</span><span class="sxs-lookup"><span data-stu-id="6292a-131">For instance, the sample below shows an app configured to launch using the `en-GB` culture:</span></span>

```html
<script src="_framework/blazor.webassembly.js" autostart="false"></script>
<script>
  :::no-loc(Blazor):::.start({
    applicationCulture: 'en-GB'
  });
</script>
```

<span data-ttu-id="6292a-132">Il valore per `applicationCulture` deve essere conforme al [formato di tag del linguaggio BCP-47](https://tools.ietf.org/html/bcp47).</span><span class="sxs-lookup"><span data-stu-id="6292a-132">The value for `applicationCulture` should conform to the [BCP-47 language tag format](https://tools.ietf.org/html/bcp47).</span></span>

<span data-ttu-id="6292a-133">Se l'app non richiede la localizzazione, è possibile configurare l'app in modo che supporti la lingua inglese, che è basata sulle `en-US` impostazioni cultura:</span><span class="sxs-lookup"><span data-stu-id="6292a-133">If the app doesn't require localization, you may configure the app to support the invariant culture, which is based on the `en-US` culture:</span></span>

```xml
<PropertyGroup>
  <InvariantGlobalization>true</InvariantGlobalization>
</PropertyGroup>
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="6292a-134">Per impostazione predefinita, la configurazione del linker linguaggio intermedio (IL) per le :::no-loc(Blazor WebAssembly)::: app rimuove le informazioni di internazionalizzazione ad eccezione delle impostazioni locali richieste in modo esplicito.</span><span class="sxs-lookup"><span data-stu-id="6292a-134">By default, the Intermediate Language (IL) Linker configuration for :::no-loc(Blazor WebAssembly)::: apps strips out internationalization information except for locales explicitly requested.</span></span> <span data-ttu-id="6292a-135">Per altre informazioni, vedere <xref:blazor/host-and-deploy/configure-linker#configure-the-linker-for-internationalization>.</span><span class="sxs-lookup"><span data-stu-id="6292a-135">For more information, see <xref:blazor/host-and-deploy/configure-linker#configure-the-linker-for-internationalization>.</span></span>

::: moniker-end

<span data-ttu-id="6292a-136">Mentre le impostazioni cultura che :::no-loc(Blazor)::: selezionano per impostazione predefinita potrebbero essere sufficienti per la maggior parte degli utenti, è consigliabile offrire agli utenti un modo per specificare le impostazioni locali preferite.</span><span class="sxs-lookup"><span data-stu-id="6292a-136">While the culture that :::no-loc(Blazor)::: selects by default might be sufficient for most users, consider offering a way for users to specify their preferred locale.</span></span> <span data-ttu-id="6292a-137">Per un' :::no-loc(Blazor WebAssembly)::: app di esempio con selezione impostazioni cultura, vedere l' [`LocSample`](https://github.com/pranavkm/LocSample) app di esempio localizzazione.</span><span class="sxs-lookup"><span data-stu-id="6292a-137">For a :::no-loc(Blazor WebAssembly)::: sample app with a culture picker, see the [`LocSample`](https://github.com/pranavkm/LocSample) localization sample app.</span></span>

### :::no-loc(Blazor Server):::

<span data-ttu-id="6292a-138">:::no-loc(Blazor Server)::: le app vengono localizzate usando il [middleware di localizzazione](xref:fundamentals/localization#localization-middleware).</span><span class="sxs-lookup"><span data-stu-id="6292a-138">:::no-loc(Blazor Server)::: apps are localized using [Localization Middleware](xref:fundamentals/localization#localization-middleware).</span></span> <span data-ttu-id="6292a-139">Il middleware seleziona le impostazioni cultura appropriate per gli utenti che richiedono risorse dall'app.</span><span class="sxs-lookup"><span data-stu-id="6292a-139">The middleware selects the appropriate culture for users requesting resources from the app.</span></span>

<span data-ttu-id="6292a-140">Le impostazioni cultura possono essere impostate utilizzando uno degli approcci seguenti:</span><span class="sxs-lookup"><span data-stu-id="6292a-140">The culture can be set using one of the following approaches:</span></span>

* <span data-ttu-id="6292a-141">[:::no-loc(Cookie):::s](#:::no-loc(cookie):::s)</span><span class="sxs-lookup"><span data-stu-id="6292a-141">[:::no-loc(Cookie):::s](#:::no-loc(cookie):::s)</span></span>
* [<span data-ttu-id="6292a-142">Fornire l'interfaccia utente per scegliere le impostazioni cultura</span><span class="sxs-lookup"><span data-stu-id="6292a-142">Provide UI to choose the culture</span></span>](#provide-ui-to-choose-the-culture)

<span data-ttu-id="6292a-143">Per altre informazioni ed esempi, vedere <xref:fundamentals/localization>.</span><span class="sxs-lookup"><span data-stu-id="6292a-143">For more information and examples, see <xref:fundamentals/localization>.</span></span>

#### <a name="no-loccookies"></a><span data-ttu-id="6292a-144">:::no-loc(Cookie):::s</span><span class="sxs-lookup"><span data-stu-id="6292a-144">:::no-loc(Cookie):::s</span></span>

<span data-ttu-id="6292a-145">Le impostazioni cultura :::no-loc(cookie)::: della localizzazione possono salvare in maniera permanente le impostazioni cultura dell'utente.</span><span class="sxs-lookup"><span data-stu-id="6292a-145">A localization culture :::no-loc(cookie)::: can persist the user's culture.</span></span> <span data-ttu-id="6292a-146">Il middleware di localizzazione legge l'oggetto :::no-loc(cookie)::: sulle richieste successive per impostare le impostazioni cultura dell'utente.</span><span class="sxs-lookup"><span data-stu-id="6292a-146">The Localization Middleware reads the :::no-loc(cookie)::: on subsequent requests to set the user's culture.</span></span> 

<span data-ttu-id="6292a-147">L'uso di un oggetto :::no-loc(cookie)::: garantisce che la connessione WebSocket possa propagare correttamente le impostazioni cultura.</span><span class="sxs-lookup"><span data-stu-id="6292a-147">Use of a :::no-loc(cookie)::: ensures that the WebSocket connection can correctly propagate the culture.</span></span> <span data-ttu-id="6292a-148">Se gli schemi di localizzazione sono basati sul percorso dell'URL o sulla stringa di query, lo schema potrebbe non essere in grado di funzionare con WebSocket, quindi non è possibile salvare in modo permanente le impostazioni cultura.</span><span class="sxs-lookup"><span data-stu-id="6292a-148">If localization schemes are based on the URL path or query string, the scheme might not be able to work with WebSockets, thus fail to persist the culture.</span></span> <span data-ttu-id="6292a-149">Pertanto, l'utilizzo delle impostazioni cultura di localizzazione :::no-loc(cookie)::: è l'approccio consigliato.</span><span class="sxs-lookup"><span data-stu-id="6292a-149">Therefore, use of a localization culture :::no-loc(cookie)::: is the recommended approach.</span></span>

<span data-ttu-id="6292a-150">Qualsiasi tecnica può essere utilizzata per assegnare impostazioni cultura se le impostazioni cultura vengono rese permanente in una localizzazione :::no-loc(cookie)::: .</span><span class="sxs-lookup"><span data-stu-id="6292a-150">Any technique can be used to assign a culture if the culture is persisted in a localization :::no-loc(cookie):::.</span></span> <span data-ttu-id="6292a-151">Se l'app ha già uno schema di localizzazione stabilito per ASP.NET Core lato server, continuare a usare l'infrastruttura di localizzazione esistente dell'app e impostare le impostazioni cultura di localizzazione nello :::no-loc(cookie)::: schema dell'app.</span><span class="sxs-lookup"><span data-stu-id="6292a-151">If the app already has an established localization scheme for server-side ASP.NET Core, continue to use the app's existing localization infrastructure and set the localization culture :::no-loc(cookie)::: within the app's scheme.</span></span>

<span data-ttu-id="6292a-152">Nell'esempio seguente viene illustrato come impostare le impostazioni cultura correnti in un oggetto :::no-loc(cookie)::: che può essere letto dal middleware di localizzazione.</span><span class="sxs-lookup"><span data-stu-id="6292a-152">The following example shows how to set the current culture in a :::no-loc(cookie)::: that can be read by the Localization Middleware.</span></span> <span data-ttu-id="6292a-153">Creare un' :::no-loc(Razor)::: espressione nel `Pages/_Host.cshtml` file immediatamente all'interno del tag di apertura `<body>` :</span><span class="sxs-lookup"><span data-stu-id="6292a-153">Create a :::no-loc(Razor)::: expression in the `Pages/_Host.cshtml` file immediately inside the opening `<body>` tag:</span></span>

```cshtml
@using System.Globalization
@using Microsoft.AspNetCore.Localization

...

<body>
    @{
        this.HttpContext.Response.:::no-loc(Cookie):::s.Append(
            :::no-loc(Cookie):::RequestCultureProvider.Default:::no-loc(Cookie):::Name,
            :::no-loc(Cookie):::RequestCultureProvider.Make:::no-loc(Cookie):::Value(
                new RequestCulture(
                    CultureInfo.CurrentCulture,
                    CultureInfo.CurrentUICulture)));
    }

    ...
</body>
```

<span data-ttu-id="6292a-154">La localizzazione viene gestita dall'app nella sequenza di eventi seguente:</span><span class="sxs-lookup"><span data-stu-id="6292a-154">Localization is handled by the app in the following sequence of events:</span></span>

1. <span data-ttu-id="6292a-155">Il browser invia una richiesta HTTP iniziale all'app.</span><span class="sxs-lookup"><span data-stu-id="6292a-155">The browser sends an initial HTTP request to the app.</span></span>
1. <span data-ttu-id="6292a-156">Le impostazioni cultura vengono assegnate dal middleware di localizzazione.</span><span class="sxs-lookup"><span data-stu-id="6292a-156">The culture is assigned by the Localization Middleware.</span></span>
1. <span data-ttu-id="6292a-157">L' :::no-loc(Razor)::: espressione nella `_Host` pagina ( `_Host.cshtml` ) rende permanente le impostazioni cultura in un :::no-loc(cookie)::: come parte della risposta.</span><span class="sxs-lookup"><span data-stu-id="6292a-157">The :::no-loc(Razor)::: expression in the `_Host` page (`_Host.cshtml`) persists the culture in a :::no-loc(cookie)::: as part of the response.</span></span>
1. <span data-ttu-id="6292a-158">Il browser apre una connessione WebSocket per creare una :::no-loc(Blazor Server)::: sessione interattiva.</span><span class="sxs-lookup"><span data-stu-id="6292a-158">The browser opens a WebSocket connection to create an interactive :::no-loc(Blazor Server)::: session.</span></span>
1. <span data-ttu-id="6292a-159">Il middleware di localizzazione legge :::no-loc(cookie)::: e assegna le impostazioni cultura.</span><span class="sxs-lookup"><span data-stu-id="6292a-159">The Localization Middleware reads the :::no-loc(cookie)::: and assigns the culture.</span></span>
1. <span data-ttu-id="6292a-160">La :::no-loc(Blazor Server)::: sessione inizia con le impostazioni cultura corrette.</span><span class="sxs-lookup"><span data-stu-id="6292a-160">The :::no-loc(Blazor Server)::: session begins with the correct culture.</span></span>

<span data-ttu-id="6292a-161">Quando si utilizza un <xref:Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.:::no-loc(Razor):::Page> , utilizzare la <xref:Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.:::no-loc(Razor):::Page.Context> proprietà:</span><span class="sxs-lookup"><span data-stu-id="6292a-161">When working with a <xref:Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.:::no-loc(Razor):::Page>, use the <xref:Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.:::no-loc(Razor):::Page.Context> property:</span></span>

```razor
@{
    this.Context.Response.:::no-loc(Cookie):::s.Append(
        :::no-loc(Cookie):::RequestCultureProvider.Default:::no-loc(Cookie):::Name,
        :::no-loc(Cookie):::RequestCultureProvider.Make:::no-loc(Cookie):::Value(
            new RequestCulture(
                CultureInfo.CurrentCulture,
                CultureInfo.CurrentUICulture)));
}
```

#### <a name="provide-ui-to-choose-the-culture"></a><span data-ttu-id="6292a-162">Fornire l'interfaccia utente per scegliere le impostazioni cultura</span><span class="sxs-lookup"><span data-stu-id="6292a-162">Provide UI to choose the culture</span></span>

<span data-ttu-id="6292a-163">Per consentire a un utente di selezionare le impostazioni cultura, è consigliabile un *approccio basato su Reindirizzamento* .</span><span class="sxs-lookup"><span data-stu-id="6292a-163">To provide UI to allow a user to select a culture, a *redirect-based approach* is recommended.</span></span> <span data-ttu-id="6292a-164">Il processo è simile a quello che accade in un'app Web quando un utente tenta di accedere a una risorsa protetta.</span><span class="sxs-lookup"><span data-stu-id="6292a-164">The process is similar to what happens in a web app when a user attempts to access a secure resource.</span></span> <span data-ttu-id="6292a-165">L'utente viene reindirizzato a una pagina di accesso, quindi viene reindirizzato di nuovo alla risorsa originale.</span><span class="sxs-lookup"><span data-stu-id="6292a-165">The user is redirected to a sign-in page and then redirected back to the original resource.</span></span> 

<span data-ttu-id="6292a-166">L'app rende permanente le impostazioni cultura selezionate dall'utente tramite un reindirizzamento a un controller.</span><span class="sxs-lookup"><span data-stu-id="6292a-166">The app persists the user's selected culture via a redirect to a controller.</span></span> <span data-ttu-id="6292a-167">Il controller imposta le impostazioni cultura selezionate dall'utente in un :::no-loc(cookie)::: e reindirizza l'utente all'URI originale.</span><span class="sxs-lookup"><span data-stu-id="6292a-167">The controller sets the user's selected culture into a :::no-loc(cookie)::: and redirects the user back to the original URI.</span></span>

<span data-ttu-id="6292a-168">Stabilire un endpoint HTTP nel server per impostare le impostazioni cultura selezionate dall'utente in un oggetto :::no-loc(cookie)::: ed eseguire di nuovo il reindirizzamento all'URI originale:</span><span class="sxs-lookup"><span data-stu-id="6292a-168">Establish an HTTP endpoint on the server to set the user's selected culture in a :::no-loc(cookie)::: and perform the redirect back to the original URI:</span></span>

```csharp
[Route("[controller]/[action]")]
public class CultureController : Controller
{
    public IActionResult SetCulture(string culture, string redirectUri)
    {
        if (culture != null)
        {
            HttpContext.Response.:::no-loc(Cookie):::s.Append(
                :::no-loc(Cookie):::RequestCultureProvider.Default:::no-loc(Cookie):::Name,
                :::no-loc(Cookie):::RequestCultureProvider.Make:::no-loc(Cookie):::Value(
                    new RequestCulture(culture)));
        }

        return LocalRedirect(redirectUri);
    }
}
```

> [!WARNING]
> <span data-ttu-id="6292a-169">Usare il <xref:Microsoft.AspNetCore.Mvc.ControllerBase.LocalRedirect%2A> risultato dell'azione per impedire gli attacchi di reindirizzamento aperti.</span><span class="sxs-lookup"><span data-stu-id="6292a-169">Use the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.LocalRedirect%2A> action result to prevent open redirect attacks.</span></span> <span data-ttu-id="6292a-170">Per altre informazioni, vedere <xref:security/preventing-open-redirects>.</span><span class="sxs-lookup"><span data-stu-id="6292a-170">For more information, see <xref:security/preventing-open-redirects>.</span></span>

<span data-ttu-id="6292a-171">Se l'app non è configurata per l'elaborazione delle azioni del controller:</span><span class="sxs-lookup"><span data-stu-id="6292a-171">If the app isn't configured to process controller actions:</span></span>

* <span data-ttu-id="6292a-172">Aggiungere i servizi MVC alla raccolta di servizi in `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="6292a-172">Add MVC services to the service collection in `Startup.ConfigureServices`:</span></span>

  ```csharp
  services.AddControllers();
  ```

* <span data-ttu-id="6292a-173">Aggiungi routing endpoint controller in `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="6292a-173">Add controller endpoint routing in `Startup.Configure`:</span></span>

  ```csharp
  app.UseEndpoints(endpoints =>
  {
      endpoints.MapControllers();
      endpoints.Map:::no-loc(Blazor):::Hub();
      endpoints.MapFallbackToPage("/_Host");
  });
  ```

<span data-ttu-id="6292a-174">Il componente seguente mostra un esempio di come eseguire il reindirizzamento iniziale quando l'utente seleziona le impostazioni cultura:</span><span class="sxs-lookup"><span data-stu-id="6292a-174">The following component shows an example of how to perform the initial redirection when the user selects a culture:</span></span>

```razor
@inject NavigationManager NavigationManager

<h3>Select your language</h3>

<select @onchange="OnSelected">
    <option>Select...</option>
    <option value="en-US">English</option>
    <option value="fr-FR">Français</option>
</select>

@code {
    private void OnSelected(ChangeEventArgs e)
    {
        var culture = (string)e.Value;
        var uri = new Uri(NavigationManager.Uri)
            .GetComponents(UriComponents.PathAndQuery, UriFormat.Unescaped);
        var query = $"?culture={Uri.EscapeDataString(culture)}&" +
            $"redirectUri={Uri.EscapeDataString(uri)}";

        NavigationManager.NavigateTo("/Culture/SetCulture" + query, forceLoad: true);
    }
}
```

## <a name="additional-resources"></a><span data-ttu-id="6292a-175">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="6292a-175">Additional resources</span></span>

* <xref:fundamentals/localization>
