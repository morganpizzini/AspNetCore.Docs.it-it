---
title: Stringhe di scopo in ASP.NET Core
author: rick-anderson
description: Informazioni sul modo in cui vengono usate le stringhe di scopo in ASP.NET Core le API di protezione dei dati.
ms.author: riande
ms.date: 10/14/2016
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
uid: security/data-protection/consumer-apis/purpose-strings
ms.openlocfilehash: 9a55131714b23909da5d00b73607078b295a1c4d
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060807"
---
# <a name="purpose-strings-in-aspnet-core"></a><span data-ttu-id="e2942-103">Stringhe di scopo in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e2942-103">Purpose strings in ASP.NET Core</span></span>

<a name="data-protection-consumer-apis-purposes"></a>

<span data-ttu-id="e2942-104">I componenti che utilizzano `IDataProtectionProvider` devono passare un parametro per *finalità* univoche al `CreateProtector` metodo.</span><span class="sxs-lookup"><span data-stu-id="e2942-104">Components which consume `IDataProtectionProvider` must pass a unique *purposes* parameter to the `CreateProtector` method.</span></span> <span data-ttu-id="e2942-105">Il *parametro* degli scopi è intrinseco alla sicurezza del sistema di protezione dei dati, in quanto fornisce l'isolamento tra i consumer di crittografia, anche se le chiavi di crittografia radice sono le stesse.</span><span class="sxs-lookup"><span data-stu-id="e2942-105">The purposes *parameter* is inherent to the security of the data protection system, as it provides isolation between cryptographic consumers, even if the root cryptographic keys are the same.</span></span>

<span data-ttu-id="e2942-106">Quando un consumer specifica uno scopo, la stringa per finalità viene utilizzata insieme alle chiavi di crittografia radice per derivare le sottochiavi crittografiche univoche per tale consumer.</span><span class="sxs-lookup"><span data-stu-id="e2942-106">When a consumer specifies a purpose, the purpose string is used along with the root cryptographic keys to derive cryptographic subkeys unique to that consumer.</span></span> <span data-ttu-id="e2942-107">Questo consente di isolare il consumer da tutti gli altri consumer crittografici nell'applicazione: nessun altro componente può leggere i payload e non è in grado di leggere i payload di altri componenti.</span><span class="sxs-lookup"><span data-stu-id="e2942-107">This isolates the consumer from all other cryptographic consumers in the application: no other component can read its payloads, and it cannot read any other component's payloads.</span></span> <span data-ttu-id="e2942-108">Questo isolamento esegue anche il rendering di intere categorie non realizzabili di attacchi contro il componente.</span><span class="sxs-lookup"><span data-stu-id="e2942-108">This isolation also renders infeasible entire categories of attack against the component.</span></span>

![Esempio di diagramma scopo](purpose-strings/_static/purposes.png)

<span data-ttu-id="e2942-110">Nel diagramma precedente, le `IDataProtector` istanze a e B **non** sono in grado di leggere i payload degli altri, ma solo dei relativi.</span><span class="sxs-lookup"><span data-stu-id="e2942-110">In the diagram above, `IDataProtector` instances A and B **cannot** read each other's payloads, only their own.</span></span>

<span data-ttu-id="e2942-111">Non è necessario che la stringa di scopo sia segreta.</span><span class="sxs-lookup"><span data-stu-id="e2942-111">The purpose string doesn't have to be secret.</span></span> <span data-ttu-id="e2942-112">Dovrebbe essere semplicemente univoco nel senso che nessun altro componente ben funzionante fornirà la stessa stringa di scopo.</span><span class="sxs-lookup"><span data-stu-id="e2942-112">It should simply be unique in the sense that no other well-behaved component will ever provide the same purpose string.</span></span>

>[!TIP]
> <span data-ttu-id="e2942-113">L'uso dello spazio dei nomi e del nome del tipo del componente che utilizza le API di protezione dati è una regola empirica, come in pratica queste informazioni non sono in conflitto.</span><span class="sxs-lookup"><span data-stu-id="e2942-113">Using the namespace and type name of the component consuming the data protection APIs is a good rule of thumb, as in practice this information will never conflict.</span></span>
>
><span data-ttu-id="e2942-114">Un componente creato da Contoso che è responsabile per la creazione di token di portanti può utilizzare contoso. Security. BearerToken come stringa di scopo.</span><span class="sxs-lookup"><span data-stu-id="e2942-114">A Contoso-authored component which is responsible for minting bearer tokens might use Contoso.Security.BearerToken as its purpose string.</span></span> <span data-ttu-id="e2942-115">O, anche meglio, potrebbe usare contoso. Security. BearerToken. v1 come stringa di scopo.</span><span class="sxs-lookup"><span data-stu-id="e2942-115">Or - even better - it might use Contoso.Security.BearerToken.v1 as its purpose string.</span></span> <span data-ttu-id="e2942-116">L'aggiunta del numero di versione consente a una versione futura di usare contoso. Security. BearerToken. v2 come scopo e le diverse versioni sarebbero completamente isolate l'una dall'altra per quanto riguarda i payload.</span><span class="sxs-lookup"><span data-stu-id="e2942-116">Appending the version number allows a future version to use Contoso.Security.BearerToken.v2 as its purpose, and the different versions would be completely isolated from one another as far as payloads go.</span></span>

<span data-ttu-id="e2942-117">Dato che il parametro purposes a `CreateProtector` è una matrice di stringhe, il valore precedente potrebbe essere stato invece specificato come `[ "Contoso.Security.BearerToken", "v1" ]` .</span><span class="sxs-lookup"><span data-stu-id="e2942-117">Since the purposes parameter to `CreateProtector` is a string array, the above could've been instead specified as `[ "Contoso.Security.BearerToken", "v1" ]`.</span></span> <span data-ttu-id="e2942-118">Questo consente di stabilire una gerarchia di scopi e apre la possibilità di scenari di multi-tenant con il sistema di protezione dei dati.</span><span class="sxs-lookup"><span data-stu-id="e2942-118">This allows establishing a hierarchy of purposes and opens up the possibility of multi-tenancy scenarios with the data protection system.</span></span>

<a name="data-protection-contoso-purpose"></a>

>[!WARNING]
> <span data-ttu-id="e2942-119">I componenti non devono consentire l'input dell'utente non attendibile come unica fonte di input per la catena di scopi.</span><span class="sxs-lookup"><span data-stu-id="e2942-119">Components shouldn't allow untrusted user input to be the sole source of input for the purposes chain.</span></span>
>
><span data-ttu-id="e2942-120">Si consideri, ad esempio, un componente contoso. Messaging. SecureMessage responsabile dell'archiviazione dei messaggi protetti.</span><span class="sxs-lookup"><span data-stu-id="e2942-120">For example, consider a component Contoso.Messaging.SecureMessage which is responsible for storing secure messages.</span></span> <span data-ttu-id="e2942-121">Se il componente di messaggistica protetta venisse chiamato `CreateProtector([ username ])` , un utente malintenzionato potrebbe creare un account con il nome utente "contoso. Security. BearerToken" nel tentativo di ottenere il componente da chiamare `CreateProtector([ "Contoso.Security.BearerToken" ])` , causando inavvertitamente il sistema di messaggistica sicura per la mentazione dei payload che potrebbero essere percepiti come token di autenticazione.</span><span class="sxs-lookup"><span data-stu-id="e2942-121">If the secure messaging component were to call `CreateProtector([ username ])`, then a malicious user might create an account with username "Contoso.Security.BearerToken" in an attempt to get the component to call `CreateProtector([ "Contoso.Security.BearerToken" ])`, thus inadvertently causing the secure messaging system to mint payloads that could be perceived as authentication tokens.</span></span>
>
><span data-ttu-id="e2942-122">Una catena di scopi migliore per il componente di messaggistica sarebbe `CreateProtector([ "Contoso.Messaging.SecureMessage", "User: username" ])` , che fornisce un isolamento appropriato.</span><span class="sxs-lookup"><span data-stu-id="e2942-122">A better purposes chain for the messaging component would be `CreateProtector([ "Contoso.Messaging.SecureMessage", "User: username" ])`, which provides proper isolation.</span></span>

<span data-ttu-id="e2942-123">L'isolamento fornito da e i comportamenti degli `IDataProtectionProvider` `IDataProtector` scopi, e sono i seguenti:</span><span class="sxs-lookup"><span data-stu-id="e2942-123">The isolation provided by and behaviors of `IDataProtectionProvider`, `IDataProtector`, and purposes are as follows:</span></span>

* <span data-ttu-id="e2942-124">Per un determinato `IDataProtectionProvider` oggetto, il `CreateProtector` metodo creerà un `IDataProtector` oggetto in modo univoco associato all' `IDataProtectionProvider` oggetto che lo ha creato e al parametro purposes passato nel metodo.</span><span class="sxs-lookup"><span data-stu-id="e2942-124">For a given `IDataProtectionProvider` object, the `CreateProtector` method will create an `IDataProtector` object uniquely tied to both the `IDataProtectionProvider` object which created it and the purposes parameter which was passed into the method.</span></span>

* <span data-ttu-id="e2942-125">Il parametro purpose non può essere null.</span><span class="sxs-lookup"><span data-stu-id="e2942-125">The purpose parameter must not be null.</span></span> <span data-ttu-id="e2942-126">Se gli scopi sono specificati come matrice, significa che la matrice non deve essere di lunghezza zero e che tutti gli elementi della matrice non devono essere null. Uno scopo di stringa vuoto è tecnicamente consentito, ma è sconsigliato.</span><span class="sxs-lookup"><span data-stu-id="e2942-126">(If purposes is specified as an array, this means that the array must not be of zero length and all elements of the array must be non-null.) An empty string purpose is technically allowed but is discouraged.</span></span>

* <span data-ttu-id="e2942-127">Due argomenti per finalità sono equivalenti se e solo se contengono le stesse stringhe (usando un operatore di confronto ordinale) nello stesso ordine.</span><span class="sxs-lookup"><span data-stu-id="e2942-127">Two purposes arguments are equivalent if and only if they contain the same strings (using an ordinal comparer) in the same order.</span></span> <span data-ttu-id="e2942-128">Un argomento a scopo singolo equivale alla matrice a scopo di singolo elemento corrispondente.</span><span class="sxs-lookup"><span data-stu-id="e2942-128">A single purpose argument is equivalent to the corresponding single-element purposes array.</span></span>

* <span data-ttu-id="e2942-129">Due `IDataProtector` oggetti sono equivalenti se e solo se vengono creati da oggetti equivalenti `IDataProtectionProvider` con parametri di scopi equivalenti.</span><span class="sxs-lookup"><span data-stu-id="e2942-129">Two `IDataProtector` objects are equivalent if and only if they're created from equivalent `IDataProtectionProvider` objects with equivalent purposes parameters.</span></span>

* <span data-ttu-id="e2942-130">Per un determinato `IDataProtector` oggetto, una chiamata a restituirà `Unprotect(protectedData)` l'originale `unprotectedData` se e solo se `protectedData := Protect(unprotectedData)` per un `IDataProtector` oggetto equivalente.</span><span class="sxs-lookup"><span data-stu-id="e2942-130">For a given `IDataProtector` object, a call to `Unprotect(protectedData)` will return the original `unprotectedData` if and only if `protectedData := Protect(unprotectedData)` for an equivalent `IDataProtector` object.</span></span>

> [!NOTE]
> <span data-ttu-id="e2942-131">Il caso in cui un componente sceglie intenzionalmente una stringa di scopo nota come conflitto con un altro componente non viene considerato.</span><span class="sxs-lookup"><span data-stu-id="e2942-131">We're not considering the case where some component intentionally chooses a purpose string which is known to conflict with another component.</span></span> <span data-ttu-id="e2942-132">Questo componente è essenzialmente considerato dannoso e questo sistema non è progettato per fornire garanzie di sicurezza nel caso in cui il codice dannoso sia già in esecuzione all'interno del processo di lavoro.</span><span class="sxs-lookup"><span data-stu-id="e2942-132">Such a component would essentially be considered malicious, and this system isn't intended to provide security guarantees in the event that malicious code is already running inside of the worker process.</span></span>
