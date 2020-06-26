---
title: Impedisci attacchi di reindirizzamento aperti in ASP.NET Core
author: ardalis
description: Mostra come impedire gli attacchi di reindirizzamento aperti contro un'app ASP.NET Core
ms.author: riande
ms.date: 07/07/2017
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/preventing-open-redirects
ms.openlocfilehash: eb18c599d84fd08ffe97867b67a837303af188db
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/26/2020
ms.locfileid: "85408149"
---
# <a name="prevent-open-redirect-attacks-in-aspnet-core"></a><span data-ttu-id="9c83f-103">Impedisci attacchi di reindirizzamento aperti in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9c83f-103">Prevent open redirect attacks in ASP.NET Core</span></span>

<span data-ttu-id="9c83f-104">Un'app Web che reindirizza a un URL specificato tramite la richiesta, ad esempio la QueryString o i dati del modulo, può essere potenzialmente manomessa per reindirizzare gli utenti a un URL esterno e dannoso.</span><span class="sxs-lookup"><span data-stu-id="9c83f-104">A web app that redirects to a URL that's specified via the request such as the querystring or form data can potentially be tampered with to redirect users to an external, malicious URL.</span></span> <span data-ttu-id="9c83f-105">Questa manomissione è detta attacco di reindirizzamento aperto.</span><span class="sxs-lookup"><span data-stu-id="9c83f-105">This tampering is called an open redirection attack.</span></span>

<span data-ttu-id="9c83f-106">Ogni volta che la logica dell'applicazione reindirizza a un URL specificato, è necessario verificare che l'URL di reindirizzamento non sia stato alterato.</span><span class="sxs-lookup"><span data-stu-id="9c83f-106">Whenever your application logic redirects to a specified URL, you must verify that the redirection URL hasn't been tampered with.</span></span> <span data-ttu-id="9c83f-107">ASP.NET Core dispone di funzionalità predefinite che consentono di proteggere le app da attacchi di reindirizzamento aperti (noti anche come reindirizzamento aperti).</span><span class="sxs-lookup"><span data-stu-id="9c83f-107">ASP.NET Core has built-in functionality to help protect apps from open redirect (also known as open redirection) attacks.</span></span>

## <a name="what-is-an-open-redirect-attack"></a><span data-ttu-id="9c83f-108">Che cos'è un attacco di reindirizzamento aperto?</span><span class="sxs-lookup"><span data-stu-id="9c83f-108">What is an open redirect attack?</span></span>

<span data-ttu-id="9c83f-109">Le applicazioni Web indirizzano spesso gli utenti a una pagina di accesso quando accedono alle risorse che richiedono l'autenticazione.</span><span class="sxs-lookup"><span data-stu-id="9c83f-109">Web applications frequently redirect users to a login page when they access resources that require authentication.</span></span> <span data-ttu-id="9c83f-110">Il reindirizzamento include in genere un `returnUrl` parametro QueryString, in modo che l'utente possa essere restituito all'URL richiesto in origine dopo che l'accesso è stato eseguito correttamente.</span><span class="sxs-lookup"><span data-stu-id="9c83f-110">The redirection typically includes a `returnUrl` querystring parameter so that the user can be returned to the originally requested URL after they have successfully logged in.</span></span> <span data-ttu-id="9c83f-111">Dopo l'autenticazione, l'utente viene reindirizzato all'URL richiesto originariamente.</span><span class="sxs-lookup"><span data-stu-id="9c83f-111">After the user authenticates, they're redirected to the URL they had originally requested.</span></span>

<span data-ttu-id="9c83f-112">Poiché l'URL di destinazione è specificato nella stringa QueryString della richiesta, un utente malintenzionato potrebbe manomettere la QueryString.</span><span class="sxs-lookup"><span data-stu-id="9c83f-112">Because the destination URL is specified in the querystring of the request, a malicious user could tamper with the querystring.</span></span> <span data-ttu-id="9c83f-113">Una QueryString manomessa potrebbe consentire al sito di reindirizzare l'utente a un sito esterno e dannoso.</span><span class="sxs-lookup"><span data-stu-id="9c83f-113">A tampered querystring could allow the site to redirect the user to an external, malicious site.</span></span> <span data-ttu-id="9c83f-114">Questa tecnica è detta attacco di reindirizzamento (o reindirizzamento) aperto.</span><span class="sxs-lookup"><span data-stu-id="9c83f-114">This technique is called an open redirect (or redirection) attack.</span></span>

### <a name="an-example-attack"></a><span data-ttu-id="9c83f-115">Un attacco di esempio</span><span class="sxs-lookup"><span data-stu-id="9c83f-115">An example attack</span></span>

<span data-ttu-id="9c83f-116">Un utente malintenzionato può sviluppare un attacco progettato per consentire all'utente malintenzionato di accedere alle credenziali di un utente o alle informazioni riservate.</span><span class="sxs-lookup"><span data-stu-id="9c83f-116">A malicious user can develop an attack intended to allow the malicious user access to a user's credentials or sensitive information.</span></span> <span data-ttu-id="9c83f-117">Per iniziare l'attacco, l'utente malintenzionato convince l'utente a fare clic su un collegamento alla pagina di accesso del sito con un `returnUrl` valore QueryString aggiunto all'URL.</span><span class="sxs-lookup"><span data-stu-id="9c83f-117">To begin the attack, the malicious user convinces the user to click a link to your site's login page with a `returnUrl` querystring value added to the URL.</span></span> <span data-ttu-id="9c83f-118">Si consideri ad esempio un'app in `contoso.com` che include una pagina di accesso all'indirizzo `http://contoso.com/Account/LogOn?returnUrl=/Home/About` .</span><span class="sxs-lookup"><span data-stu-id="9c83f-118">For example, consider an app at `contoso.com` that includes a login page at `http://contoso.com/Account/LogOn?returnUrl=/Home/About`.</span></span> <span data-ttu-id="9c83f-119">L'attacco segue questa procedura:</span><span class="sxs-lookup"><span data-stu-id="9c83f-119">The attack follows these steps:</span></span>

1. <span data-ttu-id="9c83f-120">L'utente fa clic su un collegamento dannoso `http://contoso.com/Account/LogOn?returnUrl=http://contoso1.com/Account/LogOn` (il secondo URL è "Contoso**1**. com", non "contoso.com").</span><span class="sxs-lookup"><span data-stu-id="9c83f-120">The user clicks a malicious link to `http://contoso.com/Account/LogOn?returnUrl=http://contoso1.com/Account/LogOn` (the second URL is "contoso**1**.com", not "contoso.com").</span></span>
2. <span data-ttu-id="9c83f-121">L'utente ha eseguito l'accesso.</span><span class="sxs-lookup"><span data-stu-id="9c83f-121">The user logs in successfully.</span></span>
3. <span data-ttu-id="9c83f-122">L'utente viene reindirizzato (dal sito) a `http://contoso1.com/Account/LogOn` (un sito dannoso simile al sito reale).</span><span class="sxs-lookup"><span data-stu-id="9c83f-122">The user is redirected (by the site) to `http://contoso1.com/Account/LogOn` (a malicious site that looks exactly like real site).</span></span>
4. <span data-ttu-id="9c83f-123">L'utente accede nuovamente (assegnando loro le credenziali al sito dannoso) e viene reindirizzato di nuovo al sito reale.</span><span class="sxs-lookup"><span data-stu-id="9c83f-123">The user logs in again (giving malicious site their credentials) and is redirected back to the real site.</span></span>

<span data-ttu-id="9c83f-124">È probabile che l'utente ritenga che il primo tentativo di accesso non sia riuscito e che il secondo tentativo abbia avuto esito positivo.</span><span class="sxs-lookup"><span data-stu-id="9c83f-124">The user likely believes that their first attempt to log in failed and that their second attempt is successful.</span></span> <span data-ttu-id="9c83f-125">L'utente probabilmente rimane inconsapevole del fatto che le relative credenziali sono compromesse.</span><span class="sxs-lookup"><span data-stu-id="9c83f-125">The user most likely remains unaware that their credentials are compromised.</span></span>

![Processo di attacco di reindirizzamento aperto](preventing-open-redirects/_static/open-redirection-attack-process.png)

<span data-ttu-id="9c83f-127">Oltre alle pagine di accesso, alcuni siti forniscono pagine di reindirizzamento o endpoint.</span><span class="sxs-lookup"><span data-stu-id="9c83f-127">In addition to login pages, some sites provide redirect pages or endpoints.</span></span> <span data-ttu-id="9c83f-128">Si supponga che l'app abbia una pagina con un reindirizzamento aperto, `/Home/Redirect` .</span><span class="sxs-lookup"><span data-stu-id="9c83f-128">Imagine your app has a page with an open redirect, `/Home/Redirect`.</span></span> <span data-ttu-id="9c83f-129">Un utente malintenzionato potrebbe creare, ad esempio, un collegamento in un messaggio di posta elettronica che passa a `[yoursite]/Home/Redirect?url=http://phishingsite.com/Home/Login` .</span><span class="sxs-lookup"><span data-stu-id="9c83f-129">An attacker could create, for example, a link in an email that goes to `[yoursite]/Home/Redirect?url=http://phishingsite.com/Home/Login`.</span></span> <span data-ttu-id="9c83f-130">Un utente tipico osserverà l'URL e vedrà che inizia con il nome del sito.</span><span class="sxs-lookup"><span data-stu-id="9c83f-130">A typical user will look at the URL and see it begins with your site name.</span></span> <span data-ttu-id="9c83f-131">Considerato attendibile, i colleghi faranno clic sul collegamento.</span><span class="sxs-lookup"><span data-stu-id="9c83f-131">Trusting that, they will click the link.</span></span> <span data-ttu-id="9c83f-132">Il reindirizzamento aperto invierà quindi l'utente al sito di phishing, che sembra identico a quello dell'utente e l'utente potrebbe accedere a quello che ritiene sia il sito.</span><span class="sxs-lookup"><span data-stu-id="9c83f-132">The open redirect would then send the user to the phishing site, which looks identical to yours, and the user would likely login to what they believe is your site.</span></span>

## <a name="protecting-against-open-redirect-attacks"></a><span data-ttu-id="9c83f-133">Protezione da attacchi di reindirizzamento aperti</span><span class="sxs-lookup"><span data-stu-id="9c83f-133">Protecting against open redirect attacks</span></span>

<span data-ttu-id="9c83f-134">Quando si sviluppano applicazioni Web, considerare tutti i dati forniti dall'utente come non attendibili.</span><span class="sxs-lookup"><span data-stu-id="9c83f-134">When developing web applications, treat all user-provided data as untrustworthy.</span></span> <span data-ttu-id="9c83f-135">Se l'applicazione dispone di funzionalità che reindirizza l'utente in base al contenuto dell'URL, assicurarsi che i reindirizzamenti vengano eseguiti solo localmente all'interno dell'app (o a un URL noto, non a qualsiasi URL che può essere fornito nella querystring).</span><span class="sxs-lookup"><span data-stu-id="9c83f-135">If your application has functionality that redirects the user based on the contents of the URL,  ensure that such redirects are only done locally within your app (or to a known URL, not any URL that may be supplied in the querystring).</span></span>

### <a name="localredirect"></a><span data-ttu-id="9c83f-136">LocalRedirect</span><span class="sxs-lookup"><span data-stu-id="9c83f-136">LocalRedirect</span></span>

<span data-ttu-id="9c83f-137">Usare il `LocalRedirect` metodo helper della classe di base `Controller` :</span><span class="sxs-lookup"><span data-stu-id="9c83f-137">Use the `LocalRedirect` helper method from the base `Controller` class:</span></span>

```csharp
public IActionResult SomeAction(string redirectUrl)
{
    return LocalRedirect(redirectUrl);
}
```

<span data-ttu-id="9c83f-138">`LocalRedirect`genererà un'eccezione se viene specificato un URL non locale.</span><span class="sxs-lookup"><span data-stu-id="9c83f-138">`LocalRedirect` will throw an exception if a non-local URL is specified.</span></span> <span data-ttu-id="9c83f-139">In caso contrario, si comporta esattamente come il `Redirect` metodo.</span><span class="sxs-lookup"><span data-stu-id="9c83f-139">Otherwise, it behaves just like the `Redirect` method.</span></span>

### <a name="islocalurl"></a><span data-ttu-id="9c83f-140">IsLocalUrl</span><span class="sxs-lookup"><span data-stu-id="9c83f-140">IsLocalUrl</span></span>

<span data-ttu-id="9c83f-141">Usare il metodo [IsLocalUrl](/dotnet/api/Microsoft.AspNetCore.Mvc.IUrlHelper.islocalurl#Microsoft_AspNetCore_Mvc_IUrlHelper_IsLocalUrl_System_String_) per verificare gli URL prima del reindirizzamento:</span><span class="sxs-lookup"><span data-stu-id="9c83f-141">Use the [IsLocalUrl](/dotnet/api/Microsoft.AspNetCore.Mvc.IUrlHelper.islocalurl#Microsoft_AspNetCore_Mvc_IUrlHelper_IsLocalUrl_System_String_) method to test URLs before redirecting:</span></span>

<span data-ttu-id="9c83f-142">Nell'esempio seguente viene illustrato come controllare se un URL è locale prima del reindirizzamento.</span><span class="sxs-lookup"><span data-stu-id="9c83f-142">The following example shows how to check whether a URL is local before redirecting.</span></span>

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

<span data-ttu-id="9c83f-143">Il `IsLocalUrl` Metodo protegge gli utenti dal reindirizzamento inavvertitamente a un sito dannoso.</span><span class="sxs-lookup"><span data-stu-id="9c83f-143">The `IsLocalUrl` method protects users from being inadvertently redirected to a malicious site.</span></span> <span data-ttu-id="9c83f-144">È possibile registrare i dettagli dell'URL fornito quando viene specificato un URL non locale in una situazione in cui è previsto un URL locale.</span><span class="sxs-lookup"><span data-stu-id="9c83f-144">You can log the details of the URL that was provided when a non-local URL is supplied in a situation where you expected a local URL.</span></span> <span data-ttu-id="9c83f-145">Gli URL di reindirizzamento della registrazione possono essere utili per la diagnosi degli attacchi di reindirizzamento.</span><span class="sxs-lookup"><span data-stu-id="9c83f-145">Logging redirect URLs may help in diagnosing redirection attacks.</span></span>
