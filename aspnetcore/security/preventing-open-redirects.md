---
title: Impedisci attacchi di reindirizzamento aperti in ASP.NET Core
author: ardalis
description: Mostra come impedire gli attacchi di reindirizzamento aperti contro un'app ASP.NET Core
ms.author: riande
ms.date: 07/07/2017
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/preventing-open-redirects
ms.openlocfilehash: ad4c9806146567b6ef1f5e78eaeca96cb649c1af
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774392"
---
# <a name="prevent-open-redirect-attacks-in-aspnet-core"></a>Impedisci attacchi di reindirizzamento aperti in ASP.NET Core

Un'app Web che reindirizza a un URL specificato tramite la richiesta, ad esempio la QueryString o i dati del modulo, può essere potenzialmente manomessa per reindirizzare gli utenti a un URL esterno e dannoso. Questa manomissione è detta attacco di reindirizzamento aperto.

Ogni volta che la logica dell'applicazione reindirizza a un URL specificato, è necessario verificare che l'URL di reindirizzamento non sia stato alterato. ASP.NET Core dispone di funzionalità predefinite che consentono di proteggere le app da attacchi di reindirizzamento aperti (noti anche come reindirizzamento aperti).

## <a name="what-is-an-open-redirect-attack"></a>Che cos'è un attacco di reindirizzamento aperto?

Le applicazioni Web indirizzano spesso gli utenti a una pagina di accesso quando accedono alle risorse che richiedono l'autenticazione. Il reindirizzamento include in genere un `returnUrl` parametro QueryString, in modo che l'utente possa essere restituito all'URL richiesto in origine dopo che l'accesso è stato eseguito correttamente. Dopo l'autenticazione, l'utente viene reindirizzato all'URL richiesto originariamente.

Poiché l'URL di destinazione è specificato nella stringa QueryString della richiesta, un utente malintenzionato potrebbe manomettere la QueryString. Una QueryString manomessa potrebbe consentire al sito di reindirizzare l'utente a un sito esterno e dannoso. Questa tecnica è detta attacco di reindirizzamento (o reindirizzamento) aperto.

### <a name="an-example-attack"></a>Un attacco di esempio

Un utente malintenzionato può sviluppare un attacco progettato per consentire all'utente malintenzionato di accedere alle credenziali di un utente o alle informazioni riservate. Per iniziare l'attacco, l'utente malintenzionato convince l'utente a fare clic su un collegamento alla pagina di accesso del `returnUrl` sito con un valore QueryString aggiunto all'URL. Si consideri ad esempio un' `contoso.com` app in che include una pagina `http://contoso.com/Account/LogOn?returnUrl=/Home/About`di accesso all'indirizzo. L'attacco segue questa procedura:

1. L'utente fa clic su `http://contoso.com/Account/LogOn?returnUrl=http://contoso1.com/Account/LogOn` un collegamento dannoso (il secondo URL è "Contoso**1**. com", non "contoso.com").
2. L'utente ha eseguito l'accesso.
3. L'utente viene reindirizzato (dal sito) `http://contoso1.com/Account/LogOn` a (un sito dannoso simile al sito reale).
4. L'utente accede nuovamente (assegnando loro le credenziali al sito dannoso) e viene reindirizzato di nuovo al sito reale.

È probabile che l'utente ritenga che il primo tentativo di accesso non sia riuscito e che il secondo tentativo abbia avuto esito positivo. L'utente probabilmente rimane inconsapevole del fatto che le relative credenziali sono compromesse.

![Processo di attacco di reindirizzamento aperto](preventing-open-redirects/_static/open-redirection-attack-process.png)

Oltre alle pagine di accesso, alcuni siti forniscono pagine di reindirizzamento o endpoint. Si supponga che l'app abbia una pagina con un reindirizzamento `/Home/Redirect`aperto,. Un utente malintenzionato potrebbe creare, ad esempio, un collegamento in un messaggio di `[yoursite]/Home/Redirect?url=http://phishingsite.com/Home/Login`posta elettronica che passa a. Un utente tipico osserverà l'URL e vedrà che inizia con il nome del sito. Considerato attendibile, i colleghi faranno clic sul collegamento. Il reindirizzamento aperto invierà quindi l'utente al sito di phishing, che sembra identico a quello dell'utente e l'utente potrebbe accedere a quello che ritiene sia il sito.

## <a name="protecting-against-open-redirect-attacks"></a>Protezione da attacchi di reindirizzamento aperti

Quando si sviluppano applicazioni Web, considerare tutti i dati forniti dall'utente come non attendibili. Se l'applicazione dispone di funzionalità che reindirizza l'utente in base al contenuto dell'URL, assicurarsi che i reindirizzamenti vengano eseguiti solo localmente all'interno dell'app (o a un URL noto, non a qualsiasi URL che può essere fornito nella querystring).

### <a name="localredirect"></a>LocalRedirect

Usare il `LocalRedirect` metodo helper della classe di base `Controller` :

```csharp
public IActionResult SomeAction(string redirectUrl)
{
    return LocalRedirect(redirectUrl);
}
```

`LocalRedirect`genererà un'eccezione se viene specificato un URL non locale. In caso contrario, si comporta esattamente come il `Redirect` metodo.

### <a name="islocalurl"></a>IsLocalUrl

Usare il metodo [IsLocalUrl](/dotnet/api/Microsoft.AspNetCore.Mvc.IUrlHelper.islocalurl#Microsoft_AspNetCore_Mvc_IUrlHelper_IsLocalUrl_System_String_) per verificare gli URL prima del reindirizzamento:

Nell'esempio seguente viene illustrato come controllare se un URL è locale prima del reindirizzamento.

```csharp
private IActionResult RedirectToLocal(string returnUrl)
{
    if (Url.IsLocalUrl(returnUrl))
    {
        return Redirect(returnUrl);
    }
    else
    {
        return RedirectToAction(nameof(HomeController.Index), "Home");
    }
}
```

Il `IsLocalUrl` metodo protegge gli utenti dal reindirizzamento inavvertitamente a un sito dannoso. È possibile registrare i dettagli dell'URL fornito quando viene specificato un URL non locale in una situazione in cui è previsto un URL locale. Gli URL di reindirizzamento della registrazione possono essere utili per la diagnosi degli attacchi di reindirizzamento.
