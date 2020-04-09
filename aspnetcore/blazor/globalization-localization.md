---
title: ASP.NET Blazor la globalizzazione e la localizzazione dei ASP.NET core
author: guardrex
description: Informazioni su come rendere i componenti Razor accessibili agli utenti in più lingue e lingue.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/07/2020
no-loc:
- Blazor
- SignalR
uid: blazor/globalization-localization
ms.openlocfilehash: 2795b21f418ccb2722e7fd0e76a77cfde58e524a
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80976742"
---
# <a name="aspnet-core-opno-locblazor-globalization-and-localization"></a>ASP.NET Blazor la globalizzazione e la localizzazione dei ASP.NET core

Di [Luke Latham](https://github.com/guardrex) e [Daniel Roth](https://github.com/danroth27)

I componenti Razor possono essere resi accessibili agli utenti in più lingue e lingue. Sono disponibili i seguenti scenari di globalizzazione e localizzazione .NET:

* . Sistema di risorse di NET
* Formattazione di numeri e date specifica delle impostazioni cultura

È attualmente supportato un set limitato di scenari di localizzazione di ASP.NET Core:A limited set of ASP.NET Core's localization scenarios are currently supported:

* `IStringLocalizer<>`*è supportato* nelle Blazor app.
* `IHtmlLocalizer<>`, `IViewLocalizer<>`e la localizzazione delle annotazioni dei dati Blazor sono ASP.NET scenari MVC di base e non sono **supportati** nelle app.

Per altre informazioni, vedere <xref:fundamentals/localization>.

## <a name="globalization"></a>Globalizzazione

BlazorLa `@bind` funzionalità esegue formati e analizza i valori per la visualizzazione in base alle impostazioni cultura correnti dell'utente.

È possibile accedere alle <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName> impostazioni cultura correnti dalla proprietà .

[CultureInfo.InvariantCulture](xref:System.Globalization.CultureInfo.InvariantCulture) viene utilizzato per i`<input type="{TYPE}" />`seguenti tipi di campo ( ):

* `date`
* `number`

Il campo precedente digita:

* Vengono visualizzati utilizzando le regole di formattazione basate su browser appropriate.
* Non può contenere testo in formato libero.
* Fornire caratteristiche di interazione dell'utente in base all'implementazione del browser.

I seguenti tipi di campo hanno requisiti di Blazor formattazione specifici e non sono attualmente supportati da perché non sono supportati da tutti i principali browser:

* `datetime-local`
* `month`
* `week`

`@bind`supporta il `@bind:culture` parametro <xref:System.Globalization.CultureInfo?displayProperty=fullName> per fornire un per l'analisi e la formattazione di un valore. La specifica di impostazioni cultura non `date` `number` è consigliata quando si utilizzano i tipi di campo e . `date`e `number` dispongono Blazor di un supporto integrato che fornisce le impostazioni cultura necessarie.

## <a name="localization"></a>Localizzazione

### <a name="opno-locblazor-webassembly"></a>BlazorAssembly Web

BlazorLe app WebAssembly impostano le impostazioni cultura utilizzando la [preferenza di lingua](https://developer.mozilla.org/docs/Web/API/NavigatorLanguage/languages)dell'utente.

Per configurare in `CultureInfo.DefaultThreadCurrentCulture` modo `CultureInfo.DefaultThreadCurrentUICulture` `Program.Main`esplicito le impostazioni cultura, impostare e in .

Per impostazione predefinita, Blazor Blazor la configurazione del linker 's per le app WebAssembly elimina le informazioni di internazionalizzazione, ad eccezione delle impostazioni locali richieste in modo esplicito. Per ulteriori informazioni e indicazioni sul controllo <xref:host-and-deploy/blazor/configure-linker#configure-the-linker-for-internationalization>del comportamento del linker, vedere .

Mentre le Blazor impostazioni cultura che selezionano per impostazione predefinita potrebbero essere sufficienti per la maggior parte degli utenti, è consigliabile offrire agli utenti un modo per specificare le impostazioni locali preferite. Per Blazor un'app di esempio WebAssembly con una selezione impostazioni cultura, vedi l'app di esempio di localizzazione [LocSample.For](https://github.com/pranavkm/LocSample) a WebAssembly sample app with a culture picker, see the LocSample localization sample app.

### <a name="opno-locblazor-server"></a>BlazorServer

BlazorLe app server vengono localizzate tramite [il middleware di localizzazione.](xref:fundamentals/localization#localization-middleware) Il middleware seleziona le impostazioni cultura appropriate per gli utenti che richiedono risorse dall'app.

Le impostazioni cultura possono essere impostate utilizzando uno dei seguenti approcci:

* [Cookie](#cookies)
* [Fornire l'interfaccia utente per scegliere le impostazioni culturaProvide UI to choose the culture](#provide-ui-to-choose-the-culture)

Per altre informazioni ed esempi, vedere <xref:fundamentals/localization>.

#### <a name="cookies"></a>Cookie

Un cookie delle impostazioni cultura di localizzazione può rendere persistenti le impostazioni cultura dell'utente. Il cookie viene `OnGet` creato dal metodo della pagina host dell'app (*Pages/Host.cshtml.cs*). Il middleware di localizzazione legge il cookie nelle richieste successive per impostare le impostazioni cultura dell'utente. 

L'uso di un cookie garantisce che la connessione WebSocket possa propagare correttamente le impostazioni cultura. Se gli schemi di localizzazione sono basati sul percorso URL o sulla stringa di query, lo schema potrebbe non essere in grado di funzionare con WebSockets, pertanto non è in grado di mantenere le impostazioni cultura. Pertanto, l'utilizzo di un cookie delle impostazioni cultura di localizzazione è l'approccio consigliato.

Qualsiasi tecnica può essere utilizzata per assegnare impostazioni cultura se le impostazioni cultura vengono mantenute in un cookie di localizzazione. Se l'app ha già uno schema di localizzazione stabilito per ASP.NET Core sul lato server, continua a usare l'infrastruttura di localizzazione esistente dell'app e imposta il cookie delle impostazioni cultura di localizzazione all'interno dello schema dell'app.

Nell'esempio seguente viene illustrato come impostare le impostazioni cultura correnti in un cookie che può essere letto dal Middleware di localizzazione. Creare un file *Pages/_Host.cshtml.cs* con Blazor il contenuto seguente nell'app Server:

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
1. Il `OnGet` metodo in *_Host.cshtml.cs* rende persistenti le impostazioni cultura in un cookie come parte della risposta.
1. Il browser apre una connessione WebSocket per creare una sessione Server interattiva. Blazor
1. Il middleware di localizzazione legge il cookie e assegna le impostazioni cultura.
1. La Blazor sessione del server inizia con le impostazioni cultura corrette.

#### <a name="provide-ui-to-choose-the-culture"></a>Fornire l'interfaccia utente per scegliere le impostazioni culturaProvide UI to choose the culture

Per fornire l'interfaccia utente per consentire a un utente di selezionare impostazioni cultura, è consigliabile un *approccio basato sul reindirizzamento.* Il processo è simile a quello che accade in un'app Web quando un utente tenta di accedere a una risorsa protetta. L'utente viene reindirizzato a una pagina di accesso e quindi reindirizzato alla risorsa originale. 

L'app mantiene le impostazioni cultura selezionate dell'utente tramite un reindirizzamento a un controller. Il controller imposta le impostazioni cultura selezionate dell'utente in un cookie e reindirizza l'utente all'URI originale.

Stabilire un endpoint HTTP nel server per impostare le impostazioni cultura selezionate dell'utente in un cookie ed eseguire il reindirizzamento all'URI originale:

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
> Utilizzare `LocalRedirect` il risultato dell'azione per impedire attacchi di reindirizzamento aperto. Per altre informazioni, vedere <xref:security/preventing-open-redirects>.

Il componente seguente mostra un esempio di come eseguire il reindirizzamento iniziale quando l'utente seleziona le impostazioni cultura:The following component shows an example of how to perform the initial redirection when the user selects a culture:

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
