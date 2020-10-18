---
title: BlazorGlobalizzazione e localizzazione ASP.NET Core
author: guardrex
description: Informazioni su come rendere i Razor componenti accessibili agli utenti in più impostazioni cultura e lingue.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/04/2020
no-loc:
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
uid: blazor/globalization-localization
ms.openlocfilehash: be73b0c1e33a2cd15c9ff0dc51044f9bd48c43fe
ms.sourcegitcommit: ecae2aa432628b9181d1fa11037c231c7dd56c9e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2020
ms.locfileid: "92113816"
---
# <a name="aspnet-core-no-locblazor-globalization-and-localization"></a><span data-ttu-id="a89ec-103">BlazorGlobalizzazione e localizzazione ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a89ec-103">ASP.NET Core Blazor globalization and localization</span></span>

<span data-ttu-id="a89ec-104">Di [Luke Latham](https://github.com/guardrex) e [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="a89ec-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="a89ec-105">Razor i componenti possono essere resi accessibili agli utenti in più impostazioni cultura e lingue.</span><span class="sxs-lookup"><span data-stu-id="a89ec-105">Razor components can be made accessible to users in multiple cultures and languages.</span></span> <span data-ttu-id="a89ec-106">Sono disponibili i seguenti scenari di globalizzazione e localizzazione di .NET:</span><span class="sxs-lookup"><span data-stu-id="a89ec-106">The following .NET globalization and localization scenarios are available:</span></span>

* <span data-ttu-id="a89ec-107">. Sistema di risorse di NET</span><span class="sxs-lookup"><span data-stu-id="a89ec-107">.NET's resources system</span></span>
* <span data-ttu-id="a89ec-108">Formattazione di numeri e date specifiche delle impostazioni cultura</span><span class="sxs-lookup"><span data-stu-id="a89ec-108">Culture-specific number and date formatting</span></span>

<span data-ttu-id="a89ec-109">Sono attualmente supportati un set limitato di scenari di localizzazione di ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="a89ec-109">A limited set of ASP.NET Core's localization scenarios are currently supported:</span></span>

* <span data-ttu-id="a89ec-110"><xref:Microsoft.Extensions.Localization.IStringLocalizer> e <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> *sono supportati* nelle Blazor app.</span><span class="sxs-lookup"><span data-stu-id="a89ec-110"><xref:Microsoft.Extensions.Localization.IStringLocalizer> and <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> *are supported* in Blazor apps.</span></span>
* <span data-ttu-id="a89ec-111"><xref:Microsoft.AspNetCore.Mvc.Localization.IHtmlLocalizer>la <xref:Microsoft.AspNetCore.Mvc.Localization.IViewLocalizer> localizzazione delle annotazioni dei dati, e è ASP.NET Core scenari MVC e **non è supportata** nelle Blazor app.</span><span class="sxs-lookup"><span data-stu-id="a89ec-111"><xref:Microsoft.AspNetCore.Mvc.Localization.IHtmlLocalizer>, <xref:Microsoft.AspNetCore.Mvc.Localization.IViewLocalizer>, and Data Annotations localization are ASP.NET Core MVC scenarios and **not supported** in Blazor apps.</span></span>

<span data-ttu-id="a89ec-112">Per ulteriori informazioni, vedere <xref:fundamentals/localization>.</span><span class="sxs-lookup"><span data-stu-id="a89ec-112">For more information, see <xref:fundamentals/localization>.</span></span>

## <a name="globalization"></a><span data-ttu-id="a89ec-113">Globalizzazione</span><span class="sxs-lookup"><span data-stu-id="a89ec-113">Globalization</span></span>

<span data-ttu-id="a89ec-114">Blazorla [`@bind`](xref:mvc/views/razor#bind) funzionalità di esegue formati e analizza i valori per la visualizzazione in base alle impostazioni cultura correnti dell'utente.</span><span class="sxs-lookup"><span data-stu-id="a89ec-114">Blazor's [`@bind`](xref:mvc/views/razor#bind) functionality performs formats and parses values for display based on the user's current culture.</span></span>

<span data-ttu-id="a89ec-115">È possibile accedere alle impostazioni cultura correnti dalla <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName> Proprietà.</span><span class="sxs-lookup"><span data-stu-id="a89ec-115">The current culture can be accessed from the <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName> property.</span></span>

<span data-ttu-id="a89ec-116"><xref:System.Globalization.CultureInfo.InvariantCulture?displayProperty=nameWithType> viene usato per i tipi di campo seguenti ( `<input type="{TYPE}" />` ):</span><span class="sxs-lookup"><span data-stu-id="a89ec-116"><xref:System.Globalization.CultureInfo.InvariantCulture?displayProperty=nameWithType> is used for the following field types (`<input type="{TYPE}" />`):</span></span>

* `date`
* `number`

<span data-ttu-id="a89ec-117">I tipi di campo precedenti:</span><span class="sxs-lookup"><span data-stu-id="a89ec-117">The preceding field types:</span></span>

* <span data-ttu-id="a89ec-118">Vengono visualizzati utilizzando le regole di formattazione appropriate basate su browser.</span><span class="sxs-lookup"><span data-stu-id="a89ec-118">Are displayed using their appropriate browser-based formatting rules.</span></span>
* <span data-ttu-id="a89ec-119">Non può contenere testo in formato libero.</span><span class="sxs-lookup"><span data-stu-id="a89ec-119">Can't contain free-form text.</span></span>
* <span data-ttu-id="a89ec-120">Fornire le caratteristiche di interazione utente in base all'implementazione del browser.</span><span class="sxs-lookup"><span data-stu-id="a89ec-120">Provide user interaction characteristics based on the browser's implementation.</span></span>

<span data-ttu-id="a89ec-121">I tipi di campo seguenti hanno requisiti di formattazione specifici e non sono attualmente supportati da perché non sono Blazor supportati da tutti i browser principali:</span><span class="sxs-lookup"><span data-stu-id="a89ec-121">The following field types have specific formatting requirements and aren't currently supported by Blazor because they aren't supported by all major browsers:</span></span>

* `datetime-local`
* `month`
* `week`

<span data-ttu-id="a89ec-122">[`@bind`](xref:mvc/views/razor#bind) supporta il `@bind:culture` parametro per fornire un oggetto <xref:System.Globalization.CultureInfo?displayProperty=fullName> per l'analisi e la formattazione di un valore.</span><span class="sxs-lookup"><span data-stu-id="a89ec-122">[`@bind`](xref:mvc/views/razor#bind) supports the `@bind:culture` parameter to provide a <xref:System.Globalization.CultureInfo?displayProperty=fullName> for parsing and formatting a value.</span></span> <span data-ttu-id="a89ec-123">Non è consigliabile specificare impostazioni cultura quando si usano i `date` `number` tipi di campo e.</span><span class="sxs-lookup"><span data-stu-id="a89ec-123">Specifying a culture isn't recommended when using the `date` and `number` field types.</span></span> <span data-ttu-id="a89ec-124">`date` e `number` dispongono di supporto incorporato Blazor che fornisce le impostazioni cultura richieste.</span><span class="sxs-lookup"><span data-stu-id="a89ec-124">`date` and `number` have built-in Blazor support that provides the required culture.</span></span>

## <a name="localization"></a><span data-ttu-id="a89ec-125">Localizzazione</span><span class="sxs-lookup"><span data-stu-id="a89ec-125">Localization</span></span>

### Blazor WebAssembly

<span data-ttu-id="a89ec-126">Blazor WebAssembly le app impostano le impostazioni cultura usando le [Preferenze della lingua](https://developer.mozilla.org/docs/Web/API/NavigatorLanguage/languages)dell'utente.</span><span class="sxs-lookup"><span data-stu-id="a89ec-126">Blazor WebAssembly apps set the culture using the user's [language preference](https://developer.mozilla.org/docs/Web/API/NavigatorLanguage/languages).</span></span>

<span data-ttu-id="a89ec-127">Per configurare in modo esplicito le impostazioni cultura, impostare <xref:System.Globalization.CultureInfo.DefaultThreadCurrentCulture?displayProperty=nameWithType> e <xref:System.Globalization.CultureInfo.DefaultThreadCurrentUICulture?displayProperty=nameWithType> in `Program.Main` .</span><span class="sxs-lookup"><span data-stu-id="a89ec-127">To explicitly configure the culture, set <xref:System.Globalization.CultureInfo.DefaultThreadCurrentCulture?displayProperty=nameWithType> and <xref:System.Globalization.CultureInfo.DefaultThreadCurrentUICulture?displayProperty=nameWithType> in `Program.Main`.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="a89ec-128">Per impostazione predefinita, Blazor WebAssembly contiene risorse di globalizzazione minime necessarie per visualizzare i valori, ad esempio date e valute, nelle impostazioni cultura dell'utente.</span><span class="sxs-lookup"><span data-stu-id="a89ec-128">By default, Blazor WebAssembly carries minimal globalization resources required to display values, such as dates and currency, in the user's culture.</span></span> <span data-ttu-id="a89ec-129">Le applicazioni che devono supportare la modifica dinamica delle impostazioni cultura devono essere configurate `BlazorWebAssemblyLoadAllGlobalizationData` nel file di progetto:</span><span class="sxs-lookup"><span data-stu-id="a89ec-129">Applications that must support dynamically changing the culture should configure `BlazorWebAssemblyLoadAllGlobalizationData` in the project file:</span></span>

```xml
<PropertyGroup>
  <BlazorWebAssemblyLoadAllGlobalizationData>true</BlazorWebAssemblyLoadAllGlobalizationData>
</PropertyGroup>
```

<span data-ttu-id="a89ec-130">Se l'app non richiede la localizzazione, è possibile configurare l'app in modo che supporti la lingua inglese, che è basata sulle `en-US` impostazioni cultura:</span><span class="sxs-lookup"><span data-stu-id="a89ec-130">If the app doesn't require localization, you may configure the app to support the invariant culture, which is based on the `en-US` culture:</span></span>

```xml
<PropertyGroup>
  <InvariantGlobalization>true</InvariantGlobalization>
</PropertyGroup>
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="a89ec-131">Per impostazione predefinita, la configurazione del linker linguaggio intermedio (IL) per le Blazor WebAssembly app rimuove le informazioni di internazionalizzazione ad eccezione delle impostazioni locali richieste in modo esplicito.</span><span class="sxs-lookup"><span data-stu-id="a89ec-131">By default, the Intermediate Language (IL) Linker configuration for Blazor WebAssembly apps strips out internationalization information except for locales explicitly requested.</span></span> <span data-ttu-id="a89ec-132">Per ulteriori informazioni, vedere <xref:blazor/host-and-deploy/configure-linker#configure-the-linker-for-internationalization>.</span><span class="sxs-lookup"><span data-stu-id="a89ec-132">For more information, see <xref:blazor/host-and-deploy/configure-linker#configure-the-linker-for-internationalization>.</span></span>

::: moniker-end

<span data-ttu-id="a89ec-133">Mentre le impostazioni cultura che Blazor selezionano per impostazione predefinita potrebbero essere sufficienti per la maggior parte degli utenti, è consigliabile offrire agli utenti un modo per specificare le impostazioni locali preferite.</span><span class="sxs-lookup"><span data-stu-id="a89ec-133">While the culture that Blazor selects by default might be sufficient for most users, consider offering a way for users to specify their preferred locale.</span></span> <span data-ttu-id="a89ec-134">Per un' Blazor WebAssembly app di esempio con selezione impostazioni cultura, vedere l' [`LocSample`](https://github.com/pranavkm/LocSample) app di esempio localizzazione.</span><span class="sxs-lookup"><span data-stu-id="a89ec-134">For a Blazor WebAssembly sample app with a culture picker, see the [`LocSample`](https://github.com/pranavkm/LocSample) localization sample app.</span></span>

### Blazor Server

<span data-ttu-id="a89ec-135">Blazor Server le app vengono localizzate usando il [middleware di localizzazione](xref:fundamentals/localization#localization-middleware).</span><span class="sxs-lookup"><span data-stu-id="a89ec-135">Blazor Server apps are localized using [Localization Middleware](xref:fundamentals/localization#localization-middleware).</span></span> <span data-ttu-id="a89ec-136">Il middleware seleziona le impostazioni cultura appropriate per gli utenti che richiedono risorse dall'app.</span><span class="sxs-lookup"><span data-stu-id="a89ec-136">The middleware selects the appropriate culture for users requesting resources from the app.</span></span>

<span data-ttu-id="a89ec-137">Le impostazioni cultura possono essere impostate utilizzando uno degli approcci seguenti:</span><span class="sxs-lookup"><span data-stu-id="a89ec-137">The culture can be set using one of the following approaches:</span></span>

* <span data-ttu-id="a89ec-138">[Cookies](#cookies)</span><span class="sxs-lookup"><span data-stu-id="a89ec-138">[Cookies](#cookies)</span></span>
* [<span data-ttu-id="a89ec-139">Fornire l'interfaccia utente per scegliere le impostazioni cultura</span><span class="sxs-lookup"><span data-stu-id="a89ec-139">Provide UI to choose the culture</span></span>](#provide-ui-to-choose-the-culture)

<span data-ttu-id="a89ec-140">Per altre informazioni ed esempi, vedere <xref:fundamentals/localization>.</span><span class="sxs-lookup"><span data-stu-id="a89ec-140">For more information and examples, see <xref:fundamentals/localization>.</span></span>

#### <a name="no-loccookies"></a><span data-ttu-id="a89ec-141">Cookies</span><span class="sxs-lookup"><span data-stu-id="a89ec-141">Cookies</span></span>

<span data-ttu-id="a89ec-142">Le impostazioni cultura cookie della localizzazione possono salvare in maniera permanente le impostazioni cultura dell'utente.</span><span class="sxs-lookup"><span data-stu-id="a89ec-142">A localization culture cookie can persist the user's culture.</span></span> <span data-ttu-id="a89ec-143">Il middleware di localizzazione legge l'oggetto cookie sulle richieste successive per impostare le impostazioni cultura dell'utente.</span><span class="sxs-lookup"><span data-stu-id="a89ec-143">The Localization Middleware reads the cookie on subsequent requests to set the user's culture.</span></span> 

<span data-ttu-id="a89ec-144">L'uso di un oggetto cookie garantisce che la connessione WebSocket possa propagare correttamente le impostazioni cultura.</span><span class="sxs-lookup"><span data-stu-id="a89ec-144">Use of a cookie ensures that the WebSocket connection can correctly propagate the culture.</span></span> <span data-ttu-id="a89ec-145">Se gli schemi di localizzazione sono basati sul percorso dell'URL o sulla stringa di query, lo schema potrebbe non essere in grado di funzionare con WebSocket, quindi non è possibile salvare in modo permanente le impostazioni cultura.</span><span class="sxs-lookup"><span data-stu-id="a89ec-145">If localization schemes are based on the URL path or query string, the scheme might not be able to work with WebSockets, thus fail to persist the culture.</span></span> <span data-ttu-id="a89ec-146">Pertanto, l'utilizzo delle impostazioni cultura di localizzazione cookie è l'approccio consigliato.</span><span class="sxs-lookup"><span data-stu-id="a89ec-146">Therefore, use of a localization culture cookie is the recommended approach.</span></span>

<span data-ttu-id="a89ec-147">Qualsiasi tecnica può essere utilizzata per assegnare impostazioni cultura se le impostazioni cultura vengono rese permanente in una localizzazione cookie .</span><span class="sxs-lookup"><span data-stu-id="a89ec-147">Any technique can be used to assign a culture if the culture is persisted in a localization cookie.</span></span> <span data-ttu-id="a89ec-148">Se l'app ha già uno schema di localizzazione stabilito per ASP.NET Core lato server, continuare a usare l'infrastruttura di localizzazione esistente dell'app e impostare le impostazioni cultura di localizzazione nello cookie schema dell'app.</span><span class="sxs-lookup"><span data-stu-id="a89ec-148">If the app already has an established localization scheme for server-side ASP.NET Core, continue to use the app's existing localization infrastructure and set the localization culture cookie within the app's scheme.</span></span>

<span data-ttu-id="a89ec-149">Nell'esempio seguente viene illustrato come impostare le impostazioni cultura correnti in un oggetto cookie che può essere letto dal middleware di localizzazione.</span><span class="sxs-lookup"><span data-stu-id="a89ec-149">The following example shows how to set the current culture in a cookie that can be read by the Localization Middleware.</span></span> <span data-ttu-id="a89ec-150">Creare un' Razor espressione nel `Pages/_Host.cshtml` file immediatamente all'interno del tag di apertura `<body>` :</span><span class="sxs-lookup"><span data-stu-id="a89ec-150">Create a Razor expression in the `Pages/_Host.cshtml` file immediately inside the opening `<body>` tag:</span></span>

```cshtml
@using System.Globalization
@using Microsoft.AspNetCore.Localization

...

<body>
    @{
        this.HttpContext.Response.Cookies.Append(
            CookieRequestCultureProvider.DefaultCookieName,
            CookieRequestCultureProvider.MakeCookieValue(
                new RequestCulture(
                    CultureInfo.CurrentCulture,
                    CultureInfo.CurrentUICulture)));
    }

    ...
</body>
```

<span data-ttu-id="a89ec-151">La localizzazione viene gestita dall'app nella sequenza di eventi seguente:</span><span class="sxs-lookup"><span data-stu-id="a89ec-151">Localization is handled by the app in the following sequence of events:</span></span>

1. <span data-ttu-id="a89ec-152">Il browser invia una richiesta HTTP iniziale all'app.</span><span class="sxs-lookup"><span data-stu-id="a89ec-152">The browser sends an initial HTTP request to the app.</span></span>
1. <span data-ttu-id="a89ec-153">Le impostazioni cultura vengono assegnate dal middleware di localizzazione.</span><span class="sxs-lookup"><span data-stu-id="a89ec-153">The culture is assigned by the Localization Middleware.</span></span>
1. <span data-ttu-id="a89ec-154">L' Razor espressione nella `_Host` pagina ( `_Host.cshtml` ) rende permanente le impostazioni cultura in un cookie come parte della risposta.</span><span class="sxs-lookup"><span data-stu-id="a89ec-154">The Razor expression in the `_Host` page (`_Host.cshtml`) persists the culture in a cookie as part of the response.</span></span>
1. <span data-ttu-id="a89ec-155">Il browser apre una connessione WebSocket per creare una Blazor Server sessione interattiva.</span><span class="sxs-lookup"><span data-stu-id="a89ec-155">The browser opens a WebSocket connection to create an interactive Blazor Server session.</span></span>
1. <span data-ttu-id="a89ec-156">Il middleware di localizzazione legge cookie e assegna le impostazioni cultura.</span><span class="sxs-lookup"><span data-stu-id="a89ec-156">The Localization Middleware reads the cookie and assigns the culture.</span></span>
1. <span data-ttu-id="a89ec-157">La Blazor Server sessione inizia con le impostazioni cultura corrette.</span><span class="sxs-lookup"><span data-stu-id="a89ec-157">The Blazor Server session begins with the correct culture.</span></span>

#### <a name="provide-ui-to-choose-the-culture"></a><span data-ttu-id="a89ec-158">Fornire l'interfaccia utente per scegliere le impostazioni cultura</span><span class="sxs-lookup"><span data-stu-id="a89ec-158">Provide UI to choose the culture</span></span>

<span data-ttu-id="a89ec-159">Per consentire a un utente di selezionare le impostazioni cultura, è consigliabile un *approccio basato su Reindirizzamento* .</span><span class="sxs-lookup"><span data-stu-id="a89ec-159">To provide UI to allow a user to select a culture, a *redirect-based approach* is recommended.</span></span> <span data-ttu-id="a89ec-160">Il processo è simile a quello che accade in un'app Web quando un utente tenta di accedere a una risorsa protetta.</span><span class="sxs-lookup"><span data-stu-id="a89ec-160">The process is similar to what happens in a web app when a user attempts to access a secure resource.</span></span> <span data-ttu-id="a89ec-161">L'utente viene reindirizzato a una pagina di accesso, quindi viene reindirizzato di nuovo alla risorsa originale.</span><span class="sxs-lookup"><span data-stu-id="a89ec-161">The user is redirected to a sign-in page and then redirected back to the original resource.</span></span> 

<span data-ttu-id="a89ec-162">L'app rende permanente le impostazioni cultura selezionate dall'utente tramite un reindirizzamento a un controller.</span><span class="sxs-lookup"><span data-stu-id="a89ec-162">The app persists the user's selected culture via a redirect to a controller.</span></span> <span data-ttu-id="a89ec-163">Il controller imposta le impostazioni cultura selezionate dall'utente in un cookie e reindirizza l'utente all'URI originale.</span><span class="sxs-lookup"><span data-stu-id="a89ec-163">The controller sets the user's selected culture into a cookie and redirects the user back to the original URI.</span></span>

<span data-ttu-id="a89ec-164">Stabilire un endpoint HTTP nel server per impostare le impostazioni cultura selezionate dall'utente in un oggetto cookie ed eseguire di nuovo il reindirizzamento all'URI originale:</span><span class="sxs-lookup"><span data-stu-id="a89ec-164">Establish an HTTP endpoint on the server to set the user's selected culture in a cookie and perform the redirect back to the original URI:</span></span>

```csharp
[Route("[controller]/[action]")]
public class CultureController : Controller
{
    public IActionResult SetCulture(string culture, string redirectUri)
    {
        if (culture != null)
        {
            HttpContext.Response.Cookies.Append(
                CookieRequestCultureProvider.DefaultCookieName,
                CookieRequestCultureProvider.MakeCookieValue(
                    new RequestCulture(culture)));
        }

        return LocalRedirect(redirectUri);
    }
}
```

> [!WARNING]
> <span data-ttu-id="a89ec-165">Usare il <xref:Microsoft.AspNetCore.Mvc.ControllerBase.LocalRedirect%2A> risultato dell'azione per impedire gli attacchi di reindirizzamento aperti.</span><span class="sxs-lookup"><span data-stu-id="a89ec-165">Use the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.LocalRedirect%2A> action result to prevent open redirect attacks.</span></span> <span data-ttu-id="a89ec-166">Per ulteriori informazioni, vedere <xref:security/preventing-open-redirects>.</span><span class="sxs-lookup"><span data-stu-id="a89ec-166">For more information, see <xref:security/preventing-open-redirects>.</span></span>

<span data-ttu-id="a89ec-167">Se l'app non è configurata per l'elaborazione delle azioni del controller:</span><span class="sxs-lookup"><span data-stu-id="a89ec-167">If the app isn't configured to process controller actions:</span></span>

* <span data-ttu-id="a89ec-168">Aggiungere i servizi MVC alla raccolta di servizi in `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="a89ec-168">Add MVC services to the service collection in `Startup.ConfigureServices`:</span></span>

  ```csharp
  services.AddControllers();
  ```

* <span data-ttu-id="a89ec-169">Aggiungi routing endpoint controller in `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="a89ec-169">Add controller endpoint routing in `Startup.Configure`:</span></span>

  ```csharp
  app.UseEndpoints(endpoints =>
  {
      endpoints.MapControllers();
      endpoints.MapBlazorHub();
      endpoints.MapFallbackToPage("/_Host");
  });
  ```

<span data-ttu-id="a89ec-170">Il componente seguente mostra un esempio di come eseguire il reindirizzamento iniziale quando l'utente seleziona le impostazioni cultura:</span><span class="sxs-lookup"><span data-stu-id="a89ec-170">The following component shows an example of how to perform the initial redirection when the user selects a culture:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="a89ec-171">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="a89ec-171">Additional resources</span></span>

* <xref:fundamentals/localization>
