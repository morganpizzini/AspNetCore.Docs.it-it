---
title: Intestazioni di contesto nel ASP.NET Core
author: rick-anderson
description: Informazioni sui dettagli di implementazione di ASP.NET Core intestazioni del contesto di protezione dati.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: security/data-protection/implementation/context-headers
ms.openlocfilehash: 45463c9d96ad58e1721f7cb5c16912f783f646ed
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051720"
---
# <a name="context-headers-in-aspnet-core"></a><span data-ttu-id="f77ec-103">Intestazioni di contesto nel ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f77ec-103">Context headers in ASP.NET Core</span></span>

<a name="data-protection-implementation-context-headers"></a>

## <a name="background-and-theory"></a><span data-ttu-id="f77ec-104">Background e teoria</span><span class="sxs-lookup"><span data-stu-id="f77ec-104">Background and theory</span></span>

<span data-ttu-id="f77ec-105">Nel sistema di protezione dei dati, una "chiave" indica un oggetto che può fornire servizi di crittografia autenticati.</span><span class="sxs-lookup"><span data-stu-id="f77ec-105">In the data protection system, a "key" means an object that can provide authenticated encryption services.</span></span> <span data-ttu-id="f77ec-106">Ogni chiave è identificata da un ID univoco (GUID), che contiene informazioni algoritmiche e materiali intropici.</span><span class="sxs-lookup"><span data-stu-id="f77ec-106">Each key is identified by a unique id (a GUID), and it carries with it algorithmic information and entropic material.</span></span> <span data-ttu-id="f77ec-107">È necessario che ogni chiave includa un'entropia univoca, ma non può essere applicata dal sistema ed è inoltre necessario tenere conto degli sviluppatori che potrebbero modificare manualmente l'anello della chiave modificando le informazioni algoritmiche di una chiave esistente nell'anello chiave.</span><span class="sxs-lookup"><span data-stu-id="f77ec-107">It's intended that each key carry unique entropy, but the system cannot enforce that, and we also need to account for developers who might change the key ring manually by modifying the algorithmic information of an existing key in the key ring.</span></span> <span data-ttu-id="f77ec-108">Per soddisfare i requisiti di sicurezza in base a questi casi, il sistema di protezione dei dati ha un concetto di [agilità crittografica](https://www.microsoft.com/research/publication/cryptographic-agility-and-its-relation-to-circular-encryption), che consente di usare in modo sicuro un singolo valore in più algoritmi di crittografia.</span><span class="sxs-lookup"><span data-stu-id="f77ec-108">To achieve our security requirements given these cases the data protection system has a concept of [cryptographic agility](https://www.microsoft.com/research/publication/cryptographic-agility-and-its-relation-to-circular-encryption), which allows securely using a single entropic value across multiple cryptographic algorithms.</span></span>

<span data-ttu-id="f77ec-109">La maggior parte dei sistemi che supportano l'agilità crittografica consente di includere alcune informazioni di identificazione sull'algoritmo all'interno del payload.</span><span class="sxs-lookup"><span data-stu-id="f77ec-109">Most systems which support cryptographic agility do so by including some identifying information about the algorithm inside the payload.</span></span> <span data-ttu-id="f77ec-110">L'OID dell'algoritmo è in genere un buon candidato per questa operazione.</span><span class="sxs-lookup"><span data-stu-id="f77ec-110">The algorithm's OID is generally a good candidate for this.</span></span> <span data-ttu-id="f77ec-111">Tuttavia, un problema che si è verificato è che esistono diversi modi per specificare lo stesso algoritmo: "AES" (CNG) e le classi gestite AES, AesManaged, AesCryptoServiceProvider, AesCng e RijndaelManaged (determinati parametri specifici) sono in realtà la stessa cosa ed è necessario mantenere un mapping di tutti questi nell'OID corretto.</span><span class="sxs-lookup"><span data-stu-id="f77ec-111">However, one problem that we ran into is that there are multiple ways to specify the same algorithm: "AES" (CNG) and the managed Aes, AesManaged, AesCryptoServiceProvider, AesCng, and RijndaelManaged (given specific parameters) classes are all actually the same thing, and we'd need to maintain a mapping of all of these to the correct OID.</span></span> <span data-ttu-id="f77ec-112">Se uno sviluppatore vuole fornire un algoritmo personalizzato (o anche un'altra implementazione di AES!), deve indicare il relativo OID.</span><span class="sxs-lookup"><span data-stu-id="f77ec-112">If a developer wanted to provide a custom algorithm (or even another implementation of AES!), they'd have to tell us its OID.</span></span> <span data-ttu-id="f77ec-113">Questo passaggio di registrazione aggiuntivo rende la configurazione del sistema particolarmente penosa.</span><span class="sxs-lookup"><span data-stu-id="f77ec-113">This extra registration step makes system configuration particularly painful.</span></span>

<span data-ttu-id="f77ec-114">Tornando indietro, abbiamo deciso di affrontare il problema dalla direzione sbagliata.</span><span class="sxs-lookup"><span data-stu-id="f77ec-114">Stepping back, we decided that we were approaching the problem from the wrong direction.</span></span> <span data-ttu-id="f77ec-115">Un OID indica l'algoritmo, ma in realtà non è rilevante.</span><span class="sxs-lookup"><span data-stu-id="f77ec-115">An OID tells you what the algorithm is, but we don't actually care about this.</span></span> <span data-ttu-id="f77ec-116">Se è necessario usare un singolo valore di tipo Tropico in modo sicuro in due algoritmi diversi, non è necessario conoscere gli algoritmi effettivamente.</span><span class="sxs-lookup"><span data-stu-id="f77ec-116">If we need to use a single entropic value securely in two different algorithms, it's not necessary for us to know what the algorithms actually are.</span></span> <span data-ttu-id="f77ec-117">Il modo in cui si è effettivamente interessati è il comportamento.</span><span class="sxs-lookup"><span data-stu-id="f77ec-117">What we actually care about is how they behave.</span></span> <span data-ttu-id="f77ec-118">Un algoritmo di crittografia a blocchi simmetrico decente è anche una forte permutazione pseudocasuale (PRP): la correzione degli input (chiave, modalità di concatenamento, IV, testo non crittografato) e l'output del testo crittografato con probabilità eccessiva possono essere distinti da qualsiasi altro algoritmo di crittografia a blocchi simmetrico dato gli stessi input.</span><span class="sxs-lookup"><span data-stu-id="f77ec-118">Any decent symmetric block cipher algorithm is also a strong pseudorandom permutation (PRP): fix the inputs (key, chaining mode, IV, plaintext) and the ciphertext output will with overwhelming probability be distinct from any other symmetric block cipher algorithm given the same inputs.</span></span> <span data-ttu-id="f77ec-119">Analogamente, qualsiasi funzione hash con chiave decente è anche una funzione pseudocasuale forte (PRF) e, dato un set di input fisso, l'output sarà decisamente diverso da qualsiasi altra funzione hash con chiave.</span><span class="sxs-lookup"><span data-stu-id="f77ec-119">Similarly, any decent keyed hash function is also a strong pseudorandom function (PRF), and given a fixed input set its output will overwhelmingly be distinct from any other keyed hash function.</span></span>

<span data-ttu-id="f77ec-120">Si usa questo concetto di PRPs e PRFs avanzati per creare un'intestazione di contesto.</span><span class="sxs-lookup"><span data-stu-id="f77ec-120">We use this concept of strong PRPs and PRFs to build up a context header.</span></span> <span data-ttu-id="f77ec-121">Questa intestazione del contesto funge essenzialmente da identificazione personale stabile sugli algoritmi utilizzati per qualsiasi operazione e fornisce l'agilità crittografica necessaria per il sistema di protezione dei dati.</span><span class="sxs-lookup"><span data-stu-id="f77ec-121">This context header essentially acts as a stable thumbprint over the algorithms in use for any given operation, and it provides the cryptographic agility needed by the data protection system.</span></span> <span data-ttu-id="f77ec-122">Questa intestazione è riproducibile e viene usata in un secondo momento come parte del [processo di derivazione della sottochiave](xref:security/data-protection/implementation/subkeyderivation#data-protection-implementation-subkey-derivation).</span><span class="sxs-lookup"><span data-stu-id="f77ec-122">This header is reproducible and is used later as part of the [subkey derivation process](xref:security/data-protection/implementation/subkeyderivation#data-protection-implementation-subkey-derivation).</span></span> <span data-ttu-id="f77ec-123">Esistono due modi diversi per compilare l'intestazione del contesto a seconda delle modalità di funzionamento degli algoritmi sottostanti.</span><span class="sxs-lookup"><span data-stu-id="f77ec-123">There are two different ways to build the context header depending on the modes of operation of the underlying algorithms.</span></span>

## <a name="cbc-mode-encryption--hmac-authentication"></a><span data-ttu-id="f77ec-124">Crittografia in modalità CBC + autenticazione HMAC</span><span class="sxs-lookup"><span data-stu-id="f77ec-124">CBC-mode encryption + HMAC authentication</span></span>

<a name="data-protection-implementation-context-headers-cbc-components"></a>

<span data-ttu-id="f77ec-125">L'intestazione del contesto è costituita dai componenti seguenti:</span><span class="sxs-lookup"><span data-stu-id="f77ec-125">The context header consists of the following components:</span></span>

* <span data-ttu-id="f77ec-126">[16 bit] Il valore 00 00, ovvero un marcatore che significa "CBC Encryption + HMAC Authentication".</span><span class="sxs-lookup"><span data-stu-id="f77ec-126">[16 bits] The value 00 00, which is a marker meaning "CBC encryption + HMAC authentication".</span></span>

* <span data-ttu-id="f77ec-127">[bit 32] Lunghezza della chiave (in byte, big endian) dell'algoritmo di crittografia a blocchi simmetrico.</span><span class="sxs-lookup"><span data-stu-id="f77ec-127">[32 bits] The key length (in bytes, big-endian) of the symmetric block cipher algorithm.</span></span>

* <span data-ttu-id="f77ec-128">[bit 32] Dimensioni del blocco (in byte, big endian) dell'algoritmo di crittografia a blocchi simmetrico.</span><span class="sxs-lookup"><span data-stu-id="f77ec-128">[32 bits] The block size (in bytes, big-endian) of the symmetric block cipher algorithm.</span></span>

* <span data-ttu-id="f77ec-129">[bit 32] Lunghezza della chiave (in byte, big endian) dell'algoritmo HMAC.</span><span class="sxs-lookup"><span data-stu-id="f77ec-129">[32 bits] The key length (in bytes, big-endian) of the HMAC algorithm.</span></span> <span data-ttu-id="f77ec-130">Attualmente la dimensione della chiave corrisponde sempre alle dimensioni del digest.</span><span class="sxs-lookup"><span data-stu-id="f77ec-130">(Currently the key size always matches the digest size.)</span></span>

* <span data-ttu-id="f77ec-131">[bit 32] Dimensioni del digest (in byte, big endian) dell'algoritmo HMAC.</span><span class="sxs-lookup"><span data-stu-id="f77ec-131">[32 bits] The digest size (in bytes, big-endian) of the HMAC algorithm.</span></span>

* <span data-ttu-id="f77ec-132">`EncCBC(K_E, IV, "")`, ovvero l'output dell'algoritmo di crittografia a blocchi simmetrico dato un input di stringa vuoto e dove IV è un vettore all-zero.</span><span class="sxs-lookup"><span data-stu-id="f77ec-132">`EncCBC(K_E, IV, "")`, which is the output of the symmetric block cipher algorithm given an empty string input and where IV is an all-zero vector.</span></span> <span data-ttu-id="f77ec-133">La costruzione di `K_E` è descritta di seguito.</span><span class="sxs-lookup"><span data-stu-id="f77ec-133">The construction of `K_E` is described below.</span></span>

* <span data-ttu-id="f77ec-134">`MAC(K_H, "")`, ovvero l'output dell'algoritmo HMAC dato un input di stringa vuoto.</span><span class="sxs-lookup"><span data-stu-id="f77ec-134">`MAC(K_H, "")`, which is the output of the HMAC algorithm given an empty string input.</span></span> <span data-ttu-id="f77ec-135">La costruzione di `K_H` è descritta di seguito.</span><span class="sxs-lookup"><span data-stu-id="f77ec-135">The construction of `K_H` is described below.</span></span>

<span data-ttu-id="f77ec-136">Idealmente, è possibile passare tutti i vettori zero per `K_E` e `K_H` .</span><span class="sxs-lookup"><span data-stu-id="f77ec-136">Ideally, we could pass all-zero vectors for `K_E` and `K_H`.</span></span> <span data-ttu-id="f77ec-137">Tuttavia, si desidera evitare la situazione in cui l'algoritmo sottostante controlla l'esistenza di chiavi vulnerabili prima di eseguire qualsiasi operazione (in particolare DES e 3DES), che impedisce l'utilizzo di un modello semplice o ripetibile come un vettore all-zero.</span><span class="sxs-lookup"><span data-stu-id="f77ec-137">However, we want to avoid the situation where the underlying algorithm checks for the existence of weak keys before performing any operations (notably DES and 3DES), which precludes using a simple or repeatable pattern like an all-zero vector.</span></span>

<span data-ttu-id="f77ec-138">Viene invece usato il KDF NIST SP800-108 in modalità contatore (vedere [NIST SP800-108](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-108.pdf), Sec. 5,1) con una chiave di lunghezza zero, un'etichetta e un contesto e HMACSHA512 come PRF sottostante.</span><span class="sxs-lookup"><span data-stu-id="f77ec-138">Instead, we use the NIST SP800-108 KDF in Counter Mode (see [NIST SP800-108](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-108.pdf), Sec. 5.1) with a zero-length key, label, and context and HMACSHA512 as the underlying PRF.</span></span> <span data-ttu-id="f77ec-139">Si derivano `| K_E | + | K_H |` i byte di output, quindi si scompone il risultato in `K_E` e in `K_H` se stesso.</span><span class="sxs-lookup"><span data-stu-id="f77ec-139">We derive `| K_E | + | K_H |` bytes of output, then decompose the result into `K_E` and `K_H` themselves.</span></span> <span data-ttu-id="f77ec-140">In matematica, questo viene rappresentato come indicato di seguito.</span><span class="sxs-lookup"><span data-stu-id="f77ec-140">Mathematically, this is represented as follows.</span></span>

`( K_E || K_H ) = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")`

### <a name="example-aes-192-cbc--hmacsha256"></a><span data-ttu-id="f77ec-141">Esempio: AES-192-CBC + HMACSHA256</span><span class="sxs-lookup"><span data-stu-id="f77ec-141">Example: AES-192-CBC + HMACSHA256</span></span>

<span data-ttu-id="f77ec-142">Ad esempio, si consideri il caso in cui l'algoritmo di crittografia a blocchi simmetrico è AES-192-CBC e l'algoritmo di convalida è HMACSHA256.</span><span class="sxs-lookup"><span data-stu-id="f77ec-142">As an example, consider the case where the symmetric block cipher algorithm is AES-192-CBC and the validation algorithm is HMACSHA256.</span></span> <span data-ttu-id="f77ec-143">Il sistema genera l'intestazione del contesto attenendosi alla procedura seguente.</span><span class="sxs-lookup"><span data-stu-id="f77ec-143">The system would generate the context header using the following steps.</span></span>

<span data-ttu-id="f77ec-144">Prima di tutto, lasciare `( K_E || K_H ) = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")` , dove `| K_E | = 192 bits` e in `| K_H | = 256 bits` base agli algoritmi specificati.</span><span class="sxs-lookup"><span data-stu-id="f77ec-144">First, let `( K_E || K_H ) = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")`, where `| K_E | = 192 bits` and `| K_H | = 256 bits` per the specified algorithms.</span></span> <span data-ttu-id="f77ec-145">Questa operazione conduce a `K_E = 5BB6..21DD` e `K_H = A04A..00A9` nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="f77ec-145">This leads to `K_E = 5BB6..21DD` and `K_H = A04A..00A9` in the example below:</span></span>

```
5B B6 C9 83 13 78 22 1D 8E 10 73 CA CF 65 8E B0
61 62 42 71 CB 83 21 DD A0 4A 05 00 5B AB C0 A2
49 6F A5 61 E3 E2 49 87 AA 63 55 CD 74 0A DA C4
B7 92 3D BF 59 90 00 A9
```

<span data-ttu-id="f77ec-146">Successivamente, calcolo `Enc_CBC (K_E, IV, "")` per AES-192-CBC specificato `IV = 0*` e `K_E` come sopra.</span><span class="sxs-lookup"><span data-stu-id="f77ec-146">Next, compute `Enc_CBC (K_E, IV, "")` for AES-192-CBC given `IV = 0*` and `K_E` as above.</span></span>

`result := F474B1872B3B53E4721DE19C0841DB6F`

<span data-ttu-id="f77ec-147">Quindi, calcolo `MAC(K_H, "")` per HMACSHA256 specificato `K_H` come sopra.</span><span class="sxs-lookup"><span data-stu-id="f77ec-147">Next, compute `MAC(K_H, "")` for HMACSHA256 given `K_H` as above.</span></span>

`result := D4791184B996092EE1202F36E8608FA8FBD98ABDFF5402F264B1D7211536220C`

<span data-ttu-id="f77ec-148">Questa operazione produce l'intestazione del contesto completo seguente:</span><span class="sxs-lookup"><span data-stu-id="f77ec-148">This produces the full context header below:</span></span>

```
00 00 00 00 00 18 00 00 00 10 00 00 00 20 00 00
00 20 F4 74 B1 87 2B 3B 53 E4 72 1D E1 9C 08 41
DB 6F D4 79 11 84 B9 96 09 2E E1 20 2F 36 E8 60
8F A8 FB D9 8A BD FF 54 02 F2 64 B1 D7 21 15 36
22 0C
```

<span data-ttu-id="f77ec-149">Questa intestazione del contesto è l'identificazione personale della coppia di algoritmi di crittografia autenticata (la convalida AES-192-CBC Encryption + HMACSHA256).</span><span class="sxs-lookup"><span data-stu-id="f77ec-149">This context header is the thumbprint of the authenticated encryption algorithm pair (AES-192-CBC encryption + HMACSHA256 validation).</span></span> <span data-ttu-id="f77ec-150">I componenti, come descritto in [precedenza](xref:security/data-protection/implementation/context-headers#data-protection-implementation-context-headers-cbc-components) , sono i seguenti:</span><span class="sxs-lookup"><span data-stu-id="f77ec-150">The components, as described [above](xref:security/data-protection/implementation/context-headers#data-protection-implementation-context-headers-cbc-components) are:</span></span>

* <span data-ttu-id="f77ec-151">marcatore `(00 00)`</span><span class="sxs-lookup"><span data-stu-id="f77ec-151">the marker `(00 00)`</span></span>

* <span data-ttu-id="f77ec-152">lunghezza della chiave di crittografia a blocchi `(00 00 00 18)`</span><span class="sxs-lookup"><span data-stu-id="f77ec-152">the block cipher key length `(00 00 00 18)`</span></span>

* <span data-ttu-id="f77ec-153">Dimensioni blocco crittografico blocco `(00 00 00 10)`</span><span class="sxs-lookup"><span data-stu-id="f77ec-153">the block cipher block size `(00 00 00 10)`</span></span>

* <span data-ttu-id="f77ec-154">lunghezza della chiave HMAC `(00 00 00 20)`</span><span class="sxs-lookup"><span data-stu-id="f77ec-154">the HMAC key length `(00 00 00 20)`</span></span>

* <span data-ttu-id="f77ec-155">dimensioni del digest HMAC `(00 00 00 20)`</span><span class="sxs-lookup"><span data-stu-id="f77ec-155">the HMAC digest size `(00 00 00 20)`</span></span>

* <span data-ttu-id="f77ec-156">output della crittografia a blocchi `(F4 74 - DB 6F)` e</span><span class="sxs-lookup"><span data-stu-id="f77ec-156">the block cipher PRP output `(F4 74 - DB 6F)` and</span></span>

* <span data-ttu-id="f77ec-157">output della PRF HMAC `(D4 79 - end)` .</span><span class="sxs-lookup"><span data-stu-id="f77ec-157">the HMAC PRF output `(D4 79 - end)`.</span></span>

> [!NOTE]
> <span data-ttu-id="f77ec-158">L'intestazione del contesto di autenticazione CBC-Mode Encryption + HMAC viene compilata allo stesso modo, indipendentemente dal fatto che le implementazioni degli algoritmi siano fornite da Windows CNG o dai tipi gestiti SymmetricAlgorithm e KeyedHashAlgorithm.</span><span class="sxs-lookup"><span data-stu-id="f77ec-158">The CBC-mode encryption + HMAC authentication context header is built the same way regardless of whether the algorithms implementations are provided by Windows CNG or by managed SymmetricAlgorithm and KeyedHashAlgorithm types.</span></span> <span data-ttu-id="f77ec-159">Ciò consente alle applicazioni in esecuzione su sistemi operativi diversi di produrre in modo affidabile la stessa intestazione di contesto anche se le implementazioni degli algoritmi sono diverse tra i sistemi operativi.</span><span class="sxs-lookup"><span data-stu-id="f77ec-159">This allows applications running on different operating systems to reliably produce the same context header even though the implementations of the algorithms differ between OSes.</span></span> <span data-ttu-id="f77ec-160">In pratica, il KeyedHashAlgorithm non deve essere un HMAC appropriato.</span><span class="sxs-lookup"><span data-stu-id="f77ec-160">(In practice, the KeyedHashAlgorithm doesn't have to be a proper HMAC.</span></span> <span data-ttu-id="f77ec-161">Può essere qualsiasi tipo di algoritmo hash con chiave.</span><span class="sxs-lookup"><span data-stu-id="f77ec-161">It can be any keyed hash algorithm type.)</span></span>

### <a name="example-3des-192-cbc--hmacsha1"></a><span data-ttu-id="f77ec-162">Esempio: 3DES-192-CBC + HMACSHA1</span><span class="sxs-lookup"><span data-stu-id="f77ec-162">Example: 3DES-192-CBC + HMACSHA1</span></span>

<span data-ttu-id="f77ec-163">Prima di tutto, lasciare `( K_E || K_H ) = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")` , dove `| K_E | = 192 bits` e in `| K_H | = 160 bits` base agli algoritmi specificati.</span><span class="sxs-lookup"><span data-stu-id="f77ec-163">First, let `( K_E || K_H ) = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")`, where `| K_E | = 192 bits` and `| K_H | = 160 bits` per the specified algorithms.</span></span> <span data-ttu-id="f77ec-164">Questa operazione conduce a `K_E = A219..E2BB` e `K_H = DC4A..B464` nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="f77ec-164">This leads to `K_E = A219..E2BB` and `K_H = DC4A..B464` in the example below:</span></span>

```
A2 19 60 2F 83 A9 13 EA B0 61 3A 39 B8 A6 7E 22
61 D9 F8 6C 10 51 E2 BB DC 4A 00 D7 03 A2 48 3E
D1 F7 5A 34 EB 28 3E D7 D4 67 B4 64
```

<span data-ttu-id="f77ec-165">Successivamente, calcolo `Enc_CBC (K_E, IV, "")` per 3DES-192-CBC specificato `IV = 0*` e `K_E` come sopra.</span><span class="sxs-lookup"><span data-stu-id="f77ec-165">Next, compute `Enc_CBC (K_E, IV, "")` for 3DES-192-CBC given `IV = 0*` and `K_E` as above.</span></span>

`result := ABB100F81E53E10E`

<span data-ttu-id="f77ec-166">Quindi, calcolo `MAC(K_H, "")` per HMACSHA1 specificato `K_H` come sopra.</span><span class="sxs-lookup"><span data-stu-id="f77ec-166">Next, compute `MAC(K_H, "")` for HMACSHA1 given `K_H` as above.</span></span>

`result := 76EB189B35CF03461DDF877CD9F4B1B4D63A7555`

<span data-ttu-id="f77ec-167">Questa operazione produce l'intestazione del contesto completo che rappresenta un'identificazione personale della coppia di algoritmi di crittografia autenticata (3DES-192-CBC Encryption + HMACSHA1 Validation), mostrata di seguito:</span><span class="sxs-lookup"><span data-stu-id="f77ec-167">This produces the full context header which is a thumbprint of the authenticated encryption algorithm pair (3DES-192-CBC encryption + HMACSHA1 validation), shown below:</span></span>

```
00 00 00 00 00 18 00 00 00 08 00 00 00 14 00 00
00 14 AB B1 00 F8 1E 53 E1 0E 76 EB 18 9B 35 CF
03 46 1D DF 87 7C D9 F4 B1 B4 D6 3A 75 55
```

<span data-ttu-id="f77ec-168">I componenti si suddividono come segue:</span><span class="sxs-lookup"><span data-stu-id="f77ec-168">The components break down as follows:</span></span>

* <span data-ttu-id="f77ec-169">marcatore `(00 00)`</span><span class="sxs-lookup"><span data-stu-id="f77ec-169">the marker `(00 00)`</span></span>

* <span data-ttu-id="f77ec-170">lunghezza della chiave di crittografia a blocchi `(00 00 00 18)`</span><span class="sxs-lookup"><span data-stu-id="f77ec-170">the block cipher key length `(00 00 00 18)`</span></span>

* <span data-ttu-id="f77ec-171">Dimensioni blocco crittografico blocco `(00 00 00 08)`</span><span class="sxs-lookup"><span data-stu-id="f77ec-171">the block cipher block size `(00 00 00 08)`</span></span>

* <span data-ttu-id="f77ec-172">lunghezza della chiave HMAC `(00 00 00 14)`</span><span class="sxs-lookup"><span data-stu-id="f77ec-172">the HMAC key length `(00 00 00 14)`</span></span>

* <span data-ttu-id="f77ec-173">dimensioni del digest HMAC `(00 00 00 14)`</span><span class="sxs-lookup"><span data-stu-id="f77ec-173">the HMAC digest size `(00 00 00 14)`</span></span>

* <span data-ttu-id="f77ec-174">output della crittografia a blocchi `(AB B1 - E1 0E)` e</span><span class="sxs-lookup"><span data-stu-id="f77ec-174">the block cipher PRP output `(AB B1 - E1 0E)` and</span></span>

* <span data-ttu-id="f77ec-175">output della PRF HMAC `(76 EB - end)` .</span><span class="sxs-lookup"><span data-stu-id="f77ec-175">the HMAC PRF output `(76 EB - end)`.</span></span>

## <a name="galoiscounter-mode-encryption--authentication"></a><span data-ttu-id="f77ec-176">Crittografia e autenticazione in modalità contatore di Galois/Counter</span><span class="sxs-lookup"><span data-stu-id="f77ec-176">Galois/Counter Mode encryption + authentication</span></span>

<span data-ttu-id="f77ec-177">L'intestazione del contesto è costituita dai componenti seguenti:</span><span class="sxs-lookup"><span data-stu-id="f77ec-177">The context header consists of the following components:</span></span>

* <span data-ttu-id="f77ec-178">[16 bit] Il valore 00 01, che è un marcatore che significa "GCM Encryption + Authentication".</span><span class="sxs-lookup"><span data-stu-id="f77ec-178">[16 bits] The value 00 01, which is a marker meaning "GCM encryption + authentication".</span></span>

* <span data-ttu-id="f77ec-179">[bit 32] Lunghezza della chiave (in byte, big endian) dell'algoritmo di crittografia a blocchi simmetrico.</span><span class="sxs-lookup"><span data-stu-id="f77ec-179">[32 bits] The key length (in bytes, big-endian) of the symmetric block cipher algorithm.</span></span>

* <span data-ttu-id="f77ec-180">[bit 32] Dimensioni del parametro nonce (in byte, big endian) utilizzate durante le operazioni di crittografia autenticata.</span><span class="sxs-lookup"><span data-stu-id="f77ec-180">[32 bits] The nonce size (in bytes, big-endian) used during authenticated encryption operations.</span></span> <span data-ttu-id="f77ec-181">Per il nostro sistema, questo problema viene risolto alle dimensioni del parametro nonce = 96 bit.</span><span class="sxs-lookup"><span data-stu-id="f77ec-181">(For our system, this is fixed at nonce size = 96 bits.)</span></span>

* <span data-ttu-id="f77ec-182">[bit 32] Dimensioni del blocco (in byte, big endian) dell'algoritmo di crittografia a blocchi simmetrico.</span><span class="sxs-lookup"><span data-stu-id="f77ec-182">[32 bits] The block size (in bytes, big-endian) of the symmetric block cipher algorithm.</span></span> <span data-ttu-id="f77ec-183">(Per GCM, questo è corretto a dimensione blocco = 128 bit).</span><span class="sxs-lookup"><span data-stu-id="f77ec-183">(For GCM, this is fixed at block size = 128 bits.)</span></span>

* <span data-ttu-id="f77ec-184">[bit 32] Dimensioni del tag di autenticazione (in byte, big endian) prodotte dalla funzione di crittografia autenticata.</span><span class="sxs-lookup"><span data-stu-id="f77ec-184">[32 bits] The authentication tag size (in bytes, big-endian) produced by the authenticated encryption function.</span></span> <span data-ttu-id="f77ec-185">Per il nostro sistema, questo problema viene risolto in corrispondenza della dimensione del tag = 128 bit.</span><span class="sxs-lookup"><span data-stu-id="f77ec-185">(For our system, this is fixed at tag size = 128 bits.)</span></span>

* <span data-ttu-id="f77ec-186">[bit 128] Tag di `Enc_GCM (K_E, nonce, "")` , ovvero l'output dell'algoritmo di crittografia a blocchi simmetrico dato un input di stringa vuoto e dove nonce è un vettore all-zero a 96 bit.</span><span class="sxs-lookup"><span data-stu-id="f77ec-186">[128 bits] The tag of `Enc_GCM (K_E, nonce, "")`, which is the output of the symmetric block cipher algorithm given an empty string input and where nonce is a 96-bit all-zero vector.</span></span>

<span data-ttu-id="f77ec-187">`K_E` viene derivato utilizzando lo stesso meccanismo dello scenario di autenticazione CBC Encryption + HMAC.</span><span class="sxs-lookup"><span data-stu-id="f77ec-187">`K_E` is derived using the same mechanism as in the CBC encryption + HMAC authentication scenario.</span></span> <span data-ttu-id="f77ec-188">Tuttavia, poiché non è presente `K_H` in questa sezione, abbiamo essenzialmente `| K_H | = 0` e l'algoritmo viene compresso nel formato seguente.</span><span class="sxs-lookup"><span data-stu-id="f77ec-188">However, since there's no `K_H` in play here, we essentially have `| K_H | = 0`, and the algorithm collapses to the below form.</span></span>

`K_E = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")`

### <a name="example-aes-256-gcm"></a><span data-ttu-id="f77ec-189">Esempio: AES-256-GCM</span><span class="sxs-lookup"><span data-stu-id="f77ec-189">Example: AES-256-GCM</span></span>

<span data-ttu-id="f77ec-190">Per prima cosa, let `K_E = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")` , Where `| K_E | = 256 bits` .</span><span class="sxs-lookup"><span data-stu-id="f77ec-190">First, let `K_E = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")`, where `| K_E | = 256 bits`.</span></span>

`K_E := 22BC6F1B171C08C4AE2F27444AF8FC8B3087A90006CAEA91FDCFB47C1B8733B8`

<span data-ttu-id="f77ec-191">Successivamente, calcolare il tag di autenticazione di `Enc_GCM (K_E, nonce, "")` per AES-256-GCM specificato `nonce = 096` e `K_E` come sopra.</span><span class="sxs-lookup"><span data-stu-id="f77ec-191">Next, compute the authentication tag of `Enc_GCM (K_E, nonce, "")` for AES-256-GCM given `nonce = 096` and `K_E` as above.</span></span>

`result := E7DCCE66DF855A323A6BB7BD7A59BE45`

<span data-ttu-id="f77ec-192">Questa operazione produce l'intestazione del contesto completo seguente:</span><span class="sxs-lookup"><span data-stu-id="f77ec-192">This produces the full context header below:</span></span>

```
00 01 00 00 00 20 00 00 00 0C 00 00 00 10 00 00
00 10 E7 DC CE 66 DF 85 5A 32 3A 6B B7 BD 7A 59
BE 45
```

<span data-ttu-id="f77ec-193">I componenti si suddividono come segue:</span><span class="sxs-lookup"><span data-stu-id="f77ec-193">The components break down as follows:</span></span>

* <span data-ttu-id="f77ec-194">marcatore `(00 01)`</span><span class="sxs-lookup"><span data-stu-id="f77ec-194">the marker `(00 01)`</span></span>

* <span data-ttu-id="f77ec-195">lunghezza della chiave di crittografia a blocchi `(00 00 00 20)`</span><span class="sxs-lookup"><span data-stu-id="f77ec-195">the block cipher key length `(00 00 00 20)`</span></span>

* <span data-ttu-id="f77ec-196">dimensioni del parametro nonce `(00 00 00 0C)`</span><span class="sxs-lookup"><span data-stu-id="f77ec-196">the nonce size `(00 00 00 0C)`</span></span>

* <span data-ttu-id="f77ec-197">Dimensioni blocco crittografico blocco `(00 00 00 10)`</span><span class="sxs-lookup"><span data-stu-id="f77ec-197">the block cipher block size `(00 00 00 10)`</span></span>

* <span data-ttu-id="f77ec-198">dimensioni del tag `(00 00 00 10)` di autenticazione e</span><span class="sxs-lookup"><span data-stu-id="f77ec-198">the authentication tag size `(00 00 00 10)` and</span></span>

* <span data-ttu-id="f77ec-199">Tag di autenticazione dall'esecuzione della crittografia a blocchi `(E7 DC - end)` .</span><span class="sxs-lookup"><span data-stu-id="f77ec-199">the authentication tag from running the block cipher `(E7 DC - end)`.</span></span>
