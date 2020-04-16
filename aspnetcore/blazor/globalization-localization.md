---
title: ASP.NET Blazor la globalizzazione e la localizzazione dei ASP.NET core
author: guardrex
description: Informazioni su come rendere i componenti Razor accessibili agli utenti in più lingue e lingue.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/14/2020
no-loc:
- Blazor
- SignalR
uid: blazor/globalization-localization
ms.openlocfilehash: 0883a67e0129590f7a3fb68689eaba8d85e5523f
ms.sourcegitcommit: 6c8cff2d6753415c4f5d2ffda88159a7f6f7431a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81440714"
---
# <a name="aspnet-core-opno-locblazor-globalization-and-localization"></a><span data-ttu-id="faa3d-103">ASP.NET Blazor la globalizzazione e la localizzazione dei ASP.NET core</span><span class="sxs-lookup"><span data-stu-id="faa3d-103">ASP.NET Core Blazor globalization and localization</span></span>

<span data-ttu-id="faa3d-104">Di [Luke Latham](https://github.com/guardrex) e [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="faa3d-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="faa3d-105">I componenti Razor possono essere resi accessibili agli utenti in più lingue e lingue.</span><span class="sxs-lookup"><span data-stu-id="faa3d-105">Razor components can be made accessible to users in multiple cultures and languages.</span></span> <span data-ttu-id="faa3d-106">Sono disponibili i seguenti scenari di globalizzazione e localizzazione .NET:</span><span class="sxs-lookup"><span data-stu-id="faa3d-106">The following .NET globalization and localization scenarios are available:</span></span>

* <span data-ttu-id="faa3d-107">. Sistema di risorse di NET</span><span class="sxs-lookup"><span data-stu-id="faa3d-107">.NET's resources system</span></span>
* <span data-ttu-id="faa3d-108">Formattazione di numeri e date specifica delle impostazioni cultura</span><span class="sxs-lookup"><span data-stu-id="faa3d-108">Culture-specific number and date formatting</span></span>

<span data-ttu-id="faa3d-109">È attualmente supportato un set limitato di scenari di localizzazione di ASP.NET Core:A limited set of ASP.NET Core's localization scenarios are currently supported:</span><span class="sxs-lookup"><span data-stu-id="faa3d-109">A limited set of ASP.NET Core's localization scenarios are currently supported:</span></span>

* <span data-ttu-id="faa3d-110">`IStringLocalizer<>`*è supportato* nelle Blazor app.</span><span class="sxs-lookup"><span data-stu-id="faa3d-110">`IStringLocalizer<>` *is supported* in Blazor apps.</span></span>
* <span data-ttu-id="faa3d-111">`IHtmlLocalizer<>`, `IViewLocalizer<>`e la localizzazione delle annotazioni dei dati Blazor sono ASP.NET scenari MVC di base e non sono **supportati** nelle app.</span><span class="sxs-lookup"><span data-stu-id="faa3d-111">`IHtmlLocalizer<>`, `IViewLocalizer<>`, and Data Annotations localization are ASP.NET Core MVC scenarios and **not supported** in Blazor apps.</span></span>

<span data-ttu-id="faa3d-112">Per altre informazioni, vedere <xref:fundamentals/localization>.</span><span class="sxs-lookup"><span data-stu-id="faa3d-112">For more information, see <xref:fundamentals/localization>.</span></span>

## <a name="globalization"></a><span data-ttu-id="faa3d-113">Globalizzazione</span><span class="sxs-lookup"><span data-stu-id="faa3d-113">Globalization</span></span>

Blazor<span data-ttu-id="faa3d-114">La `@bind` funzionalità esegue formati e analizza i valori per la visualizzazione in base alle impostazioni cultura correnti dell'utente.</span><span class="sxs-lookup"><span data-stu-id="faa3d-114">'s `@bind` functionality performs formats and parses values for display based on the user's current culture.</span></span>

<span data-ttu-id="faa3d-115">È possibile accedere alle <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName> impostazioni cultura correnti dalla proprietà .</span><span class="sxs-lookup"><span data-stu-id="faa3d-115">The current culture can be accessed from the <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName> property.</span></span>

<span data-ttu-id="faa3d-116">[CultureInfo.InvariantCulture](xref:System.Globalization.CultureInfo.InvariantCulture) viene utilizzato per i`<input type="{TYPE}" />`seguenti tipi di campo ( ):</span><span class="sxs-lookup"><span data-stu-id="faa3d-116">[CultureInfo.InvariantCulture](xref:System.Globalization.CultureInfo.InvariantCulture) is used for the following field types (`<input type="{TYPE}" />`):</span></span>

* `date`
* `number`

<span data-ttu-id="faa3d-117">Il campo precedente digita:</span><span class="sxs-lookup"><span data-stu-id="faa3d-117">The preceding field types:</span></span>

* <span data-ttu-id="faa3d-118">Vengono visualizzati utilizzando le regole di formattazione basate su browser appropriate.</span><span class="sxs-lookup"><span data-stu-id="faa3d-118">Are displayed using their appropriate browser-based formatting rules.</span></span>
* <span data-ttu-id="faa3d-119">Non può contenere testo in formato libero.</span><span class="sxs-lookup"><span data-stu-id="faa3d-119">Can't contain free-form text.</span></span>
* <span data-ttu-id="faa3d-120">Fornire caratteristiche di interazione dell'utente in base all'implementazione del browser.</span><span class="sxs-lookup"><span data-stu-id="faa3d-120">Provide user interaction characteristics based on the browser's implementation.</span></span>

<span data-ttu-id="faa3d-121">I seguenti tipi di campo hanno requisiti di Blazor formattazione specifici e non sono attualmente supportati da perché non sono supportati da tutti i principali browser:</span><span class="sxs-lookup"><span data-stu-id="faa3d-121">The following field types have specific formatting requirements and aren't currently supported by Blazor because they aren't supported by all major browsers:</span></span>

* `datetime-local`
* `month`
* `week`

<span data-ttu-id="faa3d-122">`@bind`supporta il `@bind:culture` parametro <xref:System.Globalization.CultureInfo?displayProperty=fullName> per fornire un per l'analisi e la formattazione di un valore.</span><span class="sxs-lookup"><span data-stu-id="faa3d-122">`@bind` supports the `@bind:culture` parameter to provide a <xref:System.Globalization.CultureInfo?displayProperty=fullName> for parsing and formatting a value.</span></span> <span data-ttu-id="faa3d-123">La specifica di impostazioni cultura non `date` `number` è consigliata quando si utilizzano i tipi di campo e .</span><span class="sxs-lookup"><span data-stu-id="faa3d-123">Specifying a culture isn't recommended when using the `date` and `number` field types.</span></span> <span data-ttu-id="faa3d-124">`date`e `number` dispongono Blazor di un supporto integrato che fornisce le impostazioni cultura necessarie.</span><span class="sxs-lookup"><span data-stu-id="faa3d-124">`date` and `number` have built-in Blazor support that provides the required culture.</span></span>

## <a name="localization"></a><span data-ttu-id="faa3d-125">Localizzazione</span><span class="sxs-lookup"><span data-stu-id="faa3d-125">Localization</span></span>

### <a name="opno-locblazor-webassembly"></a>Blazor<span data-ttu-id="faa3d-126">Assembly Web</span><span class="sxs-lookup"><span data-stu-id="faa3d-126"> WebAssembly</span></span>

<span data-ttu-id="faa3d-127">Per impostazione predefinita, Blazor Blazor la configurazione del linker 's per le app WebAssembly elimina le informazioni di internazionalizzazione, ad eccezione delle impostazioni locali richieste in modo esplicito.</span><span class="sxs-lookup"><span data-stu-id="faa3d-127">By default, Blazor's linker configuration for Blazor WebAssembly apps strips out internationalization information except for locales explicitly requested.</span></span> <span data-ttu-id="faa3d-128">Per ulteriori informazioni e indicazioni sul controllo <xref:host-and-deploy/blazor/configure-linker#configure-the-linker-for-internationalization>del comportamento del linker, vedere .</span><span class="sxs-lookup"><span data-stu-id="faa3d-128">For more information and guidance on controlling the linker's behavior, see <xref:host-and-deploy/blazor/configure-linker#configure-the-linker-for-internationalization>.</span></span>

<!-- HOLD FOR 3.2 PREVIEW 4: Replace prior paragraph with ...

Blazor WebAssembly apps set the culture using the user's [language preference](https://developer.mozilla.org/docs/Web/API/NavigatorLanguage/languages).

To explicitly configure the culture, set `CultureInfo.DefaultThreadCurrentCulture` and `CultureInfo.DefaultThreadCurrentUICulture` in `Program.Main`.

By default, Blazor's linker configuration for Blazor WebAssembly apps strips out internationalization information except for locales explicitly requested. For more information and guidance on controlling the linker's behavior, see <xref:host-and-deploy/blazor/configure-linker#configure-the-linker-for-internationalization>.

While the culture that Blazor selects by default might be sufficient for most users, consider offering a way for users to specify their preferred locale. For a Blazor WebAssembly sample app with a culture picker, see the [LocSample](https://github.com/pranavkm/LocSample) localization sample app.

-->

### <a name="opno-locblazor-server"></a>Blazor<span data-ttu-id="faa3d-129">Server</span><span class="sxs-lookup"><span data-stu-id="faa3d-129"> Server</span></span>

Blazor<span data-ttu-id="faa3d-130">Le app server vengono localizzate tramite [il middleware di localizzazione.](xref:fundamentals/localization#localization-middleware)</span><span class="sxs-lookup"><span data-stu-id="faa3d-130"> Server apps are localized using [Localization Middleware](xref:fundamentals/localization#localization-middleware).</span></span> <span data-ttu-id="faa3d-131">Il middleware seleziona le impostazioni cultura appropriate per gli utenti che richiedono risorse dall'app.</span><span class="sxs-lookup"><span data-stu-id="faa3d-131">The middleware selects the appropriate culture for users requesting resources from the app.</span></span>

<span data-ttu-id="faa3d-132">Le impostazioni cultura possono essere impostate utilizzando uno dei seguenti approcci:</span><span class="sxs-lookup"><span data-stu-id="faa3d-132">The culture can be set using one of the following approaches:</span></span>

* [<span data-ttu-id="faa3d-133">Cookie</span><span class="sxs-lookup"><span data-stu-id="faa3d-133">Cookies</span></span>](#cookies)
* [<span data-ttu-id="faa3d-134">Fornire l'interfaccia utente per scegliere le impostazioni culturaProvide UI to choose the culture</span><span class="sxs-lookup"><span data-stu-id="faa3d-134">Provide UI to choose the culture</span></span>](#provide-ui-to-choose-the-culture)

<span data-ttu-id="faa3d-135">Per altre informazioni ed esempi, vedere <xref:fundamentals/localization>.</span><span class="sxs-lookup"><span data-stu-id="faa3d-135">For more information and examples, see <xref:fundamentals/localization>.</span></span>

#### <a name="cookies"></a><span data-ttu-id="faa3d-136">Cookie</span><span class="sxs-lookup"><span data-stu-id="faa3d-136">Cookies</span></span>

<span data-ttu-id="faa3d-137">Un cookie delle impostazioni cultura di localizzazione può rendere persistenti le impostazioni cultura dell'utente.</span><span class="sxs-lookup"><span data-stu-id="faa3d-137">A localization culture cookie can persist the user's culture.</span></span> <span data-ttu-id="faa3d-138">Il cookie viene `OnGet` creato dal metodo della pagina host dell'app (*Pages/Host.cshtml.cs*).</span><span class="sxs-lookup"><span data-stu-id="faa3d-138">The cookie is created by the `OnGet` method of the app's host page (*Pages/Host.cshtml.cs*).</span></span> <span data-ttu-id="faa3d-139">Il middleware di localizzazione legge il cookie nelle richieste successive per impostare le impostazioni cultura dell'utente.</span><span class="sxs-lookup"><span data-stu-id="faa3d-139">The Localization Middleware reads the cookie on subsequent requests to set the user's culture.</span></span> 

<span data-ttu-id="faa3d-140">L'uso di un cookie garantisce che la connessione WebSocket possa propagare correttamente le impostazioni cultura.</span><span class="sxs-lookup"><span data-stu-id="faa3d-140">Use of a cookie ensures that the WebSocket connection can correctly propagate the culture.</span></span> <span data-ttu-id="faa3d-141">Se gli schemi di localizzazione sono basati sul percorso URL o sulla stringa di query, lo schema potrebbe non essere in grado di funzionare con WebSockets, pertanto non è in grado di mantenere le impostazioni cultura.</span><span class="sxs-lookup"><span data-stu-id="faa3d-141">If localization schemes are based on the URL path or query string, the scheme might not be able to work with WebSockets, thus fail to persist the culture.</span></span> <span data-ttu-id="faa3d-142">Pertanto, l'utilizzo di un cookie delle impostazioni cultura di localizzazione è l'approccio consigliato.</span><span class="sxs-lookup"><span data-stu-id="faa3d-142">Therefore, use of a localization culture cookie is the recommended approach.</span></span>

<span data-ttu-id="faa3d-143">Qualsiasi tecnica può essere utilizzata per assegnare impostazioni cultura se le impostazioni cultura vengono mantenute in un cookie di localizzazione.</span><span class="sxs-lookup"><span data-stu-id="faa3d-143">Any technique can be used to assign a culture if the culture is persisted in a localization cookie.</span></span> <span data-ttu-id="faa3d-144">Se l'app ha già uno schema di localizzazione stabilito per ASP.NET Core sul lato server, continua a usare l'infrastruttura di localizzazione esistente dell'app e imposta il cookie delle impostazioni cultura di localizzazione all'interno dello schema dell'app.</span><span class="sxs-lookup"><span data-stu-id="faa3d-144">If the app already has an established localization scheme for server-side ASP.NET Core, continue to use the app's existing localization infrastructure and set the localization culture cookie within the app's scheme.</span></span>

<span data-ttu-id="faa3d-145">Nell'esempio seguente viene illustrato come impostare le impostazioni cultura correnti in un cookie che può essere letto dal Middleware di localizzazione.</span><span class="sxs-lookup"><span data-stu-id="faa3d-145">The following example shows how to set the current culture in a cookie that can be read by the Localization Middleware.</span></span> <span data-ttu-id="faa3d-146">Creare un file *Pages/_Host.cshtml.cs* con Blazor il contenuto seguente nell'app Server:</span><span class="sxs-lookup"><span data-stu-id="faa3d-146">Create a *Pages/_Host.cshtml.cs* file with the following contents in the Blazor Server app:</span></span>

```csharp
public class HostModel : PageModel
{
    public void OnGet()
    {
        HttpContext.Response.Cookies.Append(
            CookieRequestCultureProvider.DefaultCookieName,
            CookieRequestCultureProvider.MakeCookieValue(
                new RequestCulture(
                    CultureInfo.CurrentCulture,
                    CultureInfo.CurrentUICulture)));
    }
}
```

<span data-ttu-id="faa3d-147">La localizzazione viene gestita dall'app nella sequenza di eventi seguente:</span><span class="sxs-lookup"><span data-stu-id="faa3d-147">Localization is handled by the app in the following sequence of events:</span></span>

1. <span data-ttu-id="faa3d-148">Il browser invia una richiesta HTTP iniziale all'app.</span><span class="sxs-lookup"><span data-stu-id="faa3d-148">The browser sends an initial HTTP request to the app.</span></span>
1. <span data-ttu-id="faa3d-149">Le impostazioni cultura vengono assegnate dal middleware di localizzazione.</span><span class="sxs-lookup"><span data-stu-id="faa3d-149">The culture is assigned by the Localization Middleware.</span></span>
1. <span data-ttu-id="faa3d-150">Il `OnGet` metodo in *_Host.cshtml.cs* rende persistenti le impostazioni cultura in un cookie come parte della risposta.</span><span class="sxs-lookup"><span data-stu-id="faa3d-150">The `OnGet` method in *_Host.cshtml.cs* persists the culture in a cookie as part of the response.</span></span>
1. <span data-ttu-id="faa3d-151">Il browser apre una connessione WebSocket per creare una sessione Server interattiva. Blazor</span><span class="sxs-lookup"><span data-stu-id="faa3d-151">The browser opens a WebSocket connection to create an interactive Blazor Server session.</span></span>
1. <span data-ttu-id="faa3d-152">Il middleware di localizzazione legge il cookie e assegna le impostazioni cultura.</span><span class="sxs-lookup"><span data-stu-id="faa3d-152">The Localization Middleware reads the cookie and assigns the culture.</span></span>
1. <span data-ttu-id="faa3d-153">La Blazor sessione del server inizia con le impostazioni cultura corrette.</span><span class="sxs-lookup"><span data-stu-id="faa3d-153">The Blazor Server session begins with the correct culture.</span></span>

#### <a name="provide-ui-to-choose-the-culture"></a><span data-ttu-id="faa3d-154">Fornire l'interfaccia utente per scegliere le impostazioni culturaProvide UI to choose the culture</span><span class="sxs-lookup"><span data-stu-id="faa3d-154">Provide UI to choose the culture</span></span>

<span data-ttu-id="faa3d-155">Per fornire l'interfaccia utente per consentire a un utente di selezionare impostazioni cultura, è consigliabile un *approccio basato sul reindirizzamento.*</span><span class="sxs-lookup"><span data-stu-id="faa3d-155">To provide UI to allow a user to select a culture, a *redirect-based approach* is recommended.</span></span> <span data-ttu-id="faa3d-156">Il processo è simile a quello che accade in un'app Web quando un utente tenta di accedere a una risorsa protetta.</span><span class="sxs-lookup"><span data-stu-id="faa3d-156">The process is similar to what happens in a web app when a user attempts to access a secure resource.</span></span> <span data-ttu-id="faa3d-157">L'utente viene reindirizzato a una pagina di accesso e quindi reindirizzato alla risorsa originale.</span><span class="sxs-lookup"><span data-stu-id="faa3d-157">The user is redirected to a sign-in page and then redirected back to the original resource.</span></span> 

<span data-ttu-id="faa3d-158">L'app mantiene le impostazioni cultura selezionate dell'utente tramite un reindirizzamento a un controller.</span><span class="sxs-lookup"><span data-stu-id="faa3d-158">The app persists the user's selected culture via a redirect to a controller.</span></span> <span data-ttu-id="faa3d-159">Il controller imposta le impostazioni cultura selezionate dell'utente in un cookie e reindirizza l'utente all'URI originale.</span><span class="sxs-lookup"><span data-stu-id="faa3d-159">The controller sets the user's selected culture into a cookie and redirects the user back to the original URI.</span></span>

<span data-ttu-id="faa3d-160">Stabilire un endpoint HTTP nel server per impostare le impostazioni cultura selezionate dell'utente in un cookie ed eseguire il reindirizzamento all'URI originale:</span><span class="sxs-lookup"><span data-stu-id="faa3d-160">Establish an HTTP endpoint on the server to set the user's selected culture in a cookie and perform the redirect back to the original URI:</span></span>

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
> <span data-ttu-id="faa3d-161">Utilizzare `LocalRedirect` il risultato dell'azione per impedire attacchi di reindirizzamento aperto.</span><span class="sxs-lookup"><span data-stu-id="faa3d-161">Use the `LocalRedirect` action result to prevent open redirect attacks.</span></span> <span data-ttu-id="faa3d-162">Per altre informazioni, vedere <xref:security/preventing-open-redirects>.</span><span class="sxs-lookup"><span data-stu-id="faa3d-162">For more information, see <xref:security/preventing-open-redirects>.</span></span>

<span data-ttu-id="faa3d-163">Il componente seguente mostra un esempio di come eseguire il reindirizzamento iniziale quando l'utente seleziona le impostazioni cultura:The following component shows an example of how to perform the initial redirection when the user selects a culture:</span><span class="sxs-lookup"><span data-stu-id="faa3d-163">The following component shows an example of how to perform the initial redirection when the user selects a culture:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="faa3d-164">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="faa3d-164">Additional resources</span></span>

* <xref:fundamentals/localization>
