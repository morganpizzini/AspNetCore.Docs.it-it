---
title: Globalizzazione e localizzazione ASP.NET Core Blazor
author: guardrex
description: Informazioni su come rendere Razor i componenti accessibili agli utenti in più impostazioni cultura e lingue.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/14/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/globalization-localization
ms.openlocfilehash: b39695f8b506744b4af27a1d7e09bfac9594d7ca
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82772491"
---
# <a name="aspnet-core-blazor-globalization-and-localization"></a>Globalizzazione e localizzazione ASP.NET Core Blazor

Di [Luke Latham](https://github.com/guardrex) e [Daniel Roth](https://github.com/danroth27)

Razori componenti possono essere resi accessibili agli utenti in più impostazioni cultura e lingue. Sono disponibili i seguenti scenari di globalizzazione e localizzazione di .NET:

* . Sistema di risorse di NET
* Formattazione di numeri e date specifiche delle impostazioni cultura

Sono attualmente supportati un set limitato di scenari di localizzazione di ASP.NET Core:

* `IStringLocalizer<>`*è supportato* nelle Blazor app.
* `IHtmlLocalizer<>`la `IViewLocalizer<>`localizzazione delle annotazioni dei dati, e è ASP.NET Core scenari MVC e Blazor non è **supportata** nelle app.

Per altre informazioni, vedere <xref:fundamentals/localization>.

## <a name="globalization"></a>Globalizzazione

Blazorla `@bind` funzionalità di esegue formati e analizza i valori per la visualizzazione in base alle impostazioni cultura correnti dell'utente.

È possibile accedere alle impostazioni cultura correnti dalla <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName> proprietà.

[CultureInfo. InvariantCulture](xref:System.Globalization.CultureInfo.InvariantCulture) viene usato per i tipi di campo seguenti`<input type="{TYPE}" />`():

* `date`
* `number`

I tipi di campo precedenti:

* Vengono visualizzati utilizzando le regole di formattazione appropriate basate su browser.
* Non può contenere testo in formato libero.
* Fornire le caratteristiche di interazione utente in base all'implementazione del browser.

I tipi di campo seguenti hanno requisiti di formattazione specifici e non sono Blazor attualmente supportati da perché non sono supportati da tutti i browser principali:

* `datetime-local`
* `month`
* `week`

`@bind`supporta il `@bind:culture` parametro per fornire un <xref:System.Globalization.CultureInfo?displayProperty=fullName> oggetto per l'analisi e la formattazione di un valore. Non è consigliabile specificare impostazioni cultura quando si `date` usano `number` i tipi di campo e. `date`e `number` dispongono di Blazor supporto incorporato che fornisce le impostazioni cultura richieste.

## <a name="localization"></a>Localizzazione

### <a name="blazor-webassembly"></a>BlazorWebassembly

BlazorLe app webassembly impostano le impostazioni cultura usando le [Preferenze della lingua](https://developer.mozilla.org/docs/Web/API/NavigatorLanguage/languages)dell'utente.

Per configurare in modo esplicito le impostazioni `CultureInfo.DefaultThreadCurrentCulture` cultura `CultureInfo.DefaultThreadCurrentUICulture` , `Program.Main`impostare e in.

Per impostazione predefinita Blazor, la configurazione del linker per Blazor le app webassembly rimuove le informazioni di internazionalizzazione ad eccezione delle impostazioni locali richieste in modo esplicito. Per ulteriori informazioni e istruzioni sul controllo del comportamento del linker, vedere <xref:host-and-deploy/blazor/configure-linker#configure-the-linker-for-internationalization>.

Mentre le impostazioni cultura Blazor che selezionano per impostazione predefinita potrebbero essere sufficienti per la maggior parte degli utenti, è consigliabile offrire agli utenti un modo per specificare le impostazioni locali preferite. Per un' Blazor app di esempio webassembly con selezione impostazioni cultura, vedere l'app di esempio [LocSample](https://github.com/pranavkm/LocSample) Localization.

### <a name="blazor-server"></a>BlazorServer

BlazorLe app Server sono localizzate usando il [middleware di localizzazione](xref:fundamentals/localization#localization-middleware). Il middleware seleziona le impostazioni cultura appropriate per gli utenti che richiedono risorse dall'app.

Le impostazioni cultura possono essere impostate utilizzando uno degli approcci seguenti:

* [Cookie](#cookies)
* [Fornire l'interfaccia utente per scegliere le impostazioni cultura](#provide-ui-to-choose-the-culture)

Per altre informazioni ed esempi, vedere <xref:fundamentals/localization>.

#### <a name="cookies"></a>Cookie

Un cookie di impostazioni cultura di localizzazione può salvare in maniera permanente le impostazioni cultura dell'utente. Il cookie viene creato tramite il `OnGet` metodo della pagina host dell'app (*pages/host. cshtml. cs*). Il middleware di localizzazione legge il cookie nelle richieste successive per impostare le impostazioni cultura dell'utente. 

L'uso di un cookie garantisce che la connessione WebSocket possa propagare correttamente le impostazioni cultura. Se gli schemi di localizzazione sono basati sul percorso dell'URL o sulla stringa di query, lo schema potrebbe non essere in grado di funzionare con WebSocket, quindi non è possibile salvare in modo permanente le impostazioni cultura. Pertanto, l'utilizzo di un cookie di impostazioni cultura di localizzazione è l'approccio consigliato.

Qualsiasi tecnica può essere utilizzata per assegnare impostazioni cultura se le impostazioni cultura vengono rese permanente in un cookie di localizzazione. Se l'app dispone già di uno schema di localizzazione stabilito per ASP.NET Core lato server, continuare a usare l'infrastruttura di localizzazione esistente dell'app e impostare il cookie delle impostazioni cultura di localizzazione nello schema dell'app.

Nell'esempio seguente viene illustrato come impostare le impostazioni cultura correnti in un cookie che può essere letto dal middleware di localizzazione. Creare un file *pages/_Host. cshtml. cs* con il contenuto seguente nell' Blazor app Server:

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

La localizzazione viene gestita dall'app nella sequenza di eventi seguente:

1. Il browser invia una richiesta HTTP iniziale all'app.
1. Le impostazioni cultura vengono assegnate dal middleware di localizzazione.
1. Il `OnGet` metodo in *_Host. cshtml. cs* rende permanente le impostazioni cultura di un cookie come parte della risposta.
1. Il browser apre una connessione WebSocket per creare una sessione Blazor del server interattiva.
1. Il middleware di localizzazione legge il cookie e assegna le impostazioni cultura.
1. La Blazor sessione del server inizia con le impostazioni cultura corrette.

#### <a name="provide-ui-to-choose-the-culture"></a>Fornire l'interfaccia utente per scegliere le impostazioni cultura

Per consentire a un utente di selezionare le impostazioni cultura, è consigliabile un *approccio basato su Reindirizzamento* . Il processo è simile a quello che accade in un'app Web quando un utente tenta di accedere a una risorsa protetta. L'utente viene reindirizzato a una pagina di accesso, quindi viene reindirizzato di nuovo alla risorsa originale. 

L'app rende permanente le impostazioni cultura selezionate dall'utente tramite un reindirizzamento a un controller. Il controller imposta le impostazioni cultura selezionate dall'utente in un cookie e reindirizza di nuovo l'utente all'URI originale.

Stabilire un endpoint HTTP nel server per impostare le impostazioni cultura selezionate dall'utente in un cookie ed eseguire di nuovo il reindirizzamento all'URI originale:

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
> Usare il `LocalRedirect` risultato dell'azione per impedire gli attacchi di reindirizzamento aperti. Per altre informazioni, vedere <xref:security/preventing-open-redirects>.

Il componente seguente mostra un esempio di come eseguire il reindirizzamento iniziale quando l'utente seleziona le impostazioni cultura:

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

## <a name="additional-resources"></a>Risorse aggiuntive

* <xref:fundamentals/localization>
