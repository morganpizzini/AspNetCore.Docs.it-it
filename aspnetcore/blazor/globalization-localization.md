---
<span data-ttu-id="a43d6-101">title: "ASP.NET Core Blazor globalizzazione e localizzazione" Author: Description: "informazioni su come rendere i Razor componenti accessibili agli utenti in più impostazioni cultura e linguaggi".</span><span class="sxs-lookup"><span data-stu-id="a43d6-101">title: 'ASP.NET Core Blazor globalization and localization' author: description: 'Learn how to make Razor components accessible to users in multiple cultures and languages.'</span></span>
<span data-ttu-id="a43d6-102">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="a43d6-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="a43d6-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="a43d6-103">'Blazor'</span></span>
- <span data-ttu-id="a43d6-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="a43d6-104">'Identity'</span></span>
- <span data-ttu-id="a43d6-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="a43d6-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="a43d6-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="a43d6-106">'Razor'</span></span>
- <span data-ttu-id="a43d6-107">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="a43d6-107">'SignalR' uid:</span></span> 

---
# <a name="aspnet-core-blazor-globalization-and-localization"></a><span data-ttu-id="a43d6-108">BlazorGlobalizzazione e localizzazione ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a43d6-108">ASP.NET Core Blazor globalization and localization</span></span>

<span data-ttu-id="a43d6-109">Di [Luke Latham](https://github.com/guardrex) e [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="a43d6-109">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

Razor<span data-ttu-id="a43d6-110">i componenti possono essere resi accessibili agli utenti in più impostazioni cultura e lingue.</span><span class="sxs-lookup"><span data-stu-id="a43d6-110"> components can be made accessible to users in multiple cultures and languages.</span></span> <span data-ttu-id="a43d6-111">Sono disponibili i seguenti scenari di globalizzazione e localizzazione di .NET:</span><span class="sxs-lookup"><span data-stu-id="a43d6-111">The following .NET globalization and localization scenarios are available:</span></span>

* <span data-ttu-id="a43d6-112">. Sistema di risorse di NET</span><span class="sxs-lookup"><span data-stu-id="a43d6-112">.NET's resources system</span></span>
* <span data-ttu-id="a43d6-113">Formattazione di numeri e date specifiche delle impostazioni cultura</span><span class="sxs-lookup"><span data-stu-id="a43d6-113">Culture-specific number and date formatting</span></span>

<span data-ttu-id="a43d6-114">Sono attualmente supportati un set limitato di scenari di localizzazione di ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="a43d6-114">A limited set of ASP.NET Core's localization scenarios are currently supported:</span></span>

* <span data-ttu-id="a43d6-115"><xref:Microsoft.Extensions.Localization.IStringLocalizer>e <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> *sono supportati* nelle Blazor app.</span><span class="sxs-lookup"><span data-stu-id="a43d6-115"><xref:Microsoft.Extensions.Localization.IStringLocalizer> and <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> *are supported* in Blazor apps.</span></span>
* <span data-ttu-id="a43d6-116"><xref:Microsoft.AspNetCore.Mvc.Localization.IHtmlLocalizer>la <xref:Microsoft.AspNetCore.Mvc.Localization.IViewLocalizer> localizzazione delle annotazioni dei dati, e è ASP.NET Core scenari MVC e **non è supportata** nelle Blazor app.</span><span class="sxs-lookup"><span data-stu-id="a43d6-116"><xref:Microsoft.AspNetCore.Mvc.Localization.IHtmlLocalizer>, <xref:Microsoft.AspNetCore.Mvc.Localization.IViewLocalizer>, and Data Annotations localization are ASP.NET Core MVC scenarios and **not supported** in Blazor apps.</span></span>

<span data-ttu-id="a43d6-117">Per altre informazioni, vedere <xref:fundamentals/localization>.</span><span class="sxs-lookup"><span data-stu-id="a43d6-117">For more information, see <xref:fundamentals/localization>.</span></span>

## <a name="globalization"></a><span data-ttu-id="a43d6-118">Globalizzazione</span><span class="sxs-lookup"><span data-stu-id="a43d6-118">Globalization</span></span>

Blazor<span data-ttu-id="a43d6-119">la [`@bind`](xref:mvc/views/razor#bind) funzionalità di esegue formati e analizza i valori per la visualizzazione in base alle impostazioni cultura correnti dell'utente.</span><span class="sxs-lookup"><span data-stu-id="a43d6-119">'s [`@bind`](xref:mvc/views/razor#bind) functionality performs formats and parses values for display based on the user's current culture.</span></span>

<span data-ttu-id="a43d6-120">È possibile accedere alle impostazioni cultura correnti dalla <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName> Proprietà.</span><span class="sxs-lookup"><span data-stu-id="a43d6-120">The current culture can be accessed from the <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName> property.</span></span>

<span data-ttu-id="a43d6-121"><xref:System.Globalization.CultureInfo.InvariantCulture?displayProperty=nameWithType>viene usato per i tipi di campo seguenti ( `<input type="{TYPE}" />` ):</span><span class="sxs-lookup"><span data-stu-id="a43d6-121"><xref:System.Globalization.CultureInfo.InvariantCulture?displayProperty=nameWithType> is used for the following field types (`<input type="{TYPE}" />`):</span></span>

* `date`
* `number`

<span data-ttu-id="a43d6-122">I tipi di campo precedenti:</span><span class="sxs-lookup"><span data-stu-id="a43d6-122">The preceding field types:</span></span>

* <span data-ttu-id="a43d6-123">Vengono visualizzati utilizzando le regole di formattazione appropriate basate su browser.</span><span class="sxs-lookup"><span data-stu-id="a43d6-123">Are displayed using their appropriate browser-based formatting rules.</span></span>
* <span data-ttu-id="a43d6-124">Non può contenere testo in formato libero.</span><span class="sxs-lookup"><span data-stu-id="a43d6-124">Can't contain free-form text.</span></span>
* <span data-ttu-id="a43d6-125">Fornire le caratteristiche di interazione utente in base all'implementazione del browser.</span><span class="sxs-lookup"><span data-stu-id="a43d6-125">Provide user interaction characteristics based on the browser's implementation.</span></span>

<span data-ttu-id="a43d6-126">I tipi di campo seguenti hanno requisiti di formattazione specifici e non sono attualmente supportati da perché non sono Blazor supportati da tutti i browser principali:</span><span class="sxs-lookup"><span data-stu-id="a43d6-126">The following field types have specific formatting requirements and aren't currently supported by Blazor because they aren't supported by all major browsers:</span></span>

* `datetime-local`
* `month`
* `week`

<span data-ttu-id="a43d6-127">[`@bind`](xref:mvc/views/razor#bind)supporta il `@bind:culture` parametro per fornire un oggetto <xref:System.Globalization.CultureInfo?displayProperty=fullName> per l'analisi e la formattazione di un valore.</span><span class="sxs-lookup"><span data-stu-id="a43d6-127">[`@bind`](xref:mvc/views/razor#bind) supports the `@bind:culture` parameter to provide a <xref:System.Globalization.CultureInfo?displayProperty=fullName> for parsing and formatting a value.</span></span> <span data-ttu-id="a43d6-128">Non è consigliabile specificare impostazioni cultura quando si usano i `date` `number` tipi di campo e.</span><span class="sxs-lookup"><span data-stu-id="a43d6-128">Specifying a culture isn't recommended when using the `date` and `number` field types.</span></span> <span data-ttu-id="a43d6-129">`date`e `number` dispongono di supporto incorporato Blazor che fornisce le impostazioni cultura richieste.</span><span class="sxs-lookup"><span data-stu-id="a43d6-129">`date` and `number` have built-in Blazor support that provides the required culture.</span></span>

## <a name="localization"></a><span data-ttu-id="a43d6-130">Localizzazione</span><span class="sxs-lookup"><span data-stu-id="a43d6-130">Localization</span></span>

### <a name="blazor-webassembly"></a>Blazor<span data-ttu-id="a43d6-131">Webassembly</span><span class="sxs-lookup"><span data-stu-id="a43d6-131"> WebAssembly</span></span>

Blazor<span data-ttu-id="a43d6-132">Le app webassembly impostano le impostazioni cultura usando le [Preferenze della lingua](https://developer.mozilla.org/docs/Web/API/NavigatorLanguage/languages)dell'utente.</span><span class="sxs-lookup"><span data-stu-id="a43d6-132"> WebAssembly apps set the culture using the user's [language preference](https://developer.mozilla.org/docs/Web/API/NavigatorLanguage/languages).</span></span>

<span data-ttu-id="a43d6-133">Per configurare in modo esplicito le impostazioni cultura, impostare <xref:System.Globalization.CultureInfo.DefaultThreadCurrentCulture?displayProperty=nameWithType> e <xref:System.Globalization.CultureInfo.DefaultThreadCurrentUICulture?displayProperty=nameWithType> in `Program.Main` .</span><span class="sxs-lookup"><span data-stu-id="a43d6-133">To explicitly configure the culture, set <xref:System.Globalization.CultureInfo.DefaultThreadCurrentCulture?displayProperty=nameWithType> and <xref:System.Globalization.CultureInfo.DefaultThreadCurrentUICulture?displayProperty=nameWithType> in `Program.Main`.</span></span>

<span data-ttu-id="a43d6-134">Per impostazione predefinita, Blazor la configurazione del linker per le Blazor app webassembly rimuove le informazioni di internazionalizzazione ad eccezione delle impostazioni locali richieste in modo esplicito.</span><span class="sxs-lookup"><span data-stu-id="a43d6-134">By default, Blazor's linker configuration for Blazor WebAssembly apps strips out internationalization information except for locales explicitly requested.</span></span> <span data-ttu-id="a43d6-135">Per ulteriori informazioni e istruzioni sul controllo del comportamento del linker, vedere <xref:host-and-deploy/blazor/configure-linker#configure-the-linker-for-internationalization> .</span><span class="sxs-lookup"><span data-stu-id="a43d6-135">For more information and guidance on controlling the linker's behavior, see <xref:host-and-deploy/blazor/configure-linker#configure-the-linker-for-internationalization>.</span></span>

<span data-ttu-id="a43d6-136">Mentre le impostazioni cultura che Blazor selezionano per impostazione predefinita potrebbero essere sufficienti per la maggior parte degli utenti, è consigliabile offrire agli utenti un modo per specificare le impostazioni locali preferite.</span><span class="sxs-lookup"><span data-stu-id="a43d6-136">While the culture that Blazor selects by default might be sufficient for most users, consider offering a way for users to specify their preferred locale.</span></span> <span data-ttu-id="a43d6-137">Per un' Blazor app di esempio webassembly con selezione impostazioni cultura, vedere l'app di esempio [LocSample](https://github.com/pranavkm/LocSample) Localization.</span><span class="sxs-lookup"><span data-stu-id="a43d6-137">For a Blazor WebAssembly sample app with a culture picker, see the [LocSample](https://github.com/pranavkm/LocSample) localization sample app.</span></span>

### <a name="blazor-server"></a>Blazor<span data-ttu-id="a43d6-138">Server</span><span class="sxs-lookup"><span data-stu-id="a43d6-138"> Server</span></span>

Blazor<span data-ttu-id="a43d6-139">Le app Server sono localizzate usando il [middleware di localizzazione](xref:fundamentals/localization#localization-middleware).</span><span class="sxs-lookup"><span data-stu-id="a43d6-139"> Server apps are localized using [Localization Middleware](xref:fundamentals/localization#localization-middleware).</span></span> <span data-ttu-id="a43d6-140">Il middleware seleziona le impostazioni cultura appropriate per gli utenti che richiedono risorse dall'app.</span><span class="sxs-lookup"><span data-stu-id="a43d6-140">The middleware selects the appropriate culture for users requesting resources from the app.</span></span>

<span data-ttu-id="a43d6-141">Le impostazioni cultura possono essere impostate utilizzando uno degli approcci seguenti:</span><span class="sxs-lookup"><span data-stu-id="a43d6-141">The culture can be set using one of the following approaches:</span></span>

* [<span data-ttu-id="a43d6-142">Cookie</span><span class="sxs-lookup"><span data-stu-id="a43d6-142">Cookies</span></span>](#cookies)
* [<span data-ttu-id="a43d6-143">Fornire l'interfaccia utente per scegliere le impostazioni cultura</span><span class="sxs-lookup"><span data-stu-id="a43d6-143">Provide UI to choose the culture</span></span>](#provide-ui-to-choose-the-culture)

<span data-ttu-id="a43d6-144">Per altre informazioni ed esempi, vedere <xref:fundamentals/localization>.</span><span class="sxs-lookup"><span data-stu-id="a43d6-144">For more information and examples, see <xref:fundamentals/localization>.</span></span>

#### <a name="cookies"></a><span data-ttu-id="a43d6-145">Cookie</span><span class="sxs-lookup"><span data-stu-id="a43d6-145">Cookies</span></span>

<span data-ttu-id="a43d6-146">Un cookie di impostazioni cultura di localizzazione può salvare in maniera permanente le impostazioni cultura dell'utente.</span><span class="sxs-lookup"><span data-stu-id="a43d6-146">A localization culture cookie can persist the user's culture.</span></span> <span data-ttu-id="a43d6-147">Il cookie viene creato tramite il `OnGet` metodo della pagina host dell'app (*pages/host. cshtml. cs*).</span><span class="sxs-lookup"><span data-stu-id="a43d6-147">The cookie is created by the `OnGet` method of the app's host page (*Pages/Host.cshtml.cs*).</span></span> <span data-ttu-id="a43d6-148">Il middleware di localizzazione legge il cookie nelle richieste successive per impostare le impostazioni cultura dell'utente.</span><span class="sxs-lookup"><span data-stu-id="a43d6-148">The Localization Middleware reads the cookie on subsequent requests to set the user's culture.</span></span> 

<span data-ttu-id="a43d6-149">L'uso di un cookie garantisce che la connessione WebSocket possa propagare correttamente le impostazioni cultura.</span><span class="sxs-lookup"><span data-stu-id="a43d6-149">Use of a cookie ensures that the WebSocket connection can correctly propagate the culture.</span></span> <span data-ttu-id="a43d6-150">Se gli schemi di localizzazione sono basati sul percorso dell'URL o sulla stringa di query, lo schema potrebbe non essere in grado di funzionare con WebSocket, quindi non è possibile salvare in modo permanente le impostazioni cultura.</span><span class="sxs-lookup"><span data-stu-id="a43d6-150">If localization schemes are based on the URL path or query string, the scheme might not be able to work with WebSockets, thus fail to persist the culture.</span></span> <span data-ttu-id="a43d6-151">Pertanto, l'utilizzo di un cookie di impostazioni cultura di localizzazione è l'approccio consigliato.</span><span class="sxs-lookup"><span data-stu-id="a43d6-151">Therefore, use of a localization culture cookie is the recommended approach.</span></span>

<span data-ttu-id="a43d6-152">Qualsiasi tecnica può essere utilizzata per assegnare impostazioni cultura se le impostazioni cultura vengono rese permanente in un cookie di localizzazione.</span><span class="sxs-lookup"><span data-stu-id="a43d6-152">Any technique can be used to assign a culture if the culture is persisted in a localization cookie.</span></span> <span data-ttu-id="a43d6-153">Se l'app dispone già di uno schema di localizzazione stabilito per ASP.NET Core lato server, continuare a usare l'infrastruttura di localizzazione esistente dell'app e impostare il cookie delle impostazioni cultura di localizzazione nello schema dell'app.</span><span class="sxs-lookup"><span data-stu-id="a43d6-153">If the app already has an established localization scheme for server-side ASP.NET Core, continue to use the app's existing localization infrastructure and set the localization culture cookie within the app's scheme.</span></span>

<span data-ttu-id="a43d6-154">Nell'esempio seguente viene illustrato come impostare le impostazioni cultura correnti in un cookie che può essere letto dal middleware di localizzazione.</span><span class="sxs-lookup"><span data-stu-id="a43d6-154">The following example shows how to set the current culture in a cookie that can be read by the Localization Middleware.</span></span> <span data-ttu-id="a43d6-155">Creare un file *pages/_Host. cshtml. cs* con il contenuto seguente nell' Blazor app Server:</span><span class="sxs-lookup"><span data-stu-id="a43d6-155">Create a *Pages/_Host.cshtml.cs* file with the following contents in the Blazor Server app:</span></span>

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

<span data-ttu-id="a43d6-156">La localizzazione viene gestita dall'app nella sequenza di eventi seguente:</span><span class="sxs-lookup"><span data-stu-id="a43d6-156">Localization is handled by the app in the following sequence of events:</span></span>

1. <span data-ttu-id="a43d6-157">Il browser invia una richiesta HTTP iniziale all'app.</span><span class="sxs-lookup"><span data-stu-id="a43d6-157">The browser sends an initial HTTP request to the app.</span></span>
1. <span data-ttu-id="a43d6-158">Le impostazioni cultura vengono assegnate dal middleware di localizzazione.</span><span class="sxs-lookup"><span data-stu-id="a43d6-158">The culture is assigned by the Localization Middleware.</span></span>
1. <span data-ttu-id="a43d6-159">Il `OnGet` metodo in *_Host. cshtml. cs* rende permanente le impostazioni cultura di un cookie come parte della risposta.</span><span class="sxs-lookup"><span data-stu-id="a43d6-159">The `OnGet` method in *_Host.cshtml.cs* persists the culture in a cookie as part of the response.</span></span>
1. <span data-ttu-id="a43d6-160">Il browser apre una connessione WebSocket per creare una Blazor sessione del server interattiva.</span><span class="sxs-lookup"><span data-stu-id="a43d6-160">The browser opens a WebSocket connection to create an interactive Blazor Server session.</span></span>
1. <span data-ttu-id="a43d6-161">Il middleware di localizzazione legge il cookie e assegna le impostazioni cultura.</span><span class="sxs-lookup"><span data-stu-id="a43d6-161">The Localization Middleware reads the cookie and assigns the culture.</span></span>
1. <span data-ttu-id="a43d6-162">La Blazor sessione del server inizia con le impostazioni cultura corrette.</span><span class="sxs-lookup"><span data-stu-id="a43d6-162">The Blazor Server session begins with the correct culture.</span></span>

#### <a name="provide-ui-to-choose-the-culture"></a><span data-ttu-id="a43d6-163">Fornire l'interfaccia utente per scegliere le impostazioni cultura</span><span class="sxs-lookup"><span data-stu-id="a43d6-163">Provide UI to choose the culture</span></span>

<span data-ttu-id="a43d6-164">Per consentire a un utente di selezionare le impostazioni cultura, è consigliabile un *approccio basato su Reindirizzamento* .</span><span class="sxs-lookup"><span data-stu-id="a43d6-164">To provide UI to allow a user to select a culture, a *redirect-based approach* is recommended.</span></span> <span data-ttu-id="a43d6-165">Il processo è simile a quello che accade in un'app Web quando un utente tenta di accedere a una risorsa protetta.</span><span class="sxs-lookup"><span data-stu-id="a43d6-165">The process is similar to what happens in a web app when a user attempts to access a secure resource.</span></span> <span data-ttu-id="a43d6-166">L'utente viene reindirizzato a una pagina di accesso, quindi viene reindirizzato di nuovo alla risorsa originale.</span><span class="sxs-lookup"><span data-stu-id="a43d6-166">The user is redirected to a sign-in page and then redirected back to the original resource.</span></span> 

<span data-ttu-id="a43d6-167">L'app rende permanente le impostazioni cultura selezionate dall'utente tramite un reindirizzamento a un controller.</span><span class="sxs-lookup"><span data-stu-id="a43d6-167">The app persists the user's selected culture via a redirect to a controller.</span></span> <span data-ttu-id="a43d6-168">Il controller imposta le impostazioni cultura selezionate dall'utente in un cookie e reindirizza di nuovo l'utente all'URI originale.</span><span class="sxs-lookup"><span data-stu-id="a43d6-168">The controller sets the user's selected culture into a cookie and redirects the user back to the original URI.</span></span>

<span data-ttu-id="a43d6-169">Stabilire un endpoint HTTP nel server per impostare le impostazioni cultura selezionate dall'utente in un cookie ed eseguire di nuovo il reindirizzamento all'URI originale:</span><span class="sxs-lookup"><span data-stu-id="a43d6-169">Establish an HTTP endpoint on the server to set the user's selected culture in a cookie and perform the redirect back to the original URI:</span></span>

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
> <span data-ttu-id="a43d6-170">Usare il <xref:Microsoft.AspNetCore.Mvc.ControllerBase.LocalRedirect%2A> risultato dell'azione per impedire gli attacchi di reindirizzamento aperti.</span><span class="sxs-lookup"><span data-stu-id="a43d6-170">Use the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.LocalRedirect%2A> action result to prevent open redirect attacks.</span></span> <span data-ttu-id="a43d6-171">Per altre informazioni, vedere <xref:security/preventing-open-redirects>.</span><span class="sxs-lookup"><span data-stu-id="a43d6-171">For more information, see <xref:security/preventing-open-redirects>.</span></span>

<span data-ttu-id="a43d6-172">Il componente seguente mostra un esempio di come eseguire il reindirizzamento iniziale quando l'utente seleziona le impostazioni cultura:</span><span class="sxs-lookup"><span data-stu-id="a43d6-172">The following component shows an example of how to perform the initial redirection when the user selects a culture:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="a43d6-173">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="a43d6-173">Additional resources</span></span>

* <xref:fundamentals/localization>
