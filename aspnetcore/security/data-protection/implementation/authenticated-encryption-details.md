---
title: Dettagli della crittografia autenticata in ASP.NET Core
author: rick-anderson
description: Informazioni sui dettagli di implementazione di ASP.NET Core crittografia autenticata per la protezione dei dati.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/data-protection/implementation/authenticated-encryption-details
ms.openlocfilehash: 3066cd505781ed2ddad46626dda9d9ce35307877
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776968"
---
# <a name="authenticated-encryption-details-in-aspnet-core"></a><span data-ttu-id="4050a-103">Dettagli della crittografia autenticata in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4050a-103">Authenticated encryption details in ASP.NET Core</span></span>

<a name="data-protection-implementation-authenticated-encryption-details"></a>

<span data-ttu-id="4050a-104">Le chiamate a IDataProtector. Protect sono operazioni di crittografia autenticate.</span><span class="sxs-lookup"><span data-stu-id="4050a-104">Calls to IDataProtector.Protect are authenticated encryption operations.</span></span> <span data-ttu-id="4050a-105">Il metodo Protect offre la riservatezza e l'autenticità ed è associato alla catena di scopi usata per derivare questa particolare istanza di IDataProtector dalla relativa IDataProtectionProvider radice.</span><span class="sxs-lookup"><span data-stu-id="4050a-105">The Protect method offers both confidentiality and authenticity, and it's tied to the purpose chain that was used to derive this particular IDataProtector instance from its root IDataProtectionProvider.</span></span>

<span data-ttu-id="4050a-106">IDataProtector. Protect accetta un parametro di testo non crittografato byte [] e produce un payload protetto byte [], il cui formato è descritto di seguito.</span><span class="sxs-lookup"><span data-stu-id="4050a-106">IDataProtector.Protect takes a byte[] plaintext parameter and produces a byte[] protected payload, whose format is described below.</span></span> <span data-ttu-id="4050a-107">(Esiste anche un overload del metodo di estensione che accetta un parametro di testo non crittografato della stringa e restituisce un payload protetto da stringa.</span><span class="sxs-lookup"><span data-stu-id="4050a-107">(There's also an extension method overload which takes a string plaintext parameter and returns a string protected payload.</span></span> <span data-ttu-id="4050a-108">Se questa API viene usata, il formato del payload protetto avrà ancora la struttura seguente, ma verrà [codificato base64url](https://tools.ietf.org/html/rfc4648#section-5).</span><span class="sxs-lookup"><span data-stu-id="4050a-108">If this API is used the protected payload format will still have the below structure, but it will be [base64url-encoded](https://tools.ietf.org/html/rfc4648#section-5).)</span></span>

## <a name="protected-payload-format"></a><span data-ttu-id="4050a-109">Formato payload protetto</span><span class="sxs-lookup"><span data-stu-id="4050a-109">Protected payload format</span></span>

<span data-ttu-id="4050a-110">Il formato del payload protetto è costituito da tre componenti principali:</span><span class="sxs-lookup"><span data-stu-id="4050a-110">The protected payload format consists of three primary components:</span></span>

* <span data-ttu-id="4050a-111">Intestazione magica a 32 bit che identifica la versione del sistema di protezione dei dati.</span><span class="sxs-lookup"><span data-stu-id="4050a-111">A 32-bit magic header that identifies the version of the data protection system.</span></span>

* <span data-ttu-id="4050a-112">ID chiave a 128 bit che identifica la chiave utilizzata per proteggere questo payload specifico.</span><span class="sxs-lookup"><span data-stu-id="4050a-112">A 128-bit key id that identifies the key used to protect this particular payload.</span></span>

* <span data-ttu-id="4050a-113">Il resto del payload protetto è [specifico del codificatore incapsulato da questa chiave](xref:security/data-protection/implementation/subkeyderivation#data-protection-implementation-subkey-derivation).</span><span class="sxs-lookup"><span data-stu-id="4050a-113">The remainder of the protected payload is [specific to the encryptor encapsulated by this key](xref:security/data-protection/implementation/subkeyderivation#data-protection-implementation-subkey-derivation).</span></span> <span data-ttu-id="4050a-114">Nell'esempio seguente la chiave rappresenta un HMACSHA256 di crittografia AES-256-CBC + e il payload viene suddiviso ulteriormente come segue:</span><span class="sxs-lookup"><span data-stu-id="4050a-114">In the example below, the key represents an AES-256-CBC + HMACSHA256 encryptor, and the payload is further subdivided as follows:</span></span>
  * <span data-ttu-id="4050a-115">Modificatore di chiave a 128 bit.</span><span class="sxs-lookup"><span data-stu-id="4050a-115">A 128-bit key modifier.</span></span>
  * <span data-ttu-id="4050a-116">Vettore di inizializzazione a 128 bit.</span><span class="sxs-lookup"><span data-stu-id="4050a-116">A 128-bit initialization vector.</span></span>
  * <span data-ttu-id="4050a-117">48 byte di output AES-256-CBC.</span><span class="sxs-lookup"><span data-stu-id="4050a-117">48 bytes of AES-256-CBC output.</span></span>
  * <span data-ttu-id="4050a-118">Tag di autenticazione HMACSHA256.</span><span class="sxs-lookup"><span data-stu-id="4050a-118">An HMACSHA256 authentication tag.</span></span>

<span data-ttu-id="4050a-119">Di seguito è illustrato un payload protetto di esempio.</span><span class="sxs-lookup"><span data-stu-id="4050a-119">A sample protected payload is illustrated below.</span></span>

```
09 F0 C9 F0 80 9C 81 0C 19 66 19 40 95 36 53 F8
AA FF EE 57 57 2F 40 4C 3F 7F CC 9D CC D9 32 3E
84 17 99 16 EC BA 1F 4A A1 18 45 1F 2D 13 7A 28
79 6B 86 9C F8 B7 84 F9 26 31 FC B1 86 0A F1 56
61 CF 14 58 D3 51 6F CF 36 50 85 82 08 2D 3F 73
5F B0 AD 9E 1A B2 AE 13 57 90 C8 F5 7C 95 4E 6A
8A AA 06 EF 43 CA 19 62 84 7C 11 B2 C8 71 9D AA
52 19 2E 5B 4C 1E 54 F0 55 BE 88 92 12 C1 4B 5E
52 C9 74 A0
```

<span data-ttu-id="4050a-120">Dal formato del payload sopra i primi 32 bit oppure 4 byte sono l'intestazione magica che identifica la versione (09 F0 C9 F0)</span><span class="sxs-lookup"><span data-stu-id="4050a-120">From the payload format above the first 32 bits, or 4 bytes are the magic header identifying the version (09 F0 C9 F0)</span></span>

<span data-ttu-id="4050a-121">I successivi 128 bit o 16 byte è l'identificatore di chiave (80 9C 81 0C 19 66 19 40 95 36 53 F8 AA FF EE 57)</span><span class="sxs-lookup"><span data-stu-id="4050a-121">The next 128 bits, or 16 bytes is the key identifier (80 9C 81 0C 19 66 19 40 95 36 53 F8 AA FF EE 57)</span></span>

<span data-ttu-id="4050a-122">Il resto contiene il payload ed è specifico del formato utilizzato.</span><span class="sxs-lookup"><span data-stu-id="4050a-122">The remainder contains the payload and is specific to the format used.</span></span>

> [!WARNING]
> <span data-ttu-id="4050a-123">Tutti i payload protetti a una determinata chiave inizieranno con la stessa intestazione di 20 byte (valore magico, ID chiave).</span><span class="sxs-lookup"><span data-stu-id="4050a-123">All payloads protected to a given key will begin with the same 20-byte (magic value, key id) header.</span></span> <span data-ttu-id="4050a-124">Gli amministratori possono utilizzare questo fatto a scopo di diagnostica per approssimarsi quando è stato generato un payload.</span><span class="sxs-lookup"><span data-stu-id="4050a-124">Administrators can use this fact for diagnostic purposes to approximate when a payload was generated.</span></span> <span data-ttu-id="4050a-125">Il payload precedente, ad esempio, corrisponde alla chiave {0c819c80-6619-4019-9536-53f8aaffee57}.</span><span class="sxs-lookup"><span data-stu-id="4050a-125">For example, the payload above corresponds to key {0c819c80-6619-4019-9536-53f8aaffee57}.</span></span> <span data-ttu-id="4050a-126">Se dopo aver verificato il repository della chiave si è verificato che la data di attivazione della chiave specifica è 2015-01-01 e che la data di scadenza è 2015-03-01, è ragionevole presupporre che il payload (se non alterato) sia stato generato all'interno di tale finestra, assegnare o assumere un piccolo fattore di Fudge su entrambi i lati.</span><span class="sxs-lookup"><span data-stu-id="4050a-126">If after checking the key repository you find that this specific key's activation date was 2015-01-01 and its expiration date was 2015-03-01, then it's reasonable to assume that the payload (if not tampered with) was generated within that window, give or take a small fudge factor on either side.</span></span>
