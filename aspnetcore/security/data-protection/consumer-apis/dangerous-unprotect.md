---
title: Rimuovere la protezione dai payload le cui chiavi sono state revocate in ASP.NET Core
author: rick-anderson
description: Informazioni su come rimuovere la protezione dei dati, protetti con chiavi da revocare, in un'app ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 10/24/2018
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/data-protection/consumer-apis/dangerous-unprotect
ms.openlocfilehash: 062703fc72ab4e515a99558b3316070ce1f83f79
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776799"
---
# <a name="unprotect-payloads-whose-keys-have-been-revoked-in-aspnet-core"></a><span data-ttu-id="f6fa1-103">Rimuovere la protezione dai payload le cui chiavi sono state revocate in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f6fa1-103">Unprotect payloads whose keys have been revoked in ASP.NET Core</span></span>

<a name="data-protection-consumer-apis-dangerous-unprotect"></a>

<span data-ttu-id="f6fa1-104">Le API di protezione dei dati ASP.NET Core non sono destinate principalmente alla persistenza illimitata dei payload riservati.</span><span class="sxs-lookup"><span data-stu-id="f6fa1-104">The ASP.NET Core data protection APIs are not primarily intended for indefinite persistence of confidential payloads.</span></span> <span data-ttu-id="f6fa1-105">Altre tecnologie come [DPAPI di Windows](https://msdn.microsoft.com/library/windows/desktop/hh706794%28v=vs.85%29.aspx) e [Rights Management di Azure](/rights-management/) sono più adatte allo scenario di archiviazione indefinita e hanno funzionalità di gestione delle chiavi corrispondenti.</span><span class="sxs-lookup"><span data-stu-id="f6fa1-105">Other technologies like [Windows CNG DPAPI](https://msdn.microsoft.com/library/windows/desktop/hh706794%28v=vs.85%29.aspx) and [Azure Rights Management](/rights-management/) are more suited to the scenario of indefinite storage, and they have correspondingly strong key management capabilities.</span></span> <span data-ttu-id="f6fa1-106">Detto questo, non c'è nulla che impedisce agli sviluppatori di usare le API di protezione dei dati ASP.NET Core per la protezione a lungo termine dei dati riservati.</span><span class="sxs-lookup"><span data-stu-id="f6fa1-106">That said, there's nothing prohibiting a developer from using the ASP.NET Core data protection APIs for long-term protection of confidential data.</span></span> <span data-ttu-id="f6fa1-107">Le chiavi non vengono mai rimosse dall'anello chiave `IDataProtector.Unprotect` , quindi è possibile recuperare sempre i payload esistenti purché le chiavi siano disponibili e valide.</span><span class="sxs-lookup"><span data-stu-id="f6fa1-107">Keys are never removed from the key ring, so `IDataProtector.Unprotect` can always recover existing payloads as long as the keys are available and valid.</span></span>

<span data-ttu-id="f6fa1-108">Tuttavia, si verifica un problema quando lo sviluppatore tenta di rimuovere la protezione dei dati protetti con una chiave revocata, in `IDataProtector.Unprotect` quanto genererà un'eccezione in questo caso.</span><span class="sxs-lookup"><span data-stu-id="f6fa1-108">However, an issue arises when the developer tries to unprotect data that has been protected with a revoked key, as `IDataProtector.Unprotect` will throw an exception in this case.</span></span> <span data-ttu-id="f6fa1-109">Questa operazione può essere corretta per i payload temporanei o temporanei, ad esempio i token di autenticazione, in quanto questi tipi di payload possono essere facilmente ricreati dal sistema e, nel peggiore dei casi, potrebbe essere necessario che il visitatore del sito acceda nuovamente.</span><span class="sxs-lookup"><span data-stu-id="f6fa1-109">This might be fine for short-lived or transient payloads (like authentication tokens), as these kinds of payloads can easily be recreated by the system, and at worst the site visitor might be required to log in again.</span></span> <span data-ttu-id="f6fa1-110">Tuttavia, per i payload salvati in modo `Unprotect` permanente, la generazione di throw potrebbe causare una perdita di dati inaccettabile.</span><span class="sxs-lookup"><span data-stu-id="f6fa1-110">But for persisted payloads, having `Unprotect` throw could lead to unacceptable data loss.</span></span>

## <a name="ipersisteddataprotector"></a><span data-ttu-id="f6fa1-111">IPersistedDataProtector</span><span class="sxs-lookup"><span data-stu-id="f6fa1-111">IPersistedDataProtector</span></span>

<span data-ttu-id="f6fa1-112">Per supportare lo scenario di consentire la protezione dei payload anche in presenza di chiavi revocate, il sistema di protezione dei dati contiene un `IPersistedDataProtector` tipo.</span><span class="sxs-lookup"><span data-stu-id="f6fa1-112">To support the scenario of allowing payloads to be unprotected even in the face of revoked keys, the data protection system contains an `IPersistedDataProtector` type.</span></span> <span data-ttu-id="f6fa1-113">Per ottenere un'istanza di `IPersistedDataProtector`, è sufficiente ottenere un'istanza `IDataProtector` di in modo normale e provare a eseguire `IDataProtector` il `IPersistedDataProtector`cast di a.</span><span class="sxs-lookup"><span data-stu-id="f6fa1-113">To get an instance of `IPersistedDataProtector`, simply get an instance of `IDataProtector` in the normal fashion and try casting the `IDataProtector` to `IPersistedDataProtector`.</span></span>

> [!NOTE]
> <span data-ttu-id="f6fa1-114">Non è `IDataProtector` possibile eseguire il cast di `IPersistedDataProtector`tutte le istanze a.</span><span class="sxs-lookup"><span data-stu-id="f6fa1-114">Not all `IDataProtector` instances can be cast to `IPersistedDataProtector`.</span></span> <span data-ttu-id="f6fa1-115">Gli sviluppatori devono usare l'operatore C# As o un tipo simile per evitare eccezioni di runtime causate da cast non validi e devono essere preparati a gestire il caso di errore in modo appropriato.</span><span class="sxs-lookup"><span data-stu-id="f6fa1-115">Developers should use the C# as operator or similar to avoid runtime exceptions caused by invalid casts, and they should be prepared to handle the failure case appropriately.</span></span>

<span data-ttu-id="f6fa1-116">`IPersistedDataProtector`espone la seguente superficie API:</span><span class="sxs-lookup"><span data-stu-id="f6fa1-116">`IPersistedDataProtector` exposes the following API surface:</span></span>

```csharp
DangerousUnprotect(byte[] protectedData, bool ignoreRevocationErrors,
     out bool requiresMigration, out bool wasRevoked) : byte[]
```

<span data-ttu-id="f6fa1-117">Questa API accetta il payload protetto (sotto forma di matrice di byte) e restituisce il payload non protetto.</span><span class="sxs-lookup"><span data-stu-id="f6fa1-117">This API takes the protected payload (as a byte array) and returns the unprotected payload.</span></span> <span data-ttu-id="f6fa1-118">Non è presente alcun overload basato su stringa.</span><span class="sxs-lookup"><span data-stu-id="f6fa1-118">There's no string-based overload.</span></span> <span data-ttu-id="f6fa1-119">I due parametri out sono i seguenti.</span><span class="sxs-lookup"><span data-stu-id="f6fa1-119">The two out parameters are as follows.</span></span>

* <span data-ttu-id="f6fa1-120">`requiresMigration`: verrà impostato su true se la chiave usata per proteggere il payload non è più la chiave predefinita attiva, ad esempio la chiave usata per proteggere questo payload è obsoleta ed è stata eseguita un'operazione di sequenza della chiave.</span><span class="sxs-lookup"><span data-stu-id="f6fa1-120">`requiresMigration`: will be set to true if the key used to protect this payload is no longer the active default key, e.g., the key used to protect this payload is old and a key rolling operation has since taken place.</span></span> <span data-ttu-id="f6fa1-121">Il chiamante potrebbe voler prendere in considerazione la riprotezione del payload a seconda delle esigenze aziendali.</span><span class="sxs-lookup"><span data-stu-id="f6fa1-121">The caller may wish to consider reprotecting the payload depending on their business needs.</span></span>

* <span data-ttu-id="f6fa1-122">`wasRevoked`: verrà impostato su true se la chiave usata per proteggere il payload è stata revocata.</span><span class="sxs-lookup"><span data-stu-id="f6fa1-122">`wasRevoked`: will be set to true if the key used to protect this payload was revoked.</span></span>

>[!WARNING]
> <span data-ttu-id="f6fa1-123">Prestare molta attenzione quando si `ignoreRevocationErrors: true` passa al `DangerousUnprotect` metodo.</span><span class="sxs-lookup"><span data-stu-id="f6fa1-123">Exercise extreme caution when passing `ignoreRevocationErrors: true` to the `DangerousUnprotect` method.</span></span> <span data-ttu-id="f6fa1-124">Se dopo la chiamata a questo `wasRevoked` metodo il valore è true, la chiave usata per proteggere il payload è stata revocata e l'autenticità del payload deve essere considerata sospetta.</span><span class="sxs-lookup"><span data-stu-id="f6fa1-124">If after calling this method the `wasRevoked` value is true, then the key used to protect this payload was revoked, and the payload's authenticity should be treated as suspect.</span></span> <span data-ttu-id="f6fa1-125">In questo caso, è possibile continuare a utilizzare il payload non protetto solo se si dispone di una garanzia separata che è autentica, ad esempio se si tratta di un database protetto anziché inviato da un client Web non attendibile.</span><span class="sxs-lookup"><span data-stu-id="f6fa1-125">In this case, only continue operating on the unprotected payload if you have some separate assurance that it's authentic, e.g. that it's coming from a secure database rather than being sent by an untrusted web client.</span></span>

[!code-csharp[](dangerous-unprotect/samples/dangerous-unprotect.cs)]
